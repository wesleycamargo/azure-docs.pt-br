---
title: "Introdução ao Azure Cosmos DB | Microsoft Docs"
description: "Saiba mais sobre o Azure Cosmos DB. Este multimodelo de banco de dados distribuído globalmente foi criado para alta disponibilidade, escalabilidade elástica e baixa latência."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: a855183f-34d4-49cc-9609-1478e465c3b7
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 12/15/2017
ms.author: mimig
ms.custom: mvc
ms.openlocfilehash: e8b1454583e52f2c7a38b375df259a8b66f7d24f
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2017
---
# <a name="welcome-to-azure-cosmos-db"></a>Bem-vindo ao Azure Cosmos DB

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

O BD Cosmos do Azure é o multimodelo de banco de dados distribuído globalmente da Microsoft. Com o clique de um botão, o BD Cosmos do Azure permite que você dimensione a taxa de transferência e o armazenamento de maneira elástica e independente em qualquer número de regiões geográficas do Azure. Ele oferece garantias de taxa de transferência, disponibilidade, latência e consistência com [SLAs](https://aka.ms/acdbsla) (contratos de nível de serviço) abrangentes, algo que nenhum outro serviço de banco de dados pode oferecer. Você pode [Experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma assinatura do Azure, gratuitamente e sem compromisso.

> [!div class="nextstepaction"]
> [Experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/)

![O Azure Cosmos DB é serviço de banco de dados distribuído globalmente da Microsoft com escala horizontal elástica, baixa latência garantida, cinco modelos de consistência e SLAs de garantia abrangente](./media/introduction/azure-cosmos-db.png)

## <a name="key-capabilities"></a>Principais recursos
Como um serviço de banco de dados multimodelo globalmente distribuído, o Azure Cosmos DB facilita a compilação de aplicativos escaláveis e altamente responsivos em escala global:

* **Distribuição global turnkey**
    * Você pode [distribuir seus dados](distribute-data-globally.md) para qualquer número de [regiões do Azure](https://azure.microsoft.com/regions/), com o [clique de um botão](tutorial-global-distribution-sql-api.md). Isso permite que você coloque seus dados onde os usuários estão, garantindo a menor latência possível aos seus clientes. 
    * Usando as APIs de hospedagem múltipla (multihoming) do Azure Cosmos DB, o aplicativo sempre sabe qual é a região mais próxima e envia solicitações ao data center mais próximo. Tudo isso é possível sem alterações de configuração. Você define sua região de gravação e todas as regiões de leitura que desejar e o resto é feito para você.
    * À medida que você adiciona e remove regiões ao banco de dados do Cosmos DB, seu aplicativo não precisa ser reimplantado e continua altamente disponível, graças à capacidade da API de várias home pages.

* **Vários modelos de dados e APIs populares para acessar e consultar dados**
    * O modelo de dados baseado em ARS (Sequência de Registros Atom) em que o Azure Cosmos DB é criado é compatível nativamente com vários modelos de dados, incluindo, mas não se limitando a modelos de documentos, gráficos, valores-chave, tabela e família de colunas.
    * APIs para os seguintes modelos de dados são compatíveis com SDKs disponíveis em vários idiomas:
        * [API de SQL](sql-api-introduction.md): um mecanismo do banco de dados JSON sem esquemas, com ricos recursos de consulta SQL.
        * [API MongoDB](mongodb-introduction.md): um *MongoDB como serviço* altamente escalável, da plataforma Azure Cosmos DB. Compatível com bibliotecas, drivers, ferramentas e aplicativos MongoDB existentes.
        * [API Cassandra](cassandra-introduction.md): um Cassandra como serviço distribuído globalmente, da plataforma Azure Cosmos DB. Compatível com bibliotecas, drivers, ferramentas e aplicativos [Apache Cassandra](https://cassandra.apache.org/) existentes.
        * [API do Graph (Gremlin)](graph-introduction.md): um serviço de banco de dados de gráficos totalmente gerenciado, e escalável, que facilita a criação e execução de aplicativos que funcionam com conjuntos de dados altamente conectados, compatíveis com APIs do Open Graph (baseadas nas especificações do [Apache TinkerPop](http://tinkerpop.apache.org/), Apache Gremlin).
        * [API de tabela](table-introduction.md): um serviço de banco de dados de chave-valor criado para fornecer recursos premium (por exemplo, indexação automática, garantia de baixa latência, distribuição global) para aplicativos de armazenamento de tabela do Azure existentes sem fazer alterações no aplicativo.
        * Modelos de dados adicionais em breve!

* **Dimensionar elasticamente a taxa de transferência e o armazenamento sob demanda, em todo o mundo**
    * Dimensione facilmente a taxa de transferência do banco de dados em uma granularidade [por segundo](request-units.md) e altere-a sempre que desejar. 
    * Dimensione o tamanho do armazenamento [de forma transparente e automática](partition-data.md) para cobrir a seus requisitos de tamanho agora e para sempre.

* **Criar aplicativos altamente responsivos e críticos**
    * O Azure Cosmos DB garante aos seus clientes uma baixa latência de ponta a ponta no 99º percentil. 
    * Para um item típico de 1 KB, o Cosmos BD garante a latência ponta a ponta de leituras em até 10 ms e gravações indexadas até 15 ms no 99º percentil, na mesma região do Azure. As latências medianas são significativamente menores (abaixo de 5 ms).

* **Garantir disponibilidade "sempre ativa"**
    * SLA de disponibilidade de 99,99% para todas as contas de banco de dados de uma única região e disponibilidade de leitura de 99,999% em todas as contas de banco de dados de várias regiões.
    * Implante em qualquer número de [regiões do Azure](https://azure.microsoft.com/regions) para maior disponibilidade e melhor desempenho.
    * Defina, dinamicamente, prioridades para regiões e [simule uma falha](regional-failover.md) de uma ou mais regiões com garantia de zero de perda de dados para testar a disponibilidade ponta a ponta do aplicativo (além de apenas o banco de dados). 

* **Escrever aplicativos distribuídos globalmente, da maneira correta**
    * Cinco [modelos de consistência](consistency-levels.md) bem definidos, práticos e intuitivos fornecem um espectro de consistência forte semelhante a SQL até a consistência final do tipo NoSQL, e todas as ações relacionadas. 
  
* **Garantia do dinheiro de volta**
    * Os principais [contratos de nível de serviço](https://aka.ms/acdbsla), abrangentes e com suporte financeiro, para disponibilidade, latência, taxa de transferência e consistência para seus dados essenciais. 

* **Sem gerenciamento de esquema/índice de banco de dados**
    * Itere rapidamente o esquema do seu aplicativo sem se preocupar com o esquema do banco de dados e/ou com o gerenciamento de índice.
    * O mecanismo de banco de dados do Azure Cosmos DB é completamente independente de esquema: ele indexa automaticamente todos os dados que ingere sem a necessidade de qualquer esquema ou índice e proporciona consultas incrivelmente rápidas. 

* **Baixo custo de propriedade**
    * Cinco a dez vezes [mais econômico](https://aka.ms/cosmos-db-tco-paper) do que uma solução não gerenciada ou uma solução NoSQL no local.
    * Três vezes mais barato do que AWS DynamoDB ou Spanner do Google.

## <a name="capability-comparison"></a>Comparação de funcionalidade

O Azure Cosmos DB fornece as melhores funcionalidades de bancos de dados relacionais e não relacionais.

| Funcionalidades | Bancos de dados relacionais   | Bancos de dados não relacionais (NoSQL) |    Azure Cosmos DB |
| --- | --- | --- | --- |
| Distribuição global | Não  | Não  | Sim, a distribuição turnkey em 30 + regiões, com as APIs de hospedagem múltipla|
| Escala horizontal | Não  | sim | Sim, você pode dimensionar de maneira independente o armazenamento e a taxa de transferência | 
| Garantias de latência | Não  | sim | Sim, 99% de leituras em < 10 ms e gravações em < 15 ms | 
| Alta disponibilidade | Não  | sim | Sim, o Azure Cosmos DB está sempre ativo, tem compensações PACELC bem definidas e fornece opções de failover automático e manual|
| Modelo de dados + API | Relacional + SQL | Multimodelo + API OSS | Multimodelo + SQL + API OSS (e mais em breve) |
| SLAs | sim | Não  | Sim, SLAs abrangentes de latência, taxa de transferência, consistência e disponibilidade |

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Soluções que se beneficiam do Azure Cosmos DB

Qualquer aplicativo [Web, móvel, de jogos ou IoT](use-cases.md) que precisa lidar com grandes quantidades de dados, lê e escreve em uma escala [global](distribute-data-globally.md) com tempos de resposta quase reais para uma variedade de dados, se beneficiarão da alta disponibilidade, alta taxa de transferência, baixa latência e consistência ajustável [garantidas](https://azure.microsoft.com/support/legal/sla/cosmos-db/) do Azure Cosmos DB. Saiba como o Azure Cosmos DB pode ser aplicado a [IoT e telemática](use-cases.md#iot-and-telematics), [varejo e marketing](use-cases.md#retail-and-marketing), [jogos](use-cases.md#gaming), [Web e aplicativos móveis](use-cases.md#web-and-mobile-applications).

## <a name="next-steps"></a>Próximas etapas
Comece no Azure Cosmos DB com um dos nossos guias de início rápido:

* [Introdução à API de SQL do Azure Cosmos DB](create-sql-api-dotnet.md)
* [Introdução à API MongoDB do Azure Cosmos DB](create-mongodb-nodejs.md)
* [Introdução à API Cassandra do Azure Cosmos DB](create-cassandra-dotnet.md)
* [Introdução à API do Graph do Azure Cosmos DB](create-graph-dotnet.md)
* [Introdução à API de Tabela do Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/)
