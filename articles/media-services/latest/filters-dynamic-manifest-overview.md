---
title: Filtros e manifestos dinâmicos dos Serviços de Mídia do Azure | Microsoft Docs
description: Este tópico descreve como criar filtros para que seu cliente possa usá-los na transmissão de seções específicas de um fluxo. Os Serviços de Mídia criam manifestos dinâmicos para atingir esse streaming seletivo.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 02/27/2019
ms.author: juliako
ms.openlocfilehash: 57007674e11271e6a3d5bdf660531d01b1eff82c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57861427"
---
# <a name="dynamic-manifests"></a>Manifestos dinâmicos

Os Serviços de Mídia oferecem **Manifestos dinâmicos** com base em filtros predefinidos. Depois de definir os filtros (confira [definir filtros](filters-concept.md)), seus clientes poderão usá-los para transmitir uma reprodução ou subclipes específicos de seu vídeo. Eles podem especificar filtros na URL de transmissão. Os filtros podem ser aplicados a protocolos de streaming de taxa de bits adaptável: Apple HTTP Live Streaming (HLS), MPEG-DASH e Smooth Streaming. 

A tabela a seguir mostra alguns exemplos de URLs com filtros:

|Protocolo|Exemplo|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

> [!NOTE]
> Manifestos dinâmicos não alteram o ativo e o manifesto padrão para esse ativo. O cliente pode optar por solicitar um fluxo com ou sem filtros. 
> 

Este tópico explica os conceitos relacionados aos **Manifestos Dinâmicos** e fornece exemplos de cenários nos quais você talvez deseje usar esse recurso.

## <a name="manifests-overview"></a>Visão geral de manifestos

