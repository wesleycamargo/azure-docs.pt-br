---
title: Scripts do PowerShell usando o módulo Az.Search - Azure Search
description: Criar e configurar um serviço Azure Search com o PowerShell. Você pode dimensionar um serviço para cima ou para baixo, gerenciar a administração e chaves de api de consulta e consultar informações do sistema.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: heidist
ms.openlocfilehash: 7a91ad691089ac816b31ebe1fce202110e580f71
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520557"
---
# <a name="manage-your-azure-search-service-with-powershell"></a>Gerencie o serviço de Azure Search com o PowerShell
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [API REST](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [SDK .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Você pode executar scripts e cmdlets do PowerShell no Windows, Linux, ou em [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) para criar e configurar [do Azure Search](https://docs.microsoft.com/azure/search/). O [ **Az.Search** ](https://docs.microsoft.com/powershell/module/az.search/?view=azps-1.4.0#search) estende o módulo [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.4.0) com paridade completa para o [APIs de REST de gerenciamento do Azure Search](https://docs.microsoft.com/rest/api/searchmanagement). Com o Azure PowerShell e **Az.Search**, você pode executar as seguintes tarefas:

> [!div class="checklist"]
> * [Listar todos os serviços de pesquisa em sua assinatura](#list-search-services)
> * [Obter informações sobre um serviço de pesquisa específica](#get-search-service-information)
> * [Criar ou excluir um serviço](#create-or-delete-a-service)
> * Regenerar chaves de API do administrador
> * [Criar ou excluir chaves de api de consulta](#create-or-delete-query-keys)
> * [Dimensionar um serviço, aumentando ou diminuindo réplicas e partições](#scale-replicas-and-partitions)

PowerShell não pode ser usado para alterar o nome, região ou camada do seu serviço. Recursos dedicados são alocados quando um serviço é criado. Alteração do hardware subjacente (local ou tipo de nó) requer um novo serviço. Há não há ferramentas ou APIs para transferência de conteúdo. Todo o gerenciamento de conteúdo é por meio [restante](https://docs.microsoft.com/rest/api/searchservice/) ou [.NET](https://docs.microsoft.com/dotnet/api/?term=microsoft.azure.search) APIs, e se você quiser mover índices, você precisará recriar e recarregá-los em um novo serviço. 

Embora não haja nenhum dedicados comandos do PowerShell para gerenciamento de conteúdo, você pode escrever um script do PowerShell que chama a REST ou .NET para criar e carregar índices. O **Az.Search** módulo por si só não fornece essas operações.

Outras tarefas que não tem suportadas por meio do PowerShell ou qualquer outra API (somente portal) incluem:
+ [Anexar a um recurso de serviços cognitivos](cognitive-search-attach-cognitive-services.md) para [IA sofisticados indexação](cognitive-search-concept-intro.md). Um serviço cognitivo está anexado a um conjunto de qualificações, não uma assinatura ou o serviço.
+ [Soluções de monitoramento de complemento](search-monitor-usage.md#add-on-monitoring-solutions) ou [análise de tráfego de pesquisa](search-traffic-analytics.md) usado para o monitoramento do Azure Search.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>Verificar as versões e carregar módulos

Os exemplos neste artigo são interativos e exigem permissões elevadas. O Azure PowerShell (o **Az** módulo) deve ser instalado. Para obter mais informações, consulte [Instalar o Azure PowerShell](/powershell/azure/overview).

### <a name="powershell-version-check-51-or-later"></a>Verificação de versão do PowerShell (5.1 ou posterior)

PowerShell local deve ser 5.1 ou posterior, em qualquer sistema operacional com suporte.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Carregar o Azure PowerShell

Se você não tiver certeza se **Az** estiver instalado, execute o comando a seguir como uma etapa de verificação. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

Alguns sistemas fazer não carregar automaticamente módulos. Se você receber um erro comando anterior, tente carregar o módulo e se isso falhar, vá até as instruções de instalação para ver se você perdeu uma etapa.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Conectar-se ao Azure com um token de entrada do navegador

Você pode usar suas credenciais de logon no portal para se conectar a uma assinatura no PowerShell. Como alternativa, você pode [autenticar de forma não interativa com uma entidade de serviço](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

```azurepowershell-interactive
Connect-AzAccount
```

Se você tiver várias assinaturas do Azure, defina sua assinatura do Azure. Para ver uma lista de suas assinaturas atuais, execute este comando.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Para especificar a assinatura, execute o comando a seguir. No exemplo a seguir, o nome da assinatura é `ContosoSubscription`.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-all-azure-search-services-in-your-subscription"></a>Listar todos os serviços do Azure Search em sua assinatura

Os comandos a seguir são de [ **Az.Resources**](https://docs.microsoft.com/powershell/module/az.resources/?view=azps-1.4.0#resources), retornando informações sobre os recursos existentes e os serviços já provisionados em sua assinatura. Se você não souber quantos serviços de pesquisa já foram criados, esses comandos retornam essa informação, economizando uma viagem para o portal.

O primeiro comando retorna todos os serviços de pesquisa.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

Na lista de serviços, retorne informações sobre um recurso específico.

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

Resultados devem ser semelhantes à seguinte saída.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alpha-numeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>Importar Az.Search

Comandos do [ **Az.Search** ](https://docs.microsoft.com/powershell/module/az.search/?view=azps-1.4.0#search) não estão disponíveis até que você carrega o módulo.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>Listar todos os comandos de Az.Search

Como uma etapa de verificação, retorne uma lista de comandos fornecidos no módulo.

```azurepowershell-interactive
Get-Command -Module Az.Search
```

Resultados devem ser semelhantes à seguinte saída.

```
CommandType     Name                                Version    Source
-----------     ----                                -------    ------
Cmdlet          Get-AzSearchAdminKeyPair            0.7.1      Az.Search
Cmdlet          Get-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          Get-AzSearchService                 0.7.1      Az.Search
Cmdlet          New-AzSearchAdminKey                0.7.1      Az.Search
Cmdlet          New-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          New-AzSearchService                 0.7.1      Az.Search
Cmdlet          Remove-AzSearchQueryKey             0.7.1      Az.Search
Cmdlet          Remove-AzSearchService              0.7.1      Az.Search
Cmdlet          Set-AzSearchService                 0.7.1      Az.Search
```

## <a name="get-search-service-information"></a>Obter informações do serviço de pesquisa

Após **Az.Search** é importado e você sabe que o grupo de recursos que contém o serviço de pesquisa, execute [Get-AzSearchService](https://docs.microsoft.com/powershell/module/az.search/get-azsearchservice?view=azps-1.4.0) para retornar a definição de serviço, incluindo nome, a região, a camada e a réplica e Contagem de partições.

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

Resultados devem ser semelhantes à seguinte saída.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : West US
Sku               : Standard
ReplicaCount      : 1
PartitionCount    : 1
HostingMode       : Default
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-or-delete-a-service"></a>Criar ou excluir um serviço

[**Novo AzSearchService** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) é usado para [criar um novo serviço de pesquisa](search-create-service-portal.md).

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName "demo-westus" -Name "my-demo-searchapp" -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3
``` 
Resultados devem ser semelhantes à seguinte saída.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Id                : /subscriptions/<alphanumeric-subscription-ID>/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 3
PartitionCount    : 3
HostingMode       : Default
Tags
```     

## <a name="regenerate-admin-keys"></a>Regenerar chaves de administrador

[**Novo AzSearchAdminKey** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) é usada para sobrepor admin [chaves de API](search-security-api-keys.md). Duas chaves admin são criadas com cada serviço para acesso autenticado. As chaves são necessárias em cada solicitação. As duas chaves admin são funcionalmente equivalentes, a concessão de acesso de gravação completo para um serviço de pesquisa com a capacidade de recuperar todas as informações, ou criar e excluir qualquer objeto. Duas chaves existirem para que você pode usar um, substituindo a outra. 

Você só pode regenerar uma de cada vez, especificado como o `primary` ou `secondary` chave. Para um serviço ininterrupto, lembre-se de atualizar todo o código de cliente para usar uma chave secundária enquanto sobrepor a chave primária. Evite alterar as chaves enquanto operações estão em trânsito.

Como você pode esperar, se você regenerar chaves sem atualizar o código do cliente, as solicitações usando a chave antiga falhará. Regenerar todas as novas chaves não prender você permanentemente fora de seu serviço, e você ainda pode acessar o serviço por meio do portal. Depois de regenerar chaves primárias e secundárias, você pode atualizar o código do cliente para usar as novas chaves e operações serão retomadas de acordo.

Valores para as chaves de API são gerados pelo serviço. Você não pode fornecer uma chave personalizada para o Azure Search usar. Da mesma forma, não há nenhum nome definido pelo usuário para as chaves de API do administrador. Referências à chave corrigidas ou cadeias de caracteres `primary` ou `secondary`. 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

Resultados devem ser semelhantes à seguinte saída. As duas chaves são retornadas, mesmo que você altere apenas um de cada vez.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>Criar ou excluir as chaves de consulta

[**Novo AzSearchQueryKey** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchquerykey?view=azps-1.4.0) é usado para criar consulta [chaves de API](search-security-api-keys.md) para acesso somente leitura dos aplicativos clientes para um índice de Azure Search. As chaves de consulta são usadas para autenticar em um índice específico a fim de recuperar os resultados da pesquisa. As chaves de consulta não concedem acesso somente leitura a outros itens no serviço, como um índice, a fonte de dados ou o indexador.

Você não pode fornecer uma chave para o Azure Search usar. Chaves de API são geradas pelo serviço.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>Partições e réplicas de escala

[**Conjunto AzSearchService** ](https://docs.microsoft.com/powershell/module/az.search/set-azsearchservice?view=azps-1.4.0) é usado para [aumentar ou diminuir as réplicas e partições](search-capacity-planning.md) reajustar faturáveis recursos dentro de seu serviço. Aumentar as réplicas ou partições adiciona à sua fatura, que tem ambos encargos fixos e variáveis. Se você tiver uma necessidade temporária para poder de processamento adicional, você pode aumentar as réplicas e partições para lidar com a carga de trabalho. Na área de monitoramento na página Visão geral do portal tem blocos na latência da consulta, consultas por segundo e a limitação, que indica se a capacidade atual é adequada.

Pode levar um tempo para adicionar ou remover a alocação de recursos. Ajustes à capacidade ocorrem em segundo plano, permitindo que cargas de trabalho existentes continuar. Capacidade adicional é usada para solicitações de entrada, assim que estiver pronto, sem nenhuma configuração adicional necessária. 

Removendo a capacidade pode ser prejudicial. Parar todos os trabalhos de indexação e indexador antes de reduzir a capacidade é recomendável para evitar solicitações ignoradas. Se isso não for viável, você pode considerar reduzindo a capacidade de forma incremental, uma réplica e partição por vez, até que os novos níveis de destino são atingidos.

Depois de enviar o comando, não há terminasse metade. Você precisará aguardar até que o comando seja concluído antes de revisar as contagens.

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

Resultados devem ser semelhantes à seguinte saída.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 6
PartitionCount    : 6
HostingMode       : Default
Id                : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```


## <a name="next-steps"></a>Próximas etapas

Criar uma [índice](search-what-is-an-index.md), [consultar um índice](search-query-overview.md) usando o portal, APIs REST ou o SDK do .NET.

* [Criar um índice de Azure Search no portal do Azure](search-create-index-portal.md)
* [Configurar um indexador para carregar dados de outros serviços](search-indexer-overview.md)
* [Consultar um índice do Azure Search usando o Search Explorer no portal do Azure](search-explorer.md)
* [Como usar o Azure Search no .NET](search-howto-dotnet-sdk.md)
