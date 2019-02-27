---
title: Definir filtros nos Serviços de Mídia do Azure
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
ms.date: 02/12/2019
ms.author: juliako
ms.openlocfilehash: 09de372ffdb48c00fde9a43c07f8f8b574462d1f
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2019
ms.locfileid: "56372913"
---
# <a name="define-account-filters-and-asset-filters"></a>Definir filtros de contas e de ativos  

Ao entregar seu conteúdo aos clientes (streaming de eventos ao vivo ou Video por Demanda), seu cliente pode precisar de mais flexibilidade do que o descrito no arquivo de manifesto do ativo padrão. Os Serviços de Mídia do Azure permitem definir filtros de conta e filtros de recursos para o seu conteúdo. 

Os filtros são regras do lado do servidor que permitem que seus clientes façam coisas como: 

- Reproduza apenas uma seção de um vídeo (em vez de reproduzir o vídeo inteiro). Por exemplo: 
  - Reduzir o manifesto para mostrar um subclipe de um evento ao vivo ("filtragem de subclipe"), ou
  - Corte do início de um vídeo ("corte de um vídeo").
- Entregue apenas as execuções especificadas e / ou faixas de idioma especificadas que são suportadas pelo dispositivo usado para reproduzir o conteúdo ("filtragem de renderização"). 
- Ajuste a Janela de Apresentação (DVR) para fornecer uma duração limitada da janela do DVR no leitor ("ajustar a janela de apresentação").

Os Serviços de Mídia oferecem [Manifestos dinâmicos](filters-dynamic-manifest-overview.md) com base em filtros predefinidos. Depois que você definir filtros, seus clientes poderão usá-los na URL de streaming. Os filtros podem ser aplicados a protocolos de streaming de taxa de bits adaptável: Apple HTTP Live Streaming (HLS), MPEG-DASH e Smooth Streaming.

A tabela a seguir mostra alguns exemplos de URLs com filtros:

|Protocolo|Exemplo|
|---|---|
|HLS V4|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|HLS V3|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,filter=myAccountFilter)`|
|MPEG DASH|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=myAssetFilter)`|

## <a name="define-filters"></a>Definir filtros

Há dois tipos de filtros de ativo: 

