Étapes pour configurer Redis comme cache :
1. Ajouter la dépendance Redis
Dans le fichier pom.xml :

xml
Copier le code
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
2. Configurer Redis dans application.properties
Définissez les informations de connexion :

properties
Copier le code
spring.redis.host=localhost
spring.redis.port=6379
spring.cache.type=redis
3. Activer la gestion du cache avec Redis
Ajoutez l’annotation @EnableCaching dans la classe principale ou de configuration de votre application :

java
Copier le code
import org.springframework.cache.annotation.EnableCaching;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
@EnableCaching
public class RedisCacheApplication {
    public static void main(String[] args) {
        SpringApplication.run(RedisCacheApplication.class, args);
    }
}
4. Configurer le cache Redis
Créez une classe de configuration pour Redis si vous souhaitez une personnalisation supplémentaire :

java
Copier le code
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.GenericJackson2JsonRedisSerializer;
import org.springframework.data.redis.serializer.StringRedisSerializer;

@Configuration
public class RedisConfig {

    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory connectionFactory) {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(connectionFactory);
        template.setKeySerializer(new StringRedisSerializer());
        template.setValueSerializer(new GenericJackson2JsonRedisSerializer());
        return template;
    }
}
5. Utiliser le cache dans votre application
Ajoutez les annotations @Cacheable, @CachePut, ou @CacheEvict sur les méthodes où vous souhaitez utiliser Redis comme cache.

Exemple :

java
Copier le code
import org.springframework.cache.annotation.Cacheable;
import org.springframework.stereotype.Service;

@Service
public class ProductService {

    @Cacheable(value = "products", key = "#id")
    public String getProductById(String id) {
        simulateSlowService();
        return "Product with ID: " + id;
    }

    private void simulateSlowService() {
        try {
            Thread.sleep(2000); // Simule un traitement long
        } catch (InterruptedException e) {
            throw new IllegalStateException(e);
        }
    }
}
6. Tester le cache
Créez un contrôleur pour tester le comportement du cache :

java
Copier le code
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class ProductController {

    @Autowired
    private ProductService productService;

    @GetMapping("/products/{id}")
    public String getProduct(@PathVariable String id) {
        return productService.getProductById(id);
    }
}
Exemple de comportement attendu
Lancez l'application et accédez à http://localhost:8080/products/1.
La première requête prendra ~2 secondes (simulation de traitement lent).
Effectuez la même requête à nouveau : le résultat sera instantané, car il provient du cache Redis.
Points à noter :
Expiration du cache : Vous pouvez définir une durée d'expiration via la configuration Redis ou des annotations spécifiques si nécessaire.
Commandes Redis : Assurez-vous que Redis fonctionne correctement via la commande redis-cli.
