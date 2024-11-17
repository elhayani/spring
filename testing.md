###  JUnit 5
JUnit 5 avec Mocks et Stubs
Dans un environnement de test, mocks et stubs sont utilisés pour simuler le comportement des dépendances d'un composant, permettant ainsi de tester un composant isolément.

Mocks : Simulent des objets en enregistrant leurs interactions (vérifications).
Stubs : Fournissent des réponses préprogrammées à des appels.
1. Configuration avec JUnit 5
Pour utiliser les mocks et stubs dans JUnit 5, on peut utiliser Mockito, un framework populaire.

Dépendance Maven
Ajoutez la dépendance suivante à votre fichier pom.xml :

```xml
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-core</artifactId>
    <version>5.5.0</version>
    <scope>test</scope>
</dependency>
```
2. Exemple avec un Service et Repository
2.1 Entité Product
```java
public class Product {
    private Long id;
    private String name;
    private double price;

    // Constructeurs, getters, et setters
}
```
2.2 Repository
```java
public interface ProductRepository {
    Optional<Product> findById(Long id);
    Product save(Product product);
}
```
2.3 Service
```java
public class ProductService {
    private final ProductRepository productRepository;

    public ProductService(ProductRepository productRepository) {
        this.productRepository = productRepository;
    }

    public Product getProductById(Long id) {
        return productRepository.findById(id)
                .orElseThrow(() -> new RuntimeException("Product not found"));
    }

    public Product saveProduct(Product product) {
        return productRepository.save(product);
    }
}
```
3. Test avec JUnit 5 et Mockito
3.1 Création d'un Test
Classe de test pour ProductService :
```java
@ExtendWith(MockitoExtension.class) // Extension Mockito pour JUnit 5
class ProductServiceTest {

    @Mock // Mock du repository
    private ProductRepository productRepository;

    @InjectMocks // Injecte les mocks dans la classe à tester
    private ProductService productService;

    @Test
    void testGetProductById_Success() {
        // Stub : configurer le comportement du mock
        Product mockProduct = new Product(1L, "Laptop", 1500.0);
        when(productRepository.findById(1L)).thenReturn(Optional.of(mockProduct));

        // Appeler la méthode à tester
        Product product = productService.getProductById(1L);

        // Assertions
        assertNotNull(product);
        assertEquals("Laptop", product.getName());
        assertEquals(1500.0, product.getPrice());
        
        // Vérifier les interactions
        verify(productRepository, times(1)).findById(1L);
    }

    @Test
    void testGetProductById_NotFound() {
        // Stub : simuler un produit non trouvé
        when(productRepository.findById(1L)).thenReturn(Optional.empty());

        // Tester que l'exception est levée
        assertThrows(RuntimeException.class, () -> productService.getProductById(1L));

        // Vérifier les interactions
        verify(productRepository, times(1)).findById(1L);
    }

    @Test
    void testSaveProduct() {
        // Stub : configurer le comportement du mock
        Product mockProduct = new Product(null, "Phone", 800.0);
        Product savedProduct = new Product(1L, "Phone", 800.0);
        when(productRepository.save(mockProduct)).thenReturn(savedProduct);

        // Appeler la méthode à tester
        Product product = productService.saveProduct(mockProduct);

        // Assertions
        assertNotNull(product);
        assertEquals(1L, product.getId());
        assertEquals("Phone", product.getName());

        // Vérifier les interactions
        verify(productRepository, times(1)).save(mockProduct);
    }
}
```
4. Explications
Annotations principales
@ExtendWith(MockitoExtension.class) :
Permet à Mockito de gérer les mocks dans les tests JUnit 5.
@Mock :
Crée une instance simulée (mock) pour une dépendance.
@InjectMocks :
Injecte les mocks dans la classe à tester.
when(...) :
Configure le comportement d'une méthode d'un mock.
verify(...) :
Vérifie si une méthode d'un mock a été appelée.
5. Concepts de base
Mocks
Utilisés pour vérifier les interactions.
Exemple :
```java
verify(productRepository, times(1)).findById(1L);
```
Stubs
Utilisés pour retourner des données spécifiques lors de l'appel d'une méthode.
Exemple :
```java
when(productRepository.findById(1L)).thenReturn(Optional.of(mockProduct));
```
6. Avantages des Mocks et Stubs
Permettent de tester une classe indépendamment de ses dépendances.
Simulent des cas difficiles à reproduire dans un environnement réel (par exemple, des erreurs ou des retours spécifiques).
7. Exemple de rapport d'exécution
Test réussi :
```makefile
Test: testGetProductById_Success - PASSED
Test: testGetProductById_NotFound - PASSED
Test: testSaveProduct - PASSED
```
Avec cette approche, vous pouvez tester vos services, contrôleurs, ou toute autre logique métier tout en simulant les dépendances nécessaires.

