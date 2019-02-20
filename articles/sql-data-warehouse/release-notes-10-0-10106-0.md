---
title: Notas sobre a versão do SQL Data Warehouse do Azure | Microsoft Docs
description: Notas de versão do SQL Data Warehouse do Azure.
services: sql-data-warehouse
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/09/2019
author: mlee3gsd
ms.author: anumjs
ms.reviewer: jrasnick
manager: craigg
ms.openlocfilehash: 51932ebf7d5bdc6830098ce7136a3eee7255ffe1
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56245500"
---
# <a name="azure-sql-data-warehouse-release-notes"></a>Notas sobre a versão do SQL Data Warehouse do Azure
Este artigo resume os novos recursos e melhorias nas versões recentes do [SQL Server em máquinas virtuais do Azure](sql-data-warehouse-overview-what-is.md). O artigo também lista importantes atualizações de conteúdo que não estão diretamente relacionadas à versão, mas foram publicadas no mesmo período. Para encontrar melhorias para outros serviços do Azure, confira [Atualizações de serviço](https://azure.microsoft.com/updates)

## <a name="sql-data-warehouse-version-100101060-january"></a>Versão 10.0.10106.0 do SQL Data Warehouse (janeiro)

### <a name="service-improvements"></a>Melhorias de serviços

| Melhorias de serviços | Detalhes |
| --- | --- |
| **Capacidade de Reinicialização de Consulta – CTAS e Insert/Select** | Em situações raras (ou seja, problemas intermitentes de conexão de rede, falhas de nó), a consulta em execução no SQL DW do Azure pode falhar. Instruções de execução prolongada, como as operações [CREATE TABLE AS SELECT (CTAS)](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-ctas) e INSERT-SELECT, estão mais expostas a esse problema potencial. Com esta versão, o SQL DW do Azure implementa a lógica de repetição para instruções de CTAS e INSERT-SELECT, além de instruções SELECT anunciadas anteriormente. As alterações permitem que o serviço lide com problemas transitórios e evite falhas em consultas de maneira transparente. O número de tentativas de repetição e a lista de erros transitórios manipulados são configurado pelo sistema.|
|**Ordem de Devolução por Otimização**|Consultas SELECT...ORDER BY obtêm um aumento de desempenho nesta versão.   Agora, todos os nós de computação enviam seus resultados para um único nó de computação, que mescla e classifica os resultados, que são retornados ao usuário por meio do nó de computação.  A mesclagem por meio de um único nó de computação resulta em um ganho de desempenho significativo quando o conjunto de resultados de consulta contém um grande número de linhas. Anteriormente, o mecanismo de execução de consulta ordenaria os resultados em cada nó de computação e os transmitiria para o nó de controle, que então mesclaria os resultados.|
|**Aprimoramentos de Movimentação de Dados para PartitionMove e BroadcastMove**|No SQL Data Warehouse do Azure Gen2, as etapas de movimentação de dados do tipo ShuffleMove usam técnicas de movimentação de dados instantânea descritas no [blog de aprimoramentos de desempenho](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/). Com esta versão, tipos de movimentação de dados PartitionMove e BroadcastMove agora também são alimentados pelas mesmas técnicas de movimentação de dados instantânea. Consultas de usuário que utilizam esses tipos de etapas de movimentação de dados serão executadas com melhor desempenho. Nenhuma alteração de código é necessária para aproveitar essas melhorias de desempenho.|

### <a name="documentation-improvements"></a>Melhorias na documentação

| Melhorias na documentação | Detalhes |
| --- | --- |
|Nenhum | |
| | |

## <a name="next-steps"></a>Próximas etapas
[criar um SQL Data Warehouse](./create-data-warehouse-portal.md)

## <a name="more-information"></a>Mais informações
- [Blog – SQL Data Warehouse do Azure](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [Blogs da Equipe Consultoria para Clientes](https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/)
- [Histórias de sucesso de clientes](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Fórum Stack Overflow](http://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
- [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Glossário do Azure](../azure-glossary-cloud-terminology.md)