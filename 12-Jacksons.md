#    Jackson JAR (Java Library)

The Jackson library is a popular, high-performance JSON processing library for Java. It allows developers to serialize (convert Java objects to JSON) and deserialize (convert JSON to Java objects) efficiently. It is widely used in Spring Boot and other Java applications for handling JSON data.

Key Features of Jackson
Object Mapping:

Maps JSON to Java objects (and vice versa).
Supports nested structures, collections, and custom objects.
Annotations:

Configure serialization/deserialization using annotations like:
@JsonProperty
@JsonIgnore
@JsonFormat
@JsonInclude
Custom Serializers/Deserializers:

Create custom behavior for specific fields or object types.
Support for Other Formats:

Extensions for XML, YAML, CBOR, Smile, and more.
Streaming API:

Provides a lower-level streaming API for reading/writing JSON, ideal for large datasets.
Integration:

Works seamlessly with frameworks like Spring Boot, RESTEasy, and Dropwizard.
Dependencies
To use Jackson in your project, include the following dependencies in your build file.

Maven:
```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.15.2</version> <!-- Replace with the latest version -->
</dependency>
```
Gradle:
```gradle
implementation 'com.fasterxml.jackson.core:jackson-databind:2.15.2'
```
Modules of Jackson
Core Modules:

jackson-core: Core streaming APIs for JSON parsing and generation.
jackson-annotations: Provides annotations for controlling JSON behavior.
jackson-databind: Combines core and annotations for object mapping.
Additional Modules:

jackson-module-java8: Support for Java 8 features (e.g., Optional).
jackson-datatype-jsr310: Support for Java 8 Date and Time API.
jackson-module-kotlin: Integration with Kotlin.
Basic Example
Serialization (Java Object to JSON):
```java
import com.fasterxml.jackson.databind.ObjectMapper;

public class JacksonExample {
    public static void main(String[] args) throws Exception {
        ObjectMapper mapper = new ObjectMapper();

        // Java Object
        Person person = new Person("John", 30);

        // Serialize to JSON
        String json = mapper.writeValueAsString(person);
        System.out.println(json); // {"name":"John","age":30}
    }
}

class Person {
    private String name;
    private int age;

    // Constructor, Getters, and Setters
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() { return name; }
    public void setName(String name) { this.name = name; }

    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
}
```
Deserialization (JSON to Java Object):
```java
import com.fasterxml.jackson.databind.ObjectMapper;

public class JacksonExample {
    public static void main(String[] args) throws Exception {
        ObjectMapper mapper = new ObjectMapper();

        // JSON String
        String json = "{\"name\":\"John\",\"age\":30}";

        // Deserialize to Java Object
        Person person = mapper.readValue(json, Person.class);
        System.out.println(person.getName() + " is " + person.getAge() + " years old.");
    }
}
```
Common Annotations
Annotation	Description
@JsonProperty	Maps a JSON property to a specific Java field.
@JsonIgnore	Ignores the field during serialization/deserialization.
@JsonInclude	Controls inclusion of null/empty values.
@JsonFormat	Customizes date and time format.
@JsonCreator	Marks a constructor or factory method for deserialization.
@JsonValue	Marks a method to define how an object is serialized.
Error Handling
Jackson throws exceptions for invalid JSON or mapping issues:

JsonMappingException: Issues with mapping JSON to Java.
JsonParseException: Issues with parsing invalid JSON.

Voici un exemple pratique utilisant les annotations courantes de Jackson dans une classe Java. Nous allons illustrer comment personnaliser la sérialisation et la désérialisation en utilisant les annotations suivantes :

@JsonProperty
@JsonIgnore
@JsonInclude
@JsonFormat
@JsonCreator et @JsonValue
Code Complet avec les Annotations
Classe Java avec Annotations
```java
import com.fasterxml.jackson.annotation.*;
import com.fasterxml.jackson.databind.ObjectMapper;
import java.time.LocalDate;

public class JacksonAnnotationsExample {

    public static void main(String[] args) throws Exception {
        ObjectMapper mapper = new ObjectMapper();

        // Exemple de données à sérialiser
        Person person = new Person(1, "Alice", "Smith", LocalDate.of(1990, 5, 20), null);

        // Sérialisation : Objet Java -> JSON
        String json = mapper.writeValueAsString(person);
        System.out.println("JSON sérialisé : " + json);

        // Désérialisation : JSON -> Objet Java
        String inputJson = """
        {
            "person_id": 2,
            "first_name": "Bob",
            "last_name": "Brown",
            "birth_date": "1985-10-15"
        }
        """;
        Person deserializedPerson = mapper.readValue(inputJson, Person.class);
        System.out.println("Objet désérialisé : " + deserializedPerson);
    }
}

// Classe avec annotations Jackson
@JsonInclude(JsonInclude.Include.NON_NULL) // Exclut les champs null du JSON
class Person {
    @JsonProperty("person_id") // Nom personnalisé pour le champ dans le JSON
    private int id;

    @JsonProperty("first_name") // Nom personnalisé pour le champ dans le JSON
    private String firstName;

    @JsonProperty("last_name") // Nom personnalisé pour le champ dans le JSON
    private String lastName;

    @JsonFormat(pattern = "yyyy-MM-dd") // Format pour les dates
    private LocalDate birthDate;

    @JsonIgnore // Ignore ce champ pendant la sérialisation/désérialisation
    private String password;

    // Constructeur pour Jackson
    @JsonCreator
    public Person(
            @JsonProperty("person_id") int id,
            @JsonProperty("first_name") String firstName,
            @JsonProperty("last_name") String lastName,
            @JsonProperty("birth_date") LocalDate birthDate,
            @JsonProperty("password") String password) {
        this.id = id;
        this.firstName = firstName;
        this.lastName = lastName;
        this.birthDate = birthDate;
        this.password = password;
    }

    // Getters et Setters
    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getFirstName() {
        return firstName;
    }

    public void setFirstName(String firstName) {
        this.firstName = firstName;
    }

    public String getLastName() {
        return lastName;
    }

    public void setLastName(String lastName) {
        this.lastName = lastName;
    }

    public LocalDate getBirthDate() {
        return birthDate;
    }

    public void setBirthDate(LocalDate birthDate) {
        this.birthDate = birthDate;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    // Méthode personnalisée pour sérialisation
    @JsonValue
    public String getFullName() {
        return firstName + " " + lastName;
    }

    @Override
    public String toString() {
        return "Person{id=" + id + ", firstName='" + firstName + '\'' +
                ", lastName='" + lastName + '\'' + ", birthDate=" + birthDate + '}';
    }
}
```
Explications des Annotations
Annotation	Description
@JsonProperty	Définit un nom personnalisé pour un champ dans le JSON (exemple : person_id pour le champ id).
@JsonIgnore	Ignore un champ lors de la sérialisation et de la désérialisation (exemple : password).
@JsonInclude	Exclut les champs null ou vides en fonction de la configuration (exemple : password = null).
@JsonFormat	Définit un format spécifique pour les dates, heures, etc. (exemple : yyyy-MM-dd pour birthDate).
@JsonCreator	Indique le constructeur à utiliser pour la désérialisation (associé à @JsonProperty).
@JsonValue	Indique une méthode à utiliser pour produire une représentation JSON personnalisée (exemple : nom complet).
Sortie
JSON Sérialisé
```json
{
  "person_id": 1,
  "first_name": "Alice",
  "last_name": "Smith",
  "birth_date": "1990-05-20"
}
```
Objet Désérialisé
```plaintext
Objet désérialisé : Person{id=2, firstName='Bob', lastName='Brown', birthDate=1985-10-15}
```
