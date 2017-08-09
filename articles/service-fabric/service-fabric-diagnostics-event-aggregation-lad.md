---
title: "Agregação de Eventos do Azure Service Fabric com Diagnóstico do Linux Azure | Microsoft Docs"
description: "Saiba mais sobre a agregação e coleta de eventos utilizando o LAD para monitoramento e diagnóstico de clusters do Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/17/2017
ms.author: dekapur
ms.translationtype: HT
ms.sourcegitcommit: 0425da20f3f0abcfa3ed5c04cec32184210546bb
ms.openlocfilehash: bcc3a229369a065cfcfbd32eadbf3f6ae6fe0036
ms.contentlocale: pt-br
ms.lasthandoff: 07/20/2017

---

# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Coleta e agregação de eventos utilizando o Diagnóstico do Linux Azure
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Quando você estiver executando um cluster de Service Fabric do Azure, é uma boa ideia coletar os logs de todos os nós em um local central. Ter os logs em um local central ajuda a analisar e solucionar problemas no cluster ou nos aplicativos e serviços em execução nesse cluster.

Uma maneira de carregar e coletar logs é utilizar a extensão LAD (Diagnóstico do Linux Azure) que carrega os logs no Armazenamento do Microsoft Azure e, além disso, possui a opção de enviar os logs para o Azure Application Insights ou Hubs de Evento. Também é possível utilizar um processo externo para ler os eventos do armazenamento e colocá-los em um produto da plataforma de análise, como [OMS Log Analytics](../log-analytics/log-analytics-service-fabric.md) ou outra solução de análise de logs.

## <a name="log-and-event-sources"></a>Origem do evento e log

### <a name="service-fabric-platform-events"></a>Eventos de plataforma do Service Fabric
O Service Fabric emite alguns logs prontos para uso via [LTTng](http://lttng.org), incluindo eventos operacionais ou eventos de tempo de execução. Esses logs são armazenados na localização especificada pelo modelo do Resource Manager do cluster. Para obter ou definir os detalhes da conta de armazenamento, pesquise pela marca **AzureTableWinFabETWQueryable** e procure por **StoreConnectionString**.

### <a name="application-events"></a>Eventos de aplicativo
 Eventos emitidos pelo código de seus serviços e aplicativos, conforme especificado por você durante a instrumentação do software. Você pode usar qualquer solução de log que grave arquivos de log baseados em texto, por exemplo, LTTng. Para mais informações, consulte a documentação do LTTng no rastreamento do aplicativo.

[Monitora e diagnostica serviços em uma configuração de desenvolvimento de computador local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

## <a name="deploy-the-diagnostics-extension"></a>Implantar a extensão de Diagnóstico
A primeira etapa para coletar logs será implantar a extensão Diagnóstico em cada uma das VMs no cluster do Service Fabric. A extensão de Diagnóstico coleta logs em cada VM e os carrega para a conta de armazenamento especificada. As etapas variam com base em seu uso do Portal do Azure ou do Gerenciador de Recursos do Azure.

Para implantar a extensão de diagnóstico nas VMs no cluster como parte da criação do cluster, defina **Diagnóstico** como **Ativado**. Depois de criar o cluster, você não poderá alterar essa configuração por meio do portal.

Em seguida, configure o LAD (Diagnóstico do Azure para o Linux) para coletar os arquivos e colocá-los na sua conta de armazenamento. Este processo é explicado como cenário 3 ("carregar seus próprios arquivos de log") no artigo [Usando o LAD para monitorar e diagnosticar as VMs do Linux](../virtual-machines/linux/classic/diagnostic-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json). Seguindo esse processo, você terá acesso aos rastreamentos. Você poderá carregar os rastreamentos em um visualizador de sua escolha.

Você também poderá implantar a extensão de diagnóstico usando o Gerenciador de Recursos do Azure. O processo é semelhante para o Windows e para o Linux, e é documentado para clusters do Windows em [Como coletar logs com o Diagnóstico do Azure](service-fabric-diagnostics-how-to-setup-wad.md).

Você também pode usar o Operations Management Suite, conforme descrito em [Análise de Log do Operations Management Suite com o Linux](https://blogs.technet.microsoft.com/hybridcloud/2016/01/28/operations-management-suite-log-analytics-with-linux/).

Depois que essa configuração for definida, o agente do LAD monitora os arquivos de log especificados. Sempre que uma nova linha for anexada ao arquivo, ela cria uma entrada de syslog que é enviada para o armazenamento especificado.

## <a name="next-steps"></a>Próximas etapas

Confira [Documentação do LTTng](http://lttng.org/docs) e [Usando o LAD](../virtual-machines/linux/classic/diagnostic-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) para entender mais detalhadamente quais eventos você deve examinar ao solucionar problemas.
