Spring Actuator : Surveillance et Monitoring des Applications Spring Boot
Spring Actuator est un module de Spring Boot qui fournit des fonctionnalités prêtes à l'emploi pour surveiller, diagnostiquer et gérer les applications. Il expose des endpoints REST pour surveiller l'état de santé, les métriques, les beans, l'environnement, etc.

1. Ajout des dépendances
Pour utiliser Spring Actuator, ajoutez cette dépendance dans le fichier pom.xml :

xml
Copier le code
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
2. Configuration de base
Activer les endpoints dans application.properties :

properties
Copier le code
# Activer tous les endpoints Actuator
management.endpoints.web.exposure.include=*
# Configurer le port pour les endpoints Actuator (optionnel)
management.server.port=8081
3. Endpoints disponibles
Spring Actuator expose plusieurs endpoints par défaut. Voici quelques-uns des plus importants :

Endpoint	Description
/actuator/health	Vérifie l'état de santé de l'application.
/actuator/metrics	Fournit des métriques de performance et d'usage.
/actuator/env	Montre les propriétés de l'environnement.
/actuator/beans	Liste tous les beans de Spring.
/actuator/loggers	Permet de gérer les niveaux de log.
/actuator/info	Fournit des informations personnalisées.
/actuator/threaddump	Affiche un dump des threads actifs.
4. Exemple : Endpoint Health
L'endpoint /actuator/health montre l'état de santé de l'application. Par défaut :

json
Copier le code
{
  "status": "UP"
}
Vous pouvez personnaliser les vérifications de santé en ajoutant des HealthIndicators :

java
Copier le code
import org.springframework.boot.actuate.health.Health;
import org.springframework.boot.actuate.health.HealthIndicator;
import org.springframework.stereotype.Component;

@Component
public class CustomHealthIndicator implements HealthIndicator {

    @Override
    public Health health() {
        // Exemple d'état personnalisé
        boolean serviceRunning = checkServiceStatus();
        if (serviceRunning) {
            return Health.up().withDetail("ServiceStatus", "Running").build();
        }
        return Health.down().withDetail("ServiceStatus", "Down").build();
    }

    private boolean checkServiceStatus() {
        // Logique pour vérifier le statut du service
        return true; // Exemple simple
    }
}
5. Métriques (Metrics)
L'endpoint /actuator/metrics fournit des statistiques sur l'application. Par exemple, pour obtenir les métriques de la mémoire JVM :

Accédez à /actuator/metrics/jvm.memory.used.
Exemple de réponse :
json
Copier le code
{
  "name": "jvm.memory.used",
  "description": "The amount of used memory",
  "baseUnit": "bytes",
  "measurements": [
    { "statistic": "VALUE", "value": 1024000 }
  ],
  "availableTags": [
    { "tag": "area", "values": ["heap", "nonheap"] }
  ]
}
6. Personnalisation de l'Endpoint Info
Ajoutez des informations personnalisées dans application.properties :

properties
Copier le code
info.app.name=My Spring Application
info.app.version=1.0.0
info.app.description=Application de démonstration Actuator
L'endpoint /actuator/info retourne :

json
Copier le code
{
  "app": {
    "name": "My Spring Application",
    "version": "1.0.0",
    "description": "Application de démonstration Actuator"
  }
}
7. Sécurisation des Endpoints
Par défaut, Spring Actuator sécurise les endpoints sensibles. Configurez Spring Security pour protéger ces endpoints.

Extrait de configuration :
java
Copier le code
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.web.SecurityFilterChain;

@Configuration
public class SecurityConfig {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http.authorizeHttpRequests(auth -> auth
                .requestMatchers("/actuator/health", "/actuator/info").permitAll()
                .requestMatchers("/actuator/**").hasRole("ADMIN")
                .anyRequest().authenticated()
        )
        .httpBasic(); // Authentification basique pour les endpoints sécurisés
        return http.build();
    }
}
8. Actuator et Prometheus
Actuator peut être intégré avec Prometheus pour la surveillance : Ajoutez la dépendance suivante :

xml
Copier le code
<dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-registry-prometheus</artifactId>
</dependency>
Configurez application.properties :

properties
Copier le code
management.endpoints.web.exposure.include=*
management.metrics.export.prometheus.enabled=true
Accédez à /actuator/prometheus pour voir les métriques compatibles avec Prometheus.

9. Tests avec Actuator
Exemple de test pour l'endpoint Health :
java
Copier le code
import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.beans.factory.annotation.Autowired;

import static org.assertj.core.api.Assertions.assertThat;

@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
class ActuatorTests {

    @Autowired
    private TestRestTemplate restTemplate;

