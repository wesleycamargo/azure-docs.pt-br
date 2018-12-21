---
title: 'Início Rápido: usar o Postman para obter respostas da base de dados de conhecimento – QnA Maker'
titlesuffix: Azure Cognitive Services
description: Este Início Rápido fornece uma orientação para obtenção de uma resposta de uma base de dados de conhecimento usando Postman.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 12/11/2018
ms.author: diberry
ms.openlocfilehash: 476e982bdddd41c1daf06c3a673d964ce2a85f98
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53270885"
---
# <a name="quickstart-get-an-answer-from-knowledge-base-using-postman"></a>Início Rápido: obter uma resposta da base de dados de conhecimento usando o Postman

Este Início Rápido baseado no Postman fornece uma orientação para obtenção de uma resposta de uma base de dados de conhecimento.

## <a name="prerequisites"></a>Pré-requisitos

* [**Postman**](https://www.getpostman.com/) mais recente.
* Você deve ter um [serviço do QnA Maker](../How-To/set-up-qnamaker-service-azure.md) e uma [base de dados de conhecimento com perguntas e respostas](../Tutorials/create-publish-query-in-portal.md). 

## <a name="publish-to-get-endpoint"></a>Publicar para obter ponto de extremidade

Quando você estiver pronto para gerar uma resposta a uma pergunta da sua base de dados de conhecimento, [publique](../How-to/publish-knowledge-base.md) sua base de dados de conhecimento.

## <a name="use-production-endpoint-with-postman"></a>Usar o ponto de extremidade de produção com o Postman

Quando sua base de dados de conhecimento for publicada, a página **Publicar** exibirá as configurações de solicitação HTTP para gerar uma resposta. A exibição padrão mostra as configurações necessárias para gerar uma resposta do [Postman](https://www.getpostman.com).

[![Publicar os resultados](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png)](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png#lightbox)

Para gerar uma resposta com o Postman, conclua as seguintes etapas:

1. Abra o Postman. 
1. Selecione o bloco de construção para criar uma solicitação básica.
1. Defina o **Nome da solicitação** como `Generate QnA Maker answer` e a **coleção** como `Generate QnA Maker answers`.
1. No workspace, selecione o método HTTP de **POST**.
1. Para a URL, concatene o valor HOST e o valor Post para criar a URL completa. 

    [![No Postman, defina o método como Post e a URL completa](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png#lightbox)

1. Selecione a guia **Cabeçalhos** sob a URL e, em seguida, selecione **Edição em massa**. 
1. Copie os cabeçalhos na área de texto.

    [![No Postman, defina os cabeçalhos](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png#lightbox)

1. Selecione a guia **Corpo**.
1. Selecione o formato **bruto** e insira o JSON que representa a pergunta.

    [![No Postman, defina o valor JSON de corpo](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png#lightbox)

1. Selecione o botão **Enviar**.
1. A resposta contém a resposta juntamente com outras informações que podem ser importantes para o aplicativo cliente. 

    [![No Postman, defina o valor JSON de corpo](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png)](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png#lightbox)

## <a name="use-staging-endpoint-with-curl"></a>Usar o ponto de extremidade de preparo com cURL

Se quiser obter uma resposta do ponto de extremidade de preparo, use o parâmetro booliano de querystring `isTest` com o valor de `true`.

`isTest=true`

## <a name="next-steps"></a>Próximas etapas

A página de publicação também fornece informações para [gerar uma resposta](get-answer-from-kb-using-curl.md) com cURL. 

> [!div class="nextstepaction"]
> [Usar metadados ao gerar uma resposta](../How-to/metadata-generateanswer-usage.md)