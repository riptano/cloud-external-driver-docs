Connecting to your database with the DataStax Node.js driver
============================================================

Use the unified DataStax Node.js driver to connect to your Astra database and begin building your own application.

Prerequisites
-------------

<a href="https://nodejs.org/en/download/" target="_blank">Download</a> and install a current Node.js LTS version with `npm`.

Procedure
---------

1.  Install the DataStax Node.js driver:

    ```javascript
    npm install cassandra-driver
    ```

2.  Create a `connect-database.js` file in the main directory for your Node.js project.

    ```bash
    cd nodejsProject
    ```

    ```bash
    touch connect-database.js
    ```

3.  Copy the following connection code into the `connect-database.js` file.

    **Note**: Include the absolute path to the secure connect bundle for your Cassandra database (`secure-connect-database_name.zip`) in the `secureConnectBundle` parameter, as shown in the following examples.

    ```javascript
    'use strict'

    const { Client } = require('cassandra-driver');
    ```

4.  After the `Client` class, add the following code to `connect-database.js`. This code creates a `Client` instance to connect to your Astra database, runs a CQL query, and prints the output to the console.

    ```javascript
    async function run() {
      const client = new Client({
        cloud: { secureConnectBundle: 'path/to/secure-connect-database_name.zip' },
        credentials: { username: 'username', password: 'password' }
      });

      await client.connect();

      // Execute a query
      const rs = await client.execute('SELECT * FROM system.local');
      console.log(`Hello from cluster: ${rs.first()['cluster_name']}`);

      await client.shutdown();
    }

    // Run the async function
    run();
    ```

5.  Save and close the `connect-database.js` file.
6.  Run the `connect-database.js` example with the Node.js runtime:

    ```javascript
    node connect-database.js
    ```

    The console output displays the `cluster_name` value from the `system.local` table.
