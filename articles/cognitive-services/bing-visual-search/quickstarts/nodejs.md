---
title: Início rápido do JavaScript para API de Pesquisa Visual do Bing | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Mostra como fazer upload de uma imagem para a API de Pesquisa Visual do Bing e obter insights sobre a imagem.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 60b1dc9b8ea9eda258e9776b8967df38c97d964e
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071694"
---
# <a name="your-first-bing-visual-search-query-in-javascript"></a>Sua primeira consulta de Pesquisa Visual do Bing em JavaScript

API de Pesquisa Visual do Bing retorna informações sobre uma imagem que você fornece. Você pode fornecer a imagem usando a URL da imagem, um token de insights ou fazendo upload de uma imagem. Para obter informações sobre essas opções, consulte [O que é a API de Pesquisa Visual do Bing?](../overview.md) Este artigo demonstra como fazer upload de uma imagem. Fazer upload de uma imagem pode ser útil em cenários móveis em que você tira uma foto de um ponto de referência conhecido e obtém informações sobre ele. Por exemplo, os insights podem incluir fatos secundários sobre o ponto de referência. 

Se você fizer upload de uma imagem local, inclua no corpo do POST os dados de formulário mostrados a seguir. Os dados de formulário precisam incluir o cabeçalho Content-Disposition. Seu parâmetro `name` precisa ser definido como "image" e o parâmetro `filename` pode ser definido como qualquer cadeia de caracteres. O conteúdo do formulário é o binário da imagem. O tamanho máximo de imagem que você pode fazer upload é 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Este artigo inclui um aplicativo de console simples que envia uma solicitação de API de Pesquisa Visual do Bing e exibe os resultados da pesquisa JSON. Embora esse aplicativo seja escrito em JavaScript, a API é um serviço Web RESTful compatível com qualquer linguagem de programação que pode fazer solicitações HTTP e analisar JSON. 

## <a name="prerequisites"></a>Pré-requisitos

É necessário ter o [Node.js 6](https://nodejs.org/en/download/) para executar esse código.

Para este guia de início rápido, você pode usar uma chave de assinatura de [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) ou uma chave de assinatura paga.

## <a name="running-the-application"></a>Executando o aplicativo

O exemplo a seguir mostra como enviar a mensagem usando FormData no Node.js.

Para executar o aplicativo, siga estas etapas:

1. Crie uma pasta para o seu projeto (ou use seu IDE ou editor favorito).
2. Em um prompt de comando ou terminal, navegue até a pasta que você acabou de criar.
3. Instale os módulos de solicitação:  
  ```  
  npm install request  
  ```  
3. Instale os módulos de dados do formulário:  
  ```  
  npm install form-data  
  ```  
4. Crie um arquivo nomeado GetVisualInsights.js e adicione o seguinte código a ele.
5. Substitua o valor `subscriptionKey` pela sua chave de assinatura.
6. Substitua o valor `imagePath` pelo caminho da imagem da qual o upload será feito.
7. Execute o programa.  
  ```
  node GetVisualInsights.js
  ```

```javascript
var request = require('request');
var FormData = require('form-data');
var fs = require('fs');

var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
var subscriptionKey = '<yoursubscriptionkeygoeshere>';
var imagePath = "<pathtoyourimagegoeshere>";

var form = new FormData();
form.append("image", fs.createReadStream(imagePath));

form.getLength(function(err, length){
  if (err) {
    return requestCallback(err);
  }

  var r = request.post(baseUri, requestCallback);
  r._form = form; 
  r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
});

function requestCallback(err, res, body) {
    console.log(JSON.stringify(JSON.parse(body), null, '  '))
}
```


## <a name="next-steps"></a>Próximas etapas

[Obter insights sobre uma imagem usando um token de insights](../use-insights-token.md)  
[Tutorial de upload de imagem da Pesquisa Visual do Bing](../tutorial-visual-search-image-upload.md)
[Tutorial de aplicativo de página única da Pesquisa Visual do Bing](../tutorial-bing-visual-search-single-page-app.md)  
[Visão geral da Pesquisa Visual do Bing](../overview.md)  
[Experimente](https://aka.ms/bingvisualsearchtryforfree)  
[Obter uma chave de acesso de avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Referência de API de Pesquisa Visual do Bing](https://aka.ms/bingvisualsearchreferencedoc)
