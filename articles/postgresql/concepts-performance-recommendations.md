---
title: Recomendações de desempenho no Banco de Dados do Azure para PostgreSQL
description: Este artigo descreve o recurso de recomendação de desempenho no banco de dados do Azure para PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/26/2018
ms.openlocfilehash: d8edbc2847c06e95e658a1324f2e85f1758e60be
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487933"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql"></a>Recomendações de desempenho no Banco de Dados do Azure para PostgreSQL

**Aplica-se a:** Banco de Dados do Azure para PostgreSQL 9.6 e 10

O recurso de Recomendações de Desempenho identifica os índices principais que podem ser criados em seu Banco de Dados do Azure para PostgreSQL para servidor PostgreSQL para melhorar o desempenho. Para produzir recomendações de índice, o recurso leva em consideração várias características do banco de dados, inclusive seu esquema e a carga de trabalho, conforme relatado pelo Repositório de Dados de Consultas. Depois de implementar qualquer recomendação de desempenho, os clientes devem testar o desempenho para avaliar o impacto dessas alterações. 

## <a name="permissions"></a>Permissões
**Permissões de Proprietário** ou **Colaborador** necessárias para executar a análise usando o recurso de recomendações de desempenho.

## <a name="performance-recommendations"></a>Recomendações do desempenho
O recurso das [Recomendações de Desempenho](concepts-performance-recommendations.md) recurso analisa as cargas de trabalho entre seu servidor para identificar os índices com o potencial de melhorar o desempenho.

Abra **Recomendações de Desempenho** da seção **suporte + solução de problemas** da barra de menus, na página do portal do Azure para seu servidor PostgreSQL.

![Página das Recomendações de Desempenho](./media/concepts-performance-recommendations/performance-recommendations-landing-page.png)

Selecione **Analisar** e escolha um banco de dados. Isso iniciará a análise. Dependendo de sua carga de trabalho, isso pode levar vários minutos para ser concluído. Quando a análise for concluída, haverá uma notificação no portal.

A janela **Recomendações de Desempenho** Mostrará uma lista de recomendações, caso seja encontrada. Uma recomendação mostrará informações sobre os relevantes **banco de dados**, **tabela**, **coluna**, e **tamanho de índice**.

![Nova página de recomendações de desempenho](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Para implementar a recomendação, copie o texto da consulta e executá-lo do seu cliente de escolha.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [monitoramento e ajuste](concepts-monitoring.md) no Banco de Dados do Azure para PostgreSQL.

