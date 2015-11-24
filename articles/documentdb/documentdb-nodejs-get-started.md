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
	ms.date="11/18/2015"
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

## Pré-requisitos

Certifique-se que você tem o seguinte:

- Uma conta ativa do Azure. Se não tiver uma, você poderá se inscrever em uma [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).
- [Node.js](https://nodejs.org/) versão v0.10.29 ou superior.

## Etapa 1: Criar uma conta do Banco de Dados de Documentos

Vamos criar uma conta de Banco de Dados de Documentos. Se você já tiver uma conta que deseja usar, poderá pular para [Configurar seu aplicativo Node.js](#SetupNode).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupNode"></a> Etapa 2: Configurar o aplicativo Node.js

1. Abra seu terminal favorito.
2. Localize a pasta ou o diretório em que você deseja salvar o aplicativo do Node.js.
3. Crie dois arquivos JavaScript vazios com os seguintes comandos:
	- Windows:    
	    * **fsutil file createnew app.js 0**
        * **fsutil file createnew config.js 0**
	- Linux/OS X: 
	    * **toque em app.js**
        * **toque em config.js**
4. Instale o módulo documentdb via npm. Use o seguinte comando:
    * **npm install documentdb --save**

Ótimo! Agora que você terminou a configuração, vamos começar a escrever o código.

##<a id="Config"></a> Etapa 3: Definir as configurações do aplicativo

Abra *config.js* no editor de texto de sua preferência.

Em seguida, crie um objeto vazio chamado *config* e defina as propriedades *config.endpoint* e *config.authKey* para o ponto de extremidade e a chave de autorização do Banco de Dados de Documentos. Ambas as configurações podem ser encontradas no [Portal de Visualização do Azure](https://portal.azure.com).

![Captura de tela da visualização do Portal do Azure mostrando uma conta do Banco de Dados de Documentos com o hub ATIVO realçado, o botão CHAVES realçado na folha da conta do Banco de Dados de Documentos e os valores de URI, de CHAVE PRIMÁRIA e de CHAVE SECUNDÁRIA realçados na folha Chaves][keys]

    var config = {}

    config.endpoint = "https://YOUR_ENDPOINT_URI.documents.azure.com:443/";
    config.authKey = "oqTveZeWlbtnJQ2yMj23HOAViIr0ya****YOUR_AUTHORIZATION_KEY****ysadfbUV+wUdxwDAZlCfcVzWp0PQg==";

Agora vamos adicionar a *ID de banco de dados*, a *ID da coleção* e *documentos JSON* para o objeto *config*. Abaixo de onde você define as propriedades *config.endpoint* e *config.authKey*, adicione o código a seguir. Se já tiver dados que gostaria de armazenar em seu banco de dados, você pode usar a[ferramenta de Migração de Dados](documentdb-import-data.md) do Banco de Dados de Documentos em vez de adicionar as definições do documento.

    config.dbDefinition = {"id": "FamilyRegistry"};
    config.collDefinition = {"id": "FamilyCollection"};

    var documents = {
      "Andersen": {
        "id": "AndersenFamily",
        "LastName": "Andersen",
        "Parents": [
          {
            "FirstName": "Thomas"
          },
          {
            "FirstName": "Mary Kay"
          }
        ],
        "Children": [
          {
            "FirstName": "Henriette Thaulow",
            "Gender": "female",
            "Grade": 5,
            "Pets": [
              {
                "GivenName": "Fluffy"
              }
            ]
          }
        ],
        "Address": {
          "State": "WA",
          "County": "King",
          "City": "Seattle"
        }
      },
      "Wakefield":   {
          "id": "WakefieldFamily",
          "Parents": [
            {
              "FamilyName": "Wakefield",
              "FirstName": "Robin"
            },
            {
              "FamilyName": "Miller",
              "FirstName": "Ben"
            }
          ],
          "Children": [
            {
              "FamilyName": "Merriam",
              "FirstName": "Jesse",
              "Gender": "female",
              "Grade": 8,
              "Pets": [
                {
                  "GivenName": "Goofy"
                },
                {
                  "GivenName": "Shadow"
                }
              ]
            },
            {
              "FamilyName": "Miller",
              "FirstName": "Lisa",
              "Gender": "female",
              "Grade": 1
            }
          ],
          "Address": {
            "State": "NY",
            "County": "Manhattan",
            "City": "NY"
          },
          "IsRegistered": false
        }
    };

    config.docsDefinitions = documents;

As definições de banco de dados, coleção e documento atuarão como a *ID de banco de dados*, *ID da coleção* e dados de documentos do Banco de Dados de Documentos.

Finalmente, exporte o objeto *config*, para que você possa fazer referência a ele no arquivo *app.js*.

    module.exports = config;

##<a id="Connect"></a> Etapa 4: Conectar-se a uma conta do Banco de Dados de Documentos

Abra o arquivo *app.js* vazio no editor de texto. Importe o módulo *documentdb* e o módulo *config* recém-criado.

    var documentClient = require("documentdb").DocumentClient;
    var config = require("./config");

Em seguida, vamos usar *config.endpoint* e *config.authKey*, salvos anteriormente, para criar um novo DocumentClient.

    var client = new documentClient(config.endpoint, {"masterKey": config.authKey});

Agora que você se conectou a uma conta do Banco de Dados de Documentos, vamos conferir o trabalho com recursos do Banco de Dados de Documentos.

## Etapa 5: Criar um banco de dados do nó
Um [banco de dados](documentdb-resources.md#databases) pode ser criado usando a função [createDatabase](https://azure.github.io/azure-documentdb-node/DocumentClient.html) da classe **DocumentClient**. Um banco de dados é o contêiner lógico de armazenamento de documentos particionado em coleções. Adicione uma função para criar um novo banco de dados no arquivo app.js com a *id* especificada no objeto *config*. Vamos verificar primeiro se um banco de dados com a mesma ID *FamilyRegistry* ainda não existe. Se existir, vamos retornar esse banco de dados, em vez de criar um novo.

    var getOrCreateDatabase = function(callback) {
        var querySpec = {
            query: 'SELECT * FROM root r WHERE r.id=@id',
            parameters: [{
                name: '@id',
                value: config.dbDefinition.id
            }]
        };

        client.queryDatabases(querySpec).toArray(function(err, results) {
            if(err) return callback(err);

            if (results.length === 0) {
                client.createDatabase(config.dbDefinition, function(err, created) {
                    if(err) return callback(err);

                    callback(null, created);
                });
            }
            else {
                callback(null, results[0]);
            }
        });
    };

##<a id="CreateColl"></a>Etapa 6: Criar uma coleção  

> [AZURE.WARNING]**CreateDocumentCollectionAsync** criará uma nova coleção de S1, que tem implicações de preços. Para obter mais detalhes, visite a nossa [página de preços](https://azure.microsoft.com/pricing/details/documentdb/).

Uma [coleção](documentdb-resources.md#collections) pode ser criada usando a função [createCollection](https://azure.github.io/azure-documentdb-node/DocumentClient.html) da classe **DocumentClient**. Uma coleção é um contêiner de documentos JSON e uma lógica de aplicativo JavaScript associada. A coleção recém-criada será mapeada para um [nível de desempenho S1](documentdb-performance-levels.md). Adicione uma função para criar uma nova coleção no arquivo app.js com a *id* especificada no objeto *config*. Novamente, vamos verificar se uma coleção com a mesma ID *FamilyCollection* ainda não existe. Se existir, vamos retornar essa coleção, em vez de criar uma nova.

    var getOrCreateCollection = function(callback) {

        var querySpec = {
            query: 'SELECT * FROM root r WHERE r.id=@id',
            parameters: [{
                name: '@id',
                value: config.collDefinition.id
            }]
        };

        var dbUri = "dbs/" + config.dbDefinition.id;

        client.queryCollections(dbUri, querySpec).toArray(function(err, results) {
            if(err) return callback(err);

            if (results.length === 0) {
                client.createCollection(dbUri, config.collDefinition, function(err, created) {
                    if(err) return callback(err);
                    callback(null, created);
                });
            }
            else {
                callback(null, results[0]);
            }
        });
    };

##<a id="CreateDoc"></a>Etapa 7: Criar um documento
Um [documento](documentdb-resources.md#documents) pode ser criado usando a função [createDocument](https://azure.github.io/azure-documentdb-node/DocumentClient.html) da classe **DocumentClient**. Os documentos são conteúdo JSON (arbitrário) definido pelo usuário. Agora você pode inserir um documento no Banco de Dados de Documentos.

Em seguida, adicione uma função a app.js para a criação de documentos contendo os dados JSON salvos no objeto *config*. Novamente, vamos verificar se um documento com a mesma ID ainda não existe.

    var getOrCreateDocument = function(document, callback) {
        var querySpec = {
            query: 'SELECT * FROM root r WHERE r.id=@id',
            parameters: [{
                name: '@id',
                value: document.id
            }]
        };

        var collectionUri = "dbs/" + config.dbDefinition.id + "/colls/" + config.collDefinition.id;

        client.queryDocuments(collectionUri, querySpec).toArray(function(err, results) {
            if(err) return callback(err);

            if(results.length === 0) {
                client.createDocument(collectionUri, document, function(err, created) {
                    if(err) return callback(err);

                    callback(null, created);
                });
            } else {
                callback(null, results[0]);
            }
        });
    };

Parabéns! Agora você tem funções para criar um banco de dados, uma coleção e o documento no Banco de Dados de Documentos!

![Diagrama que ilustra a relação hierárquica entre a conta, o banco de dados, a coleção e os documentos](./media/documentdb-nodejs-get-started/node-js-tutorial-account-database.png)

##<a id="Query"></a>Etapa 8: Consultar recursos do Banco de Dados de Documentos

O Banco de Dados de Documentos tem suporte para [consultas](documentdb-sql-query.md) avançadas de documentos JSON armazenados em cada coleção. O código de exemplo a seguir mostra uma consulta que pode ser executada em documentos em sua coleção. Adicione a função a seguir ao arquivo *app.js*. O Banco de Dados de Documentos dá suporte a consultas do tipo SQL, conforme mostrado abaixo. Para saber mais sobre como criar consultas complexas, consulte o [Espaço de Consulta](https://www.documentdb.com/sql/demo) e a [documentação de consulta](documentdb-sql-query.md).

    var queryCollection = function(documentId, callback) {
      var querySpec = {
          query: 'SELECT * FROM root r WHERE r.id=@id',
          parameters: [{
              name: '@id',
              value: documentId
          }]
      };

      var collectionUri = "dbs/" + config.dbDefinition.id + "/colls/" + config.collDefinition.id;

      client.queryDocuments(collectionUri, querySpec).toArray(function(err, results) {
          if(err) return callback(err);

          callback(null, results);
      });
    };

O diagrama a seguir ilustra como a sintaxe de consulta do SQL do Banco de Dados de Documentos é chamada em relação à coleção que você criou.

![Diagrama que ilustra o escopo e o significado da consulta](./media/documentdb-nodejs-get-started/node-js-tutorial-collection-documents.png)

A palavra-chave [FROM](documentdb-sql-query.md/#from-clause) é opcional na consulta, pois as consultas do Banco de Dados de Documentos já têm o escopo para uma única coleção. Portanto, "FROM Families f" pode ser trocado por "FROM root r" ou qualquer outra variável de nome que você escolher. O Banco de Dados de Documentos fará com que Families, root ou o nome de variável escolhido por você faça referência à coleção atual, por padrão.

##<a id="DeleteDatabase"></a>Etapa 9: Excluir o banco de dados do nó

Excluir o banco de dados criado removerá o banco de dados e todos os recursos filhos (coleções, documentos, etc.). Você pode excluir o banco de dados adicionando o trecho de código a seguir.

    // WARNING: this function will delete your database and all its children resources: collections and documents
    function cleanup(callback) {
        client.deleteDatabase("dbs/" + config.dbDefinition.id, function(err) {
            if(err) return callback(err);

            callback(null);
        });
    }

##<a id="Build"></a>Etapa 10: Juntar tudo

Agora que você definiu todas as funções necessárias para seu aplicativo, vamos chamá-las!

A ordem das chamadas de função será * *getOrCreateDatabase* * *getOrCreateCollection* * *getOrCreateDocument* * *getOrCreateDocument* * *queryCollection* * *cleanup*

Adicione o trecho de código a seguir na parte inferior do seu código no *app.js*.

    getOrCreateDatabase(function(err, db) {
        if(err) return console.log(err);
        console.log('Read or created db:\n' + db.id + '\n');

        getOrCreateCollection(function(err, coll) {
            if(err) return console.log(err);
            console.log('Read or created collection:\n' + coll.id + '\n');

            getOrCreateDocument(config.docsDefinitions.Andersen, function(err, doc) {
                if(err) return console.log(err);
                console.log('Read or created document:\n' + doc.id + '\n');

                getOrCreateDocument(config.docsDefinitions.Wakefield, function(err, doc) {
                    if(err) return console.log(err);
                    console.log('Read or created document:\n' + doc.id + '\n');

                    queryCollection("AndersenFamily", function(err, results) {
                        if(err) return console.log(err);
                        console.log('Query results:\n' + JSON.stringify(results, null, '\t') + '\n');

                        cleanup(function(err) {
                            if(err) return console.log(err);
                            console.log('Done.');
                        });
                    });
                });
            });
        });
    });

##<a id="Run"></a> Etapa 11: Executar o aplicativo Node.js

Agora você está pronto para executar o aplicativo Node.js.

No terminal, localize o arquivo *app.js* e execute o comando: **node app.js**

Você deverá ver a saída do aplicativo iniciado. A saída deve corresponder ao texto de exemplo abaixo.

    Read or created db:
    FamilyRegistry

    Read or created collection:
    FamilyCollection

    Read or created document:
    AndersenFamily

    Read or created document:
    WakefieldFamily

    Query results:
    [
            {
                    "id": "AndersenFamily",
                    "LastName": "Andersen",
                    "Parents": [
                            {
                                    "FirstName": "Thomas"
                            },
                            {
                                    "FirstName": "Mary Kay"
                            }
                    ],
                    "Children": [
                            {
                                    "FirstName": "Henriette Thaulow",
                                    "Gender": "female",
                                    "Grade": 5,
                                    "Pets": [
                                            {
                                                    "GivenName": "Fluffy"
                                            }
                                    ]
                            }
                    ],
                    "Address": {
                            "State": "WA",
                            "County": "King",
                            "City": "Seattle"
                    },
                    "_rid": "tOErANjwoQcBAAAAAAAAAA==",
                    "_ts": 1444327141,
                    "_self": "dbs/tOErAA==/colls/tOErANjwoQc=/docs/tOErANjwoQcBAAAAAAAAAA==/",
                    "_etag": ""00001200-0000-0000-0000-5616aee50000"",
                    "_attachments": "attachments/"
            }
    ]

    Done.

Parabéns! Você concluiu o tutorial do Node.js e tem seu primeiro aplicativo de console do Banco de Dados de Documentos!

##<a id="GetSolution"></a> Obter a solução completa
Para criar a solução de Introdução que contém todos os exemplos neste artigo, você precisará do seguinte:

-   [Conta de Banco de Dados de Documentos][documentdb-create-account].
-   A solução [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started) disponível no GitHub.

Instale o módulo **documentdb** via npm. Use o seguinte comando: * **npm install documentdb --save**

Em seguida, no arquivo *config.js*, atualize os valores de config.endpoint e config.authKey, conforme descrito na [Etapa 3: Definir configurações do aplicativo](#Config).

## Próximas etapas

-   Deseja obter um exemplo mais complexo do Node.js? Consulte [Criar um aplicativo Web do Node.js usando o Banco de Dados de Documentos](documentdb-nodejs-application.md).
-	Saiba como [monitorar uma conta do Banco de Dados de Documentos](documentdb-monitor-accounts.md).
-	Executar consultas em nosso conjunto de dados de exemplo no [Query Playground](https://www.documentdb.com/sql/demo).
-	Saiba mais sobre o modelo de programação na seção Desenvolvimento da [Página de documentação do Banco de Dados de Documentos](../../services/documentdb/).

[doc-landing-page]: ../../services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-nodejs-get-started/node-js-tutorial-keys.png

<!---HONumber=Nov15_HO4-->