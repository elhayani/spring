<hr><img width="775" alt="image" src="https://github.com/user-attachments/assets/f6d6f2fa-13e0-48a2-917a-6cd55be47214">
<hr><img width="860" alt="image" src="https://github.com/user-attachments/assets/b52d2521-09b5-45b4-acda-1b701fc55ac5">
<hr><img width="1043" alt="image" src="https://github.com/user-attachments/assets/d670e5e3-ec2a-4026-9b8d-2f731d485e32">
<hr><img width="1032" alt="image" src="https://github.com/user-attachments/assets/c4ec71b0-5ff9-48f4-a1bd-b6ddb62bce72">
<hr><img width="728" alt="image" src="https://github.com/user-attachments/assets/29dc15a8-4f69-4e28-8f20-28072f8086d5">
<hr><img width="815" alt="image" src="https://github.com/user-attachments/assets/cbd8f6c1-b25e-43ea-a3ed-6a9a72d2c24a">
<hr><img width="749" alt="image" src="https://github.com/user-attachments/assets/8381f1cc-0deb-48db-a039-f2bf9bccc4a2">
<hr><img width="724" alt="image" src="https://github.com/user-attachments/assets/b661b28a-fc5f-4a96-908c-569a2e224e1c">
<hr><img width="1140" alt="image" src="https://github.com/user-attachments/assets/eaa239a3-a86b-4a2c-a3ab-2237236d66dd">
<hr><img width="823" alt="image" src="https://github.com/user-attachments/assets/10c9023e-6d48-4ff9-8b22-4cb420e2c291">
<hr><img width="817" alt="image" src="https://github.com/user-attachments/assets/b9194fe0-74e5-4a90-b2a6-ebce596098ac">
<hr><img width="1118" alt="image" src="https://github.com/user-attachments/assets/6b1bbb82-10d4-4e11-844c-d50d84867b4b">

###  URL Authorization

<hr><img width="1110" alt="image" src="https://github.com/user-attachments/assets/78cf26a4-29d6-4300-be38-f938416fdf34">
<hr><img width="770" alt="image" src="https://github.com/user-attachments/assets/7bd173a5-99a9-4a23-8b0f-3508d693bd03">
<hr><img width="1105" alt="image" src="https://github.com/user-attachments/assets/ba7fd09a-e3fb-4724-a78e-8ca6a0ec5b01">
<hr><img width="1063" alt="image" src="https://github.com/user-attachments/assets/726e43c6-9b38-4a0b-9b5a-d924a7287a80">
<hr><img width="733" alt="image" src="https://github.com/user-attachments/assets/e2651f0b-7524-4eb3-a28e-cb866f796ddd">

###  Authentification

<hr><img width="1158" alt="image" src="https://github.com/user-attachments/assets/2b4eddb5-1b81-4a93-a52c-c1d1f7d163c6">
<hr><img width="1061" alt="image" src="https://github.com/user-attachments/assets/4d20e5dd-6c2f-4678-8e0b-bddd7ad5da14">
<hr><img width="748" alt="image" src="https://github.com/user-attachments/assets/31f25426-ab67-46e5-9314-60d14adb2f4a">
<hr><img width="1160" alt="image" src="https://github.com/user-attachments/assets/419294a8-9710-457d-b677-68fd1365ca3c">
<hr><img width="1054" alt="image" src="https://github.com/user-attachments/assets/85abdcb4-6ef6-4812-b125-c8c8cf331dcf">
<hr><img width="783" alt="image" src="https://github.com/user-attachments/assets/c983d092-d3f5-4c29-b784-4c3165fab29a">
<hr><img width="1041" alt="image" src="https://github.com/user-attachments/assets/7e8e12d9-e9fe-4f5a-858a-3f9f8a5241a8">
<hr><img width="748" alt="image" src="https://github.com/user-attachments/assets/288b3318-a278-4af7-aa3c-77c4a0cbaf4c">
<hr><img width="752" alt="image" src="https://github.com/user-attachments/assets/9acacf43-df93-4fd2-975f-9c9ee2192201">
<hr><img width="743" alt="image" src="https://github.com/user-attachments/assets/23761160-0944-4694-9a66-22f6ee69de89">
<hr><img width="771" alt="image" src="https://github.com/user-attachments/assets/a98789e5-ee33-4133-8370-85d0e870eac4">
<hr><img width="808" alt="image" src="https://github.com/user-attachments/assets/405ac527-867a-44d4-981b-00f7a228ac25">

###  Testing

<hr><img width="757" alt="image" src="https://github.com/user-attachments/assets/3eac576d-c613-47f1-a309-e6b5967aad6e">
<hr><img width="1154" alt="image" src="https://github.com/user-attachments/assets/f2da5703-2ca0-4bcb-8b6e-ce576bb0c3a5">
<hr><img width="747" alt="image" src="https://github.com/user-attachments/assets/5715fec0-876d-4b8a-a3d9-ae84d9513131">
<hr>


