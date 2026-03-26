# Installation

## Prerequisites

- Java 25+
- Maven 3.9+

## Add the dependency

The SDK is published on GitHub Packages. Add it to your `pom.xml`:

```xml
<dependency>
    <groupId>fr.capellegab</groupId>
    <artifactId>pme-sdk</artifactId>
    <version>0.0.1</version>
</dependency>
```

## Configure GitHub Packages

For Maven to download the SDK, add the repository to your `pom.xml`:

```xml
<repositories>
    <repository>
        <id>github</id>
        <url>https://maven.pkg.github.com/Processing-Modular-Events/pme-sdk</url>
    </repository>
</repositories>
```

And configure authentication in `~/.m2/settings.xml`:

```xml
<settings>
    <servers>
        <server>
            <id>github</id>
            <username>YOUR_USERNAME</username>
            <password>YOUR_GITHUB_TOKEN</password>
        </server>
    </servers>
</settings>
```

!!! note "GitHub Token"
    Create a Personal Access Token with the `read:packages` scope from [GitHub Settings](https://github.com/settings/tokens).

## Verify installation

```java
import fr.capellegab.api.PmeSdk;

public class Main {
    public static void main(String[] args) {
        System.out.println(PmeSdk.info());
        // Prints: pme-sdk v0.0.1
    }
}
```
