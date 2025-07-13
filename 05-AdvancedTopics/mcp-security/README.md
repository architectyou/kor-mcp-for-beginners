# 보안 모범 사례

보안은 MCP 구현에 있어 매우 중요하며, 특히 엔터프라이즈 환경에서는 더욱 그렇습니다. 도구와 데이터가 무단 액세스, 데이터 유출 및 기타 보안 위협으로부터 보호되도록 하는 것이 중요합니다.

## 소개

이 단원에서는 MCP 구현을 위한 보안 모범 사례를 살펴보겠습니다. 인증 및 권한 부여, 데이터 보호, 보안 도구 실행, 데이터 개인 정보 보호 규정 준수를 다룰 것입니다.

## 학습 목표

이 단원을 마치면 다음을 수행할 수 있습니다:

- MCP 서버를 위한 안전한 인증 및 권한 부여 메커니즘 구현
- 암호화 및 보안 스토리지를 사용하여 민감한 데이터 보호
- 적절한 액세스 제어를 통해 도구의 안전한 실행 보장
- 데이터 보호 및 개인 정보 보호 규정 준수를 위한 모범 사례 적용

## 인증 및 권한 부여

인증 및 권한 부여는 MCP 서버를 보호하는 데 필수적입니다. 인증은 "당신은 누구인가?"라는 질문에 답하고, 권한 부여는 "무엇을 할 수 있는가?"라는 질문에 답합니다.

.NET 및 Java를 사용하여 MCP 서버에서 안전한 인증 및 권한 부여를 구현하는 방법을 예시를 통해 살펴보겠습니다.

### .NET ID 통합

ASP.NET Core Identity는 사용자 인증 및 권한 부여를 관리하기 위한 강력한 프레임워크를 제공합니다. 이를 MCP 서버와 통합하여 도구 및 리소스에 대한 액세스를 보호할 수 있습니다.

ASP.NET Core Identity를 MCP 서버와 통합할 때 이해해야 할 몇 가지 핵심 개념은 다음과 같습니다:

- **ID 구성**: 사용자 역할 및 클레임으로 ASP.NET Core Identity 설정. 클레임은 사용자 역할 또는 권한(예: "관리자" 또는 "사용자")과 같은 사용자에 대한 정보입니다.
- **JWT 인증**: 안전한 API 액세스를 위해 JSON 웹 토큰(JWT) 사용. JWT는 JSON 객체로 당사자 간에 정보를 안전하게 전송하기 위한 표준이며, 디지털 서명되어 신뢰할 수 있습니다.
- **권한 부여 정책**: 사용자 역할에 따라 특정 도구에 대한 액세스를 제어하는 정책 정의. MCP는 사용자 역할 및 클레임에 따라 어떤 사용자가 어떤 도구에 액세스할 수 있는지 결정하기 위해 권한 부여 정책을 사용합니다.

```csharp
public class SecureMcpStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        // ASP.NET Core Identity 추가
        services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
        
        // JWT 인증 구성
        services.AddAuthentication(options =>
        {
            options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
            options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
        })
        .AddJwtBearer(options =>
        {
            options.TokenValidationParameters = new TokenValidationParameters
            {
                ValidateIssuer = true,
                ValidateAudience = true,
                ValidateLifetime = true,
                ValidateIssuerSigningKey = true,
                ValidIssuer = Configuration["Jwt:Issuer"],
                ValidAudience = Configuration["Jwt:Audience"],
                IssuerSigningKey = new SymmetricSecurityKey(
                    Encoding.UTF8.GetBytes(Configuration["Jwt:Key"]))
            };
        });
        
        // 권한 부여 정책 추가
        services.AddAuthorization(options =>
        {
            options.AddPolicy("CanUseAdminTools", policy =>
                policy.RequireRole("Admin"));
                
            options.AddPolicy("CanUseBasicTools", policy =>
                policy.RequireAuthenticatedUser());
        });
        
        // 보안을 사용하여 MCP 서버 구성
        services.AddMcpServer(options =>
        {
            options.ServerName = "Secure MCP Server";
            options.ServerVersion = "1.0.0";
            options.RequireAuthentication = true;
        });
        
        // 권한 부여 요구 사항으로 도구 등록
        services.AddMcpTool<BasicTool>(options => 
            options.RequirePolicy("CanUseBasicTools"));
            
        services.AddMcpTool<AdminTool>(options => 
            options.RequirePolicy("CanUseAdminTools"));
    }
    
    public void Configure(IApplicationBuilder app)
    {
        // 인증 및 권한 부여 사용
        app.UseAuthentication();
        app.UseAuthorization();
        
        // MCP 서버 미들웨어 사용
        app.UseMcpServer();
    }
}
```

위 코드에서 다음을 수행했습니다:

- 사용자 관리를 위해 ASP.NET Core Identity를 구성했습니다.
- 안전한 API 액세스를 위해 JWT 인증을 설정했습니다. 발급자, 대상 및 서명 키를 포함한 토큰 유효성 검사 매개변수를 지정했습니다.
- 사용자 역할에 따라 도구에 대한 액세스를 제어하는 권한 부여 정책을 정의했습니다. 예를 들어, "CanUseAdminTools" 정책은 사용자에게 "Admin" 역할을 요구하는 반면, "CanUseBasic" 정책은 사용자에게 인증을 요구합니다.
- 적절한 역할을 가진 사용자만 액세스할 수 있도록 특정 권한 부여 요구 사항으로 MCP 도구를 등록했습니다.

### Java Spring Security 통합

Java의 경우 Spring Security를 사용하여 MCP 서버에 대한 안전한 인증 및 권한 부여를 구현합니다. Spring Security는 Spring 애플리케이션과 원활하게 통합되는 포괄적인 보안 프레임워크를 제공합니다.

여기서 핵심 개념은 다음과 같습니다:

- **Spring Security 구성**: 인증 및 권한 부여를 위한 보안 구성 설정.
- **OAuth2 리소스 서버**: MCP 도구에 대한 안전한 액세스를 위해 OAuth2 사용. OAuth2는 타사 서비스가 안전한 API 액세스를 위해 액세스 토큰을 교환할 수 있도록 하는 권한 부여 프레임워크입니다.
- **보안 인터셉터**: 도구 실행에 대한 액세스 제어를 적용하기 위해 보안 인터셉터 구현.
- **역할 기반 액세스 제어**: 특정 도구 및 리소스에 대한 액세스를 제어하기 위해 역할 사용.
- **보안 주석**: 메서드 및 엔드포인트를 보호하기 위해 주석 사용.


```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .csrf().disable()
            .authorizeRequests()
                .antMatchers("/mcp/discovery").permitAll() // 도구 검색 허용
                .antMatchers("/mcp/tools/**").hasAnyRole("USER", "ADMIN") // 도구에 대한 인증 요구
                .antMatchers("/mcp/admin/**").hasRole("ADMIN") // 관리자 전용 엔드포인트
                .anyRequest().authenticated()
            .and()
            .oauth2ResourceServer().jwt();
    }
    
    @Bean
    public McpSecurityInterceptor mcpSecurityInterceptor() {
        return new McpSecurityInterceptor();
    }
}

// 도구 권한 부여를 위한 MCP 보안 인터셉터
public class McpSecurityInterceptor implements ToolExecutionInterceptor {
    @Autowired
    private JwtDecoder jwtDecoder;
    
    @Override
    public void beforeToolExecution(ToolRequest request, Authentication authentication) {
        String toolName = request.getToolName();
        
        // 이 도구에 대한 사용자 권한 확인
        if (toolName.startsWith("admin") && !authentication.getAuthorities().contains("ROLE_ADMIN")) {
            throw new AccessDeniedException("이 도구를 사용할 권한이 없습니다.");
        }
        
        // 도구 또는 매개변수에 기반한 추가 보안 검사
        if ("sensitiveDataAccess".equals(toolName)) {
            validateDataAccessPermissions(request, authentication);
        }
    }
    
    private void validateDataAccessPermissions(ToolRequest request, Authentication auth) {
        // 세분화된 데이터 액세스 권한을 확인하는 구현
    }
}
```

위 코드에서 다음을 수행했습니다:

- MCP 엔드포인트를 보호하도록 Spring Security를 구성하여 도구 실행에 인증을 요구하면서 도구 검색에 대한 공개 액세스를 허용했습니다.
- OAuth2를 리소스 서버로 사용하여 MCP 도구에 대한 안전한 액세스를 처리했습니다.
- 보안 인터셉터를 구현하여 도구 실행에 대한 액세스 제어를 적용하고, 특정 도구에 대한 액세스를 허용하기 전에 사용자 역할 및 권한을 확인했습니다.
- 사용자 역할에 따라 관리자 도구 및 민감한 데이터 액세스에 대한 액세스를 제한하기 위해 역할 기반 액세스 제어를 정의했습니다.

## 데이터 보호 및 개인 정보 보호

데이터 보호는 민감한 정보가 안전하게 처리되도록 보장하는 데 중요합니다. 여기에는 개인 식별 정보(PII), 금융 데이터 및 기타 민감한 정보를 무단 액세스 및 유출로부터 보호하는 것이 포함됩니다.

### Python 데이터 보호 예시

암호화 및 PII 감지를 사용하여 Python에서 데이터 보호를 구현하는 방법을 예시를 통해 살펴보겠습니다.

