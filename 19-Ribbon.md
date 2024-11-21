Using Ribbon for Client-Side Load Balancing in a Spring Boot Application
Ribbon is often used in a microservices architecture to balance traffic among multiple instances of a service. Here's an example of how to integrate Ribbon in a Spring Boot project:

Step 1: Include Ribbon in the Dependencies
Add the following dependencies to your pom.xml (for Maven projects):

xml
Copier le code
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-ribbon</artifactId>
</dependency>
If you're using Gradle, include:

gradle
Copier le code
implementation 'org.springframework.cloud:spring-cloud-starter-netflix-ribbon'
Step 2: Configure Ribbon in application.yml
Suppose you have a service named my-service with two instances running on ports 8081 and 8082.

yaml
Copier le code
my-service:
  ribbon:
    listOfServers: http://localhost:8081, http://localhost:8082
    ConnectTimeout: 3000
    ReadTimeout: 5000
Step 3: Create a RestTemplate with Ribbon
Ribbon integrates seamlessly with a RestTemplate that is annotated with @LoadBalanced.

java
Copier le code
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cloud.client.loadbalancer.LoadBalanced;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.client.RestTemplate;

@Configuration
public class RibbonConfig {

    @Bean
    @LoadBalanced
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
Step 4: Use RestTemplate to Call the Service
Use the RestTemplate to make requests to the service. Ribbon will handle the load balancing between the instances automatically.

java
Copier le code
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class MyServiceClient {

    @Autowired
    private RestTemplate restTemplate;

    public String getDataFromMyService() {
        String serviceUrl = "http://my-service/api/data";
        return restTemplate.getForObject(serviceUrl, String.class);
    }
}
Here, the service URL (http://my-service) corresponds to the logical name configured in application.yml. Ribbon resolves this name to one of the configured instances (http://localhost:8081 or http://localhost:8082) using its load-balancing algorithm (default is round-robin).

Step 5: Start Multiple Service Instances
Run two instances of the my-service on ports 8081 and 8082. For example:

Start the first instance with --server.port=8081.
Start the second instance with --server.port=8082.
Testing the Application
When calling the getDataFromMyService() method, Ribbon will distribute the requests between the two instances in a round-robin manner.

For example:

Request 1 -> http://localhost:8081
Request 2 -> http://localhost:8082
Request 3 -> http://localhost:8081
And so on...
Customizing Load-Balancing Algorithm
By default, Ribbon uses a round-robin algorithm. You can customize this by implementing your own IRule.

java
Copier le code
import com.netflix.loadbalancer.AvailabilityFilteringRule;
import com.netflix.loadbalancer.IRule;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class RibbonCustomConfig {

    @Bean
    public IRule ribbonRule() {
        return new AvailabilityFilteringRule(); // Custom rule for better availability
    }
}
Replace AvailabilityFilteringRule with any other rule provided by Ribbon or your custom implementation.

Conclusion
Ribbon simplifies the process of distributing requests across multiple instances of a service. With this setup:

You’ve implemented client-side load balancing.
Your service calls are resilient and scalable.

#  Ajouter Logs et docker-compose

Pour ajouter des logs afin de voir quelle instance est appelée par Ribbon et configurer un environnement avec Docker Compose pour vos services, suivez ces étapes.

Étape 1 : Ajouter des logs pour voir quelle instance est appelée par Ribbon
Pour afficher quelle instance est appelée par Ribbon, vous pouvez activer des logs dans votre configuration application.yml pour afficher les détails du processus de résolution des instances.

1.1. Activer le logging pour Ribbon
Ajoutez la configuration suivante dans votre fichier application.yml pour activer les logs au niveau de Ribbon :

yaml
Copier le code
logging:
  level:
    com.netflix.discovery: DEBUG        # Pour les logs de découverte
    com.netflix.loadbalancer: DEBUG     # Pour les logs de load balancer (Ribbon)
    org.springframework.cloud.netflix.ribbon: DEBUG  # Pour les logs spécifiques à Ribbon
Cela permettra de voir les instances appelées et comment Ribbon répartit les requêtes entre elles.

1.2. Ajouter des logs dans le code pour afficher les détails de la requête
Vous pouvez également ajouter des logs directement dans votre service client pour afficher les détails de la requête effectuée. Par exemple :

java
Copier le code
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.web.client.RestTemplate;

@Service
public class MyServiceClient {

    private static final Logger logger = LoggerFactory.getLogger(MyServiceClient.class);

    @Autowired
    private RestTemplate restTemplate;

    public String getDataFromMyService() {
        String serviceUrl = "http://my-service/api/data";
        logger.info("Calling service at: {}", serviceUrl); // Log avant l'appel
        String response = restTemplate.getForObject(serviceUrl, String.class);
        logger.info("Response from service: {}", response); // Log après l'appel
        return response;
    }
}
Cela vous permettra de voir le service appelé et la réponse qu'il retourne dans vos logs.

Étape 2 : Configurer Docker Compose pour plusieurs instances
Ensuite, nous allons configurer Docker Compose pour exécuter plusieurs instances du service my-service sur des ports différents. Vous pouvez aussi configurer Eureka si vous utilisez le service de découverte pour en faire un environnement complet de microservices.

2.1. Fichier docker-compose.yml
Voici un exemple de fichier docker-compose.yml pour exécuter deux instances du service my-service, un service de découverte Eureka, et un autre service config-server (si vous en avez besoin) :

yaml
Copier le code
version: '3'
services:
  # Eureka Server
  eureka-server:
    image: springcloud/eureka-server
    container_name: eureka-server
    ports:
      - "8761:8761"
    environment:
      - spring.application.name=eureka-server
      - eureka.client.register-with-eureka=false
      - eureka.client.fetch-registry=false

  # Config Server (si vous avez un config-server)
  config-server:
    image: your-config-server-image
    container_name: config-server
    ports:
      - "8888:8888"
    environment:
      - spring.application.name=config-server
    depends_on:
      - eureka-server

  # My Service 1 (instance 1)
  my-service-1:
    image: your-my-service-image
    container_name: my-service-1
    environment:
      - SERVER_PORT=8081
      - EUREKA_CLIENT_SERVICEURL_DEFAULTZONE=http://eureka-server:8761/eureka
    ports:
      - "8081:8081"
    depends_on:
      - eureka-server

  # My Service 2 (instance 2)
  my-service-2:
    image: your-my-service-image
    container_name: my-service-2
    environment:
      - SERVER_PORT=8082
      - EUREKA_CLIENT_SERVICEURL_DEFAULTZONE=http://eureka-server:8761/eureka
    ports:
      - "8082:8082"
    depends_on:
      - eureka-server
Explication de la configuration :
eureka-server : C'est le serveur Eureka pour la découverte de services. Il est accessible via http://localhost:8761.
config-server : Si vous utilisez un config server pour centraliser la configuration, il est configuré ici.
my-service-1 et my-service-2 : Ce sont deux instances du service my-service, qui s'exécuteront sur les ports 8081 et 8082. Les services s'enregistrent auprès du serveur Eureka pour être découverts.
Étape 3 : Démarrer Docker Compose
Une fois que vous avez configuré votre fichier docker-compose.yml, vous pouvez démarrer les services avec la commande suivante :

bash
Copier le code
docker-compose up --build
Cela va construire les images des services (si elles ne sont pas déjà construites) et démarrer tous les conteneurs. Les services my-service-1 et my-service-2 seront accessibles respectivement sur les ports 8081 et 8082, et le serveur Eureka sera disponible sur http://localhost:8761.

Étape 4 : Tester et observer les logs
Après avoir démarré les services :

Vous pouvez consulter les logs dans la console pour voir quelle instance est appelée.
Allez dans l'interface web d'Eureka à http://localhost:8761 pour voir les instances enregistrées et surveiller les appels.
Résumé
Vous avez ajouté des logs pour suivre les instances appelées par Ribbon.
Vous avez configuré un environnement Docker Compose avec plusieurs instances de votre service et un serveur Eureka pour la découverte de services.
En démarrant les services via Docker Compose, Ribbon se charge de répartir les requêtes entre les instances disponibles de manière transparente.
