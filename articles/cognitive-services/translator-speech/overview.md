---
title: Documentação da API de Tradução de Fala | Microsoft Docs
titleSuffix: Cognitive Services
description: Use a API de Tradução de Fala do Microsoft Translator para adicionar a conversão de fala em fala e de fala em texto aos seus aplicativos.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jansko
ms.openlocfilehash: 15f27e6b5b2fd7384958a660156855fc65f4e558
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363701"
---
# <a name="microsoft-translator-speech-api"></a>API de Tradução de Fala da Microsoft
A API de Tradução de Fala da Microsoft pode ser usada para adicionar traduções de fala completas e em tempo real a aplicativos, ferramentas ou a qualquer solução que exige a tradução de fala em vários idiomas, seja qual for o sistema operacional ou as linguagens de desenvolvimento de destino. A API pode ser usada para a conversão de fala em fala e de fala em texto.

A API de Tradução de Texto da Microsoft é um serviço do Azure, parte da [coleção de APIs dos Serviços Cognitivos da Microsoft](https://docs.microsoft.com/azure/#pivot=products&panel=cognitive) de aprendizado de máquina e algoritmos de IA na nuvem, prontamente consumíveis nos projetos de desenvolvimento.

Com a API de Tradução de Fala da Microsoft, os aplicativos cliente transmitem um áudio de fala para o serviço e recebem um fluxo de resultados baseados em texto e áudio, que incluem o texto reconhecido no idioma de origem e sua tradução no idioma de destino. Os resultados de texto são produzidos pela aplicação do ASR (Reconhecimento Automático de Fala) ativado por redes neurais profundas no fluxo de áudio de entrada. A saída bruta do ASR é aprimorada ainda por uma nova técnica chamada TrueText para refletir melhor a intenção do usuário. Por exemplo, o TrueText remove disfluências (hesitações e pigarros), palavras repetidas e restaura pontuação e uso adequados de maiúsculas. A capacidade de mascarar ou excluir linguagem vulgar também está incluída. Os mecanismos de reconhecimento e tradução são especificamente treinados para lidar com a fala conversacional. 

O serviço de Tradução de Fala usa a detecção de silêncio para determinar o fim de um enunciado. Após uma pausa na atividade de voz, o serviço transmitirá novamente um resultado final para o enunciado concluído. O serviço também pode enviar de volta resultados parciais, que fornecem traduções e reconhecimentos intermediários de um enunciado em andamento. 

Para a tradução de fala em fala, o serviço fornece a capacidade de sintetizar a fala (texto em fala) do texto falado nos idiomas de destino. O áudio de conversão de texto em fala é criado no formato especificado pelo cliente. Os formatos WAV e MP3 estão disponíveis.

A API de Tradução de Fala usa o protocolo WebSocket para fornecer um canal de comunicação full duplex entre o cliente e o servidor. 

## <a name="about-microsoft-translator"></a>Sobre o Microsoft Translator
O Microsoft Translator é um serviço de tradução automática baseado em nuvem. No centro desse serviço estão a [API de Tradução de Texto] (https://www.microsoft.com/en-us/translator/translatorapi.aspx) e a API de Tradução de Fala, que ativam vários produtos e serviços da Microsoft e são usadas por milhares de empresas no mundo todo em seus aplicativos e fluxos de trabalho, permitindo que o conteúdo alcance um público mundial.

Saiba mais sobre o [serviço Microsoft Translator](https://www.microsoft.com/en-us/translator/home.aspx)

## <a name="microsoft-translator-neural-machine-translation-nmt"></a>NMT (Tradução Automática Neural) do Microsoft Translator
A API de Tradução de Fala da Microsoft fala usa a SMT (tradução automática estatística herdada) e a mais recente NMT (tradução automática neural) para fornecer traduções.

A tradução automática estatística atingiu uma estabilidade em termos de melhoria de desempenho. A qualidade da tradução não está mais melhorando de nenhuma maneira significativa nos sistemas genéricos com SMT. Uma nova tecnologia de tradução baseada em IA ganha força com base na NN (Redes Neurais).

A NMT fornece melhores traduções não apenas de um ponto de vista de pontuação de qualidade da tradução bruta, mas também porque soa mais fluente e mais humana comparado à SMT. O motivo principal dessa fluidez é que a NMT usa o contexto completo de uma sentença para traduzir palavras. A SMT usa apenas o contexto imediato de algumas palavras antes e depois de cada palavra.

Os modelos de NMT são a essência da API e não são visíveis aos usuários finais. As únicas diferenças perceptíveis são:
* Uma melhor qualidade da tradução, especialmente para idiomas como o chinês, o japonês e o árabe
* A incompatibilidade com os recursos de personalização de Hub existentes (para uso com a API de Tradução de Texto da Microsoft)

Todos os idiomas de tradução de fala compatíveis são ativados pela NMT. Portanto, toda a tradução de fala em fala usa a NMT. 

A conversão de fala em texto pode usar uma combinação de NMT e SMT, dependendo do par de idiomas. Se o idioma de destino é compatível com a NMT, a tradução completa é ativada pela NMT. Se o idioma de destino não é compatível com a NMT, a tradução é uma combinação da NMT e da SMT usando o inglês como o "pivô" entre os dois idiomas. 

Veja os idiomas compatíveis em [Microsoft.com](https://www.microsoft.com/en-us/translator/languages.aspx). 

Saiba mais sobre [como funciona a NMT](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Inscrever-se](translator-speech-how-to-signup.md)

> [!div class="nextstepaction"]
> [Iniciar codificação](quickstarts/csharp.md)

## <a name="see-also"></a>Consulte também
- [Página Documentação dos Serviços Cognitivos](https://docs.microsoft.com/azure/#pivot=products&panel=cognitive)
- [Página do Produto de Serviços Cognitivos](https://azure.microsoft.com/services/cognitive-services/)
- [Informações sobre a solução e preços](https://www.microsoft.com/en-us/translator/home.aspx) 
