<properties
   pageTitle="Solucionar problemas comuns | Microsoft Azure"
   description="Os problemas mais comuns encontrados durante a implantação de serviços no Service Fabric do Microsoft Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="mattrowmsft"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/10/2015"
   ms.author="mattrow"/>


# Solução de problemas comuns
Ao executar serviços em sua máquina de desenvolvedor, é fácil de usar as [ferramentas de depuração do Visual Studio](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Para clusters remotos, [relatórios de integridade](service-fabric-view-entities-aggregated-health.md) sempre são um bom lugar para começar. Maneiras mais fáceis de acessar esses relatórios são por meio do Powershell ou [SFX](service-fabric-visualizing-your-cluster.md). Este artigo pressupõe que você está depurando um cluster remoto e tem um entendimento básico de como usar essas ferramentas.

##Falha do aplicativo
O relatório “Partição está abaixo da contagem de réplicas ou instâncias de destino” é uma boa indicação de que o serviço está falhando. Para descobrir onde o serviço está falhando, é necessário um pouco mais de investigação. Ao executar em grande escala, o seu melhor amigo será um conjunto de rastreamentos bem pensados. Sugerimos que você experimente [WAD](service-fabric-diagnostics-how-to-setup-wad-operational-insights.md) para coletar e exibir esses rastreamentos.

![Integridade da partição SFX](./media/service-fabric-diagnostics-troubleshoot-common-scenarios/crashNewApp.png)

###Durante a inicialização de ator ou serviço
Todas as exceções antes que o tipo de serviço seja inicializado farão com que o processo falhe. Para esses tipos de falhas, o log de eventos do aplicativo mostra o erro de seu serviço. Estas são as exceções mais comuns vistas antes que o serviço seja inicializado.

| Erro | Descrição |
| --- | --- |
| System.IO.FileNotFoundException | Essas são geralmente devido à ausência de dependências do assembly. Verifique a propriedade CopyLocal no Visual Studio ou no GAC para o nó.
| System.Runtime.InteropServices.COMException em System.Fabric.Interop.NativeRuntime+IFabricRuntime.RegisterStatefulServiceFactory (IntPtr, IFabricStatefulServiceFactory)|Isso indica que o nome do tipo de serviço registrado não coincide com o manifesto de serviço. |

O [WAD](service-fabric-diagnostics-how-to-setup-wad-operational-insights.md) pode ser configurado para carregar o log de eventos do aplicativo para todos os seus nós automaticamente.

###RunAsync() ou OnActivateAsync()
Se a falha ocorre durante a inicialização ou a execução do seu tipo de serviço registrado ou um ator, a exceção será capturada pelo Service Fabric. Você pode exibi-las nos provedores EventSource detalhados nas próximas etapas.

## Próximas etapas

Saiba mais sobre diagnósticos existentes fornecidos pelo Service Fabric:

* [Diagnóstico de atores](service-fabric-reliable-actors-diagnostics.md)
* [Diagnóstico do Reliable Services](service-fabric-reliable-services-diagnostics.md)

<!---HONumber=Nov15_HO4-->