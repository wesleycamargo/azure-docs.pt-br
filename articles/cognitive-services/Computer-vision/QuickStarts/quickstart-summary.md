---
title: Resumo do Início Rápido da Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Nestes inícios rápidos, você poderá analisar uma imagem, criar uma miniatura e extrair texto impresso e manuscrito usando a API da Pesquisa Visual Computacional.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 645c57c9bfeaefcfd2fc50b2dd431713fffaa42b
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53578777"
---
# <a name="quickstart-summary"></a>Início Rápido: Resumo

Estes inícios rápidos fornecem informações e exemplos de código para ajudar você a começar a usar o serviço de Pesquisa Visual Computacional.

Os exemplos de fazem chamadas HTTP diretas para a API de Pesquisa Visual Computacional. Veja a seção *Início Rápido do SDK* para obter exemplos de uso das bibliotecas de clientes de Pesquisa Visual Computacional, que fornecem métodos convenientes que encapsulam as chamadas HTTP.

Para experimentar rapidamente as APIs de Pesquisa Visual Computacional, tente o [Console de teste de API aberta](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

Você pode usar o serviço de Pesquisa Visual Computacional para realizar as seguintes tarefas:

* Analisar uma imagem remota
* Analisar uma imagem local
* Detectar celebridades e pontos de referência (modelos de domínio)
* Gerar uma miniatura de maneira inteligente
* Detecte e extraia texto impresso (OCR) de uma imagem
* Detecte e extraia texto manuscrito de uma imagem

O código em cada exemplo é semelhante. No entanto, eles destacam diferentes recursos de Pesquisa Visual Computacional, juntamente com diferentes técnicas para a troca de dados com o serviço, como:

* _Gerar uma miniatura_ retorna uma imagem como uma matriz de bytes no corpo da resposta.
* _Analisar uma imagem local_ requer que a imagem seja incluída na solicitação como uma matriz de bytes.
* _Extrair textos manuscritos_ requer duas chamadas para recuperar o texto.

## <a name="summary"></a>Resumo

| Início rápido               | Parâmetros de solicitação                          | Response          |
| ------------------------ | ------------------------------------------- | ----------------  |
| Analisar uma imagem remota   | visualFeatures=Categories,Description,Color | cadeia de caracteres JSON       |
| Analisar uma imagem local    | data=image_data (matriz de bytes)                | cadeia de caracteres JSON       |
| Detectar celebridades       | model=celebrities                           | cadeia de caracteres JSON       |
| Gerar uma miniatura     | width=200&height=150&smartCropping=true     | matriz de bytes        |
| Extrair texto impresso     | language=unk&detectOrientation=true         | cadeia de caracteres JSON       |
| Extrair texto escrito à mão | handwriting=true                            | Cadeia de caracteres JSON, URL  |

## <a name="next-steps"></a>Próximas etapas

Explore as APIs de Pesquisa Visual Computacional usadas para analisar uma imagem, detectar celebridades e marcos, criar uma miniatura e extrair textos manuscritos e impressos.

> [!div class="nextstepaction"]
> [Explorar APIs de Pesquisa Visual Computacional](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
