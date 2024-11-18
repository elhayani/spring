# ApplicationContext

En Spring, le ApplicationContext est l'interface principale du conteneur IoC (Inversion of Control) qui gère le cycle de vie des beans et la configuration des services au sein de l'application. L'ApplicationContext est responsable de la création, l'initialisation, et l'injection des beans, en fonction de la configuration fournie (annotations, XML, etc.).

Rôles de l'ApplicationContext :
<li>Gestion des beans : L'applicationContext crée et gère l'ensemble des objets (beans) définis dans votre application Spring.</li>
<li>Injection de dépendances : Il permet l'injection automatique des dépendances (par exemple via les annotations @Autowired).</li>
<li>Gestion des événements : Il peut publier des événements et permettre l'écoute de ces événements dans l'application.</li>
<li>Internationalisation : Il offre des mécanismes pour gérer la localisation et l'internationalisation (i18n).</li>
<li>Gestion de la configuration : Permet de charger des fichiers de configuration et d'y accéder (par exemple, les fichiers application.properties).</li>
Types d'ApplicationContext
Spring propose plusieurs implémentations de ApplicationContext en fonction du type d'application que vous souhaitez développer.

<li>ClassPathXmlApplicationContext : Utilisé pour charger un contexte à partir de fichiers de configuration XML.</li>

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
```
Classe principale pour initialiser l'ApplicationContext
```java
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

# PropertySource

L'annotation @PropertySource dans Spring est utilisée pour charger des fichiers de propriétés (.properties ou .yml) dans l'application. Ces propriétés peuvent ensuite être injectées dans des beans à l'aide de l'annotation @Value.

Syntaxe de base
```java
@Configuration
@PropertySource("classpath:application.properties")
public class AppConfig {
    // Votre configuration ici
}
```
Exemple complet avec explication
Créer un fichier de propriétés Dans le dossier src/main/resources, ajoutez un fichier nommé custom.properties :

```properties
app.name=MyApplication
app.version=1.0.0
```
Configurer @PropertySource Utilisez l'annotation pour charger ce fichier de propriétés :

```java
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;

@Configuration
@PropertySource("classpath:custom.properties")
public class AppConfig {
    @Value("${app.name}")
    private String appName;

    @Value("${app.version}")
    private String appVersion;

    public void printProperties() {
        System.out.println("App Name: " + appName);
        System.out.println("App Version: " + appVersion);
    }
}
```
Utiliser la configuration Vous pouvez accéder aux propriétés chargées à partir d'un composant Spring :

```java
import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;

@Component
public class AppRunner implements CommandLineRunner {
    private final AppConfig appConfig;

    public AppRunner(AppConfig appConfig) {
        this.appConfig = appConfig;
    }

    @Override
    public void run(String... args) throws Exception {
        appConfig.printProperties();
    }
}
```
Lancer l'application Lorsque vous exécutez votre application Spring Boot, vous verrez la sortie suivante dans la console :

```yaml
App Name: MyApplication
App Version: 1.0.0
```

Points importants :
Support multi-fichier
Vous pouvez charger plusieurs fichiers en utilisant plusieurs @PropertySource :

```java
@PropertySource("classpath:file1.properties")
@PropertySource("classpath:file2.properties")
```
Ou utiliser un tableau :

```java
@PropertySource({
    "classpath:file1.properties",
    "classpath:file2.properties"
})
```
Gestion des fichiers inexistants
Si le fichier de propriétés est manquant, Spring lève une exception. Pour éviter cela, utilisez l'attribut ignoreResourceNotFound :

```java
@PropertySource(value = "classpath:optional.properties", ignoreResourceNotFound = true)
```
Avec @PropertySource et YAML @PropertySource ne supporte pas directement les fichiers YAML. Pour utiliser YAML, Spring Boot charge automatiquement les fichiers comme application.yml.

Alternatives modernes
Dans les applications Spring Boot, les propriétés sont généralement définies dans application.properties ou application.yml, qui sont automatiquement chargées. Vous n'avez pas besoin de @PropertySource dans ces cas-là.
# Environnement
L'injection de l'objet Environment dans une application Spring permet d'accéder aux propriétés définies dans les fichiers de configuration (comme application.properties, application.yml, ou d'autres fichiers chargés via @PropertySource).

Voici comment procéder :

1. Injection avec Environment
L'interface Environment permet d'accéder aux propriétés grâce à des méthodes comme getProperty.

Exemple :
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.env.Environment;

@Configuration
public class AppConfig {

    @Autowired
    private Environment environment;

