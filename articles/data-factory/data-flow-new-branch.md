---
title: Transformação de Ramificação do Novo Fluxo de Dados de Mapeamento do Azure Data Factory
description: Transformação de Ramificação do Novo Fluxo de Dados de Mapeamento do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: c48c1500a31ce8f22bcef7207c0ea67caca24231
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56270911"
---
# <a name="azure-data-factory-mapping-data-flow-new-branch-transformation"></a>Transformação de Ramificação do Novo Fluxo de Dados de Mapeamento do Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Opções de branch](media/data-flow/menu.png "menu")

A ramificação pegará o fluxo de dados atual no fluxo de dados e irá replicá-lo em outro fluxo. Use a Nova Ramificação para executar vários conjuntos de operações e transformações no mesmo fluxo de dados.

Exemplo: Seu fluxo de dados possui uma Transformação de Código-fonte com um conjunto selecionado de colunas e conversões de tipo de dados. Em seguida, coloque uma Coluna Derivada imediatamente após essa Fonte. Na Coluna Derivada, crie um novo campo que combine o nome e sobrenome para formar um novo campo “nome completo”.

Você pode tratar esse novo fluxo com um conjunto de transformações e um coletor em uma linha e usar a Nova Ramificação para criar uma cópia desse fluxo no qual você pode transformar esses mesmos dados com um conjunto diferente de transformações. Ao transformar esses dados copiados em uma ramificação separada, você pode posteriormente coletar esses dados para um local separado.

> [!NOTE]
> “Nova Ramificação” será exibida como uma ação no menu + Transformação quando há uma transformação subsequente no local atual no qual você está tentando ramificar a seguir. ou seja, você não verá uma opção de “Nova Ramificação” no final até que você adicione outra transformação depois de Selecionar

![Ramificação](media/data-flow/branch2.png "Ramificação 2")
