---
title: Visão geral do Serviço de Fala Personalizado no Azure | Microsoft Docs
description: O Serviço de Fala Personalizada é um serviço baseado em nuvem que permite aos usuários para personalizar modelos de fala para transcrição de fala em texto.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 02/07/2017
ms.author: panosper
ms.openlocfilehash: 00c9a89bea9deafe4096566032cc9685df8c111c
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55227991"
---
# <a name="what-is-custom-speech-service"></a>O que é o Serviço de Fala Personalizado?

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

O Serviço de Fala Personalizada é um serviço baseado em nuvem que permite aos usuários a capacidade de personalizar modelos de fala para transcrição de Fala em Texto.
Para usar o Serviço de Fala Personalizado, confira o [Portal do Serviço de Fala Personalizado](https://cris.ai).

O Serviço de Fala Personalizado permite que v você crie modelos de linguagem personalizados e modelos acústicos, personalizados para o seu aplicativo e seus usuários. Ao carregar sua fala específica e/ou dados de texto no Serviço de Fala Personalizada, você pode criar modelos personalizados que podem ser usados em conjunto com os modelos de fala atuais mais modernos da Microsoft.

Por exemplo, se você estiver adicionando interação de voz a um aplicativo de celular, tablet ou PC, você pode criar um modelo de linguagem personalizado que pode ser combinado com o modelo acústico da Microsoft para criar um ponto de extremidade de fala em texto desenvolvido especialmente para o seu aplicativo. Se o seu aplicativo tiver sido projetado para uso em um ambiente específico ou por uma população de usuários específica, você também poderá criar e implantar um modelo acústico personalizado com esse serviço.


## <a name="how-do-speech-recognition-systems-work"></a>Como funcionam os sistemas de reconhecimento de fala?
Os sistemas de reconhecimento de fala são compostos por vários componentes que funcionam em conjunto. Dois dos componentes mais importantes são o modelo acústico e o modelo de linguagem.

O modelo acústico é um classificador que rotula fragmentos de áudio curtos em um dos vários fonemas, ou unidades de som, em um determinado idioma. Por exemplo, a palavra "speech" é composta por quatro fonemas, "s p iy ch". Essas classificações são feitas aproximadamente 100 vezes por segundo.

O modelo de linguagem é uma distribuição de probabilidade por sequências de palavras. O modelo de linguagem ajuda o sistema a decidir entre sequências de palavras que soam parecidas, com base na probabilidade das próprias sequências de palavras. Por exemplo, “reconhecer fala” e “amolecer bala” soam parecido, mas a primeira hipótese é muito mais provável de ocorrer e, portanto, terá atribuída a ela uma pontuação mais elevada pelo modelo de linguagem.

Os modelos de linguagem e acústico são modelos estatísticos aprendidos com dados de treinamento. Como resultado, eles apresentam um desempenho melhor quando a fala que encontram, usados em aplicativos, é semelhante aos dados observados durante o treinamento. Os modelos acústico e de linguagem no mecanismo de Conversão de Fala em Texto da Microsoft foram treinados com uma coleção enorme de fala e de texto, e fornecem um desempenho de última geração para os cenários de uso mais comuns, por exemplo, na interação com o Cortana em seu smartphone, tablet ou PC, na pesquisa na Web por voz ou ditando mensagens de texto para um amigo.

## <a name="why-use-the-custom-speech-service"></a>Por que usar o Serviço de Fala Personalizado?
Embora o mecanismo de Conversão de Fala em Texto da Microsoft é de padrão internacional, ele é voltado para os cenários descritos acima. No entanto, se você espera que consultas de voz para seu aplicativo contenham itens de vocabulário específicos, como nomes de produto ou jargões que raramente ocorrem na fala comum, é provável que você possa obter desempenho aprimorado personalizando o modelo de linguagem.

Por exemplo, se você está compilando um aplicativo para pesquisar o MSDN por voz, é provável que termos como "orientado a objeto" ou "namespace" ou "dot net" apareçam com mais frequência do que em aplicativos de voz típicos. Personalizar o modelo de linguagem permitirá que o sistema aprenda isso.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como usar o Custom Speech Service, consulte o [Portal do Serviço de fala personalizado](https://cris.ai).

* [Introdução](cognitive-services-custom-speech-get-started.md)
* [Perguntas frequentes](cognitive-services-custom-speech-faq.md)
* [Glossário](cognitive-services-custom-speech-glossary.md)
