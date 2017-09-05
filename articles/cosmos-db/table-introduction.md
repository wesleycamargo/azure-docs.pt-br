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
ms.date: 08/29/2017
ms.author: arramac
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 3ccc3b176df2f0a5d864554a74508292d272bd5a
ms.contentlocale: pt-br
ms.lasthandoff: 08/30/2017

---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Introdução ao BD Cosmos do Azure: API de Tabela

O [Azure Cosmos DB](introduction.md) fornece a API de Tabela (versão prévia) para aplicativos escritos para o serviço Armazenamento de Tabelas do Azure e precisam de recursos premium como [distribuição global turnkey](distribute-data-globally.md), [taxa de transferência dedicada](partition-data.md) no mundo todo, latências de milissegundos com um dígito no 99º percentil, garantia de alta disponibilidade e [indexação secundária automática](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf). Esses aplicativos podem migrar para o Azure Cosmos DB usando a API de Tabelas, sem alterações de código, e tirar proveito dos recursos premium.

Recomendamos que faça uma introdução assistindo ao vídeo a seguir, em que Aravind Ramachandran apresenta a API de Tabelas para o Azure Cosmos DB.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Table-API-for-Azure-Cosmos-DB/player]
> 
> 

## <a name="premium-and-standard-table-apis"></a>APIs de tabela padrão e premium
Caso utilize o Armazenamento de Tabelas do Azure neste momento, você receberá os seguintes benefícios ao mudar para a versão prévia de "tabela premium" do BD Cosmos do Azure:

|  | Armazenamento de Tabelas do Azure | BD Cosmos do Azure: Armazenamento de Tabelas (versão prévia) |
| --- | --- | --- |
| Latência | Rápido, mas não há limites superiores na latência | Latência de milissegundo de dígito único para leituras e gravações, com suporte de leituras de latência de <10 ms e gravações de latência de <15 ms no 99º percentil, em qualquer escala, em qualquer lugar do mundo |
| Taxa de transferência | modelo de taxa de transferência variável. As tabelas têm um limite de escalabilidade de 20.000 operações/s | Altamente escalonável com [taxa de transferência reservada dedicada por tabela](request-units.md), que é respaldada por SLAs. As contas não têm nenhum limite superior na taxa de transferência e dão suporte para >10 milhões de operações/s por tabela |
| Distribuição Global | Região única com uma região secundária legível opcional para alta disponibilidade. Você não pode iniciar o failover | [Distribuição global imediata](distribute-data-globally.md) de um a 30 regiões ou mais, suporte para [failovers automáticos e manuais](regional-failover.md) a qualquer momento, em qualquer lugar no mundo |
| Indexação | Somente índice primário em PartitionKey e RowKey. Nenhum índice secundário | Indexação automática e completa em todas as propriedades, não há gerenciamento de índice |
| Consultar | A execução de consulta usa o índice para chave primária. Caso contrário, realiza a verificação. | As consultas podem aproveitar a indexação automática em propriedades para tempos rápidos de consulta. O mecanismo do banco de dados do BD Cosmos do Azure é capaz de dar suporte agregações, geoespacial e classificação. |
| Consistência | Forte na região primária, eventual na região secundária | [Cinco níveis de consistência bem definidos](consistency-levels.md) para compensar a disponibilidade, latência, taxa de transferência e consistência com base nas necessidades do seu aplicativo |
| Preços | Otimização de armazenamento  | Otimização de taxa de transferência |
| SLAs | 99,9% de disponibilidade | 99,99% de disponibilidade dentro de uma única região e a capacidade de adicionar mais regiões para maior disponibilidade. [SLAs abrangentes líderes do setor](https://azure.microsoft.com/support/legal/sla/cosmos-db/) em disponibilidade geral |

## <a name="how-to-get-started"></a>Como começar

Crie uma conta do BD Cosmos do Azure no [portal do Azure](https://portal.azure.com)e conheça o [Início rápido para a API de tabela usando o .NET](create-table-dotnet.md). 

## <a name="next-steps"></a>Próximas etapas

Aqui estão algumas dicas para começar:
* [Compilar um aplicativo .NET usando a API de Tabela](create-table-dotnet.md)
* [Desenvolver com a API de Tabela no .NET](tutorial-develop-table-dotnet.md)
* [Consultar dados de tabela usando a API de Tabela](tutorial-query-table.md)
* [Como configurar a distribuição global do Azure Cosmos DB usando a API de Tabela](tutorial-global-distribution-table.md)
* [SDK da API de Tabela do Azure Cosmos DB para .NET](table-sdk-dotnet.md)

