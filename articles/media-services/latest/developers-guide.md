---
title: SDKs dos Serviços de Mídia do Azure v3 – Azure
description: Este artigo fornece uma visão geral de como iniciar o desenvolvimento com a API dos Serviços de Mídia v3 usando SDKs/ferramentas.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: ''
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 02/22/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: b9c89b6cb6dbc6cb217be671eb0e52c0416811ed
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57889838"
---
# <a name="start-developing-with-media-services-v3-api-using-sdkstools"></a>Iniciar o desenvolvimento com a API dos Serviços de Mídia v3 usando SDKs/ferramentas

Como desenvolvedor, você pode usar a [API REST](https://aka.ms/ams-v3-rest-ref) dos Serviços de Mídia ou bibliotecas de clientes que permitem interagir com a API REST para criar, gerenciar e manter fluxos de trabalho de mídia personalizados com facilidade. A API dos [Serviços de Mídia v3](https://aka.ms/ams-v3-rest-sdk) se baseia na especificação do OpenAPI (anteriormente conhecida como um Swagger).

Este tópico fornece links para os SDKs, as ferramentas e a documentação. Também fornece algumas informações úteis para diferentes ambientes de desenvolvimento.

## <a name="prerequisites"></a>Pré-requisitos

Para iniciar o desenvolvimento nos Serviços de Mídia, você precisará:

- Uma assinatura ativa do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- [Criar uma conta dos Serviços de Mídia](create-account-cli-how-to.md)

## <a name="start-developing"></a>Comece a desenvolver

Os Serviços de Mídia do Azure dão suporte aos SDKs/às ferramentas a seguir 

- [CLI do Azure](https://aka.ms/ams-v3-cli) 
- [SDK .NET](https://aka.ms/ams-v3-dotnet-sdk)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [SDK do Node.js](https://aka.ms/ams-v3-nodejs-sdk)
- [SDK do Python](https://aka.ms/ams-v3-python-sdk)
- [SDK do Go](https://aka.ms/ams-v3-go-sdk)
- [SDK do Ruby](https://aka.ms/ams-v3-ruby-sdk)

### <a name="azure-media-services-explorer"></a>Gerenciador dos Serviços de Mídia do Azure

O [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) (Gerenciador dos Serviços de Mídia do Azure) é uma ferramenta disponível para clientes do Windows que desejam saber mais sobre os Serviços de Mídia. O AMSE é um aplicativo do WinForms/C# que carrega, baixa, codifica e transmite VoD e conteúdo ao vivo com os Serviços de Mídia. A ferramenta AMSE destina-se aos clientes que desejam testar os Serviços de Mídia sem escrever nenhum código. O código AMSE é fornecido como um recurso para clientes que desejam desenvolver com os Serviços de Mídia.

O AMSE é um projeto de software livre, cujo suporte é fornecido pela comunidade (os problemas podem ser relatados a https://github.com/Azure/Azure-Media-Services-Explorer/issues). Este projeto adotou o [Código de Conduta Microsoft Open Source](https://opensource.microsoft.com/codeofconduct/). Para obter mais informações, confira as [Perguntas frequentes sobre o Código de Conduta](https://opensource.microsoft.com/codeofconduct/faq/) ou contate opencode@microsoft.com para enviar outras perguntas ou comentários.

## <a name="rest"></a>REST

Para iniciar o desenvolvimento com as APIs REST dos Serviços de Mídia, instale um cliente REST. Por exemplo, **Postman**, **Visual Studio Code** com o plug-in da REST ou o **Telerik Fiddler**. Estamos usando o [Postman](media-rest-apis-with-postman.md) para as amostras dos Serviços de Mídia v3.

Explore a documentação de [referência da API REST](https://aka.ms/ams-v3-rest-ref) dos Serviços de Mídia e confira estes exemplos:

- [Tutorial: Codificar um arquivo remoto baseado em URL e transmitir o vídeo – REST](stream-files-tutorial-with-rest.md)
- [Carregar arquivos em uma conta dos Serviços de Mídia – REST](upload-files-rest-how-to.md)
- [Criar filtros com os Serviços de Mídia – REST](filters-dynamic-manifest-rest-howto.md)
- [API REST baseada no Azure Resource Manager](https://github.com/Azure-Samples/media-services-v3-arm-templates)

<!-- ## CLI -->
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

Explore a documentação de [referência da CLI do Azure](https://aka.ms/ams-v3-cli-ref) dos Serviços de Mídia e confira estes exemplos:

- [Dimensionar as unidades reservadas para mídia – CLI](media-reserved-units-cli-how-to.md)
- [Criar uma conta dos Serviços de Mídia – CLI](./scripts/cli-create-account.md) 
- [Redefinir as credenciais da conta – CLI](./scripts/cli-reset-account-credentials.md)
- [Criar ativos – CLI](./scripts/cli-create-asset.md)
- [Carregar um arquivo – CLI](./scripts/cli-upload-file-asset.md)
- [Criar transformações – CLI](./scripts/cli-create-transform.md)
- [Criar trabalhos – CLI](./scripts/cli-create-jobs.md)
- [Criar uma EventGrid – CLI](./scripts/cli-create-event-grid.md)
- [Publicar um ativo – CLI](./scripts/cli-publish-asset.md)
- [Filtro – CLI](filters-dynamic-manifest-cli-howto.md)

## <a name="net"></a>.NET

Explore a documentação de [referência do .NET](https://aka.ms/ams-v3-dotnet-ref) dos Serviços de Mídia e confira estes exemplos:

- [Tutorial: Carregar, codificar e transmitir vídeos – .NET](stream-files-tutorial-with-api.md) 
- [Tutorial: Transmissão ao vivo com os Serviços de Mídia v3 – .NET](stream-live-tutorial-with-api.md)
- [Tutorial: Analisar vídeos com os Serviços de Mídia v3 – .NET](analyze-videos-tutorial-with-api.md)
- [Criar uma entrada de trabalho com base em um arquivo local – .NET](job-input-from-local-file-how-to.md)
- [Criar uma entrada de trabalho com base em uma URL HTTPS – .NET](job-input-from-http-how-to.md)
- [Codificação com uma transformação personalizada – .NET](customize-encoder-presets-how-to.md)
- [Usar a criptografia dinâmica AES-128 e o serviço de entrega de chaves – .NET](protect-with-aes128.md)
- [Usar a criptografia dinâmica DRM e o serviço de entrega de licenças – .NET](protect-with-drm.md)
- [Obter uma chave de assinatura da política existente – .NET](get-content-key-policy-dotnet-howto.md)
- [Criar filtros com os Serviços de Mídia – .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Exemplos de vídeos sob demanda avançados do Azure Functions v2 com Serviços de Mídia v3](https://aka.ms/ams3functions)

## <a name="java"></a>Java

Examine a documentação de [referência do Java](https://aka.ms/ams-v3-java-ref) dos Serviços de Mídia.

## <a name="nodejs"></a>Node.js

Explore a documentação de [referência do Node.js](https://aka.ms/ams-v3-nodejs-ref) dos Serviços de Mídia e confira as [amostras](https://github.com/Azure-Samples/media-services-v3-node-tutorials) que explicam como usar a API dos Serviços de Mídia com o Node.js.

## <a name="python"></a>Python

Examine a documentação de [referência do Python](https://aka.ms/ams-v3-python-ref) dos Serviços de Mídia.

## <a name="go"></a>Go

Examine a documentação de [referência do Go](https://aka.ms/ams-v3-go-ref) dos Serviços de Mídia.

## <a name="next-steps"></a>Próximas etapas

- [Criar uma conta – CLI](create-account-cli-how-to.md)
- [Acessar as APIs – CLI](access-api-cli-how-to.md)

