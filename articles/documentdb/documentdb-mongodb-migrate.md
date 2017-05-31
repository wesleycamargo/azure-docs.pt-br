---
title: Usar mongoimport e mongorestore com a API para MongoDB do Azure Cosmos DB | Microsoft Docs
description: Saiba como usar mongoimport e mongorestore para importar dados para uma conta da API para MongoDB
keywords: mongoimport, mongorestore
services: cosmosdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 259bb45fea55a6e70d77747532f876e7b382183f
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---

# <a name="azure-cosmos-db-how-to-import-mongodb-data"></a>Azure Cosmos DB: Como importar dados do MongoDB? 

Para migrar dados do MongoDB para uma conta do Azure Cosmos DB para uso com a API do MongoDB, é necessário:

* Baixar o *mongoimport.exe* ou o *mongorestore.exe* do [Centro de Download do MongoDB](https://www.mongodb.com/download-center).
* Obter a [cadeia de conexão da API para MongoDB](documentdb-connect-mongodb-account.md).

Se você estiver importando dados do MongoDB e pretender usá-los com a API do DocumentDB, deverá usar a Ferramenta de Migração de Dados para importar os dados. Para obter mais informações, consulte [Ferramenta de Migração de Dados](documentdb-import-data.md).

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Recuperando sua cadeia de conexão
> * Importando dados do MongoDB usando mongoimport
> * Importando dados do MongoDB usando mongorestore

## <a name="prerequisites"></a>Pré-requisitos

* Aumentar a taxa de transferência: a duração da sua migração de dados depende da taxa de transferência que você configurar para suas coleções. Certifique-se de aumentar a taxa de transferência para migrações de dados maiores. Depois de concluir a migração, diminua a taxa de transferência para economizar custos. Para obter mais informações sobre como aumentar a produtividade no [portal do Azure](https://portal.azure.com), consulte [Níveis de desempenho e tipos de preço no Azure Cosmos DB](documentdb-performance-levels.md).

* Habilitar SSL: o Azure Cosmos DB tem padrões e requisitos de segurança rígidos. Certifique-se de habilitar o SSL ao interagir com sua conta. Os procedimentos no restante do artigo incluem como habilitar o SSL para o *mongoimport* e o *mongorestore*.

## <a name="find-your-connection-string-information-host-port-username-and-password"></a>Localizar informações de cadeia de conexão (host, porta, nome de usuário e senha)

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, clique na entrada **Azure Cosmos DB**.
2. No painel **Assinaturas**, selecione o nome da sua conta.
3. No folha **Cadeia de Conexão**, clique em **Cadeia de Conexão**.  
O painel direito contém todas as informações necessárias para conectar-se à sua conta com êxito.

    ![A folha de "Cadeia de Conexão"](./media/documentdb-mongodb-migrate/ConnectionStringBlade.png)

## <a name="import-data-to-api-for-mongodb-with-mongoimport"></a>Importar dados para a API para MongoDB com mongoimport

Para importar dados para sua conta do Azure Cosmos DB, use o modelo a seguir para executar a importação. Preencha *host*, *nome de usuário* e *senha* com os valores que são específicos para a sua conta.  

Modelo:

    mongoimport.exe --host <your_hostname>:10250 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file C:\sample.json

Exemplo:  

    mongoimport.exe --host anhoh-host.documents.azure.com:10250 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file C:\Users\anhoh\Desktop\*.json

## <a name="import-data-to-api-for-mongodb-with-mongorestore"></a>Importar dados para a API para MongoDB com mongorestore

Para restaurar dados para sua conta da API para MongoDB, use o modelo a seguir para executar a importação. Preencha *host*, *nome de usuário* e *senha* com os valores específicos para a sua conta.

Modelo:

    mongorestore.exe --host <your_hostname>:10250 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>

Exemplo:

    mongorestore.exe --host anhoh-host.documents.azure.com:10250 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você fez o seguinte:

> [!div class="checklist"]
> * Recuperou a cadeia de conexão
> * Importou dados do MongoDB usando mongoimport
> * Importou dados do MongoDB usando mongorestore

Agora, você pode seguir para o próximo tutorial e saber como consultar dados do MongoDB usando o Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Como consultar dados do MongoDB?](../cosmos-db/tutorial-query-mongodb.md)

