<properties 
	pageTitle="Habilitar o diagnóstico nos Serviços de Nuvem do Azure usando o PowerShell | Microsoft Azure" 
	description="Saiba como habilitar o diagnóstico nos serviços de nuvem usando o PowerShell" 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="sbtron" 
	manager="" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="saurabh"/>


# Habilitar o diagnóstico nos Serviços de Nuvem do Azure usando o PowerShell

Você pode coletar dados de diagnóstico, como logs de aplicativo, contador de desempenho, etc., a partir de um Serviço de Nuvem usando a extensão de Diagnóstico do Azure. Este artigo descreve como habilitar a extensão de Diagnóstico do Azure para um Serviço de Nuvem usando o PowerShell. Consulte [Como instalar e configurar o Azure PowerShell](powershell-install-configure.md) para os pré-requisitos necessários para este artigo.

## Habilitar a extensão de diagnóstico como parte da implantação de um Serviço de Nuvem

Essa abordagem é boa para o tipo de integração contínua de cenários em que a extensão de diagnóstico pode ser habilitada. Você pode habilitar a extensão de diagnóstico como parte da implantação do serviço de nuvem passando o parâmetro *ExtensionConfiguration* para o cmdlet [New-AzureDeployment](https://msdn.microsoft.com/library/azure/mt589089.aspx). O parâmetro *ExtensionConfiguration* obtém uma série de configurações de diagnóstico que podem ser criadas usando o cmdlet [New-AzureServiceDiagnosticsExtensionConfig](https://msdn.microsoft.com/library/azure/mt589168.aspx).

O exemplo a seguir mostra como você pode habilitar o diagnóstico para um serviço de nuvem com um WebRole e WorkerRole, cada um com uma configuração de diagnóstico diferente.

	$service_name = "MyService"
	$service_package = "CloudService.cspkg"
	$service_config = "ServiceConfiguration.Cloud.cscfg"
	$diagnostics_storagename = "myservicediagnostics"
	$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
	$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

	$primary_storagekey = (Get-AzureStorageKey -StorageAccountName "$diagnostics_storagename").Primary
	$storage_context = New-AzureStorageContext -StorageAccountName $diagnostics_storagename -StorageAccountKey $primary_storagekey

	$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -Storage_context $storageContext -DiagnosticsConfigurationPath $webrole_diagconfigpath
	$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -StorageContext $storage_context -DiagnosticsConfigurationPath $workerrole_diagconfigpath
	  
	 
	New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig) 



## Habilitar extensão de diagnóstico em um Serviço de Nuvem existente

Você pode usar o cmdlet [Set-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589140.aspx) para habilitar o diagnóstico em um Serviço de Nuvem que já está em execução.


	$service_name = "MyService"
	$diagnostics_storagename = "myservicediagnostics"
	$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
	$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
	$primary_storagekey = (Get-AzureStorageKey -StorageAccountName "$diagnostics_storagename").Primary
	$storage_context = New-AzureStorageContext -StorageAccountName $diagnostics_storagename -StorageAccountKey $primary_storagekey
 
	Set-AzureServiceDiagnosticsExtension -StorageContext $storage_context -DiagnosticsConfigurationPath $webrole_diagconfigpath -ServiceName $service_name -Slot Production -Role "WebRole" 
	Set-AzureServiceDiagnosticsExtension -StorageContext $storage_context -DiagnosticsConfigurationPath $workerrole_diagconfigpath -ServiceName $service_name -Slot Production -Role "WorkerRole"
 

## Obter a configuração de extensão de diagnóstico atual
Usar o cmdlet [Get-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589204.aspx) para obter a configuração de diagnóstico atual para um serviço de nuvem.
	
	Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"

## Remover extensão de diagnóstico
Para desativar o diagnóstico em um serviço de nuvem, você pode usar o cmdlet [Remove-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589183.aspx).

	Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"

Se você tiver habilitado a extensão de diagnóstico usando o *Set-AzureServiceDiagnosticsExtension* ou o *New-AzureServiceDiagnosticsExtensionConfig* sem o parâmetro *Função*, pode remover a extensão usando *Remove-AzureServiceDiagnosticsExtension* sem o parâmetro *Função*. Se o parâmetro *Função* tiver sido usado ao habilitar a extensão, ele também deverá ser usado ao removê-la.

Para remover a extensão de diagnóstico de cada função individual:

	Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"


## Próximas etapas

- Para obter orientação adicional sobre como usar o diagnóstico do Azure e outras técnicas para solucionar problemas, consulte [Habilitar o Diagnóstico nos Serviços de Nuvem do Azure e Máquinas Virtuais](cloud-services-dotnet-diagnostics.md).
- O [Esquema de Configuração de Diagnóstico](https://msdn.microsoft.com/library/azure/dn782207.aspx) explica as várias opções de configurações de XML para a extensão de diagnóstico.
- Para saber como habilitar a extensão de diagnóstico para Máquinas Virtuais, consulte [Criar uma Máquina Virtual do Windows com monitoramento e diagnóstico usando o Modelo do Gerenciador de Recursos do Azure](virtual-machines-extensions-diagnostics-windows-template.md)  

<!---HONumber=Nov15_HO4-->