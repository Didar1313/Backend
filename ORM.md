# 🧠 What is ORM in Spring Boot?

**ORM** stands for **Object-Relational Mapping**.

Think of it like a **translator** between:

* Your **Java objects** (like `Project`, `User`, etc.)
* And the **tables** in your database (like `projects`, `users`, etc.)

Instead of writing raw SQL queries to interact with your database, ORM lets you use **Java code** to do the same thing. The ORM tool will automatically convert your Java objects into SQL queries behind the scenes.

---

## 🧱 Real-Life Example

Let’s say you’re managing a library.

You create a Java class:

```java
public class Book {
    private Long id;
    private String title;
    private String author;
}
```

In your database, there's a table called `books`:

```
| id | title        | author       |
|----|--------------|--------------|
| 1  | Clean Code   | Robert C. M. |
```

To get a book from the database, you would normally write:

```sql
SELECT * FROM books WHERE id = 1;
```

But with ORM, you write this in Java:

```java
Book book = bookRepository.findById(1L).get();
```

✅ That’s it! The ORM handles the SQL behind the scenes and gives you the result as a Java object.

---

## 🔧 How Spring Boot Uses ORM

Spring Boot uses **Hibernate** as its default ORM implementation, working alongside **JPA (Java Persistence API)**.

### Basic Workflow:

1. You create an **Entity class**:

   ```java
   @Entity
   public class Project {
       @Id
       @GeneratedValue
       private Long id;
       private String name;
       private String description;
   }
   ```

2. You define a **Repository** interface:

   ```java
   public interface ProjectRepository extends JpaRepository<Project, Long> {
   }
   ```

3. You use the repository in your service or controller:

   ```java
   projectRepository.save(project);         // INSERT
   projectRepository.findAll();             // SELECT *
   projectRepository.findById(id);          // SELECT WHERE id = ?
   projectRepository.deleteById(id);        // DELETE WHERE id = ?
   ```

You don’t need to write SQL queries — just work with Java code.

---

## 🔍 So, What is Hibernate?

**Hibernate** is the actual **library** or **tool** that performs all the ORM work in Spring Boot. It handles:

* Creating SQL queries for you
* Connecting to the database
* Converting table rows into Java objects
* Managing relationships between tables

Hibernate follows JPA standards and is used by Spring Boot automatically when you use `spring-boot-starter-data-jpa`.

> 📌 In simple terms:
> **JPA is the rulebook (interface), Hibernate is the worker (implementation).**

---


## ✅ Benefits of Using ORM (Hibernate)

| Without ORM                    | With ORM (Hibernate)           |
| ------------------------------ | ------------------------------ |
| Write raw SQL manually         | Write clean Java code          |
| Handle result sets yourself    | Java objects returned directly |
| Manual table-to-object mapping | Automatic mapping              |
| More error-prone               | Safer and easier to maintain   |

---

This is how **ORM with Hibernate in Spring Boot** works — letting you build apps faster, cleaner, and more safely.

