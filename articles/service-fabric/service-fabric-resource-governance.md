---
title: "Governança de recursos do Azure Service Fabric para contêineres e serviços | Microsoft Docs"
description: "O Azure Service Fabric permite especificar os limites de recurso para os serviços executados dentro ou fora de contêineres."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/28/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: e490dfba28b8a270bf0e6022333f148c965bc6dc
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---

# <a name="resource-governance"></a>Governança de recursos 

Ao executar vários serviços no mesmo nó ou cluster, é possível que um serviço consuma mais recursos, privando outros serviços. Esse problema é conhecido como o problema do vizinho barulhento. O Service Fabric permite que o desenvolvedor especifique reservas e limites por serviço para garantir recursos e também limitar seu uso de recursos. 

## <a name="resource-governance-metrics"></a>Métricas de governança de recursos 

Há suporte para a governança de recursos no Service Fabric por [Pacote de Serviço](service-fabric-application-model.md). Os recursos que são atribuídos ao Pacote de Serviço podem ser subdivididos entre pacotes de códigos. Os limites de recurso especificados também indicam a reserva dos recursos. O Service Fabric dá suporte à especificação de CPU e Memória por pacote de serviço, usando duas [métricas](service-fabric-cluster-resource-manager-metrics.md) internas:

* CPU (nome da métrica `ServiceFabric:/_CpuCores`): um núcleo é um núcleo lógico que está disponível no computador host e todos os núcleos em todos os nós são ponderados da mesma forma.
* Memória (nome da métrica `ServiceFabric:/_MemoryInMB`): a memória é expressa em megabytes e é mapeada para a memória física disponível no computador.

São fornecidas apenas garantias de reserva flexível – o tempo de execução rejeita a abertura de novos pacotes de serviço quando os recursos disponíveis são excedidos. No entanto, se outro executável ou contêiner for colocado no nó, isso poderá violar as garantias de reserva originais.

Para essas duas métricas, o [Gerenciador de Recursos do Cluster](service-fabric-cluster-resource-manager-cluster-description.md) controla a capacidade total do cluster, a carga em cada nó do cluster e os recursos restantes do cluster. Essas duas métricas são equivalentes a qualquer outro usuário ou outra métrica personalizada e todos os recursos existentes podem ser usados com elas:
* O cluster pode ser [equilibrado](service-fabric-cluster-resource-manager-balancing.md) de acordo com essas duas métricas (comportamento padrão).
* O cluster pode ser [desfragmentado](service-fabric-cluster-resource-manager-defragmentation-metrics.md) de acordo com essas duas métricas.
* Ao [descrever um cluster](service-fabric-cluster-resource-manager-cluster-description.md), a capacidade armazenada em buffer pode ser definida para essas duas métricas.

Não há suporte para [relatórios de cargas dinâmicas](service-fabric-cluster-resource-manager-metrics.md) nessas métricas e as cargas dessas métricas são definidas no momento da criação.

## <a name="cluster-set-up-for-enabling-resource-governance"></a>Configuração de cluster para habilitar a governança de recursos

A capacidade deve ser definida manualmente em cada tipo de nó do cluster da seguinte maneira:

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="ServiceFabric:/_CpuCores" Value="4"/>
        <Capacity Name="ServiceFabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```
 
A governança de recursos é permitida somente em serviços do usuário e não em qualquer serviço do sistema. Ao especificar a capacidade, alguns núcleos e uma parte da memória devem ser deixados não alocados para serviços do sistema. Para obter o desempenho ideal, a seguinte configuração também deve ser ativada no manifesto do cluster: 

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" /> 
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```


## <a name="specifying-resource-governance"></a>Especificando a governança de recursos 

Os limites da governança de recursos são especificados no manifesto do aplicativo (seção ServiceManifestImport), conforme mostrado no seguinte exemplo:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'>
  <Parameters>
  </Parameters>
  <!--
  ServicePackageA has the number of CPU cores defined, but doesn't have the MemoryInMB defined.
  In this case, Service Fabric will sum the limits on code packages and uses the sum as 
  the overall ServicePackage limit.
  -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1000" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1000" />
    </Policies>
  </ServiceManifestImport>
```
  
Neste exemplo, o pacote de serviço ServicePackageA obtém um núcleo nos nós em que ele é colocado. Esse pacote de serviço contém dois pacotes de códigos (CodeA1 e CodeA2) e ambos especificam o parâmetro `CpuShares`. A proporção de CpuShares 512:256 divide o núcleo entre os dois pacotes de códigos. Portanto, neste exemplo, CodeA1 obtém dois terços de um núcleo e CodeA2 obtém um terço de um núcleo (e uma reserva de garantia reversível do mesmo). Nos casos em que CpuShares não forem especificadas para pacotes de códigos, o Service Fabric dividirá os núcleos igualmente entre eles.

Os limites de memória são absolutos e, portanto, os dois pacotes de códigos são limitados a 1.024 MB de memória (e a uma reserva de garantia reversível da mesma). Os pacotes de códigos (contêineres ou processos) não podem alocar mais memória do que esse limite. A tentativa de fazer isso resultará em uma exceção de memória insuficiente. Para que a imposição do limite de recursos funcione, todos os pacotes de códigos em um pacote de serviço devem ter limites de memória especificados.


## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre o Gerenciador de Recursos do Cluster, leia este [artigo](service-fabric-cluster-resource-manager-introduction.md).
* Para saber mais sobre o modelo de aplicativo, pacotes de serviço, pacotes de códigos e como as réplicas são mapeadas para eles, leia este [artigo](service-fabric-application-model.md).

