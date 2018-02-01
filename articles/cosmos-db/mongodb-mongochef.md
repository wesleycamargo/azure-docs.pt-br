---
title: Usar o Studio 3T (MongoChef) com o Azure Cosmos DB | Microsoft Docs
description: Saiba como usar o Studio 3T com uma conta de API MongoDB do Azure Cosmos DB
keywords: mongochef, studio 3T
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: anhoh
ms.openlocfilehash: 0341fbf668bbbc8f02e78bc1f6c7a00ecc939cc2
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2018
---
# <a name="azure-cosmos-db-use-studio-3t-with-a-mongodb-api-account"></a>Azure Cosmos DB: usar o Studio 3T com um conta de API MongoDB

Para conectar-se a uma conta de API MongoDB do Azure Cosmos DB, você deverá:

* Fazer o download e instalar o [Studio 3T](https://studio3t.com/) (anteriormente conhecido como MongoChef)
* Tenha suas informações da [cadeia de conexão](connect-mongodb-account.md)do Azure Cosmos DB para a conta do MongoDB

## <a name="create-the-connection-in-studio-3t"></a>Criar a conexão no Studio 3T
Para adicionar sua conta do Azure Cosmos DB ao gerenciador de conexões do Studio 3T, execute as seguintes etapas:

1. Recupere as informações de conexão do Azure Cosmos DB para sua conta de API MongoDB usando as instruções no artigo [Conectar um aplicativo do MongoDB ao Azure Cosmos DB](connect-mongodb-account.md).

    ![Captura de tela da página da cadeia de conexão](./media/mongodb-mongochef/ConnectionStringBlade.png)
2. Clique em **Conectar** para abrir o Gerenciador de Conexões e clique em **Nova Conexão**

    ![Captura de tela do gerenciador de conexões do Studio 3T](./media/mongodb-mongochef/ConnectionManager.png)
3. Na janela **Nova Conexão**, na guia **Servidor**, insira o HOST (FQDN) da conta do Azure Cosmos DB e a PORTA.

    ![Captura de tela da guia do servidor do gerenciador de conexões do Studio 3T](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. Na janela **Nova Conexão**, na guia **Autenticação**, escolha o Modo de Autenticação **(MONGODB-CR ou SCARM-SHA-1) Básico** e insira NOME DE USUÁRIO e SENHA.  Aceite o banco de dados de autenticação padrão (admin) ou forneça seu próprio valor.

    ![Captura de tela da guia de autenticação do gerenciador de conexões do Studio 3T](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. Na janela **Nova Conexão**, na guia **SSL**, marque a caixa de seleção **Usar protocolo SSL para se conectar** e o botão de opção **Aceitar certificados SSL de servidor autoassinados**.

    ![Captura de tela da guia SSL do gerenciador de conexões do Studio 3T](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Clique no botão **Testar Conectividade** para validar as informações de conexão, clique em **OK** para retornar à janela Nova Conexão e clique em **Salvar**.

    ![Captura de tela da janela de conexão de teste do Studio 3T](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Usar o Studio 3T para criar um banco de dados, coleção e documentos
Para criar um banco de dados, coleta e documentos usando o Studio 3T, execute as seguintes etapas:

1. No **Gerenciador de Conexões**, realce a conexão e clique em **Conectar**.

    ![Captura de tela do gerenciador de conexões do Studio 3T](./media/mongodb-mongochef/ConnectToAccount.png)
2. Clique com o botão direito do mouse no host e escolha **Adicionar Banco de Dados**.  Forneça um nome de banco de dados e clique em **OK**.

    ![Captura de tela da opção Adicionar Banco de Dados do Studio 3T](./media/mongodb-mongochef/AddDatabase1.png)
3. Clique com o botão direito do mouse no banco de dados e escolha **Adicionar Coleção**.  Forneça um nome de coleção e clique em **Criar**.

    ![Captura de tela da opção Adicionar Coleção do Studio 3T](./media/mongodb-mongochef/AddCollection.png)
4. Clique no item de menu **Coleção** e clique em **Adicionar Documento**.

    ![Captura de tela do item de menu Adicionar Documento do Studio 3T](./media/mongodb-mongochef/AddDocument1.png)
5. Na caixa de diálogo Adicionar Documento, cole o conteúdo a seguir e clique em **Adicionar Documento**.

        {
        "_id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
               { "firstName": "Thomas" },
               { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
        }
6. Adicione outro documento, dessa vez com o seguinte conteúdo:

        {
        "_id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                 "givenName": "Jesse",
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            {
                "familyName": "Miller",
                 "givenName": "Lisa",
                 "gender": "female",
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
        }
7. Execute uma consulta de exemplo. Por exemplo, procure famílias com o sobrenome 'Andersen' e retorne os campos de estado e pais.

    ![Captura de tela dos resultados de consulta do MongoChef](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Próximas etapas
* Explore as amostra de [API MongoDB do Azure Cosmos DB](mongodb-samples.md).
