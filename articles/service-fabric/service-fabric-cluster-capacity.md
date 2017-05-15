---
title: Planejamento da capacidade de cluster do Service Fabric | Microsoft Docs
description: "Considerações de planejamento de capacidade de cluster do Service Fabric. Níveis de confiabilidade, durabilidade e nodetypes"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 4c584f4a-cb1f-400c-b61f-1f797f11c982
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: chackdan
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: e8d6f7c41287f5f785a52ae82bb156008d7e2699
ms.contentlocale: pt-br
ms.lasthandoff: 04/27/2017


---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Considerações de planejamento de capacidade de cluster do Service Fabric
Para qualquer implantação de produção, o planejamento de capacidade é uma etapa importante. Aqui estão alguns dos itens que você precisa considerar como parte desse processo.

* O número de tipos de nós com os quais o cluster precisa começar
* As propriedades de cada tipo de nó (tamanho, primário, voltado para a Internet, número de VMs etc.)
* As características de confiabilidade e durabilidade do cluster

Vamos examinar rapidamente cada um desses itens.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>O número de tipos de nós com os quais o cluster precisa começar
Primeiro, você precisa descobrir para que o cluster que está criando será usado e que tipos de aplicativos você planeja implantar no cluster. Se não tem certeza quanto à finalidade do cluster, você provavelmente ainda não está pronto para iniciar o processo de planejamento de capacidade.

Estabeleça o número de tipos de nós com os quais o cluster precisa começar.  Cada tipo de nó é mapeado para um Conjunto de Escala de Máquina Virtual. Cada tipo de nó pode ser escalado verticalmente para cima ou para baixo de forma independente, tem conjuntos diferentes de portas abertas e pode ter métricas de capacidade diferente. Portanto, a decisão quanto ao número de tipos de nós essencialmente se resume às seguintes considerações:

* O aplicativo tem vários serviços, e algum deles precisa ser público ou voltado para a Internet? Os aplicativos típicos contêm um serviço de gateway de front-end que recebe entrada de um cliente e um ou mais serviços de back-end que se comunicam com os serviços de front-end. Nesse caso, você acaba tendo pelo menos dois tipos de nó.
* Os serviços (que compõem o aplicativo) têm necessidades de infraestrutura diferentes, como maior RAM ou mais ciclos de CPU? Por exemplo, vamos supor que o aplicativo que você deseja implantar contenha um serviço de front-end e um serviço de back-end. O serviço de front-end pode ser executado em VMs menores (com tamanhos de VM como D2) que têm portas abertas para a Internet.  No entanto, o serviço de back-end faz uso intensivo de computação e precisa ser executado em VMs maiores (com tamanhos de VM como D4, D6, D15) que não são voltadas para a Internet.
  
  No exemplo, embora você possa optar por colocar todos os serviços em um tipo de um nó, é recomendável colocá-los em um cluster com dois tipos de nó.  Isso permite que cada tipo de nó tenha propriedades distintas, como conectividade com a Internet ou tamanho de VM. O número de VMs também pode ser dimensionado independentemente.  
* Como você não pode prever o futuro, baseie-se no que já sabe e decida com quantos tipos de nós os aplicativos precisam começar. Você sempre pode adicionar ou remover tipos de nós posteriormente. Um cluster do Service Fabric deve ter pelo menos um tipo de nó.

## <a name="the-properties-of-each-node-type"></a>As propriedades de cada tipo de nó
O **tipo de nó** pode ser visto como algo equivalente a funções nos Serviços de Nuvem. Os tipos de nó definem os tamanhos e o número de VMs e suas propriedades. Cada tipo de nó definido em um cluster do Service Fabric está configurado como um Conjunto de Dimensionamento de Máquinas Virtuais (VMSS) separado. O VMSS é um recurso de computação do Azure que você pode usar para implantar e gerenciar uma coleção de máquinas virtuais como um conjunto. Sendo definidos como VMSS distintos, cada tipo de nó pode ser escalado vertical ou horizontalmente de forma independente, ter diferentes conjuntos de portas abertas e ter métricas de capacidade diferentes.

