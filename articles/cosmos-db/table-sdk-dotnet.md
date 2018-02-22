---
title: Recursos e SDK .NET da API de Tabela do Azure CosmosDB | Microsoft Docs
description: "Saiba tudo sobre a API de Tabela do Azure Cosmos DB, incluindo as datas de lançamento, as datas de desativação e as alterações feitas entre cada versão."
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/31/2018
ms.author: mimig
ms.openlocfilehash: 9083ae631f654789b621a1fc62e0b9d31c24f119
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>API .NET de Tabela do Azure CosmosDB: download e notas de versão
> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Baixe o SDK**|[NuGet](https://aka.ms/acdbtablenuget)|
|**Documentação da API**|[Documentação de referência de API .NET](https://aka.ms/acdbtableapiref)|
|**Início rápido**|[Azure CosmosDB: Compilar um aplicativo com o .NET e a API de Tabela](create-table-dotnet.md)|
|**Tutorial**|[Azure Cosmos DB: Desenvolver com a API de Tabela no .NET](tutorial-develop-table-dotnet.md)|
|**Framework atualmente com suporte**|[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)|

> [!IMPORTANT]
> Se você criou uma conta da API de Tabela durante a versão prévia, crie uma [nova conta da API de Tabela](create-table-dotnet.md#create-a-database-account) para trabalhar com os SDKs da API de Tabela disponíveis para o público em geral.
>

## <a name="release-notes"></a>Notas de versão

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Adicionar TableQueryMaxItemCount, TableQueryEnableScan, TableQueryMaxDegreeOfParallelism e TableQueryContinuationTokenLimitInKb a TableRequestOptions
* Correções de bug

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Versão de disponibilidade geral

### <a name="a-name010-preview090-preview"></a><a name="0.1.0-preview"/>0.9.0-preview
* Versão prévia inicial

## <a name="release-and-retirement-dates"></a>Datas de lançamento e desativação
A Microsoft notifica pelo menos **12 meses** antes de desativar um SDK, a fim de realizar uma transição tranquila para uma versão mais recente/com suporte.

O pacote de versão prévia [WindowsAzure.Storage-PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) foi preterido e substituído pelo pacote [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table). O SDK WindowsAzure.Storage-PremiumTable será desativado em 15 de novembro de 2018, quando as solicitações para o SDK obsoleto não serão permitidas.

Os novos recursos, funcionalidades e otimizações são adicionados apenas ao SDK atual. Portanto, recomendamos que você atualize sempre que possível para a versão do SDK mais recente. 

Qualquer solicitação feita ao BD Cosmos do Azure usando um SDK obsoleto é rejeitada pelo serviço.
<br/>

| Versão | Data do lançamento | Data de desativação |
| --- | --- | --- |
| [1.0.0](#1.0.0) |15 de novembro de 2017|--- |
| [0.9.0-preview](#0.9.0-preview) |11 de novembro de 2017 |--- |

## <a name="troubleshooting"></a>solução de problemas

Se você receber o erro 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

ao tentar usar o pacote Microsoft.Azure.CosmosDB.Table NuGet, você tem duas opções para corrigir o problema:

* Use o Console de Gerenciamento de Pacote para instalar o pacote Microsoft.Azure.CosmosDB.Table e suas dependências. Para fazer isso, digite o seguinte no Console do Gerenciador de Pacotes para sua solução. 
    ```
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```
    
* Usando a ferramenta de gerenciamento de pacotes Nuget preferencial, instale o pacote Nuget Microsoft.Azure.Storage.Common antes de instalar o Microsoft.Azure.CosmosDB.Table.

## <a name="faq"></a>Perguntas frequentes

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também
Para saber mais sobre a API de Tabela do Azure CosmosDB, consulte [Introdução ao Azure Cosmos DB: API de Tabela](table-introduction.md). 
