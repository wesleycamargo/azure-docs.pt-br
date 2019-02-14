---
title: 'Início Rápido: Obter respostas da base de dados de conhecimento – REST, C# – QnA Maker'
titlesuffix: Azure Cognitive Services
description: Este início rápido de C# baseado em REST fornece uma orientação para obtenção de uma resposta de uma base de dados de conhecimento de forma programática.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 11/19/2018
ms.author: diberry
ms.openlocfilehash: f2b5089f1ec334f80ccb4ce80ba9b512f3305859
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55861112"
---
# <a name="get-answers-to-a-question-from-a-knowledge-base-with-c"></a>Obter respostas a uma pergunta de uma base de dados de conhecimento com C#

Este início rápido o orienta a obter programaticamente uma resposta de uma base de dados de conhecimento publicada do QnA Maker. O QnA Maker extrai automaticamente perguntas e respostas de conteúdos semiestruturados, como perguntas frequentes, de [fontes de dados](../Concepts/data-sources-supported.md). A pergunta, no formato JSON, é enviada no corpo da solicitação de API. 


## <a name="prerequisites"></a>Pré-requisitos

* [**Edição mais recente do Visual Studio Community**](https://www.visualstudio.com/downloads/).
* Você precisa ter um [serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Para recuperar sua chave, selecione **Chaves** em **Gerenciamento de Recursos** no painel do Azure para seu recurso do QnA Maker. 
* Configurações da página **Publicar**. Caso não tenha uma base de dados de conhecimento publicada, crie uma base de dados de conhecimento vazia, importe-a na página **Configurações** e depois publique-a. Você pode baixar e usar [essa base de dados de conhecimento básico](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv). 

    As configurações da página de publicação incluem o valor da rota, o valor do Host e o valor do EndpointKey. 

    ![Configurações de publicação](../media/qnamaker-quickstart-get-answer/publish-settings.png)

O código deste início rápido está no repositório [https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/get-answer). 

## <a name="create-a-knowledge-base-project"></a>Criar um projeto da base de dados de conhecimento

1. Abra o Visual Studio 2017 Community Edition.
1. Crie um novo projeto de Aplicativo de Console (.NET Core) e nomeie-o QnaMakerQuickstart. Aceite os padrões para as configurações restantes.

## <a name="add-the-required-dependencies"></a>Adicione as dependências necessárias

Na parte superior do arquivo Program.cs, substitua a única instrução using pelas linhas abaixo para adicionar as dependências necessárias ao projeto:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=1-3 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias

Na parte superior da classe `Program`, dentro do `Main`, adicione as constantes necessárias para acessar o QnA Maker. Esses valores ficam na página **Publicar** após a publicação da base de dados de conhecimento. 

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=14-30 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-answer"></a>Adicionar uma solicitação POST para enviar perguntas e obter uma resposta

O código a seguir faz uma solicitação HTTPS à API de QnA Maker para enviar a pergunta à base de dados de conhecimento e recebe a resposta:

[!code-csharp[Add a POST request to send question to knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=32-57 "Add a POST request to send question to knowledge base")]

O valor do cabeçalho de `Authorization` inclui a cadeia de caracteres `EndpointKey `. 

## <a name="build-and-run-the-program"></a>Compilar e executar o programa

Compilar e executar o programa no Visual Studio. Ela enviará automaticamente a solicitação para a API de QnA Maker, depois a resposta será impressa na janela do console.

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)] 

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST do QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
