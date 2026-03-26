# Installation

## Prerequis

- Java 25+
- Maven 3.9+

## Dependance Maven

Le SDK est publie sur GitHub Packages. Ajoutez-le a votre `pom.xml` :

```xml
<dependency>
    <groupId>fr.capellegab</groupId>
    <artifactId>pme-sdk</artifactId>
    <version>0.0.1</version>
</dependency>
```

## Configurer GitHub Packages

Ajoutez le repository dans votre `pom.xml` :

```xml
<repositories>
    <repository>
        <id>github</id>
        <url>https://maven.pkg.github.com/Processing-Modular-Events/pme-sdk</url>
    </repository>
</repositories>
```

Et configurez l'authentification dans `~/.m2/settings.xml` :

```xml
<settings>
    <servers>
        <server>
            <id>github</id>
            <username>VOTRE_USERNAME</username>
            <password>VOTRE_GITHUB_TOKEN</password>
        </server>
    </servers>
</settings>
```

!!! note "Token GitHub"
    Creez un Personal Access Token avec le scope `read:packages` depuis [GitHub Settings](https://github.com/settings/tokens).

## Verifier l'installation

```java
import fr.capellegab.api.PmeSdk;

public class Main {
    public static void main(String[] args) {
        System.out.println(PmeSdk.info());
        // Affiche : pme-sdk v0.0.1
    }
}
```
