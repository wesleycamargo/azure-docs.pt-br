---
title: Tutorial do Node.js para a API do SQL para o Azure Cosmos DB | Microsoft Docs
description: Um tutorial do Node.js que demonstra como se conectar ao Azure Cosmos DB ou consultá-lo usando a API do SQL
keywords: tutorial do node.js, banco de dados do nó
services: cosmos-db
author: deborahc
manager: andrl
editor: monicar
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 07/30/2018
ms.author: dech
ms.openlocfilehash: 97b1e13532817b63a76cb57c5b63feb87cb92716
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2018
ms.locfileid: "41920244"
---
# <a name="nodejs-tutorial-create-a-nodejs-console-application-with-azure-cosmos-db-sql-api-and-javascript-sdk-20-preview"></a>Tutorial do Node.js: criar um aplicativo de console Node.js com a API do SQL no Azure Cosmos DB e JavaScript SDK 2.0 (versão prévia)

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [Node.js – v2.0 Preview](sql-api-nodejs-get-started-preview.md)
> * [Node.js para MongoDB](mongodb-samples.md)
> * [Java](sql-api-java-get-started.md)


Bem-vindo ao tutorial do Node.js para o SDK do JavaScript do Azure Cosmos DB! Após seguir este tutorial, você terá um aplicativo de console que cria e consulta recursos do Azure Cosmos DB.

Abordaremos:

* Criar e conectar-se a uma conta do Azure Cosmos DB
* Configurando o aplicativo
* Criar um banco de dados
* Criar um contêiner
* Adicionar itens JSON ao contêiner
* Consultar um contêiner
* Substituir um item
* Excluir um item
* Excluir o banco de dados

