#  SpringBoot
Spring Boot est un framework basé sur Spring, conçu pour simplifier le développement d’applications Java. Il permet de créer des applications prêtes à l’emploi avec un minimum de configuration. Grâce à son approche opinionnée, il réduit la complexité en fournissant des configurations par défaut adaptées à la plupart des besoins.

Caractéristiques principales de Spring Boot :
1. Configuration automatique (Auto-Configuration)
Spring Boot analyse les dépendances présentes dans votre projet et configure automatiquement les beans nécessaires.

Exemple :
Si vous incluez spring-boot-starter-web dans votre projet, Spring Boot configure automatiquement un serveur Tomcat intégré et un contrôleur REST.

2. Starters
Les starters sont des dépendances préconfigurées qui regroupent les bibliothèques nécessaires à une fonctionnalité particulière.

Exemple :
xml
Copier le code
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
Avec ce starter, vous avez tout ce qu’il faut pour créer des applications web (Tomcat, Spring MVC, etc.).

3. Serveur intégré
Spring Boot fournit un serveur web intégré comme Tomcat, Jetty ou Undertow. Vous n'avez pas besoin de déployer votre application dans un serveur externe, vous pouvez directement exécuter le fichier JAR généré.

Commande pour démarrer :
bash
Copier le code
java -jar my-application.jar
4. Spring Boot CLI
Une interface en ligne de commande pour créer rapidement des applications Spring Boot. Elle est utile pour les prototypes rapides.

Exemple :
bash
Copier le code
spring init --dependencies=web,data-jpa my-app
5. Actuator
Spring Boot Actuator fournit des points de terminaison prêts à l’emploi pour surveiller et gérer votre application (santé, métriques, etc.).

Exemple :
/actuator/health : Vérifie si l'application est opérationnelle.
/actuator/metrics : Affiche des métriques sur les performances.
6. Facilité de configuration
Spring Boot permet de configurer facilement les propriétés via les fichiers application.properties ou application.yml.

Exemple (application.properties) :
properties
Copier le code
server.port=8081
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=password
7. Tests simplifiés
Spring Boot offre un support puissant pour écrire des tests unitaires et d'intégration avec des annotations comme :

@SpringBootTest
@WebMvcTest
@DataJpaTest
Exemple minimal d’une application Spring Boot :
Étape 1 : Créer un projet avec Maven
pom.xml :

xml
Copier le code
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
Étape 2 : Écrire le point d’entrée
MyApplication.java :

java
Copier le code
@SpringBootApplication
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
Étape 3 : Ajouter un contrôleur REST
HelloController.java :

java
Copier le code
@RestController
public class HelloController {
    @GetMapping("/hello")
    public String sayHello() {
        return "Hello, Spring Boot!";
    }
}
Principaux Starters Spring Boot
Starter	Description
spring-boot-starter-web	Pour développer des applications Web avec Spring MVC.
spring-boot-starter-data-jpa	Intègre JPA pour les bases de données relationnelles.
spring-boot-starter-security	Ajoute des fonctionnalités de sécurité.
spring-boot-starter-test	Inclut des dépendances pour les tests.
spring-boot-starter-thymeleaf	Intègre le moteur de template Thymeleaf.
Avantages de Spring Boot :
Réduction de la configuration grâce à l'auto-configuration.
Serveur intégré pour démarrer rapidement vos applications.
Écosystème robuste grâce aux starters.
Prêt pour la production avec des outils comme Actuator.
Support des microservices grâce à des projets comme Spring Cloud.

<hr><img width="760" alt="image" src="https://github.com/user-attachments/assets/9604be9e-433c-43cf-8e20-23f07b6671f6">
<hr><img width="1170" alt="image" src="https://github.com/user-attachments/assets/1bdf13db-05ae-493a-8110-b2c9b2b337bd">
<hr><img width="1122" alt="image" src="https://github.com/user-attachments/assets/d6604220-eb17-4269-a646-82b817a5f931">
<hr><img width="780" alt="image" src="https://github.com/user-attachments/assets/8a3cb501-5549-49f2-901f-348edb0e3711">
<hr><img width="1109" alt="image" src="https://github.com/user-attachments/assets/674c59af-d444-4f08-add9-a6295c9ebbbe">
<hr><img width="1038" alt="image" src="https://github.com/user-attachments/assets/8d1a1aed-39cb-4f43-81ef-2cd2b98ba416">
<hr><img width="797" alt="image" src="https://github.com/user-attachments/assets/c55f6604-0772-45de-8180-e372e7d3fdef">
<hr><img width="1137" alt="image" src="https://github.com/user-attachments/assets/654ece56-a144-422e-96b0-8d163b9b9357">
<hr><img width="764" alt="image" src="https://github.com/user-attachments/assets/e17acadf-ee4b-4204-af89-681a9862ffcb">
<hr><img width="768" alt="image" src="https://github.com/user-attachments/assets/519ef1d7-f4ff-491b-9864-79310fc0e007">

