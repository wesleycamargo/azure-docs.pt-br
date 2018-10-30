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
ms.openlocfilehash: 001805aaf87ed6c3481a8ad8378cdc22ef74d274
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49646381"
---
# <a name="run-your-first-resource-graph-query-using-azure-powershell"></a>Execute a primeira consulta ao Resource Graph usando o Azure PowerShell

A primeira etapa para usar o Azure Resource Graph é garantir que o módulo do Azure PowerShell esteja instalado. Este início rápido orienta você pelo processo de adicionar o módulo à instalação do Azure PowerShell.

No final desse processo, você terá adicionado o módulo à instalação do Azure PowerShell de preferência e executado sua primeira consulta ao Resource Graph.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="add-the-resource-graph-module"></a>Adicionar o módulo do Resource Graph

Para habilitar o Azure PowerShell para consultar o Azure Resource Graph, o módulo precisa ser adicionado. Esse módulo pode ser usado com o Windows PowerShell instalado localmente e o PowerShell Core ou com a [imagem do Docker do Azure PowerShell](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Requisitos base

O módulo Gráfico de Recursos do Azure requer o seguinte software:

- Azure PowerShell 6.3.0 ou superior. Se ainda não estiver instalado, siga [estas instruções](/powershell/azure/install-azurerm-ps).

  - Para o PowerShell Core, use a versão **Az** do módulo do Azure PowerShell.

  - Para o PowerShell Core, use a versão **AzureRm** do módulo do Azure PowerShell.

  > [!NOTE]
  > Atualmente, não é recomendável instalar o módulo no Cloud Shell.

- PowerShellGet 2.0.1 ou superior. Se ele não estiver instalado ou atualizado, siga [estas instruções](/powershell/gallery/installing-psget).

### <a name="cloud-shell"></a>Cloud Shell

Para adicionar o módulo do Resource Graph do Azure no Cloud Shell, siga as instruções abaixo para o PowerShell Core.

### <a name="powershell-core"></a>PowerShell Core

O módulo Gráfico de Recursos para o PowerShell Core é **Az.ResourceGraph**.

1. De um prompt **administrativo** do PowerShell Core, execute o comando a seguir:

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. Valide que o módulo foi importado e é da versão correta (0.3.0):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.ResourceGraph module
   Get-Command -Module 'Az.ResourceGraph' -CommandType 'Cmdlet'
   ```

1. Habilite aliases compatíveis com versões anteriores para **Az** a **AzureRm** com o seguinte comando:

   ```azurepowershell-interactive
   # Enable backwards alias compatibility
   Enable-AzureRmAlias
   ```

### <a name="windows-powershell"></a>Windows PowerShell

O módulo Gráfico de Recursos para o Windows PowerShell é **AzureRm.ResourceGraph**.

1. De um prompt **administrativo** do Windows PowerShell, execute o comando a seguir:

   ```powershell
   # Install the Resource Graph (prerelease) module from PowerShell Gallery
   Install-Module -Name AzureRm.ResourceGraph -AllowPrerelease
   ```

1. Valide que o módulo foi importado e é da versão correta (0.1.1 – versão prévia):

   ```powershell
   # Get a list of commands for the imported AzureRm.ResourceGraph module
   Get-Command -Module 'AzureRm.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>Executar a primeira consulta ao Resource Graph

Com o módulo do Azure PowerShell adicionado ao seu ambiente de preferência, é hora de experimentar uma consulta simples ao Resource Graph. A consulta retornará os cinco primeiros recursos do Azure com o **Nome** e o **Tipo de recurso** de cada recurso.

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

1. Atualize a consulta para, primeiro, `order by` a propriedade **Name** e depois `limit` conforme os primeiros cinco resultados:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzureRmGraph -Query 'project name, type | order by name asc | limit 5'
   ```

Quando a consulta final for executada várias vezes, supondo que nada em seu ambiente está sendo alterado, os resultados retornados são consistentes e conforme o esperado – ordenados pela propriedade **Name**, mas ainda limitados aos cinco primeiros resultados.

## <a name="cleanup"></a>Limpeza

Se quiser remover o módulo do Resource Graph do seu ambiente do Azure PowerShell, você poderá fazer isso usando o comando a seguir:

```powershell
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
- Veja exemplos de [Consultas avançadas](./samples/advanced.md)
- Fornecer comentários sobre o [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)