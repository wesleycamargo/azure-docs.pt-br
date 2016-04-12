<properties
	pageTitle="Azure Insights: métricas comuns de dimensionamento automático do Azure Insights. | Microsoft Azure"
	description="Saiba quais métricas são usadas comumente para fazer o dimensionamento automático dos Serviços de Nuvem, Máquinas Virtuais e Aplicativos Web."
	authors="kamathashwin"
	manager=""
	editor=""
	services="azure-portal"
	documentationCenter="na"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="ashwink"/>

# Métricas comuns de dimensionamento automático do Azure Insights

O dimensionamento automático do Azure Insights permite que você aumente ou reduza número de instâncias em execução com base nos dados de telemetria (métricas). Este documento descreve as métricas mais comuns que você pode querer usar. No Portal do Azure para Serviços de Nuvem e Farms de Servidores, você pode escolher a métrica do recurso segundo a qual o dimensionamento será feito. No entanto, também é possível escolher qualquer métrica de um recurso diferente e fazer o dimensionamento com base nela.

Veja os detalhes de como localizar e listar as métricas segundo as quais você deseja dimensionar. O seguinte se aplica também ao dimensionamento de Conjuntos de Escala de Máquina Virtual.

## Métricas de computação
Por padrão, a VM v2 do Azure vem com a extensão de diagnóstico configurada e tem as seguintes métricas ativadas.

