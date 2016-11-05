---
title: Planejamento da capacidade de cluster do Service Fabric | Microsoft Docs
description: Considerações de planejamento de capacidade de cluster do Service Fabric. Níveis de confiabilidade, durabilidade e nodetypes
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2016
ms.author: chackdan

---
# Considerações de planejamento de capacidade de cluster do Service Fabric
Para qualquer implantação de produção, o planejamento de capacidade é uma etapa importante. Aqui estão alguns dos itens que você precisa considerar como parte desse processo.

* O número de tipos de nós com os quais o cluster precisa começar
* As propriedades de cada tipo de nó (tamanho, primário, voltado para a Internet, número de VMs etc.)
* As características de confiabilidade e durabilidade do cluster

Vamos examinar rapidamente cada um desses itens.

## O número de tipos de nós com os quais o cluster precisa começar
Primeiro, você precisa descobrir para que o cluster que está criando será usado e que tipos de aplicativos você planeja implantar no cluster. Se não tem certeza quanto à finalidade do cluster, você provavelmente ainda não está pronto para iniciar o processo de planejamento de capacidade.

Estabeleça o número de tipos de nós com os quais o cluster precisa começar. Cada tipo de nó é mapeado para um Conjunto de Escala de Máquina Virtual. Cada tipo de nó pode ser escalado verticalmente para cima ou para baixo de forma independente, tem conjuntos diferentes de portas abertas e pode ter métricas de capacidade diferente. Portanto, a decisão quanto ao número de tipos de nós essencialmente se resume às seguintes considerações:

* O aplicativo tem vários serviços, e algum deles precisa ser público ou voltado para a Internet? Os aplicativos típicos contêm um serviço de gateway de front-end que recebe entrada de um cliente e um ou mais serviços de back-end que se comunicam com os serviços de front-end. Nesse caso, você acaba tendo pelo menos dois tipos de nó.
* Os serviços (que compõem o aplicativo) têm necessidades de infraestrutura diferentes, como maior RAM ou mais ciclos de CPU? Por exemplo, vamos supor que o aplicativo que você deseja implantar contenha um serviço de front-end e um serviço de back-end. O serviço de front-end pode ser executado em VMs menores (com tamanhos de VM como D2) que têm portas abertas para a Internet. No entanto, o serviço de back-end faz uso intensivo de computação e precisa ser executado em VMs maiores (com tamanhos de VM como D4, D6, D15) que não são voltadas para a Internet.
  
  No exemplo, embora você possa optar por colocar todos os serviços em um tipo de um nó, é recomendável colocá-los em um cluster com dois tipos de nó. Isso permite que cada tipo de nó tenha propriedades distintas, como conectividade com a Internet ou tamanho de VM. O número de VMs também pode ser dimensionado independentemente.
* Como você não pode prever o futuro, baseie-se no que já sabe e decida com quantos tipos de nós os aplicativos precisam começar. Você sempre pode adicionar ou remover tipos de nós posteriormente. Um cluster do Service Fabric deve ter pelo menos um tipo de nó.

## As propriedades de cada tipo de nó
O **tipo de nó** pode ser visto como algo equivalente a funções nos Serviços de Nuvem. Os tipos de nó definem os tamanhos e o número de VMs e suas propriedades. Cada tipo de nó definido em um cluster do Service Fabric está configurado como um Conjunto de Escala de Máquina Virtual separado. Os Conjuntos de Escala de VM são um recurso de computação do Azure que você pode usar para implantar e gerenciar uma coleção de máquinas virtuais como um conjunto. Sendo definidos como Conjuntos de Escala de VM distintos, cada tipo de nó pode ser escalado verticalmente ou horizontalmente de forma independente, ter diferentes conjuntos de portas abertas e ter métricas de capacidade diferentes.

O cluster pode ter mais de um tipo de nó, mas o tipo de nó primário (o primeiro que você define no portal) deve ter pelo menos cinco VMs para clusters usados para cargas de trabalho de produção (ou pelo menos três VMs para clusters de teste). Se estiver criando o cluster usando um modelo do Resource Manager, você encontrará um atributo **É Primário** na definição do tipo de nó. O tipo de nó primário é o tipo de nó em que os serviços do sistema do Service Fabric são colocados.