    public void printProperties() {
        String appName = environment.getProperty("app.name");
        String appVersion = environment.getProperty("app.version", "1.0.0"); // Valeur par défaut
        System.out.println("App Name: " + appName);
        System.out.println("App Version: " + appVersion);
    }
}
```
Usage :
Vous pouvez appeler la méthode printProperties depuis un autre composant pour afficher les propriétés.

2. Configuration des propriétés
Ajoutez des propriétés dans application.properties ou application.yml :

```properties
app.name=MyApplication
app.version=2.5.1
```
3. Exemple avec un composant Spring Boot
Si vous utilisez Spring Boot, voici comment accéder à l'objet Environment dans un composant @Component ou une classe @Service :

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.core.env.Environment;
import org.springframework.stereotype.Component;

@Component
public class AppRunner {

    @Autowired
    private Environment environment;

    public void run() {
        String databaseUrl = environment.getProperty("spring.datasource.url");
        System.out.println("Database URL: " + databaseUrl);
    }
}
```
Ajoutez une configuration de base dans application.properties :

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
```
4. Injection via constructeur
Si vous préférez l'injection par constructeur pour une meilleure testabilité, voici comment procéder :

```java
import org.springframework.core.env.Environment;
import org.springframework.stereotype.Service;

@Service
public class ConfigService {

    private final Environment environment;

    public ConfigService(Environment environment) {
        this.environment = environment;
    }

    public String getAppName() {
        return environment.getProperty("app.name");
    }
}
```
5. Points importants
NullPointerException : Si une propriété n'est pas définie, getProperty retourne null. Vous pouvez fournir une valeur par défaut comme suit :

```java
String appVersion = environment.getProperty("app.version", "1.0.0");
```
Support pour les profils : L'objet Environment vous permet aussi de gérer des profils avec getActiveProfiles() et getDefaultProfiles().

Exemple :
```java
String[] activeProfiles = environment.getActiveProfiles();
System.out.println("Active Profiles: " + Arrays.toString(activeProfiles));
```
6. Exemple avec profil actif
Ajoutez plusieurs profils dans application.properties :

```properties
spring.profiles.active=dev
```
Ajoutez un fichier application-dev.properties :

```properties
app.name=DevApplication
```
Lorsque l'application est lancée, les propriétés du profil actif (dev) seront injectées et accessibles via Environment.

# Profile

L’annotation @Profile en Spring est utilisée pour activer ou désactiver des beans en fonction du profil actif. Elle permet de gérer différentes configurations pour différents environnements (par exemple, dev, test, prod).

1. Activation d'un bean avec @Profile
Vous pouvez annoter un bean ou une configuration avec @Profile. Ce bean ne sera chargé que si le profil correspondant est actif.

Exemple simple :
```java
import org.springframework.context.annotation.Profile;
import org.springframework.stereotype.Component;

@Component
@Profile("dev")
public class DevDatabaseConfig {
    public DevDatabaseConfig() {
        System.out.println("Dev Database Configuration Loaded");
    }
}
```
2. Définir un profil actif
a. Via application.properties ou application.yml
Ajoutez le profil actif dans le fichier de configuration :

```properties
spring.profiles.active=dev
```
b. Via arguments de ligne de commande
Lors du lancement de l'application, spécifiez le profil actif :

```bash
java -jar app.jar --spring.profiles.active=prod
```
c. Via variables d'environnement
Ajoutez une variable d'environnement :

```bash
SPRING_PROFILES_ACTIVE=prod
```
3. Configuration basée sur les profils
Vous pouvez définir des beans différents pour chaque profil.

Exemple avec @Configuration :
```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;

@Configuration
public class AppConfig {

    @Bean
    @Profile("dev")
    public String devBean() {
        return "Bean for Dev Profile";
    }

    @Bean
    @Profile("prod")
    public String prodBean() {
        return "Bean for Prod Profile";
    }
}
```
Test :
Le bean devBean sera chargé si le profil dev est actif, et prodBean si le profil prod est actif.

4. Profil par défaut
Si aucun profil n'est activé, vous pouvez définir un profil par défaut avec spring.profiles.default :

```properties
spring.profiles.default=default
```
Ajoutez un bean pour le profil default :

```java
@Component
@Profile("default")
public class DefaultConfig {
    public DefaultConfig() {
        System.out.println("Default Configuration Loaded");
    }
}
```
5. Utilisation dans les tests
Les profils peuvent être activés dans un test Spring avec @ActiveProfiles :

Exemple :
```java
import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.ActiveProfiles;

@SpringBootTest
@ActiveProfiles("test")
public class MyServiceTest {

