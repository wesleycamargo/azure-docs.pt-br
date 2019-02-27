---
title: O que é o Video Indexer?
titlesuffix: Azure Media Services
description: Este tópico oferece uma visão geral do serviço de Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/17/2019
ms.author: juliako
ms.openlocfilehash: c1c81ba0f168759ab9cc05f3441e07ff03275e84
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56417373"
---
# <a name="what-is-video-indexer"></a>O que é o Video Indexer?

O Video Indexer do Azure é um aplicativo de nuvem criado com base na Análise de Mídia do Azure, no Azure Search, nos Serviços Cognitivos (como a API de Detecção Facial, o Microsoft Translator, a API da Pesquisa Visual Computacional e o Serviço de Fala Personalizado). Ele permite que você extraia os insights de seus vídeos usando os modelos de áudio e vídeo do Video Indexer descritos abaixo:
  
## <a name="video-insights"></a>Insights para vídeos

- **Detecção facial**: Detecta e agrupa rostos que aparecem no vídeo.
- **Identificação de celebridade**: O Video Indexer identifica automaticamente mais de 1 milhão de celebridades, tais como líderes mundiais, atores e atrizes, atletas, pesquisadores, líderes dos negócios e do setor de tecnologia em todo o mundo. Os dados sobre esses celebridades também podem ser encontrados em diversos sites famosos, assim como IMDB e Wikipédia.
- **Identificação de face baseada em conta**: O Video Indexer treina um modelo para uma conta específica. Em seguida, ele reconhece rostos no vídeo com base no modelo treinado especificamente para vídeos nessa conta.
- **Extração em miniatura para rosto** ("melhor rosto"): Identifica automaticamente o melhor rosto capturado em cada grupo de rostos (com base na qualidade, no tamanho e na posição frontal) e o extrai como um ativo de imagem.
- **Reconhecimento de texto visual** (OCR): Extrai o texto que é exibido visualmente no vídeo.
- **Moderação de conteúdo visual**: Detecta visuais somente para adultos e/ou eróticos.
- **Identificação de rótulos**: Identifica objetos visuais e ações exibidas.
- **Detecção de captura**: Determina quando uma cena é alterada no vídeo.
- **Detecção de quadro preto**: Identifica quadros pretos apresentados no vídeo.
- **Extração de quadro-chave**: Detecta quadros-chave estáveis em um vídeo.

## <a name="audio-insights"></a>Insights de áudio

- **Detecção automática de idioma**: Identifica automaticamente o idioma falado dominante. Os idiomas com suporte incluem inglês, espanhol, francês, alemão, italiano, chinês (simplificado), japonês, russo e português (brasileiro). Fará fallback para o inglês quando o idioma não puder ser detectado.
- **Transcrição de áudio**: Conversão de fala em texto em 12 idiomas e permite extensões. Os idiomas com suporte incluem inglês, espanhol, francês, alemão, italiano, chinês (simplificado), japonês, árabe, russo e português (brasileiro), hindi e coreano.
- **Legendagem oculta**: Cria legendas ocultas em três formatos: VTT, TTML, SRT.
- **Processamento de dois canais**: Detecta automaticamente, separa a transcrição e mescla em uma única linha do tempo.
- **Redução de ruído**: Limpa gravações com ruídos ou áudio com telefonia (com base nos filtros do Skype).
- **Personalização de transcrição** (CRIS): Treina e executa modelos personalizados estendidos de conversão de fala em texto para criar transcrições específicas do setor.
- **Enumeração de alto-falante**: Mapeia e entende qual locutor disse quais palavras e quando.
- **Estatísticas de alto-falante**: Fornece estatísticas das taxas de fala dos alto-falantes.
- **Moderação de conteúdo visual**: Detecta texto explícito na transcrição de áudio.
- **Efeitos de áudio**: Identifica efeitos de áudio como palmas, fala e silêncio.
- **Detecção de emoções**: Identifica emoções com base em indicações de fala e de áudio. A emoção pode ser: alegria, tristeza, raiva ou medo.
- **Tradução**: Cria traduções da transcrição de áudio em 54 idiomas diferentes.

