# User Rights and Conditions Documentation

This document provides guidelines on how to conditionally perform actions based on user rights within the system. 
## Tables in the DB
The operations trights are found in the table operation_type. 
Operations that are done by the user and that are found in this table are (even login) are saved in the table operation.
## Overview

In the system, actions are controlled based on user permissions. Permissions are determined by functions that check if a user has the right to perform certain actions. This document explains how to conditionally perform actions if a user has the required rights for "Echantillon" (Modification) and "Stockage" (Consultation).

## Functions Used

### `getDroitOnAction`

This function checks if the user has the right to perform a specific action on a specific entity.

#### Syntax
```java
public boolean getDroitOnAction(final String nomEntite, final String nomOperation)
```

#### Parameters
- `nomEntite`: The entity on which the action is to be performed (e.g., "Echantillon", "Stockage").
- `nomOperation`: The action to be performed on the entity (e.g., "Modification", "Consultation").

#### Returns
- `true` if the user has the right to perform the action on the entity.
- `false` otherwise.

#### Implementation
```java
public boolean getDroitOnAction(final String nomEntite, final String nomOperation) {
    Boolean admin = false;
    if(sessionScope.containsKey("Admin")) {
        admin = (Boolean) sessionScope.get("Admin");
    }

    // si l'utilisateur est admin => bouton cliquable
    if(admin) {
        return true;
    }
    
    // on extrait l'OperationType de la base
    final OperationType operation = ManagerLocator.getOperationTypeManager().findByNomLikeManager(nomOperation, true).get(0);

    Hashtable<String, List<OperationType>> droits = new Hashtable<>();

    if(sessionScope.containsKey("Droits")) {
        // on extrait les droits de l'utilisateur
        droits = (Hashtable<String, List<OperationType>>) sessionScope.get("Droits");

        final List<OperationType> ops = droits.get(nomEntite);
        return ops.contains(operation);
    }
    return false;
}
```

## Conditional Logic

To perform an action based on user rights, we use conditional statements that combine the results of `getDroitOnAction` calls. The condition we are examining is as follows:

```javascript
if (conditionA || (getDroitOnAction("Echantillon", "Modification") && getDroitOnAction("Stockage", "Consultation"))) {
    // Perform the action
}
```

### Explanation

1. **`conditionA`**: This is a pre-existing condition that, if true, allows the action to be performed without checking further permissions.
2. **`getDroitOnAction("Echantillon", "Modification")`**: This checks if the user has the right to modify an "Echantillon".
3. **`getDroitOnAction("Stockage", "Consultation")`**: This checks if the user has the right to consult a "Stockage".

The action is performed if either `conditionA` is true or both `getDroitOnAction("Echantillon", "Modification")` and `getDroitOnAction("Stockage", "Consultation")` are true.

## Example Usage

Here is an example in JavaScript:

```javascript
// Pre-existing condition
var conditionA = true; // or some other condition

// Check user rights and perform action accordingly
if (conditionA || (getDroitOnAction("Echantillon", "Modification") && getDroitOnAction("Stockage", "Consultation"))) {
    // User has the required rights
    console.log("User has the required rights. Performing the action...");
    // Perform the action here
} else {
    // User does not have the required rights
    console.log("User does not have the required rights. Action is not allowed.");
}
```

## Recommendation: Use Enums to Avoid Hard Coding

### Problem with Hard Coding

Hard coding strings like `"Echantillon"` and `"Modification"` can lead to errors, makes the code harder to maintain, and reduces readability. 

### Solution: Use Enums

Enums provide a way to define a set of named values. By using enums, we can avoid hard coding and make our code more maintainable and less error-prone.

#### Define Enums

First, define enums for entities and operations:

```java
public enum EntityType {
    ECHANTILLON,
    STOCKAGE,
    // Add other entities here
}

public enum OperationType {
    MODIFICATION,
    CONSULTATION,
    // Add other operations here
}
```

#### Update `getDroitOnAction` Method

Update the `getDroitOnAction` method to use enums instead of strings:

```java
public boolean getDroitOnAction(final EntityType entityType, final OperationType operationType) {
    Boolean admin = false;
    if(sessionScope.containsKey("Admin")) {
        admin = (Boolean) sessionScope.get("Admin");
    }

    if(admin) {
        return true;
    }

    final OperationType operation = ManagerLocator.getOperationTypeManager().findByNomLikeManager(operationType.name(), true).get(0);

    Hashtable<EntityType, List<OperationType>> droits = new Hashtable<>();

    if(sessionScope.containsKey("Droits")) {
        droits = (Hashtable<EntityType, List<OperationType>>) sessionScope.get("Droits");

        final List<OperationType> ops = droits.get(entityType);
        return ops.contains(operation);
    }
    return false;
}
```

#### Update Conditional Logic

Update the conditional logic to use enums:

```javascript
if (conditionA || (getDroitOnAction(EntityType.ECHANTILLON, OperationType.MODIFICATION) && getDroitOnAction(EntityType.STOCKAGE, OperationType.CONSULTATION))) {
    // Perform the action
}
```

By using enums, the code becomes more readable, maintainable, and less prone to errors.

## Summary

This document outlined how to conditionally perform actions based on user permissions for modifying "Echantillon" and consulting "Stockage". By using the `getDroitOnAction` function, we can ensure that actions are only performed by users with the appropriate rights. Additionally, using enums instead of hard-coded strings improves code quality and maintainability. The provided examples demonstrate how to implement these best practices in a practical scenario.