Você não tem tempo? Não se preocupe! A solução completa está disponível em [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ). Consulte [Obter a solução completa](#GetSolution) para as instruções rápidas.

Depois de concluir o tutorial do Node.js, use os botões de votação na parte superior e inferior desta página para nos enviar comentários. Se quiser que entremos em contato com você diretamente, inclua seu endereço de email em seu comentário.

Agora vamos começar!

## <a name="prerequisites-for-the-nodejs-tutorial"></a>Pré-requisitos para o tutorial do Node.js

Verifique se você tem o seguinte:

* Uma conta ativa do Azure. Se não tiver uma, você poderá se inscrever em uma [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js](https://nodejs.org/) versão v6.0.0 ou superior.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Etapa 1: Criar uma conta de banco de dados do Azure Cosmos DB

Vamos criar uma conta do Azure Cosmos DB. Caso tenha uma conta que queira usar, você poderá pular para [Configurar seu aplicativo Node.js](#SetupNode). Se estiver usando o Emulador do Azure Cosmos DB, siga as etapas em [Emulador do Azure Cosmos DB](local-emulator.md) para configurar o emulador e pule para [Configurar seu aplicativo Node.js](#SetupNode).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupNode"></a>Etapa 2: Configurar seu aplicativo Node.js

1. Abra seu terminal favorito.
2. Localize a pasta ou o diretório em que você deseja salvar o aplicativo do Node.js.
3. Crie dois arquivos JavaScript vazios com os seguintes comandos:
   * Windows:
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```
   * Linux/OS X:
     * ```touch app.js```
     * ```touch config.js```
4. Instale o módulo documentdb via npm. Use o seguinte comando:
   * ```npm install @azure/cosmos --save```

Ótimo! Agora que você terminou a configuração, vamos começar a escrever o código.

## <a id="Config"></a>Etapa 3: definir as configurações do aplicativo

Abra ```config.js``` em seu editor de texto favorito.

Em seguida, copie e cole o trecho de código a seguir e defina as propriedades ```config.endpoint``` e ```config.primaryKey``` para o URI do ponto de extremidade do Azure Cosmos DB e a chave primária. Ambas as configurações podem ser encontradas no [portal do Azure](https://portal.azure.com).

![Tutorial do Node.js ‒ captura de tela do portal do Azure mostrando uma conta do Azure Cosmos DB com o hub ATIVO realçado, o botão CHAVES realçado na folha da conta do Azure Cosmos DB e os valores de URI, CHAVE PRIMÁRIA e CHAVE SECUNDÁRIA realçados na folha Chaves ‒ banco de dados Nó][keys]

```nodejs
// ADD THIS PART TO YOUR CODE
var config = {}

config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
config.primaryKey = "~your primary key here~";
``` 

Copie e cole os dados ```database```, ```container``` e ```items``` de seu objeto ```config``` abaixo de onde você configura suas propriedades ```config.endpoint``` e ```config.primaryKey```. Se já tiver dados que gostaria de armazenar em seu banco de dados, você pode usar a [ferramenta de Migração de Dados](import-data.md) do Azure Cosmos DB em vez de definir os dados aqui.

```nodejs
var config = {}

config.endpoint = "~your Azure Cosmos DB account endpoint uri here~";
config.primaryKey = "~your primary key here~";

config.database = {
    "id": "FamilyDatabase"
};

config.container = {
    "id": "FamilyContainer"
};

config.items = {
    "Andersen": {
        "id": "Anderson.1",
        "lastName": "Andersen",
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
```
Observe que se você estiver familiarizado com a versão anterior do SDK do JavaScript, poderá estar acostumado a ver os termos 'coleção' e 'documento'. Como o Azure Cosmos DB é compatível com [vários modelos de API](https://docs.microsoft.com/azure/cosmos-db/introduction#key-capabilities), a versão 2.0 ou superior do SDK do JavaScript usa os termos genéricos 'contêiner' e 'item.' Um contêiner pode ser uma coleção, um gráfico ou uma tabela. Um item pode ser um documento, borda/vértice ou linha e descreve o conteúdo do contêiner. 

Por fim, exporte o objeto ```config```, para que você possa fazer referência a ele no arquivo ```app.js```.
```nodejs
        },
        "isRegistered": false
    }
};

// ADD THIS PART TO YOUR CODE
module.exports = config;
```
## <a id="Connect"></a>Etapa 4: Conectar-se a uma conta do Azure Cosmos DB

Abra o arquivo ```app.js``` vazio no editor de texto. Copie e cole o código a seguir para importar o módulo ```@azure/cosmos``` e o módulo recém-criado ```config```.

```nodejs
// ADD THIS PART TO YOUR CODE
const CosmosClient = require('@azure/cosmos').CosmosClient;

const config = require('./config');
const url = require('url');
```

Copie e cole o código para usar ```config.endpoint``` e ```config.primaryKey``` salvos anteriormente para criar um novo CosmosClient.

```nodejs
const url = require('url');

// ADD THIS PART TO YOUR CODE
const endpoint = config.endpoint;
const masterKey = config.primaryKey;

const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });
```

Agora que você tem o código para inicializar o cliente do Azure Cosmos DB, veremos como trabalhar com os recursos do Azure Cosmos DB.

## <a name="step-5-create-a-database"></a>Etapa 5: Criar um banco de dados

Copie e cole o código a seguir para configurar o status HTTP como Não Encontrado, a ID do banco de dados e a ID do contêiner. É através dessas IDs que o cliente do Azure Cosmos DB encontrará o banco de dados e o contêiner corretos.

```nodejs
const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

// ADD THIS PART TO YOUR CODE
const HttpStatusCodes = { NOTFOUND: 404 };

const databaseId = config.database.id;
const containerId = config.container.id;
```

Um [banco de dados](sql-api-resources.md#databases) pode ser criado usando a função [createIfNotExists](/javascript/api/%40azure/cosmos/databases) ou [create](/javascript/api/%40azure/cosmos/databases) da classe **Databases**. Um banco de dados é o contêiner lógico de itens particionados em contêineres. 

Copie e cole as funções **createDatabase** e **readDatabase** no arquivo app.js abaixo da definição de ```databaseId``` e ```containerId```. A função **createDatabase** criará um novo banco de dados com a ID ```FamilyDatabase```, especificada no objeto ```config``` se ele ainda não existir. A função **readDatabase** lerá a definição do banco de dados para garantir que o banco de dados existe.

```nodejs
/**
 * Create the database if it does not exist
 */
async function createDatabase() {
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    console.log(`Created database:\n${database.id}\n`);
}

/**
 * Read the database definition
 */
