---
title: O que é o Serviço de Visão Personalizada?
titlesuffix: Azure Cognitive Services
description: O Serviço de Visão Personalizada permite criar classificadores de imagem personalizada na nuvem do Azure.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: overview
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: 1d3d1a82cf59b06625487fb241a63f51352e18e5
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46365852"
---
# <a name="what-is-the-custom-vision-service"></a>O que é o Serviço de Visão Personalizada?

O Serviço de Visão Personalizada é um Serviço Cognitivo do Azure que permite criar classificadores de imagem personalizada. Ele torna fácil e rápida a compilação, implantação e melhora um classificador de imagem. O Serviço de Visão Personalizada fornece uma API REST e uma interface Web para carregar suas imagens e treinar o classificador.

## <a name="what-does-custom-vision-service-do-well"></a>O que o Serviço de Visão Personalizada faz bem?

O Serviço de Visão Personalizada funciona melhor quando o item que você está tentando classificar é destacado em sua imagem. 

Algumas imagens são necessárias para criar um classificador ou detector. 50 imagens por classe são suficientes para iniciar seu protótipo. Os usos dos métodos de Serviço de Visão Personalizada são robustos para diferenças, o que permite que você inicie a criação de protótipos com poucos dados. Isso significa que o Serviço de Visão Personalizada não é adequado para cenários em que você deseja detectar diferenças sutis. Por exemplo, pequenas rachaduras ou amassados em cenários de garantia de qualidade.

## <a name="next-steps"></a>Próximas etapas

[Saiba como criar um classificador](getting-started-build-a-classifier.md)