- [Métricas de convidado para VM v2 do Windows](#compute-metrics-for-windows-vm-v2-as-a-guest-os)
- [Métricas de convidado para VM v2 Linux](#compute-metrics-for-linux-vm-v2-as-a-guest-os)

Você pode usar o PoSH/API/CLI `Get MetricDefinitions` para exibir as métricas disponíveis para o recurso de VMSS.

Se você estiver usando conjuntos de escala de VM e não vir uma métrica específica listada, provavelmente ela estará *desabilitada* em sua extensão de diagnóstico.

Se uma determinada métrica não estiver sendo amostrada ou transferida com a frequência que você deseja, você poderá atualizar a configuração de diagnóstico.

Se qualquer um dos casos acima for verdadeiro, consulte [Usar o PowerShell para habilitar o Diagnóstico do Azure em uma máquina virtual que executa o Windows](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md) sobre o PowerShell para configurar e atualizar sua extensão de diagnóstico de VM do Azure para habilitar a métrica. Esse artigo também inclui um arquivo de configuração de diagnóstico de exemplo.

### Computar métricas para VMs v2 do Windows como um SO convidado

Quando você cria uma nova VM (v2) no Azure, o diagnóstico é habilitado usando a extensão de Diagnóstico.

Você pode gerar uma lista das métricas usando o seguinte comando do PowerShell.


```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Você pode criar um alerta para as métricas a seguir.

|Nome da métrica|	Unidade|
|---|---|
|\\Processor(\_Total)\\% Processor Time |Porcentagem| |\\Processor(\_Total)\\% Privileged Time |Porcentagem| |\\Processor(\_Total)\\% User Time |Porcentagem| |\\Processor Information(\_Total)\\Processor Frequency |Contagem| |\\System\\Processes| Contagem| |\\Process(\_Total)\\Thread Count| Contagem| |\\Process(\_Total)\\Handle Count |Contagem| |\\Memory\\% Committed Bytes In Use |Porcentagem| |\\Memory\\Available Bytes| Bytes| |\\Memory\\Committed Bytes |Bytes| |\\Memory\\Commit Limit| Bytes| |\\Memory\\Pool Paged Bytes| Bytes| |\\Memory\\Pool Nonpaged Bytes| Bytes| |\\PhysicalDisk(\_Total)\\% Disk Time| Porcentagem| |\\PhysicalDisk(\_Total)\\% Disk Read Time| Porcentagem| |\\PhysicalDisk(\_Total)\\% Disk Write Time| Porcentagem| |\\PhysicalDisk(\_Total)\\Disk Transfers/sec |CountPerSecond| |\\PhysicalDisk(\_Total)\\Disk Reads/sec |CountPerSecond| |\\PhysicalDisk(\_Total)\\Disk Writes/sec |CountPerSecond| |\\PhysicalDisk(\_Total)\\Disk Bytes/sec |BytesPerSecond| |\\PhysicalDisk(\_Total)\\Disk Read Bytes/sec| BytesPerSecond| |\\PhysicalDisk(\_Total)\\Disk Write Bytes/sec |BytesPerSecond| |\\PhysicalDisk(\_Total)\\Avg. Disk Queue Length| Contagem| |\\PhysicalDisk(\_Total)\\Avg. Disk Read Queue Length| Contagem| |\\PhysicalDisk(\_Total)\\Avg. Disk Write Queue Length |Contagem| |\\LogicalDisk(\_Total)\\% Free Space| Porcentagem| |\\LogicalDisk(\_Total)\\Free Megabytes| Contagem|



### Computar métricas para VMs v2 Linux como um SO convidado

Quando você cria uma nova VM (v2) no Azure, o diagnóstico é habilitado por padrão usando a extensão de Diagnóstico.

Você pode gerar uma lista das métricas usando o seguinte comando do PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 Você pode criar um alerta para as métricas a seguir.

|Nome da métrica|	Unidade|
|---|---|
|\\Memory\\AvailableMemory |Bytes|
|\\Memory\\PercentAvailableMemory|	Porcentagem|
|\\Memory\\UsedMemory|	Bytes|
|\\Memory\\PercentUsedMemory|	Porcentagem|
|\\Memory\\PercentUsedByCache |Porcentagem|
|\\Memory\\PagesPerSec|	CountPerSecond|
|\\Memory\\PagesReadPerSec|	CountPerSecond|
|\\Memory\\PagesWrittenPerSec |CountPerSecond|
|\\Memory\\AvailableSwap |Bytes|
|\\Memory\\PercentAvailableSwap|	Porcentagem|
|\\Memory\\UsedSwap |Bytes|
|\\Memory\\PercentUsedSwap|	Porcentagem|
|\\Processor\\PercentIdleTime|	Porcentagem|
|\\Processor\\PercentUserTime|	Porcentagem|
|\\Processor\\PercentNiceTime |Porcentagem|
|\\Processor\\PercentPrivilegedTime |Porcentagem|
|\\Processor\\PercentInterruptTime|	Porcentagem|
|\\Processor\\PercentDPCTime|	Porcentagem|
|\\Processor\\PercentProcessorTime |Porcentagem|
|\\Processor\\PercentIOWaitTime |Porcentagem|
|\\PhysicalDisk\\BytesPerSecond|	BytesPerSecond|
|\\PhysicalDisk\\ReadBytesPerSecond|	BytesPerSecond|
|\\PhysicalDisk\\WriteBytesPerSecond|	BytesPerSecond|
|\\PhysicalDisk\\TransfersPerSecond |CountPerSecond|
|\\PhysicalDisk\\ReadsPerSecond |CountPerSecond|
|\\PhysicalDisk\\WritesPerSecond |CountPerSecond|
|\\PhysicalDisk\\AverageReadTime|	Segundos|
|\\PhysicalDisk\\AverageWriteTime |Segundos|
|\\PhysicalDisk\\AverageTransferTime|	Segundos|
|\\PhysicalDisk\\AverageDiskQueueLength|	Contagem|
|\\NetworkInterface\\BytesTransmitted |Bytes|
|\\NetworkInterface\\BytesReceived |Bytes|
|\\NetworkInterface\\PacketsTransmitted |Contagem|
|\\NetworkInterface\\PacketsReceived |Contagem|
|\\NetworkInterface\\BytesTotal |Bytes|
|\\NetworkInterface\\TotalRxErrors|	Contagem|
|\\NetworkInterface\\TotalTxErrors|	Contagem|
|\\NetworkInterface\\TotalCollisions|	Contagem|




## Métricas da Web (Farm de servidores) usadas normalmente

Você também pode realizar a autoescala com base em métricas comuns do servidor Web, como o comprimento da fila Http. O nome da métrica é **HttpQueueLength**. A seção a seguir lista as métricas de farm de servidores (aplicativos Web) disponíveis.

### Métricas de aplicativos Web

Você pode gerar uma lista das métricas de aplicativos Web usando o seguinte comando do PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Você pode alertar ou dimensionar com base nessas métricas.

|Nome da métrica|	Unidade|
|---|---|
|CpuPercentage|	Porcentagem|
|MemoryPercentage |Porcentagem|
|DiskQueueLength|	Contagem|
|HttpQueueLength|	Contagem|
|BytesReceived|	Bytes|
|BytesSent|	Bytes|


## Métricas de armazenamento usadas normalmente
Você pode dimensionar segundo o comprimento da fila de armazenamento, que é o número de mensagens na fila de armazenamento. O comprimento da fila de armazenamento é uma métrica especial e o limite aplicado será o número de mensagens por instância. Isso significa que, se houver duas instâncias e o limite for definido como 100, ele será dimensionado quando o número total de mensagens na fila for 200. Por exemplo, 100 mensagens por instância.

Você pode definir essas configurações na folha **Configurações** do Portal do Azure. Para conjuntos de escala de VM, você pode atualizar a configuração de Autoescala no modelo do ARM para utilizar *metricName* como *ApproximateMessageCount* e passar a ID da fila de armazenamento como *metricResourceUri*.


```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

## Métricas do Barramento de Serviço usadas frequentemente

Você pode dimensionar segundo o comprimento da fila do Barramento de Serviço, que é o número de mensagens na fila do Barramento de Serviço. O comprimento da fila de Barramento de Serviço é uma métrica especial e o limite especificado aplicado será o número de mensagens por instância. Isso significa que, se houver duas instâncias e o limite for definido como 100, ele será dimensionado quando o número total de mensagens na fila for 200. Por exemplo, 100 mensagens por instância.

Para conjuntos de escala de VM, você pode atualizar a configuração de Autoescala no modelo do ARM para utilizar *metricName* como *ApproximateMessageCount* e passar a ID da fila de armazenamento como *metricResourceUri*.

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

>[AZURE.NOTE] Para o Barramento de Serviço, o conceito de grupo de recursos não existe, mas o Azure Resource Manager cria um grupo de recursos padrão por região. O grupo de recursos geralmente está no formato 'Default - ServiceBus-[região]'. Por exemplo, 'Default-ServiceBus-EastUS', 'Default-ServiceBus-WestUS', 'Default-ServiceBus-AustraliaEast' etc.

<!-----------HONumber=AcomDC_0330_2016-->