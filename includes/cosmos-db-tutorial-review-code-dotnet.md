Façamos uma rápida análise do que está acontecendo no aplicativo. Abra o arquivo DocumentDBRepository.cs e você verá que essas linhas de código criam os recursos do DocumentDB. 

* O DocumentClient é inicializado.

    ```csharp
    client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);`
    ```

* Um novo banco de dados é criado.

    ```csharp
    await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
    ```

* Uma nova coleção é criada.

    ```csharp
    await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri(DatabaseId),
        new DocumentCollection { Id = CollectionId },
        new RequestOptions { OfferThroughput = 1000 });
    ```
