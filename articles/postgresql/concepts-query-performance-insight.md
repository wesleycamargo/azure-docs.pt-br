---
title: Análise de Desempenho de Consultas no Banco de Dados do Azure para PostgreSQL
description: Este artigo descreve o recurso Análise de Desempenho de Consultas no Banco de Dados do Azure para PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: 38cfac8932859db7daa76e831372c97d97f0b24c
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49376304"
---
# <a name="query-performance-insight"></a>Análise de Desempenho de Consultas 

**Aplica-se a:** Banco de Dados do Azure para PostgreSQL 9.6 e 10

> [!IMPORTANT]
> O recurso Análise de Desempenho de Consultas está em versão prévia pública. 

A Análise de Desempenho de Consultas ajuda você a identificar rapidamente quais são suas consultas de execução mais longa, como elas mudam ao longo do tempo e quais esperas as estão afetando.

## <a name="permissions"></a>Permissões
**Permissões do Proprietário** ou **Colaborador** necessárias para exibir o texto das consultas na Análise de Desempenho de Consultas **Leitor** podem exibir gráficos e tabelas, mas não o texto da consulta.

## <a name="prerequisites"></a>Pré-requisitos
Para a Análise de Desempenho de Consultas funcionar, os dados precisam existir no [Repositório de Consultas](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Exibição de análises de desempenho
A visualização da [Análise de Desempenho de Consultas](concepts-query-performance-insight.md) no portal do Azure será superficial visualizações em informações do Repositório de Consultas. 

Na página do portal do Banco de Dados do Azure para PostgreSQL, selecione **Análise de Desempenho de Consultas** na seção **Suporte + solução de problemas** da barra de menus.

![Consultas de execução longa da Análise de Desempenho de Consultas](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

A guia **consultas de Longa execução** mostra as 5 consultas superiores por duração média por execução, agregadas em intervalos de 15 minutos. Você pode exibir mais consultas, selecionando a partir do **Número de consultas** lista suspensa. As cores do gráfico pode ser alteradas para uma ID de consulta específica ao fazer isso.

Você pode clicar e arrastar no gráfico para restringi-lo a uma janela de tempo específico. Como alternativa, use os ícones de ampliar e afastar para exibir um período maior ou menor, respectivamente.

A tabela abaixo do gráfico contém mais detalhes sobre as consultas de execução longa na janela de tempo.

Selecione a guia das **Estatísticas de Espera** guia para exibir as visualizações correspondentes em espera no servidor.

![Estatísticas de espera de Análise de Desempenho de Consultas](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [monitoramento e ajuste](concepts-monitoring.md) no Banco de Dados do Azure para PostgreSQL.


