Entities in a Spring project represent the business objects or data structures that are persisted to a database.

## Introduction
This guide provides step-by-step instructions for developers to create a new entity in a Spring project. Entities in a Spring project represent the business objects or data structures that are persisted to a database.

Creating a new entity involves:
1. Defining the entity
2. Creating a corresponding table in the DB
3. Integrating it into the Spring application

## Steps to Create a New Entity
### Step 1: Define the Entity Class
Create a new Java class for your entity in the appropriate package.
```java
package com.example.model;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity
public class YourEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    // Add your entity fields here

    // Add getters and setters
}
```
- `@Entity`: Indicates that the class is a JPA entity.
- `@Id`: Marks the primary key field.
- `@GeneratedValue`: Specifies how the primary key should be generated.

### Step 2: Creating a Corresponding Table in the DB
You can add the creation of the table inside a Liquibase script.

### Step 3: Integrating it into the Spring Application
If you created a new Entity, you should include it in the `persistence.xml` file; otherwise, Spring won't find it. For example:
```xml
<class>fr.aphp.tumorotek.model.config.YourEntity</class>
```