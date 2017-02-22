---
title: "Escalabilidade de serviços do Service Fabric | Microsoft Docs"
description: "Descreve como dimensionar os serviços de malha do serviço"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: ed324f23-242f-47b7-af1a-e55c839e7d5d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/30/2016
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 52dcf341a34478bf4e800d8f1b9d44867b5feaf6


---
# <a name="scaling-service-fabric-applications"></a>Colocando em escala aplicativos do Service Fabric
O Azure Service Fabric facilita a compilação de aplicativos escalonáveis gerenciando os serviços, as partições e as réplicas em todos os nós em um cluster. Isso permite a utilização máxima dos recursos.

Uma alta escala para aplicativos do Service Fabric pode ser obtida de duas maneiras:

1. Dimensionando no nível da partição de serviço
2. Dimensionando no nível da instância de serviço nomeada

## <a name="scaling-at-the-partition-level"></a>Dimensionamento no nível da partição
O Service Fabric permite o particionamento. O particionamento, por sua vez, permite que um serviço individual seja dividido em várias partições independentes, cada uma com uma parte do estado geral do serviço. A [Visão geral de particionamento](service-fabric-concepts-partitioning.md) fornece informações sobre os tipos de esquemas de particionamento para os quais há suporte. As réplicas de cada partição são distribuídas entre os nós em um cluster. Considere um serviço que usa um esquema de particionamento de intervalo com uma chave baixa de 0, uma chave alta de 99 e uma contagem de partições de 4. Em um cluster de três nós, o serviço pode ser disposto com quatro réplicas que compartilham os recursos em cada nó, conforme mostrado aqui:

<center>
![Layout de partição com três nós](./media/service-fabric-concepts-scalability/layout-three-nodes.png)
</center>

Se você aumentar o número de nós, o Service Fabric utilizará os recursos nos novos nós movendo algumas das réplicas existentes nele. Ao aumentar o número de nós para quatro, o serviço passa a ter três réplicas em execução em cada nó (cada uma pertencendo a diferentes partições), o que permite melhor utilização e desempenho de recursos.

<center>
![Layout de partição com quatro nós](./media/service-fabric-concepts-scalability/layout-four-nodes.png)
</center>

## <a name="scaling-at-the-service-name-level"></a>Dimensionamento no nível do nome do serviço
Uma instância de serviço é uma instância específica de um nome de aplicativo e um nome de tipo de serviço (consulte [Ciclo de vida do aplicativo do Service Fabric](service-fabric-application-lifecycle.md)). Durante a criação de um serviço, você especifica o esquema de partição ( [Particionamento de serviços do Service Fabric](service-fabric-concepts-partitioning.md)) a ser usado.

O primeiro nível de colocação em escala é por nome de serviço. Você pode criar instâncias de um serviço, com a opção de níveis diferentes de particionamento, conforme as suas instâncias de serviço mais antigas se tornam ocupadas. Isso permite que os consumidores do novo serviço usem instâncias de serviço menos ocupadas, em vez das mais ocupadas.

Uma opção para aumentar a capacidade é criar uma nova instância de serviço com um novo esquema de partição. Entretanto, isso aumenta a complexidade. Qualquer cliente de consumo precisa saber quando e como usar o serviço diferentemente nomeado. Como alternativa, um serviço de gerenciamento ou intermediário precisaria determinar qual serviço e partição devem manipular cada solicitação.

### <a name="example-scenario-embedded-dates"></a>Cenário de exemplo: datas inseridas
Um possível cenário seria usar informações de data como parte do nome do serviço. Por exemplo, você pode usar uma instância de serviço com um nome específico para todos os clientes que ingressaram em 2013 e outro nome para os clientes que ingressaram em 2014. Esse esquema de nomeação permite um aumento programático nos nomes dependendo da data (conforme 2014 se aproxima, a instância do serviço de 2014 pode ser criada sob demanda).

No entanto, essa abordagem baseia-se em clientes usando informações de nomenclatura específicas do aplicativo que estão fora do escopo do conhecimento do Service Fabric.

* *Usando uma convenção de nomenclatura*: em 2013, quando seu aplicativo é ativado, você cria um serviço chamado fabric:/app/service2013. Perto do segundo trimestre de 2013, você cria outro serviço chamado fabric:/app/service2014. Ambos os serviços são do mesmo tipo de serviço. Nessa abordagem, o cliente precisará fazer uso de lógica sobre como construir o nome de serviço apropriado com base no ano.
* *Usando um serviço de pesquisa*: Outro padrão é fornecer um serviço de pesquisa secundário, que pode fornecer o nome do serviço para uma chave desejada. Em seguida, novas instâncias de serviço podem ser criadas pelo serviço de pesquisa. O próprio serviço de pesquisa não retém nenhum dados do aplicativo, somente os dados sobre os nomes de serviço que ele cria. Assim, para o exemplo baseado no ano acima, o cliente primeiro entraria em contato com o serviço de pesquisa para descobrir o nome do serviço que manipula os dados de um determinado ano. Em seguida, o cliente usaria esse nome de serviço para executar a operação real. O resultado da primeira pesquisa pode ser armazenado em cache.

## <a name="putting-it-all-together"></a>Juntando as peças
Vamos juntar tudo o que discutimos aqui e aplicar em outro cenário.

Considere o seguinte exemplo: você está tentando criar um serviço que atue como um catálogo de endereços, mantendo nomes e informações de contato. Quantos usuários você pretende ter? Quantos contatos cada usuário armazenará? Tentar descobrir isso enquanto você está tentando fazer o serviço funcionar pela primeira vez é realmente difícil. As consequências de escolher a contagem de partições errada poderia causar problemas de escala mais tarde. Mas por que tentar escolher um único esquema de partição para todos os usuários?

Nesses tipos de situação, é melhor considerar o seguinte padrão:
1. Em vez de tentar escolher um esquema de particionamento para todos antecipadamente, crie um "serviço de gerenciador".
2. O trabalho do serviço de gerenciador é examinar as informações do cliente quando ele se inscreve no serviço. Em seguida, usar essas informações para criar uma instância do seu serviço de armazenamento de contatos _real_ _apenas para esse cliente_. Esse tipo de padrão dinâmico de criação de serviço traz muitos benefícios:

    * Você não tentará adivinhar antecipadamente a contagem de partições correta para todos os usuários
    * Segmentação de dados, desde que cada cliente tenha sua própria cópia do serviço
    * Cada serviço de cliente pode ser configurado de forma diferente, com mais ou menos partições ou réplicas, conforme a necessidade baseada na escala esperada.
      * Por exemplo, digamos que o cliente pagou pela camada "Gold" — ele poderia obter mais réplicas ou uma contagem mais alta de partições
      * Ou digamos que ele forneceu informações indicando que o número de contatos que precisava era "pequeno" — ele obteria apenas algumas partições.
    * Você não vai executar um monte de instâncias de serviços ou réplicas enquanto aguarda os clientes
    * Se um cliente sair, remover suas informações do serviço é tão simples quanto a exclusão do serviço por um gerente

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os conceitos do Service Fabric, confira os seguintes artigos:

* [Disponibilidade dos serviços de malha do serviço](service-fabric-availability-services.md)
* [Particionando serviços da Malha do Serviço](service-fabric-concepts-partitioning.md)
* [Definindo e gerenciando o estado](service-fabric-concepts-state.md)



<!--HONumber=Jan17_HO1-->


