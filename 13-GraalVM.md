#  Building Native Applications with Spring Boot and GraalVM
Spring Boot native images permettent de compiler une application Spring Boot en un exécutable natif, souvent pour améliorer les performances et réduire les coûts d’infrastructure, particulièrement dans des environnements cloud ou de microservices. Voici une analyse plus détaillée sur les motivations, les compromis, et les étapes pour créer et utiliser des images natives avec Spring Boot.

1. Pourquoi utiliser les images natives ?
L'utilisation d'images natives apporte plusieurs avantages :
Démarrage instantané : Une application native démarre en millisecondes, contre plusieurs secondes pour une application Java traditionnelle sur la JVM.
Absence de montée en puissance (warmup) : Les performances maximales sont atteintes immédiatement, sans besoin d'optimisation JIT (Just-In-Time).
Utilisation réduite des ressources : Un exécutable natif consomme moins de CPU et de mémoire, ce qui permet d'utiliser des instances cloud moins coûteuses et plus efficaces.
Sécurité améliorée : En réduisant les opérations de réflexion et en limitant les métadonnées nécessaires, l'application native offre une surface d'attaque plus réduite. Elle n'est pas en mesure de charger dynamiquement de nouveaux éléments de code, limitant ainsi les risques de sécurité.
Emballage compact : Les conteneurs sont plus petits, ce qui simplifie le déploiement et le stockage.
2. Différences et compromis
Les applications sur la JVM et les images natives présentent des avantages différents, mais également des compromis importants :
Temps de construction : La création d'une image native est beaucoup plus lente et exigeante en ressources qu'une simple compilation JVM. La construction native est donc un processus additionnel, et non un remplacement du build JVM standard.
Compatibilité : Les images natives nécessitent des métadonnées de "reachability" pour indiquer quelles opérations seront effectuées à l'exécution. Spring essaie de déterminer automatiquement ces métadonnées, mais pour les applications complexes, des ajustements manuels sont souvent nécessaires.
Absence d’optimisation dynamique : Les applications natives ne bénéficient pas des optimisations dynamiques du JIT, ce qui peut donner un throughput légèrement inférieur à celui de la JVM dans certains cas.
Performance variable : Le niveau de performance peut dépendre de la distribution de GraalVM utilisée. Si des ressources suffisantes sont allouées, la JVM peut souvent surpasser les performances de l'image native sur des charges importantes.
3. Comment créer une image native avec Spring Boot ?
Depuis Spring Boot 3, la prise en charge officielle de la compilation d'images natives avec GraalVM est disponible. Voici un aperçu des étapes et différences :
Déploiement traditionnel avec JVM : Une application Spring Boot classique est transformée en bytecode Java et empaquetée dans une image de conteneur qui comprend le système d'exploitation, la JVM et l'application elle-même.
Déploiement en tant qu'image native :
Optimisations AOT (Ahead-Of-Time) : Spring Framework 6 introduit des optimisations AOT pour pré-calculer, dès la phase de construction, les beans à injecter, ce qui réduit la réflexion à l'exécution.
Compilation native avec GraalVM : Après la génération du bytecode Java, le compilateur d'image native de GraalVM analyse l'application, en génère un exécutable optimisé et autonome, lié statiquement, capable de fonctionner sur un OS minimal.
Résultat : Le conteneur natif ainsi créé démarre très rapidement (quelques millisecondes) et consomme moins de mémoire. Ces conteneurs sont particulièrement adaptés aux environnements cloud et Kubernetes, notamment pour les services qui s’adaptent à la demande et peuvent "scaler à zéro" lorsqu'ils ne sont pas utilisés.
Exemple de création d'une image native avec Spring Boot 3
Assurez-vous que GraalVM est installé et configuré sur votre environnement.
Dans votre projet Spring Boot, ajoutez la dépendance de compilation native dans votre fichier pom.xml :
xml
Copier le code
<dependency>
    <groupId>org.springframework.experimental</groupId>
    <artifactId>spring-native</artifactId>
    <version>VERSION</version>
</dependency>


Activez la création de l'image native :
bash
Copier le code
mvn spring-boot:build-image -Pnative


Une fois la compilation terminée, déployez l'image native résultante dans votre environnement préféré, comme Kubernetes ou une plateforme cloud prenant en charge des microservices légers et autonomes.
En somme, les images natives pour Spring Boot, bien que puissantes, nécessitent une planification minutieuse et une compréhension des compromis, notamment en termes de compatibilité et de temps de build. Elles conviennent particulièrement aux architectures de microservices qui bénéficient de démarrages rapides, de faibles empreintes mémoire et de capacités d'évolutivité instantanées.


package com.example.demo;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
class HelloController {

    @GetMapping("/")
    String hello() {
        return "hello";
    }
}


4: Install GraalVM and the native image build tools
Now that we've seen the non-native application work as expected, we'll use the Native Build Tools plugins in order to compile your Spring Boot application to a native executable.
But, we haven't installed any native build tools. Let's do so, using the recommended installation method for Linux and MacOS: Using SDKMAN!
Install SDKMAN!
The installation is simple and quick:
[~/demo] $ curl -s "https://get.sdkman.io" | bash

Because of the way Linux shells work, in order for the installed sdk executable to be visible, you'll need to alter the shell environment to make it visible, or create a new shell. We'll follow the instructions from SDKMAN! to alter the shell environment:
[~/demo] source "/home/eduk8s/.sdkman/bin/sdkman-init.sh"

Install GraalVM
Now we'll use SDKMAN! to install a GraalVM distribution. Since those are Java distributions, we'll ask SDKMAN! what choices it has in the java application: There are many, but we recommend either "GraalVM CE" (CE means Community Edition) or "Liberica NIK" (NIK means Native Image Kit).
[~/demo] $ sdk list java
================================================================================
Available Java Versions for Linux 64bit
================================================================================
 Vendor        | Use | Version      | Dist    | Status     | Identifier
