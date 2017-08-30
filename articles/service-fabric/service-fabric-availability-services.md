---
title: "Disponibilidade de serviços do Service Fabric | Microsoft Docs"
description: "Descreve a detecção de falhas, failover e recuperação para serviços"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 279ba4a4-f2ef-4e4e-b164-daefd10582e4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: b0d4615a9b8ab566f69b27e4879b6e2d597b4990
ms.contentlocale: pt-br
ms.lasthandoff: 08/19/2017

---

# <a name="availability-of-service-fabric-services"></a>Disponibilidade dos serviços de malha do serviço
Este artigo fornece uma visão geral de como o Service Fabric mantém a disponibilidade de um serviço.

## <a name="availability-of-service-fabric-stateless-services"></a>Disponibilidade dos serviços de malha do serviço sem monitoração do estado
Os serviços do Service Fabric do Azure podem ser com ou sem estado. Um serviço sem estado é um serviço de aplicativo que não tem qualquer [estado local](service-fabric-concepts-state.md) que precise estar altamente disponível nem ser altamente confiável.

Criar um serviço sem estado requer a definição de uma `InstanceCount`. A contagem de instâncias define o número de instâncias da lógica de aplicativo do serviço sem estado que deve ser executado no cluster. Aumentar o número de instâncias é a maneira recomendada de escalar horizontalmente um serviço sem estado.

Quando uma instância de um serviço chamado sem estado falha, uma nova instância é criada em algum nó qualificado no cluster. Por exemplo, uma instância de serviço sem estado pode falhar em Node1 e ser recriada em Node5.

## <a name="availability-of-service-fabric-stateful-services"></a>Disponibilidade dos serviços de malha do serviço com monitoração do estado
Um serviço com estado tem algum estado associado a ele. Na malha de serviço, um serviço com monitoração de estado é modelado como um conjunto de réplicas. Cada réplica é uma instância em execução do código do serviço que também tem uma cópia do estado desse serviço. As operações de leitura e gravação são realizadas em uma réplica (chamada de Primária). As alterações no estado, resultantes das operações de gravação, são *replicadas* em outras réplicas no conjunto de réplicas (chamadas de Secundárias Ativas) e aplicadas. 

Pode haver apenas uma réplica Primária, mas pode haver várias réplicas Secundárias Ativas. O número de réplicas Secundárias Ativas é configurável e um número maior de réplicas pode tolerar um número maior de falhas simultâneas de hardware e de software.

Se uma réplica Primária falhar, o Service Fabric torna uma das réplicas Secundárias Ativas a nova réplica Primária. Essa réplica Secundária Ativa já tem a versão atualizada do estado (via *replicação*) e pode continuar processando operações de leitura e de gravação posteriores.

Esse conceito de réplica Primária ou Secundária Ativa é conhecido como Função de Réplica.

### <a name="replica-roles"></a>Funções de réplica
A função de uma réplica é usada para gerenciar o ciclo de vida do estado que está sendo gerenciado por essa réplica. Uma réplica cuja função é Primária atende a solicitações de leitura. A Primária também trata de todas as solicitações de leitura atualizando seu estado e replicando as alterações. Essas alterações são aplicadas nas Secundárias Ativas no conjunto de réplicas. O trabalho de uma Secundária Ativa é receber alterações de estado que a Primária replicou e atualizar a respectiva exibição do estado.

> [!NOTE]
> Os modelos de programação de nível mais alto, como [Reliable Actors](service-fabric-reliable-actors-introduction.md) e [Reliable Services](service-fabric-reliable-services-introduction.md), ocultam o conceito de função de réplica do desenvolvedor. Em Actors, a noção de função é desnecessária, enquanto em Services, ela é amplamente simplificada para a maioria dos cenários.
>

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os conceitos do Service Fabric, confira os seguintes artigos:

- [Dimensionando serviços do Service Fabric](service-fabric-concepts-scalability.md)
- [Particionando serviços da Malha do Serviço](service-fabric-concepts-partitioning.md)
- [Definindo e gerenciando o estado](service-fabric-concepts-state.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)

