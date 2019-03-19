---
title: Transformação de Coluna Derivada de Fluxo de Dados do Azure Data Factory
description: Transformação de Coluna Derivada de Fluxo de Dados do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: 1335b86431bcf345f452ead0c95926d295ae185f
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728753"
---
# <a name="azure-data-factory-data-flow-derived-column-transformation"></a>Transformação de Coluna Derivada do Fluxo de Dados do Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Use a transformação de Coluna Derivada para gerar novas colunas em seu fluxo de dados ou modificar campos existentes.

![derivar coluna](media/data-flow/dc1.png "Coluna Derivada")

Você pode executar várias ações de Coluna Derivada em uma única transformação de Coluna Derivada. Clique em "Adicionar coluna" para transformar mais de uma coluna na etapa única de transformação.

No campo Coluna, selecione uma coluna existente para sobrepor com um novo valor derivado ou clique em "Criar Nova Coluna" para gerar uma nova coluna com o novo valor derivado.

A caixa de texto Expressão abrirá o Construtor de Expressões onde você poderá criar a expressão para as colunas derivadas usando funções de expressão.
