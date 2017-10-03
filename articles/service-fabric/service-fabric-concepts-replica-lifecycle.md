---
title: "Réplicas e instâncias no Azure Service Fabric | Microsoft Docs"
description: "Entender réplicas, instâncias, sua função e seu ciclo de vida"
services: service-fabric
documentationcenter: .net
author: appi101
manager: anuragg
editor: 
ms.assetid: d5ab75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2017
ms.author: aprameyr
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 794cbed0f5072bcc1c18343b9896aad464861c45
ms.contentlocale: pt-br
ms.lasthandoff: 09/25/2017

---

# <a name="replicas-and-instances"></a>Réplicas e instâncias 
Este artigo fornece uma visão geral do ciclo de vida das réplicas de serviços com estado e instâncias de serviços sem monitoração de estado.

## <a name="instances-of-stateless-services"></a>Instâncias de serviços sem monitoração de estado
Uma instância de um serviço sem estado é uma cópia da lógica de serviço em execução em um dos nós do cluster. Uma instância dentro de uma partição é identificada exclusivamente pelo seu InstanceId. O ciclo de vida de uma instância pode ser modelado pelo diagrama a seguir:

![Ciclo de vida da instância](./media/service-fabric-concepts-replica-lifecycle/instance.png)

### <a name="inbuild-ib"></a>InBuild (IB)
Depois que o Cluster Resource Manager determinar um posicionamento para a instância, ele entrará nesse estado de ciclo de vida. Neste momento, a instância será iniciada no nó (o host do aplicativo foi iniciado, a instância foi criada e, em seguida, aberta). Depois que a inicialização for concluída, a instância fará a transição para o estado pronto. 

Se o nó ou o host de aplicativo para essa instância falhar, ele fará a transição para o estado removido.

### <a name="ready-rd"></a>Pronta (RD)
No estado pronto, a instância entra em funcionamento no nó. Se esse for um serviço confiável, o RunAsync terá sido chamado. 

Se o nó ou o host de aplicativo para essa instância falhar, ele fará a transição para o estado removido.

### <a name="closing-cl"></a>Fechamento (CL)
No estado de fechamento, o Service Fabric está no processo de desligamento da instância neste nó. Isso pode acontecer devido a muitos motivos – por exemplo, atualização de aplicativo, balanceamento de carga ou o serviço que está sendo excluído. Quando o desligamento for concluído, ele fará a transição para o estado removido.

### <a name="dropped-dd"></a>Removido (DD)
No estado removido, a instância não está mais em execução no nó. Neste ponto, o Service Fabric está mantendo os metadados sobre essa instância (que será excluída lentamente).

> [!NOTE]
> É possível fazer a transição de qualquer estado para o estado removido usando a opção ForceRemove em `Remove-ServiceFabricReplica`.
>

## <a name="replicas-of-stateful-services"></a>Réplicas de serviços com estado
Uma réplica de um serviço com estado é uma cópia da lógica de serviço em execução em um dos nós do cluster. Além disso, a réplica mantém uma cópia do estado desse serviço. Há dois conceitos relacionados que descrevem o ciclo de vida e o comportamento de réplicas com estado:
- Ciclo de vida da réplica
- Função da Réplica

A discussão a seguir descreve os serviços com estado persistentes. Para serviços com estado voláteis (ou na memória), os estados inoperantes e removidos são equivalentes.

![Ciclo de vida da réplica](./media/service-fabric-concepts-replica-lifecycle/replica.png)

### <a name="inbuild-ib"></a>InBuild (IB)
Uma réplica InBuild é uma réplica que está sendo criada ou preparada para unir o conjunto de réplicas. Dependendo da função da réplica, o IB tem semânticas diferentes. 

Se o host do aplicativo ou o nó de uma réplica InBuild falhar, ela fará a transição para o estado inoperante.

####<a name="primary-inbuild-replicas"></a>Réplicas do InBuild primárias 
As réplicas do InBuild primárias são as primeiras réplicas em uma partição. Geralmente isso ocorre quando a partição está sendo criada. As réplicas do InBuild primárias também surgem quando todas as réplicas de uma partição são reiniciadas ou removidas.

####<a name="idlesecondary-inbuild-replicas"></a>Réplicas do InBuild IdleSecondary
Essas são novas réplicas que estão sendo criadas pelo Cluster Resource Manager. Elas também podem ser réplicas existentes que estavam inoperantes e precisavam ser adicionadas de volta ao conjunto. Essas réplicas são propagadas ou criadas pela primária antes de poderem ingressar no conjunto de réplicas como ActiveSecondary e participam da confirmação de quorum de operações.

####<a name="activesecondary-inbuild-replicas"></a>Réplicas do InBuild ActiveSecondary
Esse estado pode ser observado em algumas consultas. É uma otimização em que o conjunto de réplicas não está sendo alterado, mas uma réplica precisa ser criada. A própria réplica segue as transições de computador de estado normal (conforme descrito na seção sobre funções de réplica).

### <a name="ready-rd"></a>Pronta (RD)
Uma réplica Pronta é uma réplica que está participando da replicação e da confirmação de quorum de operações. O estado pronto é aplicável a réplicas primárias e secundárias ativas.

Se o host do aplicativo ou o nó de uma réplica pronta falhar, ela fará a transição para o estado inoperante.

### <a name="closing-cl"></a>Fechamento (CL)
Uma réplica entra no estado de fechamento nos seguintes cenários:

