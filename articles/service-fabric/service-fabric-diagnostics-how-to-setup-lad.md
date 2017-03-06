---
title: "Coletar logs com o Diagnóstico do Azure para Linux | Microsoft Docs"
description: "Este artigo descreve como configurar o Diagnóstico do Azure para coletar logs de um cluster do Linux do Service Fabric em execução no Azure."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: a160d469-8b7d-4560-82dd-8500db34a44a
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/02/2017
ms.author: subramar
translationtype: Human Translation
ms.sourcegitcommit: c9730b553e59d12b8720bbf3a06cc956912e27de
ms.openlocfilehash: 37063d35e76d03a84f6e4451c2f6c363704607f2
ms.lasthandoff: 01/12/2017


---
# <a name="collect-logs-by-using-azure-diagnostics"></a>Coletar logs usando o Diagnóstico do Azure
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
> * [Linux](service-fabric-diagnostics-how-to-setup-lad.md)
> 
> 

Quando você estiver executando um cluster de Service Fabric do Azure, é uma boa ideia coletar os logs de todos os nós em um local central. Ter os logs em uma localização central facilita a análise e a resolução de problemas, independentemente de eles estarem em seus serviços, aplicativos ou no próprio cluster. Uma forma de carregar e coletar logs é usar a extensão de Diagnóstico do Azure, que carrega os logs no Armazenamento do Azure, no Application Insights do Azure ou nos Hubs de Eventos do Azure. Você também pode ler os eventos no armazenamento ou nos Hubs de Eventos e colocá-los em um produto, como o [Log Analytics](../log-analytics/log-analytics-service-fabric.md), ou em outra solução de análise de log. O [Application Insights do Azure](https://azure.microsoft.com/services/application-insights/) apresenta um serviço abrangente interno de pesquisa e análise de log.

## <a name="log-sources-that-you-might-want-to-collect"></a>Fontes de log que você talvez queira coletar
* **Logs do Service Fabric:** emitidos pela plataforma usando [LTTng](http://lttng.org) e carregados em sua conta de armazenamento. Os logs podem ser eventos operacionais ou de tempo de execução emitidos pela plataforma. Esses logs são armazenados no local especificado pelo manifesto do cluster. (Para obter os detalhes da conta de armazenamento, procure a marca **AzureTableWinFabETWQueryable** e busque por **StoreConnectionString**.)
* **Eventos do aplicativo:** eventos emitidos do seu código de serviço. Você pode usar qualquer solução de log que grave arquivos de log baseados em texto, por exemplo, LTTng. Para mais informações, consulte a documentação do LTTng no rastreamento do aplicativo.  

## <a name="deploy-the-diagnostics-extension"></a>Implantar a extensão de diagnóstico
A primeira etapa para coletar logs será implantar a extensão Diagnóstico em cada uma das VMs no cluster do Service Fabric. A extensão de Diagnóstico coleta logs em cada VM e os carrega para a conta de armazenamento especificada. As etapas variam com base em seu uso do Portal do Azure ou do Gerenciador de Recursos do Azure.

Para implantar a extensão de diagnóstico nas VMs no cluster como parte da criação do cluster, defina **Diagnóstico** como **Ativado**. Depois de criar o cluster, você não poderá alterar essa configuração por meio do portal.

Em seguida, configure o LAD (Diagnóstico do Azure para o Linux) para coletar os arquivos e colocá-los na sua conta de armazenamento. Este processo é explicado como cenário 3 ("carregar seus próprios arquivos de log") no artigo [Usando o LAD para monitorar e diagnosticar as VMs do Linux](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json). Seguindo esse processo, você terá acesso aos rastreamentos. Você poderá carregar os rastreamentos em um visualizador de sua escolha.

Você também poderá implantar a extensão de diagnóstico usando o Gerenciador de Recursos do Azure. O processo é semelhante para o Windows e para o Linux, e é documentado para clusters do Windows em [Como coletar logs com o Diagnóstico do Azure](service-fabric-diagnostics-how-to-setup-wad.md).

Você também pode usar o Operations Management Suite, conforme descrito em [Análise de Log do Operations Management Suite com o Linux](https://blogs.technet.microsoft.com/hybridcloud/2016/01/28/operations-management-suite-log-analytics-with-linux/).

Depois que essa configuração for definida, o agente do LAD monitora os arquivos de log especificados. Sempre que uma nova linha for anexada ao arquivo, ela cria uma entrada de syslog que é enviada para o armazenamento especificado.

## <a name="next-steps"></a>Próximas etapas
Confira [Documentação do LTTng](http://lttng.org/docs) e [Usando o LAD](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) para entender mais detalhadamente quais eventos você deve examinar ao solucionar problemas.


