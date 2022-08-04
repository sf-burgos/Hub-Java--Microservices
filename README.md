<p align="center">
 <img width="300px" src="https://i.im.ge/2022/08/04/FYEZQx.Perficient-Logo-Horz-NoTag.png" align="center" alt="GitHub Readme Stats" />
 <h1 align="center">Hub Java - Microservices</h1>
 <p align="center">Learn about microservices</p>
</p>
# Abstract
Using spring boot, openfeign, spring cloud, and eureka, you will create your first microservices and understand the benefits and disadvantages of this architecture mode.
<h1></h1>

### _🧑‍💻Mentor -> [Brayan Burgos](https://www.linkedin.com/in/brayan-steven-burgos-delgado-21a9a0178/)_

# Microservice, Part 1 (20%)

1. Please go to and upload a project with this settings -> [spring initializr](https://start.spring.io/)

![image](https://user-images.githubusercontent.com/45188320/182848607-409466bc-daca-4794-a133-48d97f8fa7ee.png)

2. Add in src/main/resources/application.properties

```
spring.application.name=service-products
server.port=8001
```

3. Add this dependency in pom.xml
```
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
```

4. Entity Product - Create the package /java/com/perficient/microservices/app/products/models/entity and after that create a Class _Product.java_
```
@Entity
@Getter
@Setter
@Table(name = "product")
public class Product implements Serializable {
  @Id
  @GeneratedValue(strategy =  GenerationType.IDENTITY)
  private Long id;
  private String name;
  private Double cost;
  @Column(name = "created_at")
  @Temporal(TemporalType.DATE)
  private Date createdAt;
}
```
5. Persistence Layer - Create the package src/main/java/com/perficient/microservices/app/products/models/repository and after that create a interface _IProductRepository.java_
```
public interface IProductRepository extends JpaRepository<Product, Long> {
}
```
6. Service Layer - Create the package src/main/java/com/perficient/microservices/app/products/models/service and create implementation _ProductServiceImpl.java_ and interface _IProductService.java_
```
public interface IProductService {
  List<Product> findAll();
  Product findById(Long id);
}
```
```
 @Service
public class ProductServiceImpl implements IProductService {

  @Autowired
  private IProductRepository productRepository;

  @Override
  @Transactional(readOnly = true)
  public List<Product> findAll() {
    return productRepository.findAll();
  }

  @Override
  public Product findById(Long id) {
    return productRepository.findById(id).orElse(null);
  }
}
```
7. Controller Layer - Create the package src/main/java/com/perficient/microservices/app/products/controller and create a class _ProductController.java_
```
@RestController
public class ProductController {

  @Autowired
  private IProductService productService;

  @GetMapping("/products")
  public List<Product> getAll() {
    return productService.findAll();
  }

  @GetMapping("/product/{id}")
  public Product getById(@PathVariable Long id) {
    return productService.findById(id);
  }
}
```
8. In the source src/main/resources, create import.sql and add those inserts and please add at least _20_, creativity with the new products:
```
INSERT INTO product (name, cost, created_at) VALUES ('Chocoramo', 1000, NOW());
INSERT INTO product (name, cost, created_at) VALUES ('Gansito', 800, NOW());
´´´´
9. Run the project and verify the endpoint in _localhost:8081_ (add a screenshot here)


 




