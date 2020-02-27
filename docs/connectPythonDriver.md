Connecting to your database with the DataStax Python driver
===========================================================

Use the unified DataStax Python driver to connect to your Astra database and begin building your own application.

Prerequisites
-------------

Download and install a supported Python version. Python 2.7, 3.4, 3.5 and 3.6 are supported.

**Note**: <a href="https://www.python.org/" target="_blank">CPython</a> and <a href="http://pypy.org/" target="_blank">PyPy</a> are supported and tested.

Procedure
---------

1.  Install the DataStax Python driver.

    ```python
    pip install cassandra-driver
    ```

    See the <a href="https://docs.datastax.com/en/developer/python-dse-driver/latest/installation/" target="_blank">Python driver installation instructions</a> for more information.

2.  Verify that the DataStax Python driver installed successfully:

    ```python
    python -c 'import cassandra; print cassandra.__version__'
    ```

    The version number displays in the console output:

    ```python
    3.20.0
    ```

3.  Create a `connect_database.py` file in the main directory for your Python project.

    ```bash
    cd python_project
    ```

    ```bash
    touch connect_database.py
    ```

4.  Copy the following connection code into the `connect_database.py` file.

    **Note**: Set the `cloud_config` parameter for the `Cluster` initialization as shown in the following example. The `secure_connect_bundle` must include the absolute path to your Astra database credentials (`secure-connect-database_name.zip`).

    ```python
    from cassandra.cluster import Cluster
    from cassandra.auth import PlainTextAuthProvider

    cloud_config= {
            'secure_connect_bundle': '/path/to/secure-connect-database_name.zip'
    }
    auth_provider = PlainTextAuthProvider('username', 'password')
    cluster = Cluster(cloud=cloud_config, auth_provider=auth_provider)
    session = cluster.connect()
    ```

5.  After the connection code, add the following code to `connect-database.py`. This code creates a `Cluster` instance to connect to your Astra database, runs a CQL query, and prints the output to the console. As the name suggests, you will typically have one instance of <a href="https://docs.datastax.com/en/developer/python-dse-driver/2.11/api/dse/cluster/#dse.cluster.Cluster" target="_blank">Cluster</a> for each Cassandra cluster you want to interact with.

    ```
    row = session.execute("select release_version from system.local").one()
    if row:
        print(row[0])
    else:
        print("An error occurred.")
    ```

6.  Save and close the `connect_database.py` file.
7.  Run `connect_database.py`.

    ```python
    python ./connect_database.py
    ```

    The console output displays the `release_version` from the `system.local` table:

    ```
    4.0.0.670
    ```
