---
title: "Solução de problemas com o rastreamento de eventos | Microsoft Docs"
description: "Os problemas mais comuns encontrados durante a implantação de serviços no Service Fabric do Microsoft Azure."
services: service-fabric
documentationcenter: .net
author: mattrowmsft
manager: timlt
editor: 
ms.assetid: 5eb8ef21-da04-4ac8-8b9a-5f7ff1e0a180
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/31/2016
ms.author: mattrow
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 45cfb660db9ea1d93d136d223d65602f3a373574


---
# <a name="troubleshoot-common-issues-when-you-deploy-services-on-azure-service-fabric"></a>Solucionar problemas comuns quando você implanta serviços no Azure Service Fabric
Ao executar serviços em seu computador de desenvolvedor, é fácil usar as [ferramentas de depuração do Visual Studio](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Para clusters remotos, [relatórios de integridade](service-fabric-view-entities-aggregated-health.md) sempre são um bom lugar para começar. As maneiras mais fáceis de acessar esses relatórios são por meio do PowerShell ou [SFX](service-fabric-visualizing-your-cluster.md). Este artigo pressupõe que você está depurando um cluster remoto e tem um entendimento básico de como usar essas ferramentas.

## <a name="application-crash"></a>Falha do aplicativo
O relatório “Partição está abaixo da contagem de réplicas ou instâncias de destino” é uma boa indicação de que o serviço está falhando. Para descobrir onde o serviço está falhando, é necessário um pouco mais de investigação. Ao executar seu serviço em grande escala, o seu melhor amigo será um conjunto de rastreamentos bem pensados.  Sugerimos que você experimente o [Diagnóstico do Azure](service-fabric-diagnostics-how-to-setup-wad.md) para coletar esses rastreamentos e use uma solução como [Elastic Search](service-fabric-diagnostic-how-to-use-elasticsearch.md) para exibir e pesquisar os rastreamentos.

![Integridade da partição SFX](./media/service-fabric-diagnostics-troubleshoot-common-scenarios/crashNewApp.png)

### <a name="during-service-or-actor-initialization"></a>Durante a inicialização de ator ou serviço
Todas as exceções antes que o tipo de serviço seja inicializado farão com que o processo falhe. Para esses tipos de falhas, o log de eventos do aplicativo mostrará o erro de seu serviço.
Estas são as exceções mais comuns vistas antes que o serviço seja inicializado.

***System.IO.FileNotFoundException***

Esse erro, frequentemente, é devido à ausência de dependências do assembly. Verifique a propriedade CopyLocal no Visual Studio ou no cache de assembly global para o nó.

***System.Runtime.InteropServices.COMException***
 *em System.Fabric.Interop.NativeRuntime+IFabricRuntime.RegisterStatefulServiceFactory(IntPtr, IFabricStatefulServiceFactory)*

 Isso indica que o nome do tipo de serviço registrado não coincide com o manifesto de serviço.

[Azure Diagnostics](service-fabric-diagnostics-how-to-setup-wad.md) pode ser configurado para carregar o log de eventos do aplicativo para todos os seus nós automaticamente.

### <a name="runasync-or-onactivateasync"></a>RunAsync() ou OnActivateAsync()
Se a falha ocorre durante a inicialização ou a execução do seu tipo de serviço registrado ou um ator, a exceção será capturada pelo Service Fabric do Azure. Você pode exibi-las nos provedores EventSource detalhados na seção “Próximas etapas”.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre diagnósticos existentes fornecidos pelo Service Fabric:

* [Diagnóstico do Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Diagnóstico do Reliable Services](service-fabric-reliable-services-diagnostics.md)




<!--HONumber=Nov16_HO3-->


