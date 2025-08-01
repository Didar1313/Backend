
## ✅ Goal:

Build Spring Security with:

* Admin UI-based authentication using form login (`/admin/**`)
* REST API-based JWT authentication (`/api/**`)
* Separation of SecurityConfig, UserDetails, TokenService, etc.

---

## ✅ Step-by-Step Roadmap to Build It from Scratch

---

### **1. Start with the Domain Model**

#### 📌 Why first?

Because everything (auth, DB, services) revolves around your `UserEntity`.

```java
public class UserEntity {
    private Long id;
    private String username;
    private String password;
    private String firstName;
    private String lastName;
}
```

👉 You also define `UserRepository` at this stage.

---

### **2. Create the `UserDetails` Implementation**

#### 📌 Why next?

Because Spring Security uses `UserDetails` internally to validate users.

```java
public class AuthUser implements UserDetails {
    private final UserEntity user;
}
```

---

### **3. Create `CustomUserDetailsService`**

#### 📌 Why?

Because Spring uses this to load the `UserDetails` during authentication.

```java
public class CustomUserDetailsService implements UserDetailsService {
    public UserDetails loadUserByUsername(String username) { ... }
}
```

---

### **4. Configure `PasswordEncoder` Bean**

#### 📌 Why?

Spring Security needs it to compare hashed passwords.

```java
@Bean
public PasswordEncoder passwordEncoder() {
    return new BCryptPasswordEncoder(10);
}
```

---

### **5. Set Up the AuthenticationManager**

#### 📌 Why now?

Because it uses the `UserDetailsService` + `PasswordEncoder`.

```java
@Bean
public AuthenticationManager authManager(UserDetailsService uds, PasswordEncoder pe) {
    var provider = new DaoAuthenticationProvider();
    provider.setUserDetailsService(uds);
    provider.setPasswordEncoder(pe);
    return new ProviderManager(provider);
}
```

---

### **6a. Build JWT Support (`JwtTokenService`)**

#### 📌 Why?

Once `AuthenticationManager` can validate users, we want to generate and parse JWT tokens.

```java
public class JwtTokenService {
    public String generateToken(Authentication authentication) { ... }
    public Long extractExpirationTime(String token) { ... }
}
```

---

### ✅ 6b. JwtConfig: Where Encoder & Decoder Are Wired 🔥

#### 📌 Why it's needed:

Spring Security needs beans for JWT encoding and decoding. Without these, your `JwtTokenService` won't work. You must explicitly define how tokens are encoded and validated.

#### 🧪 Configuration Code:

```java
@Configuration
public class JwtConfig {

    @Value("${security.rest.jwt.key}")
    private String jwtKey;

    @Bean
    public JwtEncoder jwtEncoder() {
        return new NimbusJwtEncoder(new ImmutableSecret<>(jwtKey.getBytes()));
    }

    @Bean
    public JwtDecoder jwtDecoder() {
        byte[] bytes = jwtKey.getBytes();
        SecretKeySpec originalKey = new SecretKeySpec(bytes, 0, bytes.length, "RSA");
        return NimbusJwtDecoder.withSecretKey(originalKey)
                .macAlgorithm(MacAlgorithm.HS256)
                .build();
    }
}
```

* **Long enough** (at least 256 bits recommended).
* **Secure** — use environment variables or config files.
* **Never hardcoded** in source code or version control.

> This configuration makes your application ready to securely encode and decode JWTs using a shared secret.



### **7. Write the Authentication Service**

#### 📌 Why now?

Because it pulls everything together: it uses `AuthenticationManager`, then calls `JwtTokenService`.

```java
public class AuthService {
    public AuthResponse authenticate(AuthRequest request) {
        ...
    }
}
```

---

### **8. Create SecurityConfig**

#### 📌 Why last?

Because now that your UserDetails, AuthManager, JWT, etc. are ready, you can wire them into the `SecurityFilterChain`.

```java
@Configuration
public class SecurityConfig {
    @Bean
    public SecurityFilterChain webSecurityFilterChain(HttpSecurity http) { ... }

    @Bean
    public SecurityFilterChain restApiSecurityFilterChain(HttpSecurity http) { ... }
}
```

---

### 🔄 **Optional Steps After Core Logic**

* Controller for login/signup (REST or web)
* Exception handlers
* Dto + Response classes
* Unit Tests