Leia [este documento](service-fabric-cluster-nodetypes.md) para obter mais detalhes sobre o relacionamento de Nodetypes para VMSS, como RDP em uma das instâncias, abrir novas portas etc.

O cluster pode ter mais de um tipo de nó, mas o tipo de nó primário (o primeiro que você define no portal) deve ter pelo menos cinco VMs para clusters usados para cargas de trabalho de produção (ou pelo menos três VMs para clusters de teste). Se estiver criando o cluster usando um modelo do Resource Manager, você encontrará um atributo **é Primário** na definição do tipo de nó. O tipo de nó primário é o tipo de nó em que os serviços do sistema do Service Fabric são colocados.  

### <a name="primary-node-type"></a>Tipo de nó primário
Para um cluster com vários tipos de nó, você precisará escolher um deles como primário. Aqui estão as características de um tipo de nó primário:

* O **tamanho mínimo de VMs** para o tipo de nó primário é determinado pela **camada de durabilidade** que você escolhe. O padrão para a camada de durabilidade é Bronze. Role para baixo para obter detalhes sobre o que é a camada de durabilidade e os valores que ela pode ter.  
* O **número mínimo de VMs** para o tipo de nó primário é determinado pela **camada de confiabilidade** que você escolhe. O padrão para a camada de confiabilidade é Prata. Role para baixo para obter detalhes sobre o que é a camada de confiabilidade e os valores que ela pode ter. 

 

* Os serviços do sistema do Service Fabric (por exemplo, o serviço Gerenciador de Cluster ou o Serviço de Armazenamento de Imagens) são colocados no tipo de nó primário. Assim, a confiabilidade e a durabilidade do cluster são determinadas pelos valores de camadas de confiabilidade e durabilidade selecionados para o tipo de nó primário.

![Captura de tela de um cluster com dois tipos de nó ][SystemServices]

### <a name="non-primary-node-type"></a>Tipo de nó não primário
Para um cluster com vários tipos de nó, há um tipo de nó primário, e os demais serão não primários. Aqui estão as características de um tipo de nó não primário:

* O tamanho mínimo de VMs para o tipo de nó é determinado pela camada de durabilidade que você escolhe. O padrão para a camada de durabilidade é Bronze. Role para baixo para obter detalhes sobre o que é a camada de durabilidade e os valores que ela pode ter.  
* O número mínimo de VMs para esse tipo de nó pode ser um. No entanto, você deve escolher esse número com base no número de réplicas do aplicativo/serviços que deseja executar nesse tipo de nó. O número de VMs em um tipo de nó pode ser aumentado após a implantação do cluster.

## <a name="the-durability-characteristics-of-the-cluster"></a>As características de durabilidade do cluster
A camada de durabilidade é usada para indicar ao sistema os privilégios que as VMs têm com a infraestrutura subjacente do Azure. No tipo de nó primário, esse privilégio permite que o Service Fabric pause qualquer solicitação de infraestrutura de nível de VM (por exemplo, reinicialização de VM, recriação de imagem de VM ou migração de VM) que afete os requisitos de quorum para os serviços do sistema e os serviços com estado. Nos tipos de nó não primários, esse privilégio permite que o Service Fabric pause qualquer solicitação de infraestrutura de nível de VM (como reinicialização de VM, recriação de imagem de VM, migração de VM etc.) que afete os requisitos de quorum para os serviços com estado em execução.

Esse privilégio é expresso nos seguintes valores:

* Ouro - os Trabalhos de infraestrutura podem ser pausados por duas horas por UD. A durabilidade ouro pode ser habilitada apenas em skus de VM de nó completo como D15_V2, G5 etc.
* Prata – os Trabalhos de Infraestrutura podem ser pausados por uma duração de 30 minutos por UD (Isso não está habilitado para uso atualmente. Uma vez habilitado, isso estará disponível em todas as VMs padrão de núcleo único e acima).
* Bronze - sem privilégios. Esse é o padrão.

