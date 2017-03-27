---
title: Exemplo de Script do Azure PowerShell - Dimensionar um aplicativo Web manualmente | Microsoft Docs
description: Exemplo de Script do Azure PowerShell - Dimensionar um aplicativo Web manualmente
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: de5d4285-9c7d-4735-a695-288264047375
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/08/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: d51e36e673cf0045ced11cf7b2643c511ddc2820
ms.lasthandoff: 03/10/2017

---

# <a name="scale-a-web-app-manually"></a>Dimensionar manualmente um aplicativo Web

Nesse cenário, você aprenderá a criar um grupo de recursos, o aplicativo Web e o Plano do Serviço de Aplicativo. Em seguida, dimensionará o Plano do Serviço de Aplicativo de uma única instância para várias instâncias.

Antes de executar esse script, certifique-se de que uma conexão com o Azure foi criada usando o cmdlet `Login-AzureRmAccount`.

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[principal](../../../powershell_scripts/app-service/scale-manual/scale-manual.ps1 "Dimensionar manualmente um aplicativo Web")]

## <a name="clean-up-deployment"></a>Limpar implantação 

Após a execução do exemplo de script, o comando a seguir pode ser usado para remover o Grupo de Recursos, o aplicativo Web e todos os recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Resources/v3.5.0/new-azurermresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzureRmAppServicePlan](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermappserviceplan) | Cria um Plano do Serviço de Aplicativo. |
| [New-AzureRmWebApp](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermwebapp) | Cria um aplicativo web. |
| [Set-AzureRmWebApp](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/set-azurermwebapp) | Modifica a configuração de um aplicativo Web. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o módulo do Azure PowerShell, confira a [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).

Exemplos adicionais do Azure PowerShell para Aplicativos Web do Serviço de Aplicativo do Azure podem ser encontrados nos [exemplos do Azure PowerShell](../app-service-powershell-samples.md).

