---
title: "Cadeia de conexão do MongoDB para a conta do Azure Cosmos DB | Microsoft Docs"
description: "Saiba como conectar seu aplicativo do MongoDB a uma conta do Azure Cosmos DB usando uma cadeia de conexão do MongoDB."
keywords: "cadeia de conexão do mongodb"
services: cosmosdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: e36f7375-9329-403b-afd1-4ab49894f75e
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 95fe71c87a3f70174b59cc866d7d399b7e91720c
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---

# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Conectar um aplicativo do MongoDB ao Azure Cosmos DB
Saiba como conectar seu aplicativo do MongoDB a uma conta do Azure Cosmos DB usando uma cadeia de conexão do MongoDB. Ao conectar seu aplicativo do MongoDB a um banco de dados do Azure Cosmos DB, você pode usar um banco de dados do Azure Cosmos DB como o armazenamento de dados do aplicativo do MongoDB. 

Este tutorial fornece duas maneiras de recuperar informações da cadeia de conexão:

- [O método de Início rápido](#QuickstartConnection), para uso com os drivers do .NET, Node.js, Shell do MongoDB, Java e Python.
- [O método de cadeia de conexão personalizada](#GetCustomConnection), para uso com outros drivers.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Se você ainda não tiver uma, crie agora mesmo uma [conta gratuita do Azure](https://azure.microsoft.com/free/). 
- Uma conta do Azure Cosmos DB. Para obter instruções, consulte [Criar uma conta do Azure Cosmos DB para uso com aplicativos do MongoDB](documentdb-create-mongodb-account.md).

## <a id="QuickstartConnection"></a>Obter a cadeia de conexão do MongoDB usando o Início rápido
1. Em um navegador da Internet, entre no [Portal do Azure](https://portal.azure.com).
2. Na folha **Azure Cosmos DB**, selecione a conta da API do MongoDB. 
3. Na barra **Navegação Esquerda** da folha da conta, clique em **Início rápido**. 
4. Escolha sua plataforma (*driver do .NET*, *driver do Node.js*, *Shell do MongoDB*, *driver do Java*, *driver do Python*). Caso não veja seu driver ou ferramenta na lista, não se preocupe, pois documentamos continuamente mais trechos de código de conexão. Comente abaixo o que você gostaria de ver e leia [Obter informações da cadeia de conexão da conta](#GetCustomConnection) para saber como criar sua própria conexão.
5. Copie e cole o trecho de código no seu aplicativo MongoDB e você estará pronto para seguir adiante.

    ![Captura de tela da folha de início rápido](./media/documentdb-connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a> Obter a cadeia de conexão do MongoDB para personalização
1. Em um navegador da Internet, entre no [Portal do Azure](https://portal.azure.com).
2. Na folha **Azure Cosmos DB**, selecione a conta da API do MongoDB. 
3. Na barra **Navegação Esquerda** da folha da conta, clique em **Cadeia de Conexão**. 
4. A folha **Informações da Cadeia de Conexões** é aberta e tem todas as informações necessárias para se conectar à conta usando um driver para MongoDB, incluindo uma cadeia de conexão pré-construída.

    ![Captura de tela da folha de cadeia de conexão](./media/documentdb-connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Requisitos da cadeia de conexão
> [!Important]
> O Azure Cosmos DB tem padrões e requisitos de segurança rígidos. As contas do Azure Cosmos DB exigem autenticação e comunicação segura por **SSL**.
>
>

É importante observar que o Azure Cosmos DB dá suporte ao formato de URI da cadeia de conexão padrão do MongoDB, com alguns requisitos específicos: as contas do Azure Cosmos DB exigem autenticação e comunicação segura por SSL.  Sendo assim, o formato da cadeia de conexão é:

    mongodb://username:password@host:port/[database]?ssl=true

Onde os valores dessa cadeia de caracteres estão disponíveis na folha Cadeia de Conexão mostrada acima.

* Nome de usuário (obrigatório)
  * Nome da conta do Azure Cosmos DB
* Senha (obrigatória)
  * Senha da conta do Azure Cosmos DB
* Host (obrigatório)
  * FQDN da conta do Azure Cosmos DB
* Porta (obrigatória)
  * 10250
* Banco de dados (opcional)
  * O banco de dados padrão usado pela conexão (se nenhum banco de dados for fornecido, o banco de dados padrão será "test")
* ssl=true (obrigatório)

Por exemplo, considere a conta mostrada nas Informações da Cadeia de Conexão acima.  Uma cadeia de conexão válida é:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@anhohmongo.documents.azure.com:10250/mydatabase?ssl=true

## <a name="next-steps"></a>Próximas etapas
* Saiba como [usar o MongoChef](documentdb-mongodb-mongochef.md) com uma conta do Azure Cosmos DB: API para MongoDB.
* Conheça as [amostras](documentdb-mongodb-samples.md) do Azure Cosmos DB: API para MongoDB.

