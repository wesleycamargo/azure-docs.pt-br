---
title: "Exemplo de Script do Azure PowerShell - Atribuir um domínio personalizado para um aplicativo Web | Microsoft Docs"
description: "Exemplo de Script do Azure PowerShell - Atribuir um domínio personalizado para um aplicativo Web"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 356f5af9-f62e-411c-8b24-deba05214103
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 6d25fe8098848fc69470c77e3200bee554c1f875
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2017
---
# <a name="assign-a-custom-domain-to-a-web-app"></a>Atribuir um domínio personalizado para um aplicativo Web

Este script de exemplo cria um aplicativo Web no serviço de aplicativo com seus recursos relacionados e, em seguida, mapeia `www.<yourdomain>` a ele. 

Se necessário, instale o Azure PowerShell usando a instrução encontrada no [guia do Azure PowerShell](/powershell/azure/overview) e, em seguida, execute `Login-AzureRmAccount` para criar uma conexão com o Azure. Além disso, você precisa ter acesso à página de configuração do DNS do registrador de domínios.

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[principal](../../../powershell_scripts/app-service/map-custom-domain/map-custom-domain.ps1?highlight=1 "Atribuir um domínio personalizado para um aplicativo Web")]

## <a name="clean-up-deployment"></a>Limpar implantação 

Após a execução da amostra de script, o comando a seguir pode ser usado para remover o grupo de recursos, o aplicativo Web e todos os recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | Cria um Plano do Serviço de Aplicativo. |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Cria um aplicativo web. |
| [Set-AzureRmAppServicePlan](/powershell/module/azurerm.websites/set-azurermappserviceplan) | Modifica um plano do Serviço de Aplicativo para alterar seu tipo de preço. |
| [Set-AzureRmWebApp](/powershell/module/azurerm.websites/set-azurermwebapp) | Modifica a configuração de um aplicativo Web. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o módulo do Azure PowerShell, confira a [Documentação do Azure PowerShell](/powershell/azure/overview).

Exemplos adicionais do Azure PowerShell para Aplicativos Web do Serviço de Aplicativo do Azure podem ser encontrados nos [exemplos do Azure PowerShell](../app-service-powershell-samples.md).
