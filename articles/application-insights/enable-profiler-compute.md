---
title: "Habilitar o Application Insights Profiler para aplicativos de Computação do Azure | Microsoft Docs"
description: "Saiba como configurar o Profiler em um aplicativo em execução na Computação do Azure."
services: application-insights
documentationcenter: 
author: ramach
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: ramach
ms.openlocfilehash: b39b3b234112647ebeb531262d3b3876aee0b63b
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2017
---
# <a name="enable-application-insights-profiler-on-azure-virtual-machines-service-fabric-and-cloud-services"></a>Habilitar Application Insights Profiler em máquinas virtuais do Azure, do Service Fabric e de Serviços de Nuvem

Este passo a passo demonstra como habilitar o Azure Application Insights Profiler em um aplicativo ASP.NET hospedado por um recurso de Computação do Azure.  
Os exemplos incluem suporte para Máquinas Virtuais do Azure, Conjuntos de Dimensionamento de Máquinas Virtuais do Azure e Azure Service Fabric e Serviços de Nuvem do Azure.  
Todos os exemplos dependem de modelos que dão suporte ao modelo de implantação do [Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview).  


## <a name="overview"></a>Visão geral

O diagrama a seguir ilustra como o Profiler funciona para os recursos de Computação do Azure. Ele usa uma máquina virtual do Azure como exemplo.

![Visão geral](./media/enable-profiler-compute/overview.png)


Para habilitar totalmente o Profiler, a configuração deve ser feita em três locais:

1. Folha do portal da instância do Application Insights.
2. Código-fonte do aplicativo (por exemplo: um aplicativo Web ASP .NET).
3. Código-fonte de definição de implantação do ambiente (por exemplo: um arquivo JSON de modelo de implantação de Máquina Virtual).


## <a name="configure-the-application-insights-instance"></a>Configurar a instância do Application Insights

Criar ou visitar a folha do Portal do Azure para a instância do Application Insights que você deseja usar e anotar sua chave de instrumentação. Ela será útil para as outras etapas de configuração:

  ![Local da chave](./media/enable-profiler-compute/CopyAIKey.png)

Esta instância deve ser a mesma configurada no seu aplicativo para enviar os dados de telemetria para cada solicitação.
Além disso, resultados do Profiler estarão disponível nesta instância.  

Ainda no Portal do Azure, siga [Habilitar o Profiler](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler) para concluir a configuração da instância de AI do Profiler. Não é necessário vincular os aplicativos Web para os fins deste passo a passo, apenas garanta que o Profiler esteja habilitado no Portal.


## <a name="configure-the-application-source-code"></a>Configurar o código-fonte do aplicativo

Seu aplicativo deve ser configurado para enviar dados de telemetria para uma instância do Application Insights em cada operação `Request`.  

