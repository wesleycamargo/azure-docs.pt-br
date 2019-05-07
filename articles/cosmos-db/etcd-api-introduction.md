---
title: Introdução ao etcd API do Azure Cosmos DB
description: Este artigo fornece uma benefícios principais e visão geral do etcd API no Azure Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 6c7fcb1429438ee024cb226b63cfcdcab05ed9f8
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205797"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Introdução ao etcd do Azure Cosmos DB API (versão prévia)

O Azure Cosmos DB é um serviço de banco de dados multimodelo globalmente distribuído da Microsoft para aplicativos de missão crítica. Ele oferece distribuição global turnkey, dimensionamento elástica do armazenamento, latências de milissegundo de único dígito no 99 º percentil e a produtividade e garantia de alta disponibilidade, tudo com suporte por SLAS líderes do setor.

[Etcd](https://github.com/etcd-io/etcd) é um repositório de chave/valor distribuídas. Na [Kubernetes](https://kubernetes.io/), etcd é usado para armazenar o estado e a configuração de clusters Kubernetes. Assegurar a disponibilidade, confiabilidade e desempenho de etcd é crucial para a integridade geral do cluster, escalabilidade, disponibilidade de elasticidade e desempenho de um cluster Kubernetes. 

A API etcd no Azure Cosmos DB permite que você use o Azure Cosmos DB como armazenamento de back-end para [Kubernetes do Azure](../aks/index.yml). etcd API no Azure Cosmos DB está atualmente em visualização. O Azure Cosmos DB implementa o protocolo de transmissão etcd. Com etcd API no Azure Cosmos DB, os desenvolvedores automaticamente terão altamente confiáveis, [disponível](high-availability.md), [distribuído globalmente](distribute-data-globally.md) Kubernetes. Essa API permite aos desenvolvedores escalar o gerenciamento de estado do Kubernetes em um serviço de PaaS nativo de nuvem totalmente gerenciado. 

> [!NOTE]
> Ao contrário de outras APIs no Azure Cosmos DB, você não pode provisionar uma conta da API etcd por meio do portal do Azure, SDKs ou CLI. Você pode provisionar uma conta da API etcd Implantando o modelo do Resource Manager. Para obter etapas detalhadas, consulte [como provisionar o Azure Kubernetes com o Azure Cosmos DB](bootstrap-kubernetes-cluster.md) artigo. A API do Azure Cosmos DB etcd está atualmente em versão prévia limitada. Você pode [inscrição para a versão prévia](https://aka.ms/cosmosetcdapi-signup), preenchendo o formulário de inscrição.

## <a name="wire-level-compatibility"></a>Compatibilidade de nível de transmissão

O Azure Cosmos DB implementa o protocolo de transmissão de etcd versão 3 e permite que o [do nó mestre](https://kubernetes.io/docs/concepts/overview/components/) servidores de API para usar o Azure Cosmos DB exatamente como faria em um ambiente etcd instalado localmente. A API etcd dá suporte à autenticação mútua de TLS. 

O diagrama a seguir mostra os componentes de um cluster Kubernetes. No mestre de cluster, o servidor de API usa o API do Azure Cosmos DB etcd, em vez de etcd instalado localmente. 

![O Azure Cosmos DB, Implementando o protocolo de transmissão etcd](./media/etcd-api-introduction/etcd-api-wire-protocol.png)

## <a name="key-benefits"></a>Principais benefícios

### <a name="no-etcd-operations-management"></a>Sem gerenciamento de operações etcd

Como um serviço nativo de nuvem totalmente gerenciado, o Azure Cosmos DB elimina a necessidade para desenvolvedores de Kubernetes configurar e gerenciar etcd. A API etcd no Azure Cosmos DB é escalonável, altamente disponível e tolerante a falhas e oferece alto desempenho. A sobrecarga de configuração de replicação em vários nós, executando sem interrupção de atualizações, patches de segurança, e o monitoramento da integridade de etcd são manipuladas pelo Azure Cosmos DB.

### <a name="global-distribution--high-availability"></a>Distribuição global e alta disponibilidade 

Usando a API de etcd, o Azure Cosmos DB garante a disponibilidade de 99,99% para leituras de dados e grava em uma única região e 99,999% de disponibilidade em várias regiões. 

### <a name="elastic-scalability"></a>Escalabilidade elástica

O Azure Cosmos DB oferece escalabilidade elástica para leitura e solicitações de gravação em regiões diferentes.
À medida que cresce o cluster Kubernetes, a conta etcd API no Azure Cosmos DB é escalonado elasticamente sem nenhum tempo de inatividade. Armazenar dados etcd no Azure Cosmos DB, em vez dos nós mestres do Kubernetes também permite que o dimensionamento mais flexível de nó mestre. 

### <a name="security--enterprise-readiness"></a>Preparação de segurança e enterprise

Quando os dados de etcd são armazenados no Azure Cosmos DB, os desenvolvedores de Kubernetes obtém automaticamente a [internos de criptografia em repouso](database-encryption-at-rest.md), [certificações e conformidade](compliance.md), e [de backup e restauração recursos](online-backup-and-restore.md) com suporte do Azure Cosmos DB. 

## <a name="next-steps"></a>Próximas etapas

* [Como usar o Azure Kubernetes com o Azure Cosmos DB](bootstrap-kubernetes-cluster.md)
* [Principais benefícios do Azure Cosmos DB](introduction.md)
* [Guia de início rápido do AKS mecanismo](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)