---
title: "Usar as extensões de PostgreSQL no Banco de Dados do Azure para PostgreSQL | Microsoft Docs"
description: "Descreve a capacidade de estender a funcionalidade de seu banco de dados usando as extensões no Banco de Dados para PostgreSQL."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql - database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: dd16442078cd1d440edf0a16cba32b1494c6db55
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---
# <a name="postgresql-extensions-in-azure-database-for-postgresql"></a>Extensões de PostgreSQL no Banco de Dados do Azure para PostgreSQL
O PostgreSQL fornece a capacidade de estender a funcionalidade de seu banco de dados usando as extensões. As extensões permitem o agrupamento de vários objetos SQL relacionadas em um único pacote, e podem ser carregadas ou removidas de seu banco de dados com um único comando. Após o carregamento no banco de dados, as extensões podem funcionar como recursos incorporados. Para saber mais sobre extensões PostgreSQL, consulte [Empacotar objetos relacionados em uma extensão](https://www.postgresql.org/docs/9.6/static/extend-extensions.html).

## <a name="how-to-use-postgresql-extensions"></a>Como usar as extensões PostgreSQL?
As extensões PostgreSQL precisam ser instalados no banco de dados antes de você poder usá-las. Para instalar uma extensão específica, execute um comando simples [CREATE EXTENSION](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) na ferramenta psql para carregar os objetos empacotados em seu banco de dados.

O Banco de Dados do Azure para PostgreSQL oferece suporte a um subconjunto de extensões importantes, conforme listado abaixo, e você só pode instalá-las em seu banco de dados. Não é possível criar sua própria extensão com o Banco de Dados do Azure para o serviço PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Extensões com suporte do Banco de Dados do Azure para PostgreSQL
Veja a seguir uma lista de extensões PostgreSQL padrão que têm suporte atualmente do Banco de Dados para PostgreSQL. Você também pode obter essas informações consultando pg\_available\_extensions. 

### <a name="data-types-extensions"></a>Extensões de tipos de dados
| **Extensão** | **Descrição** |
|------------------------------------------------------------------|--------------------------------------------------------|
| [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Fornece um tipo de cadeia de caracteres que diferencia maiúsculas de minúsculas |
| [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Fornece o tipo de dados para armazenar conjuntos de pares de chave/valor |

### <a name="functions-extensions"></a>Extensões de funções
| **Extensão** | **Descrição** |
|--------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------|
| [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Fornece várias funções para determinar semelhanças e a distância entre cadeias de caracteres. |
| [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Fornece funções e operadores para manipular matrizes de inteiros sem nulos. |
| [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Fornece funções de criptografia |
| [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Gerencia as tabelas particionadas por hora ou ID |
| [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Fornece funções e operadores para determinar a semelhança de texto alfanumérico, com base na correspondência de trigram |
| [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Gerar identificadores universais exclusivos (UUIDs) |

### <a name="full-text-search-extensions"></a>Extensões de pesquisa de texto completo
| **Extensão** | **Descrição** |
|----------------------------------------------------------------------|-------------------------------------------------------------------------------|
| [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Um dicionário de pesquisa de texto que remove acentos (sinais diacríticos) dos lexemas. |

### <a name="index-types-extensions"></a>Extensões de tipos de índice
| **Extensão** | **Descrição** |
|---------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------|
| [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Fornece classes de operador GIN de exemplo que implementam um comportamento como da árvore B para certos tipos de dados. |
| [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Fornece classes de operador de índice GiST que implementam a árvore B. |

### <a name="language-extensions"></a>Extensões de linguagem
| **Extensão** | **Descrição** |
|--------------------------------------------------------------------|---------------------------------------|
| [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | Linguagem de procedimento carregável PL/pgSQL |

### <a name="miscellaneous-extensions"></a>Extensões diversas
| **Extensão** | **Descrição** |
|------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------|
| [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Fornece um meio para examinar o que está acontecendo no cache do buffer compartilhado em tempo real. |
| [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Fornece uma maneira para carregar dados de relação no cache de buffer. |
| [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Fornece um meio para rastrear as estatísticas de execução de todas as instruções SQL executadas por um servidor. |
| [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Wrapper de dados externos usado para acessar dados armazenados em servidores PostgreSQL externos |

### <a name="postgis"></a>PostGIS
| **Extensão** | **Descrição** |
|--------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------|
| [PostGIS](http://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | Objetos espaciais e geográficos para PostgreSQL. |
| address\_standardizer, address\_standardizer\_data\_us | Usado para analisar um endereço em elementos constituintes. Geralmente usado para oferecer suporte a etapa de normalização de endereços de geocodificação. |
| [pgrouting](http://pgrouting.org/) | Estende o banco de dados geoespacial PostGIS/PostgreSQL para fornecer a funcionalidade de roteamento geoespacial. |

## <a name="next-steps"></a>Próximas etapas
Não vê uma extensão que você queira usar? Fale conosco. Vote em solicitações existentes ou crie novos comentários e desejos em nosso [Fórum de comentários do cliente](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
