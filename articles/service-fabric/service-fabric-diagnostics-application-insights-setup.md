<properties
   pageTitle="Configurando o Application Insights para seu aplicativo da Malha do Serviço"
   description="Receber eventos da Malha do Serviço para seu aplicativo no Application Insights."
   services="service-fabric"
   documentationCenter=".net"
   authors="mattrowmsft"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/20/2015"
   ms.author="mattrow"/>

# Configurando o Application Insights para seu aplicativo da Malha do Serviço
 Este artigo mostra como habilitar o Application Insights para seu aplicativo da Malha do Serviço.

## Pré-requisitos

Este artigo pressupõe que você tenha um aplicativo da Malha do Serviço já criado no Visual Studio. Para saber como [clique aqui](service-fabric-reliable-services-quick-start.md).

## Instalando o pacote NuGet
A versão de pré-lançamento do nosso pacote nuget Microsoft.ServiceFabric.Telemetry.ApplicationInsights foi lançada como parte do SDK da Malha do Serviço. Esse pacote reúne os eventos EventSource da Malha do Serviço com o Application Insights para fornecer instrumentação automatizada do seu aplicativo da Malha do Serviço. O pacote continuará sendo atualizado com novos eventos que serão emitidos automaticamente pelo aplicativo.

Você pode instalar o pacote seguindo estas etapas:

1. Abra o Gerenciador de Pacotes do NuGet do seu aplicativo da Malha do Serviço. Você pode fazer isso clicando com o botão direito do mouse no seu projeto, no Visual Studio, e selecionando 'Gerenciar Pacotes do NuGet...'.
2. É preciso selecionar ‘Malha do Serviço do Microsoft Azure’ como a origem do pacote para listar pacotes incluídos no SDK da Malha do Serviço. ![Gerenciador de Pacotes NuGet VS2015](media/service-fabric-diagnostics-application-insights-setup/AI-nuget-package-manager.jpg)
3. Selecione o pacote Microsoft.ServiceFabric.Telemetry.ApplicationInsights à esquerda.
4. Clique em Instalar para iniciar o processo de instalação.
5. Leia e aceite o EULA.

## Habilitando eventos da Malha do Serviço
Para receber eventos da Malha do Serviço automaticamente no Application Insights, você precisará habilitar nosso ouvinte. Você pode fazer isso inserindo a linha de código a seguir em seu aplicativo.

```csharp
    Microsoft.ServiceFabric.Telemetry.ApplicationInsights.Listener.Enable(EventLevel.Verbose);
```
 
### Exemplo de StatefulActor\\Program.cs:

```csharp
    public static void Main(string[] args)
    {
        Microsoft.ServiceFabric.Telemetry.ApplicationInsights.Listener.Enable(EventLevel.Verbose);
        try
        {
            using (FabricRuntime fabricRuntime = FabricRuntime.Create())
            {
                fabricRuntime.RegisterActor(typeof(StatefulActor));

                Thread.Sleep(Timeout.Infinite);
            }
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e);
            throw;
        }
    }
```

Você pode aprender sobre os eventos emitidos no tempo de execução de Atores Confiáveis [aqui](service-fabric-reliable-actors-diagnostics.md) e no tempo de execução de Serviços Confiáveis [aqui](service-fabric-reliable-services-diagnostics.md).

Observe que para obter as chamadas de método do tempo de execução de Atores Confiáveis, é preciso usar EventLevel.Verbose (conforme mostrado no exemplo acima).

## Configurando o Application Insights
Uma chave de instrumentação é o que vincula seu aplicativo da Malha do Serviço ao recurso Application Insights. Aprenda como obter a chave de instrumentação seguindo o [Guia do Application Insights](../app-insights-create-new-resource.md#create-an-application-insights-resource). Selecione ‘Outro’ para o tipo de aplicativo durante a criação de recursos.

![Selecionar Outro para tipo de app AI](media/service-fabric-diagnostics-application-insights-setup/AI-app-type-other.JPG)

Assim que estiver com sua chave de instrumentação, você poderá inseri-la no arquivo ApplicationInsights.config desta forma:

```xml
    <InstrumentationKey>INSERT YOUR KEY HERE</InstrumentationKey>
```

## Exibindo dados
Você pode [personalizar a folha App Insights](../app-insights-metrics-explorer.md) para atender às suas necessidades. A maioria dos eventos da Malha do Serviço é exibida como ‘Eventos Personalizados’, enquanto as chamadas de método Ator de Malha e as chamadas ao serviço RunAsync() serão mostradas como solicitações. Modelar esses eventos como solicitações permite usar a dimensão ‘nome da solicitação’ e a métrica ‘duração da solicitação’ na criação de gráficos. Novos gráficos, métricas e eventos continuarão sendo adicionados, os quais você poderá aproveitar futuramente.

## Próximas etapas
Saiba mais sobre como usar o Application Insights para instrumentar seus aplicativos da Malha do Serviço.

- [Introdução ao Application Insights](../app-insights-get-started.md)
- [Aprenda a criar seus próprios eventos e métricas personalizados](../app-insights-custom-events-metrics-api.md)
 

<!---HONumber=July15_HO4-->