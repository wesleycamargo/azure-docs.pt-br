---
title: Usar o Robomongo no Azure Cosmos DB
description: 'Saiba como usar o Robomongo com uma conta do Azure Cosmos DB: API para conta MongoDB'
keywords: robomongo
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: sngun
ms.openlocfilehash: 78f0158c9a80a60717b81b4788531c7efd979111
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52863798"
---
# <a name="use-robomongo-with-an-azure-cosmos-db-api-for-mongodb-account"></a>Usar o Robomongo com um Azure Cosmos DB: API para conta MongoDB
Conectar-se a uma conta do Azure Cosmos DB: API para MongoDB usando o Robomongo, você deve:

* Baixar e instalar o [Robomongo](https://robomongo.org/)
* Obtenha o seu Azure Cosmos DB: Informação da [string de conexão](connect-mongodb-account.md) da conta da API para MongoDB

## <a name="connect-using-robomongo"></a>Conectar usando o Robomongo
Para adicionar o Azure Cosmos DB: API para MongoDB do DocumentDB às conexões entre o Robomongo e o MongoDB, execute as etapas a seguir.

1. Recupere seu Azure Cosmos DB: API para a conta MongoDB usando as instruções encontradas [aqui](connect-mongodb-account.md).

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
* Explorar o Azure Cosmos DB: API para [amostras](mongodb-samples.md) do MongoDB.
