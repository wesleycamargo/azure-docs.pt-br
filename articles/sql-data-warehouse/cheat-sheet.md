---
title: "Folha de referências para SQL Data Warehouse do Azure | Microsoft Docs"
description: "Localize links e melhores práticas para compilar rapidamente suas soluções do SQL Data Warehouse do Azure."
services: sql-data-warehouse
documentationcenter: NA
author: acomet
manager: jhubbard
editor: 
ms.assetid: 51f1e444-9ef7-4e30-9a88-598946c45196
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 12/14/2017
ms.author: acomet
ms.openlocfilehash: a16c2230c26865913285cb8cbd5b0f81426acdd1
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2018
---
# <a name="cheat-sheet-for-azure-sql-data-warehouse"></a>Roteiro do SQL Data Warehouse do Azure
Esta folha de referências fornece dicas úteis e melhores práticas para a compilação de suas soluções do SQL Data Warehouse do Azure. Antes da introdução, saiba mais sobre cada etapa detalhadamente, lendo os [Padrões de Carga de Trabalho do SQL Data Warehouse do Azure e Anti-Padrões](https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/azure-sql-data-warehouse-workload-patterns-and-anti-patterns), que explica o que é SQL Data Warehouse e o que não é.

O gráfico a seguir mostra o processo de criação de um data warehouse:

![Esboço]

## <a name="queries-and-operations-across-tables"></a>Consultas e operações entre tabelas

Ao conhecer antecipadamente as principais operações e consultas a serem executadas no seu data warehouse, você poderá priorizar sua arquitetura de data warehouse para essas operações. Essas consultas e operações podem incluir:
* Junção de uma ou duas tabelas de fatos com tabelas de dimensão, filtragem de tabela combinada e, em seguida, acrescentar os resultados em um data mart.
* Fazer atualizações grandes ou pequenas em suas vendas de fato.
* Acrescentar apenas os dados às tabelas.

Saber os tipos de operações antecipadamente ajuda-o a otimizar o design de suas tabelas.

## <a name="data-migration"></a>Migração de dados

Primeiro, carregue os dados no [Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-factory/connector-azure-data-lake-store) ou no Armazenamento de Blobs do Azure. Em seguida, use o PolyBase para carregar os dados no SQL Data Warehouse em uma tabela de preparo. Use a seguinte configuração:

| Design | Recomendações |
|:--- |:--- |
| Distribuição | Round Robin |
| Indexação | Heap |
| Particionamento | Nenhum |
| Classe de recursos | largerc ou xlargerc |

Saiba mais sobre [migração de dados], [carregamento de dados] e o [processo ELT (Extrair, Carregar e Transformar)](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/design-elt-data-loading). 

## <a name="distributed-or-replicated-tables"></a>Tabelas replicadas ou distribuídas

Use as seguintes estratégias, dependendo das propriedades da tabela:

| type | Ideal para...| Fique atento se...|
|:--- |:--- |:--- |
| Replicada | • Tabelas de dimensões pequenas em um esquema em estrela com menos de 2 GB de armazenamento após a compactação (compactação de ~5x) |•  Muitas transações de gravação estão na tabela (como inserir, upsert, excluir, atualizar)<br></br>• Você altera o provisionamento DWU (Unidade de Data Warehouse) com frequência<br></br>• Você usa apenas 2-3 colunas, mas sua tabela tem muitas colunas<br></br>• Você indexar uma tabela replicada |
| Round Robin (padrão) | • Mesa de preparo/temporária<br></br> • Nenhuma coluna candidata boa ou chave de união |•   Desempenho lento devido ao movimento de dados |
| Hash | • Tabela de fatos<br></br>•    Tabela de dimensões grandes |• A chave de distribuição não pode ser atualizada |

