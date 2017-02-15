---
title: "Análise de Desempenho do Banco de Dados SQL do Azure| Microsoft Docs"
description: "O Banco de Dados SQL do Azure fornece ferramentas de desempenho para ajudá-lo a identificar áreas em que é possível melhorar o desempenho de consulta atual."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: a60b75ac-cf27-4d73-8322-ee4d4c448aa2
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 07/19/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: 071cc504cc4111e20391af86ffdc7819644ec434


---
# <a name="sql-database-performance-insight"></a>Análise de Desempenho de Banco de Dados SQL
O Banco de Dados SQL do Azure fornece ferramentas de desempenho para ajudá-lo a identificar e melhorar o desempenho dos bancos de dados, fornecendo recomendações e ações de ajuste inteligentes. 

1. Navegue até seu banco de dados no [Portal do Azure](http://portal.azure.com) e clique em **Todas as configurações** > **Desempenho** > **Visão geral** para abrir a página **Desempenho**. 
2. Clique em **Recomendações** para abrir o [Assistente do Banco de Dados SQL](#sql-database-advisor), então clique em **Consultas** para abrir a [Análise de Desempenho de Consultas](#query-performance-insight).
   
    ![Exibir Desempenho](./media/sql-database-performance/entries.png)

## <a name="performance-overview"></a>Visão geral do desempenho
Clicar no bloco **Visão geral** ou **Desempenho** levará você ao painel de desempenho do banco de dados. Essa exibição fornece um resumo do desempenho de seu banco de dados e lhe ajudará com ajustes e solução de problemas de desempenho. 

![Desempenho](./media/sql-database-performance/performance.png)

* O bloco **Recomendações** fornece uma divisão das recomendações de ajuste para seu banco de dados (as 3 principais recomendações serão exibidas, se existirem mais). Clicar nesse bloco leva você para o **Advisor do Banco de Dados SQL**. 
* O bloco **Atividade de ajuste** fornece um resumo das atividades de ajuste em andamento e concluídas para o banco de dados, oferecendo uma visão rápida do histórico de atividades de ajuste. Clicar nesse bloco leva você para a exibição do histórico completo de ajustes do banco de dados.
* O bloco **Ajuste automático** mostra a configuração de ajuste automático do banco de dados (quais ações de ajuste estão configuradas para serem aplicadas automaticamente ao banco de dados). Clicar nesse bloco abre a caixa de diálogo de configuração de automação.
* O bloco **Consultas de banco de dados** mostra o resumo do desempenho de consulta do banco de dados (uso geral de DTU e consultas com maior consumo de recursos). Clicar nesse bloco leva para **Análise de Desempenho de Consultas**.

## <a name="sql-database-advisor"></a>Advisor do Banco de Dados SQL
[Advisor do Banco de Dados SQL](sql-database-advisor.md) fornece recomendações de ajuste inteligentes que podem ajudar a melhorar o desempenho de seu banco de dados. 

* Recomendações sobre quais índices criar ou remover (e uma opção para aplicar as recomendações de índice automaticamente sem qualquer interação com o usuário, revertendo automaticamente recomendações com impacto negativo sobre o desempenho).
* Recomendações quando problemas de esquema são identificados no banco de dados.
* Recomendações quando consultas podem se beneficiar de consultas parametrizadas.

## <a name="query-performance-insight"></a>Análise de Desempenho de Consultas
[Análise de Desempenho de Consultas](sql-database-query-performance.md) permite que você gaste menos tempo solucionando problemas de desempenho de banco de dados, fornecendo o seguinte:

* Mais informações sobre o consumo de recursos de bancos de dados (DTU). 
* As consultas que mais consomem CPU, que potencialmente podem ser ajustadas para melhorar o desempenho. 
* A capacidade de fazer drill down nos detalhes de uma consulta. 

## <a name="additional-resources"></a>Recursos adicionais
* [Diretrizes de desempenho do Banco de Dados SQL do Azure para bancos de dados únicos](sql-database-performance-guidance.md)
* [Quando um pool elástico deve ser usado?](sql-database-elastic-pool-guidance.md)




<!--HONumber=Dec16_HO2-->


