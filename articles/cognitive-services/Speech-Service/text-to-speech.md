---
title: Sobre a conversão de texto em fala – Serviço de Fala
titleSuffix: Azure Cognitive Services
description: A API de Conversão de Texto em Fala oferece mais de 75 vozes em mais de 45 idiomas e localidades. Para usar fontes de voz padrão, é necessário somente especificar o nome da voz com alguns outros parâmetros ao chamar o Serviço de Fala.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 0836ae4a9041db27cfed35dd0f1fc0df6e541aff
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55859327"
---
# <a name="about-the-text-to-speech-api"></a>Sobre a API de conversão de texto em fala

A API de **TTS** (conversão de texto em fala) converte o texto de entrada em fala com fluência som natural (também chamada *sintetização de fala*).

Para gerar a fala, o aplicativo envia solicitações HTTP POST para a API de conversão de texto em fala. Lá, o texto é sintetizado em voz humana e retornado como um arquivo de áudio. Há suporte para uma variedade de vozes e idiomas.

Cenários nos quais a sintetização de voz está sendo adotada incluem:

* *Melhorando a acessibilidade:* a tecnologia de **conversão de texto em fala** permite que editores e proprietários de conteúdo respondam às diferentes maneiras pelas quais as pessoas interagem com o conteúdo. Pessoas com deficiência visual ou dificuldades de leitura apreciam a capacidade de consumir conteúdo auditivamente. A saída de voz também facilita para as pessoas desfrutarem de conteúdo textual, como jornais ou blogs, em dispositivos móveis enquanto se deslocam ou se exercitam.

* *Respondendo em cenários multitarefas:* a **conversão de texto em fala** permite que as pessoas absorvam informações importantes de maneira rápida e confortável durante a condução ou fora de um ambiente de leitura conveniente. A navegação é um aplicativo comum nesta área.

* *Aprimorando o aprendizado com vários modos:* diferentes pessoas aprendem melhor de maneiras diferentes. Especialistas em aprendizagem online mostraram que fornecer voz e texto juntos pode ajudar a tornar as informações mais fáceis de aprender e serem retidas.

* *Fornecimento de bots ou assistentes intuitivos:* a capacidade de falar pode ser parte integrante de um chat bot inteligente ou um assistente virtual. Mais e mais empresas estão desenvolvendo chat bots para oferecer a seus clientes atrativas experiências de serviço de atendimento ao consumidor. A voz adiciona outra dimensão, permitindo que as respostas do bot sejam recebidas auditivamente (por exemplo, por telefone).

## <a name="voice-support"></a>Suporte de voz

A **Conversão de Texto em Fala** da Microsoft oferece mais de 75 vozes em mais de 45 idiomas e localidades. Para usar essas "fontes de voz" padrão, é necessário somente especificar o nome da voz com alguns outros parâmetros quando chamar a API REST do serviço. Para obter mais informações sobre os idiomas, localidades e vozes com suporte, consulte [idiomas com suporte](language-support.md#text-to-speech).

> [!IMPORTANT]
> Os custos variam para vozes padrão, personalizadas e neurais. Para saber mais, consulte [Preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="neural-voices"></a>Vozes neurais

A conversão de texto em fala neural pode ser usada para interagir com chatbots e assistentes virtuais de forma mais natural e participativa para converter textos digitais, como livros eletrônicos, em audiolivros, e aprimorar sistemas de navegação de carros. Com a prosódia natural semelhante à humana e a articulação clara das palavras, a TTS neural reduz significativamente a fadiga de escuta quando os usuários interagem com sistemas de inteligência artificial. Para obter mais informações sobre as vozes neurais, consulte [idiomas com suporte](language-support.md#text-to-speech).

### <a name="custom-voices"></a>Vozes personalizadas

A personalização da voz da conversão de texto em fala permite que você crie uma voz exclusiva e reconhecível para sua marca: uma: *fonte de voz.* Para criar a fonte de voz, você faz uma gravação em estúdio e carrega os scripts associados como os dados de treinamento. Em seguida, o serviço cria um modelo de voz exclusivo ajustado para a gravação. É possível usar essa fonte de voz para a sintetização de voz. Para obter mais informações, consulte [fontes de voz personalizadas](how-to-customize-voice-font.md).

## <a name="api-capabilities"></a>Recursos de API

Muitos dos recursos da API de **conversão de texto em fala**, especialmente em torno da personalização, estão disponíveis via REST. A tabela a seguir resume os recursos de cada método de acesso à API. Para obter uma lista completa de recursos e detalhes da API, consulte [referência ao Swagger](https://westus.cris.ai/swagger/ui/index).

| Caso de uso | REST | SDKs |
|-----|-----|-----|----|
| Carregar conjuntos de dados para adaptação de voz | Sim | Não  |
| Criar e gerenciar modelos de fontes de voz | Sim | Não  |
| Criar e gerenciar implantações de fontes de voz | Sim | Não  |
| Criar e gerenciar testes de fontes de voz| Sim | Não  |
| Gerenciar Assinaturas | Sim | Não  |

> [!NOTE]
> A API implementa limitação que limita as solicitações de API a 25 por 5 segundos. Cabeçalhos de mensagens informarão sobre os limites.

## <a name="next-steps"></a>Próximas etapas

* [Obter uma assinatura gratuita dos Serviços de Fala](https://azure.microsoft.com/try/cognitive-services/)
* [Início Rápido: converter texto em fala, Python](quickstart-python-text-to-speech.md)
* [Início Rápido: converter texto em fala, .NET Core](quickstart-dotnet-text-to-speech.md)
* [Referência da API REST](rest-apis.md)
