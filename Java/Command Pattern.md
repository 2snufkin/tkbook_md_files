# Liquibase Overview

Liquibase is an open-source database-independent library for tracking, managing, and applying database schema changes. It allows you to version control your database schema, making it easier to deploy.

## Configuring Liquibase
To work with Liquibase and run Liquibase commands, you need to edit the `tumorotek-webapp/src/main/resources/liquibase/liquibase.properties` file. Update the following properties:
- `url`: the URL for tk database
- `username`: MySQL username
- `password`: MySQL password

## Resolving Commend Error
If you encounter an error while running the update command like "Could not acquire change log lock. Currently locked by xxx since yyy," follow these steps:
1. Run this query to see the problem: 
    ```sql
    SELECT * FROM DATABASECHANGELOGLOCK WHERE ID = 1;
    ```
2. Release the lock by running: 
    ```sql
    UPDATE DATABASECHANGELOGLOCK SET LOCKED = 0, LOCKGRANTED = NULL, LOCKEDBY = NULL WHERE ID = 1;
    ```

## How Liquibase Works
### Changelog File
Liquibase uses a changelog file (usually in XML, YAML, or SQL format) to define database changes.
The changelog file contains a set of changesets, each representing a specific database change.

### Changesets
Changesets are units of change within the changelog file.
Each changeset describes a specific database change using a set of commands.

### Commands
Liquibase commands define specific database operations such as creating a table, adding a column, or inserting data.
Common commands include `<createTable>`, `<addColumn>`, `<modifyColumn>`, `<sql>`, etc.

### Database Connection
Liquibase requires information about the database connection to apply changes.
Connection details (URL, username, password) are usually specified in a properties file or directly in the changelog file.

### Execution
Liquibase can be executed from the command line, integrated into build tools (Maven, Gradle), or used programmatically.
When Liquibase runs, it checks the database for its change history and applies pending changes.

## Setting up Liquibase Configuration
1. **Install Liquibase**: Download it from the official website or use Maven or Gradle dependency management.
2. **Database Configuration**: Configure your database connection details in either a properties file or directly in the changelog file.
3. **Changelog File**: Create a changelog file to define your database changes.

## Defining Datasource in Liquibase
You can define the database connection information using either a properties file or directly in the changelog file:
1. **Using Properties File**:
   - Create a `liquibase.properties` file with your connection details.
   ```properties
   url=jdbc:mysql://localhost:3306/dev_tumo?useSSL=false&createDatabaseIfNotExist=true&characterEncoding=UTF-8&rewriteBatchedStatements=true&serverTimezone=Europe/Paris
   username=your_username
   password=your_password
   ```
2. **Directly in Changelog File**:
   - Define connection details within the changelog XML using attributes like `url`, `username`, and `password`.

## Using Liquibase in TK Project
In TK project's pom.xml files:
- **tumorotek-webapp** plugin configuration sets up Liquibase execution during Maven build process.
- **tumorotek-model** ignores certain goals of Liquibasen based on version range.

## Force Rerun of Changeset 
If you need to force rerun of specific changeset due to configurations:
1. Identify Changeset: Find ID and AUTHOR of desired changeset.
2. Delete from DATABASECHANGELOG: Remove corresponding record using SQL DELETE statement.
3. Run Liquibse: After deletion, run Liquibse again for reapplication of changeset during Maven build process.

This is an overview; adjust processes based on your requirements and databases supported by Liquibae features like rollbacks and preconditions for effective management of database schema changes.