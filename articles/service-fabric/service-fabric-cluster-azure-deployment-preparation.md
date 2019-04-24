---
title: Planejar uma implantação de cluster do Service Fabric do Azure | Microsoft Docs
description: Saiba mais sobre como planejar e preparar para uma implantação de cluster do Service Fabric no Azure de produção.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: aljo
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2019
ms.author: aljo
ms.openlocfilehash: 0f3a9010805ec1a18490f6f530f60d7a3c763398
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60387852"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>Planejar e preparar para uma implantação de cluster

Planejando e preparando-se para uma implantação de cluster de produção são muito importante.  Há muitos fatores a considerar.  Este artigo orienta você pelas etapas de preparação da sua implantação do cluster.

## <a name="read-the-best-practices-information"></a>Leia as informações de práticas recomendadas
Para gerenciar clusters e aplicativos do Service Fabric com êxito, há operações que é altamente recomendável que executar para otimizar a confiabilidade do seu ambiente de produção.  Para obter mais informações, leia [práticas recomendadas de aplicativo do Service Fabric e o cluster](service-fabric-best-practices-overview.md).

## <a name="select-the-os-for-the-cluster"></a>Selecione o sistema operacional para o cluster
O Service Fabric permite a criação de clusters do Service Fabric em qualquer VM ou computador que esteja executando o Windows Server ou o Linux.  Antes de implantar o cluster, você deve escolher o sistema operacional:  Windows ou Linux.  Cada nó do cluster (máquina virtual) é executado o mesmo sistema operacional, você não pode misturar VMs do Linux e Windows no mesmo cluster.

## <a name="capacity-planning"></a>planejamento de capacidade
Para qualquer implantação de produção, o planejamento de capacidade é uma etapa importante. Aqui estão alguns pontos a serem considerados como parte desse processo.

* O número inicial de tipos de nó do cluster 
* As propriedades de cada tipo de nó (tamanho, número de instâncias, primários, de internet, número de VMs, etc.)
* As características de confiabilidade e durabilidade do cluster

