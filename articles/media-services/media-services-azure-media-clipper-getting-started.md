---
title: "Introdução ao Azure Media Clipper | Microsoft Docs"
description: "Introdução ao Azure Media Clipper, uma ferramenta para criação de clipes de mídia com base em ativos"
services: media-services
keywords: "clipe; subclipe; codificação; mídia"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 8a4f2c79131664ca0d078fa58c6a75b54243e705
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="create-clips-with-azure-media-clipper"></a>Criar clipes com o Azure Media Clipper
Esta seção mostra as etapas básicas de como começar a usar o Azure Media Clipper. As próximas seções fornecem informações específicas sobre como configurar o Azure Media Clipper.

- Primeiro, adicione os links a seguir ao Player de Mídia do Azure e ao Azure Media Clipper ao cabeçalho do documento. Recomendamos especificar explicitamente uma versão do Clipper e do Player de Mídia do Azure nas URLs. Não use a última versão desses recursos em produção, pois eles estão sujeitos a alterações sob demanda.

```javascript
<!--Azure Media Player 2.1.4 or later is a prerequisite-->
<link href="//amp.azure.net/libs/amp/2.1.4/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amp/2.1.4/azuremediaplayer.min.js"></script>
<!--Azure Media Clipper script and stylesheet-->
<link href="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.min.js"></script>
```

- Em seguida, adicione as classes a seguir ao elemento div no qual você gostaria de criar uma instância do Clipper.

```javascript
<div id="root" class="azure-subclipper" />
```

Opcionalmente, para habilitar o tema escuro, adicione a classe de capa escura:

```javascript
<div id="root" class="azure-subclipper dark-skin" />
```

- Em seguida, crie uma instância do Clipper com a seguinte chamada à API:

```javascript
var subclipper = new subclipper({
    selector: '#root',
    logLevel: 'info',
    restVersion: '2.0',
    minimumMarkerGap: 6,
    submitSubclipCallback: onSubmitSubclip,
    singleBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with single H264Layers
                }, {
                    // Audio Codec
                }]
    },
    multiBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with multiple H264Layers
                }, {
                    // Audio Codec
                }]
    },
    keymap: {
      // See below for more info
    },
   // Enable the Video Assets panel in the widget to automatically load assets (input contract)
    assetsPanelLoaderCallback: onLoadAssets,
    height: 600,
    subclippingMode: 'all',
    filterAssetsTypes: true,
    speedLevels: [
        { name: '4x', value: 4.0 },
        { name: '3x', value: 3.0 },
        { name: '2x', value: 2.0 },
        { name: 'normal', value: 1.0 },
        { name: '0.75x', value: 0.75 },
        { name: '0.5x', value: 0.5 },
    ],
    resetOnJobDone: false,
    autoplayVideo: true,
    language: 'en'    
});
```

