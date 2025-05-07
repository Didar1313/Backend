
### ✅ Recommended Order for Building a Feature (like `Project`):

1. **Define the Model (Entity or POJO)**

   * This gives structure to your data.
   * Example: `Project.java`

2. **Create the DTO (Data Transfer Object)**

   * Used to handle request/response data.
   * Example: `ProjectRequestDTO.java`, `ProjectResponseDTO.java` (optional but clean)

3. **Write the Service Layer**

   * Contains the business logic.
   * Example: `ProjectService.java`

4. **Create the Controller**

   * Handles incoming HTTP requests.
   * Example: `ProjectController.java`

### The Flow of data and why we use return in both method?

Client ← [JSON]
        ↑
   Controller ← service.getAll()
        ↑
    Service ← List<Project> from DB/memory

---

### 🔄 Why this order?

* **Model first**: Everything depends on the structure of your data.
* **DTOs second**: You define how you’ll receive and send data.
* **Service third**: Implement logic using the above.
* **Controller last**: Just plug the service methods.

---

### 📁 Folder Structure (Standard)

```
com.didar.TaskPro
├── controller
│   └── ProjectController.java
├── dto
│   └── ProjectRequestDTO.java
├── model
│   └── Project.java
├── service
│   └── ProjectService.java
```

---
