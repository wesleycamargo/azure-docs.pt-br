---
title: Exemplo de Script do Azure PowerShell - Fazer backup de um aplicativo Web | Microsoft Docs
description: Exemplo de script do Azure PowerShell - Fazer backup de um aplicativo Web
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: fc755f82-ca3e-4532-b251-690b699324d6
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 10/30/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: f1f08ca8517d01a6d72f1f8a18514ea3f851ff14
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2019
ms.locfileid: "56587898"
---
# <a name="back-up-a-web-app-using-powershell"></a>Fazer backup de um aplicativo Web usando o PowerShell

Este script de exemplo cria um aplicativo Web no Serviço de Aplicativo com seus recursos relacionados e então cria um backup único para ele. 

Se necessário, instale o Azure PowerShell usando a instrução encontrada no [guia do Azure PowerShell](/powershell/azure/overview) e, em seguida, execute `Connect-AzAccount` para criar uma conexão com o Azure. 

## <a name="sample-script"></a>Script de exemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-onetime/backup-onetime.ps1?highlight=1-5 "Back up a web app")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Após a execução da amostra de script, o comando a seguir pode ser usado para remover o grupo de recursos, o aplicativo Web e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Cria uma conta de armazenamento. |
| [New-AzStorageContainer](/powershell/module/az.storage/new-AzStoragecontainer) | Cria um contêiner de armazenamento do Azure. |
| [New-AzStorageContainerSASToken](/powershell/module/az.storage/new-AzStoragecontainersastoken) | Gera um token SAS para um contêiner de armazenamento do Azure.  |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Cria um Plano do Serviço de Aplicativo. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Cria um aplicativo web. |
| [New-AzWebAppBackup](/powershell/module/az.websites/new-azwebappbackup) | Cria um backup de um aplicativo Web. |
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Obtém uma lista de backups para um aplicativo Web. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o módulo do Azure PowerShell, confira a [Documentação do Azure PowerShell](/powershell/azure/overview).

Exemplos adicionais do Azure PowerShell para Aplicativos Web do Serviço de Aplicativo do Azure podem ser encontrados nos [exemplos do Azure PowerShell](../samples-powershell.md).
