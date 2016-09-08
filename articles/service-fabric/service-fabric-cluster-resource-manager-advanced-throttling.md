<properties
   pageTitle="Limitação no Resource Manager de Cluster do Service Fabric | Microsoft Azure"
   description="Aprenda a configurar os limitadores fornecidos pelo Resource Manager de Cluster do Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/19/2016"
   ms.author="masnider"/>


# Limitando o comportamento do Gerenciador de Recursos do cluster do Service Fabric
Mesmo se você tiver configurado o Gerenciador de Recursos do Cluster corretamente, o cluster poderá ser interrompido. Por exemplo, poderia haver nós simultâneos ou falhas do tipo domínio de falha – o que aconteceria se isso ocorresse durante uma atualização? O Gerenciador de Recursos fará o melhor para corrigir tudo, mas, em situações como essa, talvez você queira considerar o uso de um suporte para que o próprio cluster tenha a oportunidade de se estabilizar (os nós que devem retornar retornarão, as condições da rede serão corrigidas automaticamente, os bits corrigidos serão implantados). Para ajudar com esses tipos de situações, o Gerenciador de Recursos de Cluster do Service Fabric inclui várias restrições. Observe que essas restrições são bastante perturbadoras e, no geral, não devem ser usadas, a menos que seja feito um cálculo cuidadoso em relação à quantidade de trabalho paralelo que realmente pode ser feito no cluster, bem como se houver uma necessidade frequente em responder a esses tipos de eventos de reconfiguração macroscópicos não planejados (também conhecidos como “dias péssimos”).

Em geral, recomendamos evitar esses dias péssimos por meio de outras opções (primeiramente, como atualizações de código regulares e evitar o excesso de agendamentos no cluster), em vez de limitar o cluster para impedir que ele use recursos quando estiver tentando se autocorrigir. Os limitadores têm valores padrão que julgamos pela experiência serem padrões bons. No entanto, provavelmente você deverá conferi-los e ajustá-los de acordo com sua carga real esperada. Embora não restringir ou carregar excessivamente o cluster seja uma melhor prática, você poderá determinar que haverá casos em que (até que seja possível corrigi-los) será necessário colocar algumas restrições em vigor, mesmo que isso signifique que o cluster levará mais tempo para se estabilizar.

##Configurando os limitadores
As restrições incluídas por padrão são:

-	GlobalMovementThrottleThreshold – isso controla o número total de movimentações no cluster ao longo de um período de tempo (definido como GlobalMovementThrottleCountingInterval, com o valor em segundos)
-	MovementPerPartitionThrottleThreshold – isso controla o número total de movimentações de qualquer partição de serviço ao longo de um período de tempo (o MovementPerPartitionThrottleCountingInterval, com o valor em segundos)

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThreshold" Value="1000" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
     <Parameter Name="MovementPerPartitionThrottleThreshold" Value="50" />
     <Parameter Name="MovementPerPartitionThrottleCountingInterval" Value="600" />
</Section>
```

Lembre-se de que, na maioria das vezes que vimos clientes usarem essas restrições foi porque eles já estavam em um ambiente de restrição de recursos (como largura de banda de rede limitada em nós individuais ou discos que não estavam dentro das exigências de compilações de réplica paralela que estavam sendo aplicadas neles), o que significa que essas operações não teriam êxito ou seriam lentas de qualquer forma. Nessas situações, os clientes estavam familiarizados, sabendo que estavam, potencialmente, aumentando a quantidade de tempo que levaria para o cluster alcançar um estado estável, inclusive sabendo que poderiam acabar executando em uma confiabilidade geral menor durante a restrição do cluster.

## Próximas etapas
- Para descobrir como o Gerenciador de Recursos de Cluster gerencia e balanceia carga no cluster, confira o artigo sobre [como balancear carga](service-fabric-cluster-resource-manager-balancing.md).
- O Gerenciador de Recursos de Cluster tem muitas opções para descrever o cluster. Para saber mais sobre elas, confira este artigo sobre a [descrição de um cluster do Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)

<!---HONumber=AcomDC_0824_2016-->