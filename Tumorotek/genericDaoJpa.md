All or most of the DAO interfaces in Tumorotek extends GenericDaoJpa. 

### GenericDaoJpa Interface
The GenericDaoJpa interface defines a generic Data Access Object (DAO) using Java Persistence API (JPA) for performing CRUD (Create, Read, Update, Delete) operations on a database. This interface is parameterized with a type T (the entity type) and PK (the primary key type which extends Serializable).

#### Purpose
The purpose of this interface is to provide a standardized set of methods for interacting with a database, allowing for the persistence, retrieval, updating, and deletion of objects. By defining these methods generically, this interface can be implemented for any entity type, promoting code reuse and reducing boilerplate.

#### Methods and Their Purposes
- **createObject(T newInstance)**
  - *Purpose:* Persist a new instance of an object in the database.
  - *Parameter:* newInstance is the instance of the object to be created.
  - *CRUD Action:* Create

- **mergeObject(T o)**
  - *Purpose:* Merge the state of the given entity into the current persistence context.
  - *Parameter:* o is the instance of the object to be merged.
  - *Returns:* The managed instance that the state was merged to.
  - *CRUD Action:* Update

- **findById(PK id)**
  - *Purpose:* Retrieve an object that was persistent in the database using its primary key.
  - *Parameter:* id is the primary key of the object.
  - *Returns:* The object with the given primary key.
  - *CRUD Action:* Read

- **findAll()**
  - *Purpose:* Return all objects of a certain type present in the database.
  - *Returns:* A list of all objects of the specified type.
  - *CRUD Action:* Read

- **updateObject(T transientObject)**
  - *Purpose:* Save the changes made to a persistent object.
  - *Parameter:* transientObject is the object to be updated in the database.
  - *CRUD Action:* Update

- **removeObject(PK id)**
  	- **Purpose:** Remove an object from the database.
  	- **Parameter:** id is the primary key of the object to be removed.
  	- **CRUD Action:** Delete

#### Delete
Where there is a need to delete a record from [Databse] your first reflax may be to cretae a named query. For Example:

```java
@NamedQuery(name = "ParametreValeurSpecifique.deleteByPlateformeIdAndCode",
            query = "DELETE FROM ParametreValeurSpecifique p WHERE p.plateformeId = ?1 AND p.code = ?2")
```

Then to create a method with [the] dame name `deleteByPlateformeIdAndCode` in [the] DAO, Manager, and in [the] ManagerImpl [to] call [the] deleteByPlateformeIdAndCode on [the] dao instance. But it won't work. You will get an exception: object is not an instance of declaring class. The excpetion happens when you call [the] invoke method in public class FinderIntroductionInterceptor implements IntroductionInterceptor.

```java
@Override
public Object invoke(final MethodInvocation methodInvocation) throws Throwable {
    final FinderExecutor genericDao = (FinderExecutor) methodInvocation.getThis();
    final String methodName = methodInvocation.getMethod().getName();
    if(methodName.startsWith("find") && !methodName.equals("findById") && !methodName.equals("findAll")) {
        final Object[] arguments = methodInvocation.getArguments();
        return genericDao.executeFinder(methodInvocation.getMethod(), arguments);
    }
    else if(methodName.startsWith("count")) {
        final Object[] arguments = methodInvocation.getArguments();
        return genericDao.executeCounter(methodInvocation.getMethod(), arguments);         
    }
    return methodInvocation.proceed();
}
```

Now, try to find in [the] code base a named query that corresponds to "DELETE FROM...".

I didn't find any in [the] main Entites. This means that deleting from [the] Database in Tumorotek is implemented differently and not by [the] mechanism of named queries. A similar exploration through [the] different DAOs of [the] project shows that most of [the] methods are declared starting with "find". Even if what you need is to count, developers called it "findCount". The support for invoking methods that start with "count" is later introduction (2022). 

```java
else if(methodName.startsWith("count"))
```

**How do we delete in Tumorotek?** In [the manager interface], create a method that starts with "remove" and not "delete", and finish it with "Manager". If deleting in cascade is needed, see code examples from EchantillonManager. If not, call [the removeObject method](#methods-and-their-purposes) and pass[...] ID[...] record you want to delete.