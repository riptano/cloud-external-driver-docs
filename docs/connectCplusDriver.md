Connecting to your database with the DataStax C++ driver
========================================================

Use the unified DataStax C++ driver to connect to your Apollo database and begin building your own application.

Prerequisites
-------------

1.  [Download the secure connect bundle](dscloudObtainingCredentials.html "Download the connection credentials for your Apollo database.") to obtain connection credentials for your DataStax Apollo database.

    Tip: Alternatively, [have a teammate provide access to their Apollo database](dscloudShareClusterDetails.html "Provide other members of your team with access to an Apollo database.").

2.  Download the DataStax C++ driver and dependency packages for your platform using the links in the following table.

    Table 1. DataStax C++ driver download links

    | Platform     | Download links                                                                                                                                                       |
    |--------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | CentOS 6     | [Driver](https://downloads.datastax.com/cpp-driver//centos/6/cassandra/) | [Dependencies](https://downloads.datastax.com/cpp-driver//centos/6/dependencies/)         |
    | CentOS 7     | [Driver](https://downloads.datastax.com/cpp-driver//centos/7/cassandra/) | [Dependencies](https://downloads.datastax.com/cpp-driver//centos/7/dependencies/)         |
    | Ubuntu 14.04 | [Driver](https://downloads.datastax.com/cpp-driver//ubuntu/14.04/cassandra/) | [Dependencies](https://downloads.datastax.com/cpp-driver//ubuntu/14.04/dependencies/) |
    | Ubuntu 16.04 | [Driver](https://downloads.datastax.com/cpp-driver//ubuntu/16.04/cassandra/) | [Dependencies](https://downloads.datastax.com/cpp-driver//ubuntu/16.04/dependencies/) |
    | Ubuntu 18.04 | [Driver](https://downloads.datastax.com/cpp-driver//ubuntu/18.04/cassandra/) | [Dependencies](https://downloads.datastax.com/cpp-driver//ubuntu/18.04/dependencies/) |
    | Windows      | [Driver](https://downloads.datastax.com/cpp-driver//windows/cassandra/) | [Dependencies](https://downloads.datastax.com/cpp-driver//windows/dependencies/)           |
    | Other        | Other platforms should build the driver from the [source code packages](https://github.com/datastax/cpp-driver).                                                     |

3.  Using the packages you downloaded, follow the [installation instructions](/en/developer/cpp-driver/latest/topics/installation/) for your platform to install the DataStax C++ driver.

Procedure
---------

1.  Create a `connect_database.c` file in the main directory for your C++ project.

    ```
    cd my_project
    ```
    ```
    touch connect_database.c
    ```

2.  Copy the following connection code into the `connect_database.c` file.

    The `secure_connect_bundle` must include the absolute path to your Apollo database credentials (`secure-connect-database_name.zip`).

    **Note**: The `cass_cluster_set_contact_points()` and `cass_cluster_set_ssl()` methods should not used in conjunction with the `cass_cluster_set_cloud_secure_connection_bundle()` method.

    ```
    #include <cassandra.h>
    #include <stdio.h>

    int main(int argc, char* argv[]) {
        /* Setup and connect to cluster */
          CassCluster* cluster = cass_cluster_new();
          CassSession* session = cass_session_new();

          /* Setup driver to connect to the cloud using the secure connection bundle */
          const char* secure_connect_bundle = "/path/to/secure-connect-database_name.zip";
          if (cass_cluster_set_cloud_secure_connection_bundle(cluster, secure_connect_bundle) != CASS_OK) {
            fprintf(stderr, "Unable to configure cloud using the secure connection bundle: %s\n",
                       secure_connect_bundle);
            return 1;
          }

          /* Set credentials provided when creating your database */
          cass_cluster_set_credentials(cluster, "username", "password");

          CassFuture* connect_future = cass_session_connect(session, cluster);

          if (cass_future_error_code(connect_future) == CASS_OK) {
            /* Use the session to run queries */
          } else {
            /* Handle error */
          }

          cass_future_free(connect_future);
          cass_cluster_free(cluster);
          cass_session_free(session);

          return 0;
        }
    ```

3.  Build and link your application against the DataStax C++ driver.
    *   Linux or macOS

        **Note**: For static linking, use `cassandra_static.a`.

        ```
        cc connect-database.c -I/path/to/cassandra.h -L/path/to/cassandra.so -lcassandra
        ```

    *   Windows

        Include these libraries in your Microsoft Visual Studio project by adding them to the project’s properties under *Configuration Properties/Linker/Input/Additional Dependencies*.

        Note: For static linking, use `cassandra_static.lib`.

        Link your application against `cassandra.lib`. Your application will also require `cassandra.dll` to be in your runtime path.

4.  After connecting to your Apollo database, use the following code to query your database.

    This code creates a `CassStatement` object to connect to your Apollo database, runs a CQL query, and prints the output to the console.

    ```
    /* Build statement and execute query /*
    const char\* query = "SELECT release\_version FROM system.local";
    CassStatement\* statement = cass\_statement\_new(query, 0);

    CassFuture\* result\_future = cass\_session\_execute(session, statement);

    if (cass\_future\_error\_code(result\_future) == CASS\_OK) {
      /\* Retrieve result set and get the first row \*/
      const CassResult\* result = cass\_future\_get\_result(result\_future);
      const CassRow\* row = cass\_result\_first\_row(result);

      if (row) {
        const CassValue\* value = cass\_row\_get\_column\_by\_name(row, "release\_version");

        const char\* release\_version;
        size\_t release\_version\_length;
        cass\_value\_get\_string(value, &release\_version, &release\_version\_length);
        printf("release\_version: '%.\*s'\\n", (int)release\_version\_length, release\_version);
      }

     cass\_result\_free(result);
     } else {
      /\* Handle error \*/
      const char\* message;
      size\_t message\_length;
      cass\_future\_error\_message(result\_future, &message, &message\_length);
      fprintf(stderr, "Unable to run query: '%.\*s'\\n", (int)message\_length, message);
     }

    cass\_statement\_free(statement);
    cass\_future\_free(result\_future);
    ```

What's next
-----------

Build your application. See the [DataStax C++ driver](https://docs.datastax.com/en/developer/cpp-driver-dse/1.10/features/cloud/) documentation for more information about using the DataStax C++ driver.
