---
title: Distribuir dados globalmente com o Azure Cosmos DB
description: Aprenda sobre a replicação geográfica em escala de planeta, vários mestres, failover e recuperação de dados usando bancos de dados globais do Azure Cosmos DB, um serviço de banco de dados de vários modelos distribuído globalmente.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/26/2018
ms.openlocfilehash: 3599875f96c6bd79ecace1d59c3580027fab3168
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54040346"
---
# <a name="global-data-distribution-with-azure-cosmos-db"></a>Distribuição de dados global com o Azure Cosmos DB

As aplicações de hoje precisam ser altamente responsivas e sempre on-line. Para obter baixa latência e alta disponibilidade, instâncias desses aplicativos precisam ser implantadas em datacenters próximos aos seus usuários. Esses aplicativos geralmente são implantados em vários datacenters e são chamados de distribuídos globalmente. Os aplicativos distribuídos globalmente precisam de um banco de dados distribuído globalmente que possa replicar os dados de maneira transparente em qualquer lugar do mundo, a fim de permitir que os aplicativos operem em uma cópia dos dados próxima de seus usuários. 

O Azure Cosmos DB é um serviço de banco de dados distribuído globalmente projetado para fornecer baixa latência, escalabilidade elástica de taxa de transferência, semântica bem definida para consistência de dados e alta disponibilidade. Em suma, se seu aplicativo precisa de um tempo de resposta rápido garantido em qualquer lugar do mundo, se precisa estar sempre online e escalabilidade elástica de taxa de transferência e armazenamento ilimitada, considere a possibilidade de criar aplicativos usando o Azure Cosmos DB.

É possível configurar os bancos de dados para serem distribuídos globalmente e disponíveis em qualquer uma das regiões do Azure. Para diminuir a latência, coloque os dados mais próximos da localização de usuários. A escolha das regiões requeridas depende do alcance global de sua aplicação e onde seus usuários estão localizados. O Azure Cosmos DB replica de forma transparente os dados da sua conta para todas as regiões associadas à conta. Ele fornece uma única imagem do sistema do banco de dados do Azure Cosmos globalmente distribuído e dos contêineres que o aplicativo pode ler e gravar localmente. 

Com o Azure Cosmos DB, você poderá adicionar ou remover as regiões associadas à sua conta a qualquer momento. Seu aplicativo não precisa ser pausado ou reimplementado para adicionar ou remover uma região. Ele continua altamente disponível o tempo todo por causa dos recursos de multihoming fornecidos pelo serviço.

## <a name="key-benefits-of-global-distribution"></a>Principais benefícios da distribuição global

**Compilar aplicativos globais de ativo-ativo.** Com o recurso de vários mestre, cada região é uma região de gravação. Isso também é legível. O recurso de vários mestres também garante:

- Escalabilidade de gravação de elástico ilimitado. 
- 99,999% de leitura e gravação de disponibilidade em todo o mundo.
- Garantia de leituras e gravações atendidas em menos de 10 milissegundos no percentil 99.

Ao usar o multihoming das APIs do Azure Cosmos DB, seu aplicativo está ciente da região mais próxima. Em seguida, ele pode enviar solicitações para essa região. A região mais próxima é identificada sem alterações de configuração. Na medida em que você adiciona e remove regiões da conta do Azure Cosmos DB, seu aplicativo não precisará ser reimplantado. O aplicativo continua a ser altamente disponível.

**Crie aplicativos altamente responsivos.** Seu aplicativo pode ser criado facilmente para realizar quase em tempo real leituras e gravações. Ele pode usar as latências de milissegundo de dígito único em relação a todas as regiões que você escolheu para seu banco de dados. Internamente, o Azure Cosmos DB manipula a replicação de dados entre regiões. Como resultado, é garantido o nível de consistência selecionado para a conta do Azure Cosmos DB.

Muitos aplicativos serão beneficiados pelos aprimoramentos de desempenho que vêm com a capacidade de executar gravações em várias regiões (locais). Alguns aplicativos que exigem consistência forte preferem canalizar todas as gravações para uma única região. Para esses aplicativos, o Azure Cosmos DB dá suporte a configurações multirregionais e região única.

**Crie aplicativos altamente disponíveis.** A execução de um banco de dados em várias regiões aumenta a disponibilidade do banco de dados. Se uma região não estiver disponível, outras regiões manipularão automaticamente as solicitações do aplicativo. O Azure Cosmos DB oferece 99,999% de disponibilidade de leitura e gravação para bancos de dados de várias regiões.

**Manter a continuidade dos negócios durante interrupções regionais.** O Azure Cosmos DB dá suporte ao [failover automático](how-to-manage-database-account.md#automatic-failover) durante uma ou mais interrupções regionais. Durante uma indisponibilidade regional, o Azure Cosmos DB continuará mantendo seus SLAs de latência, disponibilidade, consistência e taxa de transferência. Para ajudar a garantir que todo o seu aplicativo esteja altamente disponível, o Azure Cosmos DB oferece uma API de failover manual para simular uma indisponibilidade regional. Ao usar essa API, você pode executar exercícios regulares de continuidade de negócios.

**Escala de leitura e taxa de transferência globalmente.** Com o recurso de vários mestres, você pode elasticamente dimensionar a taxa de transferência de leitura e gravação em todo o mundo. O recurso de vários mestres garante que a taxa de transferência que seu aplicativo configura em um banco de dados do Azure Cosmos DB ou em um contêiner seja entregue em todas as regiões. A taxa de transferência também é protegida pelos [SLAs de suporte financeiro](https://aka.ms/acdbsla).

**Escolha entre vários modelos de consistência bem definidos.** O protocolo de replicação do Azure Cosmos DB oferece cinco modelos de consistência bem definidos, práticos e intuitivos. Cada modelo tem uma compensação entre consistência e desempenho. Usar esses modelos de consistência permitem que você crie aplicativos distribuídos globalmente com facilidade.

## <a id="Next Steps"></a>Próximas etapas

Leia mais sobre a distribuição global nos seguintes artigos:

* [Distribuição global – nos bastidores](global-dist-under-the-hood.md)
* [Configurar clientes para multihoming](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Como adicionar/remover regiões da conta do Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Criar uma política de resolução de conflitos personalizada para contas da API do SQL](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)