Os parâmetros para a chamada do método de inicialização são:
- `selector` {REQUIRED, string}: seletor de CSS do elemento HTML correspondente no qual o widget deve ser renderizado.
- `restVersion` {REQUIRED, string}: a versão de API REST dos Serviços de Mídia do Azure a ser direcionada. A versão da REST define o formato da saída gerada pelo widget. Atualmente, há suporte apenas para 2.0.
- `submitSubclipCallback` {REQUIRED, promise} A função de retorno de chamada invocada ao clicar no botão “Enviar” do widget. A função de retorno de chamada deve esperar a saída gerada pelo widget (uma configuração do trabalho de renderização ou uma definição de filtro). Para obter mais informações, consulte Enviar retorno de chamada de subclipe.
- `logLevel` {OPTIONAL, {'info', 'warn', 'error'}}: o nível de log a ser exibido no console do navegador. Valor padrão: 'error'
- `minimumMarkerGap` {OPTIONAL, int}: o tamanho mínimo de um subclipe (em segundos). Observação: o valor deve ser maior ou igual a 6, que também é o padrão.
- `singleBitrateMp4Profile` {OPTIONAL, JSON object} o perfil de mp4 de taxa de bits única a ser usado para a configuração do trabalho de renderização gerada pelo widget. Se isso não for fornecido, ele usará o [perfil de MP4 de taxa de bits única padrão](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-single-bitrate-1080p).
- `multiBitrateMp4Profile` {OPTIONAL, JSON object} o perfil de mp4 de múltiplas taxas de bits a ser usado para a configuração do trabalho de renderização gerada pelo widget. Se isso não for fornecido, ele usará o [perfil de MP4 de múltiplas taxas de bits padrão](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-multiple-bitrate-1080p).
- `keymap` {OPTIONAL, json object} Permite personalizar os atalhos de teclado do widget. Para obter mais informações, consulte [Atalhos de teclado personalizáveis](media-services-azure-media-clipper-keyboard-shortcuts.md).
- `assetsPanelLoaderCallback` {OPTIONAL, promise} A função de retorno de chamada invocada para carregar (de forma assíncrona) uma nova página de ativos no painel de ativos sempre que o usuário rolar para baixo até a parte inferior do painel. Para obter mais informações, consulte Retorno de chamada de carregador do painel de ativos.
- `height` {OPTIONAL, number} A altura total do widget (a altura mínima é de 600 px sem o painel de ativos e 850 px com o painel de ativos).
- `subclippingMode` (OPTIONAL, {'all', 'render', 'filter'}): os modos de sub-recorte permitidos. O valor padrão é 'all'.
- `filterAssetsTypes` (OPTIONAL, bool): filterAssetsTypes permite mostrar/ocultar a lista suspensa de filtros do painel de ativos. O valor padrão é true.
- `speedLevels` (OPTIONAL, array): speedLevels permite configurar diferentes níveis de velocidade para o player de vídeo. Consulte a [documentação do Player de Mídia do Azure](http://amp.azure.net/libs/amp/latest/docs/#amp.player.playbackspeedoptions) para obter mais informações.
- `resetOnJobDone` (OPTIONAL, bool): resetOnJobDone permite que o Clipper redefina o subclipper para um estado inicial quando um trabalho é enviado com êxito.
- `autoplayVideo` (OPTIONAL, bool): autoplayVideo permite que o Clipper reproduza o vídeo automaticamente após o carregamento. O valor padrão é true.
- `language` {OPTIONAL, string}: language define o idioma do widget. Se isso não for especificado, o widget tentará localizar as mensagens de acordo com o idioma do navegador. Se nenhum idioma for detectado no navegador, o widget usará o inglês como padrão. Para obter mais informações, consulte a seção de idiomas com suporte.
- `languages` {OPTIONAL, JSON}: o parâmetro languages substitui o dicionário padrão de idiomas por um dicionário personalizado definido pelo usuário. Para obter mais informações, consulte a seção de idiomas com suporte.
- `extraLanguages` (OPTIONAL, JSON): o parâmetro extraLanaguages adiciona novos idiomas ao dicionário padrão. Para obter mais informações, consulte a seção de idiomas com suporte.

## <a name="typescript-definition"></a>Definição de TypeScript
Um arquivo de definição de [TypeScript](https://www.typescriptlang.org/) para o Clipper pode ser encontrado [aqui](http://amp.azure.net/libs/amc/latest/azuremediaclipper.d.ts).

## <a name="azure-media-clipper-api"></a>API do Azure Media Clipper
Esta seção documenta a superfície de API fornecida pelo Clipper.

- `load(assets)`: carrega uma lista de ativos no painel de ativos (não deve ser usado junto com `assetsPanelLoaderCallback`). Consulte este [artigo](media-services-azure-media-clipper-load-assets.md) para obter detalhes sobre como carregar ativos no Clipper.
- `setLogLevel(level)`: define o nível de log a ser exibido no console do navegador. Os valores possíveis são `info`, `warn` e `error`.
- `setHeight(height)`: define a altura total do widget em pixels (a altura mínima é de 600 px sem o painel de ativos e 850 px com o painel de ativos).
- `version`: obtém a versão do widget.

## <a name="configuring-azure-media-clipper"></a>Configurando o Azure Media Clipper
Consulte as próximas etapas para configurar o Azure Media Clipper:
- [Carregando ativos no Azure Media Clipper](media-services-azure-media-clipper-load-assets.md)
- [Configurando atalhos de teclado personalizados](media-services-azure-media-clipper-keyboard-shortcuts.md)
- [Enviando trabalhos de recorte do Clipper](media-services-azure-media-clipper-submit-job.md)

## <a name="supported-languages"></a>Idiomas com suporte
O widget do Clipper está disponível em 18 idiomas. Para definir o idioma do widget, defina o parâmetro `language` durante a inicialização. Passe a cadeia de caracteres de código do idioma desejada da seguinte lista:
- Chinês (simplificado): zh-hans
- Chinês (tradicional): zh-hant
- Tcheco: cs
- Holandês, flamengo: nl
- Inglês: en
- Francês: fr
- Alemão: de
- Húngaro: hu
- Italiano: it
- Japonês: ja
- Coreano: ko
- Polonês: pl
- Português (Brasil): pt-br
- Português (Portugal): pt-pt
- Russo: ru
- Espanhol: es
- Sueco: sv
- Turco: tr

Para definir um dicionário de idioma personalizado ou estender o dicionário do idioma padrão, defina o parâmetro `languages` ou `extraLanguages`, respectivamente. Passe um dicionário personalizado usando o seguinte formato JSON:

```javascript
{
      "{language-code}":
          "{message-id}": "{message}"
          ...
      }
      ...
}
```

Por exemplo, a seguinte amostra define as cadeias de caracteres em inglês localizadas:

```javascript
export default {
  'VideoPlayer.noPreview': 'No video preview',
  'VideoPlayer.loadAsset': 'You must provide a valid asset',
  'AssetsPanel.name': 'Name',
  'AssetsPanel.type': 'Asset type',
  'AssetsPanel.actions': 'Actions',
  'AssetsPanel.loading': 'Loading...',
  'AssetsPanel.duration': 'Duration',
  'AssetsPanel.resolution': 'Resolution',
  'AssetsPanel.pluralFiles': '{0} assets',
  'AssetsPanel.searchFiles': 'Search assets',
  'AssetsPanel.showTypes': 'Show:',
  'AssetsPanel.typesInfo': 'Rendered assets are actual MP4 files. Dynamic manifest filters are filters applied to a parent asset\'s video segment playlist.',
  'AssetsPanel.filterTypes': 'Filters',
  'AssetsPanel.assetTypes': 'Assets',
  'AssetsPanel.assetsAll': 'All',
  'AssetsPanel.addAsset': 'Add asset to the end',
  'AssetsPanel.addFilter': 'Add filter to the timeline',
  'AssetsPanel.invalidAsset': 'The metadata of this asset is not compatible with the other assets in the timeline',
  'AssetsPanel.addAssetWarning': 'Subclipping on assets with different resolutions may cause resolution autoscaling.',
  'AssetsPanel.live': 'LIVE',
  'AssetsPanel.unknown': 'UNKNOWN',
  'AssetsPanel.minimGapNotMet': 'The asset duration must be greater than the minimum clip duration ({0} seconds)',
  'VideoPlayer.openAdvancedSettings': 'Advanced settings',
  'VideoPlayer.singleBitrate': 'Single-bitrate MP4 (rendered)',
  'VideoPlayer.multiBitrate': 'Multi-bitrate MP4 (rendered)',
  'VideoPlayer.dynamicManifest': 'Dynamic manifest filter',
  'VideoPlayer.ErrorWithMessage': 'There was an error in the video player, code {0}, message: {1}',
  'Common.cancel': 'Cancel',
  'Common.OK': 'OK',
  'AdvancedSettings.framerate': 'Frame rate',
  'Dropdown.select': 'Select an option...',
  'InputAsset.RemoveInput': 'Remove source',
  'Zoom.startTime': 'Start time',
  'Zoom.endTime': 'End time',
  'VideoPlayer.subclips': 'Subclips:',
  'VideoPlayer.length': 'Clip length:',
  'Accordion.scrollLeft': 'Scroll to the left',
  'Accordion.scrollRight': 'Scroll to the right',
  'AdvancedSettings.title': 'Advanced settings',
  'AdvancedSettings.subclipName': 'Subclip name',
  'AdvancedSettings.subclipType': 'Subclipping mode',
  'AdvancedSettings.includeAudioTracks': 'Include audio tracks',
  'AdvancedSettings.subclipTypeInfo': 'Single-bitrate and multi-bitrate MP4s are frame accurate rendered assets. Dynamic manifest filters are group-of-pictures (GOP) accurate filters applied to a parent asset. Creating filters does not create a new asset and does not require encoding. Subclipping jobs on live assets are valid as long as their mark times are within the archive window of the parent asset. Filters are valid as long as the parent asset exists and mark times are within its archive window.',
  'AdvancedSettings.frameRateInfo': 'We autodetect frame rate under most scenarios. however, If we cannot autodetect, choose a frame rate from the dropdown for the selected asset(s).',
  'AdvancedSettings.frameRateError': 'Unable to determine frame rate',
  'AdvancedSettings.subclipNameInfo': 'Choose a name for your subclip.',
  'AdvancedSettings.singleAudioTrack': '1 audio track selected',
  'AdvancedSettings.allAudioTracks': 'All audio tracks selected',
  'AdvancedSettings.someAudioTracks': '{0} audio tracks selected',
  'AdvancedSettings.includeAllAudioTracks': 'Include all audio tracks',
  'AssetsPanel.loadingError': 'Failed to retreive assets from server.',
  'AssetsPanel.retry': 'Retry?',
  'CommandBar.prevFrameTitle': 'Back up one frame',
  'CommandBar.prevKeyFrameTitle': 'Back up one GOP',
  'CommandBar.cleanJob': 'Remove all assets',
  'CommandBar.cleanJobTitle': 'Remove all assets from timeline',
  'CommandBar.cleanJobMessage': 'This will empty all video clips from your timeline.',
  'CommandBar.update': 'Update filter',
  'CommandBar.createFilter': 'Create filter',
  'CommandBar.submit': 'Submit subclipper job',
  'CommandBar.jobErrorTitle': 'Operation failed',
  'CommandBar.jobErrorMessage': 'Your subclip failed to submit. Please try again.',
  'CommandBar.markInTitle': 'Set in at playhead',
  'CommandBar.markInPosition': 'Mark in timecode',
  'CommandBar.markOutTitle': 'Set out at playhead',
  'CommandBar.markOutPosition': 'Mark out timecode',
  'CommandBar.nextFrameTitle': 'Advance one frame',
  'CommandBar.nextKeyFrameTitle': 'Advance one GOP',
  'CommandBar.play': 'Play video',
  'CommandBar.pause': 'Pause video',
  'CommandBar.playPreviewTitle': 'Play subclip preview',
  'CommandBar.pausePreviewTitle': 'Pause subclip preview',
  'CommandBar.redoTitle': 'Redo last action',
  'CommandBar.removeAsset': 'Remove current asset',
  'CommandBar.undoTitle': 'Undo last action',
  'VideoPlayer.errorTitle': 'Error',
  'VideoPlayer.errorMessage': 'There was an error loading the selected asset.',
  'Timeline.markIn': 'Mark in bracket',
  'Timeline.markOut': 'Mark out bracket',
  'Timeline.playHead': 'Play head',
};
```