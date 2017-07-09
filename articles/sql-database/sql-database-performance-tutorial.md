---
title: Solucionar problemas de desempenho e otimizar seu banco de dados | Microsoft Docs
description: "Aplicar recomendações de desempenho para o banco de dados SQL, bem como aprender a obter informações sobre o desempenho das consultas em execução no seu banco de dados"
metakeywords: azure sql database performance monitoring recommendation
services: sql-database
documentationcenter: 
manager: jhubbard
author: jan-eng
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,monitor & tune
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: f9ae96cdc80c347593f229cb2fce3f2d4d8e7caf
ms.contentlocale: pt-br
ms.lasthandoff: 06/15/2017


---
# <a name="troubleshoot-performance-issues-and-optimize-your-database"></a>Solucionar problemas de desempenho e otimizar seu banco de dados

Índices ausentes e consultas precárias são motivos comuns de um desempenho ruim do banco de dados. Neste tutorial, você aprenderá a:
> [!div class="checklist"]
> * Revisar, aplicar e reverter recomendações de melhoria de desempenho
> * Localizar consultas com alta utilização de recursos
> * Localizar consultas de longa execução

> Você precisa de uma carga de trabalho contínua em um banco de dados com problemas de desempenho – por exemplo, para receber uma recomendação de índice ausente.
>

## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure

Faça logon no [Portal do Azure](https://portal.azure.com/).

## <a name="review-and-apply-a-recommendation"></a>Examinar e aplicar uma recomendação

Siga estas etapas para aplicar uma recomendação do sistema ao banco de dados:

1. Clique no menu **Recomendações de desempenho** na folha do banco de dados.

    ![recomendação de desempenho](./media/sql-database-performance-tutorial/perf_recommendations.png)

2. Na lista de recomendações, selecione uma recomendação ativa. Neste exemplo, Create Index.

    ![selecionar recomendação](./media/sql-database-performance-tutorial/create_index.png)

3. Aplique a recomendação clicando no botão **Aplicar**. Opcionalmente, examine os detalhes de recomendação e veja o script T-SQL a ser executado clicando no botão **Exibir Script**.

    ![aplicar recomendação](./media/sql-database-performance-tutorial/apply.png)

4. [Opcional] Habilite o ajuste automático para que as recomendações sejam aplicadas automaticamente.

    ![auto ajuste](./media/sql-database-performance-tutorial/auto_tuning.png)

## <a name="revert-a-recommendation"></a>Reverter uma recomendação

O Assistente do Banco de Dados monitora todas as recomendações implementadas. Se uma recomendação não melhorar a carga de trabalho, ela será automaticamente revertida. É possível reverter manualmente uma recomendação, mas isso não será necessário na maioria dos casos. Para reverter uma recomendação:

1. Vá para o menu de recomendações de desempenho e selecione uma das recomendações aplicadas.

    ![selecionar recomendação](./media/sql-database-performance-tutorial/select.png)

2. Na exibição de detalhes, clique em **Reverter**.

    ![reverter recomendação](./media/sql-database-performance-tutorial/revert.png)

## <a name="find-the-query-that-consumes-the-most-resources"></a>Localizar a consulta que consome mais recursos

Siga estas etapas para localizar a consulta que consome mais recursos:

1. Clique no menu **Análise de Desempenho de Consultas** na folha do banco de dados.

    ![informações de consulta](./media/sql-database-performance-tutorial/query_perf_insights.png)

2. Selecionar um tipo de recurso.

    ![informações de consulta](./media/sql-database-performance-tutorial/select_resource_type.png)

3. Selecione a primeira consulta na tabela.

    ![informações de consulta](./media/sql-database-performance-tutorial/select_query.png)

4. Examine os detalhes da consulta.

    ![informações de consulta](./media/sql-database-performance-tutorial/query_details.png)

## <a name="find-the-longest-running-query"></a>Localizar a consulta mais longa em execução

1. Vá para a Análise de Desempenho de Consultas e selecione a guia **Consultas de longa execução**.

    ![informações de consulta](./media/sql-database-performance-tutorial/long_running.png)

3. Selecione a primeira consulta na tabela.

    ![informações de consulta](./media/sql-database-performance-tutorial/select_first_query.png)

4. Examine os detalhes da consulta.

    ![informações de consulta](./media/sql-database-performance-tutorial/review_query_details.png)



## <a name="next-steps"></a>Próximas etapas 
Índices ausentes e consultas precárias são motivos comuns de um desempenho ruim do banco de dados. Neste tutorial, você aprendeu a:
> [!div class="checklist"]
> * Revisar, aplicar e reverter recomendações de melhoria de desempenho
> * Localizar consultas com alta utilização de recursos
> * Localizar consultas de longa execução

[Dicas de ajuste de desempenho do Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-troubleshoot-performance)