--------------------------------------------------------------------------------
 ...
 Liberica NIK  |     | 23.r20       | nik     |            | 23.r20-nik
               |     | 23.r17       | nik     |            | 23.r17-nik

Great! Let's use Liberica NIK, which is based on the same JDK as the regular Liberica distribution. We'll choose this one and install the latest Java 17 version available:
[~/demo] $ sdk install java 23.r17-nik

Let's verify that our java executable is the GraalVM we just installed. First, you'll need to start a new shell, so that the shell can "see" what was just installed by SDKMAN!:
[~/demo] $ bash

Now that you've started a new shell, you should see that the version of Java is the newly-installed GraalVM:
[~/demo] $ java -version
openjdk version "17.0.7" 2023-04-18 LTS
OpenJDK Runtime Environment Liberica-NIK-23.0.0-1 (build 17.0.7+7-LTS)
OpenJDK 64-Bit Server VM Liberica-NIK-23.0.0-1 (build 17.0.7+7-LTS, mixed mode, sharing)

Let's also check that the native-image tool is available as expected:
[~/demo] $ native-image --version
native-image 17.0.7 2023-04-18
GraalVM Runtime Environment Liberica-NIK-23.0.0-1 (build 17.0.7+7-LTS)
Substrate VM Liberica-NIK-23.0.0-1 (build 17.0.7+7-LTS, serial gc)

Now that we have GraalVM installed, we're ready to use it to build and run our application as a native image.

5: Build and run your application using Native Build Tools
Let's build the native image:
[~/demo] $ ./gradlew nativeCompile
...
Finished generating 'demo' in 1m 34s.
    [native-image-plugin] Native Image written to: demo/build/native/nativeCompile

BUILD SUCCESSFUL in 2m 4s

You'll notice that the native build takes significantly more time than JVM build we did previously.
During the execution of this command, Spring Ahead-Of-Time (AOT) engine analyzes the application and infers the native configuration needed (for reflection, proxies, or resources, for example), and generates a programmatic application context, optimized for native image (less reflection needed, Spring Boot conditions precomputed, etc). Then, the GraalVM native image compiler performs a static analysis of the application, compiles the JVM bytecode to a native executable, and includes the dependencies and JVM subsets used.
Run the application with the native executable
In the upper terminal, execute this command:
[~/demo] $ build/native/nativeCompile/demo

Check the application startup time again. Notice that this time, the native application started significantly faster:
Started DemoApplication in 0.054 seconds (process running for 0.096)

Once again, verify that the application is behaving correctly. In the lower terminal, request the web page and check that your application still says "hello":
[~/demo] $ curl http://localhost:8080/
hello

In the upper terminal, stop the application by pressing CTRL + C.
Celebrate!
Congratulations! You just built, ran, and tested a Spring Boot application as a native image.


6: Build and run your application using Cloud Native Buildpacks
Now that you've successfully run the native image in your development environment, let's consider additional concerns that apply when deploying the application to the Cloud. Note that we won't actually deploy the application, but we will build it in a different way that is more suitable for Cloud deployment.
Cloud Native Buildpacks - What and Why?
Spring Boot provides first class support for building container images thanks to Cloud Native Buildpacks.
When you build the application using a Cloud Native Buildpack, you produce not only the native executable application, but also a wrapper (a Docker container, or more precisely an OCI container), which is a completely self-contained image, suitable for running in pretty much all modern Cloud infrastructure, including but not limited to AWS, Azure, GCP, or Tanzu.
Requirements for building with a Cloud Native Buildpack
Building your application using a Buildpack does not require the GraalVM native image compiler to be installed locally, but it does require Docker to be installed and usable as the current (non-root) user. Not to worry! This interactive Lab environment already has Docker installed and ready for use.
Note: Regarding CPU architecture support: Cloud Native Buildpack support allows you to create Linux x86/AMD container images independently of the host Operating System, but ARM CPU architecture (e.g. the Mac M1 CPU) is not yet supported.
Build the application with Cloud Native Buildpack
You can now build the application as an OCI container using the native Buildpack:
[~/demo] $ ./gradlew bootBuildImage

This will take some time to complete, as this command triggers the native compilation of the application, as well the creation of an OCI image which contains a (virtualized) operating system.
You'll know the build is complete when you see output similar to the following:
...
   [creator]     [7/7] Creating image...                                         (32.5s @ 2.31GB)
...
Successfully built image 'docker.io/library/demo:0.0.1-SNAPSHOT'
BUILD SUCCESSFUL in 2m 23s

Once the build is complete, running the resulting OCI image is quite easy and the startup time is very fast, as you can see by the output:
[~/demo] $ docker run --rm -p 8080:8080 docker.io/library/demo:0.0.1-SNAPSHOT

Started DemoApplication in 0.045 seconds (process running for 0.048)

What does the above command do?
docker run directs the Docker application to run the image that you just built.
--rm means "When the container exits, clean-up after yourself please Docker, thank you."
-p 8080:8080 allows you to make HTTP requests to the running application from outside the Docker container (but still on your local machine).
docker.io/library/demo:0.0.1-SNAPSHOT is the name of the image that was registered in your local Docker registry from the previous step.
To be sure that nothing has gone awry, go ahead and request the web page in the same way that you've already verified in earlier builds:
[~/demo] $ curl http://localhost:8080/
hello

Congratulations! You have created an OCI container image that contains your application and the entire supporting Operating System. The image contains only native code: there is no JVM involved, which means that much less hardware resources, in particular RAM, are required when running a native image.


