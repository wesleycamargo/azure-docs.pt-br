---
title: "Coletar logs diretamente de um processo de serviço do Azure Service Fabric | Microsoft Azure"
description: "Descreve como os aplicativos do Service Fabric podem enviar logs diretamente para um local central, como o Azure Application Insights ou o Elasticsearch, sem depender de agentes do Diagnóstico do Azure."
services: service-fabric
documentationcenter: .net
author: karolz-ms
manager: rwike77
editor: 
ms.assetid: ab92c99b-1edd-4677-8c28-4e591d909b47
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/18/2017
ms.author: karolz@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: d7f7b157d8d6fb54259c8f23d5005509f4eb7872
ms.openlocfilehash: 22acb6afbfbfff753e71b5e821385798cc76ffdd


---
# <a name="collect-logs-directly-from-an-azure-service-fabric-service-process"></a>Coletar logs diretamente de um processo do serviço Azure Service Fabric
## <a name="in-process-log-collection"></a>Coleta de logs no processo
Coletar logs de aplicativo usando a [extensão do Diagnóstico do Azure](service-fabric-diagnostics-how-to-setup-wad.md) será uma boa opção para serviços do **Azure Service Fabric** se o conjunto de origens e destinos de log for pequeno, não forem alterados com frequência e se houver um mapeamento direto entre as origens e seus destinos. Caso contrário, uma alternativa é fazer com que os serviços enviem seus logs diretamente para um local central. Esse processo é conhecido como **coleta de logs no processo** e tem várias vantagens potenciais:

* *Fácil Configuração e implantação*

    * A configuração de coleta de dados de diagnóstico é apenas uma parte da configuração do serviço. É fácil sempre mantê-la "sincronizada" com o restante do aplicativo.
    * A configuração por aplicativo ou por serviço é facilmente realizável.
        * A coleta de logs baseada em agentes normalmente requer a implantação e a configuração separadas do agente de diagnóstico, que é uma tarefa administrativa extra e uma possível fonte de erros. Geralmente, há apenas uma instância do agente permitido por máquina virtual (nó) e a configuração do agente é compartilhada entre todos os aplicativos e serviços em execução nesse nó. 

* *Flexibilidade*
   
    * O aplicativo pode enviar os dados sempre que eles forem necessários, desde que exista uma biblioteca de cliente que ofereça suporte ao sistema de armazenamento de dados de destino. Novos destinos podem ser adicionados conforme desejado.
    * Regras de captura complexa, filtragem e agregação de dados podem ser implementadas.
    * Coleta de log do agente geralmente é limitada pelos Coletores de dados que suporta o agente. Alguns agentes são extensíveis.

* *Acesso a dados de aplicativos internos e contexto*
   
    * O subsistema de diagnóstico em execução dentro do processo de serviço/do aplicativo pode facilmente aumentar os rastreamentos com informações contextuais.
    * Com a coleta de logs baseada em agentes, os dados devem ser enviados a um agente por meio de um mecanismo de comunicação entre processos, como Rastreamento de Eventos para Windows. Esse mecanismo pode impor limitações adicionais.

É possível combinar e aproveitar ambos os métodos de coleta. Na verdade, essa pode ser a melhor solução para muitos aplicativos. Coleção com base em agente é uma solução natural para coletar logs relacionados para o cluster inteiro e nós de cluster individuais. Ele é muito mais confiável maneira, que coleta de log em processo para diagnosticar problemas de inicialização do serviço e falhas. Além disso, com vários serviços em execução dentro de um cluster de Service Fabric, cada serviço fazer sua própria coleção de log em processo resulta em várias conexões de saída do cluster. Grande número de conexões de saída é uma sobrecarga para o subsistema de rede e o destino de log. Um agente como [ **diagnóstico do Azure** ](../cloud-services/cloud-services-dotnet-diagnostics.md) pode coletar dados de vários serviços e enviar dados por meio de algumas conexões, melhorando a taxa de transferência. 

