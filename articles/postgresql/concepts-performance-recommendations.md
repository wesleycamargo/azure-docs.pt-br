---
title: Recomendações de desempenho no Banco de Dados do Azure para PostgreSQL
description: Este artigo descreve as recomendações de desempenho, um pode obter no Banco de Dados do Azure para PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 25ab2f735cfd4b0870729cb86992665fa8984580
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46976369"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql"></a>Recomendações de desempenho no Banco de Dados do Azure para PostgreSQL

**Aplica-se a:** Banco de Dados do Azure para PostgreSQL 9.6 e 10

> [!IMPORTANT]
> Recomendações de Desempenho na Versão Prévia Pública.

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