Os serviços de mídia dá suporte a HLS, MPEG DASH, Smooth Streaming protocolos. Como parte da [empacotamento dinâmico](dynamic-packaging-overview.md), os manifestos do cliente streaming (Playlist do mestre de HLS, DASH MPD Media Presentation Description () e Smooth Streaming) são gerados dinamicamente com base no seletor de formato na URL. Consulte os protocolos de entrega no [esta seção](dynamic-packaging-overview.md#delivery-protocols). 

### <a name="get-and-examine-manifest-files"></a>Obter e examinar arquivos de manifesto

Você especifica uma lista de condições da propriedade da trilha de filtro com base nas quais as faixas de seu fluxo (Live ou Video por demanda) devem ser incluídas no manifesto criado dinamicamente. Para obter e examinar as propriedades das faixas, você precisa carregar o manifesto Smooth Streaming primeiro.

O tutorial [Upload, codificação e fluxo de arquivos com .NET](stream-files-tutorial-with-api.md) mostra como criar as URLs de streaming com o .NET (consulte a seção [criando URLs](stream-files-tutorial-with-api.md#get-streaming-urls)). Se você executar o aplicativo, uma das URLs aponta para o manifesto do Smooth Streaming: `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`.<br/>Copie e cole o URL na barra de endereços de um navegador. O arquivo será baixado. Você pode abri-lo em um editor de texto de sua escolha.

Para o exemplo REST, consulte [Carregar, codificar e transmitir arquivos com REST](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls).

### <a name="monitor-the-bitrate-of-a-video-stream"></a>Monitorar a taxa de bits de um fluxo de vídeo

Você pode usar a página de demonstração do [ Player de Mídia do Azure](https://aka.ms/amp) para monitorar a taxa de bits de um fluxo de vídeo. A página de demonstração exibe informações de diagnóstico na guia **Diagnósticos**:

![Diagnóstico do Player de Mídia do Azure][amp_diagnostics]

## <a name="rendition-filtering"></a>Filtragem de representação

Você pode optar por codificar seu ativo para vários perfis de codificação (H.264 linha de base, H.264 alto, AACL, AACH, Dolby Digital Plus) e várias taxas de bits de qualidade. No entanto, nem todos os dispositivos de cliente oferecerão suporte a todos os seus perfis e taxas de bits de todos os seus ativos. Por exemplo, os dispositivos Android mais antigos oferecem suporte apenas a H.264 Baseline+AACL. O envio de taxas de bits mais altas para um dispositivo, que não pode obter os benefícios, desperdiça a largura de banda e a computação do dispositivo. Esse dispositivo deve decodificar todas as informações determinadas, apenas para que seja subdimensionado para exibição.

Com o Dynamic Manifest, você pode criar perfis de dispositivos como celular, console, HD / SD, etc. e incluir as faixas e qualidades, que você quer que façam parte de cada perfil.

![Exemplo de filtragem de representação][renditions2]

No exemplo a seguir, o codificador foi usado para codificar um ativo mezzanine em sete representações de vídeo ISO MP4s (de 180p para 1080p). O recurso codificado pode ser empacotado dinamicamente em qualquer um dos seguintes protocolos de streaming: HLS, MPEG DASH e Smooth.  Na parte superior do diagrama, é mostrado o manifesto HLS para o ativo sem filtros (ele contém todas as sete representações).  Na parte inferior esquerda, é mostrado o manifesto HLS ao qual foi aplicado um filtro chamado "ott". O filtro "ott" especifica a remoção de todas as taxas de bits abaixo de 1 Mbps, o que resultou na remoção dos dois níveis de qualidade inferiores na resposta. Na parte inferior direita, é mostrado o manifesto HLS, ao qual foi aplicado um filtro chamado "mobile". O filtro "mobile" especifica a remoção de representações em que a resolução é maior do que 720p, resultando na remoção de duas representações de 1080p.

![Filtragem de representação][renditions1]

## <a name="removing-language-tracks"></a>Remover faixas de idiomas
Os ativos podem incluir vários idiomas de áudio, como inglês, espanhol, francês etc. Normalmente, o SDK do Player gerencia a seleção da faixa de áudio padrão e as faixas de áudio disponível por seleção do usuário. O desenvolvimento desses SDKs do Player é um desafio, requer diferentes implementações em estruturas de player específicas do dispositivo. Além disso, em algumas plataformas, os APIs de Player são limitados e não incluem o recurso de seleção de áudio em que os usuários não podem selecionar ou alterar a faixa de áudio padrão. Com filtros de ativos, é possível controlar o comportamento por meio da criação de filtros que incluem apenas os idiomas de áudio desejados.

![Filtragem das faixas de idioma][language_filter]

## <a name="trimming-start-of-an-asset"></a>Corte do início de um ativo
Na maioria dos eventos de transmissão ao vivo, os operadores executam alguns testes antes do evento real. Por exemplo, podem incluir um slate antes do início do evento com a seguinte frase: "O programa será iniciado em instantes". Se o programa estiver sendo arquivado, o teste e os dados da imagem fixa também são arquivados e incluídos na apresentação. No entanto, essas informações não devem ser mostradas para os clientes. Com o manifesto dinâmico, é possível criar um filtro de hora de início e remover os dados indesejados do manifesto.

![Corte do início][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Criar subclipes (exibições) de um arquivo ao vivo
Muitos eventos ao vivo são de longa duração e o arquivamento dinâmico pode incluir vários eventos. Após o término do evento ao vivo, talvez os difusores queiram dividir o arquivo ao vivo em sequências lógicas de início e parada do programa. Em seguida, publicam esses programas virtuais separadamente sem pós-processamento do arquivo em tempo real e sem criar ativos separados (o que não obtém os benefícios dos fragmentos de cache existentes nas CDNs). Exemplos desses programas virtuais são os tempos de um jogo de futebol ou de basquete, entradas no beisebol ou eventos individuais de qualquer programa de esportes.

Com o manifesto dinâmico, é possível criar filtros usando os horários de início/término e criar modos de exibição virtuais que ultrapassam seu arquivo ao vivo. 

![Filtro de subclipe][subclip_filter]

Ativos filtrados:

![Esqui][skiing]

## <a name="adjusting-presentation-window-dvr"></a>Ajuste da janela de apresentação (DVR)
Atualmente, o Serviços de Mídia do Azure oferece arquivamento circular onde a duração pode ser configurada entre 5 minutos e 25 horas. A filtragem de manifesto pode ser usada para criar uma janela DVR com rolagem que ultrapassa o arquivo, sem excluir a mídia. Há muitos cenários em que os difusores podem desejar fornecer uma janela DVR limitada que se move com a borda ao vivo e, ao mesmo tempo manter uma janela de arquivamento maior. Um transmissor pode querer usar os dados que estão fora da janela do DVR para destacar os clipes, ou eles podem querer fornecer diferentes janelas do DVR para diferentes dispositivos. Por exemplo, a maioria dos dispositivos móveis não suporta grandes janelas de DVR (você pode ter uma janela de DVR de 2 minutos para dispositivos móveis e uma hora para clientes de desktop).

![Janela DVR][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Ajustar o LiveBackoff (posição ao vivo)
A filtragem de manifesto pode ser usada para remover vários segundos da borda ao vivo de um programa ao vivo. A filtragem permite que os difusores assistam à apresentação no ponto de publicação de visualização e criem pontos de inserção de anúncio antes que os visualizadores recebam o fluxo (retirado por 30 segundos). Os difusores, enviam esses anúncios para suas estruturas de cliente no horário para que eles recebam e processem as informações antes da oportunidade de anúncio.

Além do suporte do anúncio, o a configuração do LiveBackoff pode ser usada para ajustar a posição dos visualizadores para que, quando os clientes se deslocarem e alcançarem a borda ao vivo, ainda possam obter fragmentos do servidor em vez de obter um erro HTTP 404 ou 412.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Combinar várias regras em um único filtro
É possível combinar várias regras de filtragem em um único filtro. Por exemplo, você pode definir uma “regra de intervalo” para remover a imagem fixa de um arquivo ao vivo e filtrar as taxas de bits disponíveis. Ao aplicar várias regras de filtragem, o resultado final é a intersecção de todas essas regras.

![várias regras][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>Combinando vários filtros (composição de filtros)

Você também pode combinar vários filtros em uma única URL. 

O seguinte cenário demonstra por que talvez seja conveniente combinar filtros:

1. Você precisa filtrar as qualidades de seus vídeos para dispositivos móveis, como Android ou iPAD (para limitar as qualidades de vídeos). Para remover as qualidades indesejadas, você criaria um filtro de conta adequado para os perfis de dispositivos. Os filtros de conta podem ser usados para todos os seus recursos na mesma conta de serviços de mídia, sem associação adicional. 
2. Você também deseja cortar a hora de início e de término de um ativo. Para conseguir isso, você criaria um filtro Ativo e definiria o horário de início/término. 
3. Você deseja combinar esses dois filtros (sem combinação, você precisa adicionar filtragem de qualidade ao filtro de aparar, o que dificulta o uso do filtro).

Para combinar os filtros, você precisa definir os nomes dos filtros para a URL do manifesto/playlist com ponto e vírgula delimitado. Vamos supor que você tenha um filtro chamado *MyMobileDevice* que filtra as qualidades e tenha outro chamado *MyStartTime* para definir uma hora de início específica. Você pode combiná-los assim:

Você pode combinar até três filtros. 

Para saber mais, confira [este](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.

## <a name="considerations-and-limitations"></a>Considerações e limitações

- Os valores para **forceEndTimestestamp**, **presentationWindowDuration** e **liveBackoffDuration** não devem ser definidos para um filtro VoD. Eles são usados apenas para cenários de filtro em tempo real. 
- Manifesto dinâmico opera nos limites do GOP (quadros chave) e, como consequência, o corte tem precisão de GOP. 
- Você pode usar o mesmo nome de filtro para filtros de conta e de recurso. Os filtros de recursos têm precedência mais alta e substituem os filtros de conta.
- Ao atualizar um filtro, talvez sejam necessários até 2 minutos para que o ponto de extremidade do streaming atualize as regras. Se o conteúdo foi distribuído usando alguns filtros (e armazenado em cache nos proxies e caches CDN), atualizar esses filtros pode resultar em falhas do player. É recomendável limpar o cache depois de atualizar o filtro. Se essa opção não for possível, considere usar um filtro diferente.
- Os clientes precisam fazer o download manual do manifesto e analisar a exata startTimestamp e a escala de tempo.
    
    - Para determinar as propriedades das faixas em um Ativo, [obtenha e examine o arquivo de manifesto](#get-and-examine-manifest-files).
    - A fórmula para definir as propriedades do registro de data e hora do filtro de ativos: <br/>startTimestamp = &lt;hora de início no manifesto&gt; +  &lt;tempo de início esperado do filtro em segundos&gt;* escala de tempo


## <a name="next-steps"></a>Próximas etapas

Os artigos a seguir mostram como criar filtros programaticamente.  

- [Criar filtros com APIs REST](filters-dynamic-manifest-rest-howto.md)
- [Criar filtros com .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Criar filtros com a CLI](filters-dynamic-manifest-cli-howto.md)

[renditions1]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/filters-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/filters-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/filters-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/filters-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/filters-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/filters-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/filters-dynamic-manifest-overview/media-services-skiing.png
[amp_diagnostics]: ./media/filters-dynamic-manifest-overview/amp_diagnostics.png