    @Test
    void testSomething() {
        // Test logic
    }
}
```
Le profil test sera utilisé uniquement dans ce test.

6. Points importants
Profils multiples : Vous pouvez activer plusieurs profils à la fois :

```properties
spring.profiles.active=dev,local
```
Condition inversée : Vous pouvez utiliser not pour exclure un profil :

```java
@Profile("!prod")
```
Priorité des profils : Si plusieurs profils sont actifs, les propriétés et configurations seront combinées. Les profils définis explicitement (comme dans spring.profiles.active) ont la priorité sur le profil par défaut.

En résumé, @Profile est une puissante fonctionnalité pour gérer des configurations différentes dans des environnements varié

# SPEL

Spring Expression Language (SpEL) est un langage d'expression puissant fourni par Spring Framework. Il permet d'évaluer des expressions dynamiques dans des fichiers de configuration, des annotations ou du code.

1. Fonctionnalités principales de SpEL
Accès aux propriétés des objets.
Appels de méthodes.
Évaluation conditionnelle.
Accès aux listes, maps, et tableaux.
Manipulation de chaînes de caractères.
Accès aux variables et aux environnements Spring.
2. Utilisation dans Spring
a. Dans les annotations
SpEL est souvent utilisé dans les annotations comme @Value, @ConditionalOnExpression, ou @Cacheable.

Exemple avec @Value :
```java
@Value("#{systemProperties['user.name']}")
private String userName;
```
Ici, SpEL récupère la propriété système user.name.

Exemple avec des propriétés conditionnelles :
```java
@Value("#{2 + 2}")
private int result; // Valeur sera 4
```
b. Dans les fichiers de configuration
SpEL peut être utilisé dans application.properties ou application.yml.

Exemple :
```properties
app.config.value=${config.value:#{10 * 2}}
```
Si config.value n'est pas défini, SpEL calcule la valeur par défaut comme 20.

c. Avec @ConditionalOnExpression
Vous pouvez conditionner le chargement d'un bean avec SpEL.

Exemple :
```java
@Bean
@ConditionalOnExpression("'${spring.profiles.active}' == 'dev'")
public MyService devService() {
    return new MyService();
}
```
Ce bean sera chargé uniquement si le profil actif est dev.

3. Syntaxe SpEL
a. Accès aux propriétés
Vous pouvez accéder aux propriétés des objets :

```java
@Value("#{person.name}")
private String personName;
```
b. Appels de méthode
SpEL permet d’appeler des méthodes d’objets :

```java
@Value("#{person.getAge()}")
```
private int age;
c. Opérations mathématiques
SpEL supporte les opérations arithmétiques :

```java
@Value("#{20 + 30}")
private int sum; // Valeur sera 50
```
d. Expressions conditionnelles
SpEL permet des évaluations conditionnelles :

```java
@Value("#{systemProperties['os.name'] == 'Windows 10' ? 'Windows User' : 'Other OS User'}")
private String osUser;
```
e. Manipulation de collections
Accéder à des éléments d'une liste ou d'un tableau :
```java
@Value("#{myList[0]}")
private String firstElement;
```
Filtrer une liste :
```java
@Value("#{myList.?[length > 3]}")
private List<String> filteredList;
```
Trouver le premier élément correspondant à une condition :
```java
@Value("#{myList.^[length > 3]}")
private String firstMatchingElement;
```
4. Utilisation avec l'API ExpressionParser
Pour des utilisations plus complexes, vous pouvez utiliser l'API ExpressionParser.

Exemple :
```java
import org.springframework.expression.ExpressionParser;
import org.springframework.expression.spel.standard.SpelExpressionParser;

public class SpELDemo {
    public static void main(String[] args) {
        ExpressionParser parser = new SpelExpressionParser();

        // Évaluer une expression simple
        String greeting = parser.parseExpression("'Hello ' + 'World!'").getValue(String.class);
        System.out.println(greeting); // Output : Hello World!

        // Accéder aux propriétés d'un objet
        Person person = new Person("John", 25);
        int age = parser.parseExpression("age").getValue(person, Integer.class);
        System.out.println("Age: " + age); // Output : 25
    }
}

class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public int getAge() {
        return age;
    }
}
```
5. Opérateurs SpEL
Type	Exemple	Description
Mathématiques	+, -, *, /, %	Opérations arithmétiques
Relationnels	==, !=, <, >, <=, >=	Comparaisons
Logiques	&&, `	
Conditionnels	?:	Ternaire (condition ? vrai : faux)
Collections	?[...], ^[...], $[...]	Filtrage, premier ou dernier élément

|Type	    |Exemple	                         |Description              |
|---------------|------------------------------------|-----------------------|
|Mathématiques	|+, -, *, /, %         |Opérations arithmétiques |
|Relationnels	|==, !=, <, >, <=, >=  |Comparaisons   |
|Logiques       |  &&,	               |     |
|Conditionnels  |  ?:	               |Ternaire (condition ? vrai : faux)  |
|Collections    |?[...], ^[...], $[...]|Filtrage, premier ou dernier élément|

7. Points importants
SpEL est puissant mais doit être utilisé avec modération pour éviter des configurations trop complexes ou difficiles à maintenir.
Sécurité : Si les expressions sont dynamiques et entrées par l'utilisateur, elles doivent être validées pour éviter des injections malveillantes.

