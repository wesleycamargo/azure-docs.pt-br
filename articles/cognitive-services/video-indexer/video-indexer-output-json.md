---
title: Examinar a saída do Video Indexer do Azure | Microsoft Docs
description: Este tópico examina a saída do Video Indexer.
services: cognitive services
documentationcenter: ''
author: juliako
manager: cflower
ms.service: cognitive-services
ms.topic: article
ms.date: 05/30/2018
ms.author: juliako
ms.openlocfilehash: 7f61833d70cea3a6d176f5a08eb0db1c30e70e86
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "35364854"
---
# <a name="examine-the-video-indexer-output-produced-by-v1-api"></a>Examinar a saída do Video Indexer produzida pela API v1

> [!Note]
> As APIs V1 do Video Indexer foram preteridas e serão removidas em 1º de agosto de 2018. Você deve começar a usar as APIs v2 do Video Indexer para evitar interrupções.
>
> Para desenvolver com as APIs do Video Indexer v2, consulte as instruções encontradas [aqui](https://api-portal.videoindexer.ai/). 

Quando você chama a API **Get Breakdowns** e o status da resposta é OK, você obtém uma saída detalhada do JSON como o conteúdo da resposta. O conteúdo JSON contém detalhes dos insights de vídeo especificados incluindo transcript (transcrição), OCRs e people (pessoas). Os detalhes incluem keywords (palavras-chave) (topics [tópicos]), faces (rostos), blocks (blocos). Cada bloco inclui intervalos de tempo, linhas de transcrição, linhas de OCR, sentimentos, rostos e miniaturas de bloco.

Você pode usar a API **Get Breakdowns** para obter o detalhamento completo de um vídeo como um conteúdo JSON.  
 
    GET https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/63c6d532ff HTTP/1.1
    Host: videobreakdown.azure-api.net
    Ocp-Apim-Subscription-Key: ••••••••••••••••••••••••••••••••
    
Você também visualmente pode examinar informações de resumidas do vídeo pressionando a **reproduzir** botão no vídeo no portal do indexador de vídeo. Para obter mais informações, consulte [insights de vídeo de exibir e editar](video-indexer-view-edit.md).

![Insights](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

Este artigo examina o conteúdo JSON retornado pela API **Get Breakdowns**. Talvez seja útil examinar o artigo de [conceitos](video-indexer-concepts.md).

> [!NOTE]
> Expiração de todos os tokens de acesso no indexador de vídeo é uma hora.

## <a name="root-elements"></a>Elementos raiz

Atributo | DESCRIÇÃO
---|---
ID|A ID deste vídeo. Por exemplo, 63c6d532ff.
partition|Uma partição lógica que o usuário pode especificar no upload para pesquisá-la mais tarde.
Nome|O nome do vídeo. Por exemplo, o Azure Monitor.
Descrição|A descrição do vídeo. Por exemplo, "John Kemnetz se une a Scott Hanselman para mostrar como liberar o poder dos dados de monitoramento do Azure com o Azure Monitor".
userName|O criador do vídeo. Por exemplo, Vídeos do Channel9.
createTime |Hora da criação. Por exemplo, 2017-03-31T16:36:41.4504249+00:00.
privacyMode|Seu vídeo pode ter um dos seguintes modos: **Private**, **Public**. **Público**: o vídeo é visível para todos na sua conta e para qualquer pessoa que tenha um link para o vídeo. **Privada** -o vídeo é visível para todos em sua conta.
isOwned|True, se o usuário atual for o proprietário do vídeo. Caso contrário, false.  
isBase|True, se o detalhamento for baseado em um vídeo de origem. False, se o detalhamento for uma playlist que é derivada de outro detalhamento.
durationInSeconds|A duração do vídeo.
summarizedInsights|Contém um [summarizedInsights](#summarizedInsights).
breakdowns|Pode conter um ou mais [breakdowns](#breakdowns)
social|Contém um elemento **social** que descreve o número de curtidas e exibições do vídeo.

## <a name="summarizedinsights"></a>summarizedInsights

Esta seção mostra o resumo das informações.

Atributo | DESCRIÇÃO
---|---
Nome|O nome do vídeo. Por exemplo, o Azure Monitor.
shortId|A ID do vídeo. Por exemplo, 63c6d532ff.
privacyMode|Seu detalhamento pode ter um dos seguintes modos: **Particular**, **Público**. **Público**: o vídeo é visível para todos na sua conta e para qualquer pessoa que tenha um link para o vídeo. **Privada** -o vídeo é visível para todos em sua conta.
duration|Contém uma duração que descreve o tempo que uma percepção ocorreu. Duração é em segundos.
thumbnailUrl|A URL completa da miniatura do vídeo. Por exemplo, "https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...". Observe que, se o vídeo for privado, a URL conterá um token de acesso de uma hora. Após uma hora, a URL não será mais válida e você precisará obter o detalhamento novamente com uma nova URL nele ou chamar GetAccessToken para obter um novo token de acesso e criar a URL completa manualmente ('https://www.videoindexer.ai/api/Thumbnail/[shortId]/[ThumbnailId]?accessToken=[accessToken]').
faces|Pode conter um ou mais [faces](#faces)
topics|Pode conter um ou mais [topics](#topics)
sentiments|Pode conter um ou mais [sentiments](#sentiments)
audioEffects| Pode conter um ou mais [audioEffects](#audioEffects)
marcas| Pode conter zero ou mais [brands](#brands)
Estatísticas|Para obter mais informações, consulte [Statistics](#Statistics)
.
### <a name="statistics"></a>Estatísticas

|NOME|DESCRIÇÃO|
|---|---|
|CorrespondenceCount|Número de correspondências no vídeo.|
|WordCount|O número de palavras por alto-falante.|
|SpeakerNumberOfFragments|A quantidade de fragmentos que o orador tem em um vídeo.|
|SpeakerLongestMonolog|O mais longo monólogo do orador. Se o falante tiver silêncios dentro do monólogo, ele será incluído. O silêncio no início e no final do monólogo é removido.| 
|SpeakerTalkToListenRatio|O cálculo é baseado no tempo gasto no monólogo do locutor (sem o silêncio intermediário) dividido pelo tempo total do vídeo. A hora é arredondada para o terceiro ponto decimal.|

## <a name="breakdowns"></a>breakdowns

Esta seção mostra os detalhes dos insights.

Atributo | DESCRIÇÃO
---|---
ID|A ID do detalhamento. Por exemplo, 63c6d532ff.
state|O estado de processamento da ID do detalhamento especificada. Pode ser um dos seguintes: Uploaded, Processing, Processed, Failed.
processingProgress|O progresso. Por exemplo, 10%.
externalId| Você pode definir a externalId durante o upload. Por exemplo, "4f9c3500-eca7-4ab3-987e-a745017af698". Você pode pesquisar seus vídeos mais tarde por essa externalId.
externalUrl|Você pode definir externalUrl durante o upload. 
metadata|Você pode definir os metadados durante o upload. 
Insights|Pode conter um ou mais [insights](#insights)
thumbnailUrl|A URL completa da miniatura do vídeo. Por exemplo, "https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO..". Observe que, se o vídeo for privado, a URL conterá um token de acesso de uma hora. Após uma hora, a URL não será mais válida e você precisará obter o detalhamento novamente com uma nova URL nele ou chamar GetAccessToken para obter um novo token de acesso e criar a URL completa manualmente ('https://www.videoindexer.ai/api/Thumbnail/[shortId]/[ThumbnailId]?accessToken=[accessToken]').
publishedUrl|A URL publicada. Por exemplo, "https://BreakdownMedia.azureedge.net:443/d5e5232d-48e2-4fbc-9893-0ea6335da563/Azure%20Monitor%20%20Azure%20Friday.ism/manifest".
viewToken|O token de portador
sourceLanguage|O idioma de origem. Os seguintes são compatíveis: chinês, inglês, francês, alemão, italiano, japonês, português, russo e espanhol.
Linguagem|O idioma da transcrição.

## <a name="insights"></a>Insights

Atributo | DESCRIÇÃO 
---|---
transcriptBlocks|Pode conter um ou mais [transcriptBlocks](#transcriptBlocks)
topics|Pode conter um ou mais [topics](#topics)
faces|Pode conter um ou mais [faces](#faces)
participants|Pode conter um ou mais [participants](#participants)
contentModeration|Pode conter um [contentModeration](#contentModeration)
audioEffectsCategories|Pode conter um ou mais [audioEffectsCategories](#audioEffectsCategories)

## <a name="faces"></a>faces

### <a name="summarizedinsights"></a>summarizedInsights

**faces** que aparecem sob **summarizedInsights**, mostram um resumo de cada rosto encontrado no vídeo.

Atributo | DESCRIÇÃO 
---|---
ID|A ID de uma pessoa. Por exemplo, 11775.
shortId|A ID curta. Como uma playlist pode ser derivada de vários detalhamentos, essa ID é necessária para descobrir qual desses detalhamentos é a origem de cada rosto.  
Nome|Se o rosto for reconhecido, o nome da pessoa será adicionado. Por exemplo, "Scott Hanselman". Se o rosto for desconhecido, "Unknown #" será adicionado. 
Descrição|Se o rosto for reconhecido, a descrição será preenchida com base na pesquisa da API do Bing. Caso contrário, a descrição será **null**.
título|Se o rosto for reconhecido, a descrição será preenchida com base na pesquisa da API do Bing. Caso contrário, o title será **null**.
thumbnailFullUrl|A URL completa da miniatura do rosto. Por exemplo, "https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...". Observe que, se o vídeo for privado, a URL conterá um token de acesso de uma hora. Após uma hora, a URL não será mais válida e você precisará obter o detalhamento novamente com uma nova URL nele ou chamar GetAccessToken para obter um novo token de acesso e criar a URL completa manualmente ('https://www.videoindexer.ai/api/Thumbnail/[shortId]/[ThumbnailId]?accessToken=[accessToken]').
appearances|Pode conter uma ou mais [appearances](#appearances)
seenDuration|Por quanto tempo o rosto foi visto (em segundos).
seenDurationRatio|Presença em relação à duração do vídeo (0-1).

### <a name="breakdown-insights"></a>breakdown insights

**faces** que aparecem em **breakdowns**, descrevem os detalhes sobre cada rosto encontrado no vídeo.

Atributo | DESCRIÇÃO 
---|---
ID|A ID de uma pessoa. Por exemplo, 11775.
bingId|
Nome|Se o rosto for reconhecido, o nome da pessoa será adicionado. Por exemplo, "Scott Hanselman". Se o rosto for desconhecido, "Unknown #" será adicionado. 
thumbnailId|Por exemplo, 616468f0-1636-4efa-94e7-262f2e575059.
Descrição|Se o rosto for reconhecido, a descrição será preenchida com base na pesquisa da API do Bing. Caso contrário, a descrição será **null**.
título|Se o rosto for reconhecido, a descrição será preenchida com base na pesquisa da API do Bing. Caso contrário, o title será **null**.
imageUrl|Esta URL aponta para uma imagem que é obtida do vídeo de origem.  
confidence|A pontuação de confiança (maior é melhor).
knownPersonId|A ID de uma pessoa conhecida (por exemplo, celebridade). Se uma pessoa não for conhecida, a ID conterá zeros. Por exemplo, e3eaff5f-ee1b-4eac-80ce-ebac47aadf64.

## <a name="topics"></a>topics

### <a name="summarizedinsights"></a>summarizedInsights

**topics** que aparecem sob **summarizedInsights**, mostram um resumo de cada tópico encontrado no vídeo.

Atributo | DESCRIÇÃO 
---|---
Nome|O nome do tópico (por exemplo, "Azure"). 
appearances|Pode conter uma ou mais [appearances](#appearances).
isTranscript|True, se encontrada em uma transcrição. False, se encontrado em um OCR.

### <a name="breakdown-insights"></a>breakdown insights

**topics** que aparecem em **breakdowns**, descrevem os detalhes sobre cada tópico encontrado no vídeo.

|Atributo | DESCRIÇÃO |
|---|---|
|ID|ID do tópico exclusiva.|
|Nome|O nome do tópico.|
|stem|No momento, esse valor não é usado.|
|words|No momento, esse valor não é usado.|
|rank|A pontuação de relevância (maior é melhor).|

## <a name="sentiments"></a>sentiments

Atributo | DESCRIÇÃO
---|---
sentimentKey| Atualmente, os seguintes sentimentos são compatíveis: Positive, Neutral, Negative. 
appearances|Pode conter uma ou mais [appearances](#appearances)|.
seenDurationRatio|Presença em relação à duração do vídeo (0-1).

## <a name="audioeffects"></a>audioEffects

Atributo | DESCRIÇÃO 
---|---
audioEffectKey| Os valores válidos são: Speech, Silence, HandClaps.
appearances|Pode conter uma ou mais [appearances](#appearances)
seenDurationRatio|Presença em relação à duração do vídeo (0-1).
seenDuration|Por quanto tempo o efeito de áudio estava presente (em segundos).

## <a name="appearances"></a>appearances

Atributo | DESCRIÇÃO 
---|---
startTime| O valor temporal.
endTime|O valor temporal.
startSeconds| O valor temporal.
endSeconds| O valor temporal.

## <a name="participants"></a>participants

Atributo | DESCRIÇÃO 
---|---
ID|A ID do participante.
Nome|O nome do participante. Por exemplo, Locutor nº 1.
pictureUrl|O atributo **pictureUrl** é reservado para uso futuro.

## <a name="contentmoderation"></a>contentModeration

Atributo | DESCRIÇÃO 
---|---
adultClassifierValue|O nível de confiança de que o vídeo tem conteúdo para adultos.
racyClassifierValue|O nível de confiança de que o vídeo tem conteúdo sexual.
bannedWordsCount|Número de palavras de linguagem obscena. 
bannedWordsRatio|Taxa de palavras de linguagem obscena do número total de palavras.
reviewRecommended|Valor booliano que indica se o vídeo deve ser revisado manualmente.
isAdult|Valores boolianos que indicam se o vídeo será considerado um vídeo adulto após a revisão manual.

## <a name="audioeffectscategories"></a>audioEffectsCategories

Atributo | DESCRIÇÃO 
---|---
Tipo|ID da categoria.
chave|Um dos seguintes: Speech, Silence, HandClaps. 

## <a name="transcriptblocks"></a>transcriptBlocks

Atributo | DESCRIÇÃO
---|---
ID|ID do bloco.
lines|Pode conter uma ou mais [lines](#lines)
sentimentIds|O atributo **sentimentIds** é reservado para uso futuro.
thumbnailIds|O atributo **thumbnailIds** é reservado para uso futuro.
sentimento|O sentimento no bloco (0-1, negativo para positivo).
faces|Pode conter um ou mais [faces](#faces).
ocrs|Pode conter um ou mais [ocrs](#ocrs).
audioEffectInstances|Pode conter um ou mais [audioEffectInstances](#audioEffectInstances).
scenes|Pode conter uma ou mais [scenes](#scenes).
annotations|Pode conter zero ou mais [annotations](#annotations).

## <a name="ocrs"></a>ocrs

Descreve em que ponto no vídeo o conteúdo de texto foi encontrado. 

Atributo | DESCRIÇÃO 
---|---
timeRange|O intervalo de tempo no vídeo original.
adjustedTimeRange|AdjustedTimeRange é o intervalo de tempo em relação à playlist atual. Como é possível criar uma playlist de diferentes linhas de diferentes vídeos, é possível usar um vídeo de uma hora e usar apenas 1 linha dele, por exemplo, 10:00-10:15. Nesse caso, você terá uma playlist com 1 linha, em que o intervalo de tempo é 10:00-10:15, mas o adjustedTimeRange é 00:00-00:15.
lines|Pode conter uma ou mais [lines](#lines).

## <a name="lines"></a>lines

### <a name="transcriptblocks"></a>transcriptBlocks

**lines** que aparecem sob **transcriptBlocks**, descrevem linhas de transcrições encontradas no vídeo.

Atributo | DESCRIÇÃO 
---|---
ID| A ID da linha.
timeRange|O intervalo de tempo no vídeo original.
adjustedTimeRange|AdjustedTimeRange é o intervalo de tempo em relação à playlist atual. Como é possível criar uma playlist de diferentes linhas de diferentes vídeos, é possível usar um vídeo de uma hora e usar apenas 1 linha dele, por exemplo, 10:00-10:15. Nesse caso, você terá uma playlist com 1 linha, em que o intervalo de tempo é 10:00-10:15, mas o adjustedTimeRange é 00:00-00:15.
participantID| A ID do locutor dessa linha.
text| A transcrição.
isIncluded| Em detalhamentos de base sempre é true. Em playlists derivadas, as linhas que estavam incluídas no vídeo de origem são definidas como isIncluded=true. Todas as outras linhas são false.

### <a name="ocrs"></a>ocrs

**lines** que aparecem sob **ocrs**, descrevem linhas de OCRs encontrados no vídeo.

Atributo | DESCRIÇÃO 
---|---
ID|A ID de OCR.
width|No momento, esse valor não é usado.
height|No momento, esse valor não é usado.
Linguagem|O idioma do OCR.
textData|O texto de OCR.
confidence|A pontuação de confiança (maior é melhor).

## <a name="scenes"></a>scenes

Atributo | DESCRIÇÃO 
---|---
ID|A ID da cena.
timeRange|Contém um **timeRange**.
keyFrame|O tempo do quadro-chave.
shots|Pode conter um ou mais [shots](#shots).

## <a name="shots"></a>shots

Atributo | DESCRIÇÃO 
---|---
ID||A ID da captura.
timeRange|Contém um **timeRange**.
keyFrame|O tempo do quadro-chave.

## <a name="audioeffectinstances"></a>audioEffectInstances

Atributo | DESCRIÇÃO 
---|---
Tipo|O índice do evento áudio: Laughter = 1, HandClaps = 2, Music = 3, Speech = 4, Silence = 5
ranges|Pode conter um ou mais [ranges](#ranges).

## <a name="ranges"></a>ranges

**ranges** que aparecem sob **audioEffectInstances**, descrevem os efeitos de áudio nesses intervalos.

Atributo | DESCRIÇÃO 
---|---
timeRange|O intervalo de tempo no vídeo original.
adjustedTimeRange|AdjustedTimeRange é o intervalo de tempo em relação à playlist atual. Como é possível criar uma playlist de diferentes linhas de diferentes vídeos, é possível usar um vídeo de uma hora e usar apenas uma linha dele, por exemplo, 10:00-10:15. Nesse caso, você terá uma playlist com 1 linha, em que o intervalo de tempo é 10:00-10:15, mas o adjustedTimeRange é 00:00-00:15.

## <a name="annotations"></a>annotations

Retorna marcas com base em objetos, seres vivos, cenários, ações e padrões de visual reconhecíveis.

|Atributo|DESCRIÇÃO|
|---|---|
|ID|A ID da anotação.|
|NOME|O nome da anotação (por exemplo, Person, Athletic game, Black Frames).|
|Appearances|Pode conter uma ou mais appearances.|

## <a name="brands"></a>marcas

Nomes de marcas comerciais e de produtos detectados na fala para transcrição de texto e / ou Vídeo OCR. Isso não inclui reconhecimento visual de marcas ou detecção de logotipo.

Atributo | DESCRIÇÃO
---|---
ID | A ID de uma marca.
Nome | O nome da marca do Bing ou uma marca personalizada.  
wikiId | O sufixo do URL da Wikipédia da marca. Por exemplo, "Target_Corporation" é o sufixo de [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
wikiUrl | A marca da url da Wikipedia, se existir. Por exemplo, [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
confidence | O valor de confiança do detector de marca indexador de vídeo (0-1).
Descrição | Descrição da marca extraída da Wikipédia. 
appearances | Pode conter uma ou mais appearances.
seenDuration | Presença em relação à duração do vídeo (0-1).

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre como criar seu próprio detalhamento, consulte [Exibir e editar insights do Video Indexer](video-indexer-view-edit.md).

Para obter informações sobre como incorporar widgets em seu aplicativo, consulte [Incorporar widgets do Video Indexer aos seus aplicativos](video-indexer-embed-widgets.md). 

## <a name="see-also"></a>Consulte também

[API de indexador de vídeo](https://videobreakdown.portal.azure-api.net/docs/services/582074fb0dc56116504aed75/operations/5857caeb0dc5610f9ce979e4)

[Visão geral do indexador vídeo](video-indexer-overview.md)

