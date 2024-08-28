This tutorial will guide you how to create and work with a new DAO.
1. **Create a DAO Interface:**
    ```java
    // Example UserDao.java
    public interface UserDao GenericDaoJpa<T,T>{
        User findById(Long id);
        // other methods...
    }
    ```
2. **DAO implementation:**
   When you define a method in a Spring Data JPA repository interface with a specific name, Spring Data JPA looks for a named query with the same name defined in the JPA entity associated with the repository.
   For example, you have a method in the EchantillonDao interface:
   ```java
   List<Echantillon> findByCode(String code);
   ```
   And in your Echantillon entity, you have a named query with the same name:
   ```java
   @NamedQueries(value = {@NamedQuery(name = "Echantillon.findByCode", query = "SELECT e FROM Echantillon e WHERE e.code like ?1")})
   ```
3. **Configure the DAO Bean in applicationContextDao.xml:**
   
<!-- applicationContextDao.xml -->
```xml
<beans>
 
    <!-- Copy From Here-->
    <bean id="!name!" parent="abstractDao">
        <property name="proxyInterfaces">
            <value>!packageName.DaoClassName!</value>
        </property>
        <property name="target">
            <bean parent="abstractDaoTarget">
                <constructor-arg>
                    <value>!packageName.EntityName!</value>
                </constructor-arg>
            </bean>
        </property>
    </bean>
        <!-- Till Here -->
</beans>
```

4. **Inject the DAO to the Service who uses it:**
```xml
<beans>

    <!-- Configure UserService bean -->
    <bean id="userService" class="com.example.service.UserService">
        <property name="userDao" ref="userDao" />
    </bean>
</beans>
```

5. **Write a Test:**

```java
public class ParametreDaoTest extends AbstractDaoTest {

   private ParametreDao parametreDao;

   public void setParametreDao(ParametreDao parametreDao){
      this.parametreDao = parametreDao;
   }

   public void testFindByPlateformeIdAndCode(){
      Integer plateformID = 1;
      String code = "welcome.message";

      Parametre newParametre = new Parametre();
      newParametre.setPlateformeId(plateformID);
      newParametre.setCode(code);

      parametreDao.createObject(newParametre);

      List<Parametre> result = parametreDao.findByPlateformeIdAndCode(plateformID, code);
      assertNotNull(result);
      assertEquals(1 , result.size());
   }
}
```

6. **Optional: Include Test in pom.xml:**

```xml
<!-- pom.xml -->
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <version>3.0.0-M5</version>
            <configuration>
                <includes>
                    <include>**/UserServiceTest.java</include>
                </includes>`
            </configuration>`
        </plugin>`
`    `</plugins>`
`</build>`
```

These steps provide a basic structure for setting up DAOs, Services, and tests in an older Spring project using XML configuration. Adjust package names, file paths, and other details to match your specific project structure.