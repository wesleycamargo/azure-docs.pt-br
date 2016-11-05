---
title: Gerenciador de Recursos de Cluster do Service Fabric – Políticas de posicionamento | Microsoft Docs
description: Visão geral das políticas de posicionamento adicionais e das regras para os Serviços do Service Fabric
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''

ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/19/2016
ms.author: masnider

---
# Políticas de posicionamento para serviços do Service Fabric
Há muitas regras adicionais diferentes que podem acabar sendo um motivo de preocupação caso seu cluster do Service Fabric esteja distribuído por uma distância geográfica, digamos, em vários datacenters ou regiões do Azure, ou caso seu ambiente abranja várias áreas de controle geopolítico (ou algum outro caso em que você tenha limites legais ou da política que sejam de seu interesse, ou então, as distâncias envolvidas têm um impacto de desempenho real/de latência). A maioria delas pode ser configurada por meio das propriedades de nó e de restrições de posicionamento, mas algumas são mais complicadas. Para tornar as coisas mais simples, fornecemos essas comandos adicionais. Assim como as outras restrições de posicionamento, as políticas de posicionamento podem ser configuradas de acordo com a instância de serviço nomeada.

## Especificando domínios inválidos
A política de posicionamento InvalidDomain permite especificar que um determinado domínio de falha é inválido para essa carga de trabalho. Essa política garante que um serviço específico nunca seja executado em uma determinada área, por exemplo, por motivos de política corporativa ou geopolíticos. Vários domínios inválidos podem ser especificados através de diferentes políticas.

![Exemplo de domínio inválido][Image1]

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
## Especificando domínios necessários
A política de posicionamento de domínio necessária exige que todas as réplicas ou instâncias de serviço sem estado para o serviço estejam presentes no domínio especificado. Vários domínios necessários podem ser especificados através de diferentes políticas.

![Exemplo de domínio necessário][Image2]

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

## Especificando um domínio preferido para as réplicas primárias
O domínio primário preferencial é um controle interessante, pois permite a seleção do domínio de falha no qual o primário deve ser posicionado, caso isso seja possível. Quando tudo estiver íntegro, o primário chegará a esse domínio. Caso o domínio ou a réplica primária falhe ou seja desligado por algum motivo, o Primário será migrado para outro local. Se esse local não estiver no domínio preferencial, quando possível, o Gerenciador de Recursos de Cluster irá movê-lo de volta ao domínio preferencial. Obviamente, essa configuração só faz sentido para serviços com estado. Essa política é mais útil em clusters que estão incluídos em regiões do Azure ou em vários datacenters. Nessas situações você está usando todos os locais para redundância, mas preferiria que as réplicas primárias fossem posicionadas em um determinado local para fornecer latência mais baixa a operações que passam para o primário (gravações e também, por padrão, todas as leituras são atendidas pelo primário).

![Domínios primários preferenciais e failover][Image3]

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## Exigindo que réplicas sejam distribuídas entre todos os domínios e desativando o empacotamento
Outra política que você pode especificar é para exigir que as réplicas sempre sejam distribuídas entre os domínios de falha disponíveis. Isso acontece por padrão na maioria dos casos em que o cluster está íntegro, no entanto há casos de degeneração onde réplicas para uma determinada partição podem acabar temporariamente empacotadas em uma única falha ou domínio de atualização. Por exemplo, digamos que embora o cluster tenha 9 nós em 3 domínios de falha (0, 1 e 2) e o serviço possua 3 réplicas, os nós que estavam sendo usados para as réplicas nos domínios de falha 1 e 2 foram desativados e devido a problemas de capacidade nenhum dos outros nós nesses domínios eram válidos. Se o Service Fabric fosse para criar substituições para essas réplicas, o Gerenciador de Recursos de Cluster teria que colocá-los no domínio de falha 0, mas isso cria uma situação em que a restrição do domínio de falha está sendo violada. Isso também aumenta a chance do conjunto inteiro da réplicas ser perdido (se o domínio de falha 0 fosse perdido permanentemente). (Para obter mais informações sobre restrições e prioridades de restrições em geral, confira [este tópico](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities) )

Se você já viu um aviso de integridade como "O balanceador de carga detectou uma violação de restrição para esta réplica:malha:/<nome de algum serviço> partição secundária <ID de alguma partição> está violando a restrição: FaultDomain" você acionou essa condição ou algo parecido. Geralmente, essas situações são temporárias (os nós não ficam inativos por muito tempo ou se ficarem e precisamos criar substitutos existem outros nós nos domínios de falha corretos que são válidos), mas há algumas cargas de trabalho que trocariam a disponibilidade pelo risco de perder todas as suas réplicas. Podemos fazer isso especificando a política "RequireDomainDistribution", que garante que existam duas réplicas da mesma partição nunca sejam permitidas no mesmo domínio de falha ou atualização.

Algumas cargas de trabalho preferem ter número de destino de réplicas (cópias de estado) em todos os momentos (apostar contra falhas de domínio totais e saber que normalmente elas podem recuperar o estado local), enquanto outras prefeririam passar logo pelo tempo de inatividade e não arriscar questões de correção e perda de dados. Já que a maioria das cargas de trabalho de produção trabalha com mais de três réplicas, o padrão é não exigir a distribuição de domínio e deixar que o balanceamento e o failover manipulem os casos normalmente, mesmo que isso signifique que um domínio tenha temporariamente várias réplicas empacotadas nele.

Código:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Agora, seria possível usar essas configurações para serviços em um cluster que não tenha sido distribuído geograficamente? Claro que sim! Mas não há uma boa razão também, especialmente as configurações de domínio necessárias, inválidas e preferenciais devem ser evitadas, a menos que você esteja realmente executando um cluster geograficamente estendido - não faz sentido tentar forçar uma determinada carga de trabalho para execução em um único rack ou dar preferência a algum segmento do seu cluster local em vez de outro a menos que haja tipos diferentes de segmentação de hardware ou a carga de trabalho em andamento e esses casos podem ser tratados por meio de restrições de posicionamento normais.

## Próximas etapas
* Para saber mais sobre outras opções disponíveis para a configuração de serviços, confira o tópico sobre outras configurações disponíveis do Gerenciador de Recursos de Cluster em [Saiba mais sobre a configuração de serviços](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png

<!---HONumber=AcomDC_0824_2016-->