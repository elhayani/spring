La gestion des transactions dans Spring permet de garantir l'intégrité et la cohérence des données lorsque plusieurs opérations sont exécutées dans une base de données. Spring fournit un mécanisme puissant et flexible pour gérer les transactions, notamment via l'annotation @Transactional, qui peut être utilisée pour gérer des transactions de manière déclarative.

1. Configurer la gestion des transactions dans Spring
Pour activer la gestion des transactions dans une application Spring, il est nécessaire de configurer un gestionnaire de transactions. Dans une application Spring Boot, cela se fait généralement automatiquement si vous utilisez une base de données compatible avec Spring Data JPA ou JDBC.

2. Configurer le gestionnaire de transactions
Dans Spring, le gestionnaire de transactions (par exemple, DataSourceTransactionManager pour JDBC ou JpaTransactionManager pour JPA) est utilisé pour gérer les transactions. Voici un exemple de configuration d'un gestionnaire de transactions avec DataSourceTransactionManager.

Exemple de configuration avec JDBC dans Spring :
```java
@Configuration
@EnableTransactionManagement
public class DataSourceConfig {

    @Bean
    public DataSource dataSource() {
        // Configuration du DataSource
        return new DriverManagerDataSource("jdbc:mysql://localhost:3306/mydb", "username", "password");
    }

    @Bean
    public PlatformTransactionManager transactionManager() {
        return new DataSourceTransactionManager(dataSource());
    }
}
```
Dans cette configuration :

@EnableTransactionManagement active la gestion des transactions dans l'application.
DataSourceTransactionManager est utilisé comme gestionnaire de transactions.
3. Utilisation de l'annotation @Transactional
L'annotation @Transactional est utilisée pour définir une méthode ou une classe dans laquelle les transactions doivent être gérées. Lorsque cette annotation est appliquée à une méthode, Spring ouvre une transaction avant l'exécution de la méthode et la ferme (commit ou rollback) après son exécution.

Exemple simple avec @Transactional :
```java
@Service
public class ProductService {

    @Autowired
    private ProductRepository productRepository;

    @Transactional
    public void updateProductPrice(Long productId, Double newPrice) {
        Product product = productRepository.findById(productId).orElseThrow(() -> new ProductNotFoundException("Product not found"));
        product.setPrice(newPrice);
        productRepository.save(product);
    }
}
```
Dans cet exemple :

L'annotation @Transactional garantit que la méthode updateProductPrice s'exécute dans une seule transaction. Si une exception est lancée pendant l'exécution de la méthode, la transaction sera annulée (rollback).
Si tout se passe bien, la transaction sera validée (commit) automatiquement à la fin de la méthode.
4. Propriétés de @Transactional
L'annotation @Transactional permet de configurer plusieurs paramètres pour personnaliser le comportement des transactions.

Quelques paramètres courants de @Transactional :

###  propagation : 
Définit la stratégie de propagation de la transaction. Les options incluent :
<li>REQUIRED (par défaut) : Utilise une transaction existante ou en crée une nouvelle si aucune n'existe.</li>
<li>REQUIRES_NEW : Crée toujours une nouvelle transaction, suspendant toute transaction existante.</li>
<li>MANDATORY : La méthode doit être appelée dans le cadre d'une transaction existante, sinon une exception est lancée.</li>
<li>SUPPORTS : La méthode s'exécute dans une transaction si elle en existe une, sinon elle s'exécute sans transaction.</li>

###  isolation : 
Définit le niveau d'isolation de la transaction, c'est-à-dire la visibilité des données entre différentes transactions. Par exemple :

<li>READ_COMMITTED : Un autre processus ne peut pas lire une donnée qui n'a pas encore été validée.</li>
<li>SERIALIZABLE : Les transactions sont exécutées de manière séquentielle, garantissant l'absence de conflits.</li>

###  timeout : 
Détermine le délai d'expiration de la transaction. Si la transaction dépasse ce délai, elle est annulée.

###  readOnly : 
Indique si la transaction est en lecture seule, ce qui permet d'optimiser certaines opérations, notamment les requêtes en base de données.

Exemple avec @Transactional paramétré :
```java
@Transactional(propagation = Propagation.REQUIRES_NEW, isolation = Isolation.READ_COMMITTED, timeout = 30)
public void updateProductPrice(Long productId, Double newPrice) {
    // Logique de mise à jour du produit
}
```
5. Gestion des transactions dans les tests
Lors de l'écriture de tests, il est également possible de gérer les transactions pour garantir que les modifications apportées à la base de données pendant les tests sont annulées après chaque test.

Spring fournit une fonctionnalité pour cela avec l'annotation @Transactional sur les méthodes de test. Cela garantit que chaque test s'exécute dans une transaction, et que la transaction est annulée à la fin du test.

