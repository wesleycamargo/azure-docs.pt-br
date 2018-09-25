---
title: Medidores e cotas do Serviço de Fala Personalizada no Azure | Microsoft Docs
description: Informações sobre medidores e cotas do Serviço de Fala Personalizada no Azure.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 0ddd6274051fed5de86a88270a0dcc2e8288d885
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974520"
---
# <a name="custom-speech-service-meters-and-quotas"></a>Medidores e cotas do Serviço de Fala Personalizada

Com o Serviço de Fala Personalizada baseado em nuvem, você pode personalizar modelos de fala para transcrição de fala em texto.

Para começar a usar o Serviço de Fala Personalizada, vá para o [portal do Serviço de Fala Personalizado](https://cris.ai).

Para os medidores de preços atuais, vá para a página [Preços do Serviços Cognitivos para Serviço de Fala Personalizada](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/).

## <a name="tiers-explained"></a>Camadas explicadas
Para testar e protótipo apenas, desejamos usar a camada F0 gratuita. Para sistemas de produção, propomos usar a camada S2. Usando a camada S2, você pode dimensionar sua implantação para o número de unidades de escala (SUs) que seu cenário requer.

> [!NOTE]
> Você *não pode* migrar entre as camadas F0 e S2.
>

## <a name="meters-explained"></a>Medidores explicadas

### <a name="scale-out"></a>Expansão
Expansão é um novo recurso que lançamos com o novo modelo de preços. Ao usar a Expansão, você pode controlar o número de solicitações simultâneas que seu modelo pode processar.

Você pode definir solicitações simultâneas usando a medida SU na exibição **Criar modelo de implantação**. Para obter mais informações, veja [Criar um ponto de extremidade fala em texto personalizado](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-endpoint.md). Dependendo da quantidade de tráfego que você prevê que o modelo consuma, você pode escolher um número apropriado de SUs. 

> [!NOTE]
> Cada unidade de escala garante 5 solicitações simultâneas. Você pode comprar ou mais SUs 1, conforme apropriado. Como o número de SUs aumenta em incrementos de 1, o número de solicitações simultâneas é garantido para aumentar em incrementos de 5.
>

### <a name="log-management"></a>Gerenciamento de Log
Você pode optar por desativar rastreamentos de áudio para um modelo implantado recentemente a um custo adicional. O Serviço de Fala Personalizada não registra as solicitações de áudio ou as transcrições do modelo.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre como usar o Serviço de Fala Personalizada, vá para o [portal do Serviço de Fala Personalizada](https://cris.ai).

* [Introdução](cognitive-services-custom-speech-get-started.md)
* [Perguntas frequentes](cognitive-services-custom-speech-faq.md)
* [Glossário](cognitive-services-custom-speech-glossary.md)
 