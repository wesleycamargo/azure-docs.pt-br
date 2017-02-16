---
title: Usar o MongoChef com uma conta do Azure DocumentDB com suporte de protocolo para o MongoDB | Microsoft Docs
description: "Saiba como usar o MongoChef com uma conta do Banco de Dados de Documentos com suporte de protocolo para MongoDB, agora disponível para a versão de visualização."
keywords: MongoChef
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: b1e73ced8f11c06adc509af355ccf47b616a211b
ms.openlocfilehash: 41b94580277bc07aad2f122b5db9060e2d7938d8


---
# <a name="use-mongochef-with-a-documentdb-account-with-protocol-support-for-mongodb"></a>Usar o MongoChef com uma conta do Banco de Dados de Documentos com suporte de protocolo para MongoDB
Para se conectar a uma conta do Banco de Dados de Documentos do Azure com suporte de protocolo para MongoDB usando o MongoChef, você deve:

* Baixar e instalar o [MongoChef](http://3t.io/mongochef)
* Ter as informações de [cadeia de conexão](documentdb-connect-mongodb-account.md) da sua conta do Banco de Dados de Documentos com suporte de protocolo para MongoDB

## <a name="create-the-connection-in-mongochef"></a>Criar a conexão no MongoChef
Para adicionar a conta do Banco de Dados de Documentos com suporte de protocolo para MongoDB ao gerenciador de conexões do MongoChef, execute as etapas a seguir.

1. Recuperar as informações do Banco de Dados de Documentos com o suporte de protocolo para as informações de conexão do MongoDB usando as instruções que constam [aqui](documentdb-connect-mongodb-account.md).

    ![Captura de tela da folha de cadeia de conexão](./media/documentdb-mongodb-mongochef/ConnectionStringBlade.png)
2. Clique em **Conectar** para abrir o Gerenciador de Conexões e clique em **Nova Conexão**

    ![Captura de tela do gerenciador de conexões do MongoChef](./media/documentdb-mongodb-mongochef/ConnectionManager.png)
3. Na janela **Nova Conexão**, na guia **Servidor**, insira o HOST (FQDN) da conta do DocumentDB com suporte de protocolo para o MongoDB e o PORT.

    ![Captura de tela da guia servidor do gerenciador de conexões do MongoChef](./media/documentdb-mongodb-mongochef/ConnectionManagerServerTab.png)
4. Na janela **Nova Conexão**, na guia **Autenticação**, escolha o Padrão do Modo de Autenticação **(MONGODB-CR ou SCARM-SHA-1)** e insira USERNAME e PASSWORD.  Aceite o banco de dados de autenticação padrão (admin) ou forneça seu próprio valor.

    ![Captura de tela da guia autenticação do gerenciador de conexões do MongoChef](./media/documentdb-mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. Na janela **Nova Conexão**, na guia **SSL**, marque a caixa de seleção **Usar protocolo SSL para se conectar** e o botão de opção **Aceitar certificados SSL de servidor autoassinados**.

    ![Captura de tela da guia SSL do gerenciador de conexões do MongoChef](./media/documentdb-mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Clique no botão **Testar Conectividade** para validar as informações de conexão, clique em **OK** para retornar à janela Nova Conexão e clique em **Salvar**.

    ![Captura de tela da janela testar conectividade do MongoChef](./media/documentdb-mongodb-mongochef/TestConnectionResults.png)

## <a name="use-mongochef-to-create-a-database-collection-and-documents"></a>Usar o MongoChef para criar um banco de dados, uma coleção e documentos
Para criar um banco de dados, uma coleção e documentos usando o MongoChef, execute as etapas a seguir.

1. No **Gerenciador de Conexões**, realce a conexão e clique em **Conectar**.

    ![Captura de tela do gerenciador de conexões do MongoChef](./media/documentdb-mongodb-mongochef/ConnectToAccount.png)
2. Clique com o botão direito do mouse e escolha **Adicionar Banco de Dados**.  Forneça um nome de banco de dados e clique em **OK**.

    ![Captura de tela da opção Adicionar Banco de Dados do MongoChef](./media/documentdb-mongodb-mongochef/AddDatabase1.png)
3. Clique com o botão direito do mouse no banco de dados e escolha **Adicionar Coleção**.  Forneça um nome de coleção e clique em **Criar**.

    ![Captura de tela da opção Adicionar Coleção do MongoChef](./media/documentdb-mongodb-mongochef/AddCollection.png)
4. Clique no item de menu **Coleção** e clique em **Adicionar Documento**.

    ![Captura de tela do item menu Adicionar Documento do MongoChef](./media/documentdb-mongodb-mongochef/AddDocument1.png)
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
6. Adicione outro documento, dessa vez com o conteúdo a seguir.

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

    ![Captura de tela dos resultados de consulta do MongoChef](./media/documentdb-mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Próximas etapas
* Explore [amostras](documentdb-mongodb-samples.md)do Banco de Dados de Documentos com suporte de protocolo para MongoDB.



<!--HONumber=Dec16_HO1-->


