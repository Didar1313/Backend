
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

### **6. Build JWT Support (`JwtTokenService`)**

#### 📌 Why?

Once `AuthenticationManager` can validate users, we want to generate and parse JWT tokens.

```java
public class JwtTokenService {
    public String generateToken(Authentication authentication) { ... }
    public Long extractExpirationTime(String token) { ... }
}
```

---

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

