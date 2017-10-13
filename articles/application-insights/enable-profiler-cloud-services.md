---
title: "Habilitar o Azure Application Insights Profiler em recursos de Computação do Azure | Microsoft Docs"
description: "Saiba como configurar o criador de perfil em um aplicativo ASP.NET hospedado por um recurso dos Serviços de Nuvem do Azure."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: bwren
ms.openlocfilehash: 65ba755f35df7bd09dd652ac6fccf96a878c6ca9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="enable-application-insights-profiler-on-an-azure-cloud-services-resource"></a>Habilitar o Application Insights Profiler em um recurso dos Serviços de Nuvem do Microsoft Azure

Este passo a passo demonstra como habilitar o Azure Application Insights Profiler em um aplicativo ASP.NET hospedado por um recurso dos Serviços de Nuvem do Microsoft Azure. Os exemplos incluem suporte para Máquinas Virtuais do Azure, conjuntos de dimensionamento de máquinas virtuais e Service Fabric do Azure. Todos os exemplos dependem de modelos que dão suporte ao modelo de implantação do Azure Resource Manager. Para saber mais sobre o modelo de implantação, leia [Implantação do Azure Resource Manager versus clássica: entenda os modelos de implantação e o estado de seus recursos](/azure-resource-manager/resource-manager-deployment-model).

## <a name="overview"></a>Visão geral

O diagrama a seguir ilustra como o criador de perfil funciona para os recursos dos Serviços de Nuvem do Microsoft Azure. Ele usa uma máquina virtual do Azure como exemplo.

![Visão geral](./media/enable-profiler-compute/overview.png) Para coletar informações de processamento e exibição no portal do Azure, você deve instalar o componente Agente de Diagnóstico para os recursos dos Serviços de Nuvem do Microsoft Azure. O restante do passo a passo apresenta diretrizes sobre como instalar e configurar o Agente de Diagnóstico para habilitar o Application Insights Profiler.

## <a name="prerequisites-for-the-walkthrough"></a>Pré-requisitos para o passo a passo

* Um modelo de implantação do Resource Manager que instala os agentes de criador de perfil nas VMs ([WindowsVirtualMachine.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)) ou conjuntos de dimensionamento ([WindowsVirtualMachineScaleSet.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)).

* Uma instância do Application Insights habilitada para criação de perfil. Para obter instruções, confira [Habilitar o criador de perfil](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler).

* .NET Framework 4.6.1 ou posterior instalado no recurso de destino dos Serviços de Nuvem do Microsoft Azure.

## <a name="create-a-resource-group-in-your-azure-subscription"></a>Criar um grupo de recursos em sua assinatura do Azure
O seguinte exemplo demonstra como criar um grupo de recursos usando um script do PowerShell:

```
New-AzureRmResourceGroup -Name "Replace_With_Resource_Group_Name" -Location "Replace_With_Resource_Group_Location"
```

## <a name="create-an-application-insights-resource-in-the-resource-group"></a>Criar um recurso Application Insights no grupo de recursos
Na folha **Application Insights**, insira as informações do recurso, conforme mostrado neste exemplo: 

![Folha Application Insights](./media/enable-profiler-compute/createai.png)

## <a name="apply-an-application-insights-instrumentation-key-in-the-azure-resource-manager-template"></a>Aplicar uma chave de instrumentação do Application Insights no modelo do Azure Resource Manager

1. Caso ainda não tenha baixado o modelo, baixe-o do [GitHub](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json).

2. Localize a chave do Application Insights.
   
   ![Local da chave](./media/enable-profiler-compute/copyaikey.png)

3. Substitua o valor do modelo.
   
   ![Valor substituído no modelo](./media/enable-profiler-compute/copyaikeytotemplate.png)

## <a name="create-an-azure-vm-to-host-the-web-application"></a>Criar uma VM do Azure para hospedar o aplicativo Web
1. Crie uma cadeia de caracteres segura para salvar a senha.

   ```
   $password = ConvertTo-SecureString -String "Replace_With_Your_Password" -AsPlainText -Force
   ```

