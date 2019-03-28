---
title: O que é a Visão Personalizada do Azure?
titlesuffix: Azure Cognitive Services
description: Aprenda a usar o Serviço de Visão Personalizada para criar classificadores de imagem personalizada na nuvem do Azure.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: 50935aca20af931eec63717921ef7a73267d2373
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58350455"
---
# <a name="what-is-azure-custom-vision"></a>O que é a Visão Personalizada do Azure?

A Visão Personalizada do Azure é um serviço cognitivo que permite compilar, implantar e aprimorar seus próprios classificadores de imagem. Um classificador de imagem é um serviço de inteligência artificial que aplica rótulos (que representam _classes_) a imagens de acordo com suas características visuais. Ao contrário do serviço [Pesquisa Visual Computacional](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), a Visão Personalizada permite que você determine quais rótulos aplicar.

## <a name="what-it-does"></a>O que faz

O Serviço de Visão Personalizada usa um algoritmo de aprendizado de máquina para aplicar rótulos a imagens. Você, o desenvolvedor, deve enviar grupos de imagens que tenham ou não as características em questão. Você mesmo rotula as imagens no momento do envio. Em seguida, o algoritmo treina com esses dados e calcula sua própria precisão se testando com essas mesmas imagens. Depois que o algoritmo é treinado, você pode testá-lo, retreiná-lo e, evento, usá-lo para classificar novas imagens de acordo com as necessidades de seu aplicativo. Você também pode exportar o próprio modelo para uso offline.

### <a name="classification-and-object-detection"></a>Classificação e detecção de objetos

A funcionalidade de Visão Personalizada pode ser dividida em dois recursos. A **classificação de imagens** aplica um ou mais rótulos a uma imagem. A **detecção de objetos** é semelhante, mas também retorna as coordenadas na imagem em que os rótulos aplicados podem ser encontrados.

### <a name="optimization"></a>Otimização

O Serviço de Visão Personalizada é otimizado para reconhecer rapidamente as principais diferenças entre imagens. Isso permite que você inicie a criação de protótipos seu modelo com uma pequena quantidade de dados. Geralmente, 50 imagens por marca são um bom começo. No entanto, isso significa que o serviço não é ideal para detectar diferenças sutis em imagens (por exemplo, detectando fendas pequenas ou estão mais em cenários de garantia de qualidade).

Além disso, você pode escolher entre diversas variedades do algoritmo da Visão Personalizada que são otimizadas para determinados assuntos, por exemplo, pontos de referência ou itens de varejo. Consulte o guia [Criar um classificador](getting-started-build-a-classifier.md) guia para obter mais informações sobre eles.

## <a name="what-it-includes"></a>O que inclui

O Serviço de Visão Personalizada está disponível como um conjunto de SDKs nativos e por meio de uma interface baseada na Web na [página inicial da Visão Personalizada](https://customvision.ai/). Você pode criar, testar e treinar um modelo por uma dessas interfaces ou usar ambas conjuntamente.

![Página inicial da Visão Personalizada em uma janela do navegador Chrome](media/browser-home.png)

## <a name="data-privacy-and-security"></a>Segurança e privacidade de dados

Assim como ocorre com todos os Serviços Cognitivos, os desenvolvedores que usam o serviço de Visão Personalizada devem estar cientes das políticas da Microsoft em relação aos dados do cliente. Confira a [página de Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na Central de Confiabilidade da Microsoft para saber mais.

## <a name="next-steps"></a>Próximas etapas

Siga o guia [Criar um classificador](getting-started-build-a-classifier.md) para começar a usar a Visão Personalizada na Web ou conclua um [Tutorial de classificação de imagem](csharp-tutorial.md) para implementar um cenário básico no código.
