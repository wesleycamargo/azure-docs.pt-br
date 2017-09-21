---
title: "Governança de recursos do Azure Service Fabric para contêineres e serviços | Microsoft Docs"
description: "O Azure Service Fabric permite especificar os limites de recurso para os serviços executados dentro ou fora de contêineres."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 62ce17533f8c195b873431089e1f1f47cb0bcbec
ms.contentlocale: pt-br
ms.lasthandoff: 09/14/2017

---

# <a name="resource-governance"></a>Governança de recursos 

Ao executar vários serviços no mesmo nó ou cluster, é possível que um serviço consuma mais recursos, privando outros serviços. Esse problema é conhecido como o problema do vizinho barulhento. O Service Fabric permite que o desenvolvedor especifique reservas e limites por serviço para garantir recursos e também limitar seu uso de recursos.

>
> Antes de continuar lendo este artigo, familiarize-se com o [Modelo de Aplicativo do Service Fabric](service-fabric-application-model.md) e o [Modelo de Hospedagem do Service Fabric](service-fabric-hosting-model.md).
>

## <a name="resource-governance-metrics"></a>Métricas de governança de recursos 

Há suporte para a governança de recursos no Service Fabric por [Pacote de Serviço](service-fabric-application-model.md). Os recursos que são atribuídos ao Pacote de Serviço podem ser subdivididos entre pacotes de códigos. Os limites de recurso especificados também indicam a reserva dos recursos. O Service Fabric dá suporte à especificação de CPU e Memória por pacote de serviço, usando duas [métricas](service-fabric-cluster-resource-manager-metrics.md) internas:

* CPU (nome da métrica `servicefabric:/_CpuCores`): um núcleo é um núcleo lógico que está disponível no computador host e todos os núcleos em todos os nós são ponderados da mesma forma.
* Memória (nome da métrica `servicefabric:/_MemoryInMB`): a memória é expressa em megabytes e é mapeada para a memória física disponível no computador.

Para essas duas métricas, o [Gerenciador de Recursos do Cluster](service-fabric-cluster-resource-manager-cluster-description.md) controla a capacidade total do cluster, a carga em cada nó do cluster e os recursos restantes do cluster. Essas duas métricas são equivalentes a qualquer outro usuário ou outra métrica personalizada e todos os recursos existentes podem ser usados com elas:
* O cluster pode ser [equilibrado](service-fabric-cluster-resource-manager-balancing.md) de acordo com essas duas métricas (comportamento padrão).
* O cluster pode ser [desfragmentado](service-fabric-cluster-resource-manager-defragmentation-metrics.md) de acordo com essas duas métricas.
* Ao [descrever um cluster](service-fabric-cluster-resource-manager-cluster-description.md), a capacidade armazenada em buffer pode ser definida para essas duas métricas.

Não há suporte para [relatórios de cargas dinâmicas](service-fabric-cluster-resource-manager-metrics.md) nessas métricas e as cargas dessas métricas são definidas no momento da criação.

## <a name="resource-governance-mechanism"></a>Mecanismo de governança de recursos

Atualmente, o tempo de execução do Service Fabric não fornece uma reserva de recursos. Quando um processo ou um contêiner é aberto, o tempo de execução definirá os limites de recursos para as cargas que foram definidas no momento da criação. Além disso, o tempo de execução rejeitará a abertura de novos pacotes de serviço disponíveis se os recursos forem excedidos. Para entender melhor como o processo funciona, vamos usar um exemplo de um nó com 2 núcleos de CPU (o mecanismo de governança de memória é equivalente):

1. Primeiro, um contêiner é colocado no nó, solicitando 1 núcleo de CPU. O tempo de execução abrirá o contêiner e definirá o limite da CPU como 1 núcleo. O contêiner não poderá usar mais de 1 núcleo.
2. Em seguida, uma réplica do serviço é colocada no nó e o pacote de serviço correspondente especifica um limite de 1 núcleo de CPU. O tempo de execução abrirá o pacote de códigos e definirá seu limite de CPU como 1 núcleo.

Neste ponto, a soma dos limites é igual à capacidade do nó e um processo e um contêiner estão em execução com um 1 núcleo cada um e não interferem um com o outro. O Service Fabric não colocará mais contêineres ou réplicas, no caso de quando eles estiverem especificando o limite de CPU. No entanto, há duas situações em que outros processos podem competir pela CPU e um processo e um contêiner de nosso exemplo podem ter o problema do vizinho barulhento:

