<properties 
 pageTitle="Gerenciar o Cache Redis do Azure com o PowerShell do Azure" 
 description="Saiba como executar tarefas administrativas para o Cache Redis do Azure usando o PowerShell do Azure." 
 services="redis-cache" 
   documentationCenter="" 
   authors="Rick-Anderson" 
   writer="Rick-Anderson" 
   manager="wpickett" 
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="all"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="multiple" 
   ms.date="04/23/2015"
   ms.author="riande"/>

# Gerenciar o Cache Redis do Azure com o PowerShell do Azure

Este tutorial fornece um início rápido para criar, atualizar e excluir um Cache Redis do Azure.

## Pré-requisitos ##

Para poder usar o Windows PowerShell com o Gerenciador de Recursos, você deve ter o seguinte:

- Windows PowerShell, versão 3.0 ou 4.0. Para localizar a versão do Windows PowerShell, digite: `$PSVersionTable` e verifique se o valor de `PSVersion` é 3.0 ou 4.0. Para instalar uma versão compatível, confira [Windows Management Framework 3.0 ](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).
	
- PowerShell do Azure, versão 0.8.0 ou posterior. Para instalar a versão mais recente e associá-la à sua assinatura do Azure, consulte [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).

Este tutorial é projetado para iniciantes do Windows PowerShell. Para obter mais informações sobre o Windows PowerShell, consulte [Introdução ao PowerShell do Windows Azure (a página pode estar em inglês)](http://technet.microsoft.com/library/hh857337.aspx).

Para obter ajuda detalhada sobre qualquer cmdlet que você vir neste tutorial, use o cmdlet Get-Help.

	Get-Help <cmdlet-name> -Detailed

Por exemplo, para obter ajuda sobre o cmdlet Add-AzureAccount, digite:

	Get-Help Add-AzureAccount -Detailed

## Um script simples do PowerShell do Azure para o cache Redis  ##

O script a seguir demonstra a criação, a atualização e a exclusão de um cache Redis do Azure.

		# Redis cache operations require mode set to AzureResourceManager.
		Switch-AzureMode AzureResourceManager
		$VerbosePreference = "Continue" 
		
		# Create a new cache.
		$cacheName = "MovieCache91117"
		$location = "West US"
		$resourceGroupName = "Default-Web-WestUS"
		
		$movieCache = New-AzureRedisCache -Location $location -Name $cacheName  -ResourceGroupName $resourceGroupName -Size 250MB -Sku Basic
		
		# Wait until the Cache is provisioned.
		
		for ($i = 0; $i -le 60; $i++)
		{
		    Start-Sleep -s 30
			$cacheGet = Get-AzureRedisCache -ResourceGroupName $resourceGroupName -Name $cacheName
		    if ([string]::Compare("succeeded", $cacheGet[0].ProvisioningState, $True) -eq 0)
		    {       
		        break
		    }
		    If($i -eq 60)
		    {
		        exit
		    }
		}
		
		# Update the access keys
		
		Write-Verbose "PrimaryKey: $($movieCache.PrimaryKey)"
		New-AzureRedisCacheKey -KeyType "Primary" -Name $cacheName  -ResourceGroupName $resourceGroupName -Force
		$cacheKeys = Get-AzureRedisCacheKey -ResourceGroupName $resourceGroupName  -Name $cacheName         
		Write-Verbose "PrimaryKey: $($cacheKeys.PrimaryKey)"
		
		# Use Set-AzureRedisCache to set Redis cache updatable parameters.
		# Set the memory policy to Least Recently Used.
		
		Set-AzureRedisCache -MaxMemoryPolicy AllKeysLRU -Name $cacheName -ResourceGroupName $resourceGroupName
		
		# Delete the cache.
		
		Remove-AzureRedisCache -Name $movieCache.Name -ResourceGroupName $movieCache.ResourceGroupName  -Force 

## Próximas etapas
Para saber mais sobre como usar o PowerShell do Microsoft Azure:
 
- [Cmdlets do Gerenciador de Recursos do Azure (a página pode estar em inglês)](http://go.microsoft.com/fwlink/?LinkID=394765&clcid=0x409): saiba usar os cmdlets no módulo AzureResourceManager.
- [Usando grupos de recursos para gerenciar os recursos do Azure](http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups): aprenda a criar e gerenciar grupos de recursos no Portal de Gerenciamento do Azure.
- [Blog do Azure](http://blogs.msdn.com/windowsazure): obtenha informações sobre os novos recursos no Azure.
- [Blog do Windows PowerShell](http://blogs.msdn.com/powershell): obtenha informações sobre os novos recursos do Windows PowerShell.
- [Blog "Hey, Scripting Guy!" Blog](http://blogs.technet.com/b/heyscriptingguy/): obtenha dicas reais e truques da comunidade.do Windows PowerShell.

<!---HONumber=July15_HO1-->