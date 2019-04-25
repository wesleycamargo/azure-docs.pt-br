---
title: Exemplos de consulta inicial
description: Use o Azure Resource Graph para executar algumas consultas iniciais, incluindo contagem de recursos, ordenação de recursos ou por uma marca específica.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/04/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 2ba48e2a21bdee0c5698bdfa314dd3bf462c1c7e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59267762"
---
# <a name="starter-resource-graph-queries"></a>Consultas do Microsoft Azure Active Directory Graph

A primeira etapa para consultas de reconhecimento com o Microsoft Azure Resource Graph é uma compreensão básica da [Linguagem de Consulta](../concepts/query-language.md). Se você já não estiver familiarizado com o [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md), é recomendável revisar os conceitos básicos para entender como compor solicitações para os recursos que está procurando.

Vamos percorrer as seguintes consultas iniciais:

> [!div class="checklist"]
> - [Recursos do Count Azure](#count-resources)
> - [Listar recursos classificados por nome](#list-resources)
> - [Mostrar todas as máquinas virtuais, ordenadas por nome em ordem decrescente](#show-vms)
> - [Mostrar as primeiras cinco máquinas virtuais por nome e tipo do sistema operacional](#show-sorted)
> - [Contagem de máquinas virtuais por tipo de sistema operacional](#count-os)
> - [Mostrar recursos que contêm o armazenamento](#show-storage)
> - [Listar todos os endereços de IP](#list-publicip)
> - [Recursos de contagem que têm endereços IP configurados por assinatura](#count-resources-by-ip)
> - [Listar de recursos com um valor de marca específica](#list-tag)
> - [Listar todas as contas de armazenamento com o valor de marca específica](#list-specific-tag)

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="language-support"></a>Suporte ao idioma

A CLI do Azure (por meio de uma extensão) e o Azure PowerShell (por meio de um módulo) suportam o Gráfico de Recursos do Azure. Antes de executar qualquer uma das consultas a seguir, verifique se seu ambiente está preparado. Veja [CLI do Azure](../first-query-azurecli.md#add-the-resource-graph-extension) e [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) para obter as etapas instalar e validar o ambiente shell de escolha.

## <a name="count-resources"></a>Recursos do Count Azure

Essa consulta retorna o número de recursos do Azure que existem nas assinaturas que você tem acesso. Também é uma boa consulta para validar se o shell de escolha tem os componentes apropriados do Azure Resource Graph instalados e em funcionamento.

```Query
summarize count()
```

```azurecli-interactive
az graph query -q "summarize count()"
```

```azurepowershell-interactive
Search-AzGraph -Query "summarize count()"
```

## <a name="list-resources"></a>Listar recursos classificados por nome

Essa consulta retorna todo tipo de recurso, mas apenas as propriedades **name**, **type** e **location**. Ele usa `order by` para classificar as propriedades pela propriedade **name** em ordem crescente (`asc`).

```Query
project name, type, location
| order by name asc
```

```azurecli-interactive
az graph query -q "project name, type, location | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "project name, type, location | order by name asc"
```

## <a name="show-vms"></a>Mostrar todas as máquinas virtuais, ordenadas por nome em ordem decrescente

Para listar somente as máquinas virtuais (que são do tipo `Microsoft.Compute/virtualMachines`), podemos fazer a correspondência com a propriedade **type** nos resultados. Semelhante à consulta anterior, `desc` alterações a `order by` devem estar em ordem decrescente. O `=~` no tipo de correspondência informa ao Microsoft Azure Active Directory Graph para diferenciar maiusculas e minúsculas.

```Query
project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

```azurecli-interactive
az graph query -q "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

## <a name="show-sorted"></a>Mostrar as primeiras cinco máquinas virtuais por nome e tipo do sistema operacional

Essa consulta usará `limit` para recuperar apenas os cinco registros correspondentes que são ordenados pelo nome. O tipo de recurso do Azure é `Microsoft.Compute/virtualMachines`. `project` informa quais propriedades do Gráfico de Recurso do Azure incluir.

```Query
where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

## <a name="count-os"></a>Contagem de máquinas virtuais por tipo de sistema operacional

Aproveitando a consulta anterior, estamos ainda limitando pelos recursos do Azure do tipo `Microsoft.Compute/virtualMachines`, mas não limitando o número de registros retornados.
Em vez disso, usamos `summarize` e `count()` para definir como agrupar e agregar os valores de propriedade, que neste exemplo é `properties.storageProfile.osDisk.osType`. Para obter um exemplo da aparência dessa cadeia de caracteres no objeto completo, consulte [Explorar recursos - descoberta de máquina virtual](../concepts/explore-resources.md#virtual-machine-discovery).

```Query
where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

Outra maneira de escrever a mesma consulta é `extend` uma propriedade e dê a ele um nome temporário para uso dentro da consulta, nesse caso **sistema operacional**. **Sistema Operacional** em seguida, é usado pelo `summarize` e `count()` do exemplo anterior.

```Query
where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

> [!NOTE]
> Lembre-se que, embora `=~` permite o uso de correspondência, diferencia maiusculas de minúsculas de propriedades (como **properties.storageProfile.osDisk.osType**) na consulta exigem que o caso esteja correto. Se a propriedade for o caso incorreto, ele ainda pode retornar um valor, mas o agrupamento ou resumo seria incorreto.

## <a name="show-storage"></a>Mostrar recursos que contêm o armazenamento

Em vez de definir explicitamente o tipo a ser correspondido, este exemplo de consulta encontrará qualquer recurso do Azure que `contains` a palavra **armazenamento**.

```Query
where type contains 'storage' | distinct type
```

```azurecli-interactive
az graph query -q "where type contains 'storage' | distinct type"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'storage' | distinct type"
```

## <a name="list-publicip"></a>Listar todos os endereços de IP

Semelhante à consulta anterior, encontre tudo o que seja um tipo que contenha a palavra **publicIPAddresses**.
Essa consulta expande esse padrão para incluir resultados em que **properties.ipAddress**
`isnotempty`, retornar somente **properties.ipAddress** e `limit` os resultados aos principais
100. Você talvez precise escapar as cotas dependendo do seu shell escolhido.

```Query
where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| project properties.ipAddress
| limit 100
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

## <a name="count-resources-by-ip"></a>Recursos de contagem que têm endereços IP configurados por assinatura

Usando a consulta de exemplo anterior e adicionando `summarize` e `count()`, obtemos uma lista por assinatura de recursos com endereços IP configurados.

```Query
where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

## <a name="list-tag"></a>Listar de recursos com um valor de marca específica

É possível limitar os resultados por propriedades que não seja o tipo de recurso do Azure, como uma marca. Neste exemplo, estamos filtrando por recursos do Azure com o nome de marca de **Ambiente** que tenha um valor de **Interno**.

```Query
where tags.environment=~'internal'
| project name
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name"
```

Para fornecer também quais marcas o recurso tem e seus valores, adicione a propriedade **tags** à palavra-chave `project`.

```Query
where tags.environment=~'internal'
| project name, tags
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name, tags"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name, tags"
```

## <a name="list-specific-tag"></a>Listar todas as contas de armazenamento com o valor de marca específica

Combine a funcionalidade de filtro do exemplo anterior e filtre o tipo de recurso do Azure pela propriedade **type**. Essa consulta também limita a nossa pesquisa por tipos específicos de recursos do Azure com um valor e nome de marca específicos.

```Query
where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

> [!NOTE]
> Este exemplo usa `==` para a correspondência em vez do `=~` condicional. `==` é uma correspondência que diferencia maiusculas de minúsculas.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [linguagem de consulta](../concepts/query-language.md)
- Saiba [explorar recursos](../concepts/explore-resources.md)
- Veja exemplos de [Consultas avançadas](advanced.md)