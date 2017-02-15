---
title: "Veja como desinstalar a ferramenta de trabalho de banco de dados elástico"
description: "Como desinstalar a ferramenta de trabalho de banco de dados elástico"
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: bfc9d820-edbd-4fca-bfbf-1f339cfcc448
ms.service: sql-database
ms.workload: sql-database
ms.custom: multiple databases
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: aa438680348748a771f9016ecdcc68372c6c5e86


---
# <a name="uninstall-elastic-database-jobs-components"></a>Desinstalar componentes de trabalhos de banco de dados elástico
**trabalhos do banco de dados elástico** podem ser desinstalados usando o Portal ou o PowerShell.

## <a name="uninstall-elastic-database-jobs-components-using-the-azure-portal"></a>Desinstalar componentes de trabalhos de banco de dados elástico usando o Portal do Azure
1. Abra o [Portal do Azure](https://portal.azure.com/).
2. Navegue até a assinatura que contém os componentes de **trabalhos do banco de dados elástico** , ou seja, a assinatura na qual os componentes do banco de dados elástico foram instalados.
3. Clique em **Procurar** e clique em **Grupos de recursos**.
4. Selecione o grupo de recursos chamado "__ElasticDatabaseJob".
5. Exclua o grupo de recursos.

## <a name="uninstall--elastic-database-jobs-components-using-powershell"></a>Desinstalar componentes de trabalhos de banco de dados elástico usando o PowerShell
1. Inicie uma janela de comando do Microsoft Azure PowerShell e navegue até o subdiretório ferramentas na pasta Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x: digite **cd tools**.
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools
2. Execute o script do PowerShell .\UninstallElasticDatabaseJobs.ps1.
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\UninstallElasticDatabaseJobs.ps1   PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\UninstallElasticDatabaseJobs.ps1

Ou simplesmente execute o script a seguir, presumindo valores padrão em que os valores são usados na instalação dos componentes:

        $ResourceGroupName = "__ElasticDatabaseJob"
        Switch-AzureMode AzureResourceManager

        $resourceGroup = Get-AzureResourceGroup -Name $ResourceGroupName
        if(!$resourceGroup)
        {
            Write-Host "The Azure Resource Group: $ResourceGroupName has already been deleted.  Elastic database job components are uninstalled."
            return
        }

        Write-Host "Removing the Azure Resource Group: $ResourceGroupName.  This may take a few minutes.”
        Remove-AzureResourceGroup -Name $ResourceGroupName -Force
        Write-Host "Completed removing the Azure Resource Group: $ResourceGroupName.  Elastic database job compoennts are now uninstalled."

## <a name="next-steps"></a>Próximas etapas
Para reinstalar trabalhos de banco de dados elástico, confira [Instalando o serviço de trabalho de banco de dados elástico](sql-database-elastic-jobs-service-installation.md)

Para uma visão geral de trabalhos de banco de dados elástico, consulte [Visão geral de trabalhos de banco de dados elástico](sql-database-elastic-jobs-overview.md).

<!--Image references-->





<!--HONumber=Nov16_HO3-->