<img width="358" alt="image" src="https://github.com/user-attachments/assets/f7c12ef0-5242-47d6-a507-6cb668f5704a">
<img width="1054" alt="image" src="https://github.com/user-attachments/assets/86175a33-b31b-4bd8-b2eb-18e48f8bb5e0">
<img width="784" alt="image" src="https://github.com/user-attachments/assets/72df6258-b596-49ab-bcde-f78ec4a4de05">
<img width="1113" alt="image" src="https://github.com/user-attachments/assets/e30b2413-d018-4fba-9bcd-8c5767dc0871">
<img width="767" alt="image" src="https://github.com/user-attachments/assets/4aea16da-a4bb-4924-8d12-6131d81eb84d">

#  SpringJUnitConfig
@SpringJUnitConfig dans les tests Spring
L'annotation @SpringJUnitConfig est une combinaison de deux annotations très utilisées dans les tests Spring :

@ExtendWith(SpringExtension.class) (pour JUnit 5)
@ContextConfiguration (pour charger le contexte Spring).
Elle est utile pour simplifier la configuration des tests Spring basés sur JUnit 5.

1. Exemple simple avec @SpringJUnitConfig
1.1 Configuration Spring
Créez une classe de configuration simple pour vos tests :

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class TestConfig {

    @Bean
    public String testBean() {
        return "Hello from TestConfig!";
    }
}
```
1.2 Test avec @SpringJUnitConfig
```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.junit.jupiter.SpringJUnitConfig;

import static org.junit.jupiter.api.Assertions.assertEquals;

@SpringJUnitConfig(TestConfig.class) // Charge la configuration Spring pour ce test
class SpringJUnitConfigTest {

    @Autowired
    private String testBean;

    @Test
    void testBeanInjection() {
        assertEquals("Hello from TestConfig!", testBean);
    }
}
```
2. Pourquoi utiliser @SpringJUnitConfig ?
Combinaison simplifiée : Combine @ExtendWith(SpringExtension.class) et @ContextConfiguration pour éviter la duplication.
Chargement explicite : Charge les beans ou la configuration Spring spécifique aux tests.
Prise en charge des tests unitaires et d'intégration : Permet de tester des composants Spring avec le contexte d'application.
3. Comparaison avec des annotations alternatives
Avec @SpringJUnitConfig :

```java
@SpringJUnitConfig(TestConfig.class)
class MyTest {
    // Test code
}
```
Sans @SpringJUnitConfig (avec annotations explicites) :

```java
@ExtendWith(SpringExtension.class)
@ContextConfiguration(classes = TestConfig.class)
class MyTest {
    // Test code
}
```
Conclusion : Avec @SpringJUnitConfig, le code est plus concis.

4. Utilisation avec un fichier XML de configuration
Si vous utilisez une configuration basée sur XML, spécifiez le fichier dans @SpringJUnitConfig :

```java
@SpringJUnitConfig(locations = "classpath:application-context.xml")
class XmlConfigTest {

    @Autowired
    private MyService myService;

    @Test
    void testXmlConfiguration() {
        assertNotNull(myService);
    }
}
```
5. Exemples avancés
5.1 Avec plusieurs fichiers de configuration
```java
@SpringJUnitConfig(classes = {AppConfig.class, TestConfig.class})
class MultiConfigTest {
    // Test code
}
```
5.2 Tests avec profils Spring
```java
@SpringJUnitConfig(TestConfig.class)
@ActiveProfiles("test") // Active le profil "test"
class ProfileTest {

    @Autowired
    private MyService myService;

    @Test
    void testServiceWithTestProfile() {
        assertNotNull(myService);
    }
}
```
6. Points importants
JUnit 5 requis : @SpringJUnitConfig est basé sur JUnit 5 et la Spring TestContext Framework.
Prend en charge les tests d'intégration : Vous pouvez facilement intégrer des composants Spring à vos tests.
Simplifie les tests Spring : Élimine le besoin d'ajouter manuellement @ExtendWith et @ContextConfiguration.
7. Résultat attendu
Lors de l'exécution des tests ci-dessus, Spring chargera uniquement les configurations nécessaires, ce qui accélère le processus de test tout en garantissant que les beans fonctionnent comme prévu

### test inegration

Exemple de test d'intégration avec Spring Boot
Un test d'intégration vérifie l'interaction entre plusieurs composants ou services dans une application. Avec Spring Boot, on peut écrire un test d'intégration en configurant un environnement Spring pour tester le système dans son ensemble.

Structure d'un test d'intégration
1. Pré-requis
Annoter la classe de test avec @SpringBootTest.
Charger un contexte Spring complet pour interagir avec les beans ou services nécessaires.
Utiliser TestRestTemplate ou MockMvc pour effectuer des appels aux endpoints REST.
2. Exemple de scénario
Nous avons une API REST qui gère des produits avec les endpoints suivants :

POST /api/products/save : Ajouter un produit.
GET /api/products/{id} : Récupérer un produit par son ID.
3. Exemple de code
3.1 Entité Product
```java
@Entity
public class Product {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private String description;
    private double price;