### <a name="select-the-initial-number-of-node-types"></a>Selecione o número inicial de tipos de nó
Primeiro, é necessário descobrir para que o cluster que você está criando será utilizado. Que tipos de aplicativos você planeja implantar nesse cluster? O aplicativo tem vários serviços, e algum deles precisa ser público ou voltado para a Internet? Os serviços (que compõem o aplicativo) têm necessidades de infraestrutura diferentes, como maior RAM ou mais ciclos de CPU? Um cluster do Service Fabric pode consistir em mais de um tipo de nó: um tipo de nó primário e um ou mais tipos de nó não primário. Cada tipo de nó é mapeado para um conjunto de dimensionamento de máquinas virtuais. Cada tipo de nó pode ser escalado verticalmente para cima ou para baixo de forma independente, tem conjuntos diferentes de portas abertas e pode ter métricas de capacidade diferente. [Propriedades de nó e restrições de posicionamento] [ placementconstraints] podem ser configurados para restringir serviços específicos para os tipos de nó específico.  Para obter mais informações, leia [o número de tipos de nós de cluster precisa começar com](service-fabric-cluster-capacity.md#the-number-of-node-types-your-cluster-needs-to-start-out-with).

### <a name="select-node-properties-for-each-node-type"></a>Selecionar as propriedades de nó para cada tipo de nó
Tipos de nó definem o SKU de VM, número e as propriedades das VMs no conjunto de escala associado.

O tamanho mínimo de VMs para cada tipo de nó é determinado pelo [camada de durabilidade] [ durability] escolhida para o tipo de nó.

O número mínimo de VMs para o tipo de nó primário é determinado pelo [camada de confiabilidade] [ reliability] escolhida.

Consulte as recomendações mínimas para [tipos de nó primário](service-fabric-cluster-capacity.md#primary-node-type---capacity-guidance), [cargas de trabalho com monitoração de estado em tipos de nó não primário](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateful-workloads), e [cargas de trabalho sem monitoração de estado em tipos de nó não primário](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateless-workloads). 

Mais que o número mínimo de nós devem ser baseada no número de réplicas de aplicativos/serviços que você deseja executar nesse tipo de nó.  [Planejamento de capacidade para aplicativos do Service Fabric](service-fabric-capacity-planning.md) ajuda a estimar os recursos necessários para executar seus aplicativos. Você sempre pode aumentar o cluster ou para baixo posteriormente para ajustar para alterar a carga de trabalho do aplicativo. 

### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>Selecione os níveis de durabilidade e confiabilidade do cluster
A camada de durabilidade é usada para indicar ao sistema os privilégios que as VMs têm com a infraestrutura subjacente do Azure. No tipo de nó primário, esse privilégio permite que o Service Fabric pause qualquer solicitação de infraestrutura de nível de VM (por exemplo, reinicialização de VM, recriação de imagem de VM ou migração de VM) que afete os requisitos de quorum para os serviços do sistema e os serviços com estado. Nos tipos de nó não primários, esse privilégio permite que o Service Fabric pause quaisquer solicitações de infraestrutura de nível de VM (como reinicialização de VM, reinicialização de VM e migração de VM) que afetem os requisitos de quorum para os serviços com estado.  Para as vantagens dos diferentes níveis e recomendações sobre qual nível de usar e quando, consulte [as características de durabilidade do cluster][durability].

A camada de confiabilidade é usada para definir o número de réplicas dos serviços do sistema que você deseja executar nesse cluster no tipo de nó primário. Quanto maior for o número de réplicas, mais confiáveis os serviços do sistema serão no cluster.  Para as vantagens dos diferentes níveis e recomendações sobre qual nível de usar e quando, consulte [as características de confiabilidade do cluster][reliability]. 

## <a name="enable-reverse-proxy-andor-dns"></a>Habilitar proxy reverso e/ou DNS
Geralmente, os serviços que se conectam entre si em um cluster podem acessar diretamente os pontos de extremidade de outros serviços, pois os nós de um cluster estão na mesma rede local. Para facilitar a conexão entre serviços, o Service Fabric oferece serviços adicionais: Um [serviço DNS](service-fabric-dnsservice.md) e uma [serviço de proxy reverso](service-fabric-reverseproxy.md).  Ambos os serviços podem ser ativados ao implantar um cluster.

Como muitos serviços, especialmente serviços em contêineres, pode ter um nome de URL existente, sendo capaz de resolvê-los usando o DNS padrão protocolo (em vez do protocolo de serviço de nomeação) é conveniente, especialmente em cenários de "lift and shift" do aplicativo. Isso é exatamente o que o serviço DNS faz. Ele permite mapear nomes DNS para um nome de serviço e, portanto, resolver endereços IP do ponto de extremidade.

O proxy reverso lida com os serviços no cluster que expõem pontos de extremidade HTTP (inclusive HTTPS). O proxy reverso simplifica bastante a chamada de outros serviços, fornecendo um formato URI específico.  O proxy reverso também lida com a resolução, conecte-se e repita as etapas necessárias para um serviço se comunique com outro.

## <a name="prepare-for-disaster-recovery"></a>Preparar para a recuperação de desastre
Uma parte crítica da entrega de alta disponibilidade é garantir que os serviços possam sobreviver a todos os tipos diferentes de falhas. Isso é especialmente importante para falhas não planejadas e fora de seu controle. [Preparar para recuperação de desastres](service-fabric-disaster-recovery.md) descreve alguns modos de falha comuns que podem ser desastres se não modelados e gerenciados corretamente. Ele também aborda mitigações e ações a serem tomadas se um desastre acontecer mesmo assim.

## <a name="production-readiness-checklist"></a>Lista de verificação de preparação para produção
O aplicativo e o cluster estão prontos para receber tráfego de produção? Antes de implantar o cluster de produção, executar por meio de [lista de verificação de preparação para produção](service-fabric-production-readiness-checklist.md). Mantenha seu aplicativo e do cluster funcionando sem problemas, apresentando os itens desta lista de verificação. É altamente recomendável que todos esses itens a ser verificado antes de entrar em produção.

## <a name="next-steps"></a>Próximas etapas
* [Criar um cluster do Service Fabric executando o Windows](service-fabric-best-practices-overview.md)
* [Criar um cluster do Service Fabric executando o Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster