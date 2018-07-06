---
title: Usar APIs do MongoDB para criar um aplicativo do Azure Cosmos DB | Microsoft Docs
description: Um tutorial que cria um banco de dados online usando as APIs do Azure Cosmos DB para MongoDB.
keywords: exemplos do mongodb
services: cosmos-db
author: SnehaGunda
manager: kfile
editor: ''
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: sample
ms.date: 03/23/2018
ms.author: sngun
ms.openlocfilehash: 188b192cf9b86a2d28a578bbcec0d6b19a8cc5d0
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2018
ms.locfileid: "37084895"
---
# <a name="build-an-azure-cosmos-db-api-for-mongodb-app-using-nodejs"></a>Criar um aplicativo do Azure Cosmos DB: API para MongoDB usando o Node.js
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Node.js para MongoDB](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Este exemplo mostra como criar um aplicativo de console do Azure Cosmos DB: API para MongoDB usando o Node.js.

Para usar este exemplo, você deve:

* [Criar](create-mongodb-dotnet.md#create-account) uma conta do Azure Cosmos DB: API para MongoDB.
* Recuperar suas informações de [cadeia de conexão](connect-mongodb-account.md) do MongoDB.

## <a name="create-the-app"></a>Crie o aplicativo

1. Crie um arquivo *app.js* e copie e cole o código abaixo.

    ```nodejs
    var MongoClient = require('mongodb').MongoClient;
    var assert = require('assert');
    var ObjectId = require('mongodb').ObjectID;
    var url = 'mongodb://<username>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';

    var insertDocument = function(db, callback) {
    db.collection('families').insertOne( {
            "id": "AndersenFamily",
            "lastName": "Andersen",
            "parents": [
                { "firstName": "Thomas" },
                { "firstName": "Mary Kay" }
            ],
            "children": [
                { "firstName": "John", "gender": "male", "grade": 7 }
            ],
            "pets": [
                { "givenName": "Fluffy" }
            ],
            "address": { "country": "USA", "state": "WA", "city": "Seattle" }
        }, function(err, result) {
        assert.equal(err, null);
        console.log("Inserted a document into the families collection.");
        callback();
    });
    };
    
    var findFamilies = function(db, callback) {
    var cursor =db.collection('families').find( );
    cursor.each(function(err, doc) {
        assert.equal(err, null);
        if (doc != null) {
            console.dir(doc);
        } else {
            callback();
        }
    });
    };
    
    var updateFamilies = function(db, callback) {
    db.collection('families').updateOne(
        { "lastName" : "Andersen" },
        {
            $set: { "pets": [
                { "givenName": "Fluffy" },
                { "givenName": "Rocky"}
            ] },
            $currentDate: { "lastModified": true }
        }, function(err, results) {
        console.log(results);
        callback();
    });
    };
    
    var removeFamilies = function(db, callback) {
    db.collection('families').deleteMany(
        { "lastName": "Andersen" },
        function(err, results) {
            console.log(results);
            callback();
        }
    );
    };
    
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    **Opcional**: se você estiver usando o driver **MongoDB Node. js 2.2** , substitua o trecho de código a seguir:

    Original:

    ```nodejs
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    Deve ser substituído por:

    ```nodejs
    MongoClient.connect(url, function(err, db) {
    assert.equal(null, err);
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                db.close();
            });
        });
        });
    });
    });
    ```
    
2. Modifique as variáveis a seguir no arquivo *app.js* de acordo com as configurações da sua conta (Saiba como encontrar sua [cadeia de conexão](connect-mongodb-account.md)):

    > [!IMPORTANT]
    > O **driver 3.0 do MongoDB Node. js** requer codificação de caracteres especiais na senha do Cosmos DB. Certifique-se de codificar caracteres '=' como % 3D
    >
    > Exemplo: A senha *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv==* codifica a *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv%3D%3D*
    >
    > O **driver 2.2 do MongoDB Node. js** requer codificação de caracteres especiais na senha do Cosmos DB.
    >
    >
   
    ```nodejs
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';
    ```
     
3. Abra seu terminal favorito, execute **npm install mongodb --save** e, em seguida, o aplicativo com **node app.js**

## <a name="next-steps"></a>Próximas etapas
* Saiba como [usar o MongoChef](mongodb-mongochef.md) com sua conta do Azure Cosmos DB: API para MongoDB.
