---
title: Sobre tradução de fala
description: Uma visão geral das funcionalidades de Tradução de Fala
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: erhopf
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 04/28/2018
ms.author: erhopf
ms.openlocfilehash: f3c22589270ccee2c60ca5ea5b15047d9c81fabe
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2018
ms.locfileid: "49165430"
---
# <a name="about-the-speech-translation-api"></a>Sobre a API de Tradução de Fala

A Microsoft Speech API permite adicionar tradução de ponta a ponta em tempo real e em vários idioma de fala a seus aplicativos, suas ferramentas e seus dispositivos. A mesma API pode ser usada para a tradução com conversão de fala em fala e de fala em texto.

Com a API de Tradução de Fala da Microsoft, aplicativos cliente transmitem por streaming áudio de fala para o serviço e recebem de volta um fluxo de resultados. Esses resultados incluem o texto reconhecido no idioma de origem e sua tradução para o idioma de destino. Traduções provisórias podem ser fornecidas até que um enunciado esteja concluído, momento em que a tradução final é fornecida.

Opcionalmente, uma versão de áudio sintetizada da tradução final pode ser preparada, possibilitando verdadeira tradução com conversão de fala em fala.

A API de Tradução de Fala usa o protocolo WebSockets para fornecer um canal de comunicação full duplex entre o cliente e o servidor. Mas você não precisa lidar com WebSockets; o SDK de Fala lida com isso para você.

A API de Tradução de Fala utiliza as mesmas tecnologias empregadas em vários produtos e serviços da Microsoft. Esse serviço já é usado por milhares de empresas em todo o mundo em seus aplicativos e fluxos de trabalho.

## <a name="about-the-technology"></a>Sobre a tecnologia

O mecanismo de tradução da Microsoft subjacente consiste em duas abordagens diferentes: SMT (tradução automática estatística) e NMT (tradução automática neural). Esta última, uma abordagem de inteligência artificial empregando redes neurais, é a abordagem mais moderna à tradução automática. A NMT fornece traduções melhores – não apenas mais precisas, mas também mais fluentes e naturais. O motivo principal dessa fluidez é que a NMT usa o contexto completo de uma sentença para traduzir palavras.

Hoje, Microsoft migrou para NMT para os idiomas mais populares, empregando SMT somente para idiomas usados com menor frequência. Todos os [idiomas disponíveis para tradução de fala em fala](language-support.md#speech-translation) também são alimentadas por NMT. A tradução com conversão de fala em texto pode usar SMT ou NMT, dependendo do par de idiomas. Se o idioma de destino é compatível com a NMT, a tradução completa é ativada pela NMT. Se o idioma de destino não é compatível com a NMT, a tradução é uma combinação da NMT e da SMT usando o inglês como o "pivô" entre os dois idiomas.

As diferenças entre os modelos são internas ao mecanismo de tradução. Os usuários observam apenas a qualidade da tradução melhor, especialmente para chinês, japonês e árabe.

> [!NOTE]
> Ficou interessado em saber mais sobre a tecnologia por trás do mecanismo de tradução da Microsoft? Veja [Tradução automática](https://www.microsoft.com/en-us/translator/mt.aspx).

## <a name="next-steps"></a>Próximas etapas

* [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como a tradução de fala em C#](how-to-translate-speech-csharp.md)
* [Veja como a tradução de fala em C++](how-to-translate-speech-cpp.md)
* [Veja como a tradução de fala em Java](how-to-translate-speech-java.md)