async function readDatabase() {
    const { body: databaseDefinition } = await client.database(databaseId).read();
    console.log(`Reading database:\n${databaseDefinition.id}\n`);
}
```

Copie e cole o código abaixo no local onde você configurou as funções **createDatabase** e **readDatabase** para adicionar a função auxiliar **exit** que imprimirá a mensagem de saída. 

```nodejs
// ADD THIS PART TO YOUR CODE
function exit(message) {
    console.log(message);
    console.log('Press any key to exit');
    process.stdin.setRawMode(true);
    process.stdin.resume();
    process.stdin.on('data', process.exit.bind(process, 0));
};
```
Copie e cole o código abaixo no local onde você configurou a função **exit** para chamar as funções **createDatabase** e **readDatabase**.

```nodejs
createDatabase()
    .then(() => readDatabase())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error \${JSON.stringify(error)}`) });
```

Neste ponto, seu código no ```app.js``` deverá ter essa aparência:

```nodejs
const CosmosClient = require('@azure/cosmos').CosmosClient;

const config = require('./config');
const url = require('url');

const endpoint = config.endpoint;
const masterKey = config.primaryKey;

const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

const HttpStatusCodes = { NOTFOUND: 404 };

const databaseId = config.database.id;
const containerId = config.container.id;


/**
 * Create the database if it does not exist
 */
async function createDatabase() {
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    console.log(`Created database:\n${database.id}\n`);
}

/**
 * Read the database definition
 */
async function readDatabase() {
    const { body: databaseDefinition } = await client.database(databaseId).read();
    console.log(`Reading database:\n${databaseDefinition.id}\n`);
}

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

createDatabase()
    .then(() => readDatabase())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
```

No terminal, localize o arquivo ```app.js``` e execute o comando: 
```bash 
node app.js
```

Parabéns! Você criou um banco de dados do Azure Cosmos DB com êxito.

## <a id="CreateContainer"></a>Etapa 6: Criar um contêiner

> [!WARNING]
> Chamar a função **createContainer** criará um novo contêiner e isso implicará custos. Para obter mais detalhes, visite a nossa [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/).

Um contêiner pode ser criado usando a função [createIfNotExists](/javascript/api/%40azure/cosmos/containers) ou [create](/javascript/api/%40azure/cosmos/containers) da classe **Containers**. 

Um contêiner é formado por itens (que, no caso da API do SQL, são documentos JSON) e uma lógica de aplicativo JavaScript associada.

Copie e cole as funções **createContainer** e **readContainer** sob a função **readDatabase** no arquivo app.js. A função **createContainer** criará um novo contêiner com a ```containerId```, especificada no objeto ```config``` se ele ainda não existir. A função **readContainer** lerá a definição do contêiner para verificar a existência do contêiner.

```nodejs
/**
 * Create the container if it does not exist
 */
async function createContainer() {
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    console.log(`Created container:\n${config.container.id}\n`);
}

/**
 * Read the container definition
 */
async function readContainer() {
    const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
    console.log(`Reading container:\n${containerDefinition.id}\n`);
}
```

Copie e cole o código abaixo da chamada para **readDatabase** para executar as funções **createContainer** e **readContainer**.
```nodejs
createDatabase()
    .then(() => readDatabase())

    // ADD THIS PART TO YOUR CODE
    .then(() => createContainer())
    .then(() => readContainer())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
```

Neste ponto, seu código no ```app.js``` deverá ter essa aparência:

```nodejs
const CosmosClient = require('@azure/cosmos').CosmosClient;

const config = require('./config');
const url = require('url');

const endpoint = config.endpoint;
const masterKey = config.primaryKey;

const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

const HttpStatusCodes = { NOTFOUND: 404 };

const databaseId = config.database.id;
const containerId = config.container.id;

/**
 * Create the database if it does not exist
 */
async function createDatabase() {
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    console.log(`Created database:\n${database.id}\n`);
}

/**
 * Read the database definition
 */
async function readDatabase() {
    const { body: databaseDefinition } = await client.database(databaseId).read();
    console.log(`Reading database:\n${databaseDefinition.id}\n`);
}

/**
 * Create the container if it does not exist
 */
async function createContainer() {
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    console.log(`Created container:\n${config.container.id}\n`);
}

/**
 * Read the container definition
 */
async function readContainer() {
    const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
    console.log(`Reading container:\n${containerDefinition.id}\n`);
}

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

createDatabase()
    .then(() => readDatabase())
    .then(() => createContainer())
    .then(() => readContainer())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
```

