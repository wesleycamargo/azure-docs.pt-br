---
title: Limites de recursos do Banco de Dados SQL do Azure - servidor lógico| Microsoft Docs
description: Este artigo fornece uma visão geral dos limites de recursos do Banco de Dados SQL do Azure para bancos de dados individuais e bancos de dados em pool usando pools elásticos. Também fornece informações sobre o que acontece quando esses limites de recursos são atingidos ou excedidos.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: sashan,moslake
manager: craigg
ms.date: 09/19/2018
ms.openlocfilehash: b48c090cc67d4557140b5734f1a5e1f763b271ab
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2018
ms.locfileid: "48829556"
---
# <a name="sql-database-resource-limits-for-single-and-pooled-databases-on-a-logical-server"></a>Limites de recursos do Banco de Dados SQL para bancos de dados individuais e em pool em um servidor lógico 

Este artigo fornece uma visão geral dos limites de recursos do Banco de Dados SQL para bancos de dados individuais e em pool em um servidor lógico. Também fornece informações sobre o que acontece quando esses limites de recursos são atingidos ou excedidos.

> [!NOTE]
> Para limites de Instância Gerenciada, consulte [Limites de recursos do Banco de Dados SQL para instâncias gerenciadas](sql-database-managed-instance-resource-limits.md).

## <a name="maximum-resource-limits"></a>Limites máximos de recursos

| Recurso | Limite |
| :--- | :--- |
| Bancos de dados por servidor | 5.000 |
| Número padrão de servidores por assinatura por região | 20 |
| Número máximo padrão de servidores por assinatura por região | 200 |  
| DTU / cota de eDTU por servidor | 54.000 |  
| Cota de vCore por servidor/instância | 540 |
| Pools de máx por servidor | Limitado pelo número de DTUs ou vCores |
||||

> [!NOTE]
> Para obter mais cotas DTU / eDTU, vCore ou mais servidores do que o valor padrão, uma nova solicitação de suporte pode ser enviada no portal do Azure para a assinatura com o tipo de problema "Cota". O DTU / eDTU limite de cota e o banco de dados por servidor restringe o número de pools Elásticos por servidor. 

> [!IMPORTANT]
> Como o número de bancos de dados aproxima-se do limite por servidor lógico, pode ocorrer o seguinte:
> - Aumento de latência nas consultas em execução no banco de dados mestre.  Isso inclui modos de exibição de estatísticas de utilização de recursos, como sys.resource_stats.
> - Aumento de latência nas operações de gerenciamento e pontos de vista do portais de renderização que envolvem a enumeração de bancos de dados no servidor.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>O que acontece quando os limites de recursos do banco de dados forem atingidos?

### <a name="compute-dtus-and-edtus--vcores"></a>Computação (DTUs e eDTUs/ vCores)

Quando a utilização de computação de banco de dados (medida por DTUs e eDTUs ou vCores) torna-se alta, a latência da consulta aumenta e pode até mesmo atingir o tempo limite. Sob essas condições, as consultas poderão ser colocadas em fila pelo serviço e receberão recursos para execução à medida que os recursos forem liberados.
Ao encontrar uma utilização alta de computação, as opções de atenuação incluem:

- Aumentar o tamanho de computação do banco de dados ou do pool elástico a fim de fornecer ao banco de dados mais recursos de computação. Consulte [limites de recursos do banco de dados individual em escala](sql-database-single-database-scale.md) e [limites de recursos do pool elástico](sql-database-elastic-pool-scale.md).
- Otimizar consultas para reduzir a utilização de recursos de cada consulta. Para saber mais, consulte [Ajuste/Dicas de consulta](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Armazenamento

Quando o espaço usado do banco de dados atingir o limite de tamanho máximo, inserções e atualizações do banco de dados que aumentam o tamanho dos dados falharão, e os clientes receberão uma [mensagem de erro](sql-database-develop-error-messages.md). Os SELECTS e DELETES do banco de dados continuarão a ter êxito.

Ao encontrar uma utilização alta de espaço, as opções de atenuação incluem:

- Aumentar o tamanho máximo do banco de dados ou pool elástico ou adicione mais armazenamento. Consulte [limites de recursos do banco de dados individual em escala](sql-database-single-database-scale.md) e [limites de recursos do pool elástico](sql-database-elastic-pool-scale.md).
- Se o banco de dados estiver em um pool elástico, o banco de dados poderá ser movido para fora do pool, de modo que seu espaço de armazenamento não seja compartilhado com outros bancos de dados.
- Encolher um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [Gerenciar espaço no arquivo no banco de dados SQL do Azure](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>Sessões e trabalhos (solicitações) 

O número máximo de sessões e trabalhos é determinado pela camada de serviço e tamanho de computação (DTUs e eDTUs). Novas solicitações serão rejeitadas quando os limites de sessão ou de trabalho forem atingidos, e os clientes receberão uma mensagem de erro. Embora o número de conexões disponíveis possa ser controlado pelo aplicativo, o número de trabalhos simultâneos costuma ser mais difícil de ser estimado e controlado. Isso é especialmente verdadeiro durante os períodos de pico de carga, quando os limites de recurso de banco de dados são atingidos e os trabalhos acumulam devido a consultas de execução mais longas. 

Ao encontrar uma utilização alta de sessão ou trabalho, as opções de atenuação incluem:
- Aumentar a camada de serviço ou o tamanho de computação do banco de dados ou do pool elástico. Consulte [limites de recursos do banco de dados individual em escala](sql-database-single-database-scale.md) e [limites de recursos do pool elástico](sql-database-elastic-pool-scale.md).
- Otimizar consultas para reduzir a utilização de recursos de cada consulta se a causa do aumento da utilização de trabalho for devido à contenção de recursos de computação. Para saber mais, consulte [Ajuste/Dicas de consulta](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="next-steps"></a>Próximas etapas

- Consulte [Perguntas frequentes sobre Banco de Dados SQL](sql-database-faq.md) para obter respostas para as perguntas frequentes.
- Para saber mais sobre limites gerais do Azure, confira [Assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md).
- Para saber mais sobre DTUs e eDTUs, confira [DTUs e eDTUs](sql-database-service-tiers.md#dtu-based-purchasing-model).
- Para obter informações sobre limites de tamanho de tempdb, consulte [TempDB no Banco de Dados SQL do Azure](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
