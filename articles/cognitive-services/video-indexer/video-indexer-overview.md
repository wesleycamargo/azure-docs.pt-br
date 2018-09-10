---
title: Visão geral do Video Indexer do Azure | Microsoft Docs
description: Este tópico oferece uma visão geral do serviço de Video Indexer.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: overview
ms.date: 07/25/2018
ms.author: nolachar
ms.openlocfilehash: f52c4af29d0c7de8b5edbe869640ffc5dddb5c5e
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397884"
---
# <a name="what-is-video-indexer-preview"></a>O que é o Video Indexer? (versão prévia)

Video Indexer é um aplicativo de nuvem criado usando a Análise de Mídia do Azure, Serviços Cognitivos (por exemplo, API de Detecção Facial, Microsoft Translator, API da Pesquisa Visual Computacional e Serviço de Fala Personalizado) e Azure Search. Ele permite que você extraia as seguintes informações de seus vídeos usando tecnologias de inteligência artificial:

- **Detecção automática de idioma**: o Video Indexer pode detectar automaticamente o idioma do vídeo. A detecção automática de idioma atualmente dá suporte ao inglês, espanhol, francês, alemão, italiano, chinês (simplificado), japonês e russo. Fará fallback para o inglês quando o idioma não puder ser detectado.
- **Transcrição de áudio**: o Video Indexer tem a funcionalidade de conversão da fala em texto, permitindo que os clientes obtenham uma transcrição das palavras faladas. Os idiomas com suporte incluem o inglês, espanhol, francês, alemão, italiano, chinês (simplificado), português (Brasil), japonês e russo (com muitos outros disponíveis no futuro). 
- **Acompanhamento facial e identificação**: as tecnologias de detecção facial permitem detectar faces em um vídeo. As faces detectadas são comparadas com um banco de dados de celebridades para avaliar quais estão presentes no vídeo. Os clientes também podem rotular as faces que não correspondem a uma celebridade. O Video Indexer cria um modelo de detecção facial baseado nesses rótulos e poderão reconhecer as faces em vídeos enviados no futuro.
- **Indexação de locutores**: o Video Indexer tem a capacidade de mapear e entender quem falou quais palavras e quando.
- **Reconhecimento de texto visual**: com essa tecnologia, o serviço do Video Indexer extrai o texto que é exibido nos vídeos.  
- **Detecção de atividades de voz**: a detecção permite que o Video Indexer separe a atividade de ruído e voz em segundo plano. 
- **Detecção de cena**: o Video Indexer tem a capacidade de realizar a análise visual no vídeo para determinar quando uma cena é alterado em um vídeo.
- **Extração de quadro-chave**: o Video Indexer detecta automaticamente os quadros-chave em um vídeo. 
- **Análise de sentimento**: o Video Indexer realiza uma análise de sentimento no texto extraído usando a conversão de fala em texto e o reconhecimento ótico de caracteres, fornecendo essas informações na forma de sentimentos positivos, negativos ou neutros, junto com códigos do tempo.
- **Tradução**: o Video Indexer tem a capacidade de traduzir a transcrição de áudio de um idioma em outro. Os seguintes idiomas têm suporte: inglês, espanhol, francês, alemão, italiano, chinês simplificado, português do Brasil, japonês e russo. Depois de traduzidos, o usuário ainda pode obter legendas no player de vídeo em outros idiomas.
- **Moderação de conteúdo visual**: essa tecnologia permite a detecção de material para adultos e/ou obsceno presente no vídeo e pode ser usada para filtrar o conteúdo. 
- **Extração de palavras-chave**: o Video Indexer extrai palavras-chave com base na transcrição das palavras faladas e no texto identificado pelo reconhecedor de texto visual.
- **Rótulos**: o Video Indexer fornece rótulos para objetos visuais, como gato, cachorro, tabela, carro, bem como ações, como aguardando, executando ou voando.
- **Marcas**: o Video Indexer extrai marcas comerciais com base na transcrição das palavras faladas e no texto identificado pelo reconhecedor de texto visual.

Depois que o Video Indexer terminar de processar e analisar, você poderá examinar, coletar, pesquisar e publicar as informações do vídeo.

Se sua função for um gerenciador de conteúdo ou um desenvolvedor, o serviço do Video Indexer poderá atender às suas necessidades. Os gerenciadores de conteúdo podem usar o portal da Web do Video Indexer para consumir o serviço sem escrever uma única linha de código; consulte [Começar a usar o portal do Video Indexer](video-indexer-get-started.md). Os desenvolvedores podem aproveitar as APIs para processar o conteúdo em grande escala; consulte [Usar API REST do Video Indexer](video-indexer-use-apis.md). O serviço também permite que os clientes usem widgets para publicar transmissões de vídeo e informações extraídas em seus próprios aplicativos; consulte [Inserir widgets visuais em seu aplicativo](video-indexer-embed-widgets.md).

Você pode se inscrever no serviço usando a conta existente do AAD, LinkedIn, Facebook, Google ou MSA. Para obter mais informações, consulte o [guia de introdução](video-indexer-get-started.md).

## <a name="scenarios"></a>Cenários

Abaixo estão alguns cenários em que o Video Indexer pode ser útil

- Pesquisa: as informações extraídas do vídeo podem ser usadas para aprimorar a experiência de pesquisa em uma biblioteca de vídeos. Por exemplo, a indexação das palavras faladas e faces pode habilitar a experiência de pesquisa para localizar momentos em um vídeo em que determinada pessoa falou certas palavras ou quando duas pessoas foram vistas junto. A pesquisa com base em tais informações de vídeos é aplicável a agências de notícias, instituições de ensino, emissoras, proprietários de conteúdo de entretenimento, aplicativos de linha de negócios e, em geral, a qualquer setor que tenha uma biblioteca de vídeos que os usuários precisam pesquisar.

- Monetização: o Video Indexer pode ajudar a melhorar o valor dos vídeos. Por exemplo, setores que contam com a receita de anúncios (como a mídia de notícias, mídias sociais etc.) podem entregar anúncios mais relevantes usando as informações extraídas como sinais adicionais para o servidor do anúncio (apresentar um anúncio de sapatos esportivos é mais relevante durante uma partida de futebol do que em uma competição de natação).

- Participação do usuário: as informações do vídeo podem ser usadas para melhorar a participação do usuário posicionando os momentos do vídeo relevantes para os usuários. Por exemplo, considere um vídeo educativo que explica as esferas nos primeiros 30 minutos e as pirâmides nos próximos 30 minutos. Um aluno que lê sobre as pirâmides aproveitaria mais se o vídeo fosse posicionado para começar a partir do marcador de 30 minutos.

Para obter mais informações, consulte este [blog](http://aka.ms/videoindexerblog).

## <a name="next-steps"></a>Próximas etapas

Você está pronto para começar com o Video Indexer. Para obter mais informações, consulte os seguintes artigos:

- [Introdução ao portal do Video Indexer](video-indexer-get-started.md)
- [Processar o conteúdo com a API REST do Video Indexer](video-indexer-use-apis.md)
- [Inserir widgets visuais em seu aplicativo](video-indexer-embed-widgets.md)