* Combinação de serviços e contêineres controlados e não controlados: se o usuário criar um serviço sem nenhuma governança de recursos especificada, o tempo de execução considerará como se ele não estivesse consumindo nenhum recurso e poderá colocá-lo no nó de nosso exemplo. Nesse caso, esse novo processo efetivamente consumirá alguns recursos da CPU, à custa dos serviços que já estão sendo executados no nó. A solução para esse problema é não combinar serviços controlados e não controlados no mesmo cluster ou usar [restrições de posicionamento](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) para que eles não acabem no mesmo conjunto de nós.
* No caso de quando outro processo for iniciado no nó, fora do Service Fabric (por exemplo, algum serviço do sistema operacional), esse processo também competirá pela CPU com os serviços existentes. A solução para esse problema é configurar as capacidades de nó corretamente para a conta para a sobrecarga do sistema operacional, conforme mostrado na próxima seção.

## <a name="cluster-set-up-for-enabling-resource-governance"></a>Configuração de cluster para habilitar a governança de recursos

Quando o nó for iniciado e ingressado no cluster, o Service Fabric detectará a quantidade de memória e o número de núcleos disponíveis e definirá as capacidades de nó para esses dois recursos. Par não deixar algum espaço do buffer para o Sistema Operacional e para outros processos que podem estar em execução no nó, o Service Fabric usará somente 80% dos recursos disponíveis no nó. Esse percentual é configurável e pode ser alterado no manifesto do cluster. Este é um exemplo de como instruir o Service Fabric a usar 50% da CPU disponível e 70% da memória disponível: 

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

Caso seja necessária uma instalação manual completa das capacidades de nó, isso também é possível usando o mecanismo regular para descrever os nós no cluster. Este é um exemplo de configuração do nó com 4 núcleos e 2 GB de memória: 

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

Quando a detecção automática de recursos disponíveis for habilitada e as capacidades de nó forem definidas manualmente no manifesto do cluster, o Service Fabric verificará se o nó tem recursos suficientes para dar suporte à capacidade definida pelo usuário:
* Se as capacidades de nó definidas no manifesto forem menores ou iguais aos recursos disponíveis no nó, o Service Fabric usará as capacidades especificadas no manifesto.
* Se as capacidades de nó definidas no manifesto forem maiores que os recursos disponíveis, o Service Fabric usará os recursos disponíveis como as capacidades de nó.

A detecção automática de recursos disponíveis pode ser totalmente desativada, caso não seja necessária, alterando a seguinte configuração:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

Para obter o desempenho ideal, a seguinte configuração também deve ser ativada no manifesto do cluster: 

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

## <a name="other-resources-for-containers"></a>Outros recursos para contêineres
Além da CPU e da memória, é possível especificar outros limites de recursos para contêineres. Esses limites são especificados no nível do pacote de códigos e serão aplicados quando o contêiner for iniciado. Ao contrário da CPU e da memória, o Gerenciador de Recursos de Cluster não reconhecerá esses recursos e não fará verificações de capacidade nem balanceamento de carga para eles. 

* MemorySwapInMB – a quantidade de memória de troca que pode ser usada por um contêiner.
* MemoryReservationInMB – limite flexível para governança de memória que é imposto apenas quando a contenção de memória é detectada no nó.
* CpuPercent – percentual de CPU que pode ser usado pelo contêiner. Se os limites de CPU forem especificados para o pacote de serviço, esse parâmetro será efetivamente ignorado.
* MaximumIOps – IOPS máximo que pode ser usado por um contêiner (leitura e gravação).
* MaximumIOBytesps – E/S máximo (bytes por segundo) que pode ser usado por um contêiner (leitura e gravação).
* BlockIOWeight – peso de E/S do bloqueio em relação a outros contêineres.

Esses recursos podem ser combinados com a CPU e a memória. Este é um exemplo de como especificar recursos adicionais para contêineres:

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuPercent="5"
            MemorySwapInMB="4084" MemoryReservationInMB="1024" MaximumIOPS="20" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre o Gerenciador de Recursos do Cluster, leia este [artigo](service-fabric-cluster-resource-manager-introduction.md).
* Para saber mais sobre o modelo de aplicativo, pacotes de serviço, pacotes de códigos e como as réplicas são mapeadas para eles, leia este [artigo](service-fabric-application-model.md).

