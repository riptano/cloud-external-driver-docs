Connecting to your database with the DataStax Java driver
=========================================================

Use the DataStax Java driver to connect to your database created using the DataStax Cloud console.

Use the unified DataStax Java driver to connect to your Apollo database and begin building your own application.

You add a repository and dependencies to the pom.xml file for your project to download the appropriate .jar files for the Java driver and make them available to your code. Additionally, you implement a `ConnectDatabase` class to initialize the DSE Java driver.

Prerequisites
-------------

[Download](https://maven.apache.org/download.cgi) and [install](https://maven.apache.org/install.html) Maven.


Procedure
---------

1.  Navigate to the `pom.xml` file at the root of your Java project and open it for editing.

    Tip: For a complete pom.xml file, see the [Example pom.xml file](#example-pom.xml-file).

  *  Add the DataStax Java driver dependency to your pom.xml file, ensuring that the name of the dependency corresponds to the installed version:

     This dependency causes Maven to automatically download the appropriate .jar files found at the `url` specified in the `repository` for the DSE Java driver and make them available to your code.

      ```
      <dependency>
          <groupId>com.datastax.oss</groupId>
          <artifactId>java-driver-core</artifactId>
          <version>4.4.0</version>
      </dependency>
      ```

  * Save and close your `pom.xml` file.

2.  Initialize the DataStax Java driver.
  *  Create a ConnectDatabase.java file in the `/src/main/java` directory for your Java project.

        `cd javaProject/src/main/java`

        `touch ConnectDatabase.java`

  *  Copy the following code for your DataStax driver into the ConnectDatabase.java file.

    The following example implements a `ConnectDatabase` class to connect to your Apollo database, runs a CQL query, and prints the output to the console.

    Make the following changes:

     *   Use the `withCloudSecureConnectBundle()` method to specify the path to the secure connect bundle for your Apollo database.
     *   Use the `withAuthCredentials()` method to specify the username and password for your database.
     *   Use the `withKeyspace()` method to specify the keyspace name for your database.

  ```javascript
      import com.datastax.oss.driver.api.core.CqlSession;
      import com.datastax.oss.driver.api.core.cql.ResultSet;
      import com.datastax.oss.driver.api.core.cql.Row;

      public class ConnectDatabase {

          public static void main(String[] args) {
             // Create the CqlSession object:
            try (CqlSession session = CqlSession.builder()
                .withCloudSecureConnectBundle(Paths.get("/path/to/secure-connect-database_name.zip"))
                .withAuthCredentials("username","password")
                .withKeyspace("keyspace_name")
                .build()) {
                // Select the release_version from the system.local table:
                ResultSet rs = session.execute("select release_version from system.local");
                Row row = rs.one();
                //Print the results of the CQL query to the console:
                if (row != null) {
                    System.out.println(row.getString("release_version"));
                } else {
                    System.out.println("An error occurred.");
                }
            }
            System.exit(0);
       }
    }
   ```

3.  Save and close the ConnectDatabase.java file.

Example pom.xml file
--------------------

You can use the following pom.xml file in your Java project to connect to your Apollo database. If you already have a pom.xml file for your Java project, copy only the repository and dependencies as indicated in the previous steps.

   ```javascript
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
        <groupId>net.techne.web</groupId>
        <artifactId>cloudTest</artifactId>
        <version>1.0</version>
        <packaging>jar</packaging>
        <properties>
            <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
            <maven.compiler.source>1.8</maven.compiler.source>
            <maven.compiler.target>1.8</maven.compiler.target>
        </properties>
        <dependencies>
            <!-- START-javaDriverDependencyCore -->
            <dependency>
                <groupId>com.datastax.dse</groupId>
                <artifactId>dse-java-driver-core</artifactId>
                <version>2.3.0</version>
            </dependency>
            <!-- END-javaDriverDependencyCore -->
            <!-- START-javaDriverDependencyQuery -->
            <dependency>
                <groupId>com.datastax.dse</groupId>
                <artifactId>dse-java-driver-query-builder</artifactId>
                <version>2.3.0</version>
            </dependency>
            <!-- END-javaDriverDependencyQuery -->
        </dependencies>
    </project>
   ```

What's next
-----------

Build your application. See the [DataStax Java driver](/en/developer/java-driver/latest/) documentation for more information about using the DataStax Java driver.
