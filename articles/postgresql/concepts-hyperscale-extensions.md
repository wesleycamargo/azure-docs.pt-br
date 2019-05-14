---
title: Extensões de PostgreSQL no banco de dados do Azure para PostgreSQL – em hiperescala (Citus) (visualização)
description: Descreve a capacidade de estender a funcionalidade de seu banco de dados usando as extensões no Banco de Dados para PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 4022c95bfda8cbdaed75876793bfbba4254a5c54
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65410259"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---hyperscale-citus-preview"></a>Extensões de PostgreSQL no banco de dados do Azure para PostgreSQL – em hiperescala (Citus) (visualização)

O PostgreSQL fornece a capacidade de estender a funcionalidade de seu banco de dados usando as extensões. As extensões permitem o agrupamento de vários objetos SQL relacionados em um único pacote que pode ser carregado ou removido de seu banco de dados com um único comando. Depois de ser carregada no banco de dados, as extensões podem funcionar como recursos internos. Para obter mais informações sobre extensões do PostgreSQL, consulte  [Empacotando Objetos Relacionados em uma Extensão](https://www.postgresql.org/docs/9.6/static/extend-extensions.html).

## <a name="how-to-use-postgresql-extensions"></a>Como usar as extensões PostgreSQL

As extensões PostgreSQL devem ser instaladas no banco de dados para que você possa usá-las. Para instalar uma extensão específica, execute as [CREATE EXTENSION](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) comando na ferramenta psql para carregar os objetos empacotados em seu banco de dados.

No momento, o Banco de Dados do Azure para PostgreSQL dá suporte a um subconjunto de extensões de chave, como é listado abaixo. As extensões além das listadas não têm suporte, não é possível criar sua própria extensão com o serviço de Banco de Dados do Azure para PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Extensões com suporte do Banco de Dados do Azure para PostgreSQL

As tabelas a seguir listam as extensões PostgreSQL padrão que têm suporte atualmente do Banco de Dados do Azure para PostgreSQL. Essas informações também estão disponíveis por meio da execução de `SELECT * FROM pg_available_extensions;`.

### <a name="data-types-extensions"></a>Extensões de tipos de dados

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Fornece um tipo de cadeia de caracteres que não diferencia maiúsculas de minúsculas. |
> | [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Fornece um tipo de dados para cubos multidimensionais. |
> | [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Fornece um tipo de dados para armazenar conjuntos de pares chave-valor. |
> | [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | Fornece tipos de dados para padrões de numeração de produto internacionais. |
> | [lo](https://www.postgresql.org/docs/current/lo.html) | Manutenção de objeto grande. |
> | [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Fornece um tipo de dados para estruturas semelhantes a árvores hierárquicas. |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | Tipo de dados para representar os segmentos de linha ou intervalos de ponto flutuantes. |
> | [topn](https://github.com/citusdata/postgresql-topn/) | Tipo de JSONB n superior. |

### <a name="functions-extensions"></a>Extensões de funções

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Funções para campos de incremento automático. |
> | [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Fornece um meio para calcular as distância ortodrômicas na superfície da Terra. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Fornece várias funções para determinar semelhanças e a distância entre cadeias de caracteres. |
> | [insert\_username](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Funções para controlar quem alterou uma tabela. |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | Agregador de inteiro e enumerador (obsoleto). |
> | [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Fornece funções e operadores para manipular matrizes de inteiros sem nulos. |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Funções para controlar a hora da última modificação. |
> | [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Fornece funções de criptografia. |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Gerencia as tabelas de partição por hora ou ID. |
> | [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Fornece funções e operadores para determinar a semelhança de texto alfanumérico, com base na correspondência de trigram. |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Funções para implementar a integridade referencial (obsoleta). |
> | sessão\_analytics | Funções para consultar hstore matrizes. |
> | [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Fornece funções que manipulam tabelas inteiras, incluindo a tabela de referência cruzada. |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Disparado notificações de alteração. |
> | [timetravel](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Funções para a implementação de tempo de viagem. |
> | [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Gera UUIDs (identificadores exclusivos universais). |

### <a name="full-text-search-extensions"></a>Extensões de pesquisa de texto completo

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Fornece um modelo de dicionário de pesquisa de texto para números inteiros. |
> | [dict\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Modelo de dicionário de pesquisa de texto para o processamento de sinônimo estendido. |
> | [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Um dicionário de pesquisa de texto que remove acentos (sinais diacríticos) dos lexemas. |

### <a name="index-types-extensions"></a>Extensões de tipos de índice

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [bloom](https://www.postgresql.org/docs/current/bloom.html) | Método de acesso de bloom - arquivo de assinatura de índice com base em. |
> | [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Fornece classes de operador GIN de exemplo que implementam um comportamento como da árvore B para certos tipos de dados. |
> | [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Fornece classes de operador de índice GiST que implementam a árvore B. |

### <a name="language-extensions"></a>Extensões de linguagem

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | Linguagem de procedimento carregável PL/pgSQL. |

### <a name="hyperscale-extensions"></a>Extensões em hiperescala

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [citus](https://github.com/citusdata/citus) | Banco de dados distribuído Citus. |
> | fragmento\_rebalancer | Reequilibrar com segurança dados em um grupo de servidor no caso de adição de nó ou a remoção. |

### <a name="miscellaneous-extensions"></a>Extensões diversas

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | Funções administrativas para PostgreSQL. |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Funções de verificação de integridade da relação. |
> | [file\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | Wrapper de dados externos para acesso de arquivo simples. |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Inspecione o conteúdo das páginas de banco de dados em um nível baixo. |
> | [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Fornece um meio para examinar o que está acontecendo no cache do buffer compartilhado em tempo real. |
> | [pg\_cron](https://github.com/citusdata/pg_cron) | Agendador de trabalhos para PostgreSQL. |
> | [pg\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Examine o mapa de espaço livre (FSM). |
> | [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Fornece uma maneira para carregar dados de relação no cache de buffer. |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Fornece um meio para rastrear as estatísticas de execução de todas as instruções SQL executadas por um servidor. (Veja abaixo uma observação sobre essa extensão). |
> | [PG\_visibilidade](https://www.postgresql.org/docs/current/pgvisibility.html) | Examine o mapa de visibilidade (VM) e as informações de visibilidade de nível de página. |
> | [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Fornece um meio para mostrar informações de bloqueio de nível de linha. |
> | [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Fornece um meio para mostrar estatísticas em nível de tupla. |
> | [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Wrapper de dados externos usado para acessar dados armazenados em servidores PostgreSQL externos. (Veja abaixo uma observação sobre essa extensão).|
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Informações sobre certificados SSL. |
> | [tsm\_system\_rows](https://www.postgresql.org/docs/current/tsm-system-rows.html) | Método TABLESAMPLE que aceita o número de linhas que um limite. |
> | [tsm\_system\_time](https://www.postgresql.org/docs/current/tsm-system-time.html) | Método TABLESAMPLE que aceita o tempo em milissegundos como um limite. |
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | Fornece um meio de criação de índices hipotéticos que não gastam CPU ou disco. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Um módulo que suporta conexões com outros bancos de dados do PostgreSQL de dentro de uma sessão de banco de dados. (Veja abaixo uma observação sobre essa extensão). |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | Consulta de XPath e XSLT. |


### <a name="postgis-extensions"></a>Extensões PostGIS

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [PostGIS](https://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | Objetos espaciais e geográficos para PostgreSQL. |
> | address\_standardizer, address\_standardizer\_data\_us | Usado para analisar um endereço em elementos constituintes. Usado para oferecer suporte à etapa de normalização de endereços de geocodificação. |
> | postgis\_sfcgal | Funções de PostGIS SFCGAL. |
> | postgis\_tiger\_geocoder | PostGIS tiger geocoder e geocoder inversa. |
> | postgis\_topologia | Funções e tipos espaciais do PostGIS topologia. |


## <a name="pgstatstatements"></a>pg_stat_statements
A extensão [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) é pré-carregada em cada servidor do Banco de Dados do Azure para PostgreSQL para fornecer a você uma maneira de acompanhar estatísticas de execução de instruções SQL.
A configuração `pg_stat_statements.track`, que controla quais instruções são contadas por extensão, tem `top` como padrão, que significa que todas as instruções emitidas diretamente por clientes são rastreadas. Dois outros níveis de rastreamento são `none` e `all`. Essa definição é configurável como um parâmetro de servidor por meio de [portal do Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) ou da [CLI do Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Há um equilíbrio entre as informações de execução de consulta fornecida por pg_stat_statements e o impacto no desempenho do servidor, que registra cada instrução SQL. Se você não está usando ativamente a extensão pg_stat_statements, recomendamos que você defina `pg_stat_statements.track` como `none`. Observe que alguns serviços de monitoramento de terceiros podem depender de pg_stat_statements para fornecer informações de desempenho de consulta, para confirmar se este é o caso para você ou não.

## <a name="dblink-and-postgresfdw"></a>dblink e postgres_fdw
dblink e postgres_fdw permitem que você se conecte de um servidor PostgreSQL a outro ou a outro banco de dados no mesmo servidor. O servidor de recebimento precisa permitir conexões do servidor de envio por meio de seu firewall. Ao usar essas extensões para conectar-se entre os servidores do Banco de Dados do Azure para PostgreSQL, isso pode ser feito definindo "Permitir acesso aos serviços do Azure" como LIGADO. Isso também será necessário se você quiser usar as extensões para executar um loop no mesmo servidor. A configuração "Permitir acesso aos serviços do Azure" pode ser encontrada na página do portal do Azure para o servidor Postgres em Segurança de Conexão. Ativar "Permitir acesso a serviços do Azure" coloca todos os IPs do Azure na lista de permissões.

Atualmente, as conexões de saída do banco de dados do Azure para PostgreSQL não têm suporte, exceto para conexões de outro banco de dados do Azure para servidores PostgreSQL.