## <a name="audio-and-video-insights-multi-channels"></a>Insights de áudio e vídeo (vários canais)

Ao fazer a indexação por um canal, o resultado parcial desses modelos estará disponível

- **Extração de palavra-chave**: Extrai palavras-chave de falas e de textos visuais.
- **Extração de marcas**: Extrai marcas de falas e de textos visuais.
- **Inferência do tópico**: Faz inferências dos principais tópicos das transcrições. A taxonomia de IPTC de 1º nível está incluída.
- **Artefatos**: Extrai um conjunto avançado de artefatos com o "próximo nível de detalhes" para cada um dos modelos.
- **Análise de Sentimento**: Identifica sentimentos positivos, negativos e neutros de falas e de textos visuais.
 
  
 
Depois que o Video Indexer terminar de processar e analisar, você poderá examinar, coletar, pesquisar e publicar as informações do vídeo.

Se sua função for um gerenciador de conteúdo ou um desenvolvedor, o serviço do Video Indexer poderá atender às suas necessidades. Os gerenciadores de conteúdo podem usar o portal da Web do Video Indexer para consumir o serviço sem escrever uma única linha de código; confira [Começar a usar o site do Video Indexer](video-indexer-get-started.md). Os desenvolvedores podem aproveitar as APIs para processar o conteúdo em grande escala; consulte [Usar API REST do Video Indexer](video-indexer-use-apis.md). O serviço também permite que os clientes usem widgets para publicar transmissões de vídeo e informações extraídas em seus próprios aplicativos; consulte [Inserir widgets visuais em seu aplicativo](video-indexer-embed-widgets.md).

Você pode se inscrever no serviço usando a conta existente do AAD, LinkedIn, Facebook, Google ou MSA. Para obter mais informações, consulte o [guia de introdução](video-indexer-get-started.md).

## <a name="scenarios"></a>Cenários

Abaixo estão alguns cenários em que o Video Indexer pode ser útil

- Pesquisa: as informações extraídas do vídeo podem ser usadas para aprimorar a experiência de pesquisa em uma biblioteca de vídeos. Por exemplo, a indexação das palavras faladas e faces pode habilitar a experiência de pesquisa para localizar momentos em um vídeo em que determinada pessoa falou certas palavras ou quando duas pessoas foram vistas junto. A pesquisa com base em tais informações de vídeos é aplicável a agências de notícias, instituições de ensino, emissoras, proprietários de conteúdo de entretenimento, aplicativos de linha de negócios e, em geral, a qualquer setor que tenha uma biblioteca de vídeos que os usuários precisam pesquisar.

- Monetização: o Video Indexer pode ajudar a melhorar o valor dos vídeos. Por exemplo, setores que contam com a receita de anúncios (como a mídia de notícias, mídias sociais etc.) podem entregar anúncios mais relevantes usando as informações extraídas como sinais adicionais para o servidor do anúncio (apresentar um anúncio de sapatos esportivos é mais relevante durante uma partida de futebol do que em uma competição de natação).

- Participação do usuário: as informações do vídeo podem ser usadas para melhorar a participação do usuário posicionando os momentos do vídeo relevantes para os usuários. Por exemplo, considere um vídeo educativo que explica as esferas nos primeiros 30 minutos e as pirâmides nos próximos 30 minutos. Um aluno que lê sobre as pirâmides aproveitaria mais se o vídeo fosse posicionado para começar a partir do marcador de 30 minutos.

Para obter mais informações, consulte este [blog](https://aka.ms/videoindexerblog).

## <a name="next-steps"></a>Próximas etapas

Você está pronto para começar com o Video Indexer. Para obter mais informações, consulte os seguintes artigos:

- [Introdução ao site do Video Indexer](video-indexer-get-started.md)
- [Processar o conteúdo com a API REST do Video Indexer](video-indexer-use-apis.md)
- [Inserir widgets visuais em seu aplicativo](video-indexer-embed-widgets.md)
