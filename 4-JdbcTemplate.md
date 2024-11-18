#  JdbcTemplate
JdbcTemplate est une classe fournie par Spring Framework dans le module Spring JDBC. Elle simplifie les opérations avec les bases de données en réduisant la quantité de code boilerplate liée à JDBC, comme la gestion des connexions, des requêtes et des résultats.

Avantages
Simplification : Réduit la complexité du code JDBC natif.
Gestion automatique des ressources : Gère automatiquement la fermeture des connexions, des déclarations et des résultats.
Flexibilité : Compatible avec les procédures stockées, les batchs et les fonctions personnalisées.
Prévention des fuites de ressources grâce à l'intégration avec le conteneur Spring.
Configuration
Avant d'utiliser JdbcTemplate, configurez une source de données (DataSource).

1. Ajouter les dépendances Maven
```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.3.27</version> <!-- Exemple de version -->
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.33</version>
</dependency>
```
2. Configurer la source de données
Avec Spring Boot, définissez les propriétés dans application.properties ou application.yml :

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/ma_base
spring.datasource.username=mon_utilisateur
spring.datasource.password=mon_mot_de_passe
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
```
3. Déclarer JdbcTemplate comme un Bean
Dans une application non Spring Boot, configurez JdbcTemplate dans une classe de configuration :

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.datasource.DriverManagerDataSource;

import javax.sql.DataSource;

@Configuration
public class AppConfig {

    @Bean
    public DataSource dataSource() {
        DriverManagerDataSource dataSource = new DriverManagerDataSource();
        dataSource.setDriverClassName("com.mysql.cj.jdbc.Driver");
        dataSource.setUrl("jdbc:mysql://localhost:3306/ma_base");
        dataSource.setUsername("mon_utilisateur");
        dataSource.setPassword("mon_mot_de_passe");
        return dataSource;
    }

    @Bean
    public JdbcTemplate jdbcTemplate(DataSource dataSource) {
        return new JdbcTemplate(dataSource);
    }
}
```
Utilisation de JdbcTemplate
1. Exemple d'insertion
```java
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.stereotype.Repository;

@Repository
public class ProductRepository {

    private final JdbcTemplate jdbcTemplate;

    public ProductRepository(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }

    public void saveProduct(String name, double price) {
        String sql = "INSERT INTO products (name, price) VALUES (?, ?)";
        jdbcTemplate.update(sql, name, price);
    }
}
```
2. Exemple de lecture
```java
import org.springframework.jdbc.core.RowMapper;
import org.springframework.stereotype.Repository;

import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.List;

@Repository
public class ProductRepository {

    private final JdbcTemplate jdbcTemplate;

    public ProductRepository(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }

    public List<Product> findAll() {
        String sql = "SELECT id, name, price FROM products";
        return jdbcTemplate.query(sql, new ProductRowMapper());
    }

    private static class ProductRowMapper implements RowMapper<Product> {
        @Override
        public Product mapRow(ResultSet rs, int rowNum) throws SQLException {
            Product product = new Product();
            product.setId(rs.getLong("id"));
            product.setName(rs.getString("name"));
            product.setPrice(rs.getDouble("price"));
            return product;
        }
    }
}
```
3. Exemple de mise à jour
```java
public void updateProductPrice(Long id, double newPrice) {
    String sql = "UPDATE products SET price = ? WHERE id = ?";
    jdbcTemplate.update(sql, newPrice, id);
}
```
4. Exemple de suppression
```java
public void deleteProduct(Long id) {
    String sql = "DELETE FROM products WHERE id = ?";
    jdbcTemplate.update(sql, id);
}
```
Méthodes principales de JdbcTemplate
update() : Pour les requêtes INSERT, UPDATE, et DELETE.
```java
jdbcTemplate.update("UPDATE table SET column = ? WHERE id = ?", value, id);
query() : Pour exécuter des requêtes SQL et obtenir une liste d'objets.
```
```java
jdbcTemplate.query("SELECT * FROM table", new RowMapper<>());
queryForObject() : Pour obtenir un seul objet.
```
```java
String name = jdbcTemplate.queryForObject(
    "SELECT name FROM table WHERE id = ?", String.class, id);
```
batchUpdate() : Pour exécuter des requêtes par lot.
```java
jdbcTemplate.batchUpdate("INSERT INTO table (col1, col2) VALUES (?, ?)", batchArgs);
```
Exemple de code avec JdbcTemplate.batchUpdate
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.BatchPreparedStatementSetter;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.stereotype.Service;

import java.sql.PreparedStatement;
import java.sql.SQLException;
import java.util.List;

@Service
public class ProductService {

    @Autowired
    private JdbcTemplate jdbcTemplate;

