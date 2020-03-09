Connecting to your database with the DataStax C++ driver
========================================================

Use the unified DataStax C++ driver to connect to your Astra database and begin building your own application.

Prerequisites
-------------

1.  Download the DataStax C++ driver and dependency packages for your platform using the links in the following table.

    Table 1. DataStax C++ driver download links

    | Platform     | Download links                                                                                                                                                       |
    |--------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | CentOS 6     | <a href="https://downloads.datastax.com/cpp-driver//centos/6/cassandra/" target="_blank">Driver</a> | <a href="https://downloads.datastax.com/cpp-driver//centos/6/dependencies/" target="_blank">Dependencies</a> |
    | CentOS 7     | <a href="https://downloads.datastax.com/cpp-driver//centos/7/cassandra/" target="_blank">Driver</a> | <a href="https://downloads.datastax.com/cpp-driver//centos/7/dependencies/" target="_blank">Dependencies</a> |
    | Ubuntu 14.04 | <a href="https://downloads.datastax.com/cpp-driver//ubuntu/14.04/cassandra/" target="_blank">Driver</a> | <a href="https://downloads.datastax.com/cpp-driver//ubuntu/14.04/dependencies/" target="_blank">Dependencies</a> |
    | Ubuntu 16.04 | <a href="https://downloads.datastax.com/cpp-driver//ubuntu/16.04/cassandra/" target="_blank">Driver</a> | <a href="https://downloads.datastax.com/cpp-driver//ubuntu/16.04/dependencies/" target="_blank">Dependencies</a> |
    | Ubuntu 18.04 | <a href="https://downloads.datastax.com/cpp-driver//ubuntu/18.04/cassandra/" target="_blank">Driver</a> | <a href="https://downloads.datastax.com/cpp-driver//ubuntu/18.04/dependencies/" target="_blank">Dependencies</a> |
    | Windows      | <a href="https://downloads.datastax.com/cpp-driver//windows/cassandra/" target="_blank">Driver</a> | <a href="https://downloads.datastax.com/cpp-driver//windows/dependencies/" target="_blank">Dependencies</a> |
    | Other platforms | Other platforms should build the driver from the <a href="https://github.com/datastax/cpp-driver" target="_blank">source code</a> |  |

2.  Using the packages you downloaded, follow the <a href="https://docs.datastax.com/en/developer/cpp-driver/latest/topics/installation/" target="_blank">installation instructions</a> for your platform to install the DataStax C++ driver.

Procedure
---------

1.  Create a `connect_database.c` file in the main directory for your C++ project.

    ```bash
    cd my_project
    ```

    ```bash
    touch connect_database.c
    ```

2.  Copy the following connection code into the `connect_database.c` file.

    **Note**: The `cass_cluster_set_contact_points()` and `cass_cluster_set_ssl()` methods should not used in conjunction with the `cass_cluster_set_cloud_secure_connection_bundle()` method.

    ```c++
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
3.  In the `connect_database.c` file, replace the `secure_connect_bundle` variable with the absolute path to your Astra database credentials (`secure-connect-database_name.zip`). You download this bundle **after** configuring the DataStax C++ Driver. 

3.  Build and link your application against the DataStax C++ driver.
    *   Linux or macOS

        **Note**: For static linking, use `cassandra_static.a`.

        ```c++
        cc connect-database.c -I/path/to/cassandra.h -L/path/to/cassandra.so -lcassandra
        ```

    *   Windows

        Include these libraries in your Microsoft Visual Studio project by adding them to the project’s properties under *Configuration Properties/Linker/Input/Additional Dependencies*.

        Note: For static linking, use `cassandra_static.lib`.

        Link your application against `cassandra.lib`. Your application will also require `cassandra.dll` to be in your runtime path.

4.  Click **Keep Going** to proceed in this workflow and download the secure connect bundle for your database.

4.  After connecting to your Astra database, use the following code to query your database.

    This code creates a `CassStatement` object to connect to your Astra database, runs a CQL query, and prints the output to the console.

    ```c++
    /* Build statement and execute query */
    const char* query = "SELECT release_version FROM system.local";
    CassStatement* statement = cass_statement_new(query, 0);

    CassFuture* result_future = cass_session_execute(session, statement);

    if (cass_future_error_code(result_future) == CASS_OK) {
      /* Retrieve result set and get the first row */
      const CassResult* result = cass_future_get_result(result_future);
      const CassRow* row = cass_result_first_row(result);

      if (row) {
        const CassValue* value = cass_row_get_column_by_name(row, "release_version");

        const char* release_version;
        size_t release_version_length;
        cass_value_get_string(value, &release_version, &release_version_length);
        printf("release_version: '%.*s'\n", (int)release_version_length, release_version);
      }

     cass_result_free(result);
     } else {
      /* Handle error */
      const char* message;
      size_t message_length;
      cass_future_error_message(result_future, &message, &message_length);
      fprintf(stderr, "Unable to run query: '%.*s'\n", (int)message_length, message);
     }

    cass_statement_free(statement);
    cass_future_free(result_future);
    ```
