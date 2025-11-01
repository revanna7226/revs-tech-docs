Swagger/OpenAPI Documentation
=========================

Overview:
    Swagger (now known as **OpenAPI**) is a framework used to document REST APIs.  
    In **Spring Boot 3**, Swagger integration is typically achieved using the **Springdoc OpenAPI** library.  
    This library automatically generates interactive API documentation (Swagger UI) for your Spring Boot REST endpoints.

Key Features:
    - Auto-generates API documentation from Spring Web annotations.
    - Provides an interactive **Swagger UI** for testing APIs.
    - Supports **OpenAPI 3** specification.
    - Easily customizable with annotations and configuration properties.

Installation and Setup:
    **Step 1: Add Maven Dependency:**

    Add the following dependency to your ``pom.xml`` file:

    .. code-block:: xml

        <dependencies>
            <dependency>
                <groupId>org.springdoc</groupId>
                <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
                <version>2.6.0</version>
            </dependency>
        </dependencies>

    For **Gradle** users, add:

    .. code-block:: gradle

        implementation 'org.springdoc:springdoc-openapi-starter-webmvc-ui:2.6.0'

    **Step 2: Application Configuration:**

    Start your Spring Boot application. By default, the documentation is automatically available at:

    - **Swagger UI:** http://localhost:8080/swagger-ui.html  
    - **OpenAPI JSON:** http://localhost:8080/v3/api-docs

Basic Configuration Example:
    You can customize the OpenAPI configuration by defining a bean.

    .. code-block:: java

        @Configuration
        public class SwaggerConfig {

            @Bean
            public OpenAPI customOpenAPI() {
                return new OpenAPI()
                        .info(new Info()
                                .title("Employee Management API")
                                .version("1.0.0")
                                .description("API documentation for Employee Management System")
                                .contact(new Contact()
                                        .name("Revannaswamy N")
                                        .email("rev@example.com")
                                        .url("https://example.com"))
                                .license(new License()
                                        .name("Apache 2.0")
                                        .url("https://www.apache.org/licenses/LICENSE-2.0.html")));
            }
        }
    You can also customize the OpenAPI configuration by adding the annotation as below.

    .. code-block:: java

        @SpringBootApplication
        @OpenAPIDefinition(
            info = @Info(
                title = "Revs MyApp API",
                version = "1.0",
                description = "API documentation for managing and tracking expenses",
                contact = @Contact(
                    name = "Revannaswamy N",
                    email = "revanna.rsn@gmail.com",
                    url = "https://revanna7226.github.io"
                ),
                license = @License(
                    name = "Apache 2.0",
                    url = "https://www.apache.org/licenses/LICENSE-2.0.html"
                )
            )
        )
        public class MyappApplication {
            public static void main(String[] args) {
                SpringApplication.run(MyappApplication.class, args);
            }
        }        

Creating a Sample REST Controller:
    You can use the following **Swagger (OpenAPI)** annotations to enrich your documentation:

    - ``@Operation`` — Describes an endpoint.
    - ``@ApiResponse`` — Describes the possible responses.
    - ``@Parameter`` — Describes a method parameter.
    - ``@RequestBody`` — Describes a request body.
     
    .. code-block:: java

        @RestController
        @RequestMapping("/api/employees")
        @Tag(name = "Employee Controller", description = "APIs for managing employees")
        public class EmployeeController {

            private List<Employee> employees = new ArrayList<>();

            @GetMapping
            @Operation(summary = "Get a single account", description = "API endpoint to retrieve an account by its ID")
            @ApiResponses(value = {
                @ApiResponse(responseCode = "200", description = "Account found"),
                @ApiResponse(responseCode = "404", description = "Account not found")
            })
            public ResponseEntity<List<Employee>> getAllEmployees() {
                return ResponseEntity.ok(employees);
            }

            @PostMapping
            @SecurityRequirement(name = "bearerAuth")
            @Operation(summary = "Add a new employee", description = "API endpoint to add a new employee")
            public ResponseEntity<Employee> addEmployee(@RequestBody Employee employee) {
                employees.add(employee);
                return ResponseEntity.ok(employee);
            }

            @GetMapping("/{id}")
            @Operation(summary = "Get an employee", description = "API endpoint to get single employee")
            public ResponseEntity<Employee> getEmployeeById(
                    @Parameter(description = "ID of the Account", example = "1") 
                    @PathVariable int id) {
                return employees.stream()
                        .filter(emp -> emp.getId() == id)
                        .findFirst()
                        .map(ResponseEntity::ok)
                        .orElse(ResponseEntity.notFound().build());
            }
        }

Model/Entity Class Example:
    .. code-block:: java

        @Schema(description = "Employee entity representing a staff member")
        public class Employee {

            @Schema(description = "Unique identifier of the employee", example = "101")
            private int id;

            @Schema(description = "Full name of the employee", example = "John Doe")
            private String name;

            @Schema(description = "Designation of the employee", example = "Software Developer")
            private String designation;

            // Getters and Setters
        }

    You can define Hibernate Validators annotation to reflect.

Spring Boot Configuration Properties (Optional):
    You can customize Swagger UI and OpenAPI endpoints in your ``application.yml`` file:

    .. code-block:: yaml

        springdoc:
            api-docs:
                path: /api-docs
            swagger-ui:
                path: /swagger-ui
                operationsSorter: method
                tagsSorter: alpha
                display-request-duration: true

Conclusion:
    Swagger integration using **Springdoc OpenAPI** in **Spring Boot 3** provides a simple, automated, and powerful way to document and test REST APIs.  
    It helps developers and API consumers understand, visualize, and interact with your services easily.

    **Key Benefits:**

    - Zero boilerplate configuration.
    - OpenAPI 3.0 compliance.
    - Interactive testing environment.
    - Highly customizable and extendable.
