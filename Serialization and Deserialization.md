### 🔄 **What is Serialization?**

**Serialization** is the process of converting a **Java object into a format that can be easily transported or stored** — most commonly into **JSON** or **XML**.

In Spring Boot, this often happens when you return an object from a controller. For example:

```java
@RestController
public class UserController {
    
    @GetMapping("/user")
    public User getUser() {
        return new User("Didar", 25);
    }
}
```

Here, Spring Boot uses **Jackson (a JSON library)** under the hood to **serialize** the `User` object into JSON when responding to a client.

**Resulting JSON (serialization):**

```json
{
  "name": "Didar",
  "age": 25
}
```

---

### 🔁 **What is Deserialization?**

**Deserialization** is the **opposite** process — converting a JSON (or any supported format) into a **Java object**.

In Spring Boot, this happens when you receive data in a **POST request**, for example:

```java
@PostMapping("/user")
public String createUser(@RequestBody User user) {
    return "User created: " + user.getName();
}
```

Here, Spring Boot takes the **JSON body** from the request and **deserializes** it into a `User` object using Jackson.

**Incoming JSON (deserialization):**

```json
{
  "name": "Didar",
  "age": 25
}
```

---

### 🧠 Under the Hood – How Spring Boot Handles It:

* Spring Boot uses **Jackson** by default.
* The annotations like `@RestController`, `@RequestBody`, and `@ResponseBody` trigger this conversion.
* Jackson uses Java **reflection** and the presence of **getters/setters or constructors** to perform the conversion.

---

### 📌 Summary:

| Operation       | Meaning            | Spring Boot Example                 |
| --------------- | ------------------ | ----------------------------------- |
| Serialization   | Java Object → JSON | Returning an object from a REST API |
| Deserialization | JSON → Java Object | Receiving JSON in a `@RequestBody`  |

---
