---
title: Cadeia de conexão do MongoDB para a conta do Azure Cosmos DB
description: Saiba como conectar seu aplicativo do MongoDB a uma conta do Azure Cosmos DB usando uma cadeia de conexão do MongoDB.
keywords: cadeia de conexão do mongodb
services: cosmos-db
author: slyons
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.topic: conceptual
ms.date: 12/19/2017
ms.author: sclyon
ms.openlocfilehash: a78a77e16e9a810c0be03656aa48b02cc8e6e5e6
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52849251"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Conectar um aplicativo do MongoDB ao Azure Cosmos DB
Saiba como conectar seu aplicativo do MongoDB a uma conta do Azure Cosmos DB usando uma cadeia de conexão do MongoDB. Você pode usar um banco de dados do Azure Cosmos DB como o armazenamento de dados para seu aplicativo MongoDB. 

Este tutorial fornece duas maneiras de recuperar informações da cadeia de conexão:

- [O método de início rápido](#QuickstartConnection), para uso com drivers do .NET, Node.js, Shell do MongoDB, Java e Python
- [O método de cadeia de conexão personalizada](#GetCustomConnection), para uso com outros drivers

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Se você ainda não tiver uma, crie agora mesmo uma [conta gratuita do Azure](https://azure.microsoft.com/free/). 
- Uma conta do Azure Cosmos DB. Para instruções, consulte [Compilar um aplicativo Web da API MongoDB com o .NET e o Portal do Azure](create-mongodb-dotnet.md).

## <a id="QuickstartConnection"></a>Obter a cadeia de conexão do MongoDB usando o início rápido
1. Em um navegador da Internet, entre no [Portal do Azure](https://portal.azure.com).
2. Na folha **Azure Cosmos DB**, selecione a API da conta MongoDB. 
3. No painel esquerdo da folha da conta, clique em **Início rápido**. 
4. Escolha sua plataforma (**.NET**, **Node.js**, **MongoDB Shell**, **Java**, **Python**). Caso não veja seu driver ou ferramenta na lista, não se preocupe, pois documentamos continuamente mais snippets de código de conexão. Comente abaixo sobre o que você gostaria de ver. Para saber como gostaria de ver sua conexão e leia [Obter informações da cadeia de conexão da conta](#GetCustomConnection).
5. Copie e cole o snippet de código no seu aplicativo MongoDB.

    ![Folha início rápido](./media/connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a> Obter a cadeia de conexão do MongoDB para personalização
1. Em um navegador da Internet, entre no [Portal do Azure](https://portal.azure.com).
2. Na folha **Azure Cosmos DB**, selecione a API da conta MongoDB. 
3. No painel esquerdo do folha de conta, clique em **Cadeia de Conexão**. 
4. A folha de **Cadeia de Conexão** é aberta. Ela tem todas as informações necessárias para se conectar à conta usando um driver para MongoDB, incluindo uma cadeia de conexão pré-construída.

    ![Folha Cadeia de conexão](./media/connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Requisitos da cadeia de conexão
> [!Important]
> O Azure Cosmos DB tem padrões e requisitos de segurança rígidos. As contas do Azure Cosmos DB exigem autenticação e comunicação segura por *SSL*. 
>
>

O Azure Cosmos DB oferece suporte ao formato de URI de cadeia de conexão padrão do MongoDB, com alguns requisitos específicos: As contas do Azure Cosmos DB exigem autenticação e comunicação segura por SSL. Sendo assim, o formato da cadeia de conexão é:

    mongodb://username:password@host:port/[database]?ssl=true

Os valores dessa cadeia de caracteres estão disponíveis na folha **Cadeia de conexão** mostrada acima:

* Nome de usuário (obrigatório): nome da conta do Azure Cosmos DB.
* Senha (obrigatória): senha da conta do Azure Cosmos DB.
* Host (obrigatório): FQDN da conta do Azure Cosmos DB.
* Porta (obrigatória): 10255.
* Banco de dados (opcional): o banco de dados que a conexão usa. Se nenhum banco de dados for fornecido, o banco de dados padrão é "teste".
* ssl=true (obrigatório)

Por exemplo, considere a conta mostrada na folha **Cadeia de conexão**. Uma cadeia de conexão válida é:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>Próximas etapas
* Saiba como usar o [Studio 3T (MongoChef)](mongodb-mongochef.md) com uma API do Azure Cosmos DB para conta MongoDB.
* Conheça a API do Azure Cosmos DB para o MongoDB ao ver [amostras](mongodb-samples.md).
