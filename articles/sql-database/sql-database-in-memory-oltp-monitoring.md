---
title: "Monitorar o armazenamento na memória XTP | Microsoft Docs"
description: "Estimar e monitorar o uso do armazenamento na memória XTP, capacidade; resolver o erro de capacidade 41823"
services: sql-database
documentationcenter: 
author: jodebrui
manager: jhubbard
editor: 
ms.assetid: b617308e-692c-4938-8fa2-070034a3ecef
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: jodebrui
ms.openlocfilehash: 1e7088e80cc86e3c7cf8ae8ea180d797de613e71
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2018
---
# <a name="monitor-in-memory-oltp-storage"></a>Monitorar o armazenamento OLTP In-Memory
Ao usar o [In-Memory OLTP](sql-database-in-memory.md), os dados em tabelas com otimização de memória e as variáveis de tabela residem no armazenamento OLTP in-memory. Cada camada de serviço Premium tem um tamanho máximo de armazenamento OLTP In-Memory, que está documentado em [limites de recursos do banco de dados individual](sql-database-resource-limits.md#single-database-storage-sizes-and-performance-levels) e [limites de recursos do pool elástico](sql-database-resource-limits.md#elastic-pool-change-storage-size). Quando esse limite for excedido, as operações insert e update poderão começar a falhar com o erro 41823 para banco de dados independentes e erro 41840 para pools elásticos. Nesse ponto, você precisa excluir dados para obter memória ou atualizar a camada de desempenho do seu banco de dados.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Determinar se os dados se ajustam ao limite de armazenamento OLTP na memória
Determine os limites de armazenamento das diferentes camadas de serviço Premium. Consulte [limites de recursos do banco de dados individual](sql-database-resource-limits.md#single-database-storage-sizes-and-performance-levels) e [limites de recursos do pool elástico](sql-database-resource-limits.md#elastic-pool-change-storage-size).

A estimativa dos requisitos de memória para uma tabela com otimização de memória funciona no SQL Server da mesma forma como no Banco de Dados SQL do Azure. Reserve alguns minutos para examinar este artigo sobre [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

A tabela e as linhas de variável de tabela, bem como índices, contam para o tamanho máximo dos dados do usuário. Além disso, ALTER TABLE precisa de espaço suficiente para criar uma nova versão da tabela inteira e de seus índices.

## <a name="monitoring-and-alerting"></a>Monitoramento e alertas
Você pode monitorar o uso de armazenamento na memória como uma porcentagem do limite de armazenamento para sua camada de desempenho no [Portal do Azure](https://portal.azure.com/): 

1. Na folha Banco de Dados, localize a caixa de utilização Recurso e clique em Editar.
2. Selecione a métrica `In-Memory OLTP Storage percentage`.
3. Para adicionar um alerta, clique na caixa Utilização de Recursos para abrir a folha Métrica e clique em Adicionar alerta.

Ou use a consulta a seguir para mostrar a utilização de armazenamento na memória:

    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats


## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>Corrigir situações de armazenamento OLTP fora de memória - Erros 41823 e 41840
Atingir o limite de armazenamento OLTP na memória em seus resultados de banco de dados em operações INSERT, UPDATE, ALTER que falham com mensagens de erro 41823 (para bancos de dados autônomo) ou erro 41840 (para pools elásticos). Ambos os erros fazem com que a transação ativa seja anulada.

Mensagens de erro 41823 e 41840 indicam que as tabelas com otimização de memória e variáveis de tabela no banco de dados ou pool atingiu o tamanho máximo de armazenamento OLTP na memória.

Para resolver esse erro:

* Exclua os dados das tabelas com otimização de memória, potencialmente descarregando os dados em tabelas baseadas em disco tradicionais ou
* Atualize a camada de serviço para uma com armazenamento na memória suficiente para os dados que você precisa manter em tabelas com otimização de memória.

> [!NOTE] 
> Em casos raros, erros 41823 e 41840 podem ser transitórios, o que significa que há armazenamento suficiente OLTP na memória disponível e que repetir a operação será bem-sucedida. Portanto, é recomendável monitorar o armazenamento OLTP na memória global disponível e tentar novamente quando encontrar erro 41823 ou 41840 pela primeira vez. Para obter mais informações sobre a lógica de repetição, consulte [Detecção de conflito e lógica de repetição com OLTP na memória](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic).

## <a name="next-steps"></a>Próximas etapas
Para obter diretrizes sobre monitoramento, consulte [Monitoramento de Banco de Dados SQL do Azure usando exibições de gerenciamento dinâmico](sql-database-monitoring-with-dmvs.md).
