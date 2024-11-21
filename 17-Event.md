#  ApplicationEventPublisher

L'interface ApplicationEventPublisher dans Spring est utilisée pour publier des événements dans le contexte de l'application Spring. Cette fonctionnalité fait partie de la gestion d'événements dans Spring, qui permet de décorréler les différentes parties de l'application en envoyant des événements qui peuvent être captés par des listeners.

Concepts clés autour de ApplicationEventPublisher :
Qu'est-ce que ApplicationEventPublisher ?

C'est une interface dans Spring qui permet de publier des événements au sein de l'application.
Les événements peuvent être captés par des beans configurés comme listeners et permettent de déclencher des actions lorsque certains événements se produisent.
L'interface offre une méthode principale :
void publishEvent(ApplicationEvent event);
Pourquoi l'utiliser ?

Découplage : En publiant un événement, vous permettez à d'autres composants de l'application (qui n'ont pas nécessairement connaissance de l'élément émetteur) de réagir à cet événement.
Gestion des événements de manière centralisée : Vous pouvez gérer tous les événements dans une application d'une manière uniforme et modulaire.
Comment l'utiliser ?

Exemple de base :

Voici un exemple pour illustrer l'utilisation d'un ApplicationEventPublisher.

Définir un événement personnalisé :

java
Copier le code
public class MyCustomEvent extends ApplicationEvent {
    private String message;
    
    public MyCustomEvent(Object source, String message) {
        super(source);
        this.message = message;
    }
    
    public String getMessage() {
        return message;
    }
}
Créer un Bean pour publier un événement :

java
Copier le code
@Component
public class EventPublisher {
    
    @Autowired
    private ApplicationEventPublisher applicationEventPublisher;
    
    public void publish(String message) {
        MyCustomEvent event = new MyCustomEvent(this, message);
        applicationEventPublisher.publishEvent(event);
    }
}
Créer un listener pour écouter l'événement :

java
Copier le code
@Component
public class MyEventListener {
    
    @EventListener
    public void onApplicationEvent(MyCustomEvent event) {
        System.out.println("Received event with message: " + event.getMessage());
    }
}
Utiliser le publisher pour publier un événement :

Dans n'importe quel autre composant, vous pouvez injecter et utiliser le EventPublisher pour publier l'événement.

java
Copier le code
@Component
public class SomeComponent {
    
    @Autowired
    private EventPublisher eventPublisher;
    
    public void triggerEvent() {
        eventPublisher.publish("Hello, this is a custom event!");
    }
}
Points importants à retenir :

Listeners : Vous pouvez utiliser l'annotation @EventListener pour définir des méthodes qui écouteront les événements spécifiques.
Propagation d'événements : Les événements peuvent être envoyés à un ou plusieurs listeners, permettant une approche flexible et asynchrone pour la gestion d'actions.
Personnalisation : Vous pouvez créer des événements personnalisés comme dans l'exemple ci-dessus, pour contenir des données spécifiques que vous souhaitez transmettre à d'autres composants.
Conclusion :
ApplicationEventPublisher est un mécanisme puissant dans Spring pour faciliter la communication entre différents composants de l'application sans qu'ils aient besoin de se connaître directement. Cela permet de construire des applications plus modulaires et réactives.

###  bloquant - non-bloquant 
Dans le contexte de l'utilisation de ApplicationEventPublisher dans Spring, le traitement des événements n'est pas nécessairement non-bloquant par défaut. Cependant, il peut être configuré pour être non-bloquant en fonction de la manière dont vous gérez l'écoute des événements.

Explication du traitement des événements dans Spring :
Événements dans Spring :
Quand vous publiez un événement avec ApplicationEventPublisher, les listeners enregistrés pour cet événement sont appelés dans le même thread que celui qui a publié l'événement, à moins que vous n'ayez configuré un mécanisme asynchrone.

Blocage ou Non-Blocage par défaut : Par défaut, le traitement des événements est bloquant dans le sens où la méthode publishEvent() bloque jusqu'à ce que tous les listeners d'un événement aient terminé leur traitement. Cela signifie que le thread qui a publié l'événement attendra la fin de l'exécution des listeners avant de continuer.

Gestion Asynchrone des Événements : Pour rendre le traitement non-bloquant, vous pouvez utiliser l'annotation @Async ou configurer Spring pour traiter les événements de manière asynchrone. Cela permet aux listeners d'être exécutés dans un thread séparé, libérant ainsi le thread principal qui a publié l'événement.

Exemple de traitement asynchrone avec @Async :
Activer le support asynchrone dans Spring : Vous devez d'abord activer le support asynchrone dans votre configuration Spring.

java
Copier le code
@Configuration
@EnableAsync
public class AsyncConfig {
    // Configurations supplémentaires si nécessaires
}
Utiliser @Async sur les listeners : Ensuite, vous pouvez utiliser l'annotation @Async sur les méthodes de vos listeners pour les exécuter de manière asynchrone.

java
Copier le code
@Component
public class MyAsyncEventListener {

    @Async
    @EventListener
    public void onApplicationEvent(MyCustomEvent event) {
        // Traitement long ou asynchrone
        System.out.println("Traitement asynchrone pour l'événement : " + event.getMessage());
    }
}
Résultat :

Non-bloquant : Le thread principal qui publie l'événement ne sera pas bloqué, et les événements seront traités dans un thread distinct.
Blocage : Si vous n'appliquez pas @Async ou une autre méthode de gestion asynchrone, le traitement sera bloquant par défaut.
Résumé :
Par défaut, le traitement des événements dans Spring avec ApplicationEventPublisher est bloquant.
Pour un traitement non-bloquant, vous pouvez utiliser l'annotation @Async pour exécuter les listeners dans des threads séparés, ce qui libère le thread d'appel.
