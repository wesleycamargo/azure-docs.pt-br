---
title: Perguntas frequentes sobre o serviço conversão de fala em texto no Azure
description: Aqui estão as respostas para as perguntas mais populares sobre o Speech to Text.
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: 8d70c4a359c713d6c5f46423193e9c9e7e1f3baf
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282850"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Perguntas Frequentes sobre a Conversão de Texto em Fala

Se você não encontrar respostas para suas perguntas nas perguntas frequentes, verifique as outras opções de suporte [aqui](support.md).

## <a name="general"></a>Geral

**Pergunta**: Qual é a diferença entre os modelos de voz padrão e personalizado?

**Resposta**: Os modelos de voz padrão (também conhecido como, fontes de voz) foram treinados com dados de propriedade da Microsoft e já estão implantados na nuvem. Os modelos de voz personalizados permitem que o usuário adapte um modelo médio e transfira o timbre e a forma de expressão de acordo com o estilo de voz do falante ou treine um modelo totalmente novo com base nos dados de treinamento preparados pelo usuário. Atualmente, cada vez mais clientes querem ter uma voz exclusiva e de marca para seus bots. A plataforma de compilação de voz personalizada é a escolha certa para isso.

**Pergunta**: Por onde começar, se eu quiser usar um modelo de voz padrão?

**Resposta**: Mais de 80 modelos de voz padrão em mais de 45 idiomas estão disponíveis através de solicitações HTTP. Primeiro, é necessário obter uma [Chave de assinatura](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started). Para fazer chamadas REST para os modelos de voz implantados previamente, consulte os [detalhes aqui](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#text-to-speech).

**Pergunta**: Se eu quiser usar um modelo de voz personalizado, a API será a mesma das vozes padrão?

**Resposta**: Quando você cria e implanta o modelo de voz personalizado, obtém um ponto de extremidade exclusivo para o modelo. Para usar a voz para falar nos aplicativos, será necessário especificar o ponto de extremidade nas solicitações HTTP. A mesma funcionalidade disponível por meio da API REST para o serviço Conversão de Texto em Fala também estará disponível para o ponto de extremidade personalizado. Saiba como [criar e usar o ponto de extremidade personalizado](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-endpoint).

**Pergunta**: É necessário preparar os dados de treinamento para criar modelos de voz personalizados por conta própria?

**Resposta**: Você mesmo precisará preparar os dados de treinamento. Uma coleção de dados de fala é necessária para criar um modelo de voz personalizado. Esta coleção consiste em um conjunto de arquivos de áudio de gravações de fala e um arquivo de texto da transcrição de cada arquivo de áudio. O resultado da voz digital depende muito da qualidade dos dados de treinamento. Para produzir uma boa voz TTS, é importante que as gravações sejam feitas em uma sala silenciosa com um microfone de alta qualidade. Volume consistente, velocidade de fala, densidade fala e até a consistência nos maneirismos expressivos da fala são essenciais para compilação de uma excelente voz digital. É altamente recomendável que você tenha as vozes gravadas em um estúdio de gravação.
Atualmente, não damos suporte para gravação online nem recomendações sobre estúdios de gravação. Para o requisito de formato, consulte [como preparar gravações e transcrições](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/how-to-customize-voice-font#prepare-recordings-and-transcripts)
 
**Pergunta**: Quais scripts devo usar para gravar os dados de fala para treinamento de voz personalizado? 

**Resposta**: Nós não limitamos os scripts para gravação de voz. Você pode usar seus próprios scripts para gravar a fala. Apenas garanta que você tenha cobertura fonética suficiente nos seus dados de fala. Para treinar uma voz personalizada, você pode começar com um pequeno volume de dados de fala, que podem ter 50 frases diferentes (cerca de 3 a 5 minutos de fala). Quanto mais dados você fornecer, mais natural será a voz. Você pode começar a treinar uma fonte de voz completa ao fornecer gravações de mais de 2000 frases (cerca de 3 a 4 horas de fala). Para obter uma voz completa de alta qualidade, será necessário preparar gravações de mais de 6000 frases (cerca de 8 a 10 horas de fala).  
Nós fornecemos serviços adicionais para ajudá-lo a preparar scripts para gravação. Contate o [atendimento ao cliente de Voz Personalizada](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) para consultas.

**Pergunta**: E se eu precisar de maior simultaneidade que o valor padrão ou do que é oferecido no portal?

**Resposta**: você pode dimensionar o modelo em incrementos de 20 solicitações simultâneas. Contate [atendimento ao cliente de Voz Personalizada](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) para consultas sobre maior 	colocação em escala.

**Pergunta**: Posso baixar meu modelo e executá-lo localmente?

**Resposta**: modelos não podem ser baixados e executados localmente.

## <a name="next-steps"></a>Próximas etapas

* [Solução de problemas](troubleshooting.md)
* [Notas de versão](releasenotes.md)