```python
from mcp_server import McpServer
from mcp_tools import Tool, ToolRequest, ToolResponse
from cryptography.fernet import Fernet
import os
import json
from functools import wraps

# PII 감지기 - 민감한 정보 식별 및 보호
class PiiDetector:
    def __init__(self):
        # 다양한 유형의 PII에 대한 패턴 로드
        with open("pii_patterns.json", "r") as f:
            self.patterns = json.load(f)
    
    def scan_text(self, text):
        """텍스트에서 PII를 스캔하고 감지된 PII 유형을 반환합니다."""
        detected_pii = []
        # 정규식 또는 ML 모델을 사용하여 PII를 감지하는 구현
        return detected_pii
    
    def scan_parameters(self, parameters):
        """요청 매개변수에서 PII를 스캔합니다."""
        detected_pii = []
        for key, value in parameters.items():
            if isinstance(value, str):
                pii_in_value = self.scan_text(value)
                if pii_in_value:
                    detected_pii.append((key, pii_in_value))
        return detected_pii

# 민감한 데이터 보호를 위한 암호화 서비스
class EncryptionService:
    def __init__(self, key_path=None):
        if key_path and os.path.exists(key_path):
            with open(key_path, "rb") as key_file:
                self.key = key_file.read()
        else:
            self.key = Fernet.generate_key()
            if key_path:
                with open(key_path, "wb") as key_file:
                    key_file.write(self.key)
        
        self.cipher = Fernet(self.key)
    
    def encrypt(self, data):
        """데이터 암호화"""
        if isinstance(data, str):
            return self.cipher.encrypt(data.encode()).decode()
        else:
            return self.cipher.encrypt(json.dumps(data).encode()).decode()
    
    def decrypt(self, encrypted_data):
        """데이터 복호화"""
        if encrypted_data is None:
            return None
        
        decrypted = self.cipher.decrypt(encrypted_data.encode())
        try:
            return json.loads(decrypted)
        except:
            return decrypted.decode()

# 도구를 위한 보안 데코레이터
def secure_tool(requires_encryption=False, log_access=True):
    def decorator(cls):
        original_execute = cls.execute_async if hasattr(cls, 'execute_async') else cls.execute
        
        @wraps(original_execute)
        async def secure_execute(self, request):
            # 요청에서 PII 확인
            pii_detector = PiiDetector()
            pii_found = pii_detector.scan_parameters(request.parameters)
            
            # 필요한 경우 액세스 로깅
            if log_access:
                tool_name = self.get_name()
                user_id = request.context.get("user_id", "anonymous")
                log_entry = {
                    "timestamp": datetime.now().isoformat(),
                    "tool": tool_name,
                    "user": user_id,
                    "contains_pii": bool(pii_found),
                    "parameters": {k: "***" for k in request.parameters.keys()}  # 실제 값 로깅 안 함
                }
                logging.info(f"Tool access: {json.dumps(log_entry)}")
            
            # 감지된 PII 처리
            if pii_found:
                # 민감한 데이터 암호화 또는 요청 거부
                if requires_encryption:
                    encryption_service = EncryptionService("keys/tool_key.key")
                    for param_name, pii_types in pii_found:
                        # 민감한 매개변수 암호화
                        request.parameters[param_name] = encryption_service.encrypt(
                            request.parameters[param_name]
                        )
                else:
                    # 암호화가 불가능하지만 PII가 감지된 경우 요청 거부
                    raise ToolExecutionException(
                        "요청에 안전하게 처리할 수 없는 민감한 데이터가 포함되어 있습니다."
                    )
            
            # 원본 메서드 실행
            return await original_execute(self, request)
        
        # execute 메서드 교체
        if hasattr(cls, 'execute_async'):
            cls.execute_async = secure_execute
        else:
            cls.execute = secure_execute
        return cls
    
    return decorator

# 데코레이터가 있는 보안 도구 예시
@secure_tool(requires_encryption=True, log_access=True)
class SecureCustomerDataTool(Tool):
    def get_name(self):
        return "customerData"
    
    def get_description(self):
        return "고객 데이터에 안전하게 액세스합니다."
    
    def get_schema(self):
        # 스키마 정의
        return {}
    
    async def execute_async(self, request):
        # 구현은 고객 데이터에 안전하게 액세스합니다.
        # 데코레이터를 사용했으므로 PII는 이미 감지되고 암호화됩니다.
        return ToolResponse(result={"status": "success"})
```

위 코드에서 다음을 수행했습니다:

- 개인 식별 정보(PII)에 대해 텍스트와 매개변수를 스캔하는 `PiiDetector` 클래스를 구현했습니다.
- `cryptography` 라이브러리를 사용하여 민감한 데이터의 암호화 및 복호화를 처리하는 `EncryptionService` 클래스를 생성했습니다.
- PII를 확인하고, 액세스를 로깅하고, 필요한 경우 민감한 데이터를 암호화하기 위해 도구 실행을 래핑하는 `secure_tool` 데코레이터를 정의했습니다.
- 샘플 도구(`SecureCustomerDataTool`)에 `secure_tool` 데코레이터를 적용하여 민감한 데이터를 안전하게 처리하도록 했습니다.

## 다음 단계

- [5.9 웹 검색](../web-search-mcp/README.md)
