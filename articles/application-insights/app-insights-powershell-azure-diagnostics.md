<properties
    pageTitle="Usando o PowerShell para enviar o Diagnóstico do Azure para o Application Insights | Microsoft Azure"
    description="Automatize a configuração do Diagnóstico do Azure para redirecionar para o Application Insights."
    services="application-insights"
    documentationCenter=".net"
    authors="sbtron"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
	ms.tgt_pltfrm="ibiza" 
    ms.devlang="na"
    ms.topic="get-started-article"
	ms.date="11/17/2015"
    ms.author="awills"/>

# Usando o PowerShell para enviar Diagnósticos do Azure para o Application Insights

O [Microsoft Azure](https://azure.com) pode ser [configurado para enviar o Diagnóstico do Azure](app-insights-azure-diagnostics.md) para o [Visual Studio Application Insights](app-insights-overview.md). O diagnóstico está relacionado aos Serviços de Nuvem do Azure e às VMs do Azure. Eles complementam a telemetria que você envia de um aplicativo usando o SDK do Application Insights. Como parte do processo de automatização da criação de novos recursos no Azure, você poderá configurar o diagnóstico usando o PowerShell.

## Habilitar a extensão de diagnóstico como parte da implantação de um Serviço de Nuvem

O cmdlet `New-AzureDeployment` tem um parâmetro `ExtensionConfiguration`, que usa uma matriz de configurações de diagnóstico. Elas podem ser criadas com o cmdlet `New-AzureServiceDiagnosticsExtensionConfig`. Por exemplo:

```ps

    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $primary_storagekey = (Get-AzureStorageKey `
     -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
       -StorageAccountName $diagnostics_storagename `
       -StorageAccountKey $primary_storagekey

    $webrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WebRole" -Storage_context $storageContext `
      -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WorkerRole" `
      -StorageContext $storage_context `
      -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment `
      -ServiceName $service_name `
      -Slot Production `
      -Package $service_package `
      -Configuration $service_config `
      -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

``` 

## Habilitar extensão de diagnóstico em um Serviço de Nuvem existente

Em um serviço existente, use `Set-AzureServiceDiagnosticsExtension`.

```ps
 
    $service_name = "MyService"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
    $primary_storagekey = (Get-AzureStorageKey `
         -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
        -StorageAccountName $diagnostics_storagename `
        -StorageAccountKey $primary_storagekey

    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $webrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WebRole" 
    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $workerrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WorkerRole"
```

## Obter a configuração de extensão de diagnóstico atual

```ps

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```


## Remover extensão de diagnóstico

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Se você tiver habilitado a extensão de diagnóstico usando `Set-AzureServiceDiagnosticsExtension` ou `New-AzureServiceDiagnosticsExtensionConfig` sem o parâmetro Função, poderá remover a extensão usando `Remove-AzureServiceDiagnosticsExtension` sem o parâmetro Função. Se o parâmetro Função tiver sido usado ao habilitar a extensão, ele também deverá ser usado ao removê-la.

Para remover a extensão de diagnóstico de cada função individual:

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```


## Tópicos relacionados

* [Monitorar aplicativos dos Serviços de Nuvem do Azure com o Application Insights](app-insights-cloudservices.md)
* [Enviar o Diagnóstico do Azure para o Application Insights](app-insights-azure-diagnostics.md)

<!---HONumber=AcomDC_1125_2015-->