1. Adicione o [SDK do Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-overview#get-started) ao seu projeto de aplicativo. Verifique se as versões do pacote nuget serão como segue:  
  - Para aplicativos ASP.NET: [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) da versão 2.3.0 ou mais recente.
  - Para aplicativos ASP.NET Core: [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) 2.1.0. ou mais recente.
  - Para outros aplicativos .NET e .NET Core (por exemplo, serviço sem estado do Service Fabric, função de trabalho do serviço de nuvem): [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) ou [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 ou mais recente.  

2. Se seu aplicativo *não* for um aplicativo ASP.NET ou ASP.NET Core (por exemplo: funções de trabalho de Serviços de Nuvem, APIs Sem Monitoração de Estado do Service Fabric), a seguinte configuração de instrumentação adicional será necessária:  

  2.1. Adicione o seguinte código em um ponto anterior no tempo de vida do aplicativo.  

  ```csharp
  using Microsoft.ApplicationInsights.Extensibility;
  ...
  // Replace with your own Application Insights instrumentation key.
  TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
  ```
  Para obter mais informações sobre essa configuração chave de Instrumentação global, consulte [Usando o Service Fabric com o Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md).  

  2.2. Para qualquer parte do código que você queira instrumentar, adicione uma instrução `StartOperation<RequestTelemetry>` `using` ao redor dela, como no exemplo a seguir:

  ```csharp
  using Microsoft.ApplicationInsights;
  using Microsoft.ApplicationInsights.DataContracts;
  ...
  var client = new TelemetryClient();
  ...
  using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
  {
    // ... Code I want to profile.
  }
  ```

> [!NOTE]  
> Não há suporte para chamar `StartOperation<RequestTelemetry>` dentro de outro escopo `StartOperation<RequestTelemetry>`. Você pode usar `StartOperation<DependencyTelemetry>` no escopo aninhado, em vez disso. Exemplo:  

```csharp
using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
{
  try
  {
    ProductDetail details = new ProductDetail() { Id = productId };
    getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();

    // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
    using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
    {
        double price = await _priceDataBase.GetAsync(productId);
        if (IsTooCheap(price))
        {
            throw new PriceTooLowException(productId);
        }
        details.Price = price;
    }

    // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
    using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
    {
        details.Reviews = await _reviewDataBase.GetAsync(productId);
    }

    getDetailsOperation.Telemetry.Success = true;
    return details;
  }
  catch(Exception ex)
  {
    getDetailsOperation.Telemetry.Success = false;

    // This exception gets linked to the 'GetProductDetails' request telemetry.
    client.TrackException(ex);
    throw;
  }
}
```


## <a name="configure-the-environment-deployment-definition"></a>Configurar a definição de implantação do ambiente

O ambiente em que o Profiler e seu aplicativo são executados pode ser uma Máquina Virtual, um Conjunto de Dimensionamento de Máquinas Virtuais, Cluster do Service Fabric ou Serviços de Nuvem.  

### <a name="virtual-machine-virtual-machine-scale-sets-or-service-fabric"></a>Máquina virtual, Conjuntos de Dimensionamento de Máquinas Virtuais ou Service Fabric

Exemplos completos:  
  * [Máquina Virtual](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
  * [Conjunto de Dimensionamento de Máquinas Virtuais](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
  * [Cluster do Service Fabric](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

1. Para garantir que o [.NET Framework 4.6.1](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) ou mais recente esteja em uso, é suficiente confirmar se o Sistema Operacional implantado é `Windows Server 2012 R2` ou mais recente.

2. Localize a extensão [Diagnóstico do Azure](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/azure-diagnostics) no arquivo de modelo de implantação e adicione a seguinte seção `SinksConfig` como um elemento filho do `WadCfg`, substituindo o `ApplicationInsightsProfiler` valor da propriedade pela sua própria chave de instrumentação do AI:  
```json
"SinksConfig": {
  "Sink": [
    {
      "name": "MyApplicationInsightsProfilerSink",
      "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
    }
  ]
}
```

Consulte os exemplos [neste guia](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para saber como adicionar a extensão de Diagnóstico ao seu modelo de implantação.


### <a name="cloud-services"></a>Serviços de Nuvem

1. Para garantir que o [.NET Framework 4.6.1](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) ou mais recente esteja em uso, é suficiente confirmar que os arquivos `ServiceConfiguration.*.cscfg` contém `osFamily` como `"5"` ou mais recente.

2. Localize o arquivo [Diagnóstico do Azure](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/azure-diagnostics) `diagnostics.wadcfgx` para sua função de aplicativo:  
![Local do arquivo de configuração de diagnóstico](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  
Se você não conseguir encontrar o arquivo, consulte [este guia](https://docs.microsoft.com/en-us/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them) sobre como habilitar a extensão de Diagnóstico em seu projeto de Serviços de Nuvem do Azure.

3. Adicione a seguinte seção `SinksConfig` como um elemento filho do `WadCfg`:  
  ```xml
  <WadCfg>
    <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
    <SinksConfig>
      <Sink name="MyApplicationInsightsProfiler">
        <!-- Replace with your own Application Insights instrumentation key. -->
        <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
      </Sink>
    </SinksConfig>
  </WadCfg>
  ```

> [!NOTE]  
> Se o arquivo `diagnostics.wadcfgx` também contiver outro coletor do tipo `ApplicationInsights`, todas essas três chaves de instrumentação deverão combinar:  
>  * a chave de instrumentação usada pelo seu aplicativo  
>  * a chave de instrumentação usada pelo coletor `ApplicationInsights`  
>  * a chave de instrumentação usada pelo coletor `ApplicationInsightsProfiler`  
>
>O valor da chave de instrumentação real usado pelo coletor `ApplicationInsights` pode ser encontrado nos arquivos `ServiceConfiguration.*.cscfg`.  
>Após o lançamento do SDK do Azure do Visual Studio 15.5, apenas as chaves de instrumentação usadas pelo aplicativo e pelo coletor `ApplicationInsightsProfiler` precisarão corresponder uma à outra.


## <a name="environment-deployment-and-runtime-configurations"></a>Configurações de implantação e tempo de execução do ambiente

1. Implante a definição de implantação de ambiente modificada.  
Normalmente, para aplicar as modificações, uma implantação de modelo completo ou os Serviços de Nuvem publicam por meio de cmdlets do PowerShell ou o Visual Studio está envolvido.  
Esta é uma abordagem alternativa para `Virtual Machines` existente que envolve apenas sua extensão de Diagnóstico:  
```powershell
$ConfigFilePath = [IO.Path]::GetTempFileName()
# After exporting the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
(Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
# Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
# if your original diagnostics configuration had the storageAccountName property in the protectedSettings section
# (which is not downloadable). Make sure to pass the same original value you had in this cmdlet call.
Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
```

2. Se o aplicativo desejado estiver em execução por meio de [IIS](https://www.microsoft.com/web/platform/server.aspx), o recurso do Windows `IIS Http Tracing` deverá ser habilitado:  
  Estabeleça acesso remoto ao ambiente e, em seguida, use a janela [Adicionar Recursos do Windows]( https://docs.microsoft.com/en-us/iis/configuration/system.webserver/tracing/) ou execute o seguinte no PowerShell (como administrador):  
  ```powershell
  Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
  ```  
  Se estabelecer acesso remoto for um problema, você poderá usar a [CLI do Azure](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli) para executar o seguinte:  
  ```powershell
  az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
  ```


## <a name="how-to-enable-profiler-on-on-premises-servers"></a>Como habilitar o Profiler em servidores locais

Também conhecida por executar o AI Profiler no modo autônomo (não vinculado às modificações de extensão de Diagnóstico).  
Não temos nenhum plano para dar suporte oficial ao Profiler para servidores locais.
Se você estiver interessado em experimentar este cenário, poderá baixar o código de suporte [daqui](https://github.com/ramach-msft/AIProfiler-Standalone).  
*Não* somos responsáveis pela manutenção desse código nem por responder a solicitações de problemas e recursos relacionados a ela.


## <a name="next-steps"></a>Próximas etapas

- Gerar tráfego para seu aplicativo (por exemplo: inicialize um [Teste de Disponibilidade](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-monitor-web-app-availability)) e aguardar 10 a 15 minutos depois de os rastreamentos começarem a ser enviados para a instância do Application Insights.

- Consulte [Rastreamentos do Profiler](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler) no Portal do Azure.

- Encontre ajuda para a solução de problemas do Profiler em [Solução de problemas do Profiler](app-insights-profiler.md#troubleshooting).

- Leia mais sobre o Profiler em [Application Insights Profiler](app-insights-profiler.md).
