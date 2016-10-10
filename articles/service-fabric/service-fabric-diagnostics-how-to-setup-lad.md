<properties
   pageTitle="Como coletar logs com o Diagnóstico do Azure para o Linux | Microsoft Azure"
   description="Este artigo descreve como configurar o Diagnóstico do Azure para coletar logs de um cluster do Linux do Service Fabric em execução no Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/28/2016"
   ms.author="subramar"/>


# Como coletar logs com o Diagnóstico do Azure

> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
- [Linux](service-fabric-diagnostics-how-to-setup-lad.md)

Quando você estiver executando um cluster de Service Fabric do Azure, é uma boa ideia coletar os logs de todos os nós em um local central. Ter os logs em uma localização central facilita a análise e a resolução de problemas, independentemente de eles estarem em seus serviços, aplicativos ou no próprio cluster. Uma forma de carregar e coletar logs é usar a extensão de Diagnóstico do Azure, que carrega os logs no Armazenamento do Azure. Você pode ler os eventos do armazenamento e colocá-los em um produto, como a [Pesquisa Elástica](service-fabric-diagnostic-how-to-use-elasticsearch.md), ou em outra solução de análise de log.

## Fontes de log diferentes que você talvez queira coletar
1. **Logs do Service Fabric:** emitidos pela plataforma usando LTTng e carregados em sua conta de armazenamento. Os logs podem ser eventos operacionais ou de tempo de execução emitidos pela plataforma. Estes logs são armazenados na localização indicada em seu manifesto do cluster (Pesquise pela marcação "AzureTableWinFabETWQueryable" e procure por "StoreConnectionString" para obter os detalhes da conta de armazenamento.
2. **Eventos do aplicativo:** eventos emitidos do seu código de serviços. Você pode usar qualquer solução de log que grava arquivos de log baseados em texto, por exemplo, [LTTng](http://lttng.org). Consulte a documentação do LTTng no rastreamento do aplicativo.


## Implantar as extensões de diagnóstico
A primeira etapa para coletar logs será implantar a extensão Diagnóstico em cada uma das VMs no cluster do Service Fabric. A extensão de Diagnóstico coleta logs em cada VM e carrega-os para a conta de armazenamento especificada. As etapas variam com base em seu uso do Portal do Azure ou do Azure Resource Manager.

### Implantar a extensão de diagnóstico como parte da criação do cluster 
Para implantar a extensão de diagnóstico nas VMs no cluster como parte da criação do cluster, defina "Diagnóstico" como **Ativado**. Depois que o cluster tiver sido criado, esta configuração não poderá ser alterada usando o portal.

Configure o LAD (Diagnóstico do Azure para o Linux) para coletar os arquivos e colocá-los na conta de armazenamento dos clientes. Este processo é explicado como cenário 3 ("carregar seus próprios arquivos de log") no artigo [Using LAD to monitor and diagnose Linux VMs](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md) (Usando o LAD para monitorar e diagnosticar as VMs do Linux). Em seguida, este processo concede a você acesso aos rastreamentos, que podem ser carregados em um visualizador de sua escolha.


Você também pode implantar a extensão de diagnóstico usando o Azure Resource Manager. O processo é semelhante para o Windows e para o Linux, e é documentado para clusters do Windows em [How to collect logs with Azure Diagnostics](service-fabric-diagnostics-how-to-setup-wad.md) (Como coletar logs com o Diagnóstico do Azure).

Você também pode usar o OMS, conforme descrito em [OMS log analytics with linux](https://blogs.technet.microsoft.com/hybridcloud/2016/01/28/operations-management-suite-log-analytics-with-linux/) (Log Analytics do OMS com o Linux).

Depois que essa configuração for definida, o agente do LAD monitora os arquivos de log especificados. Sempre que uma nova linha for anexada ao arquivo, ela cria uma entrada de syslog que é enviada para o armazenamento especificado pelo cliente.


## Próximas etapas
Confira a [Documentação do LTTng](http://lttng.org/docs) e [Using LAD](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md) (Usando o LAD) para entender mais detalhadamente quais eventos você deve examinar ao solucionar problemas.

<!---HONumber=AcomDC_0928_2016-->