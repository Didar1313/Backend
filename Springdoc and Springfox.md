### 📘 Springdoc OpenAPI

**Springdoc OpenAPI** is a library that automates the generation of API documentation for Spring Boot projects. It integrates seamlessly with Spring Boot and supports the OpenAPI 3 specification.

**Key Features:**

* **Automatic Documentation**: Scans your Spring Boot application at runtime to generate API documentation based on your controllers and models.
* **Swagger UI Integration**: Provides an interactive UI at `/swagger-ui.html` to explore and test your APIs.
* **Support for OpenAPI 3**: Generates documentation compliant with the OpenAPI 3 specification.
* **Customization**: Allows customization of the documentation path and other settings via application properties.

**Getting Started:**

To integrate Springdoc OpenAPI into your Spring Boot project, add the following dependency:

```xml
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
    <version>2.8.8</version>
</dependency>
```

After adding the dependency, you can access the Swagger UI at `http://localhost:8080/swagger-ui.html` and the OpenAPI documentation at `http://localhost:8080/v3/api-docs`.

---

### 🦊 Springfox

**Springfox** is another library that facilitates the generation of API documentation for Spring applications. It primarily supports the Swagger 2 specification.

**Key Features:**

* **Swagger 2 Support**: Generates documentation based on the Swagger 2 specification.
* **Integration with Spring MVC**: Works with Spring MVC to document APIs.
* **Customization**: Offers various annotations and configurations to customize the generated documentation.

**Getting Started:**

To use Springfox in your Spring Boot project, include the following dependencies:

```xml
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.9.2</version>
</dependency>
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.9.2</version>
</dependency>
```

After setting up, the Swagger UI is typically available at `http://localhost:8080/swagger-ui.html`.

**Note**: Springfox has compatibility issues with newer versions of Spring Boot (2.6 and above) due to changes in path matching strategies. Adjustments in configuration may be necessary to resolve these issues.

---

### 🔍 Comparison

| Feature               | Springdoc OpenAPI | Springfox          |
| --------------------- | ----------------- | ------------------ |
| OpenAPI 3 Support     | ✅ Yes             | ❌ No               |
| Swagger 2 Support     | ❌ No              | ✅ Yes              |
| Spring Boot 3 Support | ✅ Yes             | ⚠️ Limited         |
| Active Maintenance    | ✅ Active          | ⚠️ Limited         |
| Ease of Integration   | ✅ Easy            | ⚠️ Requires Config |

---

In summary, **Springdoc OpenAPI** is recommended for modern Spring Boot applications, especially those using Spring Boot 2.6 and above, due to its support for OpenAPI 3 and better compatibility. **Springfox** may still be suitable for legacy projects that rely on Swagger 2.

