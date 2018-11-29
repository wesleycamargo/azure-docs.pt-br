---
title: Início Rápido do Python – prever a intenção – LUIS
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você aprenderá a chamar um aplicativo LUIS usando Python.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: bec438933e83bfc0abf4930935f74a1af731565c
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52422330"
---
# <a name="quickstart-get-intent-using-python"></a>Início Rápido: Obter a intenção usando o Python
Neste início rápido, passe enunciados para um ponto de extremidade LUIS e obtenha intenção e entidades.

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Python 3.6](https://www.python.org/downloads/) ou posterior.
* [Visual Studio Code](https://code.visualstudio.com/)

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Obter chave LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Obter a intenção com o navegador

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent--programmatically"></a>Obter a intenção de forma programática

Você pode usar o Python para acessar os mesmos resultados que você viu na janela do navegador na etapa anterior.

1. Copie um dos seguintes snippets de código em um arquivo chamado `quickstart-call-endpoint.py`:

   [!code-python[Console app code that calls a LUIS endpoint for Python 2.7](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/2.x/quickstart-call-endpoint-2-7.py)]

   [!code-python[Console app code that calls a LUIS endpoint for Python 3.6](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/3.x/quickstart-call-endpoint-3-6.py)]

2. Substitua o valor do campo `Ocp-Apim-Subscription-Key` pela sua chave de ponto de extremidade do LUIS.

3. Instale as dependências com `pip install requests`.

4. Execute o script com `python ./quickstart-call-endpoint.py`. Ele exibe o mesmo JSON que você viu anteriormente na janela do navegador.

## <a name="luis-keys"></a>Chaves de LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Limpar recursos
Exclua o arquivo do Python. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar declarações](luis-get-started-python-add-utterance.md)
