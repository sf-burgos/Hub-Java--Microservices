<p align="center">
 <img width="300px" src="https://i.im.ge/2022/08/04/FYEZQx.Perficient-Logo-Horz-NoTag.png" align="center" alt="GitHub Readme Stats" />
 <h1 align="center">Hub Java - Microservices</h1>
 <p align="center">Learn about microservices</p>
</p>

# Abstract
Using spring boot, openfeign, spring cloud, and eureka, you will create your first microservices and understand the benefits and disadvantages of this architecture mode.
<h1></h1>

### _🧑‍💻Mentors -> [Brayan Burgos](https://www.linkedin.com/in/brayan-steven-burgos-delgado-21a9a0178/) & [Nicolas Ordoñez](https://www.linkedin.com/in/nicolas-ordonez-chala/)_



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
8. In the source src/main/resources, create _import.sql_ and add those inserts and please add at least _20_, 💡 creativity with the new products:
```
INSERT INTO product (name, cost, created_at) VALUES ('Chocoramo', 1000, NOW());
INSERT INTO product (name, cost, created_at) VALUES ('Gansito', 800, NOW());
```
9. Run the project and verify the endpoint in _localhost:8001_ (please, add some screenshots here 📷)
![image](https://user-images.githubusercontent.com/45188320/182855480-dba7ff8f-2dc3-4f6c-9729-8826748a358c.png)

10. Verify the funtiality with _postman_ or your favorite API platform for developers to design(please, add some screenshots here with your JSONs 📷): 
![image](https://user-images.githubusercontent.com/45188320/182855289-9fc7a0ba-dda5-4181-a1dc-22681c2d07d3.png)
![image](https://user-images.githubusercontent.com/45188320/182855615-fc073bfb-e540-433c-9982-168f4932bbce.png)

# Microservice, Part 2 (20%)

11. Repeat the first step and create a new microservice with the same dependencies excluded _H2_ and adding _Spring Data Jpa_:
![image](https://user-images.githubusercontent.com/45188320/182857493-845590b1-9103-4bd8-ac9f-d372ea87d5e3.png)
![image](https://user-images.githubusercontent.com/45188320/182858280-c61aa3dd-53d3-4fef-8539-f0a3dd906ebc.png)

12. So, this microservice _items_ is very similar like _products_. The structure is the same: 

```
@Getter
@Setter
@AllArgsConstructor
@NoArgsConstructor
public class Item {

  private Product product;
  private Integer amount;

  public Double getTotal() {
    return product.getCost() * amount.doubleValue();
  }

}
```
```
@Getter
@Setter
public class Product {

  private Long id;
  private String name;
  private Double cost;
  private Date createdAt;
}
```
```
spring.application.name = service-item
spring.port = 8002
```
```
public interface IItemService {

  List<Item> findAll();

  Item findById(Long id, Integer amount);
}
```
```
@Service
public class ItemServiceImpl implements IItemService {

  @Autowired
  private RestTemplate clientRest;

  @Override
  public List<Item> findAll() {
    return Arrays.stream(
            Objects.requireNonNull(clientRest.getForObject("http://localhost:8001/products", Product[].class)))
        .map(product -> new Item(product, 1)).collect(
            Collectors.toList());
  }

  @Override
  public Item findById(Long id, Integer amount) {
    Map<String, String> pathVariables = new HashMap<>();
    pathVariables.put("id", id.toString());
    Product product = clientRest.getForObject("http://localhost:8001/product/{id}", Product.class, pathVariables);
    return new Item(product, amount);
  }
}
```

```
@RestController
public class ItemController {

  @Autowired
  private IItemService itemService;

  @GetMapping("/items")
  public List<Item> findAll() {
    return itemService.findAll();
  }

  @GetMapping("/item/{id}/amount/{amount}")
  public Item detail(@PathVariable Long id, @PathVariable Integer amount) {
    return itemService.findById(id, amount);
  }
}

```
13. Questions for you. (Please write your answers here ✏️)

- Explain with your words the Class _ItemServiceImpl.java_
- Why the class ItemServiceImpl.java needs RestTemplate with the annotation @Autowired
- Go to implementation RestTemplate and describe the functionality.
- What is the annotation @PathVariable in the ItemController.java. Type here:

14. Run the project and verify the endpoint in _localhost:8002_ and use postman with the enpoints (please, add some screenshots here 📷)

![image](https://user-images.githubusercontent.com/45188320/182864373-817d8e32-45f3-4e69-85e6-4a628e71d6e7.png)
![image](https://user-images.githubusercontent.com/45188320/182864457-a72cc40a-4325-4ee9-ae9c-03ab375164de.png)
![image](https://user-images.githubusercontent.com/45188320/182864507-28e6c254-880a-453c-b1bc-f9de9ae171c7.png)

# Microservice, Part 3 (20%)

Note: please read after start this site [REST Client: Feign](https://cloud.spring.io/spring-cloud-netflix/multi/multi_spring-cloud-feign.html)

15. Add the _spring-cloud-version_, the dependencies (spring cloud and openfeign) and dependencyManagement in the _pom.xml_ in the microservice _ITEM_:
```
<properties>
	<java.version>17</java.version>
	<spring-cloud.version>2021.0.3</spring-cloud.version>
</properties>
```
```
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-config</artifactId>
</dependency>
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-bootstrap</artifactId>
</dependency>
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-openfeign</artifactId>
	<version>3.1.3</version>
</dependency>
```
```
<dependencyManagement>
	<dependencies>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-dependencies</artifactId>
			<version>${spring-cloud.version}</version>
			<type>pom</type>
			<scope>import</scope>
		</dependency>
	</dependencies>
</dependencyManagement>
```
16. Add the annotation _microservice-item/src/main/java/com/perficient/microservices/app/items/ItemsApplication.java_ (main class)
```
@EnableFeignClients
```
17. Create a package _microservice-item/src/main/java/com/perficient/microservices/app/items/clients_ and build a inteface _ProductClientRest.java_ and implementation _ItemServiceFeign.java_  
```
@FeignClient(name="service-products", url="localhost:8001")
public interface ProductClientRest {

  @GetMapping("/products")
  public List<Product> getAll();

  @GetMapping("/product/{id}")
  public Product getById(@PathVariable Long id);
}
```
```
@Service("itemServiceFeign")
@Primary
public class ItemServiceFeign implements IItemService {

  @Autowired
  private ProductClientRest productClientRest;

  @Override
  public List<Item> findAll() {
    return productClientRest.getAll().stream().map(product -> new Item(product, 1)).collect(Collectors.toList());
  }

  @Override
  public Item findById(Long id, Integer amount) {
    return new Item(productClientRest.getById(id), amount);
  }
}
```
18. Try to run your project, _What is happening?_ (Please write your answers here ✏️) and upload images to explain the situation:
19. Add the annotation _@Qualifier_ in _ItemController.java_:
```
  @Autowired
  @Qualifier("itemServiceFeign")
  private IItemService itemService;
```
20. Run the project and verify the endpoint in _localhost:8002_ and use postman with the enpoints (please, add some screenshots here 📷)

# Microservice, Part 4 (20%)
21. Create a new project: 
![image](https://user-images.githubusercontent.com/45188320/182936369-a9f59f53-c522-494a-8ed9-9c5f5b3c4abe.png)

22. Add in _eureka-server/src/main/resources/application.properties_: 
```
spring.application.name=service-eureka
server.port=8761
eureka.client.register-with-eureka=false
eureka.client.fetch-registry=false
```

23. Add the _dependency_ in _eureka-server/pom.xml_
```
<dependency>
	<groupId>org.glassfish.jaxb</groupId>
	<artifactId>jaxb-runtime</artifactId>
</dependency>
```
24. add this annotation in _eureka-server/src/main/java/com/perficient/microservices/app/eureka/EurekaApplication.java_
```
@EnableEurekaServer
```

