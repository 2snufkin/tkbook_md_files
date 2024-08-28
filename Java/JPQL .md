```markdown
# JPQL

Please note that these are general indicators and not definitive proof of whether HQL or JPQL is being used. To be certain, you may need to check the project's dependencies and the specific ORM configuration.

## Using JPQL

To use JPQL in your project, follow these steps:

1. **Define the JPQL Query:** Construct the JPQL query as a string using the JPQL syntax. You'll use Java class names and their mapped properties instead of SQL table and column names.
2. **Logging (Optional):** Before executing the query, you might want to log the generated JPQL query for debugging purposes.
3. **Create an EntityManager:** Declare the entityManagerFactory and create an EntityManager instance.
4. **Create a TypedQuery:** Create a TypedQuery object using the `createQuery()` method on the EntityManager, passing the JPQL query as an argument.
5. **Set Parameters:** If the JPQL query includes parameters, set them using the appropriate methods (`setParameter()` or `setXxx()`) on the TypedQuery object.
6. **Execute the Query and Retrieve Results:** Execute the query using the `getResultList()` method to fetch a list of entities that match the query's criteria.
7. **Handle the Results:** Use the obtained result list as needed, such as returning the results or performing additional operations.
8. **Close Resources:** Once query execution and result processing are complete, close the EntityManager to release acquired resources.
9. **Handle Exceptions:** Properly handle any exceptions that may occur during the process to ensure application stability.

## Best Practices for Writing JPQL Queries

Here are some best practices for writing JPQL queries:

- **Entity and Property Names:** Use Java entity class names and their property names instead of table and column names from the database. Ensure proper capitalization and spelling.
- **Avoid Database-specific Functions:** HQL aims to be database agnostic, so avoid using database-specific functions in your queries.
- **Use Parameters for Dynamic Values:** Use query parameters (`:paramName`) for dynamic values to prevent SQL injection vulnerabilities.
- **Prefer Lazy Fetching:** Unless necessary, prefer lazy fetching for associations to avoid unnecessary data loading.

...

By following these best practices, you can write efficient, secure, and maintainable JPQL queries for your Java applications.

### Example

Assuming you have two entity classes: Employee and Department, with a one-to-many relationship. The JPQL query for performing a JOIN between these entities could look like this:

```java
String jpqlQuery = "SELECT e FROM Employee e JOIN e.department d WHERE d.departmentName = :department";
```