---
title: Introdução do Azure Content Moderator | Microsoft Docs
description: Como começar com o Azure Content Moderator.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/15/2018
ms.author: sajagtap
ms.openlocfilehash: ae4333047ebd95733c7baaed0323a0c2c477d323
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363746"
---
# <a name="get-started-with-content-moderator"></a>Introdução ao Content Moderator

Comece a usar as APIs do Content Moderator e a ferramenta de análise das seguintes maneiras:

- [Inicie com a ferramenta de análise](#start-with-the-review-tool) para criar as chaves de API e uma equipe de análise. Explore a ferramenta de análise e saiba como integrar usando as APIs do Content Moderator.
- [Assine o Content Moderator](#start-with-the-apis) no portal do Azure. Você ainda precisa se inscrever online para criar uma equipe de análise.
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

Agora que você explorou as experiências de moderação de conteúdo e ferramenta de análise, saiba como integrar o Content Moderator em seus aplicativos de negócios. Use a seção a seguir para saber mais e acelerar seu conhecimento com os SDKs e exemplos.

## <a name="start-with-the-apis"></a>Iniciar com as APIs

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

Saiba mais sobre como iniciar a moderação de conteúdo com a [API de moderação de imagem](image-moderation-api.md).
