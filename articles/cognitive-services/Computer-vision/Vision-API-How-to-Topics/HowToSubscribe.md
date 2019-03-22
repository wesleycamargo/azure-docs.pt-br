---
title: Obter chaves de assinatura - Pesquisa Visual Computacional
titlesuffix: Azure Cognitive Services
description: Saiba como obter chaves de assinatura para chamadas à API do Computer Vision no Azure Cognitive Services.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 05/19/2017
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 03e519520d4a956a5c9690dc1327089505aafced
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58120849"
---
# <a name="how-to-obtain-subscription-keys"></a>Como obter chaves de assinatura

Os serviços de visão do computador exigem chaves de assinatura especial. Toda chamada à API da Pesquisa Visual Computacional requer uma chave de assinatura. Essa chave precisa ser passada por um parâmetro de cadeia de caracteres de consulta ou especificada no cabeçalho da solicitação.

Para se inscrever em chaves de assinatura, consulte [Assinaturas](https://azure.microsoft.com/try/cognitive-services/). É gratuito para se inscrever. Os preços desses serviços estão sujeito a alterações.

> [!NOTE]
> Suas chaves de assinatura são válidos para apenas um desses [regiões do Azure da Microsoft](https://azure.microsoft.com/regions/). 

| Região | Endereço |
|---|---|
| Oeste dos EUA | westus.api.cognitive.microsoft.com |
| Leste dos EUA 2 | eastus2.API.Cognitive.microsoft.com |
| Centro-Oeste dos EUA | westcentralus.api.cognitive.microsoft.com |
| Europa Ocidental | westeurope.api.cognitive.microsoft.com |
| Sudeste Asiático | southeastasia.api.cognitive.microsoft.com |

Se você se inscrever usando a avaliação gratuita do Computer Vision, suas chaves de assinatura serão válidas para a região **westcentral** (`https://westcentralus.api.cognitive.microsoft.com/`). Que é o caso mais comum. No entanto, se você se inscrever no Computer Vision com sua conta do Microsoft Azure por meio do site [https://azure.microsoft.com/](https://azure.microsoft.com/), especifique a região para a sua avaliação na lista anterior de regiões.

Por exemplo, se você se inscrever no Computer Vision com sua conta do Microsoft Azure e especificar `westus` para sua região, deverá usar a região `westus` para suas chamadas da API REST (`https://westus.api.cognitive.microsoft.com/`).

Se você esquecer a região da sua chave de assinatura depois de obter sua chave de avaliação, poderá encontrar sua região em [https://azure.microsoft.com/try/cognitive-services/my-apis/](https://azure.microsoft.com/try/cognitive-services/my-apis/).

### <a name="related-links"></a>Links relacionados:

* [Opções de precificação para APIs de Serviços Cognitivos do Azure](https://azure.microsoft.com/pricing/details/cognitive-services/)