Neste artigo, mostramos como configurar uma coleção no processo log usando [ **biblioteca de código-fonte aberto EventFlow**](https://github.com/Azure/diagnostics-eventflow). Outras bibliotecas que podem ser usadas para a mesma finalidade, mas EventFlow tem a vantagem de ter foi projetado especificamente para coleta de log em processo e para dar suporte a serviços de Service Fabric. Usamos [ **Azure Application Insights** ](https://azure.microsoft.com/services/application-insights/) como o destino de log. Outros destinos, como [ **Hubs de eventos** ](https://azure.microsoft.com/services/event-hubs/) ou [ **Elasticsearch** ](https://www.elastic.co/products/elasticsearch) também têm suporte. É apenas uma questão de instalar o pacote do NuGet apropriado e configurando o destino no arquivo de configuração EventFlow. Para obter mais informações sobre destinos de log diferente do Application Insights, consulte [EventFlow documentação](https://github.com/Azure/diagnostics-eventflow).

## <a name="adding-eventflow-library-to-a-service-fabric-service-project"></a>Adição da biblioteca EventFlow a um projeto de serviço do Service Fabric
EventFlow binários estão disponíveis como um conjunto de pacotes do NuGet. Para adicionar EventFlow a um projeto de serviço do Service Fabric, clique com botão direito do mouse no projeto no Gerenciador de Soluções e escolha "Gerenciar pacotes NuGet." Alternar para a guia "Procurar" e pesquise por "`Diagnostics.EventFlow`":

![Pacotes do EventFlow NuGet no Gerenciador de pacotes do NuGet do Visual Studio da interface do usuário][1]

O serviço que hospeda EventFlow deve incluir pacotes apropriados dependendo da origem e destino para os logs de aplicativo. Adicione os seguintes pacotes: 

* `Microsoft.Diagnostics.EventFlow.Input.EventSource` 
    * (para capturar dados de classe do EventSource do serviço e de EventSources padrão como *Microsoft-ServiceFabric-Services* e *Microsoft-ServiceFabric-Actors*)
* `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights` 
    * (vamos enviar os logs para um recurso do Azure Application Insights)  
* `Microsoft.Diagnostics.EventFlow.ServiceFabric` 
    * (permite a inicialização do pipeline EventFlow da configuração do serviço Service Fabric e relata quaisquer problemas com o envio de dados de diagnóstico como relatórios de integridade do Service Fabric)

> [!NOTE]
> O pacote `Microsoft.Diagnostics.EventFlow.Input.EventSource` requer que o projeto de serviço tenha como destino o .NET Framework 4.6 ou mais recente. Certifique-se de que definir a estrutura de destino apropriada nas propriedades do projeto antes de instalar este pacote. 

Depois que todos os pacotes são instalados, a próxima etapa é configurar e habilitar EventFlow no serviço.

## <a name="configuring-and-enabling-log-collection"></a>Configurar e habilitar a coleta de logs
O pipeline de EventFlow, responsável pelo envio de logs, é criado de uma especificação armazenada em um arquivo de configuração. `Microsoft.Diagnostics.EventFlow.ServiceFabric`pacote instala um arquivo de configuração inicial do EventFlow em `PackageRoot\Config` pasta da solução. O nome do arquivo é `eventFlowConfig.json`. Esse arquivo de configuração precisa ser modificado para capturar dados do serviço padrão `EventSource` classe e enviar dados ao serviço Application Insights.

> [!NOTE]
> Vamos supor que você esteja familiarizado com o seviço **Azure Application Insights** e que tenha um recurso Application Insights que planeja usar para monitorar seu serviço do Service Fabric. Se você precisar de mais informações, veja [Criar um recurso do Application Insights](../application-insights/app-insights-create-new-resource.md).

Abra o `eventFlowConfig.json` no editor de arquivo e altere seu conteúdo conforme mostrado abaixo. Substitua o nome da ServiceEventSource e a chave de instrumentação do Application Insights de acordo com comentários. 

```json
{
  "inputs": [
    {
      "type": "EventSource",
      "sources": [
        { "providerName": "Microsoft-ServiceFabric-Services" },
        { "providerName": "Microsoft-ServiceFabric-Actors" },
        // (replace the following value with your service's ServiceEventSource name)
        { "providerName": "your-service-EventSource-name" }
      ]
    }
  ],
  "filters": [
    {
      "type": "drop",
      "include": "Level == Verbose"
    }
  ],
  "outputs": [
    {
      "type": "ApplicationInsights",
      // (replace the following value with your AI resource's instrumentation key)
      "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
  ],
  "schemaVersion": "2016-08-11"
}
```

> [!NOTE]
> O nome da ServiceEventSource do serviço é o valor da propriedade Nome do `EventSourceAttribute` aplicado à classe ServiceEventSource. Isso é especificado no arquivo `ServiceEventSource.cs`, que faz parte do código do serviço. Por exemplo, no trecho de código a seguir, o nome do ServiceEventSource é *MyCompany-Application1-Stateless1*:
> ```csharp
> [EventSource(Name = "MyCompany-Application1-Stateless1")]
> internal sealed class ServiceEventSource : EventSource
> {
>    // (rest of ServiceEventSource implementation)
>} 
> ```

Observe que o arquivo `eventFlowConfig.json` faz parte do pacote de configuração de serviço. As alterações feitas neste arquivo podem ser incluídas em atualizações completo - ou apenas da configuração do serviço, sujeito a verificações de integridade de atualização do Service Fabric e a reversão automática se houver falha na atualização. Para saber mais, confira [Atualização de aplicativos do Service Fabric](service-fabric-application-upgrade.md).

A etapa final é criar uma instância de pipeline de EventFlow no código de inicialização do serviço, localizado em `Program.cs` arquivo. No exemplo a seguir relacionados EventFlow adições são marcadas com comentários começando com `****`:

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric;
using Microsoft.ServiceFabric.Services.Runtime;

// **** EventFlow namespace
using Microsoft.Diagnostics.EventFlow.ServiceFabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>
        private static void Main()
        {
            try
            {
                // **** Instantiate log collection via EventFlow
                using (var diagnosticsPipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MyApplication-MyService-DiagnosticsPipeline"))
                {

                    
                    ServiceRuntime.RegisterServiceAsync("Stateless1Type",
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                    Thread.Sleep(Timeout.Infinite);
                }
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
                throw;
            }
        }
    }
}
```

O nome passado como o parâmetro do método `CreatePipeline` do `ServiceFabricDiagnosticsPipelineFactory` é o nome da *entidade de integridade* que representa o pipeline de coleta de logs de EventFlow. Esse nome será usado se EventFlow encontrar um erro e o reportar por meio do subsistema de integridade do Service Fabric.

## <a name="verification"></a>Verificação
Inicie o serviço e observe a depuração janela saída no Visual Studio. Depois que o serviço for iniciado, você deverá começar a ver evidências de que o serviço está enviando registros de "Application Insights Telemetry". Abra um navegador da Web e vá até seu recurso do Application Insights. Abra a guia "Pesquisar" (na parte superior da folha padrão "Visão geral"). Após um pequeno atraso, deve ser possível ver os rastreamentos no portal do Application Insights:

![Portal do Application Insights mostrando logs de um aplicativo do Service Fabric][2]

## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre o diagnóstico e monitoramento de um serviço da Malha de Serviço](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
* [Documentação de EventFlow](https://github.com/Azure/diagnostics-eventflow)


<!--Image references-->
[1]: ./media/service-fabric-diagnostics-collect-logs-without-an-agent/eventflow-nugets.png
[2]: ./media/service-fabric-diagnostics-collect-logs-without-an-agent/ai-traces.png


<!--HONumber=Jan17_HO3-->