1. Dépendances Maven
Ajoutez les dépendances nécessaires dans votre pom.xml :

xml
Copier le code
<dependencies>
    <!-- Spring Boot Starter Security -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>

    <!-- Spring Boot Starter Web -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- Spring Security Test (pour les tests) -->
    <dependency>
        <groupId>org.springframework.security</groupId>
        <artifactId>spring-security-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
2. Configuration de base avec Spring Security 6.1
Spring Security 6.1 privilégie l’approche basée sur les classes de configuration. Voici un exemple basique d’une configuration pour protéger une application.

Exemple de configuration : SecurityConfig
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
        http
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/public/**").permitAll() // Autorise l'accès à ces URLs
                .anyRequest().authenticated()              // Toutes les autres URLs nécessitent une authentification
            )
            .formLogin(form -> form
                .loginPage("/login")                       // Page de connexion personnalisée
                .permitAll()
            )
            .logout(logout -> logout
                .logoutUrl("/logout")                      // URL de déconnexion
                .logoutSuccessUrl("/public/home")          // Redirection après déconnexion
                .permitAll()
            );
        return http.build();
    }
}
3. Endpoints protégés
Dans votre contrôleur, définissez des endpoints publics et protégés :

java
Copier le code
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class DemoController {

    @GetMapping("/public/home")
    public String publicHome() {
        return "Bienvenue sur la page publique !";
    }

    @GetMapping("/secure/dashboard")
    public String secureDashboard() {
        return "Bienvenue sur le tableau de bord sécurisé !";
    }
}
L'accès à /public/home est libre.
L'accès à /secure/dashboard nécessite une authentification.
4. Utilisateur en mémoire (In-Memory User)
Pour tester rapidement, configurez un utilisateur en mémoire :

java
Copier le code
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.provisioning.InMemoryUserDetailsManager;

@Configuration
public class UserConfig {

    @Bean
    public InMemoryUserDetailsManager userDetailsService() {
        UserDetails user = User.withDefaultPasswordEncoder()
                               .username("user")
                               .password("password")
                               .roles("USER")
                               .build();
        return new InMemoryUserDetailsManager(user);
    }
}
5. Personnalisation de la page de connexion
Pour une page de connexion personnalisée, ajoutez une vue simple en HTML à /src/main/resources/templates/login.html (si vous utilisez Thymeleaf) :

Exemple : login.html
html
Copier le code
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Connexion</title>
</head>
<body>
    <h2>Connexion</h2>
    <form th:action="@{/login}" method="post">
        <label for="username">Nom d'utilisateur :</label>
        <input type="text" id="username" name="username"><br>
        <label for="password">Mot de passe :</label>
        <input type="password" id="password" name="password"><br>
        <button type="submit">Se connecter</button>
    </form>
</body>
</html>
6. Tests de sécurité
Utilisez Spring Security Test pour vérifier la sécurité de votre application.

Exemple de test :
java
Copier le code
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.security.test.context.support.WithMockUser;
import org.springframework.test.web.servlet.MockMvc;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;

@WebMvcTest(DemoController.class)
class SecurityTests {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void publicEndpointShouldBeAccessible() throws Exception {
        mockMvc.perform(get("/public/home"))
               .andExpect(status().isOk())
               .andExpect(content().string("Bienvenue sur la page publique !"));
    }

    @Test
    @WithMockUser(username = "user", roles = "USER")
    void secureEndpointShouldBeAccessibleForAuthenticatedUser() throws Exception {
        mockMvc.perform(get("/secure/dashboard"))
               .andExpect(status().isOk())
               .andExpect(content().string("Bienvenue sur le tableau de bord sécurisé !"));
    }

    @Test
    void secureEndpointShouldBeForbiddenForUnauthenticatedUser() throws Exception {
        mockMvc.perform(get("/secure/dashboard"))
               .andExpect(status().is3xxRedirection()); // Redirection vers la page de login
    }
}
7. Nouvelles fonctionnalités de Spring Security 6.1
Compatibilité avec Java 17 : Utilisation des nouvelles API du JDK.
HttpSecurity DSL simplifié : Configuration fluide et lisible.
Mise à jour du support OAuth 2.1 : Alignement sur les derniers standards OAuth.
Suppression des APIs obsolètes : Migration vers des standards modernes.
8. Lancement et tests
Démarrez l'application et accédez aux endpoints protégés et publics.
Testez l'accès avec les identifiants : user / password.
Ce guide vous donne une base solide pour commencer avec Spring Security 6.1 et peut être étendu à des cas complexes comme OAuth2, JWT, ou la gestion des rôles.
