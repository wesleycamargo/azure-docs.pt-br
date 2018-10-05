---
title: Introdução ao Content Moderator
titlesuffix: Azure Cognitive Services
description: Como começar com o Content Moderator.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: sajagtap
ms.openlocfilehash: c2ac0ccd89b5f1436a151e3d69c5d7423090f244
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47225287"
---
# <a name="get-started-with-content-moderator"></a>Introdução ao Content Moderator

Comece a usar o Content Moderator das seguintes maneiras:

- [Inicie com a ferramenta de análise](#start-with-the-review-tool) para obter a chave de API e criar uma equipe de análise. O benefício é que você pode usar a chave de API para chamar as APIs de moderação para examinar conteúdo e as APIs de análise para gerar revisões, sem etapas adicionais.
- [Assine o Content Moderator](#start-with-the-apis) no Azure para obter a chave de API. Confira a [referência de API](api-reference.md) e os [SDKs](sdk-and-samples.md#sdks-for-python-java-nodejs-and-net). Você ainda precisa se inscrever online para criar uma equipe de análise.
- [Use o conector de Fluxo e modelos](https://flow.microsoft.com/connectors/shared_cognitiveservicescontentmoderator/content-moderator/) para verificar uma grande variedade de integrações com um designer fácil de usar.

Independentemente da opção escolhida, consulte o artigo [Gerenciando credenciais](review-tool-user-guide/credentials.md) para encontrar as credenciais de sua API.

## <a name="start-with-the-review-tool"></a>Iniciar com a ferramenta de análise
[Inscreva-se](http://contentmoderator.cognitive.microsoft.com/) no site Web da ferramenta de análise Content Moderator.

![Home Page do Content Moderator](images/homepage.PNG)

### <a name="create-a-review-team"></a>Criar uma equipe de análise
Nomeie sua equipe. Se você quiser convidar seus colegas, poderá fazer isso digitando seus endereços de email.

![Convidar membro da equipe](images/QuickStart-2-small.png)

### <a name="upload-images-or-enter-text"></a>Carregar imagens ou digitar o texto
Clique em **Experimentar > Imagem** ou **Experimentar > Texto**. Carregue até cinco imagens de exemplo ou digite o texto de exemplo para a moderação.

![Experimentar Imagem ou Moderação de Texto](images/tryimagesortext.png)

### <a name="submit-for-automated-moderation"></a>Enviar para moderação automatizada
Envie seu conteúdo para a moderação automatizada. Internamente, a ferramenta de análise chama as APIs de moderação para examinar o conteúdo. Quando o exame for concluído, você verá uma mensagem informando sobre os resultados que aguardam sua análise.

![Moderar arquivos](images/submitted.png)

### <a name="review-and-confirm-results"></a>Analisar e confirmar resultados
Analise as marcas automoderadas, altere se necessário e envie usando o botão **Próximo**. Quando seu aplicativo de negócios chama as APIs do Moderator, o conteúdo marcado começa a formar filas, pronto para ser analisado por equipes de análise humana. Você examina rapidamente grandes volumes de conteúdo usando essa abordagem.

![Analisar resultados](images/reviewresults.png)

Aprenda a usar todos os [recursos da ferramenta de análise](Review-Tool-User-Guide/human-in-the-loop.md) ou continue com a próxima seção para saber mais sobre as APIs. Ignore a etapa de inscrição porque você tem a chave de API provisionada na ferramenta de análise, como mostrado no artigo [Gerenciando credenciais](review-tool-user-guide/credentials.md).

### <a name="use-the-apis"></a>Usar as APIs

Saiba como integrar o Content Moderator com seus aplicativos de negócios. Confira a [referência de API](api-reference.md) e os [SDKs](sdk-and-samples.md#sdks-for-python-java-nodejs-and-net).

## <a name="subscribe-in-the-azure-portal"></a>Assinar no portal do Azure

[Assine o Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) no portal do Azure. Inicie com uma das seguintes APIs:

### <a name="image-moderation"></a>Moderação de imagem

Comece com o [console da API](try-image-api.md) ou use o [início rápido do .NET](image-moderation-quickstart-dotnet.md) para examinar as imagens e detectar conteúdos adulto e racista em potencial usando marcas, pontuações de confiança e outras informações extraídas.

### <a name="text-moderation"></a>Moderação de texto

Comece com o [console da API](try-text-api.md) ou use o [início rápido do .NET](text-moderation-quickstart-dotnet.md) para examinar o conteúdo de texto com obscenidades em potencial, classificação de texto indesejado assistido por computador (visualização) e informações de identificação pessoal (PII). 


### <a name="video-moderation"></a>Moderação de vídeo

Comece com o [início rápido do .NET](video-moderation-api.md) para examinar vídeos e detectar conteúdos adulto e racista em potencial. 


### <a name="review-apis"></a>Analisar APIs

Comece aqui escolhendo entre as APIs de Trabalho, Revisão e Fluxo de Trabalho.

- A [API de Trabalho](try-review-api-job.md) examina o conteúdo por meio de APIs de moderação e gera análises na ferramenta de análise. 
- A [API de Revisão](try-review-api-review.md) cria diretamente análises de imagem, texto ou vídeo para moderadores humanos sem primeiro examinar o conteúdo. 
- A [API do Fluxo de Trabalho](try-review-api-workflow.md) cria, atualiza e obtém detalhes sobre os fluxos de trabalho personalizados que sua equipe cria.

## <a name="next-steps"></a>Próximas etapas

Confira a [referência de API](api-reference.md) e os [SDKs](sdk-and-samples.md#sdks-for-python-java-nodejs-and-net). Inicie rapidamente sua integração com [amostras do SDK do .NET](sdk-and-samples.md#net-sdk-samples), [amostras da API REST em C#](https://github.com/sanjeev3/azure-docs-pr/blob/master/articles/cognitive-services/Content-Moderator/sdk-and-samples.md#rest-api-samples-in-c) e [tutoriais](sdk-and-samples.md#tutorials).
