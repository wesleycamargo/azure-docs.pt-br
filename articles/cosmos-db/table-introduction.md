---
title: "Introdução à API de Tabela do BD Cosmos do Azure | Microsoft Docs"
description: "Saiba como você pode usar o BD Cosmos do Azure para armazenar e consultar grandes volumes de dados de chave-valor com baixa latência, usando as APIs do MongoDB de OSS populares."
services: cosmos-db
author: bhanupr
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: d1e1f977c9023f4727d8c444b5e490e8f5ba8d9e
ms.contentlocale: pt-br
ms.lasthandoff: 05/31/2017


---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Introdução ao BD Cosmos do Azure: API de Tabela

O [BD Cosmos do Azure](introduction.md) é o serviço multimodelo de banco de dados da Microsoft, distribuído globalmente, para aplicativos críticos. O Azure Cosmos DB fornece [distribuição global imediata](distribute-data-globally.md), [dimensionamento elástico da taxa de transferência e do armazenamento](partition-data.md) mundialmente, latências de milissegundos de um dígito no 99º percentil, [cinco níveis de consistência bem-definidos](consistency-levels.md) e garantia de alta disponibilidade, tudo isso com suporte de [SLAs líderes do setor](https://azure.microsoft.com/support/legal/sla/cosmos-db/). O Azure Cosmos DB [indexa dados automaticamente](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sem a necessidade de lidar com o gerenciamento do esquema e do índice. Ele tem vários modelos e dá suporte a modelos de dados de colunas, gráficos, chave-valor e documentos. 

![API de Armazenamento de Tabelas do Azure e BD Cosmos do Azure](./media/table-introduction/premium-tables.png) 

O BD Cosmos do Azure fornece a API de tabela para aplicativos que precisam de um repositório de chave-valor com esquema flexível. Os SDKs e as APIs REST do [Armazenamento de Tabelas do Azure](../storage/storage-introduction.md) podem ser usados para trabalhar com o BD Cosmos do Azure. Você pode usar o Azure Cosmos DB para criar tabelas com requisitos de alta taxa de transferência. O Azure Cosmos DB dá suporte a tabelas com otimização de taxa de transferência (chamadas informalmente de "tabelas premium"), atualmente em visualização pública. 

Você pode continuar usando o Armazenamento de Tabelas do Azure para tabelas com alto requisitos de armazenamento e menores taxa de transferência. O BD Cosmos do Azure apresentará o suporte para tabelas com otimização de armazenamento em uma atualização futura e contas de armazenamento de tabelas do Azure novas e existentes serão atualizadas para o BD Cosmos do Azure.

## <a name="premium-and-standard-table-apis"></a>APIs de tabela padrão e premium
Caso utilize o Armazenamento de Tabelas do Azure neste momento, você receberá os seguintes benefícios ao mudar para a versão prévia de "tabela premium" do BD Cosmos do Azure:

|  | Armazenamento de Tabelas do Azure | BD Cosmos do Azure: Armazenamento de Tabelas (versão prévia) |
| --- | --- | --- |
| Latência | Rápido, mas não há limites superiores na latência | Latência de milissegundo de dígito único para leituras e gravações, com suporte de leituras de latência de <10 ms e gravações de latência de <15 ms no 99º percentil, em qualquer escala, em qualquer lugar do mundo |
| Taxa de transferência | Modelo de taxa de transferência altamente escalonável, mas não dedicado. As tabelas têm um limite de escalabilidade de 20.000 operações/s | Altamente escalonável com [taxa de transferência reservada dedicada por tabela](request-units.md), que é respaldada por SLAs. As contas não têm nenhum limite superior na taxa de transferência e dão suporte para >10 milhões de operações/s por tabela |
| Distribuição Global | Região única com uma região secundária legível opcional para alta disponibilidade. Você não pode iniciar o failover | [Distribuição global imediata](distribute-data-globally.md) de um a 30 regiões ou mais, suporte para [failovers automáticos e manuais](regional-failover.md) a qualquer momento, em qualquer lugar no mundo |
| Indexação | Somente índice primário em PartitionKey e RowKey. Nenhum índice secundário | Indexação automática e completa em todas as propriedades, não há gerenciamento de índice |
| Consultar | A execução de consulta usa o índice para chave primária. Caso contrário, realiza a verificação. | As consultas podem aproveitar a indexação automática em propriedades para tempos rápidos de consulta. O mecanismo do banco de dados do BD Cosmos do Azure é capaz de dar suporte agregações, geoespacial e classificação. |
| Consistência | Forte na região primária, eventual na região secundária | [cinco níveis de consistência bem definidos](consistency-levels.md) para compensar a disponibilidade, latência, taxa de transferência e consistência com base nas necessidades do seu aplicativo |
| Preços | Otimização de armazenamento  | Otimização de taxa de transferência |
| SLAs | 99,9% de disponibilidade | 99,99% de disponibilidade dentro de uma única região e a capacidade de adicionar mais regiões para maior disponibilidade. [SLAs abrangentes líderes do setor](https://azure.microsoft.com/support/legal/sla/cosmos-db/) em disponibilidade geral |

## <a name="how-to-get-started"></a>Como começar

Crie uma conta do BD Cosmos do Azure no [portal do Azure](https://portal.azure.com)e conheça o [Início rápido para a API de tabela usando o .NET](create-table-dotnet.md). 

## <a name="next-steps"></a>Próximas etapas

Aqui estão algumas dicas para começar:
* Introdução à [API de Tabela do BD Cosmos do Azure](create-table-dotnet.md) usando o SDK de Tabela NET existente.
* Saiba mais sobre [Distribuição global com o BD Cosmos do Azure](distribute-data-globally.md).
* Saiba mais sobre [Taxa de transferência provisionada no BD Cosmos do Azure](request-units.md).
