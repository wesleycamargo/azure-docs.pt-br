<properties
   pageTitle="Gerenciador de Recursos de Cluster do Service Fabric – Políticas de substituição"
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
   ms.date="03/03/2016"
   ms.author="masnider"/>

# Políticas de posicionamento
Há muitas regras adicionais diferentes que podem acabar sendo um motivo de preocupação caso seu cluster do Service Fabric esteja distribuído por uma distância geográfica, digamos, em vários datacenters ou regiões do Azure, ou caso seu ambiente abranja várias áreas de controle geopolítico (ou algum outro caso em que você tenha limites legais ou da política que sejam de seu interesse). A maioria delas pode ser configurada por meio das propriedades de nó e de restrições de posicionamento (que mencionamos anteriormente), mas algumas são mais complicadas. De qualquer modo, fornecemos esses atalhos – assim como as restrições de posicionamento, as políticas de posicionamento podem ser configuradas por serviço

-	InvalidDomain – Permite especificar que determinado Domínio de Falha é inválido para esta carga de trabalho. Boa opção para garantir que determinado serviço nunca seja executado em uma área específica, por exemplo, por motivos geopolíticos ou de política corporativa.

![Exemplo de domínio inválido][Image1]

Código:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DCEast/"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/dc9/”)
```

-	RequiredDomain – Bem simples – exige que todas as réplicas estejam presentes no domínio especificado.

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

-	PreferPrimaryDomain – O domínio Primário Preferencial é um controle interessante, pois permite a seleção do domínio de falha no qual o primário deve existir, caso isso seja possível. Quando tudo estiver íntegro, o primário chegará a esse domínio. Caso o domínio ou a réplica primária falhe ou seja desligado por algum motivo, o Primário será migrado para outro local. Sempre que possível, ele será movido de volta ao domínio preferencial. Obviamente, essa configuração só faz sentido para serviços com estado.

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

-	RequireDomainDistribution – Exigir a distribuição de domínio é outra opção que pode ser usada para prevenir algumas situações. Observe que, no exemplo acima, é possível que, temporariamente, várias réplicas (na verdade, um quorum, nossa!) sejam incluídas no mesmo datacenter. Nesse caso, realmente está tudo bem, já que o Primário não está no mesmo controlador de domínio que a maioria das réplicas, (mas me desviando um pouco do assunto...), isso mostra que podemos temporariamente estar sujeitos a uma configuração que queremos evitar. Isso dependerá de outras condições no cluster no momento, mas você ainda poderá desejar evitar por completo essa situação garantindo que suas réplicas estão sempre em domínios separados. O sinalizador RequireDomainDistribution fornece isso; observe, porém, que isso pode significar que nenhuma réplica seja posicionada caso isso resulte em um empacotamento. Isso significa que, temporariamente, você está trabalhando com um número abaixo do número de destino de réplicas até que os nós em um domínio viável retornem. Já vimos alguns clientes que prefeririam ter o número de destino de réplicas (cópias de estado) em todos os momentos, enquanto outros não desejarão arriscar as questões de disponibilidade – se empacotarmos as réplicas em um domínio de falha e esse domínio falhar, você perderá todas as réplicas simultaneamente. Para um número pequeno de réplicas, isso poderá resultar na perda de quorum. Já que a maioria das pessoas trabalha com mais de três réplicas, o padrão é não exigir a distribuição de domínio e deixar que o balanceamento e o failover manipulem os casos normalmente, mesmo que isso signifique que um domínio tenha temporariamente várias réplicas empacotadas nele.

Código:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

É possível configurar vários domínios Inválidos ou Necessários em um único serviço; no entanto, os outros dois (domínios preferenciais e distribuição de domínio) são apenas configuráveis uma vez por serviço.

Agora, seria possível usar essas configurações para serviços em um cluster que não tenha sido distribuído geograficamente? Claro que sim! Mas não há uma boa razão para fazer isso – especialmente, as configurações de domínio necessário, inválido e preferencial devem ser evitadas, a menos que você esteja realmente executando um cluster distribuído geograficamente.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas
- [Saiba como configurar os Serviços](service-fabric-cluster-resource-manager-configure-services.md)



[Image1]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png

<!---HONumber=AcomDC_0309_2016-->