No terminal, localize o arquivo ```app.js``` e execute o comando: 

```bash 
node app.js
```

Parabéns! Você criou um contêiner do Azure Cosmos DB com êxito.

## <a id="CreateItem"></a>Etapa 7: Criar um item

Um item pode ser criado usando a função [create](/javascript/api/%40azure/cosmos/items) da classe **Items**. Ao usar a API do SQL, os itens são projetados como documentos, ou seja, o conteúdo JSON definido pelo usuário (arbitrário). Agora você pode inserir um item no Azure Cosmos DB.

Copie e cole a função **createFamilyItem** sob a função **readContainer**. A função **createFamilyItem** cria os itens que contêm os dados JSON salvos no objeto ```config```. Antes da criação, vamos verificar para garantir que não haja um item com a mesma ID.

```nodejs
/**
 * Create family item if it does not exist
 */
async function createFamilyItem(itemBody) {
    try {
        // read the item to see if it exists
        const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).read();
        console.log(`Item with family id ${itemBody.id} already exists\n`);
    }
    catch (error) {
        // create the family item if it does not exist
        if (error.code === HttpStatusCodes.NOTFOUND) {
            const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
            console.log(`Created family item with id:\n${itemBody.id}\n`);
        } else {
            throw error;
        }
    }
};
```

Copie e cole o código abaixo da chamada para **readContainer** para executar a função **createFamilyItem**.
```nodejs
createDatabase()
    .then(() => readDatabase())
    .then(() => createContainer())
    .then(() => readContainer())

    // ADD THIS PART TO YOUR CODE
    .then(() => createFamilyItem(config.items.Andersen))
    .then(() => createFamilyItem(config.items.Wakefield))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
```

No terminal, localize o arquivo ```app.js``` e execute o comando: 

```bash 
node app.js
```

Parabéns! Você criou um item do Azure Cosmos DB com êxito.


## <a id="Query"></a>Etapa 8: Consultar recursos do Azure Cosmos DB
O Azure Cosmos DB tem suporte para [consultas avançadas](sql-api-sql-query.md) de documentos JSON armazenados em cada contêiner. O código de exemplo a seguir mostra uma consulta que pode ser executada em documentos em seu contêiner.

