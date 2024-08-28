- Development Environment Setup
  - Required software and tools
  - Installation and configuration instructions
  - Recommended development environment best practices

Open a terminal window on your machine.
Log in to MySQL using the root user. You will be prompted to enter the password for the root user.
```bash
mysql -u root -p
```
Once you're logged in, run the following command to import the dump file into your database. Replace `dump_path` with the actual path to your dump file.
```bash
mysql -u root -p tumorotek --default-character-set=utf8 < dump_path
```
Note: It's important to use the root user for this operation. If you don't use the root user, you might encounter "Access denied" errors.

- Gatsbi Config Tomcat
Navigate to `C:\...Tomcat\...\conf\Catalina\localhost`
Verify that these attributes are configured correctly:
```xml
// ### Gatsbi .....
gatsbi.installation=true

## exemple conf pour front end déployé (war en français)
gatsbi.appli.url.path=http://localhost:4200/#/authentication?token={0}&plateformeId={1}

#If you use Nginx (the case in production environment)
## gatsbi.appli.url.path=http://localhost:80/gatsbi/fr/#/authentication?token={0}&plateformeId={1}

# change the port to the same port that is configured inside C:\T...\tumorotek-gatsbi-api-rest\src\main\resources\application.properties as a value of quarkus.http.port=xxxx

gatsbi.api.url.base=http://localhost:8703/tumorotek/api/gatsbi

#Don't touch this config
gatsbi.api.url.etude.path=/{etudeId}/etude
gatsbi.api.url.contexte.path=/{etudeId}/contexte/{type}
gatsbi.api.url.parametrage.path=/{parametrageId}/parametrage
gatsbi.api.url.schema.visite.path=/{etudeId}/etude/schemavisites

Verify Database connection parameters inside the file `application.properties`
```properties
quarkus.datasource.db-kind=mysql
quarkus.datasource.username={userName}
quarkus.datasource.password={password}
quarkus.datasource.jdbc.url=jdbc:mysql://localhost:3306/{dbname}?characterEncoding=UTF-8&rewriteBatchedStatements=true&serverTimezone=Europe/Paris
quarkus.datasource.jdbc.driver=com.mysql.cj.jdbc.Driver 
```

- Troubleshooting Potential Errors

  Access Denied Error (ERROR 1227): If you encounter an "Access denied" error like ERROR 1227 (42000) during the import process, make sure you're using the root user. Only the root user typically has the necessary privileges to perform this operation.

  Function Declaration Error (ERROR 1418): If you encounter an "ERROR 1418 (HY000)" related to function declaration and binary logging. You need to modify the MySQL configuration to allow non-deterministic functions to be created. Run the following command:
```sql 
SET GLOBAL log_bin_trust_function_creators = 1;
```

Maven Compile: If you have a Maven compile problem especially if the error message is:
```
Failed to execute goal on project tumorotek-interface: Could not resolve dependencies for project fr.aphp.tumorotek:tumorotek-interface:jar:2.3.0.4-SNAPSHOT: Failed to collect dependencies at org.springframework.ws:spring-ws-security:jar:2.1.3.RELEASE ->`...
```
You are working with the wrong Maven version. The right version is `mvn` 3.6.3