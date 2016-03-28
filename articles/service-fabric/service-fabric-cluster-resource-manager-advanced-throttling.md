<properties
   pageTitle="Introdução ao Gerenciador de Recursos de Cluster do Service Fabric | Microsoft Azure"
   description="Uma introdução ao Gerenciador de Recursos de Cluster do Service Fabric."
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
   ms.date="03/10/2016"
   ms.author="masnider"/>


# Limitando o comportando do gerenciador de recursos do cluster do Service Fabric
Às vezes, mesmo que você tenha configurado o gerenciador de recursos corretamente, o cluster poderá ser interrompido (muitas falhas de domínio de nó ou de falha durante a execução ou aplicação de atualizações, etc.). O Gerenciador de Recursos fará o melhor para corrigir tudo, mas, em situações como essa, talvez você queira considerar o uso de um suporte para que o próprio cluster tenha a oportunidade de se estabilizar (os nós que devem retornar retornarão, as condições da rede serão corrigidas automaticamente, os bits corrigidos serão implantados). Para ajudar com esses tipos de situações, o Gerenciador de Recursos inclui várias restrições. Observe que eles são “martelos grandes” e geralmente não devem ser usados a menos que seja feito um cálculo cuidadoso em relação à quantidade de trabalho paralelo que realmente pode ser feito no cluster, bem como se houver uma necessidade frequente em responder a esses tipos de eventos de reconfiguração macroscópicos não planejados (também conhecidos como “dias péssimos”).

Em geral, recomendamos evitar esses dias péssimos por meio de outras opções (primeiramente, como atualizações de código regulares e evitar o excesso de agendamentos no cluster), em vez de limitar o cluster para impedir que ele use recursos quando estiver tentando se autocorrigir. Contudo, você pode determinar que há casos em que (até que seja possível corrigi-los) você precisa ter algumas restrições em vigor, mesmo que isso signifique que o cluster levará mais tempo para se estabilizar.

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

Esteja ciente de que na maioria das vezes, temos visto clientes usarem esses limitadores porque eles já estão em um ambiente com recursos restritos (como largura de banda de rede limitada em nós individuais), o que significa que tais operações não seriam bem-sucedidas ou seriam lentas de qualquer forma, e os clientes estavam confortáveis em saber que estavam potencialmente aumentando a quantidade de tempo que levaria para o cluster atingir um estado estável, inclusive sabendo que eles poderiam acabar com a execução em um nível de confiabilidade geral mais baixo enquanto estivessem limitados.

## Próximas etapas
- Para descobrir como o Gerenciador de Recursos de Cluster gerencia e balanceia carga no cluster, confira o artigo sobre [como balancear carga](service-fabric-cluster-resource-manager-balancing.md).
- O Gerenciador de Recursos de Cluster tem muitas opções para descrever o cluster. Para saber mais sobre elas, confira este artigo sobre a [descrição de um cluster do Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)

<!---HONumber=AcomDC_0316_2016-->