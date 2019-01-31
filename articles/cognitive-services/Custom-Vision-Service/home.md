---
title: O que é a Visão Personalizada do Azure?
titlesuffix: Azure Cognitive Services
description: Aprenda a usar o Serviço de Visão Personalizada para criar classificadores de imagem personalizada na nuvem do Azure.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 01/10/2019
ms.author: anroth
ms.openlocfilehash: 887c2fa09a1108b6254f4dd13b1ca211c80f8f70
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219253"
---
# <a name="what-is-azure-custom-vision"></a>O que é a Visão Personalizada do Azure?

A API de Visão Personalizada do Azure é um serviço cognitivo que permite compilar, implantar e aprimorar os classificadores de imagem personalizados. Um classificador de imagem é um serviço de inteligência artificial que organiza as imagens em classes (marcas) de acordo com determinadas características. Ao contrário do serviço [Pesquisa Visual Computacional](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), a Visão Personalizada permite que você crie suas próprias classificações.

## <a name="what-it-does"></a>O que faz

O Serviço de Visão Personalizada usa um algoritmo de aprendizado de máquina para classificar imagens. Você, o desenvolvedor, deve enviar grupos de imagens que tenham ou não as classificações em questão. Você pode especificar as marcas corretas das imagens no momento do envio. Em seguida, o algoritmo treina com esses dados e calcula sua própria precisão se testando com esses mesmos dados. Depois que o modelo é treinado, você pode testá-lo, retreiná-lo e, evento, usá-lo para classificar novas imagens de acordo com as necessidades de seu aplicativo. Você também pode exportar o próprio modelo para uso offline.

### <a name="classification-and-object-detection"></a>Classificação e detecção de objetos

A funcionalidade de Visão Personalizada pode ser dividida em dois recursos. A **Classificação de imagens** atribui uma distribuição de classificações para cada imagem. Há suporte para modelos de classificação multiclasse (uma marca por imagem) e de vários rótulos (qualquer número de marcas por imagem). A **detecção de objetos** é semelhante à classificação de vários rótulos, mas também retorna as coordenadas na imagem em que os rótulos aplicados podem ser encontrados.

### <a name="optimization"></a>Otimização

Em geral, os métodos usados pelo Serviço de Visão Personalizada são sólidos em relação a diferenças, o que permite que você inicie a criação de protótipos com uma pequena quantidade de dados. 50 imagens por marca geralmente são um bom começo. No entanto, isso significa que o serviço não é ideal para detectar diferenças sutis em imagens (por exemplo, detectando fendas pequenas ou estão mais em cenários de garantia de qualidade).

Além disso, você pode escolher entre diversas variedades do algoritmo da Visão Personalizada que são otimizadas para determinados assuntos, &mdash;por exemplo, pontos de referência ou itens de varejo. Consulte o guia [Criar um classificador](getting-started-build-a-classifier.md) guia para obter mais informações sobre eles.

## <a name="what-it-includes"></a>O que inclui
O Serviço de Visão Personalizada está disponível como um conjunto de SDKs nativos e por meio de uma interface baseada na Web na [página inicial da Visão Personalizada](https://customvision.ai/). Você pode criar, testar e treinar um modelo por uma dessas interfaces, ou por ambas.

![Página inicial da Visão Personalizada em uma janela do navegador Chrome](media/browser-home.png)

## <a name="data-privacy-and-security"></a>Segurança e privacidade de dados

Assim como ocorre com todos os Serviços Cognitivos, os desenvolvedores que usam o serviço de Visão Personalizada devem estar cientes das políticas da Microsoft em relação aos dados do cliente. Confira a [página de Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na Central de Confiabilidade da Microsoft para saber mais.

## <a name="next-steps"></a>Próximas etapas

Siga o guia [Criar um classificador](getting-started-build-a-classifier.md) para começar a usar a Visão Personalizada na Web ou conclua um [Tutorial de classificação de imagem](csharp-tutorial.md) para implementar o cenário no código.
