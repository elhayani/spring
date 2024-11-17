# ApplicationContext

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

# Handling multiples configuration

L'annotation @Import de Spring est utilisée pour importer des configurations supplémentaires dans le contexte d'application (ApplicationContext). Elle est souvent utilisée dans des classes annotées avec @Configuration pour inclure d'autres configurations, permettant ainsi une modularité et une organisation claire de votre code.

Syntaxe de base
```java
@Import({ConfigClass1.class, ConfigClass2.class})
```
Cette annotation inclut une ou plusieurs classes de configuration dans l'ApplicationContext. Les classes spécifiées peuvent contenir des définitions de beans, des configurations, ou même des composants scannés.

Scénarios d’utilisation
1. Importer des classes de configuration
Lorsque vous avez plusieurs classes de configuration distinctes, vous pouvez utiliser @Import pour les combiner.

Exemple :
Classe de configuration principale (MainConfig):

```java
@Configuration
@Import({DataSourceConfig.class, ServiceConfig.class})
public class MainConfig {
    // Configuration principale
}
```
Classe de configuration pour la source de données (DataSourceConfig) :

```java
@Configuration
public class DataSourceConfig {

    @Bean
    public DataSource dataSource() {
        return new HikariDataSource(); // Configuration d'une DataSource
    }
}
```
Classe de configuration pour les services (ServiceConfig) :

```java
@Configuration
public class ServiceConfig {

    @Bean
    public MyService myService() {
        return new MyServiceImpl();
    }
}
```
Dans cet exemple, MainConfig importe les configurations de DataSourceConfig et ServiceConfig.

2. Importer une classe qui n'est pas annotée avec @Configuration
Vous pouvez également importer des classes normales (non @Configuration) qui contiennent des annotations comme @Component, @Service, ou @Repository.

Exemple :
```java
@Import({CustomBean.class})
public class AppConfig {
    // Configuration principale
}
```
Classe à importer :

```java
@Component
public class CustomBean {
    public void doSomething() {
        System.out.println("Doing something!");
    }
}
```
3. Combiner avec @ComponentScan
Si vous souhaitez mélanger @ComponentScan et @Import, vous pouvez le faire pour organiser vos configurations et optimiser le chargement des beans.

Exemple :
```java
@Configuration
@ComponentScan(basePackages = "com.example.components")
@Import({AnotherConfig.class})
public class AppConfig {
}
```
Import dynamique : @Import avec un ImportSelector
Pour des scénarios plus dynamiques, vous pouvez utiliser un ImportSelector. Cela permet de conditionner l'importation des configurations en fonction de la logique métier.

Exemple :
Classe de configuration principale :

```java
@Configuration
@Import(MyImportSelector.class)
public class MainConfig {
}
```
Classe ImportSelector personnalisée :

```java
public class MyImportSelector implements ImportSelector {
    @Override
    public String[] selectImports(AnnotationMetadata importingClassMetadata) {
        // Logique dynamique
        return new String[] {"com.example.config.ExtraConfig"};
    }
}
```
Classe à importer conditionnellement :

```java
@Configuration
public class ExtraConfig {
    @Bean
    public ExtraService extraService() {
        return new ExtraService();
    }
}
```
Dans cet exemple, MyImportSelector détermine dynamiquement quelle configuration importer.

Importer via DeferredImportSelector
Si vous avez besoin de gérer des imports conditionnels ou ordonnés, vous pouvez utiliser un DeferredImportSelector.