## <a name="the-reliability-characteristics-of-the-cluster"></a>As características de confiabilidade do cluster
A camada de confiabilidade é usada para definir o número de réplicas dos serviços do sistema que você deseja executar nesse cluster no tipo de nó primário. Quanto maior for o número de réplicas, mais confiáveis os serviços do sistema serão no cluster.  

A camada de confiabilidade pode ter os valores a seguir.

* Platina - executar os serviços do sistema com uma contagem de conjuntos de réplicas de destino de 9
* Ouro - executar os serviços do sistema com uma contagem de conjuntos de réplicas de destino de 7
* Prata - executar os serviços do sistema com uma contagem de conjuntos de réplicas de destino de 5
* Bronze - executar os serviços do sistema com uma contagem de conjuntos de réplicas de destino de 3

> [!NOTE]
> A camada de confiabilidade que você escolhe determina o número mínimo de nós que o tipo de nó primário deve ter. A camada de confiabilidade não influencia o tamanho máximo do cluster. Portanto, você pode ter um cluster com 20 nós, em execução com a confiabilidade Bronze.
> 
> 

 Você pode optar por atualizar a confiabilidade do cluster de uma camada para outra. Isso disparará as atualizações de cluster necessárias para alterar a contagem de conjuntos de réplicas dos serviços do sistema. Aguarde a conclusão da atualização em andamento antes de fazer outras alterações no cluster, como adicionar nós etc.  Você pode monitorar o andamento da atualização no Service Fabric Explorer ou executando [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)


## <a name="primary-node-type---capacity-guidance"></a>Tipo de nó Primário - Diretrizes de Capacidade

Eis aqui as diretrizes de planejamento da capacidade do tipo de nó principal

1. **Número de instâncias de VM:** para executar qualquer carga de trabalho de produção no Azure, você deve especificar uma camada de confiabilidade Silver ou superior, que os converte para um tamanho mínimo de tipo de Nó Primário de 5.
2. **SKU da VM:** o tipo de nó Primário é onde os serviços do sistema são executados e, portanto, a SKU de VM escolhida deve levar em consideração o pico de carga geral que você planeja colocar no cluster. Aqui está uma analogia para ilustrar o que quero dizer – pense no tipo de nó primário como seus "pulmões", ele fornece oxigênio para seu cérebro e, dessa forma, se o cérebro não obtiver oxigênio suficiente, seu corpo será prejudicado. 

As necessidades de capacidade de um cluster são absolutamente determinadas pela carga de trabalho que você planeja executar no cluster e, portanto, não podemos fornecer a você uma diretriz qualitativa para sua carga de trabalho específica, mas veja estas diretrizes amplas para ajudar você a começar

Para cargas de trabalho de produção 


- A SKU de VM recomendada é a Standard D3 ou a Standard D3_V2 ou equivalente, com um mínimo de 14 GB de SSD local.
- A SKU de VM de uso mínimo com suporte é a Standard D1 ou a Standard D1_V2 ou equivalente, com um mínimo de 14 GB de SSD local. 
- As SKUs de VM de núcleo parcial como a Standard A0 não têm suporte para cargas de trabalho de produção.
- Especificamente, a SKU Standard A1 não tem suporte para cargas de trabalho de produção por motivos de desempenho.


## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>O tipo de nó Não Principal - Diretrizes de Capacidade para cargas de trabalho com monitoração de estado

Leia o seguinte para Cargas de Trabalho usando coleções confiáveis do Service Fabric ou Reliable Actors. Leia mais sobre [modelos de programação aqui.](service-fabric-choose-framework.md)

1. **Número de instâncias de VM:** para as cargas de trabalho de produção com monitoração de estado, é recomendável que você as execute com uma contagem de réplica mínima e de destino de 5. Isso significa que no estado estável, você acabará com uma réplica (de um conjunto de réplicas) em cada domínio de falha e em cada domínio de atualização. O conceito de camada de confiabilidade inteira para serviços do sistema é, na verdade, apenas uma maneira de especificar essa configuração para serviços do sistema.

