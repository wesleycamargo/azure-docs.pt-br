---
title: Nó de Referência do Fluxo de Dados do Mapeamento do Azure Data Factory
description: O Fluxo de Dados do Data Factory adicionará um nó de referência para junções, pesquisas, uniões
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 2e318210d96822b13f65eadeef79798b1b4595d1
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2019
ms.locfileid: "56325885"
---
# <a name="mapping-data-flow-reference-node"></a>Nó de Referência do Fluxo de Dados do Mapeamento

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Nó de referência](media/data-flow/referencenode.png "nó de referência")

Um nó de referência é adicionado automaticamente à tela para indicar que o nó ao qual está conectado faz referência a outro nó existente na tela. Pense em um nó de referência como um ponteiro ou uma referência a outra transformação de fluxo de dados.

Por exemplo:  Quando você faz a junção ou a união de mais de um fluxo de dados, a tela de Fluxo de Dados pode adicionar um nó de referência que reflete o nome e as configurações do fluxo de entrada não primário.

O nó de referência não pode ser movido ou excluído. No entanto, você pode clicar no nó de modificar as configurações de transformação de origem.

As regras de interface do usuário que determinam quando o Fluxo de Dados adiciona o nó de referência são baseadas no espaço disponível e no espaçamento vertical entre linhas.