* [Filtros de conta](https://docs.microsoft.com/rest/api/media/accountfilters) (global) - podem ser aplicados a qualquer recurso na conta dos Serviços de Mídia do Azure, têm vida útil da conta.
* [Filtros de ativos](https://docs.microsoft.com/rest/api/media/assetfilters) (local) - só podem ser aplicados a um ativo ao qual o filtro foi associado na criação, têm uma vida útil do recurso. 

Os filtros [Account Filter](https://docs.microsoft.com/rest/api/media/accountfilters) e [Asset Filter](https://docs.microsoft.com/rest/api/media/assetfilters) têm exatamente as mesmas propriedades para definir / descrever o filtro. Exceto ao criar o **Filtro de ativos**, você precisa especificar o nome do ativo ao qual deseja associar o filtro.

Dependendo do seu cenário, você decide qual tipo de filtro é mais adequado (Filtro de ativos ou Filtro de conta). Os filtros de conta são adequados para perfis de dispositivos (filtragem de renderização) em que os filtros de recursos podem ser usados para cortar um recurso específico.

Você usa as seguintes propriedades para descrever os filtros. 

|NOME|DESCRIÇÃO|
|---|---|
|firstQuality|A primeira taxa de bits de qualidade do filtro.|
|presentationTimeRange|O intervalo de tempo de apresentação. Esta propriedade é usada para filtrar os pontos de início / fim do manifesto, a duração da janela de apresentação e a posição de início ao vivo. <br/>Para mais informações, consulte [PresentationTimeRange](#PresentationTimeRange).|
|faixas|As condições de seleção de faixas. Para obter mais informações, consulte [faixas](#tracks)|

### <a name="presentationtimerange"></a>presentationTimeRange

Use essa propriedade com **filtros de ativo**. Não é recomendável definir a propriedade com **filtros de conta**.

|NOME|DESCRIÇÃO|
|---|---|
|**endTimestamp**|O limite de tempo final absoluto. Aplica-se ao Video on Demand (VoD). Para a apresentação Live, ela é silenciosamente ignorada e aplicada quando a apresentação termina e o fluxo se torna VoD.<br/><br/>O valor representa um ponto final absoluto do fluxo. Ele é arredondado para o próximo início do GOP.<br/><br/>Use StartTimestamp e EndTimestamp para aparar a lista de reprodução (manifesto). Por exemplo, StartTimestamp = 40000000 e EndTimestamp = 100000000 gerará uma lista de reprodução que contém mídia entre StartTimestamp e EndTimestamp. Se um fragmento ultrapassar o limite, o fragmento inteiro será incluído no manifesto.<br/><br/>Além disso, consulte a definição **forceEndTimestamp** a seguir.|
|**forceEndTimestamp**|Se aplica aos filtros em tempo real.<br/><br/>**forceEndTimestamp** é um valor booliano que indica se ou não **endTimestamp** foi definido como um valor válido. <br/><br/>Se o valor for **verdadeiro**, o valor **endTimestamp** deverá ser especificado. Se não for especificado, uma solicitação incorreta será retornada.<br/><br/>Se, por exemplo, você quiser definir um filtro que comece com 5 minutos no vídeo de entrada e dure até o final do fluxo, defina **forceEndTimestamp** como false e omita a configuração  **endTimestamp**.|
|**liveBackoffDuration**|Aplica-se útil apenas. A propriedade é usada para definir a posição de reprodução ao vivo. Usando essa regra, você pode atrasar a posição de reprodução ao vivo e criar um buffer do lado do servidor para os jogadores. LiveBackoffDuration é relativo à posição ao vivo. A duração máxima de retirada ao vivo é de 300 segundos.|
|**presentationWindowDuration**|Aplica-se útil. Use **presentationWindowDuration** para aplicar uma janela deslizante à lista de reprodução. Por exemplo, definir presentationWindowDuration = 1200000000 para aplicar uma janela deslizante de dois minutos. Mídia dentro de 2 minutos da borda ao vivo será incluída na lista de reprodução. Se um fragmento ultrapassar o limite, todo o fragmento será incluído na lista de reprodução. A duração mínima da janela de apresentação é de 60 segundos.|
|**startTimestamp**|Aplica-se a fluxos de VoD ou ao vivo. O valor representa um ponto inicial absoluto do fluxo. O valor é arredondado para o próximo início de GOP mais próximo.<br/><br/>Use **startTimestamp** e **endTimestamp** para aparar a lista de reprodução (manifesto). Por exemplo, startTimestamp = 40000000 e endTimestamp = 100000000 gerarão uma lista de reprodução que contém mídia entre StartTimestamp e EndTimestamp. Se um fragmento ultrapassar o limite, o fragmento inteiro será incluído no manifesto.|
|**Escala de tempo**|Aplica-se a fluxos de VoD ou ao vivo. A escala de tempo usada pelos timestamps e durações especificadas acima. A escala de tempo padrão é 10000000. Uma escala de tempo alternativa pode ser usada. O padrão é 10000000 HNS (cem nanossegundos).|

### <a name="tracks"></a>Faixas

Você especifica uma lista de condições de propriedade da faixa de filtro (FilterTrackPropertyConditions) com base nas quais as faixas de seu fluxo (Live ou Video on Demand) devem ser incluídas no manifesto criado dinamicamente. Os filtros são combinados usando uma operação lógica **E** e **OU**.

As condições de propriedade da faixa de filtro descrevem tipos de trilha, valores (descritos na tabela a seguir) e operações (Equal, NotEqual). 

|NOME|DESCRIÇÃO|
|---|---|
|**Bitrate**|Use a taxa de bits da faixa para filtragem.<br/><br/>O valor recomendado é um intervalo de bitrates, em bits por segundo. Por exemplo, "0-2427000".<br/><br/>Nota: embora você possa usar um valor de taxa de bits específico, como 250000 (bits por segundo), essa abordagem não é recomendada, pois as taxas de bits exatas podem variar de um ativo para outro.|
|**FourCC**|Use o valor de FourCC da faixa para filtragem.<br/><br/>O valor é o primeiro elemento do formato de codecs, conforme especificado na [6381 RFC](https://tools.ietf.org/html/rfc6381). Atualmente, há suporte para os seguintes codecs: <br/>Vídeo: "Avc1", "hev1", "hvc1"<br/>Para áudio: "Mp4a", "ec-3"<br/><br/>Para determinar os valores de FourCC para faixas em um ativo [obter e examine o arquivo de manifesto](#get-and-examine-manifest-files).|
|**Linguagem**|Use a linguagem da faixa para filtragem.<br/><br/>O valor é a tag de um idioma que você deseja incluir, conforme especificado no RFC 5646. Por exemplo, “in”|
|**Nome**|Use o nome da faixa para filtragem.|
|**Tipo**|Use o tipo da faixa para filtragem.<br/><br/>Os seguintes valores são permitidos: "video", "áudio" ou "texto".|

## <a name="example"></a>Exemplo

```json
{
  "properties": {
    "presentationTimeRange": {
      "startTimestamp": 0,
      "endTimestamp": 170000000,
      "presentationWindowDuration": 9223372036854776000,
      "liveBackoffDuration": 0,
      "timescale": 10000000,
      "forceEndTimestamp": false
    },
    "firstQuality": {
      "bitrate": 128000
    },
    "tracks": [
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Audio"
          },
          {
            "property": "Language",
            "operation": "NotEqual",
            "value": "en"
          },
          {
            "property": "FourCC",
            "operation": "NotEqual",
            "value": "EC-3"
          }
        ]
      },
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Video"
          },
          {
            "property": "Bitrate",
            "operation": "Equal",
            "value": "3000000-5000000"
          }
        ]
      }
    ]
  }
}
```

## <a name="next-steps"></a>Próximas etapas

Os artigos a seguir mostram como criar filtros programaticamente.  

- [Criar filtros com APIs REST](filters-dynamic-manifest-rest-howto.md)
- [Criar filtros com .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Criar filtros com a CLI](filters-dynamic-manifest-cli-howto.md)

