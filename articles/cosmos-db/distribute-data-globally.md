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
ms.openlocfilehash: 4aa5e4ff46eeaa4e8d8c723f626dd1f1193fd12a
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51281601"
---
# <a name="global-data-distribution-with-azure-cosmos-db"></a>Distribuição de dados global com o Azure Cosmos DB

Muitos dos aplicativos de hoje são executados em escala planetária. Esses aplicativos estão sempre ativos e acessíveis para usuários em todo o mundo. Gerenciar a distribuição global dos dados que são usados por esses aplicativos, fornecendo alta disponibilidade e alto desempenho, é um problema sério. O Azure Cosmos DB é um serviço de banco de dados distribuído globalmente projetado para fornecer alto desempenho e alta disponibilidade. Devido a esses motivos, o Azure Cosmos DB é mais adequado para esses aplicativos em tempo real.

O Cosmos DB é um serviço do Azure básico e está disponível em todas as [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/) por padrão. A Microsoft opera datacenters do Azure em mais de 54 regiões em todo o mundo e continua a expandir para atender às crescentes necessidades dos clientes. Ao criar uma conta do Cosmos DB, você pode decidir em qual(is) região(ões) ele deve ser implantado. A Microsoft opera o serviço do Cosmos DB 24/7, portanto, você pode se concentrar em seus aplicativos.

Você pode configurar seus bancos de dados para serem distribuídos globalmente e estar disponíveis em qualquer uma das mais de 50 regiões do Azure. Para diminuir a latência, coloque os dados mais próximos da localização do usuário. A escolha das regiões requeridas depende do alcance global de sua aplicação e onde seus usuários estão localizados. O Cosmos DB replica de forma transparente os dados da sua conta para todas as regiões configuradas. Ele fornece uma única imagem do sistema do banco de dados e contêineres do Cosmos pelos quais seu aplicativo pode ler e gravar localmente. Com o Cosmos DB, você pode adicionar ou remover regiões associadas à sua conta a qualquer momento. Seu aplicativo não precisa ser pausado ou reimplementado para adicionar ou remover uma região. Ele continua altamente disponível o tempo todo por causa dos recursos de multihoming fornecidos pelo serviço.

## <a name="key-benefits-of-global-distribution"></a>Principais benefícios da distribuição global

**Crie aplicativos ativos/ativos globais**: com o recurso de mestres múltiplos, cada região é uma região de gravação (além de legível). O recurso multi-master também garante escalabilidade de gravação elástica ilimitada, 99,999% de leitura e disponibilidade de gravação em todo o mundo e leituras / gravações garantidas em menos de 10 milissegundos no 99º percentil.  

Usando as APIs de hospedagem múltipla do Azure Cosmos DB, seu aplicativo está ciente da região mais próxima e pode enviar solicitações para essa região. A região mais próxima é identificada sem alterações de configuração. À medida que você adiciona e remove regiões da conta do Cosmos DB, seu aplicativo não precisa ser reimplantado e continua altamente disponível.

**Crie aplicativos altamente responsivos**: seu aplicativo pode ser facilmente projetado para executar leituras e gravações quase em tempo real, com latências de milissegundos de dígito único em relação a todas as regiões escolhidas para seu banco de dados.  O Azure Cosmos DB lida internamente com a replicação de dados entre regiões, de modo que o nível de consistência escolhido para a conta do Cosmos seja garantido.

Muitos aplicativos serão beneficiados pelos aprimoramentos de desempenho que vêm com a capacidade de executar gravações em várias regiões (locais). Alguns aplicativos que exigem consistência forte preferem canalizar todas as gravações para uma única região. Para suportar esses aplicativos, o Cosmos DB oferece suporte a configurações de região única e multirregional.

**Crie aplicativos altamente disponíveis**: A execução de um banco de dados em várias regiões aumenta a disponibilidade do banco de dados. Se uma região não estiver disponível, outras regiões manipularão automaticamente as solicitações do aplicativo. O Azure Cosmos DB oferece 99,999% de disponibilidade de leitura e gravação para bancos de dados de várias regiões.

**Continuidade dos negócios durante interrupções regionais**: o Azure Cosmos DB dá suporte a [failover automático](how-to-manage-database-account.md#enable-automatic-failover-for-your-cosmos-account) durante uma interrupção regional. Além disso, durante uma interrupção regional, o Cosmos DB continua mantendo sua latência, disponibilidade, consistência e SLAs de taxa de transferência. Para ajudar a garantir que todo o seu aplicativo esteja altamente disponível, o Azure Cosmos DB oferece uma API de failover manual para simular uma indisponibilidade regional. Ao usar essa API, você pode executar exercícios regulares de continuidade de negócios.

**Escalabilidade global de leitura e gravação**: com o recurso de vários mestres, você pode elasticamente dimensionar a taxa de transferência de leitura e gravação em todo o mundo. O recurso de vários mestres garante que a taxa de transferência que seu aplicativo configura em um banco de dados do Azure Cosmos DB ou em um contêiner seja entregue em todas as regiões e esteja protegida pelos [SLAs com suporte financeiro](https://aka.ms/acdbsla).

**Modelos de consistência múltiplos e bem definidos**: o protocolo de replicação do Azure Cosmos DB foi desenvolvido para oferecer cinco modelos de consistência bem definidos, práticos e intuitivos. Cada modelo de consistência tem uma compensação entre consistência e desempenho. Esses modelos de consistência permitem que você crie aplicativos distribuídos globalmente com facilidade.

## <a id="Next Steps"></a>Próximas etapas

Leia mais sobre a distribuição global nos seguintes artigos:

* [Distribuição global – nos bastidores](global-dist-under-the-hood.md)
* [Como configurar clientes para multihoming](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Como adicionar / remover regiões da sua conta do Cosmos](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Como criar uma política de resolução de conflitos personalizada para contas da API do SQL](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)