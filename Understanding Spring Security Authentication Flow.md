## 🔐 Understanding Spring Security Authentication Flow — With Decisions, Internals & Alternatives

Spring Security is a beast — it gives you enterprise-grade security out of the box, but it’s often hard to figure out *what actually happens* behind the scenes during authentication.

If you’re a backend dev working with Spring Boot, this guide will break down the full **authentication flow** step-by-step, including **why each component is used**, and what alternatives or customizations you can make. Perfect for beginners trying to get to the intermediate level.

---

## 🔄 Step-by-Step Authentication Flow (with Explanations)

---

### 1️⃣ **User Submits Login Request**

**What Happens:**
User sends a POST request to `/login` with `username` and `password`.

**Why it works by default:**
Spring Security auto-registers a **`UsernamePasswordAuthenticationFilter`** and maps it to `/login`.

**Alternative:**
You can change this endpoint by customizing `formLogin().loginProcessingUrl("/your-url")` in your `SecurityFilterChain`.

---

### 2️⃣ **`UsernamePasswordAuthenticationFilter` Captures Request**

**What It Does:**
Intercepts the request, extracts credentials, and creates a `UsernamePasswordAuthenticationToken`.

**Why this filter?**
Because this filter is placed **before** the `FilterSecurityInterceptor` and is specifically designed for form-based login.

**Alternatives:**

* `BasicAuthenticationFilter` → for HTTP Basic Auth
* `BearerTokenAuthenticationFilter` → for JWT tokens (with OAuth2 Resource Server)

**Customization Tip:**
Override this filter if you want to allow login using email or phone number instead of username.

---

### 3️⃣ **Token Passed to `AuthenticationManager`**

**What It Does:**
The `AuthenticationManager` (usually a `ProviderManager`) receives the `UsernamePasswordAuthenticationToken` and forwards it to one or more **`AuthenticationProviders`**.

**Why use `AuthenticationManager`?**
It separates the high-level flow from the actual logic of validating credentials — clean, pluggable, and testable.

**Alternative:**
You can build a custom `AuthenticationManager`, but rarely needed unless you use a non-standard provider like LDAP or external OAuth server.

---

### 4️⃣ **Delegated to `DaoAuthenticationProvider`**

**What It Does:**
This provider handles username-password auth. It calls your configured `UserDetailsService`.

**Why this one?**
Because it supports persistent user credentials (e.g., DB) and works well with Spring's `UserDetailsService`.

**Alternatives:**

* `LdapAuthenticationProvider` → for LDAP-based systems
* `JwtAuthenticationProvider` → when using JWTs
* You can also create your own `AuthenticationProvider` if needed.

---

### 5️⃣ **`UserDetailsService` Loads User**

**What It Does:**
It queries the DB (or another source) and returns a `UserDetails` object containing username, hashed password, and authorities.

**Why `UserDetailsService`?**
It abstracts user fetching logic and supports both in-memory and custom database logic.

**Alternatives:**

* `InMemoryUserDetailsManager` → for quick demos/tests
* Custom implementation → e.g., load by email, or from multiple tables

---

### 6️⃣ **Password Checked via `PasswordEncoder`**

**What It Does:**
Spring takes the password from `UserDetails`, hashes the raw input password, and compares them.

**Why?**
Because storing or comparing plaintext passwords is a **HUGE** security risk. Spring enforces secure password encoding.

**Alternatives:**

* `BCryptPasswordEncoder` (recommended default)
* `Argon2PasswordEncoder` (more secure, slightly slower)
* `NoOpPasswordEncoder` (never use in production)

---

### 7️⃣ **Authentication Success**

**What It Does:**
If everything checks out, an authenticated `UsernamePasswordAuthenticationToken` is returned.

**What’s inside?**

* `principal`: your `UserDetails`
* `authorities`: user roles/permissions
* `authenticated: true`

You’re now officially authenticated 🎉

---

### 8️⃣ **Security Context Updated**

**What It Does:**
The returned token is stored in a **`SecurityContext`**, which represents the security information for the current thread.

**Why?**
Because this allows Spring to enforce authorization (e.g., `@PreAuthorize`, `.hasRole()`) in controllers and services.

---

### 9️⃣ **SecurityContextHolder Stores It**

**What It Does:**
The `SecurityContext` is stored in a **`ThreadLocal`** using `SecurityContextHolder`.

**Why this design?**
It ensures the current user info is accessible *anywhere* in the request lifecycle — even in deep service layers.

**Alternatives:**

* You can use `MODE_INHERITABLETHREADLOCAL` if using async threads.
* Or store it in session if using a session-based strategy.

---

### 🔁 Bonus: `SecurityContextHolderFilter`

**What It Does:**
For every future request, this filter reloads the `SecurityContext` so you stay authenticated until logout/session expiration.

---

## 🔧 What Can You Customize?

| What                     | How                                           |
| ------------------------ | --------------------------------------------- |
| Login endpoint           | `formLogin().loginProcessingUrl("/custom")`   |
| Login filter             | Extend `UsernamePasswordAuthenticationFilter` |
| Password encoder         | `PasswordEncoder` bean                        |
| User loading logic       | Custom `UserDetailsService`                   |
| Auth provider            | Implement `AuthenticationProvider`            |
| Auth manager             | Custom `AuthenticationManager`                |
| Success/failure handlers | Set in `HttpSecurity` configuration           |

---

## 🎯 Summary

Spring Security's authentication flow is both powerful and pluggable:

* 🔄 Follows a clear sequence of filters → providers → services
* 🔐 Keeps user data secure in `SecurityContext`
* 🧱 Designed to let you plug in custom login logic anywhere