Différence entre @Import et @ComponentScan
Aspect	@Import	@ComponentScan
Fonction	Importer des classes ou configurations spécifiques.	Scanner les classes dans un package donné.
Utilisation principale	Ajouter des classes précises à l'ApplicationContext.	Charger automatiquement des classes annotées avec @Component, @Service, etc.
Scénarios typiques	Pour une configuration modulaire ou dynamique.	Pour un scan large basé sur des packages.
Avantages
Permet une organisation modulaire des configurations Spring.
Fournit une solution flexible pour importer des classes de configuration ou des composants spécifiques.
Facilite l'ajout conditionnel de beans via des ImportSelector.
Conclusion
L'annotation @Import est un puissant outil pour structurer et organiser vos configurations Spring. Elle est particulièrement utile dans des projets complexes ou modulaires, où il est nécessaire de combiner plusieurs configurations ou d'ajouter des fonctionnalités conditionnelles.

# Beans scope

1. Singleton Scope (default)
Description : Un seul bean est créé pour l'ensemble du conteneur Spring. Tous les appels au bean renvoient la même instance.
Usage typique : Pour des objets stateless (sans état partagé entre différentes requêtes ou sessions).
Annotation : @Scope("singleton") (par défaut, inutile de spécifier explicitement).
Exemple :
```java
@Component
public class SingletonBean { }
```java
@Component
@Scope("singleton")
public class AnotherSingletonBean { }
```
2. Prototype Scope
Description : Une nouvelle instance du bean est créée chaque fois qu'il est demandé.
Usage typique : Pour des objets stateful (qui maintiennent des données spécifiques à un utilisateur ou à un processus).
Annotation : @Scope("prototype").
Exemple :
```java
@Component
@Scope("prototype")
public class PrototypeBean { }
```
Remarque :
Les beans prototypes ne sont pas gérés par le conteneur Spring après leur création. Ils nécessitent une gestion explicite (par exemple, pour la destruction).

3. Request Scope
Description : Une instance est créée pour chaque requête HTTP. Cela est utile dans des applications web.
Usage typique : Pour des objets liés à des requêtes HTTP.
Annotation : @Scope("request").
Exemple :
```java
@Component
@Scope("request")
public class RequestBean { }
```
4. Session Scope
Description : Une instance est créée pour chaque session utilisateur HTTP.
Usage typique : Pour des objets dont les données doivent persister tout au long d'une session utilisateur.
Annotation : @Scope("session").
Exemple :
```java
@Component
@Scope("session")
public class SessionBean { }
```
5. Application Scope
Description : Une seule instance de bean est partagée dans l'ensemble du contexte Servlet.
Usage typique : Pour des objets dont les données sont partagées entre toutes les sessions d'une application web.
Annotation : @Scope("application").
Exemple :
```java
@Component
@Scope("application")
public class ApplicationBean { }
```
6. WebSocket Scope (ajouté dans Spring 4.0)
Description : Une instance est créée pour chaque session WebSocket.
Usage typique : Pour des applications utilisant WebSocket.
Annotation : @Scope("websocket").
Exemple :
```java
@Component
@Scope("websocket")
public class WebSocketBean { }
```
Définir le scope programmatique
Vous pouvez définir un scope dans le fichier de configuration Java ou XML :

En Java :
```java
@Bean
@Scope("prototype")
public MyBean myBean() {
    return new MyBean();
}
```
En XML :
```xml
<bean id="myBean" class="com.example.MyBean" scope="prototype"/>
```
Résumé des scopes :
|Scope	    |Durée de vie	                    |Annotation              |
|-----------|------------------------------------|-----------------------|
|singleton	|Une instance pour tout le conteneur |	@Scope("singleton")  |
|prototype	|Une instance par requête de bean	 |@Scope("prototype")    |
|request    |  Une instance par requête HTTP	 |    @Scope("request")  |
|session    |   Une instance par session HTTP	 |    @Scope("session")  |
|application|	Une instance par contexte Servlet|	@Scope("application")|
|websocket	|Une instance par session WebSocket	 |@Scope("websocket")    |


Points importants :
Le scope par défaut en Spring est singleton.
Les scopes comme request et session nécessitent un contexte web (Spring MVC).
Les beans avec le scope prototype ne sont pas gérés pour leur destruction par Spring.