Exemple de test avec @Transactional :
```java
@RunWith(SpringRunner.class)
@SpringBootTest
@Transactional
public class ProductServiceTest {

    @Autowired
    private ProductService productService;

    @Test
    public void testUpdateProductPrice() {
        productService.updateProductPrice(1L, 150.0);
        // Assertions sur le produit mis à jour
    }
}
```
Dans ce cas, chaque méthode de test est exécutée dans une transaction, et si une modification est effectuée dans la base de données, elle est annulée à la fin du test.

6. Rollback explicite dans les tests
Si vous souhaitez effectuer un rollback explicite dans certains tests (par exemple, pour certains cas d'erreur), vous pouvez utiliser l'annotation @Rollback :

```java
@RunWith(SpringRunner.class)
@SpringBootTest
@Transactional
@Rollback
public class ProductServiceTest {
    // Tests avec rollback explicite
}
```
7. Gestion des exceptions et rollback
Par défaut, Spring effectue un rollback lorsque des exceptions non vérifiées (comme RuntimeException) se produisent. Cependant, vous pouvez personnaliser cela avec le paramètre rollbackFor de l'annotation @Transactional.

Exemple avec rollbackFor :
```java
@Transactional(rollbackFor = SQLException.class)
public void updateProductPrice(Long productId, Double newPrice) throws SQLException {
    // Code de mise à jour du produit
    if (newPrice < 0) {
        throw new SQLException("Price cannot be negative");
    }
}
```
Dans cet exemple, un SQLException entraînera un rollback de la transaction, même s'il est vérifié (checked exception).

Conclusion
La gestion des transactions avec Spring permet d'assurer la cohérence des données et d'optimiser les performances en contrôlant les transactions de manière déclarative. 
Les annotations comme @Transactional simplifient considérablement le travail de gestion des transactions dans les applications Spring, 
tout en offrant une grande flexibilité pour contrôler la manière dont les transactions sont traitées

<hr><img width="779" alt="image" src="https://github.com/user-attachments/assets/599845bd-fd20-48c6-8c66-d26537b8b3ea">
<hr><img width="1090" alt="image" src="https://github.com/user-attachments/assets/51b4793e-687f-4d39-86ae-2138b7b88969">
<hr><img width="1126" alt="image" src="https://github.com/user-attachments/assets/6ae8eabd-ddfa-4fa6-b52b-8eb29eca11ca">
<img width="752" alt="image" src="https://github.com/user-attachments/assets/84c54983-ebcc-48b6-80ad-8b23aeb60cdc">
<hr><img width="1123" alt="image" src="https://github.com/user-attachments/assets/fc18199e-d144-4f6b-b34a-c72161caa20a">
<hr><img width="1112" alt="image" src="https://github.com/user-attachments/assets/4be960d7-8c2d-48fb-9031-cad5c56096c0">
<hr><img width="635" alt="image" src="https://github.com/user-attachments/assets/fd3387d1-43e2-45e0-8d9d-6eaabebeccbb">
<hr><img width="1141" alt="image" src="https://github.com/user-attachments/assets/cd88e602-5d3a-4146-9216-04609cca01c5">
<hr><img width="771" alt="image" src="https://github.com/user-attachments/assets/9c243ed6-0a02-4ab9-b0b0-f65d31ae24af">
<hr><img width="1085" alt="image" src="https://github.com/user-attachments/assets/00296b31-6cea-4644-bece-972cf4c6c05e">
<hr><img width="1100" alt="image" src="https://github.com/user-attachments/assets/9bc4f4b5-942d-4a64-bf46-95ea5ea95423">
<hr><img width="1138" alt="image" src="https://github.com/user-attachments/assets/62050ab1-4b65-4cd0-9c2f-c263e4d16a6a">
<hr><img width="1050" alt="image" src="https://github.com/user-attachments/assets/8214b5f8-40b9-4359-a536-d2327b35c1e3">
<hr><img width="1127" alt="image" src="https://github.com/user-attachments/assets/efa7e2bd-5a7c-459b-abde-d864cb0a4a30">
<hr><img width="1117" alt="image" src="https://github.com/user-attachments/assets/3b60ed22-49e3-4d67-abae-6204a03a4787">
<hr><img width="793" alt="image" src="https://github.com/user-attachments/assets/911819b2-4829-4547-93df-7a97e733d163">
<hr><img width="1137" alt="image" src="https://github.com/user-attachments/assets/6a60ddb9-491a-4f73-a158-2aaeeebcbd33">
<hr><img width="1013" alt="image" src="https://github.com/user-attachments/assets/be2f9b7d-92d8-45fb-9e10-d9337e984573">
<hr><img width="1054" alt="image" src="https://github.com/user-attachments/assets/46b811e8-90f5-4dcb-b04e-07767530891c">
<hr><img width="1135" alt="image" src="https://github.com/user-attachments/assets/75d67135-c9ea-4521-a3b4-09948711309d">
<hr><img width="796" alt="image" src="https://github.com/user-attachments/assets/b29ba571-0e92-40c8-9cb4-efb320dfbaf9">
