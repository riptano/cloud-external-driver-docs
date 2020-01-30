Connecting to your database with the C# driver
==============================================

Use the unified DataStax C# driver to connect to your Apollo database and begin building your own application.

Prerequisites
-------------

[Download](https://dotnet.microsoft.com/download/dotnet-core) and [install](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-install-script) the current version of the .NET Core SDK.


Procedure
---------

1.  Create a new C# project and configure it to connect to your Cassandra database.

    ```bash
    mkdir csharpproject
    ```

    ```bash
    cd csharpproject
    ```

    ```csharp
    dotnet new console
    ```

    -   Add the dependencies for the C# driver to your project.

        ```csharp
        dotnet add package CassandraCSharpDriver -v 3.13.0
        ```

    -   Replace the code in Program.cs with the following code to connect to your Apollo database.

        **Note**: Include the absolute path to the secure connect bundle for your Apollo database (`secure-connect-database_name.zip`) in the `WithCloudSecureConnectionBundle` method call, and your credentials in the `WithCredentials` method call, as shown in the following examples.

        ```csharp
        using System;
        using System.Linq;
        using Cassandra;

        namespace csharpproject
        {
            class Program
            {
                static void Main(string[] args)
                {
                    var session =
                        Cluster.Builder()
                               .WithCloudSecureConnectionBundle(@"C:\path\to\secure-connect-database_name.zip")
                               .WithCredentials("username", "password")
                               .Build()
                               .Connect();
                }
            }
        }
        ```

    -   After the connection code, add the following code to the `Main` method in `Program.cs`. This code runs a CQL query, and prints the output to the console.

        ```csharp
        var rowSet = session.Execute("select * from system.local");
        Console.WriteLine(rowSet.First().GetValue<string>("cluster_name"));
        ```

2.  Run your C# project with the `dotnet` runtime.

    ```csharp
    dotnet restore
    ```

    ```csharp
    dotnet build
    ```

    ```csharp
    dotnet run --no-build
    ```