Assim, para cargas de trabalho de produção, o tamanho mínimo recomendado do tamanho do tipo de Nó não Principal é 5, se você estiver executando cargas de trabalho com monitoração de estado nele.

2. **SKU de VM:** esse é o tipo de nó em que seus serviços de aplicativo estão sendo executados e, portanto, a SKU de VM escolhida deverá levar em conta a carga máxima que você deseja colocar em cada nó. As necessidades de capacidade do tipo de nó são absolutamente determinadas pela carga de trabalho que você planeja executar no cluster e, portanto, não podemos fornecer a você uma diretriz qualitativa para sua carga de trabalho específica, mas veja estas diretrizes amplas para ajudar você a começar

Para cargas de trabalho de produção 

- A SKU de VM recomendada é a Standard D3 ou a Standard D3_V2 ou equivalente, com um mínimo de 14 GB de SSD local.
- A SKU de VM de uso mínimo com suporte é a Standard D1 ou a Standard D1_V2 ou equivalente, com um mínimo de 14 GB de SSD local. 
- As SKUs de VM de núcleo parcial como a Standard A0 não têm suporte para cargas de trabalho de produção.
- Especificamente, a SKU Standard A1 não tem suporte para cargas de trabalho de produção por motivos de desempenho.


## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>O tipo de nó Não Principal - Diretrizes de Capacidade para cargas de trabalho sem monitoração de estado

Leia o seguinte para Cargas de Trabalho sem monitoração de estado

**Número de instâncias de VM:** para cargas de trabalho de produção sem estado, o tamanho mínimo do tipo de Nó não Principal com suporte é 2. Isso permite a execução de duas instâncias sem monitoração de estado do seu aplicativo e permite que seu serviço sobreviva à perda de uma instância de VM. 

> [!NOTE]
> Se o cluster estiver em execução em uma versão do Service Fabric inferior a 5.6, devido a um defeito no tempo de execução (que deverá ser corrigido na versão 5.6), o dimensionamento de um tipo de nó não primário para menos de 5 fará com que o cluster se torne não íntegro, até você chamar [Remove-ServiceFabricNodeState cmd](https://docs.microsoft.com/powershell/servicefabric/vlatest/Remove-ServiceFabricNodeState) com o nome de nó apropriado. Leia [Sair ou entrar no cluster do Service Fabric](service-fabric-cluster-scale-up-down.md) para obter mais detalhes
> 
>

**SKU de VM:** esse é o tipo de nó em que seus serviços de aplicativo estão sendo executados e, portanto, a SKU de VM escolhida deverá levar em conta a carga máxima que você deseja colocar em cada nó. As necessidades de capacidade do tipo de nó são absolutamente determinadas pela carga de trabalho que você planeja executar no cluster e, portanto, não podemos fornecer a você uma diretriz qualitativa para sua carga de trabalho específica, mas veja estas diretrizes amplas para ajudar você a começar

Para cargas de trabalho de produção 


- A SKU de VM recomendada é a Standard D3 ou a Standard D3_V2 ou equivalente. 
- O uso mínimo com suporte da SKU de VM é a Standard D1 ou a Standard D1_V2 ou equivalente. 
- As SKUs de VM de núcleo parcial como a Standard A0 não têm suporte para cargas de trabalho de produção.
- Especificamente, a SKU Standard A1 não tem suporte para cargas de trabalho de produção por motivos de desempenho.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Próximas etapas
Após concluir o planejamento de capacidade e configurar um cluster, leia o seguinte:

* [Segurança do Cluster do Service Fabric](service-fabric-cluster-security.md)
* [Introdução ao modelo de Integridade do Service Fabric](service-fabric-health-introduction.md)
* [Relacionamento de Nodetypes para VMSS](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png

