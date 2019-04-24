---
title: Recursos e SDK do .NET Standard da API de Tabela do Azure Cosmos DB
description: Saiba tudo sobre a API de Tabela do Azure Cosmos DB e o SDK do .NET Standard, incluindo datas de lançamento, datas de desativação e alterações feitas entre cada versão.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 10/18/2018
ms.openlocfilehash: 4b193dd41c137907e56e2bf38b18ebcf2f2b481a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60326847"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>API .NET Standard de Tabela do Azure Cosmos DB: download e notas sobre a versão
> [!div class="op_single_selector"]
> 
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Baixe o SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**Amostra**|[Exemplo de .NET do cosmos DB API de tabela](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**Início rápido**|[Início rápido](create-table-dotnet.md)|
|**Tutorial**|[Tutorial](tutorial-develop-table-dotnet.md)|
|**Framework atualmente com suporte**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|

## <a name="release-notes"></a>Notas de versão

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Correções de bug

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Versão de disponibilidade geral

### <a name="a-name0110-preview0110-preview"></a><a name="0.11.0-preview"/>0.11.0-Preview
* As alterações foram feitas como CloudTableClient pode ser configurado. Leva apenas um objeto um TableClientConfiguration durante a construção. TableClientConfiguration fornece propriedades diferentes para configurar o comportamento do cliente, dependendo se o ponto de extremidade de destino é a API de tabela ou a API de tabela de armazenamento do Azure.
* Adicionado suporte para TableQuery para retornar os resultados em ordem classificada em uma coluna personalizada. Esse recurso tem suporte apenas em pontos de extremidade de tabela do Cosmos DB.
* Adicionado suporte para expor RequestCharges em vários tipos de resultado. Esse recurso tem suporte apenas em pontos de extremidade de tabela do Cosmos DB.

### <a name="a-name0101-preview0101-preview"></a><a name="0.10.1-preview"/>0.10.1-preview
* Adicione suporte para o token SAS, operações de TablePermissions, ServiceProperties e ServiceStats nos pontos de extremidade da tabela de Armazenamento do Azure. 
   > [!NOTE]
   > Ainda não há suporte para algumas funcionalidades em SDKs anteriores de tabela do Armazenamento do Azure, como a criptografia do lado do cliente.

### <a name="a-name0100-preview0100-preview"></a><a name="0.10.0-preview"/>0.10.0-preview
* Adicione suporte para as principais operações CRUD, lote e consulta nos pontos de extremidade de tabela do Armazenamento do Azure. 
   > [!NOTE]
   > Ainda não há suporte para algumas funcionalidades em SDKs anteriores de tabela do Armazenamento do Azure, como a criptografia do lado do cliente.

### <a name="a-name091-preview091-preview"></a><a name="0.9.1-preview"/>0.9.1-preview
* O SDK do .NET Standard da Tabela do Azure Cosmos DB é uma biblioteca .NET de plataforma cruzada que fornece acesso eficiente ao modelo de dados Tabela no Cosmos DB. A versão inicial dá suporte ao conjunto completo de funcionalidades de CRUD de Tabela e Entidade + Consulta com APIs semelhantes aos do [SDK da Tabela do Cosmos DB para .NET Framework](table-sdk-dotnet.md). 
   > [!NOTE]
   >  Os pontos de extremidade da Tabela de Armazenamento do Azure ainda não têm suporte na versão 0.9.1-preview.

## <a name="release-and-retirement-dates"></a>Datas de lançamento e desativação
A Microsoft notifica pelo menos **12 meses** antes de desativar um SDK, a fim de realizar uma transição tranquila para uma versão mais recente/com suporte.

| Version | Data do lançamento | Data de desativação |
| --- | --- | --- |
| [1.0.1](#1.0.1) |19 de abril de 2019 |--- |
| [1.0.0](#1.0.0) |13 de março de 2019 |--- |
| [0.11.0-preview](#0.11.0-preview) |5 de março de 2019 |--- |
| [0.10.1-preview](#0.10.1-preview) |22 janeiro de 2019 |--- |
| [0.10.0-preview](#0.10.0-preview) |18 de dezembro de 2018 |--- |
| [0.9.1-preview](#0.9.1-preview) |18 de outubro de 2018 |--- |


## <a name="faq"></a>Perguntas frequentes

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também
Para saber mais sobre a API de Tabela do Azure CosmosDB, consulte [Introdução ao Azure Cosmos DB: API de Tabela](table-introduction.md). 
