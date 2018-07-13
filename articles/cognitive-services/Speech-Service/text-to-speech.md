---
title: Sobre Conversão de Texto em Fala | Microsoft Docs
description: Uma visão geral dos recursos de Conversão de Texto em Fala.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 84baf03c83bb63883b80982056cdf6e1e25b3fb7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364768"
---
# <a name="about-the-text-to-speech-api"></a>API de Conversão de Texto em Fala

Na API de TTS **(Conversão de Texto em Fala)** o serviço do Speech converte texto de entrada em fala de som natural (também nomeada *	sintetização de voz*).

Para gerar fala, o aplicativo envia solicitações HTTP POST para o serviço do Speech. Lá, o texto é sintetizado em voz humana e retornado como um arquivo de áudio. Há suporte para uma variedade de vozes e idiomas.

Cenários nos quais a sintetização de voz está sendo adotada incluem:

* *Melhorando a acessibilidade:* a tecnologia de **Conversão de Texto em Fala** permite que editores e proprietários de conteúdo respondam às diferentes maneiras pelas quais as pessoas interagem com o conteúdo. Pessoas com deficiência visual ou dificuldades de leitura apreciam a capacidade de consumir conteúdo auditivamente. A saída de voz também facilita para as pessoas desfrutarem de conteúdo textual, como jornais ou blogs, em dispositivos móveis enquanto se deslocam ou se exercitam.

* *Respondendo em cenários multitarefas:* a **Conversão de Texto em Fala** permite que as pessoas absorvam informações importantes de maneira rápida e confortável durante a condução ou fora de um ambiente de leitura conveniente. A navegação é um aplicativo comum nesta área. 

* *Aprimorando a aprendizagem com vários modos:* pessoas diferentes aprendem melhor de maneiras diferentes. Especialistas em aprendizagem online mostraram que fornecer voz e texto juntos pode ajudar a tornar as informações mais fáceis de aprender e serem retidas.

* *Entregando assistentes ou bots intuitivos:* a capacidade de falar pode ser parte integrante de um chat bot inteligente ou um assistente virtual. Mais e mais empresas estão desenvolvendo chat bots para oferecer a seus clientes atrativas experiências de serviço de atendimento ao consumidor. A voz adiciona outra dimensão, permitindo que as respostas do bot sejam recebidas auditivamente (por exemplo, por telefone).

## <a name="voice-support"></a>Suporte de voz

A **Conversão de Texto em Fala** da Microsoft oferece mais de 75 vozes em mais de 45 idiomas e localidades. Para usar essas "fontes de voz" padrão, é necessário somente especificar o nome da voz com alguns outros parâmetros quando chamar a API REST do serviço. Para obter detalhes das vozes com suporte, consulte [Idiomas com suporte](supported-languages.md). 

Se você quiser uma voz exclusiva para o aplicativo, é possível criar [fontes de voz personalizada](how-to-customize-voice-font.md) a partir de suas próprias amostras de fala.

## <a name="next-steps"></a>Próximas etapas

* [Obter a assinatura de avaliação do Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Saiba como reconhecer fala em C#](quickstart-csharp-windows.md)
