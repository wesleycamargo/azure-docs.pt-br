---
title: Nó de Referência do Fluxo de Dados do Mapeamento do Azure Data Factory
description: O Fluxo de Dados do Data Factory adicionará um nó de referência para junções, pesquisas, uniões
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 641375c2b848957ffc0f5ad092a28460d91b6690
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56212249"
---
# <a name="azure-data-factory-mapping-data-flow-reference-node"></a>Nó de Referência do Fluxo de Dados do Mapeamento do Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Nó de referência](media/data-flow/referencenode.png "nó de referência")

Um nó de referência é adicionado automaticamente à tela para indicar que o nó ao qual está conectado faz referência a outro nó existente na tela. Pense em um nó de referência como um ponteiro ou uma referência a outra transformação de fluxo de dados.

Por exemplo:  Quando você faz a junção ou a união de mais de um fluxo de dados, a tela de Fluxo de Dados pode adicionar um nó de referência que reflete o nome e as configurações do fluxo de entrada não primário.

O nó de referência não pode ser movido ou excluído. No entanto, você pode clicar no nó de modificar as configurações de transformação de origem.

As regras de interface do usuário que determinam quando o Fluxo de Dados adiciona o nó de referência são baseadas no espaço disponível e no espaçamento vertical entre linhas.