- **Desligando o código para a réplica**: talvez o Service Fabric precise desligar o código em execução para uma réplica. Isso pode ocorrer por vários motivos, por exemplo, a atualização do aplicativo, da malha ou da infraestrutura ou devido a uma falha relatada pela réplica etc. Quando o fechamento da réplica for concluído, a réplica fará a transição para o estado inoperante. O estado persistente associado a esta réplica armazenada no disco não é limpo.

- **Removendo a réplica do cluster**: talvez o Service Fabric precise remover o estado persistente e desligar o código em execução para uma réplica. Isso pode ocorrer por vários motivos, por exemplo, o balanceamento de carga

### <a name="dropped-dd"></a>Removido (DD)
No estado removido, a instância não está mais em execução no nó. Também não há mais nenhum estado no nó. Neste ponto, o Service Fabric está mantendo os metadados sobre essa instância (que será excluída definitivamente também).

### <a name="down-d"></a>Inoperante (D)
No estado inoperante, o código de réplica não está em execução, mas o estado persistente para essa réplica existe nesse nó. Uma réplica pode estar inoperante por muitos motivos, por exemplo, o nó, estando inoperante, falha no código da réplica, na atualização do aplicativo, nas falhas da réplica etc.

Uma réplica inoperante é aberta pelo Service Fabric conforme necessário, por exemplo, quando a atualização é concluída no nó etc.

A função de réplica não é relevante no estado inoperante.

### <a name="opening-op"></a>Abertura (OP)
Uma réplica inoperante entra no estado de abertura quando o Service Fabric precisa colocar a réplica em operação novamente. Por exemplo, isso poderia acontecer após a atualização de um código para o aplicativo ser concluído em um nó etc. 

Se o host do aplicativo ou o nó de uma réplica de abertura falhar, ela fará a transição para o estado inoperante.

A função da réplica não é relevante no estado de abertura.

### <a name="standby-sb"></a>StandBy (SB)
Uma réplica StandBy é uma réplica de um serviço persistente que ficou inoperante e foi aberto. Esta réplica poderá ser usada pelo Service Fabric se ele precisar adicionar outra réplica ao conjunto de réplicas (uma vez que ele já tem alguma parte do estado e o processo de build é mais rápido). Depois que o StandByReplicaKeepDuration expirar, a réplica em espera será descartada.

Se o host do aplicativo ou o nó de uma réplica em espera falhar, ela fará a transição para o estado inoperante.

A função da réplica não é relevante no estado em espera.

> [!NOTE]
> Qualquer réplica que não esteja inoperante nem removida é considerada *operante*.
>

> [!NOTE]
> É possível fazer a transição de qualquer estado para o estado removido usando a opção ForceRemove em `Remove-ServiceFabricReplica`.
>

## <a name="replica-role"></a>Função da réplica 
A função da réplica determina sua função no conjunto de réplicas.

- **Primária (P)**: há uma primária no conjunto de réplicas responsável por executar operações de leitura e de gravação. 
- **ActiveSecondary (S)**: essas são réplicas que recebem atualizações de estado da primária, as aplica e enviar confirmações de volta. Há várias secundárias ativas no conjunto de réplicas e o número delas determina o número de falhas que o serviço pode manipular.
- **IdleSecondary (I)**: essas réplicas estão sendo criadas pela primária, ou seja, elas estão recebendo o estado da primária antes de poderem serem promovidas a secundárias ativas. 
- **Nenhuma (N)**: essas réplicas não têm uma responsabilidade no conjunto de réplicas.
- **Desconhecida (U)**: essa é a função inicial de uma réplica antes de receber qualquer chamada à API ChangeRole do Service Fabric.

A seção a seguir descreve as transições da função da réplica e alguns cenários de exemplo no qual elas podem ocorrer:

![Função da Réplica](./media/service-fabric-concepts-replica-lifecycle/role.png)

- U -> P: criação de uma nova réplica primária
- U -> I: criação de uma nova réplica ociosa
- U -> N: exclusão de uma réplica em espera
- I -> S: promoção da secundária ociosa para secundária ativa para que suas confirmações contribuem para o quorum.
- I -> P: promoção de secundária ociosa para primária. Isso pode acontecer em reconfigurações especiais quando a secundária ociosa é a candidata correta a ser primária.
- I -> N: exclusão da réplica secundária ociosa.
- S -> P: promoção de secundária ativa para primária. Isso pode ocorrer devido ao failover da primária ou a uma movimentação primária iniciada pelo Cluster Resource Manager em resposta à atualização do aplicativo ou ao balanceamento de carga etc.
- S -> N: exclusão da réplica secundária ativa.
- P -> S: rebaixamento da réplica primária. Isso pode ocorrer devido a uma movimentação primária iniciada pelo Cluster Resource Manager em resposta à atualização do aplicativo ou ao balanceamento de carga etc.
- P -> N: exclusão da réplica primária

> [!NOTE]
> Os modelos de programação de nível mais alto, como [Reliable Actors](service-fabric-reliable-actors-introduction.md) e [Reliable Services](service-fabric-reliable-services-introduction.md), ocultam o conceito de função de réplica do desenvolvedor. Em Actors, a noção de função é desnecessária, enquanto em Services, ela é amplamente simplificada para a maioria dos cenários.
>

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os conceitos do Service Fabric, confira os seguintes artigos:

- [Ciclo de vida dos Reliable Services - C#](service-fabric-reliable-services-lifecycle.md)

