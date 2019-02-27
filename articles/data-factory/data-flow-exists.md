---
title: Transformação de Ocorrências no Fluxo de Dados de Mapeamento do Azure Data Factory
description: Transformação de Ocorrências no Fluxo de Dados de Mapeamento do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 9d21b304f55ec746da4b7b42194fe0d168261b53
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56270910"
---
# <a name="azure-data-factory-mapping-data-flow-exists-transformation"></a>Transformação de Ocorrências no Fluxo de Dados de Mapeamento do Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

A transformação de ocorrências é uma transformação de filtragem de linhas que impede ou permite que as linhas de dados façam a passagem. A Transformação de Ocorrências é semelhante a ```SQL WHERE EXISTS``` e ```SQL WHERE NOT EXISTS```. Depois de uma transformação de filtro, as linhas resultantes do seu fluxo de dados incluirão todas as linhas onde os valores de coluna da fonte 1 existirem ou não na fonte 2.

![Configurações de ocorrências](media/data-flow/exsits.png "ocorrências 1")

Escolha a segunda fonte para suas ocorrências de modo que o fluxo de dados possa comparar os valores do fluxo 1 com os do fluxo 2.

Selecione as colunas da fonte 1 e da fonte 2 cujos valores quiser verificar se existem ou não.
