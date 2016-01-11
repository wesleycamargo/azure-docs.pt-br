<properties
   pageTitle="Empacotamento proativo de métricas | Microsoft Azure"
   description="Uma visão geral de como usar o empacotamento proativo de métricas no Balanceador de recursos"
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

# Empacotamento proativo de métricas

Uma configuração comum do Balanceador de Recursos no Service Fabric do Azure é atingir utilização igual para cada métrica em cada nó. O Balanceador de Recursos também é responsável por encontrar um local para os serviços quando novas solicitações de serviço chegam. Se não houver espaço livre suficiente para colocar novas réplicas de serviço (todas as réplicas de todas as partições dos serviços), o Balanceador de Recursos tentará criar espaço movendo as cargas de trabalho existentes. Embora isso seja perfeitamente normal, dependendo de quão cheio o cluster está e em qual nível está a fragmentação da carga de trabalho, isso pode demorar.

Por exemplo, se o cluster for utilizado decentemente e se o cliente quiser adicionar um novo serviço com uma carga grande padrão (por exemplo, capacidade máxima do nó para uma ou mais métricas), o Balanceador de Recursos talvez precise mover muitas réplicas para colocar o novo serviço. Além disso, se os serviços forem com estado e grandes, executar as movimentações necessárias pode demorar um pouco, uma vez que os dados precisam ser copiados. Essas duas questões podem estender o tempo de criação de um novo serviço. Embora, de modo geral, os serviços devam ser tolerantes aos eventuais tempos lentos de criação, algumas cargas de trabalho são menos tolerantes e desejam ser criadas assim que possível. Em um estado estável, o Balanceador de Recursos precisa garantir que o cluster seja "desfragmentado" a fim de que haja mais chance de espaço suficiente estar disponível para novas cargas de trabalho.

O mecanismo de empacotamento proativo de métricas (também conhecido como desfragmentação) é executado como parte da fase de balanceamento no Balanceador de Recursos. A meta é reduzir o tempo de criação de serviço empacotando cargas de trabalho em menos nós, em vez de distribui-las durante o balanceamento. Quando uma métrica é configurada para desfragmentação, o Balanceador de Recursos é direcionado para atingir o desvio máximo padrão médio, e não o desvio mínimo padrão médio que é usado para balanceamento.

Com o desvio máximo, o Balanceador de Recursos tentará colocar o máximo de serviços possível em alguns nós enquanto mantém o máximo possível de nós vazios. Além disso, uma das restrições básicas para o posicionamento de novos serviços é que as réplicas não podem estar no mesmo domínio de atualização ou domínio de falha. Como o objetivo é poder adicionar novos serviços rapidamente, o Balanceador de Recursos deve tentar ter desvio mínimo padrão de distribuição de carga entre domínios de atualização e domínios de falha (soma das cargas de serviço por domínio de atualização/domínio de falha). O resultado será a mesma quantidade de espaço livre por domínio de atualização/falha. A desfragmentação também respeita todas as outras restrições no sistema, como afinidade, restrições de posicionamento e capacidade da métrica do nó.

## Configuração do cluster do Balanceador de Recursos
No manifesto do cluster, os valores de configuração a seguir definem o comportamento geral do recurso de empacotamento de métricas no Balanceador de Recursos.

### DefragmentationMetrics

As métricas de desfragmentação são métricas que o Balanceador de Recursos deve considerar para empacotamento proativo/desfragmentação. Todas as métricas configuradas devem ser especificadas nessa lista (assim como nas listas de limites de atividade e balanceamento). Se a métrica for especificada com o valor "true", ela será tratado como uma métrica de desfragmentação. Com o valor "false" (ou se a métrica não for especificada nesta lista), a métrica não será considerada para desfragmentação.

``` xml
<FabricSettings>
  <Section Name="DefragmentationMetrics">
    <Parameter Name="MetricName1" Value="true"/>
    <Parameter Name="MetricName2" Value="false"/>
  </Section>
</FabricSettings>
```

### BalacingThreshholds

Os limites de balanceamento controlam quão fragmentado o cluster pode se tornar em relação a uma métrica específica antes que o Balanceador de Recursos execute a fase de balanceamento (que aplicará a lógica de empacotamento de métricas). Se a métrica for considerada uma métrica de desfragmentação, o limite de balanceamento será a taxa mínima entre os nós usados de modo máximo e mínimo por domínio de atualização ou falha que o Balanceador de Recursos permite que exista, antes que ele desfragmente o cluster. Se nenhum domínio de atualização ou falha tiver essa taxa menor que o limite, a fase de desfragmentação será iniciada.

A figura a seguir mostra dois exemplos em que o limite de balanceamento para a métrica fornecida é 10.

![Exemplos de limites de balanceamento][Image1]

Observe que, neste momento, a "utilização" em um nó não leva em consideração o tamanho do nó, conforme determinado pela capacidade do nó. Ela leva em consideração apenas o uso absoluto atualmente relatado no nó para a métrica especificada.

Se a métrica não for especificada, o valor padrão será 1. Nesse caso, a desfragmentação será executada até que o cluster tenha pelo menos um nó vazio em cada domínio de atualização e em cada domínio de falha.

O valor 0 significa não levar em conta essa métrica durante a fase de balanceamento — seja ele considerado para desfragmentação ou não.

O exemplo de código abaixo mostra que os limites de balanceamento das métricas são configurados por métrica pelo elemento FabricSettings no manifesto do cluster.

``` xml
<FabricSettings>
  <Section Name="MetricBalancingThresholds">
    <Parameter Name="MetricName" Value="10"/>
  </Section>
</FabricSettings>
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas

Para obter mais informações: [Arquitetura do Balanceador de Recursos](service-fabric-resource-balancer-architecture.md)

[Image1]: media/service-fabric-resource-balancer-proactive-metric-packing/PMP.png

<!---HONumber=AcomDC_1223_2015-->