# Config

En Spring, le ApplicationContext est l'interface principale du conteneur IoC (Inversion of Control) qui gère le cycle de vie des beans et la configuration des services au sein de l'application. L'ApplicationContext est responsable de la création, l'initialisation, et l'injection des beans, en fonction de la configuration fournie (annotations, XML, etc.).

Rôles de l'ApplicationContext :
Gestion des beans : Le ApplicationContext crée et gère l'ensemble des objets (beans) définis dans votre application Spring.
Injection de dépendances : Il permet l'injection automatique des dépendances (par exemple via les annotations @Autowired).
Gestion des événements : Il peut publier des événements et permettre l'écoute de ces événements dans l'application.
Internationalisation : Il offre des mécanismes pour gérer la localisation et l'internationalisation (i18n).
Gestion de la configuration : Permet de charger des fichiers de configuration et d'y accéder (par exemple, les fichiers application.properties).
Types d'ApplicationContext
Spring propose plusieurs implémentations de ApplicationContext en fonction du type d'application que vous souhaitez développer.

ClassPathXmlApplicationContext : Utilisé pour charger un contexte à partir de fichiers de configuration XML.

```java
ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
AnnotationConfigApplicationContext : Utilisé pour configurer un contexte à partir d'annotations Java (comme @Configuration).
```
```java
ApplicationContext context = new AnnotationConfigApplicationContext(MyConfigClass.class);
GenericWebApplicationContext : Utilisé dans les applications web (Spring WebFlux, Spring MVC).

GenericApplicationContext : La version générique utilisée pour la plupart des applications Spring modernes.

GenericWebApplicationContext et AnnotationConfigWebApplicationContext : Utilisés dans des applications basées sur le web.
```

Exemple d’utilisation avec AnnotationConfigApplicationContext :
Si vous utilisez une application configurée par annotations Java, vous pouvez créer un ApplicationContext comme suit :

Classe de configuration (@Configuration)
```java
@Configuration
@ComponentScan(basePackages = "com.example")  // Scanne le package pour les beans annotés @Component
public class AppConfig {

    @Bean
    public MyService myService() {
        return new MyServiceImpl();
    }
}
Classe principale pour initialiser l'ApplicationContext
java
Copier le code
public class App {
    public static void main(String[] args) {
        // Initialiser le contexte Spring avec une classe de configuration
        ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
        
        // Récupérer un bean à partir du contexte
        MyService service = context.getBean(MyService.class);
        
        // Utiliser le service
        service.performAction();
    }
}
```
Dans cet exemple :

Le contexte Spring est initialisé en chargeant la classe AppConfig qui contient les annotations @Configuration et @ComponentScan.
Un bean est créé automatiquement par Spring et injecté dans la classe MyService.
Exemple d'utilisation avec ApplicationContext et @Autowired :
Voici un autre exemple montrant comment l'injection de dépendances se fait automatiquement via @Autowired dans Spring.

Classe de service
```java
@Service
public class MyService {

    private final MyRepository myRepository;

    @Autowired
    public MyService(MyRepository myRepository) {
        this.myRepository = myRepository;
    }

    public void performAction() {
        // Logique métier
        System.out.println("Service action performed!");
    }
}
```
Classe de contrôleur
```java
@RestController
public class MyController {

    private final MyService myService;

    @Autowired
    public MyController(MyService myService) {
        this.myService = myService;
    }

    @GetMapping("/perform")
    public String performAction() {
        myService.performAction();
        return "Action performed!";
    }
}
```
Classe principale
```java
public class App {
    public static void main(String[] args) {
        ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
        MyController controller = context.getBean(MyController.class);
        controller.performAction();
    }
}
```
Dans cet exemple, Spring gère l'injection des dépendances de manière transparente, vous permettant de travailler avec des objets sans vous soucier de leur gestion manuelle (création, destruction, etc.).

Accès aux beans dans un ApplicationContext
Une fois que le contexte ApplicationContext est initialisé, vous pouvez obtenir des beans de différentes manières :

Par type :
```java
MyService service = context.getBean(MyService.class);
```
Par nom et type :
```java
MyService service = context.getBean("myService", MyService.class);
```
Par nom :
```java
MyService service = (MyService) context.getBean("myService");
```
Chargement de propriétés
Le ApplicationContext peut également être utilisé pour charger des fichiers de propriétés et y accéder :

```properties
# application.properties
myapp.message=Hello, World!
```
```java
// Accéder aux propriétés
ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
String message = context.getEnvironment().getProperty("myapp.message");
System.out.println(message); // Affiche "Hello, World!"
```
Conclusion
L'ApplicationContext de Spring est l'élément central de l'infrastructure de Spring, qui gère les beans, leur cycle de vie, les injections de dépendances et bien d'autres aspects de la configuration et du développement d'applications. Il existe plusieurs façons de l'initialiser et de l'utiliser, selon le type d'application que vous développez (application classique, web, ou microservices).
