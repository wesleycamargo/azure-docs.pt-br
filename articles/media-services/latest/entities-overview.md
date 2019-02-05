---
title: Visão geral das entidades dos Serviços de Mídia do Azure - Azure | Microsoft Docs
description: Este artigo fornece uma visão geral das entidades dos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/24/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 3f3322245983508e374d081e5d7905f67344ad7a
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2019
ms.locfileid: "54912639"
---
# <a name="azure-media-services-entities-overview"></a>Visão geral das entidades dos Serviços de Mídia do Azure

Este artigo fornece uma breve visão geral das entidades dos Serviços de Mídia do Azure e indica artigos para mais informações sobre como cada entidade é usada nos fluxos de trabalho dos Serviços de Mídia. 

| Tópico | DESCRIÇÃO |
|---|---|
| [Filtros de Contas e Filtros de Ativos](filters-dynamic-manifest-overview.md)|Ao entregar o conteúdo aos clientes (streaming de eventos ao vivo ou Vídeo por Demanda), seu cliente poderá precisar de mais flexibilidade do que está descrito no arquivo de manifesto do Ativo padrão. Os Serviços de Mídia do Azure permitem definir [Filtros de Conta](https://docs.microsoft.com/rest/api/media/accountfilters) e [Filtros de Ativo](https://docs.microsoft.com/rest/api/media/assetfilters). Em seguida, use **Manifestos dinâmicos** com base nos filtros predefinidos. |
| [Ativos](assets-concept.md)|Uma entidade [Ativo](https://docs.microsoft.com/rest/api/media/assets) contém arquivos digitais (incluindo vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e arquivos de legenda oculta) e os metadados sobre esses arquivos. Depois que os arquivos digitais são carregados em um Ativo, eles podem ser usados na análise de fluxos de trabalho de conteúdo, streaming e codificação dos Serviços de Mídia.|
| [Políticas de Chave de Conteúdo](content-key-policy-concept.md)|É possível usar os Serviços de Mídia para proteger a mídia a partir do momento que ela sai do computador por meio de armazenamento, processamento e entrega. Com os Serviços de Mídia, é possível entregar o conteúdo ao vivo e sob demanda criptografado dinamicamente com a criptografia AES (AES-128) ou qualquer um dos três principais sistemas DRM (gerenciamento de direitos digitais): Microsoft PlayReady, Google Widevine e Apple FairPlay. Os serviços de mídia também fornecem um serviço de distribuição de chaves AES e licenças DRM (PlayReady, Widevine e FairPlay) para os clientes autorizados.|
| [Eventos ao Vivo e Saídas Dinâmicas ](live-events-outputs-concept.md)|Os Serviços de Mídia permitem que você entregue eventos ao vivo para seus clientes na nuvem do Azure. Para configurar os eventos de transmissão ao vivo nos Serviços de Mídia v3, é necessário saber mais sobre [Eventos ao Vivo](https://docs.microsoft.com/rest/api/media/liveevents) e [Saídas Dinâmicas](https://docs.microsoft.com/rest/api/media/liveoutputs).|
| [Pontos de Extremidade de Streaming](streaming-endpoint-concept.md)|Uma entidade [Pontos de Extremidade de Streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints) representa um serviço de streaming que pode distribuir conteúdo diretamente a um aplicativo player do cliente ou a uma CDN (Rede de Distribuição de Conteúdo) para distribuição adicional. O fluxo de saída de um serviço de Ponto de Extremidade de Streaming pode ser uma transmissão ao vivo ou um Ativo de vídeo sob demanda em sua conta dos Serviços de Mídia. Quando você cria uma conta de Serviços de Mídia, um Ponto de Extremidade de Streaming **padrão** é criado em um estado parado. Você não pode excluir o Ponto de Extremidade de Streaming  **padrão**. Ponto de Extremidade de Streaming adicionais podem ser criados na conta. Para começar a transmitir vídeos, é necessário iniciar o Ponto de Extremidade de Streaming do qual deseja transmitir o vídeo. |
| [Localizadores de Streaming](streaming-locators-concept.md)|É necessário fornecer aos clientes uma URL que eles possam usar para reproduzir arquivos de vídeo ou áudio codificados e, para isso, você precisa criar um [Localizador de Streaming](https://docs.microsoft.com/rest/api/media/streaminglocators) e criar as URLs de streaming.|
| [Políticas de Streaming](streaming-policy-concept.md)| As [Políticas de Streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies) permitem que você defina protocolos de streaming e opções de criptografia do StreamingLocators. É possível especificar o nome de uma Política de Streaming personalizada criada ou usar uma das Políticas de Streaming predefinidas oferecidas pelos Serviços de Mídia. <br/><br/>Ao usar uma Política de Streaming personalizada, será necessário estruturar um conjunto limitado dessas políticas para sua conta de Serviço de Mídia e reutilizá-las em seus Localizadores de Streaming, sempre que as mesmas opções e protocolos de criptografia forem necessários. Você não deve criar uma nova Política de Streaming para cada Localizador de Streaming.|
| [Transformações e Trabalhos](transforms-jobs-concept.md)|Use [Transformações](https://docs.microsoft.com/rest/api/media/transforms) para configurar tarefas comuns para codificar ou analisar vídeos. Cada **Transformação** descreve uma receita ou um fluxo de trabalho de tarefas para processar os arquivos de áudio ou vídeos.<br/><br/>Um [Trabalho](https://docs.microsoft.com/rest/api/media/jobs) é a solicitação real para os serviços de mídia do Azure para aplicar a **Transformação** a um determinado conteúdo de áudio ou vídeo de entrada. O **Trabalho** especifica informações como o local do vídeo de entrada e o local para a saída. É possível especificar a localização do vídeo de entrada usando: URLs HTTPS, URLs SAS ou um Ativo.|

## <a name="next-steps"></a>Próximas etapas

[Transmitir um arquivo por streaming](stream-files-dotnet-quickstart.md)
