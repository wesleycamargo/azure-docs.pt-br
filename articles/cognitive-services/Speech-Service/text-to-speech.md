---
title: Sobre o texto em fala
description: Uma visão geral dos recursos de Conversão de Texto em Fala.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: d111a9f852b849df15dbd056a7210fac82cee190
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39324248"
---
# <a name="about-the-text-to-speech-api"></a>API de Conversão de Texto em Fala

Na API de TTS **(Conversão de Texto em Fala)** o serviço do Speech converte texto de entrada em fala de som natural (também nomeada *sintetização de voz*).

Para gerar fala, o aplicativo envia solicitações HTTP POST para o serviço do Speech. Lá, o texto é sintetizado em voz humana e retornado como um arquivo de áudio. Há suporte para uma variedade de vozes e idiomas.

Cenários nos quais a sintetização de voz está sendo adotada incluem:

* *Melhorando a acessibilidade:* a tecnologia de **Conversão de Texto em Fala** permite que editores e proprietários de conteúdo respondam às diferentes maneiras pelas quais as pessoas interagem com o conteúdo. Pessoas com deficiência visual ou dificuldades de leitura apreciam a capacidade de consumir conteúdo auditivamente. A saída de voz também facilita para as pessoas desfrutarem de conteúdo textual, como jornais ou blogs, em dispositivos móveis enquanto se deslocam ou se exercitam.

* *Respondendo em cenários multitarefas:* a **Conversão de Texto em Fala** permite que as pessoas absorvam informações importantes de maneira rápida e confortável durante a condução ou fora de um ambiente de leitura conveniente. A navegação é um aplicativo comum nesta área. 

* *Aprimorando a aprendizagem com vários modos:* pessoas diferentes aprendem melhor de maneiras diferentes. Especialistas em aprendizagem online mostraram que fornecer voz e texto juntos pode ajudar a tornar as informações mais fáceis de aprender e serem retidas.

* *Entregando assistentes ou bots intuitivos:* a capacidade de falar pode ser parte integrante de um chat bot inteligente ou um assistente virtual. Mais e mais empresas estão desenvolvendo chat bots para oferecer a seus clientes atrativas experiências de serviço de atendimento ao consumidor. A voz adiciona outra dimensão, permitindo que as respostas do bot sejam recebidas auditivamente (por exemplo, por telefone).

## <a name="voice-support"></a>Suporte de voz

A **Conversão de Texto em Fala** da Microsoft oferece mais de 75 vozes em mais de 45 idiomas e localidades. Para usar essas "fontes de voz" padrão, é necessário somente especificar o nome da voz com alguns outros parâmetros quando chamar a API REST do serviço. Para obter detalhes das vozes com suporte, consulte [Idiomas com suporte](https://docs.microsoft.com/azure/cognitive-services/speech-service/supported-languages#text-to-speech). 

Se você quiser uma voz exclusiva para o aplicativo, é possível criar [fontes de voz personalizada](how-to-customize-voice-font.md) a partir de suas próprias amostras de fala.

## <a name="next-steps"></a>Próximas etapas

* [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como sintetizador de fala por meio da API REST](how-to-text-to-speech.md)