### Tipo de nó primário
Para um cluster com vários tipos de nó, você precisará escolher um deles como primário. Aqui estão as características de um tipo de nó primário:

* O tamanho mínimo de VMs para o tipo de nó primário é determinado pela camada de durabilidade que você escolhe. O padrão para a camada de durabilidade é Bronze. Role para baixo para obter detalhes sobre o que é a camada de durabilidade e os valores que ela pode ter.
* O número mínimo de VMs para o tipo de nó primário é determinado pela camada de confiabilidade que você escolhe. O padrão para a camada de confiabilidade é Prata. Role para baixo para obter detalhes sobre o que é a camada de confiabilidade e os valores que ela pode ter.
* Os serviços do sistema do Service Fabric (por exemplo, o serviço Gerenciador de Cluster ou o Serviço de Armazenamento de Imagens) são colocados no tipo de nó primário. Assim, a confiabilidade e a durabilidade do cluster são determinadas pelos valores de camadas de confiabilidade e durabilidade selecionados para o tipo de nó primário.

![Captura de tela de um cluster com dois tipos de nó][SystemServices]

### Tipo de nó não primário
Para um cluster com vários tipos de nó, há um tipo de nó primário, e os demais serão não primários. Aqui estão as características de um tipo de nó não primário:

* O tamanho mínimo de VMs para o tipo de nó é determinado pela camada de durabilidade que você escolhe. O padrão para a camada de durabilidade é Bronze. Role para baixo para obter detalhes sobre o que é a camada de durabilidade e os valores que ela pode ter.
* O número mínimo de VMs para esse tipo de nó pode ser um. No entanto, você deve escolher esse número com base no número de réplicas do aplicativo/serviços que deseja executar nesse tipo de nó. O número de VMs em um tipo de nó pode ser aumentado após a implantação do cluster.

## As características de durabilidade do cluster
A camada de durabilidade é usada para indicar ao sistema os privilégios que as VMs têm com a infraestrutura subjacente do Azure. No tipo de nó primário, esse privilégio permite que o Service Fabric pause qualquer solicitação de infraestrutura de nível de VM (por exemplo, reinicialização de VM, recriação de imagem de VM ou migração de VM) que afete os requisitos de quorum para os serviços do sistema e os serviços com estado. Nos tipos de nó não primários, esse privilégio permite que o Service Fabric pause qualquer solicitação de infraestrutura de nível de VM (como reinicialização de VM, recriação de imagem de VM, migração de VM etc.) que afete os requisitos de quorum para os serviços com estado em execução.

Esse privilégio é expresso nos seguintes valores:

* Ouro - os Trabalhos de Infraestrutura podem ser pausados por uma duração de duas horas por UD
* Prata – os Trabalhos de Infraestrutura podem ser pausados por uma duração de 30 minutos por UD (Isso não está habilitado para uso atualmente. Uma vez habilitado, isso estará disponível em todas as VMs padrão de núcleo único e acima).
* Bronze - sem privilégios. Esse é o padrão.

## As características de confiabilidade do cluster
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

 Você pode optar por atualizar a confiabilidade do cluster de uma camada para outra. Isso disparará as atualizações de cluster necessárias para alterar a contagem de conjuntos de réplicas dos serviços do sistema. Aguarde a conclusão da atualização em andamento antes de fazer outras alterações no cluster, como adicionar nós etc. Você pode monitorar o andamento da atualização no Service Fabric Explorer ou executando [Get-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt126012.aspx)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas
Após concluir o planejamento de capacidade e configurar um cluster, leia o seguinte:

* [Segurança do Cluster do Service Fabric](service-fabric-cluster-security.md)
* [Introdução ao modelo de Integridade do Service Fabric](service-fabric-health-introduction.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png

<!---HONumber=AcomDC_0921_2016-->