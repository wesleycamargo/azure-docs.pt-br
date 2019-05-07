---
title: Dados distribuídos no banco de dados do Azure para PostgreSQL – em hiperescala (Citus) (visualização)
description: As tabelas e fragmentos distribuídos no grupo de servidores.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 9020ee690d93a1b477471fac4a482a909fca5935
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077329"
---
# <a name="distributed-data-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Dados distribuídos no banco de dados do Azure para PostgreSQL – em hiperescala (Citus) (visualização)

Este artigo descreve os tipos de tabela de três em hiperescala (Citus).
Ele mostra tabelas distribuídas como são armazenadas como fragmentos e a maneira de fragmentos são colocados em nós.

## <a name="table-types"></a>Tipos de tabela

Há três tipos de tabelas em um grupo de servidores em hiperescala, cada um usado para finalidades diferentes.

### <a name="type-1-distributed-tables"></a>Tipo 1: tabelas distribuídas

O primeiro tipo e mais comum, é *distribuída* tabelas. Eles parecem estar tabelas normais para instruções SQL, mas são horizontalmente *particionada* em nós de trabalho. Isso significa que as linhas da tabela são armazenadas em nós diferentes, nas tabelas de fragmento chamadas *fragmentos*.

Em hiperescala é executado não apenas SQL, mas as instruções de DDL ao longo de um cluster, portanto, alterar o esquema de uma tabela distribuída propaga-se para atualizar os fragmentos de todos os da tabela entre os trabalhadores.

#### <a name="distribution-column"></a>Coluna de distribuição

Hiperescala usa a fragmentação algorítmica para atribuir linhas a fragmentos. A atribuição é tomada determinística, com base no valor de uma coluna de tabela chamado o *coluna de distribuição.* O administrador de cluster deve designar a esta coluna ao distribuir uma tabela.
Fazer a escolha certa é importante para o desempenho e a funcionalidade.

### <a name="type-2-reference-tables"></a>Tipo 2: tabelas de referência

Uma tabela de referência é um tipo de tabela distribuída cujo conteúdo inteiro está concentrado em um único fragmento. O fragmento é replicado em todos os trabalhadores para que consultas em qualquer trabalhador podem acessar localmente, as informações de referência sem a sobrecarga de rede de solicitando linhas de outro nó. Tabelas de referência não tem nenhuma coluna de distribuição porque não é necessário para distinguir os fragmentos separados por linha.

Tabelas de referência são normalmente pequenas e são usadas para armazenar dados que são relevantes para a execução de consultas em qualquer nó de trabalho. Por exemplo, enumerado valores, como o status da ordem ou categorias de produtos.

### <a name="type-3-local-tables"></a>Tipo 3: tabelas locais

Quando você usa em hiperescala, o nó coordenador que você se conectar ao é um banco de dados PostgreSQL regular. Você pode criar tabelas comuns sobre o coordenador e optar por não fragmento-los.

Um bom candidato para tabelas locais seria tabelas administrativas pequenas que não participam de consultas de junção. Por exemplo, uma tabela de usuários para autenticação e logon do aplicativo.

## <a name="shards"></a>Fragmentos

A seção anterior descritas tabelas distribuídas como são armazenadas como fragmentos em nós de trabalho. Esta seção obtém mais nos detalhes técnicos.

O `pg_dist_shard` tabela de metadados sobre o coordenador contém uma linha para cada fragmento de cada tabela distribuída no sistema. A linha corresponde a uma ID de fragmento com um intervalo de inteiros em um espaço de hash (shardminvalue, shardmaxvalue):

```sql
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue 
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

Se o nó coordenador quer determinar qual fragmento contém uma linha de `github_events`, ele o valor da coluna de distribuição na linha de hashes e verifica quais fragmentos\'intervalo s contém o valor de hash. (Os intervalos são definidos para que a imagem da função de hash é seu union não contíguo).

### <a name="shard-placements"></a>Posicionamentos de fragmento

Suponha que esse fragmento 102027 está associado com a linha em questão. A linha será ser lidos ou gravada em uma tabela chamada `github_events_102027` em um dos trabalhadores. Qual trabalhador? Que é determinado inteiramente pelas tabelas de metadados e o mapeamento de fragmento para o trabalho é conhecido como o fragmento *posicionamento*.

O nó coordenador reescreve as consultas em fragmentos que fazem referência as tabelas específicas, como `github_events_102027`, e executa esses fragmentos os trabalhadores apropriado. Aqui está um exemplo de uma consulta executada em segundo plano para localizar o nó que contém o fragmento 102027 ID.

```sql
SELECT
    shardid,
    node.nodename,
    node.nodeport
FROM pg_dist_placement placement
JOIN pg_dist_node node
  ON placement.groupid = node.groupid
 AND node.noderole = 'primary'::noderole
WHERE shardid = 102027;
```

    ┌─────────┬───────────┬──────────┐
    │ shardid │ nodename  │ nodeport │
    ├─────────┼───────────┼──────────┤
    │  102027 │ localhost │     5433 │
    └─────────┴───────────┴──────────┘

## <a name="next-steps"></a>Próximas etapas
- Saiba como [escolher uma coluna de distribuição](concepts-hyperscale-choose-distribution-column.md) para tabelas distribuídas
