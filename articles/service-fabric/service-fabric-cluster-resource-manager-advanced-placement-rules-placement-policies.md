<properties
   pageTitle="Gerenciador de Recursos de Cluster do Service Fabric – Políticas de posicionamento | Microsoft Azure"
   description="Visão geral das políticas de posicionamento adicionais e das regras para os Serviços do Service Fabric"
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
   ms.date="05/20/2016"
   ms.author="masnider"/>

# Políticas de posicionamento para serviços do Service Fabric
Há muitas regras adicionais diferentes que podem acabar sendo um motivo de preocupação caso seu cluster do Service Fabric esteja distribuído por uma distância geográfica, digamos, em vários datacenters ou regiões do Azure, ou caso seu ambiente abranja várias áreas de controle geopolítico (ou algum outro caso em que você tenha limites legais ou da política que sejam de seu interesse, ou então, as distâncias envolvidas têm um impacto de desempenho real/de latência). A maioria delas pode ser configurada por meio das propriedades de nó e de restrições de posicionamento, mas algumas são mais complicadas. De qualquer modo, fornecemos esses atalhos – assim como as restrições de posicionamento, as políticas de posicionamento podem ser configuradas de acordo com instância de serviço nomeada.

## Especificando domínios inválidos
A política de posicionamento InvalidDomain permite especificar que um determinado domínio de falha é inválido para essa carga de trabalho. Essa política garante que um serviço específico nunca seja executado em uma determinada área, por exemplo, por motivos de política corporativa ou geopolíticos. Vários domínios inválidos podem ser especificados

![Exemplo de domínio inválido][Image1]

Código:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DCEast/"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/dc9/”)
```
## Especificando domínios necessários
A política de posicionamento de domínio necessária exige que todas as réplicas estejam presentes no domínio especificado. Vários domínios necessários podem ser especificados.

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
O domínio primário preferencial é um controle interessante, pois permite a seleção do domínio de falha no qual o primário deve ser posicionado, caso isso seja possível. Quando tudo estiver íntegro, o primário chegará a esse domínio. Caso o domínio ou a réplica primária falhe ou seja desligado por algum motivo, o Primário será migrado para outro local. Sempre que possível, ele será movido de volta ao domínio preferencial. Obviamente, essa configuração só faz sentido para serviços com estado. Essa política é mais útil em clusters geograficamente estendidos, em que você esteja usando outros locais para redundância, mas preferiria que as réplicas primárias fossem posicionadas em um determinado local para fornecer latência mais baixa a operações que passam para o primário (gravações e também, por padrão, todas as leituras são atendidas pelo primário).

![Domínios primários preferenciais e failover][Image3]

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUs/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUs")
```

## Exigindo que réplicas sejam distribuídas entre todos os domínios e desativem o empacotamento
Outra política que você pode especificar é para exigir que as réplicas sempre sejam distribuídas entre os domínios disponíveis. Observe que, no exemplo acima, é possível que, temporariamente, várias réplicas (na verdade, um quorum, nossa!) sejam incluídas no mesmo datacenter. Nesse caso, realmente não há problemas, já que o Primário não está no mesmo controlador de domínio que a maioria das réplicas, mas isso mostra que podemos temporariamente estar sujeitos a uma configuração que queremos evitar. Isso dependerá de outras condições no cluster no momento, mas você ainda poderá desejar evitar por completo essa situação garantindo que suas réplicas estão sempre em domínios separados. Lembre-se de que com isso, você estará optando pelo tempo de inatividade/falhas em vez de permitir que o serviço continue sendo executado nos nós restantes. A política RequireDomainDistribution configura esse comportamento, mas observe que isso pode significar que não posicionamos réplicas caso isso resulte em empacotamento. Isso significa que, temporariamente, você está trabalhando com um número abaixo do número de destino de réplicas até que os nós em um domínio viável retornem. Já vimos alguns clientes que prefeririam ter o número de destino de réplicas (cópias de estado) em todos os momentos, enquanto outros não desejarão arriscar as questões de disponibilidade – se empacotarmos as réplicas em um domínio de falha e esse domínio falhar, você perderá todas as réplicas simultaneamente. Para um número pequeno de réplicas, isso poderá resultar na perda de quorum. Na improbabilidade de que o domínio nunca volte, isso poderia resultar em perda de dados. Já que a maioria das pessoas trabalha com mais de três réplicas, o padrão é não exigir a distribuição de domínio e deixar que o balanceamento e o failover manipulem os casos normalmente, mesmo que isso signifique que um domínio tenha temporariamente várias réplicas empacotadas nele.

Código:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Agora, seria possível usar essas configurações para serviços em um cluster que não tenha sido distribuído geograficamente? Claro que sim! Mas não há uma boa razão para fazer isso – especialmente, as configurações de domínio necessário, inválido e preferencial devem ser evitadas, a menos que você esteja realmente executando um cluster distribuído geograficamente.

## Próximas etapas
- Para saber mais sobre outras opções disponíveis para a configuração de serviços, confira o tópico sobre outras configurações disponíveis do Gerenciador de Recursos de Cluster em [Saiba mais sobre a configuração de Serviços](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png

<!---HONumber=AcomDC_0525_2016-->