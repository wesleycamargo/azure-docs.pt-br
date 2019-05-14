---
title: Visão Geral do Fluxo de Dados do Mapeamento do Azure Data Factory
description: Uma explicação da visão geral dos Fluxos de Dados de Mapeamento no Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 051886f98d6d35594336291bbb2defb2a4acdfc5
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233055"
---
# <a name="what-are-mapping-data-flows"></a>Quais são o mapeamento de fluxos de dados?

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Fluxos de dados de mapeamento são a transformação de dados projetada visualmente no Azure Data Factory. Fluxos de dados permitem que os engenheiros de dados desenvolver a lógica de transformação de dados gráficos sem escrever código. Os fluxos de dados resultantes são executados como atividades em Pipelines do Azure Data Factory usando clusters do Azure Databricks expandidos.

A intenção do Fluxo de Dados do Azure Data Factory é fornecer uma experiência totalmente visual sem necessidade de codificação. Seus Fluxos de Dados serão executados em seu próprio cluster em execução para o processamento de dados expandidos. O Azure Data Factory manipula a conversão de código, a otimização de caminho e a execução de seus trabalhos de fluxo de dados.

Comece criando fluxos de dados no modo de depuração para que você possa validar sua lógica de transformação de forma interativa. Em seguida, adicione uma atividade de Fluxo de Dados ao seu pipeline para executar e testar seu fluxo de dados na depuração de pipeline ou use "Disparar agora" no pipeline para testar seu Fluxo de Dados de uma Atividade de pipeline.

Você, em seguida, agendará e monitorará suas atividades de fluxo de dados usando pipelines do Azure Data Factory que executam a atividade de Fluxo de Dados.

A opção de alternância do Modo de Depuração na superfície de design de Fluxo de Dados permite a criação interativa de transformações de dados. O Modo de Depuração fornece um ambiente de preparação de dados para a construção do fluxo de dados.
