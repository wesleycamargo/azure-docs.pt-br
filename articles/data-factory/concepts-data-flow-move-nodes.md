---
title: Nós de Movimentação de Fluxo de Dados do Azure Data Factory
description: Como mover os nós em um diagrama de Fluxo de Dados do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 4328a090478747e9acbfb25cca45b330cd4a300b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56212237"
---
#<a name="data-factory-data-flow-move-nodes"></a>Nós de Movimentação de Fluxo de Dados do Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Opções de transformação de agregação](media/data-flow/agghead.png "cabeçalho agregador")

A superfície de design de Fluxo de Dados do Azure Data Factory é uma superfície de "construção" na qual você cria fluxos de dados de cima para baixo, da esquerda para a direita. Há uma caixa de ferramentas anexada a cada transformação com um símbolo de adição (+). Concentre-se na lógica de negócios em vez de conectar nós por meio de bordas em um ambiente de DAG de forma livre.

Portanto, sem um paradigma do tipo "arrastar e soltar", o modo de mover um nó de transformação é alterar o fluxo de entrada. Em vez disso, você moverá as transformações alterando o "fluxo de entrada".

No Fluxo de Dados do Azure Data Factory, os fluxos declaram o fluxo de dados. No painel de configurações de transformação, você verá um campo de "Fluxo de entrada". Isso informa qual fluxo de dados de entrada está alimentando a transformação. Você pode alterar a localização física do seu nó de transformação no gráfico clicando no nome do fluxo de entrada e selecionando outro fluxo de dados. A transformação atual, juntamente com todas as transformações subsequentes no fluxo, será movida para a nova localização.

Se você estiver movendo uma transformação com uma ou mais transformações depois dela, a nova localização no fluxo de dados será adicionado por meio de um novo branch.

Se não tiver transformações subsequentes após o nó que você selecionou, somente essa transformação será movida para a nova localização.