    @Test
    void healthEndpointShouldReturnUp() {
        String response = this.restTemplate.getForObject("/actuator/health", String.class);
        assertThat(response).contains("\"status\":\"UP\"");
    }
}
10. Conclusion
Spring Actuator est un outil puissant pour surveiller, diagnostiquer et optimiser les applications Spring Boot. Il est extensible et peut être personnalisé pour répondre à vos besoins spécifiques de monitoring
###  Actuators
<hr><img width="778" alt="image" src="https://github.com/user-attachments/assets/90180348-633c-4ae9-8a94-4f5df10f75d5">
<hr><img width="1067" alt="image" src="https://github.com/user-attachments/assets/098c6262-f4b2-4504-87a0-3d8bdff3b18e">
<hr><img width="1159" alt="image" src="https://github.com/user-attachments/assets/1bffdcb8-7890-4aeb-b5af-e694a4e71b86">
<hr><img width="663" alt="image" src="https://github.com/user-attachments/assets/a4418aee-b979-40cc-8991-5c7b5e92729e">
<hr><img width="1138" alt="image" src="https://github.com/user-attachments/assets/f756a4c3-0239-4560-9339-0a5caab17384">
<hr><img width="906" alt="image" src="https://github.com/user-attachments/assets/b0edc781-6d67-42b3-a757-1cdcf8053a1a">
<hr><img width="1118" alt="image" src="https://github.com/user-attachments/assets/02de7da5-0afa-471c-90ca-ed31baf717fe">
<hr><img width="782" alt="image" src="https://github.com/user-attachments/assets/ea1ee819-0c0e-4ea8-84d2-c2d5c5fb740f">
<hr><img width="803" alt="image" src="https://github.com/user-attachments/assets/1df16902-8879-447c-a3c0-94c1f3e88dfd">
<hr><img width="1120" alt="image" src="https://github.com/user-attachments/assets/b4b4d9df-5a42-4d47-86a1-334497a6f465">
<hr><img width="1054" alt="image" src="https://github.com/user-attachments/assets/e7787a14-d2e4-4352-a282-6fd9e7386f62">
<hr><img width="1108" alt="image" src="https://github.com/user-attachments/assets/608f6d56-680a-40e7-b1d3-6e6188adcb56">
<hr><img width="1132" alt="image" src="https://github.com/user-attachments/assets/a4ae97b3-8260-46f7-a9c2-df363e0e1322">
<hr><img width="789" alt="image" src="https://github.com/user-attachments/assets/c1901358-1628-413f-b093-e4a1ee68a309">
<hr>
<hr>

###  Metrics
<hr><img width="791" alt="image" src="https://github.com/user-attachments/assets/e7dd4afa-b8c5-417d-8682-f7b838a116ac">
<hr><img width="714" alt="image" src="https://github.com/user-attachments/assets/b4e974c7-d300-47b2-a004-937585431eb5">
<hr><img width="1107" alt="image" src="https://github.com/user-attachments/assets/2ca6e579-38aa-476a-900b-e9bfb3d4880e">
<hr><img width="699" alt="image" src="https://github.com/user-attachments/assets/d1a0841a-19ad-4d46-a443-86d0b9361f96">
<hr><img width="1084" alt="image" src="https://github.com/user-attachments/assets/5b84f174-ccc8-45bf-a11e-307d6c7c0824">
<hr><img width="1135" alt="image" src="https://github.com/user-attachments/assets/85c209c2-414f-4599-9e86-da18ae80257d">
<hr><img width="841" alt="image" src="https://github.com/user-attachments/assets/67cca29a-0fd5-4ee7-8ba9-d543c8086d28">
<hr><img width="1163" alt="image" src="https://github.com/user-attachments/assets/5e516bfd-24b6-4561-a75e-834b81101156">

###  Health Indicator
<hr><img width="1161" alt="image" src="https://github.com/user-attachments/assets/fa9d04d6-628d-49b6-8b79-deffdcb8a641">
<hr><img width="1127" alt="image" src="https://github.com/user-attachments/assets/232b4a2e-6f4c-4dcd-bc7a-bef2fd8035ba">
<hr><img width="1109" alt="image" src="https://github.com/user-attachments/assets/e19ce19b-e304-476c-a6d2-cd6562008fa6">
<hr><img width="782" alt="image" src="https://github.com/user-attachments/assets/70ce9031-86f7-4c8a-8650-ad9353dba396">
<hr><img width="783" alt="image" src="https://github.com/user-attachments/assets/9f20f724-0a98-42b2-9680-67d5c35a9c38">
<hr><img width="1130" alt="image" src="https://github.com/user-attachments/assets/c17124fa-b173-4f05-82e6-b5efaaf7c449">
<hr><img width="1019" alt="image" src="https://github.com/user-attachments/assets/66b32066-c870-42e6-98ee-547c2da087d5">
<hr><img width="1152" alt="image" src="https://github.com/user-attachments/assets/79c12518-c8f5-4803-8c1f-bf569b846960">
<hr><img width="1063" alt="image" src="https://github.com/user-attachments/assets/4e919b4b-194f-4376-8267-e5721f346906">
<hr><img width="1063" alt="image" src="https://github.com/user-attachments/assets/79c1f6b8-2de7-4479-8655-eab55b649571">
<hr><img width="801" alt="image" src="https://github.com/user-attachments/assets/53548e2f-0534-4fef-bbe1-67de8834ffd4">
<hr><img width="602" alt="image" src="https://github.com/user-attachments/assets/287d75bf-7d3f-450f-88dc-cde3dea5fc99">
<hr><img width="793" alt="image" src="https://github.com/user-attachments/assets/8824dcb2-bc3e-4827-b139-19402e539bea">
<hr><img width="793" alt="image" src="https://github.com/user-attachments/assets/383f1595-4afd-49f1-bdd2-42b9c02fc51e">
