---
title: Consultas avançadas de gráfico de recursos do Azure
description: Use o gráfico de recursos do Azure para executar algumas consultas avançadas.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: a9281bc333b3edd12501a6813f12b9e7ad1bf59f
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219260"
---
# <a name="advanced-resource-graph-queries"></a>Consultas do Microsoft Azure Active Directory Graph

A primeira etapa para consultas de reconhecimento com o Microsoft Azure Resource Graph é uma compreensão básica da [Linguagem de Consulta](../concepts/query-language.md). Se você já não estiver familiarizado com o [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md), é recomendável revisar os conceitos básicos para entender como compor solicitações para os recursos que está procurando.

Vamos percorrer as seguintes consultas avançadas:

> [!div class="checklist"]
> - [Obter o tamanho e a capacidade VMSS](#vmss-capacity)
> - [Listar todos os nomes de marca](#list-all-tags)
> - [Máquinas virtuais correspondidas por regex](#vm-regex)

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="language-support"></a>Suporte ao idioma

A CLI do Azure (por meio de uma extensão) e o Azure PowerShell (por meio de um módulo) suportam o Gráfico de Recursos do Azure. Antes de executar qualquer uma das consultas a seguir, verifique se seu ambiente está preparado. Veja [CLI do Azure](../first-query-azurecli.md#add-the-resource-graph-extension) e [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) para obter as etapas instalar e validar o ambiente shell de escolha.

## <a name="vmss-capacity"></a>Obter o tamanho e a capacidade VMSS

Essa consulta procura por recursos VMSS (conjunto de dimensionamento de máquinas virtuais) e obtém os diversos detalhes, incluindo o tamanho da máquina virtual e a capacidade do conjunto de dimensionamento. Essas informações usam a função `toint()` para converter a capacidade em um número para que ela possa ser classificada. Isso também renomeia os valores retornados para as propriedades nomeadas personalizadas.

```Query
where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

```azurecli-interactive
az graph query -q "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

```powershell
Search-AzureRmGraph -Query "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

## <a name="list-all-tags"></a>Listar todos os nomes de marca

Essa consulta começa com a marca e cria um objeto JSON listando todos os nomes de marca exclusivos e seus tipos correspondentes.

```Query
project tags
| summarize buildschema(tags)
```

```azurecli-interactive
az graph query -q "project tags | summarize buildschema(tags)"
```

```powershell
Search-AzureRmGraph -Query "project tags | summarize buildschema(tags)"
```

## <a name="vm-regex"></a>Máquinas virtuais correspondidas por regex

Essa consulta procura por máquinas virtuais que correspondem a um [expressão regular](/dotnet/standard/base-types/regular-expression-language-quick-reference) (conhecida como _regex_).
O **corresponde ao regex @** nos permite definir o regex para correspondência, que é **^Contoso(.*)[0-9]+$**. Essa definição de regex é explicada como:

- `^` - a correspondência deve começar no início da cadeia de caracteres.
- `Contoso` - a cadeia de caracteres principal para a qual estamos fazendo a correspondência (diferencia maiúsculas de minúsculas).
- `(.*)` - uma correspondência de subexpressão:
  - `.` - corresponde a qualquer caractere único (exceto uma nova linha).
  - `*` - corresponde ao elemento anterior nenhuma ou mais vezes.
- `[0-9]` - correspondência de grupo de caracteres para os números de 0 a 9.
- `+` - corresponde ao elemento anterior uma ou mais vezes.
- `$` - a correspondência do elemento anterior deve ocorrer no final da cadeia de caracteres.

Após a correspondência por nome, a consulta projeta o nome e ordena por nome em ordem crescente.

```Query
where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$'
| project name
| order by name asc
```

```azurecli-interactive
az graph query -q "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

```powershell
Search-AzureRmGraph -Query "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

## <a name="next-steps"></a>Próximas etapas

- Ver exemplos de [Consultas iniciais](starter.md)
- Saiba mais sobre a [linguagem de consulta](../concepts/query-language.md)
- Saiba [explorar recursos](../concepts/explore-resources.md)