    public void batchInsertProducts(List<Product> products) {
        String sql = "INSERT INTO products (name, description, price) VALUES (?, ?, ?)";

        jdbcTemplate.batchUpdate(sql, new BatchPreparedStatementSetter() {
            @Override
            public int getBatchSize() {
                return products.size(); // Nombre de produits à insérer
            }

            @Override
            public void setValues(PreparedStatement ps, int i) throws SQLException {
                Product product = products.get(i);
                ps.setString(1, product.getName());
                ps.setString(2, product.getDescription());
                ps.setDouble(3, product.getPrice());
            }
        });
    }
}
```
Explication du code
BatchPreparedStatementSetter : Cette interface permet de définir les valeurs des paramètres pour chaque requête préparée dans le lot. La méthode setValues est appelée pour chaque élément de la liste, afin de définir les valeurs des paramètres (name, description, price) pour chaque produit.
getBatchSize : Cette méthode retourne le nombre total de requêtes SQL qui seront exécutées dans le lot (ici, la taille de la liste products).
jdbcTemplate.batchUpdate : La méthode prend la requête SQL ainsi que le BatchPreparedStatementSetter pour exécuter le lot de mises à jour.
Avantages de batchUpdate :
Performance améliorée : L'exécution de requêtes en lot réduit les allers-retours entre l'application et la base de données, ce qui améliore les performances lorsque vous devez effectuer plusieurs opérations d'écriture.
Réduction des transactions : Plutôt que d'exécuter plusieurs requêtes indépendantes, vous pouvez grouper toutes les opérations dans une seule transaction, réduisant ainsi la surcharge transactionnelle.

Points à noter
Les exceptions de JDBC sont converties en exceptions DataAccessException par Spring.
Utilisez les classes RowMapper ou BeanPropertyRowMapper pour mapper les résultats des requêtes SQL aux objets Java.
Préférez utiliser des paramètres ? pour éviter les injections SQL.
Exemple avec Spring Boot
Si vous utilisez Spring Boot, il vous suffit d'ajouter les dépendances et la configuration dans application.properties; Spring injectera automatiquement JdbcTemplate comme un Bean utilisable dans vos classes
<hr/><img width="1048" alt="image" src="https://github.com/user-attachments/assets/d1542239-0aa3-4224-bb6e-d1bdacb87e8c">
<hr/><img width="1153" alt="image" src="https://github.com/user-attachments/assets/9948e4d8-d286-457f-a514-253fc4eb2308">
<hr/><img width="712" alt="image" src="https://github.com/user-attachments/assets/27adc873-9217-4a25-b716-d0a9705b8548">
<hr/><img width="1116" alt="image" src="https://github.com/user-attachments/assets/ab2ab315-4f9e-4203-baed-1f2c6280263f">
<hr/><img width="545" alt="image" src="https://github.com/user-attachments/assets/f8cd99f3-b515-4fab-ac9e-ff9895182517">
<hr/><img width="1084" alt="image" src="https://github.com/user-attachments/assets/1d71c9ec-3f39-431c-9ee4-e549be1c11bf">
<hr/><img width="1127" alt="image" src="https://github.com/user-attachments/assets/5a134965-fdae-489c-8e50-d6211f81c32a">
<hr/><img width="723" alt="image" src="https://github.com/user-attachments/assets/c4e6b9cc-8d43-46fd-8810-8406bde1c2b1">
<hr/><img width="1036" alt="image" src="https://github.com/user-attachments/assets/668683f2-569b-4cbe-b24e-0640bf79f966">
<hr/><img width="1028" alt="image" src="https://github.com/user-attachments/assets/251caf6a-fbcd-46b1-8f46-ad3d6eda8226">
<hr/><img width="790" alt="image" src="https://github.com/user-attachments/assets/2c0d0577-2f83-4c06-9279-c6eab4099bd9">
<hr/><img width="1003" alt="image" src="https://github.com/user-attachments/assets/967d4afd-eb1f-446c-a1fa-54d1f64fbe31">
<hr/><img width="1120" alt="image" src="https://github.com/user-attachments/assets/f73e0bdb-6658-4ccd-b1de-bf70364d2562">
<hr/><img width="1088" alt="image" src="https://github.com/user-attachments/assets/64d92de9-b685-4a18-af17-6630c843eb7c">
<hr/><img width="1125" alt="image" src="https://github.com/user-attachments/assets/8816c300-3793-4054-a2a9-4f8a9f9f391b">
<hr/><img width="1139" alt="image" src="https://github.com/user-attachments/assets/30c9c05c-20bc-4b57-81d2-e29580e60404">
<hr/><img width="1129" alt="image" src="https://github.com/user-attachments/assets/5828976c-ce84-4b66-855f-06eb3e5992ea">
<hr/><img width="1030" alt="image" src="https://github.com/user-attachments/assets/8cc71fd8-939b-407e-beb9-117df981fbe4">
<hr/><img width="1121" alt="image" src="https://github.com/user-attachments/assets/7ecd1dd8-799a-4e67-957f-41db7c4090f6">
<hr/><img width="790" alt="image" src="https://github.com/user-attachments/assets/f412d3bd-c899-4fae-a470-058b05deaf12">

#  JdbcTemplate

<hr/><img width="794" alt="image" src="https://github.com/user-attachments/assets/bd8acf28-e842-4a54-bd41-ae5b3710eeac">
<hr/><img width="720" alt="image" src="https://github.com/user-attachments/assets/d3acb366-0039-423f-920c-4f4e1f242819">
<hr/><img width="1086" alt="image" src="https://github.com/user-attachments/assets/2c239a4a-be67-4778-9ee7-b7b1c38c66ac">
<hr/><img width="1103" alt="image" src="https://github.com/user-attachments/assets/5108cebe-2f9d-481c-b78f-cc9e2bb725b7">
<hr/><img width="727" alt="image" src="https://github.com/user-attachments/assets/868a538e-d8db-4ba4-bd72-8c0939ae99a8">
