---
title: Migrar dos Serviços de Mídia do Azure v2 para v3 | Microsoft Docs
description: Este artigo descreve as alterações introduzidas nos Serviços de Mídia do Azure v3 e mostra as diferenças entre as duas versões.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: serviços de mídia do azure, transmissão, difusão, ao vivo, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 11/07/2018
ms.author: juliako
ms.openlocfilehash: 8c3ff4af3b556614d0b2179dceed6cabd9cbabff
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51616003"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Orientação de migração para passar dos Serviços de Mídia v2 para v3

Este artigo descreve as alterações introduzidas no Azure Media Services v3, mostra as diferenças entre as duas versões e fornece as diretrizes de migração.

E se você tiver um serviço de vídeo desenvolvido hoje em dia sobre as [APIs herdadas do Media Services v2](../previous/media-services-overview.md), analise as seguintes diretrizes e considerações antes de migrar para as APIs v3. Há muitos benefícios e novos recursos na API v3 que melhoram a experiência do desenvolvedor e os recursos dos Serviços de Mídia. No entanto, conforme explicado na seção [Problemas conhecidos](#known-issues) deste artigo, também há algumas limitações devido a alterações entre as versões da API. Esta página será mantida à medida que a equipe de Serviços de Mídia fizer melhorias contínuas nas APIs da v3 e abordar as lacunas entre as versões. 

## <a name="benefits-of-media-services-v3"></a>Benefícios do Serviços de Mídia do Microsoft Azure v3

### <a name="api-is-more-approachable"></a>A API é mais acessível

*  A v3 é baseada em uma superfície de API unificada que expõe a funcionalidade de operações e gerenciamento compilada no Azure Resource Manager. Os modelos do Azure Resource Manager podem ser usados para criar e implantar Transformações, Pontos de Extremidade de Streaming, LiveEvents e muito mais.
* Documento de especificação [Open API (aka Swagger).](https://aka.ms/ams-v3-rest-sdk)
    Expõe o esquema para todos os componentes de serviço, incluindo a codificação baseada em arquivo.
* SDKs disponíveis para [.NET](https://aka.ms/ams-v3-dotnet-ref), .NET Core, [Node.js](https://aka.ms/ams-v3-nodejs-ref), [Python](https://aka.ms/ams-v3-python-ref), [Java](https://aka.ms/ams-v3-java-ref), [Go](https://aka.ms/ams-v3-go-ref), e Ruby.
* [CLI do Azure](https://aka.ms/ams-v3-cli-ref) integração com o suporte a scripts simple.

### <a name="new-features"></a>Novos recursos

* Para processamento de trabalho baseado em arquivo, você pode usar um URL HTTP (S) como entrada.
    Você não precisa ter conteúdo já armazenado no Azure, nem precisa criar Ativos.
* Introduz o conceito de [Transformações](transforms-jobs-concept.md) para processamento de trabalho baseado em arquivo. Uma transformação pode ser usada para criar configurações reutilizáveis, criar modelos do Azure Resource Manager e isolar configurações de processamento entre vários clientes ou locatários.
* Um Ativo pode ter [vários StreamingLocators](streaming-locators-concept.md), cada um com diferentes configurações de Dynamic Packaging e Dynamic Encryption.
* [A proteção de conteúdo](content-key-policy-concept.md) suporta recursos de várias chaves.
* Você pode transmitir eventos ao vivo de até 24 horas longa quando usar os Serviços de Mídia do Microsoft Azure para transcodificação uma contribuição de taxa de bits única de feed em um fluxo de saída que tem várias taxas de bits.
* Novo suporte a streaming ao vivo de baixa latência no LiveEvents.
* A Versão Prévia do LiveEvent dá suporte para Empacotamento Dinâmico e Criptografia Dinâmica. Isso permite proteção de conteúdo na Versão Prévia, bem como empacotamento HLS e DASH.
* LiveOutput é mais simples de usar do que a entidade Program nas APIs v2. 
* Você tem controle de acesso baseado em função (RBAC) sobre suas entidades. 

## <a name="changes-from-v2"></a>Alterações da v2

* Para Ativos criados com a v3, o Media Services suporta apenas a criptografia de [armazenamento do lado do servidor do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).
    * É possível usar APIs v3 com ativos criados com APIs v2 que tinham [criptografia de armazenamento](../previous/media-services-rest-storage-encryption.md) (AES 256) fornecida pelo Serviços de Mídia do Microsoft Azure.
    * Você não pode criar novos Ativos com a criptografia de [armazenamento AES 256 legada](../previous/media-services-rest-storage-encryption.md) usando APIs v3.
* Os v3 SDKs agora são desacoplados do Storage SDK, o que lhe dá mais controle sobre a versão do Storage SDK que você deseja usar e evita problemas de versão. 
* Nas APIs da v3, todas as taxas de bits de codificação estão em bits por segundo. Isso é diferente das predefinições do v2 Media Encoder Standard. Por exemplo, o bitrate em v2 seria especificado como 128 (kbps), mas em v3 seria 128000 (bits / segundo). 
* Entidades AssetFiles, AccessPolicies e IngestManifests não existem na v3.
* A propriedade IAsset.ParentAssets não existe na v3.
* Os ContentKeys não é mais uma entidade, agora é uma propriedade do StreamingLocator.
* O suporte para Grade de Eventos substitui o NotificationEndpoints.
* As seguintes entidades foram renomeadas
    * JobOutput substitui a tarefa e, agora é parte de um trabalho.
    * StreamingLocator substitui o Localizador.
    * LiveEvent substitui o Canal.
        
        O faturamento do LiveEvents é baseado em medidores de canais ao vivo. Para mais informações, consulte [Visão geral de transmissão ao vivo](live-streaming-overview.md#billing) e [precificação](https://azure.microsoft.com/pricing/details/media-services/).
    * LiveOutput substitui o Programa.
* As LiveOutputs não precisam ser iniciadas explicitamente, elas começam na criação e param quando excluídas. Os programas funcionaram de maneira diferente nas APIs v2 e tiveram que ser iniciados após a criação.

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Falhas de recurso em relação a APIs v2

A API de v3 tem as seguintes falhas de recurso em relação a API v2. Fechar as lacunas está em andamento.

* O [Codificador Premium](../previous/media-services-premium-workflow-encoder-formats.md) e os legados [processadores analíticos de mídia](../previous/media-services-analytics-overview.md) (Visualização do Azure Media Indexer 2, API de Detecção Facial, etc.) não podem ser acessados pela v3.

    Os clientes que desejam migrar da visualização do Indexador de Mídia 1 ou 2 podem usar imediatamente a predefinição AudioAnalyzer na API v3.  Essa nova predefinição contém mais recursos que o antigo Indexador de Mídia 1 ou 2. 

* Muitos dos recursos avançados do Media Encoder Standard nas APIs v2 não estão disponíveis no momento na v3, como:
    * Recorte (para cenários sob demanda e ao vivo)
    * A união de ativos
    * Sobreposições
    * Corte
    * Sprites em miniatura
* No momento, os LiveEvents com transcodificação não suportam inserção intermediária do Slate, predefinições personalizadas ou inserção de marcador de anúncio por meio de chamada de API. 

> [!NOTE]
> Por favor, marque este artigo e continue verificando atualizações.

## <a name="code-differences"></a>Diferenças de código

A tabela a seguir mostra as diferenças de código entre v2 e v3 para cenários comuns.

|Cenário|API V2|API V3|
|---|---|---|
|Criar um ativo e enviar um arquivo |[exemplo de .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[exemplo de .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Enviar um trabalho|[exemplo de .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[exemplo de .NET V3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Mostra como criar primeiro uma transformação e, em seguida, enviar uma tarefa.|
|Publicar um ativo com criptografia AES |1. Criar ContentKeyAuthorizationPolicyOption<br/>2. Criar ContentKeyAuthorizationPolicy<br/>3. Criar AssetDeliveryPolicy<br/>4. Criar Ativo e carregar conteúdo OU Enviar trabalho e usar ativo de saída<br/>5. Associar AssetDeliveryPolicy com Ativo<br/>6. Criar ContentKey<br/>7. Anexar ContentKey ao Ativo<br/>8. Criar AccessPolicy<br/>9. Criar Localizador<br/><br/>[exemplo de .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. Criar Política de Chave de Conteúdo<br/>2. Criar Ativo<br/>3. Carregar conteúdo ou usar Ativo como JobOutput<br/>4. Criar StreamingLocator<br/><br/>[exemplo de .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|

## <a name="known-issues"></a>Problemas conhecidos

* Atualmente, você não pode usar o portal do Azure para gerenciar recursos da v3. Use a [API REST](https://aka.ms/ams-v3-rest-sdk), CLI ou um dos SDKs suportados.
* Hoje, as unidades reservadas de mídia só podem ser gerenciadas usando a API do Serviços de Mídia do Microsoft Azure v2. Para obter mais informações, consulte [Scaling media processing](../previous/media-services-scale-media-processing-overview.md).
* As entidades do Serviços de Mídia do Microsoft Azure criadas com a API v3 não podem ser gerenciadas pela API v2.  
* Não é recomendado gerenciar entidades que foram criadas com APIs v2 por meio das APIs da v3. A seguir, exemplos das diferenças que tornam as entidades em duas versões incompatíveis:   
    * Trabalhos e tarefas criados na v2 não aparecem na v3, pois não estão associados a uma transformação. A recomendação é mudar para v3 Transforms e Jobs. Haverá um período de tempo relativamente curto de necessidade de monitorar as tarefas V2 durante a transição.
    * Os canais e programas criados com v2 (que são mapeados para LiveEvents e LiveOutputs na v3) não podem continuar sendo gerenciados com a v3. A recomendação é mudar para v3 LiveEvents e LiveOutputs em uma parada de canal conveniente.
    
        Atualmente, você não pode migrar continuamente executando canais.  
> [!NOTE]
> Por favor, marque este artigo e continue verificando atualizações.

## <a name="next-steps"></a>Próximas etapas

Para ver como é fácil começar a codificar e transmitir arquivos de vídeo, confira [Arquivos de transmissão](stream-files-dotnet-quickstart.md). 

