---
title: Dicas de ajuste de desempenho de Banco de Dados SQL | Microsoft Docs
description: "Dicas de ajuste de desempenho no Banco de Dados SQL por meio de avaliação e melhoria."
services: sql-database
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
keywords: ajuste de desempenho de sql, ajuste de desempenho de banco de dados, dicas de ajuste de desempenho do sql, ajuste de desempenho de banco de dados sql
ms.assetid: eb7b3f66-3b33-4e1b-84fb-424a928a6672
ms.service: sql-database
ms.custom: monitor and tune
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: v-shysun
translationtype: Human Translation
ms.sourcegitcommit: 984adf244596578a3301719e5ac2f68a841153bf
ms.openlocfilehash: 3bfcaf4ae29d23754a19a61f2775d1b12e3e69ba
ms.lasthandoff: 02/16/2017


---
# <a name="sql-database-performance-tuning-tips"></a>Dicas de ajuste de desempenho do Banco de Dados SQL
É possível alterar a qualquer momento a [camada de serviço](sql-database-service-tiers.md) de um banco de dados independente ou aumentar os eDTUs de um pool elástico para melhorar o desempenho. No entanto, recomendamos identificar as oportunidades de melhoria e otimizar o desempenho da consulta primeiro. Índices ausentes e consultas precárias são motivos comuns de um desempenho ruim do banco de dados. Este artigo fornece orientação para ajuste de desempenho no Banco de Dados SQL.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="steps-to-evaluate-and-tune-database-performance"></a>Etapas para avaliar e ajustar o desempenho do banco de dados
1. No [Portal do Azure](https://portal.azure.com), clique em **Bancos de Dados SQL**, escolha o banco de dados e use o gráfico de monitoramento para procurar recursos que estão atingindo o limite. O consumo de DTU é exibido por padrão. Clique em **Editar** para alterar o intervalo de tempo e os valores mostrados.
2. Use a [Análise de Desempenho de Consultas](sql-database-query-performance.md) para avaliar as consultas usando DTUs e use o [Supervisor de Banco de Dados SQL](sql-database-advisor.md) para exibir as recomendações para criar e descartar índices, parametrizar consultas e corrigir problemas de esquema.
3. Você pode usar as DMVs (exibições de gerenciamento dinâmico), os Xevents (Eventos Estendidos) e o Armazenamento de Consulta no SSMS para obter os parâmetros de desempenho em tempo real. Veja o [tópico com diretrizes sobre desempenho](sql-database-performance-guidance.md) para obter dicas detalhadas de monitoramento e ajuste.

> [!IMPORTANT] 
> Recomendamos que você sempre use a versão mais recente do Management Studio a fim de permanecer sincronizado com as atualizações no Microsoft Azure e no Banco de Dados SQL. [Atualizar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
>

## <a name="steps-to-improve-database-performance-with-more-resources"></a>Etapas para melhorar o desempenho do banco de dados com mais recursos
1. Para bancos de dados independentes, é possível [alterar as camadas de serviço](sql-database-service-tiers.md) sob demanda para melhorar o desempenho do banco de dados.
2. Para vários bancos de dados, considere o uso de [pools elásticos](sql-database-elastic-pool-guidance.md) para dimensionar os recursos automaticamente.


