---
title: Usar o Robo 3T para se conectar ao Azure Cosmos DB
description: Saiba como se conectar ao Azure Cosmos DB usando o Robo 3T e a API do Azure Cosmos DB para MongoDB
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 5696c376ad64df01d7f9d43ff59c87402c334c52
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034804"
---
# <a name="use-robo-3t-with-azure-cosmos-dbs-api-for-mongodb"></a>Usar o Robo 3T com a API do Azure Cosmos DB para MongoDB

Para se conectar à conta do Cosmos usando o Robo 3T, faça o seguinte:

* Baixe e instale o [Robo 3T](https://robomongo.org/)
* Obtenha informações da [cadeia de conexão](connect-mongodb-account.md) do Cosmos DB

## <a name="connect-using-robo-3t"></a>Conecte usando o Robo 3T
Para adicionar sua conta do Cosmos ao gerenciador de conexões do Robo 3T, execute as seguintes etapas:

1. Recupere as informações de conexão da sua conta do Cosmos configurada com a API do Azure Cosmos DB para MongoDB usando as instruções que estão [aqui](connect-mongodb-account.md).

    ![Captura de tela da folha de cadeia de conexão](./media/mongodb-robomongo/connectionstringblade.png)
2. Execute *Robomongo.exe*

3. Clique no botão de conexão em **Arquivo** para gerenciar suas conexões. Em seguida, clique em **Criar** na janela **Conexões do MongoDB**, o que abrirá a janela **Configurações de Conexão**.

4. No janela **Configurações de Conexão**, escolha um nome. Em seguida, localize o **Host** e **Porta** de suas informações de conexão na Etapa 1 e insira-os em **Endereço** e **Porta**, respectivamente.

    ![Captura de tela do Robomongo Manage Connections](./media/mongodb-robomongo/manageconnections.png)
5. Na guia **Autenticação**, clique em **Executar autenticação**. Em seguida, insira seu Banco de dados (o padrão é *Admin*), **Nome de Usuário** e **Senha**.
Tanto o **Nome de Usuário** quanto a **Senha** podem ser encontrados em suas informações de conexão na Etapa 1.

    ![Captura de tela da guia de Autenticação do Robomongo](./media/mongodb-robomongo/authentication.png)
6. Na guia **SSL**, marque **Usar protocolo SSL** e altere o **Método de Autenticação** para **Certificado Autoassinado**.

    ![Captura de tela da guia Robomongo SSL](./media/mongodb-robomongo/SSL.png)
7. Por fim, clique em **Teste** para verificar se você é capaz de se conectar e, em seguida, **Salve**.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [usar o Studio 3T](mongodb-mongochef.md) com uma conta de API do Azure Cosmos DB para o MongoDB.
- Explore [exemplos](mongodb-samples.md) do MongoDB com a API do Azure Cosmos DB para MongoDB.
