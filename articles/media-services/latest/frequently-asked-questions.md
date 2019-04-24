---
title: Perguntas frequentes sobre os Serviços de Mídia do Azure v3 | Microsoft Docs
description: Este artigo fornece respostas às perguntas frequentes sobre os Serviços de Mídia do Azure v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/10/2019
ms.author: juliako
ms.openlocfilehash: d6e5142bec29f58671c553b7b32bb72202cae263
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322695"
---
# <a name="azure-media-services-v3-frequently-asked-questions"></a>Perguntas frequentes sobre os serviços de mídia do Azure v3

Este artigo fornece respostas às perguntas frequentes sobre os Serviços de Mídia do Azure (AMS) v3.

## <a name="v3-apis"></a>APIs v3

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Quais funções do Azure podem executar ações nos recursos de serviços de mídia do Azure? 

Ver [controle de acesso baseado em função (RBAC) para contas de serviços de mídia](rbac-overview.md).

### <a name="how-do-i-configure-media-reserved-units"></a>Como fazer para configurar Unidades Reservadas para Mídia?

Para os trabalhos de análise de áudio e análise de vídeo acionados pelo Serviços de Mídia do Microsoft Azure v3 ou pelo Video Indexer, é altamente recomendável provisionar sua conta com 10 MRUs do S3. Se você precisar de mais de 10 MRUs do S3, abra um ticket de suporte usando o [Portal do Microsoft Azure](https://portal.azure.com/).

Para obter detalhes, confira [Dimensionar o processamento de mídia com a CLI](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Qual é o método recomendado para processar vídeos?

Use [Transformações](https://docs.microsoft.com/rest/api/media/transforms) para configurar tarefas comuns para codificar ou analisar vídeos. Cada **Transformação** descreve uma receita ou um fluxo de trabalho de tarefas para processar os arquivos de áudio ou vídeos. Um [trabalho](https://docs.microsoft.com/rest/api/media/jobs) é a solicitação real para os serviços de mídia a ser aplicado a **transformar** a um determinado conteúdo de áudio ou vídeo de entrada. Quando a Transformação for criada, você poderá enviar trabalhos usando as APIs dos Serviços de Mídia ou um dos SDKs publicados. Para obter mais informações, consulte [Transformações e Trabalhos](transforms-jobs-concept.md).

### <a name="how-does-pagination-work"></a>Como a paginação funciona?

Ao usar a paginação, você sempre deve usar o próximo link para enumerar a coleção e não depender de um tamanho de página em particular. Para obter detalhes e exemplos, confira [Filtragem, ordenação, paginação](entities-overview.md).

## <a name="live-streaming"></a>Transmissão ao vivo 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>Como inserir quebras/vídeos e imagens fixas durante a transmissão ao vivo?

A codificação ativa dos Serviços de Mídia v3 ainda não dá suporte à inserção de vídeo ou de imagens fixas durante a transmissão ao vivo. 

Você pode usar um [codificador ativo local](recommended-on-premises-live-encoders.md) para alternar para o vídeo de origem. Muitos aplicativos fornecem a capacidade de alternar fontes, incluindo o Telestream Wirecast, o Switcher Studio (no iOS), o OBS Studio (aplicativo gratuito) e muito mais.

## <a name="content-protection"></a>Proteção do conteúdo

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>Como e onde obter o token do JWT antes de usá-lo para a licença de solicitação ou a chave?

1. Para a produção, você precisa ter um Secure Token Services (STS) (serviço web) que emite o token JWT após uma solicitação HTTPS. Para teste, você pode usar o código mostrado no método **GetTokenAsync** definido no [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).
2. Player será necessário fazer uma solicitação, depois que um usuário é autenticado para o STS para esse token e atribuí-lo como o valor do token. Você pode usar o [API do Player de mídia do Azure](https://amp.azure.net/libs/amp/latest/docs/).

* Para obter um exemplo de execução de STS, com a chave simétrica e assimétrica, consulte [https://aka.ms/jwt](https://aka.ms/jwt). 
* Para obter um exemplo de um player com base no Player de mídia do Azure usando tal token JWT, consulte [https://aka.ms/amtest](https://aka.ms/amtest) (expanda o link de "player_settings" para ver a entrada de token).

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>Como você autorizar solicitações para transmitir vídeos com a criptografia AES?

A abordagem correta é aproveitar o STS (serviço de Token seguro):

No STS, dependendo do perfil do usuário, adicione declarações diferentes (como "Usuário Premium", "Usuário Básico", "Usuário de Avaliação Gratuita"). Com diferentes declarações JWT, o usuário pode ver é diferente do conteúdo. É claro que, para conteúdo diferente/ativo, o ContentKeyPolicyRestriction terá o RequiredClaims correspondente.

Use APIs de serviços de mídia do Azure para configurar a licença/chave entrega e criptografar seus ativos (conforme mostrado na [Este exemplo](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)).

Para obter mais informações, consulte:

- [Visão geral da proteção de conteúdo](content-protection-overview.md)
- [Criação de um sistema de proteção de conteúdo de DRM múltiplo com controle de acesso](design-multi-drm-system-with-access-control.md)

## <a name="media-services-v2-vs-v3"></a>Serviços de Mídia v2 comparado com v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Eu posso usar o portal do Azure para gerenciar recursos da v3?

Atualmente, você não pode usar o portal do Azure para gerenciar recursos da v3. Use a [API REST](https://aka.ms/ams-v3-rest-ref), a [CLI](https://aka.ms/ams-v3-cli-ref) ou um dos [SDKs](developers-guide.md) com suporte.

### <a name="is-there-an-assetfile-concept-in-v3"></a>Existe um conceito de AssetFile no v3?

Os AssetFiles foram removidos da API do AMS para separar os Serviços de Mídia das dependências do SDK do Armazenamento. Agora o Armazenamento, não os Serviços de Mídia, mantém as informações que pertencem ao Armazenamento. 

Para obter mais informações, confira [Migrar para os Serviços de Mídia v3](migrate-from-v2-to-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Onde está a criptografia de armazenamento do lado do cliente?

Agora é recomendável usar a criptografia de armazenamento do lado do servidor (que está ativada por padrão). Para obter mais informações, consulte [Criptografia de serviço do Armazenamento do Azure para dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="next-steps"></a>Próximos passos

[Visão geral dos Serviços de Mídia v3](media-services-overview.md)
