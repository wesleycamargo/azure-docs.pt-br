---
title: Notas sobre a versão do SQL Data Warehouse do Azure de dezembro de 2018 | Microsoft Docs
description: Notas de versão do SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 12/12/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: 8e82e352ebea4634b1b99864245adcf606352657
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55469321"
---
# <a name="whats-new-in-azure-sql-data-warehouse-version---100101060"></a>O que há de novo no SQL Data Warehouse do Azure – 10.0.10106.0?
O SQL DW (SQL Data Warehouse do Azure) está sendo aperfeiçoado continuamente. Este artigo descreve os novos recursos e alterações que foram introduzidos na versão 10.0.10106.0 do SQL DW.

## <a name="query-restartability---ctas-and-insertselect"></a>Capacidade de Reinicialização de Consulta – CTAS e Insert/Select
Em situações raras (ou seja, problemas intermitentes de conexão de rede, falhas de nó), as consultas em execução no Azure SQL DW podem falhar. Instruções de execução prolongada, como as operações [CREATE TABLE AS SELECT (CTAS)](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-ctas) e INSERT-SELECT, estão mais expostas a esse problema potencial. Com esta versão, o SQL DW do Azure implementa a lógica de repetição para as instruções CTAS e INSERT-SELECT (além das instruções SELECT anunciadas anteriormente), permitindo que o sistema trate desses problemas transitórios de modo transparente e impeça a falha de consultas. O número de tentativas de repetição e a lista de erros transitórios manipulados são configurado pelo sistema.

## <a name="return-order-by-optimization"></a>Ordem de Devolução por Otimização
Consultas SELECT...ORDER BY obtêm um aumento de desempenho nesta versão.  Anteriormente, o mecanismo de execução de consulta ordenaria os resultados em cada nó de computação e os transmitiria para o nó de controle, que então mesclaria os resultados. Com essa melhoria, todos os de computação em vez disso enviam os resultados para um único nó de computação, que então os mescla e retorna os resultados classificados para o usuário por meio do nó de computação.  Isso oferece um ganho de desempenho significativo quando o conjunto de resultados de consulta contém um grande número de linhas.

## <a name="data-movement-enhancements-for-partitionmove-and-broadcastmove"></a>Aprimoramentos de Movimentação de Dados para PartitionMove e BroadcastMove
No SQL Data Warehouse do Azure Gen2, as etapas de movimentação de dados do tipo ShuffleMove aproveitam técnicas de movimentação de dados instantânea descritas no [blog de aprimoramentos de desempenho aqui](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/).  Com esta versão, tipos de movimentação de dados PartitionMove e BroadcastMove agora também são alimentados pelas mesmas técnicas de movimentação de dados instantânea.  Consultas de usuário que utilizam esses tipos de etapas de movimentação de dados terão um aumento de desempenho.  Nenhuma alteração de código é necessária para aproveitar esses ganhos de desempenho.

## <a name="next-steps"></a>Próximas etapas
Agora que você conhece um pouco do SQL Data Warehouse, aprenda como [criar um SQL Data Warehouse][create a SQL Data Warehouse] rapidamente. Se você ainda não conhece o Azure, pode achar o [glossário do Azure][Azure glossary] útil enquanto aprende a nova terminologia. Ou, dê uma olhada em alguns desses outros Recursos do SQL Data Warehouse.  

* [Histórias de sucesso de clientes]
* [Blogs]
* [Solicitações de recursos]
* [Vídeos]
* [Blogs da Equipe Consultoria para Clientes]
* [Fórum Stack Overflow]
* [Twitter]


[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs da Equipe Consultoria para Clientes]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Histórias de sucesso de clientes]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Solicitações de recursos]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
