---
title: O que é a API de Tradução de Texto?
titlesuffix: Azure Cognitive Services
description: Integre a API de Tradução de Texto a seus aplicativos, sites, ferramentas e outras soluções para fornecer experiências de usuário em vários idiomas.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: overview
ms.date: 05/10/2018
ms.author: erhopf
ms.openlocfilehash: 6c89ff41531b130843eb288b98ffe7def1d8915e
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49645001"
---
# <a name="what-is-translator-text-api"></a>O que é a API de Tradução de Texto?

A API de Tradução de Texto pode ser integrada perfeitamente aos seus aplicativos, sites, ferramentas ou outras soluções para fornecer experiências de usuário em [mais de 60 idiomas](languages.md). Ela pode ser usada em qualquer plataforma de hardware e em qualquer sistema operacional para realizar a tradução do idioma texto a texto.

A API de Tradução de Texto faz parte da coleção de [APIs dos Serviços Cognitivos](https://docs.microsoft.com/azure/#pivot=products&panel=ai) do Azure de aprendizado de máquina e algoritmos de IA na nuvem, prontamente consumíveis nos projetos de desenvolvimento.

## <a name="about-microsoft-translator"></a>Sobre o Microsoft Translator

O Microsoft Translator é um serviço de tradução automática baseado em nuvem. No centro desse serviço estão a API de Tradução de Texto e a [API de Tradução de Fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-translation), que ativam vários produtos e serviços da Microsoft e são usadas por milhares de empresas no mundo todo em seus aplicativos e fluxos de trabalho, permitindo que o conteúdo alcance um público mundial.

A Tradução de Fala também está disponível por meio da [visualização de Fala de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/speech-service/), que combina a API de Tradução de Fala, a API de Fala do Bing e o Serviço de Fala Personalizado (versão prévia) existentes em um serviço unificado e totalmente personalizável.  

Saiba mais sobre o [serviço Microsoft Translator](https://www.microsoft.com/en-us/translator/home.aspx)

## <a name="language-customization"></a>Personalização de linguagem

Uma extensão do núcleo do serviço Microsoft Translator, o Tradutor Personalizado pode ser usado em conjunto com a API de Tradução de Texto para ajudá-lo a personalizar o sistema de tradução neural e aprimorar a tradução conforme sua terminologia e estilo específicos.

Com o Tradutor Personalizado, é possível compilar sistemas de tradução que controlem a terminologia usada na sua própria empresa ou setor. Assim, seu sistema de tradução personalizada será integrado facilmente aos aplicativos, fluxos de trabalho e sites existentes, e em vários tipos de dispositivos, por meio da API de Tradução de Texto regular da Microsoft, usando o parâmetro de categoria.

Saiba mais sobre a [personalização de idioma](customization.md)

## <a name="microsoft-translator-neural-machine-translation"></a>Tradução Automática Neural do Microsoft Translator

A NMT (Tradução automática neural) é o novo padrão para traduções automáticas de IA (inteligência artificial) de alta qualidade. Ela substitui a tecnologia herdada de SMT (tradução automática estatística), que atingiu um limite de qualidade em meados da década de 2010.

A NMT fornece traduções melhores que a SMT não apenas de um ponto de vista de pontuação de qualidade da tradução bruta, mas também porque ela soará mais fluente e humana. O motivo principal dessa fluidez é que a NMT usa o contexto completo de uma sentença para traduzir palavras. A SMT usava apenas o contexto imediato de algumas palavras presentes antes e depois de cada palavra.

Os modelos de NMT são a essência da API e não são visíveis aos usuários finais. A única diferença notável é uma melhor qualidade da tradução, especialmente para idiomas como o chinês, o japonês e o árabe.

Saiba mais sobre [como funciona a NMT](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="next-steps"></a>Próximas etapas

- Leia sobre [detalhes de preços](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

- [Inscreva-se](translator-text-how-to-signup.md) para obter uma chave de acesso.

- [Guia de início rápido](quickstarts/csharp.md) é um passo a passo das chamadas à API REST escritas em C#. Saiba como traduzir o texto de um idioma para outro com o mínimo de códigos.

- A [Documentação de referência de API](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference) fornece a documentação técnica para as APIs.

## <a name="see-also"></a>Consulte também

- [Página Documentação dos Serviços Cognitivos](https://docs.microsoft.com/azure/#pivot=products&panel=ai)
- [Página do Produto de Serviços Cognitivos](https://azure.microsoft.com/services/cognitive-services/)
- [Informações sobre a solução e preços](https://www.microsoft.com/en-us/translator/default.aspx)