2. Implante o modelo do Azure Resource Manager.

   Altere o diretório no console do PowerShell para a pasta que contém o modelo do Resource Manager. Para implantar o modelo, execute o seguinte comando:

   ```
   New-AzureRmResourceGroupDeployment -ResourceGroupName "Replace_With_Resource_Group_Name" -TemplateFile .\WindowsVirtualMachine.json -adminUsername "Replace_With_your_user_name" -adminPassword $password -dnsNameForPublicIP "Replace_WIth_your_DNS_Name" -Verbose
   ```

Depois que o script for executado com êxito, você deverá encontrar uma VM denominada **MyWindowsVM** em seu grupo de recursos.

## <a name="configure-web-deploy-on-the-vm"></a>Configurar Implantação da Web na VM
Verifique se a Implantação da Web está habilitada na sua VM para que você possa publicar o aplicativo Web usando o Visual Studio.

Para instalar a Implantação da Web em uma VM manualmente por meio do WebPI, confira [Installing and Configuring Web Deploy on IIS 8.0 or Later](https://docs.microsoft.com/en-us/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later) (Instalar e configurar a Implantação da Web no IIS 8.0 ou posterior). Para obter um exemplo de como automatizar a instalação da Implantação da Web usando um modelo do Azure Resource Manager, confira [Create, configure, and deploy a web application to an Azure VM](https://azure.microsoft.com/en-us/resources/templates/201-web-app-vm-dsc/) (Criar, configurar e implantar um aplicativo Web em uma VM do Azure).

Se você estiver implantando um aplicativo MVC ASP.NET, vá para o Gerenciador do Servidor, escolha **Adicionar Funções e Recursos** > **Servidor Web (IIS)** > **Servidor Web** > **Desenvolvimento de Aplicativo** e habilite o ASP.NET 4.5 no servidor.

![Adicionar ASP.NET](./media/enable-profiler-compute/addaspnet45.png)

## <a name="install-the-azure-application-insights-sdk-for-your-project"></a>Instalar o SDK do Azure Application Insights para seu projeto
1. Abra o aplicativo Web ASP.NET no Visual Studio.

2. Clique com o botão direito do mouse no projeto e escolha **Adicionar** > **Serviços Conectados**.

3. Selecione **Application Insights**.

4. Siga as instruções na página. Selecione o recurso Application Insights que você criou anteriormente.

5. Escolha o botão **Registrar**.


## <a name="publish-the-project-to-an-azure-vm"></a>Publicar o projeto em uma VM do Azure
Há várias maneiras de publicar um aplicativo para uma VM do Azure. Uma delas é usar o Virtual Studio 2017.

1. Clique com o botão direito do mouse no projeto e escolha **Publicar**.

2. Escolha **Máquinas Virtuais do Microsoft Azure** como o destino de publicação e siga as etapas.

   ![Publish-FromVS](./media/enable-profiler-compute/publishtoVM.png)

3. Execute um teste de carga no seu aplicativo. Você deve ver os resultados na página da Web do portal da instância do Application Insights.


## <a name="enable-the-profiler"></a>Habilitar o criador de perfil

1. Vá para a folha **Desempenho** do Application Insights e clique em **Criador de Perfil** no canto superior direito para configurá-lo.

   ![Botão Configurar o Criador de Perfil](./media/enable-profiler-compute/PerformanceTriageViewPofilerButton.png)

2. Selecione **Habilitar o Criador de Perfil**.

   ![Botão Habilitar o Criador de Perfil](./media/enable-profiler-compute/enableprofiler2.png)


## <a name="add-a-performance-test-to-your-application"></a>Adicionar um teste de desempenho para o aplicativo
Siga estas etapas para que seja possível coletar alguns dados de exemplo a serem exibidos no Application Insights Profiler:

1. Navegue até o recurso Application Insights que você criou anteriormente. 

2. Vá para a folha **Disponibilidade** e adicione um teste de desempenho que envie solicitações da Web para a URL do aplicativo. 

   ![Adicionar teste de desempenho](./media/enable-profiler-compute/AvailabilityTest.png)

## <a name="view-your-performance-data"></a>Exibir seus dados de desempenho

1. Aguarde de 10 a 15 minutos para que o criador de perfil colete e analise os dados. 

2. Vá para a folha **Desempenho** em seu recurso Application Insights e veja o desempenho do aplicativo quando ele está sob carga. Concentre-se a operação lenta de interesse com uso suficiente, classificando a grade operacional pela coluna Contagem. Observe quais intervalos de duração têm rastreamentos do criador de perfil, examinando a rota do Criador de Perfil acima da distribuição de duração. Observe que quanto maior a duração pela qual você monitorar seu aplicativo, mais rastreamentos o criador de perfil coletará e, portanto, mais a distribuição será abordada pelos exemplos de nível de código avançado com suporte pelos rastreamentos do criador de perfil. 

   ![Rastreamentos do criador de perfil na exibição de triagem de desempenho](./media/enable-profiler-compute/PerformanceTriageViewProfilerTraces.png)

    Você pode aplicar zoom ao intervalo de duração de interesse, assim como o terceiro pico em torno do 95º percentil. Isso restringirá o número de amostras e de rastreamentos do criador de perfil nos botões Executar Ação. 

    ![Aplicar zoom ao intervalo de duração](./media/enable-profiler-compute/DurationRangeZoomedTo95th.png)

    Agora, clique no botão **Rastreamentos do Criador de Perfil** para abrir o Criador de Perfil com o rastreamento apropriado.

3. Escolha o ícone em **Exemplos** para abrir a folha **Exibição de Rastreamento**.

   ![Abrindo a folha Exibição de Rastreamento](./media/enable-profiler-compute/traceview.png)


## <a name="work-with-an-existing-template"></a>Trabalhar com um modelo existente

1. Localize a declaração do recurso Diagnóstico do Azure em seu modelo de implantação.
   
   Caso não tenha uma declaração, você poderá criar uma semelhante à declaração no exemplo a seguir. Você pode atualizar o modelo no [site do Azure Resource Explorer](https://resources.azure.com).

2. Altere o editor de `Microsoft.Azure.Diagnostics` para `AIP.Diagnostics.Test`.

3. Para `typeHandlerVersion`, use `0.0`.

4. Verifique se `autoUpgradeMinorVersion` está definido como `true`.

5. Adicione a nova instância do coletor `ApplicationInsightsProfiler` no objeto de configurações `WadCfg`, conforme mostrado no seguinte exemplo:

```
"resources": [
        {
          "type": "extensions",
          "name": "Microsoft.Insights.VMDiagnosticsSettings",
          "apiVersion": "2016-03-30",
          "properties": {
            "publisher": "AIP.Diagnostics.Test",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "WadCfg": {
                "SinksConfig": {
                  "Sink": [
                    {
                      "name": "Give a descriptive short name. E.g.: MyApplicationInsightsProfilerSink",
                      "ApplicationInsightsProfiler": "Enter the Application Insights instance instrumentation key guid here"
                    }
                  ]
                },
                "DiagnosticMonitorConfiguration": {
                    ...
                }
                ...
              }
              ...
            }
            ...
          }
          ...
]
```

## <a name="enable-the-profiler-on-virtual-machine-scale-sets"></a>Habilitar o criador de perfil em conjuntos de dimensionamento de máquinas virtuais
Para ver como habilitar o criador de perfil, baixe o modelo [WindowsVirtualMachineScaleSet.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json). Aplique as mesmas alterações em um modelo de VM ao recurso de extensão de diagnóstico para o conjunto de dimensionamento de máquinas virtuais.

Verifique se cada instância no conjunto de dimensionamento tem acesso à Internet. O Agente do Criador de Perfil pode enviar as amostras coletadas ao Application Insights para exibição e análise.

## <a name="enable-the-profiler-on-service-fabric-applications"></a>Habilitar o criador de perfil em aplicativos do Service Fabric
1. Provisione o cluster do Service Fabric para que ele tenha a extensão do Diagnóstico do Azure que instala o Agente do Criador de Perfil.

2. Instale o SDK do Application Insights no projeto e configure a chave do Application Insights.

3. Adicione o código do aplicativo à telemetria de instrumento.

### <a name="provision-the-service-fabric-cluster-to-have-the-azure-diagnostics-extension-that-installs-the-profiler-agent"></a>Provisionar o cluster do Service Fabric para que ele tenha a extensão do Diagnóstico do Azure que instala o Agente do Criador de Perfil
Um cluster do Service Fabric, que pode ser seguro ou não. Você pode definir um cluster de gateway como não seguro, de modo que ele não exija um certificado para o acesso. Clusters que hospedam lógica de negócios e dados devem ser seguros. Você pode habilitar o criador de perfil em clusters seguros e não seguros do Service Fabric. Este passo a passo usa um cluster não seguro como um exemplo para explicar as alterações necessárias para habilitar o criador de perfil. Você pode provisionar um cluster seguro da mesma maneira.

1. Baixe o [ServiceFabricCluster.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json). Assim como feito nas VMs e nos conjuntos de dimensionamento de máquinas virtuais, substitua `Application_Insights_Key` pela chave do Application Insights:

   ```
   "publisher": "AIP.Diagnostics.Test",
                 "settings": {
                   "WadCfg": {
                     "SinksConfig": {
                       "Sink": [
                         {
                           "name": "MyApplicationInsightsProfilerSinkVMSS",
                           "ApplicationInsightsProfiler": "[Application_Insights_Key]"
                         }
                       ]
                     },
   ```

2. Implante o modelo usando um script do PowerShell:

   ```
   Login-AzureRmAccount
   New-AzureRmResourceGroup -Name [Your_Resource_Group_Name] -Location [Your_Resource_Group_Location] -Verbose -Force
   New-AzureRmResourceGroupDeployment -Name [Choose_An_Arbitrary_Name] -ResourceGroupName [Your_Resource_Group_Name] -TemplateFile [Path_To_Your_Template]

   ```

### <a name="install-the-application-insights-sdk-in-the-project-and-configure-the-application-insights-key"></a>Instalar o SDK do Application Insights no projeto e configurar a chave do Application Insights
Instale o SDK do Application Insights do [pacote do NuGet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/). Certifique-se de instalar uma versão estável 2.3 ou posterior. 

Para obter informações sobre como configurar o Application Insights em seus projetos, confira [Using Service Fabric with Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md) (Usando o Service Fabric com o Application Insights).

### <a name="add-application-code-to-instrument-telemetry"></a>Adicionar o código do aplicativo à telemetria de instrumento
1. Em qualquer parte de código que você queira instrumentar, adicione uma instrução de uso em torno dele. 

   No exemplo a seguir, o método `RunAsync` está desempenhando seu papel e a classe `telemetryClient` captura a telemetria depois que ela é iniciada. O evento precisa de um nome exclusivo no aplicativo.

   ```
   protected override async Task RunAsync(CancellationToken cancellationToken)
       {
           // TODO: Replace the following sample code with your own logic
           //       or remove this RunAsync override if it's not needed in your service.

           while (true)
           {
               using( var operation = telemetryClient.StartOperation<RequestTelemetry>("[Insert_Event_Unique_Name]"))
               {
                   cancellationToken.ThrowIfCancellationRequested();

                   ++this.iterations;

                   ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", this.iterations);

                   await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
               }

           }
       }
   ```

2. Implante seu aplicativo para o cluster do Service Fabric. Aguarde por 10 minutos até o aplicativo ser executado. Para obter melhor efeito, você pode executar um teste de carga no aplicativo. Vá para a folha **Desempenho** do portal do Application Insights e aparecerão exemplos de rastreamentos de criação de perfil.

<!---
Commenting out these sections for now
## Enable the Profiler on Cloud Services applications
[TODO]
## Enable the Profiler on classic Azure Virtual Machines
[TODO]
## Enable the Profiler on on-premise servers
[TODO]
--->

## <a name="next-steps"></a>Próximas etapas

- Encontre ajuda para solução de problemas do criador de perfil em [Solução de problemas do criador de perfil](app-insights-profiler.md#troubleshooting).

- Leia mais sobre o criador de perfil em [Application Insights Profiler](app-insights-profiler.md).
