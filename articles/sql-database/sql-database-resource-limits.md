---
title: Visão geral dos Limites de recursos do Banco de Dados SQL do Azure | Microsoft Docs
description: Esta página descreve alguns limites de recursos baseados em DTU comuns para o Banco de Dados SQL do Microsoft Azure.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: carlrab
ms.openlocfilehash: 3b05f553e591de2660e9842f316de0cb6f80c852
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2018
ms.locfileid: "42141861"
---
# <a name="overview-azure-sql-database-resource-limits"></a>Visão geral dos Limites de recursos do Banco de Dados SQL do Azure 

Este artigo fornece uma visão geral do recurso de banco de dados SQL limita e fornece informações sobre o que acontece quando os limites de recurso são atingidos ou excedidos.

## <a name="what-is-the-maximum-number-of-servers-and-databases"></a>Qual é o número máximo de servidores e bancos de dados?

| Máximo | Servidor lógico | Instância gerenciada |
| :--- | :--- | :--- |
| Bancos de dados por servidor/instância | 5.000 | 100 |
| Número padrão de servidores por assinatura por região | 20 | N/D |
| Número máximo padrão de servidores por assinatura por região | 200 | N/D | 
| DTU / cota de eDTU por servidor | 54.000 | N/D |  
| Cota de vCore por servidor/instância | 540 | 80 |
| Pools de máx por servidor | Limitado pelo número de DTUs ou vCores | N/D |
||||

> [!NOTE]
> Para obter mais cotas DTU / eDTU, vCore ou mais servidores do que o valor padrão, uma nova solicitação de suporte pode ser enviada no portal do Azure para a assinatura com o tipo de problema "Cota". O DTU / eDTU limite de cota e o banco de dados por servidor restringe o número de pools Elásticos por servidor. 

> [!IMPORTANT]
> Conforme o número de bancos de dados vai se aproximando do limite por servidor, pode ocorrer o seguinte:
> - Aumento de latência nas consultas em execução no banco de dados mestre.  Isso inclui modos de exibição de estatísticas de utilização de recursos, como sys.resource_stats.
> - Aumento de latência nas operações de gerenciamento e pontos de vista do portais de renderização que envolvem a enumeração de bancos de dados no servidor.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>O que acontece quando os limites de recursos do banco de dados forem atingidos?

### <a name="compute-dtus-and-edtus--vcores"></a>Computação (DTUs e eDTUs/ vCores)

Quando a utilização de computação de banco de dados (medida por DTUs e eDTUs ou vCores) torna-se alta, a latência da consulta aumenta e pode até mesmo atingir o tempo limite. Sob essas condições, as consultas poderão ser colocadas em fila pelo serviço e receberão recursos para execução à medida que os recursos forem liberados.
Ao encontrar uma utilização alta de computação, as opções de atenuação incluem:

- Aumentar o nível de desempenho do banco de dados ou do pool elástico a fim de fornecer ao banco de dados mais recursos de computação. Consulte [limites de recursos do banco de dados individual em escala](sql-database-single-database-scale.md) e [limites de recursos do pool elástico](sql-database-elastic-pool-scale.md).
- Otimizar consultas para reduzir a utilização de recursos de cada consulta. Para saber mais, consulte [Ajuste/Dicas de consulta](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Armazenamento

Quando o espaço usado do banco de dados atingir o limite de tamanho máximo, inserções e atualizações do banco de dados que aumentam o tamanho dos dados falharão, e os clientes receberão uma [mensagem de erro](sql-database-develop-error-messages.md). Os SELECTS e DELETES do banco de dados continuarão a ter êxito.

Ao encontrar uma utilização alta de espaço, as opções de atenuação incluem:

- Aumentar o tamanho máximo do banco de dados ou pool elástico ou adicione mais armazenamento. Consulte [limites de recursos do banco de dados individual em escala](sql-database-single-database-scale.md) e [limites de recursos do pool elástico](sql-database-elastic-pool-scale.md).
- Se o banco de dados estiver em um pool elástico, o banco de dados poderá ser movido para fora do pool, de modo que seu espaço de armazenamento não seja compartilhado com outros bancos de dados.
- Encolher um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [Gerenciar espaço no arquivo no banco de dados SQL do Azure](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>Sessões e trabalhos (solicitações) 

O número máximo de sessões e trabalhos é determinado pelo nível de desempenho e camada de serviço (DTUs e eDTUs). Novas solicitações serão rejeitadas quando os limites de sessão ou de trabalho forem atingidos, e os clientes receberão uma mensagem de erro. Embora o número de conexões disponíveis possa ser controlado pelo aplicativo, o número de trabalhos simultâneos costuma ser mais difícil de ser estimado e controlado. Isso é especialmente verdadeiro durante os períodos de pico de carga, quando os limites de recurso de banco de dados são atingidos e os trabalhos acumulam devido a consultas de execução mais longas. 

Ao encontrar uma utilização alta de sessão ou trabalho, as opções de atenuação incluem:
- Aumentar a camada de serviço ou o nível de desempenho do banco de dados ou do pool elástico. Consulte [limites de recursos do banco de dados individual em escala](sql-database-single-database-scale.md) e [limites de recursos do pool elástico](sql-database-elastic-pool-scale.md).
- Otimizar consultas para reduzir a utilização de recursos de cada consulta se a causa do aumento da utilização de trabalho for devido à contenção de recursos de computação. Para saber mais, consulte [Ajuste/Dicas de consulta](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="next-steps"></a>Próximas etapas

- Consulte [Perguntas frequentes sobre Banco de Dados SQL](sql-database-faq.md) para obter respostas para as perguntas frequentes.
- Para saber mais sobre limites gerais do Azure, confira [Assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md).
- Para saber mais sobre DTUs e eDTUs, confira [DTUs e eDTUs](sql-database-service-tiers.md#what-are-database-transaction-units-dtus).
- Para obter informações sobre limites de tamanho de tempdb, consulte https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database.
