---
title: Início Rápido para analisar o texto de linguagem natural em Reconhecimento Vocal (LUIS) usando PHP – Serviços Cognitivos – Serviços Cognitivos do Azure | Microsoft Docs
description: Neste início rápido, use um aplicativo LUIS público disponível para determinar a intenção do usuário do texto de conversa. Usando PHP, envie a intenção do usuário como texto para o ponto de extremidade de previsão de HTTP do aplicativo público. No ponto de extremidade, LUIS aplica o modelo do aplicativo público para analisar o texto de idioma natural quanto ao significado, determinando a intenção geral e extraindo dados relevantes para o domínio do assunto do aplicativo.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: 14ac32ffc0f4332d0cbc0da59a55ccc500e216d7
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "43768534"
---
# <a name="quickstart-analyze-text-using-php"></a>Início Rápido: Analisar texto usando PHP

[!include[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Pré-requisitos

* Linguagem de programação [PHP](http://php.net/)
* [Visual Studio Code](https://code.visualstudio.com/)
* ID do aplicativo público: df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Obter chave LUIS

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>Analisar o texto com navegador

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-php"></a>Analisar texto com PHP 

Você pode usar o PHP para acessar os mesmos resultados que você viu na janela do navegador na etapa anterior. 

1. Copie o código a seguir e salve-o com um nome de arquivo `endpoint-call.php`:

   [!code-php[PHP code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/php/endpoint-call.php)]

2. Substitua `"YOUR-KEY"` pela sua chave de ponto de extremidade.

3. Executar o aplicativo PHP com `php endpoint-call.php`. Ele exibe o mesmo JSON que você viu anteriormente na janela do navegador.

## <a name="luis-keys"></a>Chaves de LUIS

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Exclua o arquivo PHP.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar declarações](luis-get-started-php-add-utterance.md)