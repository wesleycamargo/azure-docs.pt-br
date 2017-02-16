---
title: "Resource Manager de Cluster do Service Fabric – políticas de posicionamento | Microsoft Docs"
description: "Visão geral das políticas de posicionamento adicionais e das regras para os Serviços do Service Fabric"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 5c2d19c6-dd40-4c4b-abd3-5c5ec0abed38
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 7fa35de8b99132ad1c10229a9bb2231f11fdbaa0


---
# <a name="placement-policies-for-service-fabric-services"></a>Políticas de posicionamento para serviços do Service Fabric
Há muitas regras adicionais diferentes que podem precisar ser configuradas em alguns cenários raros. Alguns exemplos desses cenários são:
* Se o cluster do Service Fabric está distribuído em uma distância geográfica, como em vários datacenters locais ou várias regiões do Azure
* Se seu ambiente abrange várias áreas de controle geopolítico (ou algum outro caso em que você tenha limites legais ou de política importantes)
* Há considerações de desempenho/latência reais devido à comunicação do cluster viajar grandes distâncias ou transitar em determinadas redes mais lentas ou menos confiáveis.

Nesses tipos de situações, pode ser importante para um determinado serviço sempre ser executado ou nunca ser executado em determinadas regiões. Da mesma forma pode ser importante tentar colocar o primário em uma determinada região para minimizar a latência do usuário final.

As políticas de posicionamento avançadas são:

1. Domínios inválidos
2. Domínios necessários
3. Domínios preferenciais
4. Desativação de empacotamento de réplica

A maioria dos controles a seguir pode ser configurada por meio das propriedades de nó e de restrições de posicionamento, mas algumas são mais complicadas. Para simplificar, o Cluster Resource Manager do Service Fabric fornece essas políticas de posicionamento adicionais. Assim como com outras restrições de posicionamento, as políticas de posicionamento podem ser configuradas de acordo com a instância de serviço nomeada e atualizada dinamicamente.

## <a name="specifying-invalid-domains"></a>Especificando domínios inválidos
A política de posicionamento InvalidDomain permite especificar que um determinado domínio de falha é inválido para essa carga de trabalho. Essa política garante que um serviço específico nunca seja executado em uma determinada área, por exemplo, por motivos de política corporativa ou geopolíticos. Vários domínios inválidos podem ser especificados através de diferentes políticas.

<center>
![Exemplo de domínio inválido][Image1]
</center>

Código:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>Especificando domínios necessários
A política de posicionamento de domínio necessária exige que todas as réplicas com estado ou instâncias de serviço sem estado para o serviço estejam presentes no domínio especificado. Vários domínios necessários podem ser especificados através de diferentes políticas.

<center>
![Exemplo de domínio necessário][Image2]
</center>

Código:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas"></a>Especificando um domínio preferido para as réplicas primárias
O domínio primário preferencial é um controle interessante, pois permite a seleção do domínio de falha no qual o primário deve ser posicionado, caso isso seja possível. O primário é encerrado nesse domínio quando tudo está íntegro. Caso o domínio ou a réplica primária falhe ou seja desligado por algum motivo, o primário é migrado para outra localização. Se essa nova localização não estiver no domínio preferencial, o Cluster Resource Manager vai movê-lo de volta ao domínio preferencial assim que possível. Obviamente, essa configuração só faz sentido para serviços com estado. Essa política é mais útil em clusters que estão distribuídos em regiões do Azure ou em vários datacenters, mas preferem que as réplicas primárias sejam posicionadas em uma determinada localização. Manter as primárias próximas aos seus usuários ajuda a fornecer uma latência menor, especialmente para leitores.

<center>
![Domínios primários preferenciais e failover][Image3]
</center>

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replicas-to-be-distributed-among-all-domains-and-disallowing-packing"></a>Exigindo que réplicas sejam distribuídas entre todos os domínios e desativando o empacotamento
As réplicas são _normalmente_ distribuídas entre os domínios em que cluster está íntegro, mas há casos em que réplicas de uma determinada partição podem ficar temporariamente empacotadas em um único domínio. Por exemplo, digamos que o cluster tem nove nós em três domínios de falha (fd:/0, fd:/1 e fd:/2) e o serviço tem três réplicas. Digamos que os nós que estavam sendo usados para as réplicas em fd:/1 e fd:/2 foram desativados. Agora, normalmente o Cluster Resource Manager preferiria outros nós nos mesmos domínios de falha. Nesse caso, digamos que devido a problemas de capacidade nenhum dos outros nós nesses domínios era válido. Se o Cluster Resource Manager criasse substituições para essas réplicas, ele precisaria escolher nós em fd:/0. No entanto, fazer _isso_ cria uma situação em que a restrição de domínio de falha está sendo violada. Isso também aumenta a chance de o conjunto de réplicas inteiro ser perdido (se o FD 0 fosse perdido permanentemente). Para obter mais informações sobre restrições e prioridades de restrições em geral, confira [este tópico](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities).

Se você já viu um aviso de integridade como `The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain`, você atingiu essa condição ou algo parecido. Isso é raro, mas pode acontecer e geralmente essas situações são transitórias uma vez que os nós voltam. Se os nós permanecem inativos e o Cluster Resource Manager precisa criar substituições, normalmente haverá outros nós disponíveis nos domínios de falha ideais.

Algumas cargas de trabalho preferirão sempre ter o número de destino de réplicas, mesmo se elas forem incluídas em menos domínios. Essas cargas de trabalho apostam contra falhas de domínio permanentes simultâneas totais e normalmente podem recuperar o estado local. Outras cargas de trabalho preferem passar logo pelo tempo de inatividade do que arriscar a correção ou a perda de dados. Uma vez que a maioria das cargas de trabalho de produção executa mais que três réplicas, mais que três domínios de falha e muitos nós válidos por domínio de falha, o padrão é não exigir a distribuição de domínio. Isso permite que o balanceamento e o failover normais tratem desses casos, mesmo que isso signifique que temporariamente um domínio pode ter várias réplicas incluídas nele.

Se você desejar desabilitar esse empacotamento para uma determinada carga de trabalho, poderá especificar a política “RequireDomainDistribution” no serviço. Quando essa política é definida, o Cluster Resource Manager garante que nunca sejam permitidas duas réplicas da mesma partição no mesmo domínio de atualização ou de falha.

Código:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Agora, seria possível usar essas configurações para serviços em um cluster que não tenha sido distribuído geograficamente? Claro que sim! Mas também não há um bom motivo. As configurações de domínio necessárias, inválidas e preferenciais devem ser evitadas, a menos que você esteja realmente executando um cluster que abrange distâncias geográficas. Não faz sentido tentar forçar uma determinada carga de trabalho a ser executada em um único rack ou preferir algum segmento do seu cluster local em vez de outro. Diferentes configurações de hardware devem ser distribuídas entre domínios e aqueles manipulados por propriedades de nó e restrições de posicionamento normais.

## <a name="next-steps"></a>Próximas etapas
* Para obter mais informações sobre as opções disponíveis para configurar serviços, acesse [Saiba mais sobre a configuração de serviços](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png



<!--HONumber=Jan17_HO1-->


