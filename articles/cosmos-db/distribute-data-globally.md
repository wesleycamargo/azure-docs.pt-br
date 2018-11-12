---
title: Distribuir dados globalmente com o Azure Cosmos DB | Microsoft Docs
description: Aprenda sobre a replicação geográfica em escala de planeta, vários mestres, failover e recuperação de dados usando bancos de dados globais do Azure Cosmos DB, um serviço de banco de dados de vários modelos distribuído globalmente.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: 68ac603964593892dfcbc3488b4e6f2c91a0eb92
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50238267"
---
# <a name="global-data-distribution-with-azure-cosmos-db"></a>Distribuição de dados global com o Azure Cosmos DB

Muitos aplicativos de hoje executam ou têm ambições de executar em escala planetária. Eles estão sempre ativos e são acessíveis a usuários em todo o mundo. Gerenciar a distribuição global dos dados que são usados por esses aplicativos, fornecendo alta disponibilidade e alto desempenho, é um problema sério. O Cosmos DB é um serviço de banco de dados globalmente distribuído que foi projetado especialmente para atender a esses desafios.

O Cosmos DB é um serviço do Azure básico e está disponível em todas as [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/) por padrão. A Microsoft opera centros de dados do Azure em mais de 50 regiões em todo o mundo e continua a expandir para atender às crescentes necessidades dos clientes. A Microsoft opera o serviço do Cosmos DB 24/7, portanto, você pode se concentrar em seus aplicativos. Ao criar uma conta do Cosmos DB, você pode decidir em qual(is) região(ões) ele deve ser implantado.

Os clientes do Cosmos DB podem configurar seus bancos de dados para ser globalmente distribuídos e estar disponíveis em qualquer lugar de uma a mais de a 50 regiões do Azure. Para reduzir a latência, você deve colocar os dados mais próximos ao local do seu usuário, portanto, decidir quantas regiões e em qual delas executar depende do alcance global do seu aplicativo e onde seus usuários estão. O Cosmos DB replica de forma transparente todos os dados dentro de sua conta do Cosmos para todas as regiões configuradas. O Cosmos DB fornece uma única imagem do sistema de seu banco de dados do Cosmos e contêineres, de modo que seu aplicativo possa ler e gravar localmente. Com o Cosmos DB, você pode adicionar ou remover regiões associadas à sua conta a qualquer momento. Seu aplicativo não precisa ser pausado ou reimplantado e continua altamente disponível, fornecendo dados em todos os momentos, graças aos recursos de hospedagem múltipla que o serviço fornece.

## <a name="key-benefits-of-global-distribution"></a>Principais benefícios da distribuição global

**Criar aplicativos ativo-ativo globais com facilidade**: com o recurso de vários mestres, toda região é gravável (além de ser legível), permitindo escalabilidade de gravação elástica ilimitada, 99,999% de disponibilidade de leitura e gravação, em todo o mundo, e garantia de leituras e gravações rápidas atendidas em menos de 10 milissegundos, no percentil de 99.  

Usando as APIs de hospedagem múltipla do Azure Cosmos DB, seu aplicativo sempre sabe qual é a região mais próxima e envia as solicitações para aquela região. A região mais próxima é identificada sem alterações de configuração. À medida que você adiciona e remove regiões da conta do Cosmos DB, seu aplicativo não precisa ser reimplantado e continua altamente disponível.

**Criar aplicativos altamente responsivos**: seu aplicativo pode ser criado facilmente para realizar leituras e gravações quase em tempo real, com latências de milissegundos de dígito único, em relação a todas as regiões que você escolheu para seu banco de dados.  Internamente, o Azure Cosmos DB manipula a replicação de dados entre as regiões de modo a garantir o nível de consistência escolhido para a conta do Cosmos.

Muitos aplicativos serão beneficiados pelos aprimoramentos de desempenho que vêm com a capacidade de executar gravações em várias regiões (locais). Alguns aplicativos, como aqueles que exigem forte consistência, preferirão direcionar todas as gravações para uma única região. O Cosmos DB dá suporte a ambas configurações, região única e várias regiões.

**Criar aplicativos altamente disponíveis**: executar um banco de dados em várias regiões melhora a disponibilidade do banco de dados. Se uma região não estiver disponível, outras regiões manipularão automaticamente as solicitações do aplicativo. O Azure Cosmos DB oferece 99,999% de disponibilidade de leitura e gravação para bancos de dados de várias regiões.

**Continuidade dos negócios durante interrupções regionais**: o Azure Cosmos DB dá suporte a [failover automático](how-to-manage-database-account.md#enable-automatic-failover-for-your-cosmos-account) durante uma interrupção regional. Além disso, durante uma interrupção regional, o Cosmos DB continua mantendo sua latência, disponibilidade, consistência e SLAs de taxa de transferência. Para ajudar a garantir que todo o seu aplicativo esteja altamente disponível, o Azure Cosmos DB oferece a API de failover manual para simular uma interrupção regional. Usando essa API, você pode executar análises de continuidade de negócios regulares e estar pronto.

**Escalabilidade global de leitura e gravação**: com o recurso de vários mestres, você pode elasticamente dimensionar a taxa de transferência de leitura e gravação em todo o mundo. O recurso de vários mestres garante que a taxa de transferência que seu aplicativo configura em um banco de dados do Azure Cosmos DB ou em um contêiner seja entregue em todas as regiões e esteja protegida pelos [SLAs com suporte financeiro](https://aka.ms/acdbsla).

**Vários modelos de consistência bem definidos**: o protocolo de replicação do Azure Cosmos DB é projetado para oferecer cinco modelos de consistência bem definidos, práticos e intuitivos, cada um com uma clara compensação entre consistência e desempenho. Esses modelos de consistência permitem criar aplicativos distribuídos globalmente corretos com facilidade.

## <a id="Next Steps"></a>Próximas etapas

Leia mais sobre a distribuição global nos seguintes artigos:

* [Distribuição global – nos bastidores](global-dist-under-the-hood.md)
* [Como configurar clientes para hospedagem múltipla](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Como adicionar/remover regiões do seu banco de dados](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Como criar uma política de resolução de conflitos personalizada para contas da API do SQL](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)