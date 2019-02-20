---
title: Notas sobre a versão do SQL Data Warehouse do Azure | Microsoft Docs
description: Notas de versão do SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: mlee3gsd
ms.author: anumjs
ms.reviewer: jrasnick
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/09/2019
ms.openlocfilehash: 2ac60287c9d92ab6230e1dd6777504036e54492d
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56244667"
---
# <a name="azure-sql-data-warehouse-release-notes-and-documentation-updates"></a>Atualizações da documentação e das notas de versão do Azure SQL Data Warehouse

O SQL DW (SQL Data Warehouse) do Azure é um Enterprise Data Warehouse baseado em nuvem que aproveita o MPP (Processamento Paralelo Maciço) para executar rapidamente consultas complexas em petabytes de dados. Use o SQL Data Warehouse como um componente fundamental de uma solução de big data. Importe big data para o SQL Data Warehouse com consultas T-SQL simples do PolyBase e, em seguida, use o poder do MPP para executar a análise de alto desempenho. À medida que você integrar e analisar, o data warehouse se tornará a única versão da verdade com que sua empresa poderá contar para obter informações.

Clique nos links abaixo para saber mais sobre os novos recursos e aprimoramentos que você pode esperar na última versão do SQL Data Warehouse do Azure. Você pode esperar receber essas atualizações de serviço durante a programação de manutenção identificada.

- [Versão 10.0.10106.0 do SQL Data Warehouse (janeiro)](./release-notes-10-0-10106-0.md)
- [Dezembro de 2018](./release-notes-december-2018.md)
- [Outubro de 2018](./release-notes-october-2018.md)
- [Setembro de 2018](./release-notes-september-2018.md)
- [Agosto de 2018](./release-notes-august-2018.md)
- [Julho de 2018](./release-notes-july-2018.md)
- [Junho de 2018](./release-notes-june-2018.md)
- [Maio de 2018](./release-notes-may-2018.md)

## <a name="checking-the-code-version-that-has-been-applied-to-your-data-warehouse"></a>Verificar a versão do código que foi aplicada ao data warehouse

Para confirmar qual versão foi aplicada ao data warehouse. Conecte-se ao data warehouse por meio do SSMS e execute a seguinte sintaxe para retornar a versão atual do SQL Data Warehouse.

```sql
SELECT @@VERSION AS 'SQL Data Warehouse';
```

Saída de exemplo: ![Versão do SQL Data Warehouse](./media/release-notes/dw-version.png)

Use a versão identificada para confirmar qual versão foi aplicada ao SQL Data Warehouse do Azure. 


## <a name="next-steps"></a>Próximas etapas
- [Saiba mais](https://docs.microsoft.com/azure/sql-data-warehouse/viewing-maintenance-schedule) sobre como exibir um agendamento de manutenção. 
- [Saiba mais](https://docs.microsoft.com/azure/sql-data-warehouse/changing-maintenance-schedule) sobre como alterar um agendamento de manutenção.
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/alert-metric) sobre como criar, exibir e gerenciar alertas usando o Azure Monitor.
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) sobre ações de webhook para regras de alerta do log.
- [Saiba mais](https://docs.microsoft.com/azure/service-health/service-health-overview) sobre a Integridade do Serviço do Azure