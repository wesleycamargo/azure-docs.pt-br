---
title: Notas sobre a versão do SQL Data Warehouse do Azure de dezembro de 2018 | Microsoft Docs
description: Notas de versão do SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 12/12/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: b897b50edf4d5a7eeabacc6da1505e165f2bb21a
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54431734"
---
# <a name="whats-new-in-azure-sql-data-warehouse-december-2018"></a>O que há de novo no SQL Data Warehouse do Azure? Dezembro de 2018
O SQL Data Warehouse do Azure recebe melhorias continuamente. Este artigo descreve os novos recursos e as alterações que foram introduzidas em dezembro de 2018.

## <a name="virtual-network-service-endpoints-generally-available"></a>Pontos de extremidade de serviço de rede virtual disponíveis
Esta versão inclui a disponibilidade geral dos Pontos de Extremidade de Serviço de VNET (Rede Virtual) para o SQL Data Warehouse do Azure em todas as regiões do Azure. Os Pontos de Extremidade de serviço de VNET permitem isolar a conectividade com o servidor lógico de uma única sub-rede ou de um conjunto de sub-redes na rede virtual. O tráfego para o SQL Data Warehouse do Azure da VNET sempre permanecerá na rede backbone do Azure. Essa rota direta será preferencial em relação a outras rotas específicas que passam o tráfego da Internet por soluções de virtualização ou localmente. Não há cobrança adicional para acesso à rede virtual por meio de pontos de extremidade de serviço. O modelo de preço atual do [SQL Data Warehouse do Azure](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/) se aplica no estado em que se encontra.

Com esta versão, também habilitamos a conectividade do PolyBase no [ADLS (Azure Data Lake Storage) Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) por meio do driver do [ABFS (Sistema de Arquivos de Blob do Azure)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-abfs-driver). O Azure Data Lake Storage Gen2 leva todas as qualidades necessárias para o ciclo de vida completo dos dados analíticos para o Armazenamento do Azure. Os recursos dos dois serviços de armazenamento do Azure existentes, o Armazenamento de Blobs do Azure e o Azure Data Lake Storage Gen1, foram convergidos. Os recursos do [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), como semântica do sistema de arquivos, segurança e escala em nível de arquivo, são combinados com armazenamento em camadas de baixo custo e funcionalidades de alta disponibilidade/recuperação de desastre do [Armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). 

Usando o PolyBase, você também pode importar dados para o SQL Data Warehouse do Azure do Armazenamento do Azure protegido para a VNET. Da mesma forma, também há suporte para exportação de dados do SQL Data Warehouse do Azure para o Armazenamento do Azure protegido para a VNET por meio do PolyBase. 

