---
title: Recursos e SDK do .NET da API de Tabela do Azure Cosmos DB
description: Saiba tudo sobre a API de Tabela do Azure Cosmos DB, incluindo as datas de lançamento, as datas de desativação e as alterações feitas entre cada versão.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/17/2018
ms.openlocfilehash: 506f623fe928cf122a16630844996c981cc20e9e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58791721"
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>API .NET de Tabela do Azure Cosmos DB: Downloads e notas sobre a versão

> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Baixe o SDK**|[NuGet](https://aka.ms/acdbtablenuget)|
|**Documentação da API**|[Documentação de referência de API .NET](https://aka.ms/acdbtableapiref)|
|**Início rápido**|[Banco de dados do Azure Cosmos DB: Compilar um aplicativo com o .NET e a API de Tabela](create-table-dotnet.md)|
|**Tutorial**|[Banco de dados do Azure Cosmos DB: Desenvolver com a API de Tabela no .NET](tutorial-develop-table-dotnet.md)|
|**Framework atualmente com suporte**|[Microsoft .NET Framework 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> Se você criou uma conta da API de Tabela durante a versão prévia, crie uma [nova conta da API de Tabela](create-table-dotnet.md#create-a-database-account) para trabalhar com os SDKs da API de Tabela disponíveis para o público em geral.
>

## <a name="release-notes"></a>Notas de versão

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0

* Correções de bug

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0

* Adição de suporte de gravação de várias regiões
* Correção das dependências de pacotes NuGet em Microsoft.Azure.DocumentDB, Microsoft.OData.Core, Microsoft.OData.Edm e Microsoft.Spatial

### <a name="a-name113113"></a><a name="1.1.3"/>1.1.3

* Corrigidas as dependências de pacote NuGet no Microsoft.Azure.Storage.Common e Microsoft.Azure.DocumentDB.
* Correções de bugs na serialização de tabelas quando o JsonConvert.DefaultSettings é configurado.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Validação adicional para ETAGs malformadas no Modo Direto.
* Bug de consulta LINQ fixado no Modo de Gateway.
* As APIs síncronas agora são executadas no pool de threads com SynchronizationContext.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Adicionar TableQueryMaxItemCount, TableQueryEnableScan, TableQueryMaxDegreeOfParallelism e TableQueryContinuationTokenLimitInKb a TableRequestOptions
* Correções de bug

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* Versão de disponibilidade geral

### <a name="a-name010-preview090-preview"></a><a name="0.1.0-preview"/>0.9.0-preview

* Versão prévia inicial

## <a name="release-and-retirement-dates"></a>Datas de lançamento e desativação

A Microsoft notifica pelo menos **12 meses** antes de desativar um SDK, a fim de realizar uma transição tranquila para uma versão mais recente/com suporte.

O pacote de versão prévia [WindowsAzure.Storage-PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) foi preterido e substituído pelo pacote [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table). O SDK WindowsAzure.Storage-PremiumTable será desativado em 15 de novembro de 2018, quando as solicitações para o SDK obsoleto não serão permitidas. A biblioteca `Microsoft.Azure.CosmosDB.Table` só está disponível atualmente para o .NET Standard, ela ainda não está disponível para o .NET Core.

Os novos recursos, funcionalidades e otimizações são adicionados apenas ao SDK atual. Portanto, recomendamos que você atualize sempre que possível para a versão do SDK mais recente. 

Qualquer solicitação feita ao BD Cosmos do Azure usando um SDK obsoleto é rejeitada pelo serviço.
<br/>

| Versão | Data do lançamento | Data de desativação |
| --- | --- | --- |
| [2.1.0](#2.1.0) |22 janeiro de 2019|--- |
| [2.0.0](#2.0.0) |26 de setembro de 2018|--- |
| [1.1.3](#1.1.3) |17 de julho de 2018|--- |
| [1.1.1](#1.1.1) |26 de março de 2018|--- |
| [1.1.0](#1.1.0) |21 de fevereiro de 2018|--- |
| [1.0.0](#1.0.0) |15 de novembro de 2017|--- |
| 0.9.0-preview |11 de novembro de 2017 |--- |

## <a name="troubleshooting"></a>solução de problemas

Se você receber o erro 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

ao tentar usar o pacote Microsoft.Azure.CosmosDB.Table NuGet, você tem duas opções para corrigir o problema:

* Use o Console de Gerenciamento de Pacote para instalar o pacote Microsoft.Azure.CosmosDB.Table e suas dependências. Para fazer isso, digite o seguinte no Console do Gerenciador de Pacotes para sua solução. 

    ```powershell
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```

    
* Usando a ferramenta de gerenciamento de pacotes NuGet preferencial, instale o pacote NuGet Microsoft.Azure.Storage.Common antes de instalar o Microsoft.Azure.CosmosDB.Table.

## <a name="faq"></a>Perguntas frequentes

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também

Para saber mais sobre a API de Tabela do Azure CosmosDB, consulte [Introdução ao Azure Cosmos DB: API de Tabela](table-introduction.md). 