---

## 🧭 Summary of Learning Order

| Step | What to Write                  | Purpose                                |
| ---- | ------------------------------ | -------------------------------------- |
| 1️⃣  | `UserEntity`, `UserRepository` | Domain Model                           |
| 2️⃣  | `AuthUser`                     | Wrap your user in Spring `UserDetails` |
| 3️⃣  | `CustomUserDetailsService`     | Load users from DB                     |
| 4️⃣  | `PasswordEncoder` bean         | Password hashing                       |
| 5️⃣  | `AuthenticationManager` bean   | For authenticating login requests      |
| 6️⃣  | `JwtTokenService`              | Generate + decode JWT                  |
| 7️⃣  | `AuthService`                  | Authenticate + return token            |
| 8️⃣  | `SecurityConfig`               | Configure form login + REST login      |

---

## 💡 Tips to Learn by Yourself

* Follow this sequence multiple times until you can do it from scratch.
* Build small examples (start with just `/login` JWT endpoint).
* Look into `Spring Security FilterChain` order to understand how filters are triggered.
* Debug with breakpoints to see what’s happening during login.
* Read official [Spring Security docs](https://docs.spring.io/spring-security/reference/index.html) for deeper understanding.


---

## ✅ Short Descrioption

*Includes missing `JwtConfig` (Step 6b)*

---

### **1. Start with the Domain Model**

➡️ `UserEntity`, `UserRepository`
✅ Define what you're securing

---

### **2. Create the `UserDetails` Implementation**

➡️ `AuthUser implements UserDetails`
✅ Makes your user compatible with Spring Security

---

### **3. Create `CustomUserDetailsService`**

➡️ Loads users from DB
✅ Used by Spring Security to find user on login

---

### **4. Configure `PasswordEncoder` Bean**

➡️ BCrypt recommended
✅ Secures and verifies passwords

---

### **5. Set Up the `AuthenticationManager`**

➡️ Wires UserDetailsService + Encoder
✅ Used to authenticate login attempts

---

### **6. Build JWT Support (Split into 2 Parts)**

#### **6a. `JwtTokenService`**

➡️ Your custom logic to create and read tokens

```java
public class JwtTokenService {
    private final JwtEncoder encoder;
    private final JwtDecoder decoder;

    public String generateToken(Authentication auth) { ... }
    public Long extractExpirationTime(String token) { ... }
}
```

---

#### ✅ **6b. `JwtConfig`: Where Encoder & Decoder Are Wired** 🔥

📌 **Why it's needed**: Spring Security needs beans for JWT encoding/decoding. You can't use `JwtTokenService` unless you provide these.



> 🧠 **Tip**: If you're using symmetric signing (e.g., HS256), make sure the key is long and secret. Use a secure key from env variables or config files — **never hardcode secrets**.

---

### **7. Write the Authentication Service**

➡️ Uses `AuthenticationManager` + `JwtTokenService`
✅ Accepts login requests, returns JWT

---

### **8. Create SecurityConfig**

➡️ Two `SecurityFilterChain` beans (one for `/api/**`, one for `/admin/**`)
✅ Controls how and where authentication is enforced

---

## 🧭 Final Learning Order Summary (Updated)

| Step | Component                  | Role                                     |
| ---- | -------------------------- | ---------------------------------------- |
| 1️⃣  | `UserEntity`               | Define secured user data                 |
| 2️⃣  | `AuthUser`                 | Implements `UserDetails`                 |
| 3️⃣  | `CustomUserDetailsService` | Loads user by username                   |
| 4️⃣  | `PasswordEncoder`          | Hash/validate passwords                  |
| 5️⃣  | `AuthenticationManager`    | Validates credentials                    |
| 6a️⃣ | `JwtTokenService`          | Issues & validates tokens                |
| 6b️⃣ | `JwtConfig`                | Registers encoder/decoder beans          |
| 7️⃣  | `AuthService`              | Authenticates and issues tokens          |
| 8️⃣  | `SecurityConfig`           | Sets up `/admin/**` and `/api/**` chains |

---

## ✅ Bonus Tip for Configuration

Make sure your `application.yml` or `application.properties` includes:

```yaml
security:
  rest:
    jwt:
      key: your-very-secret-key-here
```

Or with `application.properties`:

```properties
security.rest.jwt.key=your-very-secret-key-here
```