Para obter mais informações sobre Pontos de Extremidade de Serviço de VNET no SQL Data Warehouse do Azure, consulte a [postagem no blog](https://azure.microsoft.com/blog/general-availability-of-vnet-service-endpoints-for-azure-sql-data-warehouse/) ou a [documentação](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json).

## <a name="automatic-performance-monitoring-preview"></a>Monitoramento de Desempenho Automático (Versão prévia)
O [Repositório de Consultas](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-2017) agora está disponível na Versão Prévia do SQL Data Warehouse do Azure. O Repositório de Consultas foi projetado para ajudá-lo com a solução de problemas de desempenho de consultas pelo acompanhamento de consultas, planos de consulta, estatísticas de tempo de execução e histórico de consultas para ajudá-lo a monitorar a atividade e o desempenho de seu data warehouse. O Repositório de Consultas é um conjunto de repositórios internos e DMVs (Exibições de Gerenciamento Dinâmico) que permitem:

- Identificar e ajustar as consultas que mais consumem recursos
- Identifique e melhore as cargas de trabalho ad hoc
- Avaliar o desempenho da consulta e o impacto no plano com alterações em estatísticas, em índices ou no tamanho do sistema (configuração de DWU)
- Ver o texto de consulta completa para todas as consultas executadas

O Repositório de Consultas contém três repositórios reais:  
- Um repositório de planos para persistir as informações de plano de execução 
- Um repositório de estatísticas de tempo de execução para persistir as informações de estatísticas de execução
- Um repositório de estatísticas de espera para persistir as informações de estatísticas de espera. 

O SQL Data Warehouse gerencia esses repositórios automaticamente e fornece um número ilimitado de consultas armazenadas nos últimos sete dias sem custo adicional. A habilitação do Repositório de Consultas é tão simples quanto a execução de uma instrução T-SQL ALTER DATABASE:

```sql
ALTER DATABASE [Database Name] SET QUERY_STORE = ON;
```
Para obter mais informações sobre o Repositório de Consultas no SQL Data Warehouse do Azure, confira o artigo [Monitorando o desempenho usando o Repositório de Consultas](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-2017) e as DMVs do Repositório de Consultas como [sys.query_store_query](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-query-transact-sql?view=sql-server-2017). Veja a seguir a [postagem no blog](https://azure.microsoft.com/blog/automatic-performance-monitoring-in-azure-sql-data-warehouse-with-query-store/) anunciando a versão.

## <a name="lower-compute-tiers-for-azure-sql-data-warehouse-gen2"></a>Camadas inferiores de computação para o SQL Data Warehouse do Azure Gen2
O SQL Data Warehouse do Azure Gen2 agora dá suporte a camadas inferiores de computação. Os clientes podem experimentar recursos de segurança, flexibilidade e desempenho líder do SQL Data Warehouse do Azure começando com 100 cDWU ([Unidades de Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/what-is-a-data-warehouse-unit-dwu-cdwu)) e aumentá-las para 30.000 cDWU em minutos. A partir de meados de dezembro de 2018, os clientes poderão se beneficiar do desempenho e da flexibilidade do Gen2 com camadas inferiores de computação nas [regiões](https://docs.microsoft.com/azure/sql-data-warehouse/gen2-lower-tier-regions), com o restante das regiões disponíveis durante 2019.

Removendo o ponto de entrada para o data warehouse da próxima geração, a Microsoft abre as portas para clientes orientados pelo valor que desejam avaliar todos os benefícios de um data warehouse seguro e de alto desempenho, sem adivinhar qual ambiente de avaliação é melhor para eles. Os clientes podem começar com apenas 100 cDWU, abaixo do ponto de entrada atual de 500 cDWU. O SQL Data Warehouse Gen2 continua dando suporte a operações de pausa e retomada e vai além de apenas flexibilidade na computação. O Gen2 também dá suporte à capacidade de armazenamento ilimitado de columnstore, juntamente com 2,5 vezes mais memória por consulta, até 128 consultas simultâneas e recursos de [cache adaptável](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/). Em média, esses recursos geram cinco vezes mais desempenho em comparação com a mesma Unidade de Data Warehouse no Gen1 pelo mesmo preço. Backups com redundância geográfica são um padrão no Gen2 com a proteção de dados interna garantida. O SQL Data Warehouse Gen2 do Azure está pronto para ser dimensionado de acordo com as suas necessidades.

## <a name="columnstore-background-merge"></a>Mesclagem em segundo plano de columnstore
Por padrão, o SQL DW do Azure (SQL Data Warehouse do Azure) armazena dados em formato de coluna, com micropartições chamadas [rowgroups](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-memory-optimizations-for-columnstore-compression). Às vezes, devido a restrições de memória no tempo de carregamento de dados ou de build de índice, os rowgroups podem ser compactados com um tamanho menor que o ideal de um milhão de linhas. Os rowgroups também podem se tornar fragmentados devido a exclusões. Rowgroups pequenos ou fragmentados resultam em maior consumo de memória, bem como em uma execução de consulta ineficiente. Com esta versão do SQL DW do Azure, a tarefa de manutenção de columnstore em segundo plano mescla pequenos rowgroups compactados para criar rowgroups maiores para melhor utilização da memória e aceleração da execução da consulta.

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