###  Config

<hr><img width="686" alt="image" src="https://github.com/user-attachments/assets/6aaf7684-6f4a-4bf7-b8f2-f205b54cca9b">
<hr><img width="784" alt="image" src="https://github.com/user-attachments/assets/67c66973-ffdf-4980-aa98-5eab18b80a8a">
<hr><img width="1141" alt="image" src="https://github.com/user-attachments/assets/d078db02-80b6-4678-baaf-2a0405c81693">
<hr><img width="1074" alt="image" src="https://github.com/user-attachments/assets/2d6e1d2c-5f63-475a-8a1e-6a914e55f9ae">
<hr><img width="811" alt="image" src="https://github.com/user-attachments/assets/789f09e1-ea6d-4edb-928b-3f904019f4bc">
<hr><img width="1063" alt="image" src="https://github.com/user-attachments/assets/2ec0a1aa-f056-4e94-a26e-5ecadbb0ff76">
<hr><img width="1095" alt="image" src="https://github.com/user-attachments/assets/058b844a-5d0a-47c5-8deb-a1de7a6a23d6">
<hr><img width="1116" alt="image" src="https://github.com/user-attachments/assets/8c2d1e9e-cb6a-42f5-afc9-f44c49315d86">
<hr><img width="1122" alt="image" src="https://github.com/user-attachments/assets/12b1bc74-7c6d-4301-9c08-294d38fd0e0b">
<hr><img width="1144" alt="image" src="https://github.com/user-attachments/assets/f236b0c6-2dff-4d81-84ff-134595bd5ebe">
<hr><img width="1126" alt="image" src="https://github.com/user-attachments/assets/a1477c58-abbe-4f26-8e47-f8122f3ad102">
<hr><img width="1124" alt="image" src="https://github.com/user-attachments/assets/df275506-6791-4398-9322-d91568dc692f">
<hr><img width="1135" alt="image" src="https://github.com/user-attachments/assets/28b29868-fb3a-433e-b2bb-c7b1c1c7d1ec">
<hr><img width="1099" alt="image" src="https://github.com/user-attachments/assets/2c8a35b3-6eef-4fe0-9e32-4405ab945f43">
<hr><img width="1027" alt="image" src="https://github.com/user-attachments/assets/bbe8b59c-0770-46c8-b131-9b47b59a1f30">

###  Overriding Config

Dans Spring, overriding configuration fait référence à la capacité de remplacer ou de modifier les propriétés de configuration existantes dans différentes couches ou environnements. Cela permet une gestion flexible des propriétés et configurations, notamment pour les environnements de développement, test, et production.

Voici une vue d'ensemble des façons dont vous pouvez effectuer de l'overriding de configuration dans une application Spring :

1. Ordre de priorité des propriétés
Spring charge les propriétés dans l'ordre suivant (de la plus basse à la plus haute priorité). Cela signifie que si une propriété est définie à plusieurs endroits, la valeur ayant la priorité la plus élevée est utilisée.

Ordre de priorité :
application.properties/application.yml (dans le chemin src/main/resources).
Variables d’environnement (env variables définies sur le système d’exploitation ou le conteneur).
Paramètres de ligne de commande (via -D ou --).
Propriétés définies dans un fichier de configuration externe (chargées explicitement).
Propriétés définies dans le code avec l'annotation @PropertySource.
2. Utilisation des profils (@Profile)
Vous pouvez définir différentes configurations selon les profils actifs grâce à l'annotation @Profile et aux fichiers de propriétés spécifiques au profil.

Exemple :
Fichiers de configuration (application-dev.yml et application-prod.yml) :
application-dev.yml :

yaml
Copier le code
app:
  name: "MonApplication (Développement)"
  url: "http://localhost:8080"
application-prod.yml :

yaml
Copier le code
app:
  name: "MonApplication (Production)"
  url: "https://app.production.com"
Activation du profil dans application.properties :
properties
Copier le code
spring.profiles.active=dev
3. Propriétés de ligne de commande
Vous pouvez remplacer n'importe quelle propriété avec des paramètres de ligne de commande lors de l'exécution de l'application.

