<properties
	pageTitle="Tutorial do Node.js NoSQL para o Banco de Dados de Documentos | Microsoft Azure"
	description="Um tutorial do Node.js NoSQL que cria um banco de dados do nó e um aplicativo de console usando o SDK do Node.js do Banco de Dados de Documentos. O Banco de Dados de Documentos é um banco de dados NoSQL para JSON."
    keywords="tutorial do node.js, banco de dados do nó"
	services="documentdb"
	documentationCenter="node.js"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="node"
	ms.topic="hero-article" 
	ms.date="03/30/2016"
	ms.author="anhoh"/>

# Tutorial do Node.js NoSQL: aplicativo de console Node.js do Banco de Dados de Documentos  

> [AZURE.SELECTOR]
- [.NET](documentdb-get-started.md)
- [Node.js](documentdb-nodejs-get-started.md)

Bem-vindo ao tutorial do Node.js para o SDK do Node.js do Banco de Dados de Documentos! Após seguir este tutorial, você terá um aplicativo de console que cria e consulta recursos do Banco de Dados de Documentos, incluindo um banco de dados do Nó.

Abordaremos:

- Criando e se conectando a uma conta do Banco de Dados de Documentos
- Configurando o aplicativo
- Criando um banco de dados do nó
- Criar uma coleção
- Criando documentos JSON
- Consultar a coleção
- Excluindo o banco de dados do nó

