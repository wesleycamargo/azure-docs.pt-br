---
title: 'Início Rápido: usar um modelo de domínio – REST, PHP – Pesquisa Visual Computacional'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você usa um modelo de domínio para identificar pontos de referência em uma imagem usando a API de Pesquisa Visual Computacional com PHP.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.openlocfilehash: 176e11615fe2f2c1b58b7608dfd5cbd349f9e547
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49340062"
---
# <a name="quickstart-use-a-domain-model-using-the-rest-api-and-php-in-computer-vision"></a>Início Rápido: usar um modelo de domínio usando a API REST e o PHP na Pesquisa Visual Computacional

Neste início rápido, você usa um modelo de domínio para identificar pontos de referência ou, opcionalmente, celebridades em uma imagem armazenada remotamente usando a API REST da Pesquisa Visual Computacional. Com o método [Recognize Domain Specific Content](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200), você pode aplicar um modelo específico do domínio para reconhecer o conteúdo dentro de uma imagem.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- É necessário ter o [PHP](https://secure.php.net/downloads.php) instalado.
- É necessário ter o [Pear](https://pear.php.net) instalado.
- Você precisa ter uma chave de assinatura para a Pesquisa Visual Computacional. Para obter uma chave de assinatura, confira [Obter chaves de assinatura](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="create-and-run-the-sample"></a>Criar e executar o exemplo

Para criar e executar o exemplo, siga estas etapas:

1. Instale o pacote [`HTTP_Request2`](http://pear.php.net/package/HTTP_Request2) PHP5.
   1. Abra uma janela de prompt de comando como administrador.
   1. Execute o comando a seguir:

      ```console
      pear install HTTP_Request2
      ```

   1. Depois que o pacote for instalado com êxito, feche a janela do prompt de comando.

1. Copie o código a seguir em um editor de texto.
1. Faça as alterações a seguir no código quando necessário:
    1. Substitua o valor de `subscriptionKey` pela sua chave de assinatura.
    1. Substitua o valor de `uriBase` pela URL do ponto de extremidade para o método [Recognize Domain Specific Content](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) da região do Azure em que você adquiriu suas chaves de assinatura, se necessário.
    1. Outra opção é substituir o valor de `imageUrl` pela URL de uma imagem diferente que você deseja analisar.
    1. Opcionalmente, substitua o valor do parâmetro de solicitação `domain` por `celebrites` se você quiser usar o modelo de domínio `celebrities` em vez do modelo de domínio `landmarks`.
1. Salve o código como um arquivo com uma extensão `.php`. Por exemplo, `use-domain-model.php`.
1. Abra uma janela do navegador com suporte para PHP.
1. Arraste e solte o arquivo na janela do navegador.

```php
<html>
<head>
    <title>Analyze Domain Model Sample</title>
</head>
<body>
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to obtain
// your subscription keys. For example, if you obtained your subscription keys
// from westus, replace "westcentralus" in the URL below with "westus".
$uriBase = 'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/';

// Change 'landmarks' to 'celebrities' to use the Celebrities model.
$domain = 'landmarks';

$imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/2/23/Space_Needle_2011-07-04.jpg';

require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . 'models/' . $domain . '/analyze');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'model' => $domain
);
$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body parameters
$body = json_encode(array('url' => $imageUrl));

// Request body
$request->setBody($body);

try
{
    $response = $request->send();
    echo "<pre>" .
        json_encode(json_decode($response->getBody()), JSON_PRETTY_PRINT) . "</pre>";
}
catch (HttpException $ex)
{
    echo "<pre>" . $ex . "</pre>";
}
?>
</body>
</html>
```

## <a name="examine-the-response"></a>Examinar a resposta

Uma resposta com êxito é retornada em JSON. O site de exemplo analisa e exibe uma resposta bem-sucedida na janela do navegador, semelhante ao exemplo a seguir:

```json
{
    "result": {
        "landmarks": [
            {
                "name": "Space Needle",
                "confidence": 0.9998177886009216
            }
        ]
    },
    "requestId": "4d26587b-b2b9-408d-a70c-1f8121d84b0d",
    "metadata": {
        "height": 4132,
        "width": 2096,
        "format": "Jpeg"
    }
}
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o arquivo e, em seguida, desinstale o pacote `HTTP_Request2` PHP5. Para desinstalar o pacote, siga estas etapas:

1. Abra uma janela de prompt de comando como administrador.
2. Execute o comando a seguir:

   ```console
   pear uninstall HTTP_Request2
   ```

3. Depois que o pacote for desinstalado com êxito, feche a janela do prompt de comando.

## <a name="next-steps"></a>Próximas etapas

Explore a API da Pesquisa Visual Computacional usada para analisar uma imagem, detectar celebridades e pontos de referência, criar uma miniatura e extrair textos manuscritos e impressos. Para testar rapidamente a API da Pesquisa Visual Computacional, experimente o [Abrir o console de teste de API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Explorar a API da Pesquisa Visual Computacional](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
