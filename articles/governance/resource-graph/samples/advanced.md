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
ms.openlocfilehash: 6798ef3f81ff8e733886fccc56f6474afc2ee5e9
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46992483"
---
# <a name="advanced-resource-graph-queries"></a>Consultas do Microsoft Azure Active Directory Graph

A primeira etapa para consultas de reconhecimento com o Microsoft Azure Resource Graph é uma compreensão básica da [Linguagem de Consulta](../concepts/query-language.md). Se você ainda não está familiarizado com o Idioma de Consulta Kusto (KQL), é recomendável revisar os conceitos básicos para entender como compor solicitações para os recursos que você está procurando.

Vamos percorrer as seguintes consultas avançadas:

> [!div class="checklist"]
> - [Localizar conjuntos de dimensionamento de máquina virtual que não tem criptografia de disco ou a criptografia não está habilitado.](#vmss-not-encrypted)
> - [Listar todos os nomes de marca](#list-all-tags)

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="language-support"></a>Suporte ao idioma

A CLI do Azure (por meio de uma extensão) e o Azure PowerShell (por meio de um módulo) suportam o Gráfico de Recursos do Azure. Antes de executar qualquer uma das consultas a seguir, verifique se seu ambiente está preparado. Veja [CLI do Azure](../first-query-azurecli.md#add-the-resource-graph-extension) e [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) para obter as etapas instalar e validar o ambiente shell de escolha.

## <a name="vmss-not-encrypted"></a>Localizar conjuntos de dimensionamento de máquina virtual que não tem criptografia de disco ou a criptografia não está habilitado

Esta consulta procura recursos do conjunto de dimensionamento de máquina virtual, expande os detalhes de extensões, examina a extensão **Tipo** e **propriedades EncryptionOperation**, resume e retorna os recursos em que a contagem de configurações esperadas foram 0.

```Query
where type =~ 'microsoft.compute/virtualmachinescalesets'
| project id, extension = properties.virtualMachineProfile.extensionProfile.extensions
| where extension !has 'AzureDiskEncryption' or extension !has '"EnableEncryption"'
| project id
```

```azurecli-interactive
az graph query -q "where type =~ 'microsoft.compute/virtualmachinescalesets' | project id, extension = properties.virtualMachineProfile.extensionProfile.extensions | where extension !has 'AzureDiskEncryption' or extension !has '"EnableEncryption"' | project id"
```

```azurepowershell-interactive
Search-AzureRmGraph -Query "where type =~ 'microsoft.compute/virtualmachinescalesets' | project id, extension = properties.virtualMachineProfile.extensionProfile.extensions | where extension !has 'AzureDiskEncryption' or extension !has '"EnableEncryption"' | project id"
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

```azurepowershell-interactive
Search-AzureRmGraph -Query "project tags | summarize buildschema(tags)"
```

## <a name="next-steps"></a>Próximas etapas

- Ver exemplos de [Consultas iniciais](starter.md)
- Saiba mais sobre a [linguagem de consulta](../concepts/query-language.md)
- Saiba [explorar recursos](../concepts/explore-resources.md)