    // Getters et Setters
}
```
3.2 Repository ProductRepository
```java
@Repository
public interface ProductRepository extends JpaRepository<Product, Long> {
}
```
3.3 Service ProductService
```java
@Service
public class ProductService {
    @Autowired
    private ProductRepository productRepository;

    public Product saveProduct(Product product) {
        return productRepository.save(product);
    }

    public Optional<Product> getProductById(Long id) {
        return productRepository.findById(id);
    }
}
```
3.4 Controller ProductController
```java
@RestController
@RequestMapping("/api/products")
public class ProductController {
    @Autowired
    private ProductService productService;

    @PostMapping("/save")
    public ResponseEntity<Product> saveProduct(@RequestBody Product product) {
        Product savedProduct = productService.saveProduct(product);
        return ResponseEntity.ok(savedProduct);
    }

    @GetMapping("/{id}")
    public ResponseEntity<Product> getProductById(@PathVariable Long id) {
        return productService.getProductById(id)
                .map(ResponseEntity::ok)
                .orElse(ResponseEntity.notFound().build());
    }
}
```
4. Test d'intégration
4.1 Classe de test
```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@RunWith(SpringRunner.class)
public class ProductControllerIntegrationTest {

    @Autowired
    private TestRestTemplate restTemplate;

    @Autowired
    private ProductRepository productRepository;

    @BeforeEach
    public void setUp() {
        productRepository.deleteAll(); // Nettoyer la base de données avant chaque test
    }

    @Test
    public void testSaveProduct() {
        // Préparation des données
        Product product = new Product();
        product.setName("Laptop");
        product.setDescription("High-end laptop");
        product.setPrice(1500.0);

        // Appel à l'API
        ResponseEntity<Product> response = restTemplate.postForEntity(
                "/api/products/save", product, Product.class);

        // Assertions
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertNotNull(response.getBody().getId());
        assertEquals("Laptop", response.getBody().getName());
    }

    @Test
    public void testGetProductById() {
        // Préparation des données
        Product savedProduct = productRepository.save(new Product("Phone", "Smartphone", 800.0));

        // Appel à l'API
        ResponseEntity<Product> response = restTemplate.getForEntity(
                "/api/products/" + savedProduct.getId(), Product.class);

        // Assertions
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals("Phone", response.getBody().getName());
    }
}
```
5. Explications des annotations utilisées
@SpringBootTest :
Charge tout le contexte Spring pour exécuter le test dans un environnement réel.
@RunWith(SpringRunner.class) :
Fournit un environnement d'exécution pour les tests JUnit avec Spring.
@BeforeEach :
S'exécute avant chaque méthode de test pour configurer ou réinitialiser les données nécessaires.
TestRestTemplate :
Utilisé pour effectuer des appels HTTP aux endpoints de l'application.
6. Résultat attendu
Lors de l'exécution des tests :
testSaveProduct : Vérifie que l'API /save ajoute correctement un produit.
testGetProductById : Vérifie que l'API /id retourne le produit sauvegardé.
7. Points importants
Base de données réelle ou mémoire : Pour les tests, utilisez une base de données en mémoire (comme H2) pour éviter d'affecter les données réelles.
Nettoyage des données : Assurez-vous que chaque test commence avec une base de données propre.
Assertions : Vérifiez les réponses HTTP ainsi que les données retournées
<img width="766" alt="image" src="https://github.com/user-attachments/assets/c7181c86-8cae-453a-9d87-f882167dd5fd">
<img width="771" alt="image" src="https://github.com/user-attachments/assets/c97bd81b-bb92-4aab-bd0f-182845ae79b4">
<img width="967" alt="image" src="https://github.com/user-attachments/assets/a20dfec2-0c03-44db-9f03-4d793ad22223">
<img width="1037" alt="image" src="https://github.com/user-attachments/assets/cb1d0907-979c-4ba5-990e-0a8f732003c4">
<img width="1119" alt="image" src="https://github.com/user-attachments/assets/4c7afbfa-b32f-45ce-88e2-ea05c66145eb">
<img width="1137" alt="image" src="https://github.com/user-attachments/assets/a31fa113-ef6a-4363-9a1d-78d883fbf12b">
<img width="1127" alt="image" src="https://github.com/user-attachments/assets/3cc08c1d-d075-4b47-a9d1-666fea1d6627">
<img width="1145" alt="image" src="https://github.com/user-attachments/assets/06f57d72-df4a-48b3-9581-9e29bb176c7f">
<img width="1128" alt="image" src="https://github.com/user-attachments/assets/7a72d746-e55d-4d36-8ed8-0e9053f1bd44">
<img width="1110" alt="image" src="https://github.com/user-attachments/assets/b3048b66-79ce-46cc-8722-92c47c37802e">
<img width="1125" alt="image" src="https://github.com/user-attachments/assets/9048fa5d-a46c-43cc-9186-49ea19f64497">
<img width="1108" alt="image" src="https://github.com/user-attachments/assets/c48be2c6-6fd2-4f76-ad75-415201fd4034">

###  Profiles

Tester les Spring Profiles avec JUnit et Mockito
Dans Spring, les profils permettent de configurer différentes parties d'une application en fonction de l'environnement (par exemple, dev, prod, ou test). Lors des tests, il est courant de vérifier le comportement des beans activés dans un profil spécifique.

1. Configurer des profils dans votre application
1.1 Exemple de configuration de profils
application-dev.properties
```properties
app.environment=Development
application-prod.properties
```
```properties
app.environment=Production
```
2. Classe de service avec profils
Créons un service qui utilise des profils différents :

```java
import org.springframework.context.annotation.Profile;
import org.springframework.stereotype.Service;

