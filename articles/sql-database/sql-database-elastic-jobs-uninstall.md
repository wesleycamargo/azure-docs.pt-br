<properties 
	pageTitle="Veja como desinstalar a ferramenta de trabalho de banco de dados elástico" 
	description="Veja como desinstalar a ferramenta de trabalho de banco de dados elástico" 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/25/2015" 
	ms.author="sidneyh"/>

# Como desinstalar os componentes de trabalho do banco de dados elástico

Se ocorrer uma falha ao tentar instalar o serviço do trabalho de banco de dados elástico, exclua o grupo de recursos do serviço.

## Para desinstalar os componentes do serviço

1. Abra o [portal de visualização do Azure](https://ms.portal.azure.com/).
2. Navegue até a assinatura que contém o trabalho elástico.
3. Clique em **Procurar** e clique em **Grupos de recursos**.
4. Selecione o grupo de recursos chamado "\_\_ElasticDatabaseJob".
5. Exclua o grupo de recursos.

Como alternativa, use este script do PowerShell:

1. Inicie uma [janela do PowerShell do Microsoft Azure](../powershell-install-configure.md). 
2. Verifique se você está usando o SDK do PowerShell versão 0.8.10 ou posterior.
3. Execute o script:

		$ResourceGroupName = "__ElasticDatabaseJob"
		Switch-AzureMode AzureResourceManager
		
		$resourceGroup = Get-AzureResourceGroup -Name $ResourceGroupName
		if(!$resourceGroup)
		{
		    Write-Host "The Azure Resource Group: $ResourceGroupName has already been deleted.  Elastic database job is uninstalled."
		    return
		}
		
		Write-Host "Removing the Azure Resource Group: $ResourceGroupName.  This may take a few minutes.”
		Remove-AzureResourceGroup -Name $ResourceGroupName -Force
		Write-Host "Completed removing the Azure Resource Group: $ResourceGroupName.  Elastic database job is now uninstalled."

## Próximas etapas

Para reinstalar os trabalhos do banco de dados elástico, confira [Instalando o serviço de trabalho do banco de dados elástico](sql-database-elastic-jobs-service-installation.md)

Para uma visão geral do serviço de trabalho do banco de dados elástico, confira [Visão geral dos trabalhos elásticos](sql-database-elastic-jobs-overview.md).

<!--Image references-->
[1]: ./media/sql-database-elastic-job-uninstall/
 

<!---HONumber=August15_HO6-->