---
title: Analisar o texto de linguagem natural em Reconhecimento Vocal (LUIS) usando JavaScript – Serviços Cognitivos – Serviços Cognitivos do Azure | Microsoft Docs
description: Neste início rápido, use um aplicativo LUIS público disponível para determinar a intenção do usuário do texto de conversa. Usando JavaScript, envie a intenção do usuário como texto para o ponto de extremidade de previsão de HTTP do aplicativo público. No ponto de extremidade, LUIS aplica o modelo do aplicativo público para analisar o texto de idioma natural quanto ao significado, determinando a intenção geral e extraindo dados relevantes para o domínio do assunto do aplicativo.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: d787f744ff0fe7315553e9dd6f4465122f7e06b2
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159700"
---
# <a name="quickstart-analyze-text-using-javascript"></a>Início Rápido: Analisar texto usando Javascript

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio Code](https://code.visualstudio.com/)
* ID do aplicativo público: df67dcdb-c37d-46af-88e1-8b97951ca1c2


## <a name="get-luis-key"></a>Obter chave LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>Analisar o texto com navegador

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-javascript"></a>Analisar texto com Javascript 

Você pode usar o JavaScript para acessar os mesmos resultados que você viu na janela do navegador na etapa anterior. 

1. Copie o código a seguir e salve-o em um arquivo HTML:

   [!code-html[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/javascript/call-endpoint.html)]

2. Abra o arquivo em um navegador. Insira a chave do ponto de extremidade do LUIS no formulário e selecione **Enviar**.

    ![Exemplo de HTML exibido no navegador com resultados do LUIS para o aplicativo de Automação Inicial](./media/luis-get-started-js-get-intent/html-results.png)

    O resultado é exibido abaixo do formulário. 

## <a name="luis-keys"></a>Chaves de LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Exclua o arquivo Javascript.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Adicionar declarações](luis-get-started-javascript-add-utterance.md)