public interface EnvironmentService {
    String getEnvironment();
}

@Service
@Profile("dev")
class DevEnvironmentService implements EnvironmentService {
    @Override
    public String getEnvironment() {
        return "Development Environment";
    }
}

@Service
@Profile("prod")
class ProdEnvironmentService implements EnvironmentService {
    @Override
    public String getEnvironment() {
        return "Production Environment";
    }
}
```
3. Tester les profils avec JUnit
Lors des tests, vous pouvez activer un profil spécifique pour vérifier les beans correspondants.

3.1 Dépendances Maven
Ajoutez les dépendances suivantes pour les tests Spring et Mockito :

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```
3.2 Exemple de test unitaire
Test de service pour un profil dev :
```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.ActiveProfiles;

import static org.junit.jupiter.api.Assertions.assertEquals;

@SpringBootTest
@ActiveProfiles("dev") // Active le profil 'dev'
class DevEnvironmentServiceTest {

    @Autowired
    private EnvironmentService environmentService;

    @Test
    void testGetEnvironment() {
        String environment = environmentService.getEnvironment();
        assertEquals("Development Environment", environment);
    }
}
```
Test de service pour un profil prod :
```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.ActiveProfiles;

import static org.junit.jupiter.api.Assertions.assertEquals;

@SpringBootTest
@ActiveProfiles("prod") // Active le profil 'prod'
class ProdEnvironmentServiceTest {

    @Autowired
    private EnvironmentService environmentService;

    @Test
    void testGetEnvironment() {
        String environment = environmentService.getEnvironment();
        assertEquals("Production Environment", environment);
    }
}
```
4. Test d’intégration avec plusieurs profils
Vous pouvez également tester les interactions en activant plusieurs profils.

Exemple :
```java
@SpringBootTest
@ActiveProfiles({"dev", "test"}) // Active plusieurs profils
class MultiProfileTest {

    @Autowired
    private EnvironmentService environmentService;

    @Test
    void testEnvironmentService() {
        String environment = environmentService.getEnvironment();
        assertEquals("Development Environment", environment);
    }
}
```
5. Explication des annotations utilisées
@SpringBootTest :
Charge le contexte Spring pour les tests d'intégration.
@ActiveProfiles :
Spécifie quel profil Spring doit être actif pendant le test.
@Autowired :
Injecte les dépendances gérées par Spring dans le test.
6. Exécution et résultats attendus
Résultat des tests :
```
DevEnvironmentServiceTest.testGetEnvironment - PASSED
ProdEnvironmentServiceTest.testGetEnvironment - PASSED
MultiProfileTest.testEnvironmentService - PASSED
```
Avec cette configuration, vous pouvez valider que les beans spécifiques à chaque profil fonctionnent comme attendu dans des environnements différents (dev, prod, etc.).

<img width="644" alt="image" src="https://github.com/user-attachments/assets/c95219bb-6f1a-4443-a107-74fb55aed84b">
















