# Module architecture

Every PME module must follow a mandatory architecture. The [**template**](https://github.com/Processing-Modular-Events/pme-module-template) already provides this structure — use it as a starting point.

Non-compliant modules will be rejected by the core at loading time.

## Structure

```
pme-module-{name}/
├── module.yml                                    <- Configuration (required)
├── pom.xml
└── src/main/java/{your}/{package}/{name}/
    ├── {Name}Module.java                         <- Entry point (implements EventModule)
    ├── analyzer/                                 <- Business logic
    │   └── ...
    └── model/                                    <- Data models
        └── ...
```

The Java package is **free** — use your organization's domain name (e.g. `fr.capellegab.fraud`, `com.grafikart.analytics`, `io.steeve.metrics`).

## Rules

### 1. `module.yml` (included in template)

Configuration file in `src/main/resources/`. Edit the values with your module's info. All fields are required.

```yaml
name: my-module
version: 1.0.0
author: my-name
description: What my module does
priority: MEDIUM
subscribes-to:
  - TRANSACTION
```

### 2. `{Name}Module.java` (included in template)

Class at the root of the package that implements `EventModule`. Rename `ExampleModule.java` and implement your logic.

```java
public class MyModule implements EventModule {

    @Override
    public ModuleConfig config() {
        return ModuleConfigReader.load();
    }

    @Override
    public void onEvent(Event event, EventContext context) {
        // ...
    }
}
```

### 3. `analyzer/` (included in template)

Package containing all business logic. Add your processing classes here.

### 4. `model/` (included in template)

Package containing data models specific to the module (records, enums, classes).

## Validation at loading time

The core automatically validates:

- `module.yml` is present and complete
- No empty or null fields
- Module name is unique (no duplicates)

If validation fails, the module is rejected and an error message is logged.

## Example: fraud module

```
pme-module-fraud/
├── module.yml
├── pom.xml
└── src/main/java/fr/capellegab/fraud/
    ├── FraudModule.java
    ├── analyzer/
    │   └── FraudAnalyzer.java
    └── model/
        └── FraudAlert.java
```
