---
title: Início Rápido do Java – prever a intenção – LUIS
titleSuffix: Azure Cognitive Services
description: Neste início rápido, use um aplicativo LUIS público disponível para determinar a intenção do usuário do texto de conversa. Usando Java, envie a intenção do usuário como texto para o ponto de extremidade de previsão de HTTP do aplicativo público. No ponto de extremidade, LUIS aplica o modelo do aplicativo público para analisar o texto de idioma natural quanto ao significado, determinando a intenção geral e extraindo dados relevantes para o domínio do assunto do aplicativo.
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: c492a0d0a9d90a79b0e05d4e7de6f5ef0796184a
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50417524"
---
# <a name="quickstart-get-intent-using-java"></a>Início Rápido: Obter a intenção usando o Java

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Pré-requisitos

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* [Visual Studio Code](https://code.visualstudio.com/)
* ID do aplicativo público: df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Obter chave LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Obter a intenção com o navegador

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Obter a intenção de forma programática 

Você pode usar o Java para acessar os mesmos resultados que você viu na janela do navegador na etapa anterior. 

1. Copie o código a seguir para criar uma classe em um arquivo chamado `LuisGetRequest.java`:

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/java/call-endpoint.java)]

2. Substitua o valor da variável `YOUR-KEY` pela sua chave do LUIS.

3. Crie o programa Java com `javac -cp ":lib/*" LuisGetRequest.java`. 

4. Execute o aplicativo com `java -cp ":lib/*" LuisGetRequest.java`. Ele exibe o mesmo JSON que você viu anteriormente na janela do navegador.

    ![A janela de console exibe resultados JSON do LUIS](./media/luis-get-started-java-get-intent/console-turn-on.png)
    
## <a name="luis-keys"></a>Chaves de LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Exclua o arquivo Java. 

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Adicionar declarações](luis-get-started-java-add-utterance.md)