Você não tem tempo? Não se preocupe! A solução completa está disponível em [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started). Consulte [Obter a solução completa](#GetSolution) para as instruções rápidas.

Depois de concluir o tutorial do Node.js, use os botões de votação na parte superior e inferior desta página para nos enviar comentários. Se quiser que entremos em contato com você diretamente, inclua seu endereço de email em seu comentário.

Agora vamos começar!

## Pré-requisitos para o tutorial do Node.js

Certifique-se que você tem o seguinte:

- Uma conta ativa do Azure. Se não tiver uma, você poderá se inscrever em uma [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
- [Node.js](https://nodejs.org/) versão v0.10.29 ou superior.

## Etapa 1: Criar uma conta do Banco de Dados de Documentos

Vamos criar uma conta de Banco de Dados de Documentos. Se você já tiver uma conta que deseja usar, poderá pular para [Configurar seu aplicativo Node.js](#SetupNode).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupNode"></a> Etapa 2: Configurar o aplicativo Node.js

1. Abra seu terminal favorito.
2. Localize a pasta ou o diretório em que você deseja salvar o aplicativo do Node.js.
3. Crie dois arquivos JavaScript vazios com os seguintes comandos:
	- Windows:    
	    * ```fsutil file createnew app.js 0```
        * ```fsutil file createnew config.js 0```
	- Linux/OS X: 
	    * ```touch app.js```
        * ```touch config.js```
4. Instale o módulo documentdb via npm. Use o seguinte comando:
    * ```npm install documentdb --save```

Ótimo! Agora que você terminou a configuração, vamos começar a escrever o código.

##<a id="Config"></a> Etapa 3: Definir as configurações do aplicativo

Abra ```config.js``` no editor de texto de sua preferência.

Em seguida, crie um objeto vazio chamado ```config``` e defina as propriedades ```config.endpoint``` e ```config.authKey``` para o ponto de extremidade e a chave de autorização do Banco de Dados de Documentos. Ambas as configurações podem ser encontradas no [Portal do Azure](https://portal.azure.com).

![Tutorial do Node.js ‒ captura de tela do Portal do Azure mostrando uma conta do Banco de Dados de Documentos com o hub ATIVO realçado, o botão CHAVES realçado na folha da conta do Banco de Dados de Documentos e os valores de URI, de CHAVE PRIMÁRIA e de CHAVE SECUNDÁRIA realçados na folha Chaves ‒ banco de dados do Nó][keys]

    var config = {}

    config.endpoint = "https://YOUR_ENDPOINT_URI.documents.azure.com:443/";
    config.authKey = "oqTveZeWlbtnJQ2yMj23HOAViIr0ya****YOUR_AUTHORIZATION_KEY****ysadfbUV+wUdxwDAZlCfcVzWp0PQg==";

Agora vamos adicionar ```database id```, ```collection id``` e ```JSON documents``` ao objeto ```config```. Abaixo de onde você configurar as propriedades ```config.endpoint``` e ```config.authKey```, adicione o código a seguir. Se já tiver dados que gostaria de armazenar em seu banco de dados, você pode usar a[ferramenta de Migração de Dados](documentdb-import-data.md) do Banco de Dados de Documentos em vez de adicionar as definições do documento.

    config.database = {
        "id": "FamilyDB"
    };

    config.collection = {
        "id": "FamilyColl",
        "partitionKey": { "paths": ["/district"], "kind": "Hash" }
    };

    config.documents = {
        "Andersen": {
            "id": "Anderson.1",
            "lastName": "Andersen",
            "district": "WA5",
            "parents": [{
                "firstName": "Thomas"
            }, {
                    "firstName": "Mary Kay"
                }],
            "children": [{
                "firstName": "Henriette Thaulow",
                "gender": "female",
                "grade": 5,
                "pets": [{
                    "givenName": "Fluffy"
                }]
            }],
            "address": {
                "state": "WA",
                "county": "King",
                "city": "Seattle"
            }
        },
        "Wakefield": {
            "id": "Wakefield.7",
            "district": "NY23",
            "parents": [{
                "familyName": "Wakefield",
                "firstName": "Robin"
            }, {
                    "familyName": "Miller",
                    "firstName": "Ben"
                }],
            "children": [{
                "familyName": "Merriam",
                "firstName": "Jesse",
                "gender": "female",
                "grade": 8,
                "pets": [{
                    "givenName": "Goofy"
                }, {
                        "givenName": "Shadow"
                    }]
            }, {
                    "familyName": "Miller",
                    "firstName": "Lisa",
                    "gender": "female",
                    "grade": 1
                }],
            "address": {
                "state": "NY",
                "county": "Manhattan",
                "city": "NY"
            },
            "isRegistered": false
        }
    };


As definições de banco de dados, de coleção e de documento atuarão como a ```database id``` e a ```collection id``` do Banco de Dados de Documentos e como os dados dos documentos.

Por fim, exporte o objeto ```config```, para que você possa fazer referência a ele no arquivo ```app.js```.

    module.exports = config;

##<a id="Connect"></a> Etapa 4: conectar-se a uma conta do Banco de Dados de Documentos

Abra o arquivo ```app.js``` vazio no editor de texto. Importe o módulo ```documentdb``` e o módulo ```config``` recém-criado.

    "use strict";

    var documentClient = require("documentdb").DocumentClient;
    var config = require("./config");
    var url = require('url');

Em seguida, usaremos o ```config.endpoint``` e a ```config.authKey``` salvos anteriormente para criar um novo DocumentClient.

    var client = new documentClient(config.endpoint, { "masterKey": config.authKey });

Agora que você se conectou a uma conta do Banco de Dados de Documentos, vamos conferir o trabalho com recursos do Banco de Dados de Documentos.

## Etapa 5: criar um banco de dados do Nó
Um [banco de dados](documentdb-resources.md#databases) pode ser criado usando a função [createDatabase](https://azure.github.io/azure-documentdb-node/DocumentClient.html) da classe **DocumentClient**. Um banco de dados é o contêiner lógico de armazenamento de documentos particionado em coleções. Adicione uma função para criar um novo banco de dados no arquivo app.js com a ```id``` especificada no objeto ```config```. Primeiro, vamos verificar a existência de um banco de dados com a mesma ID ```FamilyRegistry```. Se existir, vamos retornar esse banco de dados, em vez de criar um novo.

    var HttpStatusCodes = { NOTFOUND: 404 };
    var databaseUrl = `dbs/${config.database.id}`;
    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

    /**
     * Get the database by ID, or create if it doesn't exist.
     * @param {string} database - The database to get or create
     */
    function getDatabase() {
        console.log(`Getting database:\n${config.database.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDatabase(databaseUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDatabase(config.database, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }
##<a id="CreateColl"></a>Etapa 6: Criar uma coleção  

> [AZURE.WARNING] **CreateDocumentCollectionAsync** criará uma nova coleção, que tem implicações de preços. Para obter mais detalhes, visite a nossa [página de preços](https://azure.microsoft.com/pricing/details/documentdb/).

Uma [coleção](documentdb-resources.md#collections) pode ser criada usando a função [createCollection](https://azure.github.io/azure-documentdb-node/DocumentClient.html) da classe **DocumentClient**. Uma coleção é um contêiner de documentos JSON e lógica de aplicativo JavaScript associada. Adicione uma função para criar a nova coleção no arquivo app.js com o ```id``` especificado no objeto ```config```. Novamente, verificaremos se uma coleção com a mesma id ```FamilyCollection``` já não existe. Se existir, vamos retornar essa coleção, em vez de criar uma nova.

    /**
     * Get the collection by ID, or create if it doesn't exist.
     */
    function getCollection() {
        console.log(`Getting collection:\n${collection.id}\n`);

        return new Promise((resolve, reject) => {
            client.readCollection(collectionUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createCollection(databaseUrl, config.collection, { offerThroughput: 400 }, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

##<a id="CreateDoc"></a>Etapa 7: Criar um documento
Um [documento](documentdb-resources.md#documents) pode ser criado usando a função [createDocument](https://azure.github.io/azure-documentdb-node/DocumentClient.html) da classe **DocumentClient**. Os documentos são conteúdo JSON (arbitrário) definido pelo usuário. Agora você pode inserir um documento no Banco de Dados de Documentos.

Em seguida, adicione uma função a app.js para a criação de documentos contendo os dados JSON salvos no objeto ```config```. Novamente, vamos verificar se um documento com a mesma ID ainda não existe.

    /**
     * Get the document by ID, or create if it doesn't exist.
     * @param {function} callback - The callback function on completion
     */
    function getFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Getting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDocument(documentUrl, { partitionKey: document.district }, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDocument(collectionUrl, document, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    };

Parabéns! Agora você tem funções para criar um banco de dados, uma coleção e o documento no Banco de Dados de Documentos!

![Tutorial do Node.js ‒ diagrama que ilustra a relação hierárquica entre a conta, o banco de dados, a coleção e os documentos ‒ banco de dados do Nó](./media/documentdb-nodejs-get-started/node-js-tutorial-account-database.png)

##<a id="Query"></a>Etapa 8: consultar recursos do Banco de Dados de Documentos

O Banco de Dados de Documentos tem suporte para [consultas](documentdb-sql-query.md) avançadas de documentos JSON armazenados em cada coleção. O código de exemplo a seguir mostra uma consulta que pode ser executada em documentos em sua coleção. Adicione a função a seguir ao arquivo ```app.js```. O Banco de Dados de Documentos dá suporte a consultas do tipo SQL, conforme mostrado abaixo. Para saber mais sobre como criar consultas complexas, consulte o [Espaço de Consulta](https://www.documentdb.com/sql/demo) e a [documentação de consulta](documentdb-sql-query.md).

    /**
     * Query the collection using SQL
     */
    function queryCollection() {
        console.log(`Querying collection through index:\n${config.collection.id}\n`);

        return new Promise((resolve, reject) => {
            client.queryDocuments(
                collectionUrl,
                'SELECT VALUE r.address.city FROM root r WHERE r.lastName = "Andersen"',
                { enableCrossPartitionQuery: true }
            ).toArray((err, results) => {
                if (err) reject(err)
                else {
                    for (var queryResult of results) {
                        console.log(`Query returned ${queryResult}`);
                    }
                    resolve(results);
                }
            });
        });
    };


O diagrama a seguir ilustra como a sintaxe de consulta do SQL do Banco de Dados de Documentos é chamada em relação à coleção que você criou.

![Tutorial do Node.js ‒ Diagrama que ilustra o escopo e o significado da consulta ‒ banco de dados do Nó](./media/documentdb-nodejs-get-started/node-js-tutorial-collection-documents.png)

A palavra-chave [FROM](documentdb-sql-query.md/#from-clause) é opcional na consulta, pois as consultas do Banco de Dados de Documentos já têm o escopo para uma única coleção. Portanto, "FROM Families f" pode ser trocado por "FROM root r" ou qualquer outra variável de nome que você escolher. O Banco de Dados de Documentos fará com que Families, root ou o nome de variável escolhido por você faça referência à coleção atual, por padrão.

##<a id="DeleteDatabase"></a>Etapa 9: excluir o banco de dados do nó

Excluir o banco de dados criado removerá o banco de dados e todos os recursos filhos (coleções, documentos, etc.). Você pode excluir o banco de dados adicionando o trecho de código a seguir.

    /**
     * Cleanup the database and collection on completion
     */
    function cleanup() {
        console.log(`Cleaning up by deleting database ${config.database.id}`);

        return new Promise((resolve, reject) => {
            client.deleteDatabase(databaseUrl, (err) => {
                if (err) reject(err)
                else resolve(null);
            });
        });
    }

##<a id="Build"></a>Etapa 10: Juntar tudo

Agora que você definiu todas as funções necessárias para seu aplicativo, vamos chamá-las!

Adicione o trecho de código a seguir à parte inferior do seu código em ```app.js```.

    /**
     * Exit the app with a prompt
     * @param {message} message - The message to display
     */
    function exit(message) {
        console.log(message);
        console.log('Press any key to exit');
        process.stdin.setRawMode(true);
        process.stdin.resume();
        process.stdin.on('data', process.exit.bind(process, 0));
    }

    getDatabase()
        .then(() => getCollection())
        .then(() => getFamilyDocument(config.documents.Andersen))
        .then(() => getFamilyDocument(config.documents.Wakefield))
        .then(() => queryCollection())
        .then(() => cleanup())
        .then(() => { exit(`Completed successfully`); })
        .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

##<a id="Run"></a> Etapa 11: Executar o aplicativo Node.js

Agora você está pronto para executar o aplicativo Node.js.

No terminal, localize o arquivo ```app.js``` e execute o comando: ```node app.js```

Você deverá ver a saída do aplicativo iniciado. A saída deve corresponder ao texto de exemplo abaixo.

    Getting database:
    FamilyDB

    Getting collection:
    FamilyColl

    Getting document:
    Anderson.1

    Getting document:
    Wakefield.7

    Querying collection through index:
    FamilyColl
      Query returned Seattle

    Cleaning up by deleting database FamilyDB

    Completed successfully
    Press any key to exit

Parabéns! Você concluiu o tutorial do Node.js e tem seu primeiro aplicativo de console do Banco de Dados de Documentos!

##<a id="GetSolution"></a> Obter a solução do tutorial do Node.js completa
Para criar a solução de Introdução que contém todos os exemplos neste artigo, você precisará do seguinte:

-   [Conta de Banco de Dados de Documentos][documentdb-create-account].
-   A solução [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started) disponível no GitHub.

Instale o módulo **documentdb** via npm. Use o seguinte comando:
* ```npm install documentdb --save```

Em seguida, no arquivo ```config.js```, atualize os valores de config.endpoint e config.authKey, como descrito na [Etapa 3: definir configurações do aplicativo](#Config).

## Próximas etapas

-   Deseja obter um exemplo mais complexo do Node.js? Consulte [Criar um aplicativo Web do Node.js usando o Banco de Dados de Documentos](documentdb-nodejs-application.md).
-	Saiba como [monitorar uma conta do Banco de Dados de Documentos](documentdb-monitor-accounts.md).
-	Executar consultas em nosso conjunto de dados de exemplo no [Query Playground](https://www.documentdb.com/sql/demo).
-	Saiba mais sobre o modelo de programação na seção Desenvolvimento da [Página de documentação do Banco de Dados de Documentos](../../services/documentdb/).

[doc-landing-page]: ../../services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-nodejs-get-started/node-js-tutorial-keys.png

<!---HONumber=AcomDC_0330_2016-->