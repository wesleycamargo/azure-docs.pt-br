---
title: Alterações de configuração de acesso - HDInsight do Azure ao cluster
description: Saiba mais sobre as alterações ao acesso a campos de configuração de cluster confidenciais.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: 85a6737ee111975d4c7ecf078673280127bfd0fd
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59610182"
---
# <a name="changes-to-cluster-configuration-access"></a>Alterações para o acesso de configuração de cluster

A versão mais recente do SDK do HDInsight do Azure traz algumas alterações importantes para dar suporte a mais acesso refinado baseado em função para obter informações confidenciais. Como parte dessas alterações, alguns **ação pode ser necessária** se você estiver usando um dos métodos afetados.

## <a name="sdk-for-net-500"></a>SDK para .NET 5.0.0

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) serão **não retornar mais parâmetros confidenciais** como chaves de armazenamento (core-site) ou credenciais HTTP (gateway).
    - Para recuperar todas as configurações, incluindo parâmetros confidenciais, use `ConfigurationOperationsExtensions.List` no futuro.  Observe que os usuários com a função 'Reader' não será capazes de usar esse método. Isso permite que um controle granular sobre quais usuários podem acessar informações confidenciais para um cluster. 
    - Para recuperar apenas as credenciais de gateway HTTP, use `ClusterOperationsExtensions.GetGatewaySettings`. 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) Agora é preterido e foi substituído pelo `ClusterOperationsExtensions.UpdateGatewaySettings`. 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) e [ `DisableHttp` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) agora são preteridos. HTTP está sempre habilitada, portanto, esses métodos não são mais necessários.