---
title: Otimizar o vácuo automático no Banco de Dados do Azure para PostgreSQL
description: Este artigo descreve como você pode otimizar o vácuo automático no Banco de Dados do Azure para PostgreSQL.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: 21ac48ff473dcf494f96f87210bdfe09e4d82646
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55103387"
---
# <a name="optimizing-autovacuum-on-azure-database-for-postgresql-server"></a>Otimizar o vácuo automático no Banco de Dados do Azure para PostgreSQL 
Este artigo descreve como você pode otimizar o vácuo automático de modo eficaz no Banco de Dados do Azure para PostgreSQL.

## <a name="overview-of-autovacuum"></a>Visão geral do vácuo automático
O PostgreSQL usa MVCC para permitir maior simultaneidade de banco de dados. Cada atualização resulta em uma inserção e exclusão, sendo que cada exclusão resulta em linhas sendo marcadas temporariamente para exclusão. A marcação temporária resulta na identificação de tuplas inativas que posteriormente serão limpas. O PostgreSQL alcança tudo isso executando um trabalho de vácuo.

Um trabalho de vácuo pode ser disparado manualmente ou automaticamente. Quando o banco de dados estiver passando por uma atualização pesada ou operações de exclusão, o número de tuplas inativas será maior. Quando o banco de dados estiver ocioso, esse número será menor.  A necessidade de executar vácuo com mais frequência é maior quando o banco de dados está sob carga pesada. Isso torna a execução **manual** de trabalhos de vácuo pouco interessante.

O vácuo automático pode ser configurado e se beneficia do ajuste. Os valores padrão que acompanham o PostgreSQL tentam garantir que o produto funcione em todos os tipos de dispositivos, incluindo Raspberry Pis, e os valores de configuração ideal dependem de vários fatores:
- Total de recursos disponíveis – tamanho do armazenamento e da SKU.
- Uso de recursos.
- Características de objetos individuais.

## <a name="autovacuum-benefits"></a>Benefícios do vácuo automático
Se você não executar o vácuo de tempos em tempos, as tuplas inativas que se acumularem poderão resultar em:
- Sobrecarga de dados – bancos de dados e tabelas maiores.
- Maiores índices de qualidade inferior.
- Aumento de E/S.

## <a name="monitoring-bloat-with-autovacuum-queries"></a>Monitorar a sobrecarga com consultas do vácuo automático
O exemplo de consulta a seguir foi projetado para identificar o número de tuplas inativas e ativas em uma tabela chamada "XYZ": 'SELECT relname, n_dead_tup, n_live_tup, (n_dead_tup/ n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables WHERE relname = 'XYZ' order by n_dead_tup DESC;'

## <a name="autovacuum-configurations"></a>Configurações de vácuo automático
Os parâmetros de configuração que controlam o vácuo automático giram em torno de duas perguntas importantes:
- Quando ele deve começar?
- Quanto ele limpa depois de ter começado?

Abaixo estão alguns dos parâmetros de configuração de vácuo automático que você pode atualizar com base nas perguntas acima, juntamente com algumas diretrizes:
Parâmetro|DESCRIÇÃO|Valor padrão
---|---|---
autovacuum_vacuum_threshold|Especifica o número mínimo de tuplas atualizadas ou excluídas necessárias para disparar um VACUUM em qualquer tabela. O padrão é 50 tuplas. Este parâmetro só pode ser definido no arquivo postgresql.conf ou na linha de comando do servidor. A configuração pode ser substituída por tabelas individuais alterando-se os parâmetros de armazenamento de tabela.|50
autovacuum_vacuum_scale_factor|Especifica uma fração do tamanho da tabela para adicionar a autovacuum_vacuum_threshold ao decidir se deseja disparar um VACUUM. O padrão é 0,2 (20% do tamanho da tabela). Este parâmetro só pode ser definido no arquivo postgresql.conf ou na linha de comando do servidor. A configuração pode ser substituída por tabelas individuais alterando-se os parâmetros de armazenamento de tabela.|5%
autovacuum_vacuum_cost_limit|Especifica o valor de limite de custo que será usado em operações de VACUUM automáticas. Se -1 for especificado (o que é o padrão), o valor normal de vacuum_cost_limit será usado. O valor é distribuído proporcionalmente entre os trabalhos de vácuo automático em execução se há mais de um trabalho. A soma dos limites para cada trabalho não excede o valor dessa variável. Este parâmetro só pode ser definido no arquivo postgresql.conf ou na linha de comando do servidor. A configuração pode ser substituída por tabelas individuais alterando-se os parâmetros de armazenamento de tabela.|-1
autovacuum_vacuum_cost_delay|Especifica o valor de atraso de custo que será usado em operações de VACUUM automáticas. Se -1 for especificado, o valor normal de vacuum_cost_delay será usado. O valor padrão é 20 milissegundos. Este parâmetro só pode ser definido no arquivo postgresql.conf ou na linha de comando do servidor. A configuração pode ser substituída por tabelas individuais alterando-se os parâmetros de armazenamento de tabela.|20 ms
autovacuum_nap_time|Especifica o atraso mínimo entre execuções do vácuo automático em qualquer determinado banco de dados. Em cada turno, o daemon examina o banco de dados e emite comandos VACUUM e ANALYZE conforme necessário para as tabelas nesse banco de dados. O atraso é medido em segundos, sendo que o padrão é um minuto (1 minuto). Este parâmetro só pode ser definido no arquivo postgresql.conf ou na linha de comando do servidor.|15 s
autovacuum_max_workers|Especifica o número máximo de processos de vácuo automático (que não seja o inicializador do vácuo automático) que podem estar em execução simultaneamente. O padrão é três. Esse parâmetro só pode ser definido na inicialização do servidor.|3
As configurações acima podem ser substituídas por tabelas individuais alterando-se os parâmetros de armazenamento de tabela.  

