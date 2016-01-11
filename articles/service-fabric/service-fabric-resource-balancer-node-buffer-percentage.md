<properties
   pageTitle="Porcentagem de buffer do nó | Microsoft Azure"
   description="Uma visão geral da função de porcentagem de buffer do nó no Balanceador de Recursos"
   services="service-fabric"
   documentationCenter=".net"
   authors="GaugeField"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/03/2015"
   ms.author="masnider"/>

# Visão geral da porcentagem de buffer do nó

Atualmente, os clientes podem especificar o limite da capacidade do nó como uma restrição que o Balanceador de Recursos respeita com base na prioridade da restrição. Se a prioridade da restrição de capacidade for alta (a capacidade do nó não pode ser violada) e se os nós de cluster forem altamente utilizados, talvez o failover não seja imediato ou a capacidade do nó seja violada.

Os problemas acontecerão se os nós com réplicas secundárias estiverem próximos da capacidade quando um nó com a réplica primária falhar. Nesse caso, se a carga primária for maior que a carga secundária, a réplica secundária não poderá ser imediatamente promovida sem superalocação do nó ou cópia da réplica.

Quando a lógica de empacotamento proativo estiver em execução, o número mais alto de nós de cluster estará mais próximo do limite da capacidade do nó. A porcentagem de buffer do nó é um recurso que evita o aumento do tempo de failover ou a alocação excessiva do nó durante o failover, permitindo que os clientes especifiquem a porcentagem do nó que deve ser mantida livre. As réplicas dos novos serviços não devem ser adicionadas ao espaço do buffer do nó, mas o Balanceador de Recursos deve poder usar a capacidade total do nó (contabilizando o espaço do buffer) para failovers e adição de réplicas ausentes.

Esse recurso pode ser usado mesmo quando o recurso de empacotamento proativo de métricas estiver desativado.

O exemplo de código abaixo mostra que as porcentagens de buffer do nó para métricas são configuradas por métrica por meio do elemento FabricSettings no manifesto do cluster.

``` xml
<FabricSettings>
  <Section Name=" NodeBufferPercentage">
    <Parameter Name="MetricName" Value="0.1"/>
  </Section>
</FabricSettings>

```

O valor 0,1 da métrica com o nome “MetricName” significa que 10 por cento da capacidade de cada nó da métrica “MetricName” deve ser mantida livre.

Se o valor não for especificado nessa seção, 0 será usado como o valor padrão.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas

Para saber mais: [Arquitetura do Balanceador de Recursos](service-fabric-resource-balancer-architecture.md)

<!---HONumber=AcomDC_1223_2015-->