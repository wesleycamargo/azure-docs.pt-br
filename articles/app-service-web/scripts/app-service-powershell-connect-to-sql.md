---
title: Exemplo de script do Azure PowerShell - Conectar um aplicativo Web a um Banco de Dados SQL | Microsoft Docs
description: Exemplo de script do Azure PowerShell - Conectar um aplicativo Web a um Banco de Dados SQL
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 055440a9-fff1-49b2-b964-9c95b364e533
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: f42112b7a97f2ddcb678ba32deca710d9dc66851
ms.lasthandoff: 03/21/2017

---

# <a name="connect-a-web-app-to-a-sql-database"></a>Conectar um aplicativo Web a um Banco de Dados SQL

Nesse cenário, você aprenderá como criar um Banco de Dados SQL do Azure e um aplicativo Web. Em seguida, você vinculará o Banco de Dados SQL ao aplicativo Web usando configurações do aplicativo.

Se necessário, instale o Azure PowerShell usando a instrução encontrada no [guia do Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) e, em seguida, execute `Login-AzureRmAccount` para criar uma conexão com o Azure.

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[principal](../../../powershell_scripts/app-service/connect-to-sql/connect-to-sql.ps1?highlight=13 "Conectar um aplicativo Web a um Banco de Dados SQL")]

## <a name="clean-up-deployment"></a>Limpar implantação 

Após a execução da amostra de script, o comando a seguir pode ser usado para remover o grupo de recursos, o aplicativo Web e todos os recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Resources/v3.5.0/new-azurermresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzureRmAppServicePlan](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermappserviceplan) | Cria um Plano do Serviço de Aplicativo. |
| [New-AzureRmWebApp](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermwebapp) | Cria um aplicativo web. |
| [New-AzureRMSQLServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver) | Cria um servidor de Banco de Dados SQL. |
| [New-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserverfirewallrule) | Cria uma regra de firewall para um servidor do Banco de Dados SQL. |
| [New-AzureRMSQLDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabase) | Cria um banco de dados ou um banco de dados elástico. |
| [Set-AzureRmWebApp](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/set-azurermwebapp) | Modifica a configuração de um aplicativo Web. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o módulo do Azure PowerShell, confira a [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).

Exemplos adicionais do Azure PowerShell para Aplicativos Web do Serviço de Aplicativo do Azure podem ser encontrados nos [exemplos do Azure PowerShell](../app-service-powershell-samples.md).

