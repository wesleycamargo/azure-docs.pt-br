---
title: Execute a primeira consulta ao Resource Graph usando o Azure PowerShell
description: Este artigo orienta você pelas etapas para habilitar o módulo do Resource Graph para o Azure PowerShell e executar a primeira consulta.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: quickstart
ms.service: resource-graph
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 7a706c65fbdd64103854b02e891c96cbf927f8a1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46962531"
---
# <a name="run-your-first-resource-graph-query-using-azure-powershell"></a>Execute a primeira consulta ao Resource Graph usando o Azure PowerShell

A primeira etapa para usar o Azure Resource Graph é garantir que o módulo para o Azure PowerShell está instalado. Este início rápido orienta você pelo processo de adicionar o módulo à instalação do Azure PowerShell. Você pode usar o módulo com o Azure PowerShell instalado localmente ou por meio do [Azure Cloud Shell](https://shell.azure.com).

No final deste processo, você terá adicionado o módulo à sua instalação do Azure PowerShell de preferência e executado sua primeira consulta ao Resource Graph.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="add-the-resource-graph-module"></a>Adicionar o módulo do Resource Graph

Para habilitar o Azure PowerShell para consultar o Azure Resource Graph, o módulo precisa ser adicionado. Esse módulo funciona sempre que o Azure PowerShell pode ser usado, incluindo no [Cloud Shell](https://shell.azure.com) (tanto autônomo quanto dentro do portal), na [imagem do Docker do Azure PowerShell](https://hub.docker.com/r/azuresdk/azure-powershell/), ou instalado localmente.

1. Verifique se o Azure PowerShell 6.3.0 ou superior está instalado. Se ele ainda não estiver instalado, siga [estas instruções](/powershell/azure/install-azurerm-ps).

1. Verifique se o PowerShellGet está instalado. Se ele não estiver instalado ou atualizado, siga [estas instruções](/powershell/gallery/installing-psget).

1. De um prompt **administrativo** do PowerShell, execute o comando a seguir:

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module AzureRm.ResourceGraph
   ```

1. Valide que o módulo foi importado e é da versão correta (0.1.0):

   ```azurepowershell-interactive
   # Get a list of commands for the imported AzureRm.Graph module
   Get-Command -Module 'AzureRm.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>Executar a primeira consulta ao Resource Graph

Agora que o módulo do Azure PowerShell foi adicionado ao seu ambiente de preferência, é hora de experimentar uma consulta simples ao Resource Graph. A consulta retornará os cinco primeiros recursos do Azure com o **Nome** e o **Tipo de recurso** de cada recurso.

1. Execute a primeira consulta ao Azure Resource Graph usando o cmdlet `Search-AzureRmGraph`:

   ```azurepowershell-interactive
   # Login first with Connect-AzureRmAccount if not using Cloud Shell

   # Run Azure Resource Graph query
   Search-AzureRmGraph -Query 'project name, type | limit 5'
   ```

   > [!NOTE]
   > Devido a essa consulta de exemplo não fornecer um modificador de classificação, tal como `order by`, executar essa consulta várias vezes provavelmente produzirá um conjunto diferente de recursos por solicitação.

1. Atualizar a consulta para `order by` a propriedade **Name**:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzureRmGraph -Query 'project name, type | limit 5 | order by name asc'
   ```

  > [!NOTE]
  > Assim como ocorre com a primeira consulta, executar esta consulta várias vezes provavelmente produzirá um conjunto diferente de recursos por solicitação. A ordem dos comandos de consulta é importante. Neste exemplo, o `order by` vem após o `limit`. Isso primeiro limitará os resultados da consulta e, em seguida, os ordenará.

1. Atualize a consulta para primeiro `order by` a propriedade **Name** e, em seguida, `limit` aos primeiros 5 resultados:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzureRmGraph -Query 'project name, type | order by name asc | limit 5'
   ```

Quando a consulta final é executada várias vezes, supondo que nada em seu ambiente está sendo alterado, os resultados retornados são consistentes e conforme o esperado – ordenados pela propriedade **Name**, mas ainda limitados aos 5 primeiros resultados.

## <a name="clean-up"></a>Limpar

Se quiser remover o módulo do Resource Graph do seu ambiente do Azure PowerShell, você poderá fazer isso usando o comando a seguir:

```azurepowershell-interactive
# Remove the Resource Graph module from the Azure PowerShell environment
Remove-Module -Name 'AzureRm.ResourceGraph'
```

> [!NOTE]
> Isso não exclui o arquivo de módulo baixado anteriormente. Isso apenas o remove da sessão do PowerShell em execução.

## <a name="next-steps"></a>Próximas etapas

- Obter mais informações sobre a [linguagem da consulta](./concepts/query-language.md)
- Aprender a [explorar recursos](./concepts/explore-resources.md)
- Executar sua primeira consulta com a [CLI do Azure](first-query-azurecli.md)
- Ver exemplos de [consultas iniciais](./samples/starter.md)
- Ver exemplos de [consultas avançadas](./samples/advanced.md)