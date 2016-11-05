---
title: Disponibilidade de serviços do Service Fabric | Microsoft Docs
description: Descreve a detecção de falhas, failover e recuperação para serviços
services: service-fabric
documentationcenter: .net
author: appi101
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/10/2016
ms.author: aprameyr

---
# Disponibilidade dos serviços de malha do serviço
Os serviços do Service Fabric do Azure podem ser com ou sem estado. Este artigo fornece uma visão geral de como a malha do serviço mantém a disponibilidade de um serviço no caso de falhas.

## Disponibilidade dos serviços de malha do serviço sem monitoração do estado
Um serviço sem monitoração de estado é um serviço de aplicativo que não tem nenhum [estado persistente local](service-fabric-concepts-state.md).

A criação de um serviço sem estado exige a definição de uma contagem de instância, que é o número de instâncias do serviço sem estado que devem ser executadas no cluster. Este é o número de cópias da lógica do aplicativo que será instanciada no cluster. Aumentar o número de instâncias é a maneira recomendada de ampliar serviços sem estado.

Quando é detectada uma falha em qualquer instância do serviço sem estado, uma nova instância é criada em outro nó elegível no cluster.

## Disponibilidade dos serviços de malha do serviço com monitoração do estado
Um serviço com estado tem algum estado associado a ele. Na malha de serviço, um serviço com monitoração de estado é modelado como um conjunto de réplicas. Cada réplica é uma instância do código do serviço que tem uma cópia do estado. Operações de leitura e gravação são realizadas em uma réplica (chamada de primária). Alterações no estado devido a operações de gravação são *replicadas* para várias outras réplicas (chamadas de secundárias ativas). Essa combinação de réplicas primária e secundárias ativas é o conjunto de réplicas do serviço.

Pode haver apenas uma réplica primária atendendo a solicitações de gravação e de leitura, mas pode haver várias réplicas secundárias ativas. O número de réplicas secundárias ativas pode ser configurado, e um número maior de réplicas tolera um número maior de falhas simultâneas de hardware e de software.

No caso de uma falha (quando a réplica primária falhar), o Service Fabric torna uma das réplicas secundárias ativas a nova réplica primária. Esta réplica secundária ativa já tem a versão atualizada do estado (via *replicação*) e pode continuar processando operações de leitura e de gravação posteriores.

Esse conceito de réplica como primária ou secundária ativa é conhecido como a função de réplica.

### Funções de réplica
A função de uma réplica é usada para gerenciar o ciclo de vida do estado que está sendo gerenciado por essa réplica. Uma réplica cuja função é primária atende a solicitações de leitura. Ela também atende a solicitações de gravação atualizando seu estado e replicando as alterações para as secundárias ativas em seu conjunto de réplicas. Uma réplica secundária ativa é responsável por receber alterações de estado que a réplica primária replicou e por atualizar sua exibição do estado.

> [!NOTE]
> Modelos de programação de alto nível, como a [Estrutura de reliable actors](service-fabric-reliable-actors-introduction.md) abstrai o conceito de função da réplica do desenvolvedor.
> 
> 

## Próximas etapas
Para obter informações sobre os conceitos do Service Fabric, consulte o seguinte:

* [Escalabilidade de serviços da Malha do Serviço](service-fabric-concepts-scalability.md)
* [Particionando serviços da Malha do Serviço](service-fabric-concepts-partitioning.md)
* [Definindo e gerenciando o estado](service-fabric-concepts-state.md)

<!---HONumber=AcomDC_0810_2016-->