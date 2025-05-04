### 🧾 What is a DTO?

A **DTO** is a simple Java class used to **transfer data** between layers (like from client → controller, or controller → service).

**Why use a DTO instead of an Entity (like `Project`) directly?**

* **Security**: You expose only the fields you want.
* **Control**: You can customize the structure of incoming or outgoing data.
* **Separation of concerns**: Keep API models separate from database models.
* **Validation**: Easier to validate input fields.

---

### ✅ Example: Using a DTO in Your `createProject` Method

#### Step 1: Define a DTO

```java
public class ProjectRequestDTO {
    private String name;
    private String description;

    // Constructors
    public ProjectRequestDTO() {}

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }
}
```

---

#### Step 2: Use the DTO in Controller

```java
@PostMapping("/api/projects")
public Project createProject(@RequestBody ProjectRequestDTO projectDTO) {
    Project project = new Project(projectDTO.getName(), projectDTO.getDescription());
    projects.add(project);
    return project;
}
```

---

### 🎯 What changed?

Before:

```json
{
  "name": "X",
  "description": "Y"
}
```

mapped directly to a `Project` object.

Now:

```json
{
  "name": "X",
  "description": "Y"
}
```

maps to a `ProjectRequestDTO` object, which is **converted manually** to a `Project`.

