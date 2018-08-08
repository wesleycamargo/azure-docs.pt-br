---
title: Recomendações do SQL Data Warehouse - Conceitos | Microsoft Docs
description: Aprenda sobre as recomendações do SQL Data Warehouse e como elas são geradas
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 07/27/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 2cbd691c29039c65b98d8b0191e9e278d2440f09
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39347598"
---
# <a name="sql-data-warehouse-recommendations"></a>Recomendações do SQL Data Warehouse

Este artigo descreve as recomendações fornecidas pelo SQL Data Warehouse por meio do Azure Advisor.  

O SQL Data Warehouse fornece recomendações para garantir que seu data warehouse seja consistentemente otimizado para desempenho. As recomendações de data warehouse são totalmente integradas ao [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) para fornecer as práticas recomendadas diretamente no [Portal do Azure](https://aka.ms/Azureadvisor). O SQL Data Warehouse analisa o estado atual de seu data warehouse, coleta a telemetria e faz recomendações de superfície para sua carga de trabalho ativa em uma cadência diária. Os cenários de recomendação de data warehouse suportados são descritos abaixo, juntamente com a forma de aplicar as ações recomendadas.

Se você tiver algum comentário sobre o SQL Data Warehouse Advisor ou encontrar algum problema, entre em contato com [sqldwadvisor@service.microsoft.com](mailto:sqldwadvisor@service.microsoft.com).   

Clique [aqui](https://aka.ms/Azureadvisor) para verificar suas recomendações hoje mesmo! Atualmente, esse recurso é aplicável apenas aos armazéns de dados Gen2. 

## <a name="data-skew"></a>Distorção de dados

A distorção de dados pode causar movimentação adicional de dados ou afunilamentos de recursos ao executar sua carga de trabalho. A seguinte documentação descreve mostrar para identificar distorção de dados e impedir que isso aconteça selecionando uma chave de distribuição ideal.

- [Identificar e Remover distorção](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>Não ou estatísticas desatualizadas

Ter estatísticas abaixo do ideal pode impactar gravemente o desempenho da consulta, pois pode fazer com que o otimizador de consulta do SQL Data Warehouse gere planos de consulta abaixo do ideal. A documentação a seguir descreve as melhores práticas de criação e atualização de estatísticas:

- [Criando e atualizando estatísticas da tabela](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistic)

Para estas duas recomendações, o orientador está executando continuamente o seguinte [script T-SQL](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables) para identificar as tabelas impactadas pelas recomendações de inclinação e estatística.