## <a name="autovacuum-cost"></a>Custo do vácuo automático
Abaixo estão os "custos" de executar uma operação de vácuo:
- Um bloqueio é estabelecido nas páginas de dados em que o vácuo é executado.
- A computação e a memória são usadas quando o vácuo está em execução.

Isso implica que vácuo não deve executar com muita frequência nem com frequência baixa demais, ele precisa ser adaptável à carga de trabalho. É recomendável testar todas as alterações de parâmetro do vácuo automático por causa de vantagens e desvantagens de cada um deles.

## <a name="autovacuum-start-trigger"></a>Gatilho de início do vácuo automático
O vácuo automático é disparado quando o número de tuplas inativas excede autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * reltuples, em que reltuples é uma constante.

A limpeza do vácuo automático precisa acompanhar a carga do banco de dados, caso contrário, você pode ficar sem armazenamento e enfrentar uma lentidão de caráter geral nas consultas. Amortizada ao longo do tempo, a taxa na qual o vácuo limpa tuplas inativas deve ser igual à taxa segundo a qual as tuplas inativas são criadas.

Bancos de dados com muitas atualizações/exclusões têm mais tuplas inativas e requerem mais espaço. Geralmente, os bancos de dados com várias atualizações/exclusões se beneficiam de valores baixos de autovacuum_vacuum_scale_factor e valores baixos de autovacuum_vacuum_threshold para impedir o acúmulo prolongado de tuplas inativas. Você pode usar valores mais altos para ambos os parâmetros com bancos de dados menores, porque a necessidade de vácuo é menos urgente. Lembrando que a execução frequente de vácuo ocorre ao custo de computação e de memória.

O fator de escala padrão de 20% funciona bem em tabelas com um percentual baixo de tuplas inativas, mas não em tabelas com um percentual alto de tuplas inativas. Por exemplo, em uma tabela de 20 GB, isso se traduz em 4 GB de tuplas inativas, enquanto em uma tabela de 1 TB, significa 200 GB de tuplas inativas.

Com o PostgreSQL, você pode definir esses parâmetros no nível de tabela ou no nível de instância. Atualmente, esses parâmetros podem ser definidos no nível de tabela apenas no Banco de Dados do Azure para PostgreSQL.

## <a name="estimating-the-cost-of-autovacuum"></a>Estimar o custo do vácuo automático
Executar o vácuo automático é "caro" e há parâmetros para controlar o tempo de execução de operações de vácuo. Os parâmetros a seguir ajudam a estimar o custo da execução de vácuo:
- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

O processo de vácuo lê páginas físicas e verifica em busca de tuplas inativas. Cada página em shared_buffers é considerada como tendo um custo igual a 1 (vacuum_cost_page_hit), todas as outras páginas são consideradas como tendo um custo de 20 (vacuum_cost_page_dirty) se há tuplas inativas ou 10 (vacuum_cost_page_miss) se não há nenhuma tupla inativa. A operação de vácuo é interrompida quando o processo excede autovacuum_vacuum_cost_limit.  

Depois que o limite é atingido, o processo fica suspenso pela duração especificada pelo parâmetro autovacuum_vacuum_cost_delay antes de ser iniciado novamente. Se o limite não for atingido, o vácuo automático começará após o valor especificado pelo parâmetro autovacuum_nap_time.

Em resumo, os parâmetros autovacuum_vacuum_cost_delay e autovacuum_vacuum_cost_limit controlam quanta limpeza de dados é permitida por tempo de unidade. Observe que os valores padrão são muito baixos para a maioria dos tipos de preço. Os valores ideais para esses parâmetros são dependentes do tipo de preço e devem ser configurados adequadamente.

O parâmetro autovacuum_max_workers determina o número máximo de processos de vácuo automático que podem ser executados simultaneamente.

Com o PostgreSQL, você pode definir esses parâmetros no nível de tabela ou no nível de instância. Atualmente, esses parâmetros podem ser definidos no nível de tabela apenas no Banco de Dados do Azure para PostgreSQL.

## <a name="optimizing-autovacuum-per-table"></a>Otimizando o vácuo automático por tabela
Todos os parâmetros de configuração acima podem ser configurados por tabela, por exemplo:
```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

O vácuo automático é um processo síncrono e por tabela. Quanto maior o percentual de tuplas inativas de uma tabela, maior o "custo" para vácuo automático.  Dividir tabelas que tenham uma alta taxa de atualizações/exclusões em várias tabelas ajudará a paralelizar o vácuo automático e reduzirá o "custo" para concluir o vácuo automático em uma tabela. Você também pode aumentar o número de trabalhos de vácuo automático paralelos para garantir que os trabalhos sejam agendados livremente.

## <a name="next-steps"></a>Próximas etapas
Revise a documentação do PostgreSQL a seguir para saber mais sobre como usar e ajustar o vácuo automático:
 - Documentação do PostgreSQL – [Capítulo 18, Configuração do servidor](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html)
 - Documentação do PostgreSQL – [Capítulo 24, Tarefas rotineiras de manutenção de banco de dados](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html)
