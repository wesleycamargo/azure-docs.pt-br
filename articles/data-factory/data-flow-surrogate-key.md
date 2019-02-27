---
title: Transformação de Chave Alternativa do Fluxo de Dados de Mapeamento do Azure Data Factory
description: Transformação de Chave Alternativa do Fluxo de Dados de Mapeamento do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 62e9879f6bc75f042669ecb931ca7459d1317cc7
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56270904"
---
# <a name="azure-data-factory-mapping-data-flow-surrogate-key-transformation"></a>Transformação de Chave Alternativa do Fluxo de Dados de Mapeamento do Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Use a Transformação de Chave Alternativa para adicionar um valor de chave crescente, arbitrário e não comercial ao conjunto de linhas de seu fluxo de dados. Isso é útil durante a criação de tabelas de dimensões em um modelo de dados analítico de esquema em estrela em que cada membro das tabelas precise ter uma chave exclusiva que seja não comercial, parte importante da metodologia Kimball de DWs.

![Transformar Chave Alternativa](media/data-flow/surrogate.png "Transformação de Chave Alternativa")

"Coluna de Chave" é o nome que você dará à sua nova coluna de chave alternativa.

"Valor Inicial" é o ponto de partida do valor incremental.
