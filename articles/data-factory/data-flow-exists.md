---
title: Transformação do Azure Data Factory Mapeando dados de fluxo existe
description: Como verificar linhas existentes usando dados de mapeamento do data factory fluxos com Exists transformação
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: b98b7afb21f2f50d44ba93ed793b6efb20f75164
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65235969"
---
# <a name="mapping-data-flow-exists-transformation"></a>Mapeamento de fluxo de dados existe transformação

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

A transformação de ocorrências é uma transformação de filtragem de linhas que impede ou permite que as linhas de dados façam a passagem. A Transformação de Ocorrências é semelhante a ```SQL WHERE EXISTS``` e ```SQL WHERE NOT EXISTS```. Após a transformação existe, as linhas resultantes do seu fluxo de dados ou incluirá todas as linhas onde os valores de coluna da fonte 1 existem na origem 2 ou não existem na origem 2.

![Configurações de ocorrências](media/data-flow/exists.png "ocorrências 1")

Escolha a segunda fonte para suas ocorrências de modo que o fluxo de dados possa comparar os valores do fluxo 1 com os do fluxo 2.

Selecione as colunas da fonte 1 e da fonte 2 cujos valores quiser verificar se existem ou não.

## <a name="multiple-exists-conditions"></a>Várias condições de existe

Ao lado de cada linha em suas condições de coluna para Exists, você encontrará um + sinal disponíveis quando você focaliza alcançar linha. Isso permitirá que você adicione várias linhas para condições de Exists. Cada condição adicional que é um "E".

## <a name="custom-expression"></a>Expressão personalizada

![Configurações personalizadas de existe](media/data-flow/exists1.png "existe personalizado")

Você pode clicar em "Expressão personalizada" para criar uma expressão de forma livre como seu existe ou não-existe condição. Marcar essa caixa permitirá que você digitar em sua própria expressão como uma condição.

## <a name="next-steps"></a>Próximas etapas

Transformações semelhantes são [Lookup](data-flow-lookup.md) e [ingressar](data-flow-join.md).
