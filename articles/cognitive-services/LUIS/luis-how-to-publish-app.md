---
title: Publicar seu aplicativo LUIS no ponto de extremidade de previsão
titleSuffix: Azure Cognitive Services
description: Quando terminar de compilar e testar seu aplicativo LUIS ativo, disponibilize-o ao seu aplicativo cliente publicando-o no ponto de extremidade.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 54d3b66f6a452e97e1d354fa75eb1b21065ab3e2
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031264"
---
# <a name="publish-your-trained-app"></a>Publicar seu aplicativo treinado

Quando terminar de compilar e testar seu aplicativo LUIS ativo, disponibilize-o ao seu aplicativo cliente publicando-o no ponto de extremidade. 

<a name="publish-your-trained-app-to-an-http-endpoint"></a>

## <a name="publishing"></a>Publicação

Para publicar no ponto de extremidade, selecione **Publicar** no painel superior direito. 

![Barra de navegação superior direita](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

Selecione o slot correto quando a janela pop-up for exibida: de preparo ou produção. Usando os dois slots de publicação, isso permite que você tenha duas versões diferentes com pontos de extremidade publicados ou a mesma versão em dois pontos de extremidade diferentes. 

O aplicativo é publicado em todas as regiões associadas aos recursos do LUIS adicionados no portal do LUIS. Por exemplo, para um aplicativo criado em [www.luis.ai](https://www.luis.ai), se você criar um recurso de LUIS em **westus** e adicioná-lo ao aplicativo como um recurso, o aplicativo será publicado nessa região. Para obter mais informações sobre as regiões do LUIS, consulte [Regiões](luis-reference-regions.md).
 
![Janela pop-up de publicação](./media/luis-how-to-publish-app/publish-pop-up.png)

Quando seu aplicativo tiver sido publicado com êxito, uma notificação de êxito em verde será exibida na parte superior do navegador. A barra de notificação verde também inclui um link para os pontos de extremidade. 

![Janela pop-up de publicação](./media/luis-how-to-publish-app/publish-success.png)

Se você precisar da URL do ponto de extremidade, selecione o link. Você também pode obter as URLs do ponto de extremidade selecionando **Gerenciar** no menu superior, em seguida, selecione **Chaves e Pontos de Extremidade** no menu à esquerda. 

## <a name="configuring-publish-settings"></a>Definindo configurações de publicação

Defina configurações de publicação selecionando **Gerenciar** na barra de navegação na parte superior direita e, em seguida, selecionando **Configurações de Publicação**. 

![Configurações de publicação](./media/luis-how-to-publish-app/publish-settings.png)

### <a name="publish-after-enabling-sentiment-analysis"></a>Publicar após habilitar a análise de sentimento

<a name="enable-sentiment-analysis"></a>

A análise de sentimento permite ao LUIS fazer a integração com a [Análise de Texto](https://azure.microsoft.com/services/cognitive-services/text-analytics/) para fornecer análise de frase-chave e sentimento. 

Você não precisa fornecer uma chave de Análise de Texto e não é cobrando nenhum encargo para esse serviço na sua conta do Azure. Depois que você marcar essa configuração, ela será persistente. 

Dados de sentimento são uma pontuação entre 1 e 0 indicando o sentimento positivo (mais próximo de 1) ou negativo (mais próximo de 0) dos dados.

Para obter mais informações sobre a resposta do ponto de extremidade JSON com análise de sentimento, veja [Análise de sentimento](luis-concept-data-extraction.md#sentiment-analysis)



## <a name="next-steps"></a>Próximas etapas

* Consulte [Gerenciar chaves](./luis-how-to-manage-keys.md) para adicionar a chave de assinatura do Azure ao LUIS e saber como definir a chave de Verificação Ortográfica do Bing e incluir todas as intenções nos resultados.
* Veja [Treinar e testar seu aplicativo](luis-interactive-test.md) para obter instruções sobre como testar seu aplicativo publicado no console de teste.

