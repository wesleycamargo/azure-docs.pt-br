---
title: Transformação de Divisão Condicional do Fluxo de Dados de Mapeamento do Azure Data Factory
description: Transformação de Divisão Condicional do Fluxo de Dados do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 9fe650465160ab837d8c8c191887d0f952976682
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56270832"
---
# <a name="azure-data-factory-mapping-data-flow-conditional-split-transformation"></a>Transformação de Divisão Condicional do Fluxo de Dados de Mapeamento do Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

A transformação de divisão condicional pode rotear linhas de dados para diferentes fluxos dependendo do conteúdo dos dados. A implementação da transformação de divisão condicional é semelhante a uma estrutura de decisão CASE em uma linguagem de programação. A transformação avalia expressões, e com base nos resultados, direciona a linha de dados para o fluxo especificado. Essa transformação também fornece uma saída padrão, para que se uma linha não corresponder a nenhuma expressão ela seja direcionada para a saída padrão.

![divisão condicional](media/data-flow/cd1.png "divisão condicional")

Para adicionar outras condições, selecione "Adicionar Stream" no painel de configuração inferior e clique na caixa de texto do construtor de expressões para criar sua expressão.
