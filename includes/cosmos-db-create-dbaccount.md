---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: a144433e1ff6c0f7ca4e0ed72704d339150f2d56
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52643451"
---
1. Em uma nova janela do navegador, entre no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Criar um recurso** > **Bancos de dados** > **Azure Cosmos DB**.
   
   ![O painel Bancos de Dados do portal do Azure](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

3. Na página **Nova conta**, insira as configurações da nova conta do Azure Cosmos DB. 
 
    Configuração|Valor|DESCRIÇÃO
    ---|---|---
    ID|Insira um nome exclusivo|Insira um nome exclusivo para identificar a conta do Azure Cosmos DB. Como *documents.Azure.com* é acrescentado à ID que você fornece para criar o URI, use uma ID exclusiva.<br><br>A ID deve conter apenas letras minúsculas, números e o caractere de hífen (-). O comprimento deve ser entre 3 e 50 caracteres.
    API|SQL|A API determina o tipo de conta a ser criada. O Azure Cosmos DB oferece cinco APIs: SQL para o banco de dados de documentos, Gremlin para o banco de dados de grafos, MongoDB para o banco de dados de documentos, API de Tabela e API do Cassandra. No momento, você deve criar uma conta separada para cada API. <br><br>Selecione **SQL** porque neste artigo você criará um banco de dados de documentos e fará consultas usando a sintaxe SQL. <br><br>[Saiba mais sobre a API do SQL](../articles/cosmos-db/documentdb-introduction.md).|
    Assinatura|Sua assinatura|Selecione a assinatura do Azure que você deseja usar para essa conta do Azure Cosmos DB. 
    Grupo de recursos|Criar Novo<br><br>Em seguida, insira o mesmo nome exclusivo fornecido na ID|Selecione **Criar novo**. Insira o novo nome do grupo de recursos para sua conta. Para simplificar, use um nome igual à sua ID. 
    Local padrão|Selecione a região mais próxima de seus usuários|Selecione uma localização geográfica para hospedar a sua conta do Azure Cosmos DB. Use o local mais próximo dos usuários para fornecer a eles acesso mais rápido aos dados.
    Habilitar a redundância geográfica| Deixar em branco | Essa opção cria uma versão replicada do banco de dados em uma segunda região (pareada). Deixe essa caixa de seleção em branco. 

    Selecione **Criar**.

    ![A página da nova conta do Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account.png)

4. A criação da conta leva alguns minutos. Aguarde até que o portal exiba a página **Parabéns! Sua conta do Azure Cosmos DB foi criada**.

    ![O painel Notificações do portal do Azure](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