**Dicas:**
* Comece com Round Robin, mas almeje uma estratégia de distribuição hash para aproveitar uma arquitetura paralela massiva.
* Certifique-se de que as chaves de hash comuns tenham o mesmo formato de dados.
* Não distribua no formato varchar.
* As tabelas de dimensão com chave de hash comum para uma tabela de fatos com operações join frequentes podem ser distribuídas por hash.
* Use *[sys.dm_pdw_nodes_db_partition_stats]* para analisar qualquer distorção nos dados.
* Use *[sys.dm_pdw_request_steps]* para analisar as movimentações de dados por trás das consultas, monitorar o tempo que as operações de difusão e organização em ordem aleatória levam. Isso é útil para examinar a estratégia de distribuição.

Saiba mais sobre [tabelas replicadas] e [tabelas distribuídas].

## <a name="index-your-table"></a>Indexar a tabela

A indexação é útil para a ler as tabelas rapidamente. Há um conjunto único de tecnologias que você pode usar com base nas suas necessidades:

| type | Ideal para... | Fique atento se...|
|:--- |:--- |:--- |
| Heap | • Tabela de preparo/temporária<br></br>• Pequenas tabelas com pequenas consultas |• Qualquer pesquisa examina a tabela inteira |
| Índice clusterizado | • Tabelas com até 100 milhões de linhas<br></br>• Tabelas grandes (mais de 100 milhões de linhas) com apenas de 1 a 2 colunas bastante utilizadas |• Usada em uma tabela replicada<br></br>•    Você possui consultas complexas envolvendo várias operações Join e Group By<br></br>•  Você faz atualizações nas colunas indexadas: isso consome memória |
| CCI (Índice Columnstore Clusterizado) (Padrão) | •   Tabelas grandes (mais de 100 milhões de linhas) | • Usada em uma tabela replicada<br></br>•    Você fizer operações de atualização massivas na tabela<br></br>•  Você particiona sua tabela em excesso: os grupos de linhas não se estendem entre diferentes partições e nós de distribuição |

**Dicas:**
* Na parte superior de um índice clusterizado, convém adicionar o índice não clusterizado a uma coluna usada para o filtro. 
* Tenha cuidado com como gerenciar a memória em uma tabela com um CCI. Quando você carrega dados, deseja que o usuário (ou a consulta) se beneficie de uma classe de recursos grande. Certifique-se de evitar corte e criação de muitos pequenos grupos de linhas compactadas.
* Otimizado para apoios de camada de computação com CCI.
* Para CCI, o desempenho lento pode acontecer devido à compressão ruim dos grupos de linhas. Se isso ocorrer, recompile ou reorganize o CCI. Você deseja pelo menos 100.000 linhas por grupos de linhas compactadas. O ideal é 1 milhão de linhas em um grupo de linhas.
* Com base na frequência de carregamento incremental e no tamanho, você deseja automatizar quando reorganiza ou recria os índices. A limpeza é sempre útil.
* Seja estratégico quando quiser cortar um grupo de linhas. Qual o tamanho dos grupos de linhas abertas? Qual é a quantidade de dados que você espera carregar nos próximos dias?

Saiba mais sobre [Índices].

## <a name="partitioning"></a>Particionamento
Você pode particionar a tabela quando tiver uma tabela de fatos grande (maior que 1 bilhão de linhas). Em 99% dos casos, a chave de partição deve ser baseada na data. Tenha cuidado para não particionar excessivamente, especialmente quando você tiver um índice columnstore clusterizado.

Com tabelas de preparo que exigem ELT, você pode se beneficiar do particionamento. Ele facilita o gerenciamento de ciclo de vida de dados.
Tenha cuidado para não particionar excessivamente os dados, especialmente em um índice columnstore clusterizado.

Saiba mais sobre [partições].

## <a name="incremental-load"></a>Carga incremental

Se você for carregar incrementalmente os dados, primeiro certifique-se de alocar classes de recursos maiores para carregar os dados. É recomendável usar PolyBase e ADF V2 para automatizar os pipelines ELT no SQL Data Warehouse.

Para um lote de atualizações grande nos dados históricos, primeiro exclua os dados em questão. Em seguida, faça uma inserção em massa dos novos dados. Essa abordagem em duas etapas é mais eficiente.