Copie e cole a função **queryContainer** abaixo da função **createFamilyItem** no arquivo app.js. O Azure Cosmos DB dá suporte a consultas do tipo SQL, conforme mostrado abaixo. Para saber mais sobre como criar consultas complexas, consulte o [Espaço de Consulta](https://www.documentdb.com/sql/demo) e a [documentação de consulta](sql-api-sql-query.md).

```nodejs
/**
 * Query the container using SQL
 */
async function queryContainer() {
    console.log(`Querying container:\n${config.container.id}`);

    // query to return all children in a family
    const querySpec = {
        query: "SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName",
        parameters: [
            {
                name: "@lastName",
                value: "Andersen"
            }
        ]
    };

    const { result: results } = await client.database(databaseId).container(containerId).items.query(querySpec).toArray();
    for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
};
```

Copie e cole o código abaixo da chamada para **createFamilyItem** para executar a função **queryContainer**.

```nodejs
createDatabase()
    .then(() => readDatabase())
    .then(() => createContainer())
    .then(() => readContainer())
    .then(() => createFamilyItem(config.items.Andersen))
    .then(() => createFamilyItem(config.items.Wakefield))

    // ADD THIS PART TO YOUR CODE
    .then(() => queryContainer())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
```

No terminal, localize o arquivo ```app.js``` e execute o comando:

```bash 
node app.js
```

Parabéns! Você consultou com sucesso itens do Azure Cosmos DB.

## <a id="ReplaceItem"></a>Etapa 9: Substituir um item
O Azure Cosmos DB é compatível com a substituição de conteúdo dos itens.

Copie e cole a função **replaceFamilyItem** abaixo da função **queryContainer** no arquivo app.js. Observe que alteramos a propriedade 'grade' de um filho para um valor 6, que anteriormente era um 5.

```nodejs
// ADD THIS PART TO YOUR CODE
/**
 * Replace the item by ID.
 */
async function replaceFamilyItem(itemBody) {
    console.log(`Replacing item:\n${itemBody.id}\n`);
    // Change property 'grade'
    itemBody.children[0].grade = 6;
    const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).replace(itemBody);
};
```
Copie e cole o código abaixo da chamada para **queryContainer** para executar a função **replaceFamilyItem**. Além disso, adicione o código para chamar **queryContainer** novamente para verificar se o item foi alterado com êxito.

```nodejs
createDatabase()
    .then(() => readDatabase())
    .then(() => createContainer())
    .then(() => readContainer())
    .then(() => createFamilyItem(config.items.Andersen))
    .then(() => createFamilyItem(config.items.Wakefield))
    .then(() => queryContainer())

    // ADD THIS PART TO YOUR CODE
    .then(() => replaceFamilyItem(config.items.Andersen))
    .then(() => queryContainer())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
```
No terminal, localize o arquivo ```app.js``` e execute o comando:

```bash 
node app.js
```

Parabéns! Você substituiu um item do Azure Cosmos DB com êxito.

## <a id="DeleteItem"></a>Etapa 10: Excluir um item

O Azure Cosmos DB é compatível com a exclusão de itens JSON.

Copie e cole a função **deleteFamilyItem** sob a função **replaceFamilyItem**.

```nodejs
/**
 * Delete the item by ID.
 */
async function deleteFamilyItem(itemBody) {
    await client.database(databaseId).container(containerId).item(itemBody.id).delete(itemBody);
    console.log(`Deleted item:\n${itemBody.id}\n`);
};
```

Copie e cole o código abaixo da chamada para a segunda **queryContainer** para executar a função **deleteFamilyItem**.

```nodejs
createDatabase()
    .then(() => readDatabase())
    .then(() => createContainer())
    .then(() => readContainer())
    .then(() => createFamilyItem(config.items.Andersen))
    .then(() => createFamilyItem(config.items.Wakefield))
    .then(() => queryContainer
    ())
    .then(() => replaceFamilyItem(config.items.Andersen))
    .then(() => queryContainer())

    // ADD THIS PART TO YOUR CODE
    .then(() => deleteFamilyItem(config.items.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
```

No terminal, localize o arquivo ```app.js``` e execute o comando: 

```bash 
node app.js
```

Parabéns! Você excluiu um item do Azure Cosmos DB com êxito.

## <a id="DeleteDatabase"></a>Etapa 11: Excluir o banco de dados

Excluir o banco de dados criado removerá o banco de dados e todos os recursos filho (contêineres, itens etc.).

Copie e cole a função **cleanup** sob a função **deleteFamilyItem** para remover o banco de dados e todos os recursos filho.

```nodejs
/**
 * Cleanup the database and container on completion
 */
async function cleanup() {
    await client.database(databaseId).delete();
}
```

Copie e cole o código abaixo da chamada para **deleteFamilyItem** para executar a função **cleanup**.

```nodejs
createDatabase()
    .then(() => readDatabase())
    .then(() => createContainer())
    .then(() => readContainer())
    .then(() => createFamilyItem(config.items.Andersen))
    .then(() => createFamilyItem(config.items.Wakefield))
    .then(() => queryContainer())
    .then(() => replaceFamilyItem(config.items.Andersen))
    .then(() => queryContainer())
    .then(() => deleteFamilyItem(config.items.Andersen))

    // ADD THIS PART TO YOUR CODE
    .then(() => cleanup())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
```

## <a id="Run"></a>Etapa 12: Executar todo o aplicativo Node.js!

De modo geral, o código final deverá ter esta aparência:
```nodejs
const CosmosClient = require('@azure/cosmos').CosmosClient;

const config = require('./config');
const url = require('url');

const endpoint = config.endpoint;
const masterKey = config.primaryKey;

const HttpStatusCodes = { NOTFOUND: 404 };

const databaseId = config.database.id;
const containerId = config.container.id;

const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

/**
 * Create the database if it does not exist
 */
async function createDatabase() {
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    console.log(`Created database:\n${database.id}\n`);
}

/**
 * Read the database definition
 */
async function readDatabase() {
    const { body: databaseDefinition } = await client.database(databaseId).read();
    console.log(`Reading database:\n${databaseDefinition.id}\n`);
}

/**
 * Create the container if it does not exist
 */
async function createContainer() {
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    console.log(`Created container:\n${config.container.id}\n`);
}

/**
 * Read the container definition
 */
async function readContainer() {
    const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
    console.log(`Reading container:\n${containerDefinition.id}\n`);
}

/**
 * Create family item if it does not exist
 */
async function createFamilyItem(itemBody) {
    try {
        // read the item to see if it exists
        const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).read();
        console.log(`Item with family id ${itemBody.id} already exists\n`);
    }
    catch (error) {
        // create the family item if it does not exist
        if (error.code === HttpStatusCodes.NOTFOUND) {
            const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
            console.log(`Created family item with id:\n${itemBody.id}\n`);
        } else {
            throw error;
        }
    }
};

/**
 * Query the container using SQL
 */
async function queryContainer() {
    console.log(`Querying container:\n${config.container.id}`);

    // query to return all children in a family
    const querySpec = {
        query: "SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName",
        parameters: [
            {
                name: "@lastName",
                value: "Andersen"
            }
        ]
    };

    const { result: results } = await client.database(databaseId).container(containerId).items.query(querySpec).toArray();
    for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
};

/**
 * Replace the item by ID.
 */
async function replaceFamilyItem(itemBody) {
    console.log(`Replacing item:\n${itemBody.id}\n`);
    // Change property 'grade'
    itemBody.children[0].grade = 6;
    const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).replace(itemBody);
};

/**
 * Delete the item by ID.
 */
async function deleteFamilyItem(itemBody) {
    await client.database(databaseId).container(containerId).item(itemBody.id).delete(itemBody);
    console.log(`Deleted item:\n${itemBody.id}\n`);
};

/**
 * Cleanup the database and container on completion
 */
async function cleanup() {
    await client.database(databaseId).delete();
}

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

createDatabase()
    .then(() => readDatabase())
    .then(() => createContainer())
    .then(() => readContainer())
    .then(() => createFamilyItem(config.items.Andersen))
    .then(() => createFamilyItem(config.items.Wakefield))
    .then(() => queryContainer())
    .then(() => replaceFamilyItem(config.items.Andersen))
    .then(() => queryContainer())
    .then(() => deleteFamilyItem(config.items.Andersen))
    .then(() => cleanup())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
```

No terminal, localize o arquivo ```app.js``` e execute o comando: 

```bash 
node app.js
```

Você deverá ver a saída do aplicativo iniciado. A saída deve corresponder ao texto de exemplo abaixo.

    Created database:
    FamilyDatabase

    Reading database:
    FamilyDatabase

    Created container:
    FamilyContainer

    Reading container:
    FamilyContainer

    Created family item with id:
    Anderson.1

    Created family item with id:
    Wakefield.7

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing item:
    Anderson.1

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleted item:
    Anderson.1

    Completed successfully
    Press any key to exit

Parabéns! Você concluiu o tutorial do Node.js e tem seu primeiro aplicativo de console do Azure Cosmos DB!

## <a id="GetSolution"></a>Obter a solução completa do tutorial do Node.js

Se você não teve tempo para concluir as etapas neste tutorial ou se apenas deseja baixar o código, poderá obtê-lo no [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ).

Para executar a solução de introdução que contém todos os códigos deste artigo, você precisará do seguinte:

* Uma [conta do Azure Cosmos DB][create-account].
* A solução [Getting Started](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started) disponível no GitHub.

Instale o módulo **@azure/cosmos** usando o npm. Use o seguinte comando:

* ```npm install @azure/cosmos --save```

Em seguida, no arquivo ```config.js```, atualize os valores config.endpoint e config.primaryKey, como descrito na [Etapa 3: Definir as configurações do aplicativo](#Config). 

No terminal, localize o arquivo ```app.js``` e execute o comando: 

```bash 
node app.js
```

Pronto, pode continuar! 

## <a name="next-steps"></a>Próximas etapas
* Deseja obter um exemplo mais complexo do Node.js? Confira [Compilar um aplicativo Web Node.js usando o Azure Cosmos DB](sql-api-nodejs-application-preview.md).
* Saiba como [monitorar uma conta do Azure Cosmos DB](monitor-accounts.md).
* Executar consultas em nosso conjunto de dados de exemplo no [Query Playground](https://www.documentdb.com/sql/demo).

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png