Exemple :
bash
Copier le code
java -jar app.jar --spring.profiles.active=prod --app.url=https://override-url.com
4. Annotation @PropertySource
Vous pouvez charger et remplacer des propriétés supplémentaires depuis des fichiers personnalisés.

Exemple :
java
Copier le code
@Configuration
@PropertySource("classpath:custom-config.properties")
public class CustomConfig {
    @Value("${custom.property}")
    private String customProperty;
}
custom-config.properties :

properties
Copier le code
custom.property=Valeur personnalisée
5. Configuration avec Java (@Bean)
Vous pouvez configurer ou remplacer des beans dans Java directement.

Exemple :
java
Copier le code
@Configuration
public class AppConfig {
    @Bean
    @Profile("dev")
    public DataSource dataSourceDev() {
        return new DataSource("jdbc:mysql://localhost/dev", "dev", "password");
    }

    @Bean
    @Profile("prod")
    public DataSource dataSourceProd() {
        return new DataSource("jdbc:mysql://prod-db/prod", "prod", "password");
    }
}
6. Spring Cloud Config (External Configuration)
Si vous utilisez Spring Cloud Config, vous pouvez externaliser vos configurations dans un serveur centralisé et les remplacer dynamiquement.

7. Tests et configuration spécifique
Pour les tests, vous pouvez utiliser des annotations comme @TestPropertySource ou @DynamicPropertySource pour surcharger des propriétés spécifiques aux tests.

Exemple avec @TestPropertySource :
java
Copier le code
@RunWith(SpringRunner.class)
@SpringBootTest
@TestPropertySource(locations = "classpath:test.properties")
public class MyTest {
    @Value("${test.property}")
    private String testProperty;
}
Conclusion
Spring offre plusieurs façons de surcharger la configuration, qu'il s'agisse d'utiliser des profils, des propriétés externes, des arguments de ligne de commande, ou encore des fichiers distants avec Spring Cloud Config. Cela rend l'application facilement adaptable à différents environnements et cas d'utilisation.

<hr><img width="800" alt="image" src="https://github.com/user-attachments/assets/c4c0c5b5-16df-4003-b847-2ba97fb90cec">
<hr><img width="1136" alt="image" src="https://github.com/user-attachments/assets/947c13aa-ac39-4fbc-ba7f-4da907361986">
<hr><img width="1121" alt="image" src="https://github.com/user-attachments/assets/c733a22a-8cde-457d-b2ff-c1aea120d427">
<hr><img width="1121" alt="image" src="https://github.com/user-attachments/assets/18953477-7de6-486b-9a6d-0f0d35010915">
<hr><img width="794" alt="image" src="https://github.com/user-attachments/assets/ef2d8793-9c5c-461e-87b9-a2bf0b568921">
<hr><img width="741" alt="image" src="https://github.com/user-attachments/assets/e84f080c-3a58-485b-8139-84481fdb5b57">
<hr><img width="1117" alt="image" src="https://github.com/user-attachments/assets/b50bac0b-cfc1-41a0-ac56-6ef2946be481">
<hr><img width="751" alt="image" src="https://github.com/user-attachments/assets/35c5493c-8760-4130-b2c1-dbbe3eaebcd3">
<hr><img width="1122" alt="image" src="https://github.com/user-attachments/assets/8ee4f789-ae37-405f-89d2-4ce11c4857ca">
<hr><img width="1127" alt="image" src="https://github.com/user-attachments/assets/3ffa5f34-3094-4d59-b8a8-cb1568854b92">
<hr><img width="722" alt="image" src="https://github.com/user-attachments/assets/ec5bc61a-71f6-4454-bfc4-2ca5582462f4">
<hr><img width="1132" alt="image" src="https://github.com/user-attachments/assets/cc722ded-6e05-47a4-b7af-ce5c068b869e">
<hr><img width="1074" alt="image" src="https://github.com/user-attachments/assets/0f855701-e0cf-4957-a530-7b54bb2abe3d">
<hr><img width="746" alt="image" src="https://github.com/user-attachments/assets/9e59f8ea-1d9f-48cb-8c5b-a4d30aff8209">
<hr><img width="798" alt="image" src="https://github.com/user-attachments/assets/e94407c8-3ddc-4307-85c7-11f9ebec3ac1">
<hr><img width="790" alt="image" src="https://github.com/user-attachments/assets/96c71540-83d7-4a1e-9364-fbf21628870b">
<hr><img width="745" alt="image" src="https://github.com/user-attachments/assets/d5be5b83-720e-4171-8b4e-0209e9537a92">

