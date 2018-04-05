---
title: Criar uma conta de API do MongoDB do Azure Cosmos DB
description: Descreve como criar uma coleção da API do MongoDB do Azure Cosmos DB no Portal do Azure
services: cosmos-db
author: mimig1
ms.service: cosmos-db
ms.topic: include
ms.date: 03/20/2018
ms.author: mimig
ms.custom: include file
ms.openlocfilehash: 02ea0e011642313b885bc48ec48104fa2789da81
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
---
1. Em uma nova janela, entre no [portal do Azure](https://portal.azure.com/).
2. No menu à esquerda, clique em **Criar um recurso**, clique em **Bancos de dados** e depois em **Azure Cosmos DB**, clique em **Criar**.
   
   ![Captura de tela do Portal do Azure, realçando Mais Serviços e BD Cosmos do Azure](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. Na folha **Nova conta**, especifique o **MongoDB** como API e preencha a configuração desejada da conta do Azure Cosmos DB.
 
    ![Captura de tela da folha Novo BD Cosmos do Azure](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-2.png)

    * A **ID** deve ser um nome exclusivo que você deve usar para identificar a conta do Azure Cosmos DB. Pode conter apenas letras minúsculas, números, o caractere '-' e deve ter entre 3 e 50 caracteres.
    * **Assinatura**: é a sua assinatura do Azure. Ela será preenchida para você.
    * **Grupo de Recursos** é o nome do grupo de recursos para sua conta do Azure Cosmos DB.
    * **Local** é a localização geográfica em que sua instância do Azure Cosmos DB está localizada. Escolha o local mais próximo de seus usuários.

4. Clique em **Criar** para criar a conta.
5. Na barra de ferramentas, clique em **Notificações** para monitorar o processo de implantação.

    ![Notificação de implantação iniciada](./media/cosmos-db-create-dbaccount-mongodb/azure-documentdb-nosql-notification.png)

6.  Quando a implantação for concluída, abra a nova conta no bloco Todos os Recursos. 

    ![Conta do Azure Cosmos DB no bloco Todos os Recursos](./media/cosmos-db-create-dbaccount-mongodb/azure-documentdb-all-resources.png)
