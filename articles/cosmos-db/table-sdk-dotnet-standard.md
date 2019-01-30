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
ms.openlocfilehash: 7ccc9793bd1c4a345098892e66be15228efcc265
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54844561"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>API .NET Standard de Tabela do Azure Cosmos DB: download e notas sobre a versão
> [!div class="op_single_selector"]

> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Baixe o SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**Framework atualmente com suporte**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|

## <a name="release-notes"></a>Notas de versão

### <a name="a-name0101-preview0101-preview"></a><a name="0.10.1-preview"/>0.10.1-preview
* Adicione suporte para o token SAS, operações de TablePermissions, ServiceProperties e ServiceStats nos pontos de extremidade da tabela de Armazenamento do Azure. 
   > [!NOTE] Ainda não há suporte para algumas funcionalidades em SDKs anteriores de tabela do Armazenamento do Azure, como a criptografia do lado do cliente.

### <a name="a-name0100-preview0100-preview"></a><a name="0.10.0-preview"/>0.10.0-preview
* Adicione suporte para as principais operações CRUD, lote e consulta nos pontos de extremidade de tabela do Armazenamento do Azure. 
   > [!NOTE] Ainda não há suporte para algumas funcionalidades em SDKs anteriores de tabela do Armazenamento do Azure, como a criptografia do lado do cliente.

### <a name="a-name091-preview091-preview"></a><a name="0.9.1-preview"/>0.9.1-preview
* O SDK do .NET Standard da Tabela do Azure Cosmos DB é uma biblioteca .NET de plataforma cruzada que fornece acesso eficiente ao modelo de dados Tabela no Cosmos DB. A versão inicial dá suporte ao conjunto completo de funcionalidades de CRUD de Tabela e Entidade + Consulta com APIs semelhantes aos do [SDK da Tabela do Cosmos DB para .NET Framework](table-sdk-dotnet.md). 
   > [!NOTE] Ainda não há suporte para os pontos de extremidade da Tabela de Armazenamento do Azure na versão 0.9.1-preview.

## <a name="release-and-retirement-dates"></a>Datas de lançamento e desativação
A Microsoft notifica pelo menos **12 meses** antes de desativar um SDK, a fim de realizar uma transição tranquila para uma versão mais recente/com suporte.

| Versão | Data do lançamento | Data de desativação |
| --- | --- | --- |
| [0.10.1-preview](#0.10.1-preview) |22 janeiro de 2019 |--- |
| [0.10.0-preview](#0.10.0-preview) |18 de dezembro de 2018 |--- |
| [0.9.1-preview](#0.9.1-preview) |18 de outubro de 2018 |--- |


## <a name="faq"></a>Perguntas frequentes

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também
Para saber mais sobre a API de Tabela do Azure CosmosDB, consulte [Introdução ao Azure Cosmos DB: API de Tabela](table-introduction.md). 