## <a name="maintain-statistics"></a>Manter as estatísticas
 Até que as estatísticas automáticas estejam geralmente disponíveis, o SQL Data Warehouse irá requerer manutenção manual de estatísticas. É importante atualizar as estatísticas quando ocorrem alterações *significativas* em seus dados. Isso ajuda a otimizar seus planos de consulta. Se você achar que demora demais para manter todas as estatísticas, seja mais seletivo sobre quais colunas têm estatísticas. 

Você também pode definir a frequência das atualizações. Por exemplo, convém atualizar as colunas de data, onde novos valores podem ser adicionados, diariamente. Você obterá mais benefícios se tiver estatísticas em colunas envolvidas em junções, colunas usadas na cláusula WHERE e colunas encontradas em GROUP BY.

Saiba mais sobre [estatísticas].

## <a name="resource-class"></a>classe de recurso
O SQL Data Warehouse usa grupos de recursos como uma maneira de alocar memória para as consultas. Se você precisar de mais memória para melhorar a consulta ou a velocidade de carregamento, aloque classes de recursos superiores. Por outro lado, usar classes de recursos maiores afeta a simultaneidade. Você deseja considerar isso antes de mover todos os seus usuários para classes de recursos maiores.

Se você notar que as consultas demoram muito, verifique se seus usuários não são executados em classes de recursos grandes. Classes de recursos grandes consomem vários slots de simultaneidade. Eles podem fazer com que outras consultas sejam colocadas em fila.

Por fim, usar a camada otimizada de computação, cada classe de recursos obterá 2,5 vezes mais memória do que a camada otimizada elástica.

Saiba mais sobre como trabalhar com [classes de recursos e simultaneidade].

## <a name="lower-your-cost"></a>Reduzir seu custo
Um recurso importante do SQL Data Warehouse é a capacidade de pausar quando você não está usando-o, o que interrompe a cobrança dos recursos de computação. Outro recurso importante é a capacidade de dimensionar os recursos. A pausa e o dimensionamento podem ser feitos no Portal do Azure ou pelos comandos do PowerShell.

Dimensione automaticamente no momento que desejar com o Azure Functions:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

## <a name="optimize-your-architecture-for-performance"></a>Otimizar a arquitetura para desempenho

É recomendável considerar o Banco de Dados SQL e o Azure Analysis Services em uma arquitetura de Hub e Spoke. Essa solução pode fornecer isolamento de carga de trabalho entre diferentes grupos de usuários, além de usar recursos de segurança avançados do Banco de Dados SQL e do Azure Analysis Services. Essa também é uma maneira de fornecer simultaneidade ilimitada para seus usuários.

Saiba mais sobre [arquiteturas típicas que se beneficiam com SQL Data Warehouse](https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/common-isv-application-patterns-using-azure-sql-data-warehouse/).

Implante com um clique seus spokes nos Bancos de Dados SQL do SQL Data Warehouse:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwSpokeDbTemplate%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>


<!--Image references-->
[Esboço]:media/sql-data-warehouse-cheat-sheet/picture-flow.png

<!--Article references-->
[carregamento de dados]:./design-elt-data-loading.md
[deeper guidance]: ./guidance-for-loading-data.md
[Índices]:./sql-data-warehouse-tables-index.md
[partições]:./sql-data-warehouse-tables-partition.md
[estatísticas]:./sql-data-warehouse-tables-statistics.md
[classes de recursos e simultaneidade]:./sql-data-warehouse-develop-concurrency.md

<!--MSDN references-->


<!--Other Web references-->
[typical architectures that take advantage of SQL Data Warehouse]: https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/common-isv-application-patterns-using-azure-sql-data-warehouse/
[is and is not]:https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/azure-sql-data-warehouse-workload-patterns-and-anti-patterns/
[migração de dados]:https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/
[tabelas replicadas]:https://docs.microsoft.com/en-us/azure/sql-data-warehouse/design-guidance-for-replicated-tables
[tabelas distribuídas]:https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute
[Azure Data Lake Store]: https://docs.microsoft.com/en-us/azure/data-factory/connector-azure-data-lake-store
[sys.dm_pdw_nodes_db_partition_stats]: https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql
[sys.dm_pdw_request_steps]:https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql
