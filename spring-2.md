# Componenet scanning

<img width="805" alt="image" src="https://github.com/user-attachments/assets/d80d5d8a-1f24-4bf9-a7b1-f059f1a9ede5">
<img width="1013" alt="image" src="https://github.com/user-attachments/assets/0f30db49-76f2-46a3-b66d-10c25420f58a">
<img width="1122" alt="image" src="https://github.com/user-attachments/assets/8beaffa8-ffc0-49cc-bca4-d8949ef014a3">
<img width="705" alt="image" src="https://github.com/user-attachments/assets/6db47f56-6419-4597-92d4-e0cc66607aff">
<img width="1104" alt="image" src="https://github.com/user-attachments/assets/10bbd7c2-4384-4093-8b22-5477d9cd7108">
<img width="1096" alt="image" src="https://github.com/user-attachments/assets/f3e5f385-3db5-475c-b1f7-6ed64f24e483">
<img width="1154" alt="image" src="https://github.com/user-attachments/assets/6b7ab667-e9d7-4668-a210-97f81d945d46">

###  Best practices
<img width="791" alt="image" src="https://github.com/user-attachments/assets/f2bd7f42-0aa3-4aaf-a55c-8d693258cfec">
<img width="1152" alt="image" src="https://github.com/user-attachments/assets/d14eb5db-fef5-4824-975e-0025a00a270b">
<img width="745" alt="image" src="https://github.com/user-attachments/assets/f632b59f-271b-43e5-8773-371fa934ec65">

###  #PostConstruct @PreDestroy
<img width="1097" alt="image" src="https://github.com/user-attachments/assets/5ac68825-c655-42b6-bf40-24578de997d2">
Les annotations @PostConstruct et @PreDestroy sont utilisées en Java pour définir des méthodes de cycle de vie dans un bean Spring. Elles permettent d'exécuter des tâches spécifiques avant ou après l'utilisation d'un bean.

1. @PostConstruct
Description
@PostConstruct est utilisée pour annoter une méthode qui sera exécutée juste après l'initialisation d'un bean (après que les propriétés aient été injectées).
Utile pour effectuer des opérations de configuration ou d'initialisation.
Exemple
```java
import jakarta.annotation.PostConstruct;
import org.springframework.stereotype.Component;

@Component
public class MyBean {
    @PostConstruct
    public void init() {
        System.out.println("Bean MyBean initialisé !");
        // Code d'initialisation
    }
}
```
2. @PreDestroy
Description
@PreDestroy est utilisée pour annoter une méthode qui sera exécutée juste avant la destruction d'un bean (par exemple, à la fermeture de l'application Spring).
Utile pour libérer des ressources ou effectuer des opérations de nettoyage.
Exemple
```java
import jakarta.annotation.PreDestroy;
import org.springframework.stereotype.Component;

@Component
public class MyBean {
    @PreDestroy
    public void destroy() {
        System.out.println("Bean MyBean détruit !");
        // Code de nettoyage
    }
}
```
3. Cycle de vie d’un bean avec @PostConstruct et @PreDestroy
Voici un cycle typique :

Le conteneur Spring crée une instance du bean.
Les dépendances du bean sont injectées.
Méthode annotée avec @PostConstruct est appelée.
Le bean est utilisé dans l'application.
À la fermeture du conteneur Spring :
Méthode annotée avec @PreDestroy est appelée.
Le bean est détruit.
4. Exemple complet
```java
import jakarta.annotation.PostConstruct;
import jakarta.annotation.PreDestroy;
import org.springframework.stereotype.Component;

@Component
public class ExampleBean {
    private String message;

    public ExampleBean() {
        System.out.println("Constructeur de ExampleBean appelé !");
    }

    @PostConstruct
    public void init() {
        System.out.println("Initialisation du bean (PostConstruct)");
        message = "Bonjour, je suis prêt à l'emploi.";
    }

    public void doSomething() {
        System.out.println(message);
    }

    @PreDestroy
    public void cleanup() {
        System.out.println("Nettoyage du bean (PreDestroy)");
    }
}
```
Utilisation
```java
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;

@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @Bean
    public CommandLineRunner runner(ExampleBean exampleBean) {
        return args -> {
            exampleBean.doSomething();
        };
    }
}
```
Output (au démarrage et à l'arrêt) :

```bash
Constructeur de ExampleBean appelé !
Initialisation du bean (PostConstruct)
Bonjour, je suis prêt à l'emploi.
Nettoyage du bean (PreDestroy)
```
5. Notes importantes
Ces annotations sont définies dans le package jakarta.annotation.
Fonctionnent uniquement pour les beans gérés par Spring.
Pour des cas plus complexes, envisagez d'utiliser InitializingBean ou DisposableBean (interfaces fournies par Spring).
@PostConstruct et @PreDestroy ne sont pas appelées si le bean est un prototype.
6. Différences avec InitializingBean et DisposableBean
Annotation/Interface	Description	Méthode associée
@PostConstruct	Méthode appelée après initialisation.	Méthode personnalisée
@PreDestroy	Méthode appelée avant destruction.	Méthode personnalisée
InitializingBean	Interface pour méthode d'initialisation.	afterPropertiesSet()
DisposableBean	Interface pour méthode de nettoyage.	destroy()
7. Cas d'utilisation courants
@PostConstruct
Initialisation d'une connexion à une base de données ou à une API.
Chargement de données initiales.
@PreDestroy
Fermeture de connexions ou de sockets.
Sauvegarde de données avant arrêt.

<img width="1121" alt="image" src="https://github.com/user-attachments/assets/ecd36844-ff95-433f-9242-a0c04b2c4de4">
<img width="1133" alt="image" src="https://github.com/user-attachments/assets/4265f409-5925-4316-a4b1-d2b5dab33fa8">
<img width="1099" alt="image" src="https://github.com/user-attachments/assets/159e006d-e424-4302-bcd9-e7e93b762719">
<img width="1132" alt="image" src="https://github.com/user-attachments/assets/23b2486c-1b10-4c9d-b4c4-b171563bcb5e">




###  Stereotypes, Meta annotations

Stereotypes et Meta Annotations dans Spring
###  1. Stereotypes
Les stéréotypes en Spring sont des annotations qui indiquent que la classe annotée est un bean Spring spécifique. Ces annotations permettent au conteneur Spring de détecter automatiquement les beans et de les enregistrer dans le contexte de l'application.

1.1 Les principales annotations de stéréotype
@Component
Utilisé pour marquer une classe générique comme bean Spring.

```java
@Component
public class MyService {
    public void serve() {
        System.out.println("Service exécuté !");
    }
}
```
@Service
Spécifique aux classes qui contiennent la logique métier.

```java
@Service
public class ProductService {
    public List<String> getProducts() {
        return List.of("Product 1", "Product 2");
    }
}
```
@Repository
Spécifique aux classes qui interagissent avec la couche de persistance (DAO). Cette annotation permet aussi une gestion des exceptions liées aux bases de données.

```java
@Repository
public class ProductRepository {
    // Méthodes d'accès aux données
}
```
@Controller
Indique que la classe est un contrôleur Spring MVC.

```java
@Controller
public class MyController {
    @GetMapping("/hello")
    public String hello() {
        return "Hello World!";
    }
}
```
@RestController
Une combinaison de @Controller et @ResponseBody, pour les API REST.

```java
@RestController
public class MyRestController {
    @GetMapping("/api/data")
    public List<String> getData() {
        return List.of("Data1", "Data2");
    }
}
```
###  2. Meta Annotations
Une meta-annotation est une annotation utilisée pour annoter une autre annotation. Spring permet la création d'annotations personnalisées en combinant des annotations existantes.

2.1 Les meta-annotations Spring courantes
@Component
Peut être utilisée pour créer des annotations personnalisées.
2.2 Exemple : Annotation personnalisée avec @Component
Vous pouvez créer une annotation qui agit comme un alias pour @Component.

```java
import org.springframework.stereotype.Component;
import java.lang.annotation.*;

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface MyCustomComponent {
    String value() default "";
}
```
Utilisation de l'annotation :

```java
@MyCustomComponent
public class CustomService {
    public void process() {
        System.out.println("Custom Service exécuté !");
    }
}
```
###  3. Exemple pratique : Stéréotypes avec Meta-Annotations
3.1 Annotation personnalisée pour les services
```java
import org.springframework.stereotype.Service;
import java.lang.annotation.*;

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Service
public @interface CustomService {
}
```
3.2 Application
```java
@CustomService
public class MyCustomService {
    public void execute() {
        System.out.println("Service exécuté via CustomService !");
    }
}
```
3.3 Chargement du bean
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;

@Component
public class AppRunner implements CommandLineRunner {

    @Autowired
    private MyCustomService myCustomService;

    @Override
    public void run(String... args) throws Exception {
        myCustomService.execute();
    }
}
```
###   4. Les meta-annotations dans Spring
Spring fournit plusieurs meta-annotations pour personnaliser les annotations et leur comportement.

Meta-Annotation	Description
@Target	Indique les types d'éléments (classe, méthode, etc.) sur lesquels l'annotation peut être appliquée.
@Retention	Indique si l'annotation est disponible à l'exécution, au moment de la compilation, etc.
@Documented	Ajoute l'annotation personnalisée à la documentation JavaDoc.
@Inherited	Indique que l'annotation peut être héritée par des sous-classes.
@Repeatable	Permet d'utiliser plusieurs fois la même annotation sur un élément.
###  5. Exemple complet de Meta-Annotation
5.1 Création d'une annotation personnalisée
```java
import org.springframework.stereotype.Component;
import java.lang.annotation.*;

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface FeatureFlag {
    String value();
}
```
5.2 Application de l'annotation
```java
@FeatureFlag("feature-new-dashboard")
public class NewDashboardService {
    public void loadDashboard() {
        System.out.println("Chargement du nouveau tableau de bord !");
    }
}
```
###  6. Différences principales
Concept	Description
Stereotypes	Annotations préconfigurées par Spring pour identifier des composants spécifiques dans l'application.
Meta-Annotations	Annotations utilisées pour définir ou personnaliser d'autres annotations.
7. Points clés à retenir
Les stéréotypes simplifient la gestion des beans dans Spring.
Les meta-annotations permettent de personnaliser et de combiner des comportements.
Créer vos propres annotations peut améliorer la lisibilité et standardiser votre code















