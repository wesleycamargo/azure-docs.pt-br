---
title: Tradução de Texto – traduzir texto com Go | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Neste início rápido, você traduz texto de um idioma para outro usando a API de Tradução de Texto com Go nos Serviços Cognitivos.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/29/2018
ms.author: nolachar
ms.openlocfilehash: 72d4a3bc7563d6427335f1536f787eab6d7a0de4
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2018
ms.locfileid: "43768480"
---
# <a name="quickstart-translate-text-with-go"></a>Início Rápido: Traduzir texto com Go

Neste início rápido, você traduz o texto de um idioma para outro usando a API de Tradução de Texto.

## <a name="prerequisites"></a>Pré-requisitos

Você precisará instalar a [distribuição do Go](https://golang.org/doc/install) para executar esse código. O exemplo de código usa apenas bibliotecas **principais**, portanto, não há dependências externas.

Para usar a API de Tradução de Texto, você também precisa de uma chave de assinatura. Veja [Como se inscrever para a API de Tradução de Texto](translator-text-how-to-signup.md).

## <a name="translate-request"></a>Solicitação de Traduzir

O código a seguir converte o texto de origem de um idioma para outro usando o método [Traduzir](./reference/v3-0-translate.md).

1. Crie um novo projeto Go em seu editor de código favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `subscriptionKey` por uma chave de acesso válida para a sua assinatura.
4. Salve o arquivo com uma extensão ".go".
5. Abra um prompt de comando em um computador com o Go instalado.
6. Crie o arquivo, por exemplo: 'go build quickstart-translate.go'.
7. Execute o arquivo, por exemplo: 'quickstart-translate'.

```golang
package translate

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strconv"
    "strings"
    "time"
)

func main() {
    // Replace the subscriptionKey string value with your valid subscription key
    const subscriptionKey = "<Subscription Key>"

    const uriBase = "https://api.cognitive.microsofttranslator.com"
    const uriPath = "/translate?api-version=3.0"

    // Translate to German and Italian
    const params = "&to=de&to=it"

    const uri = uriBase + uriPath + params

    const text = "Hello, world!"

    r := strings.NewReader("[{\"Text\" : \"" + text + "\"}]")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Content-Length", strconv.FormatInt(req.ContentLength, 10))
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    resp, err := client.Do(req)
    if err != nil {
        fmt.Printf("Error on request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("Error reading response body: %v\n", err)
        return
    }

    var f interface{}
    json.Unmarshal(body, &f)

    jsonFormatted, err := json.MarshalIndent(f, "", "  ")
    if err != nil {
        fmt.Printf("Error producing JSON: %v\n", err)
        return
    }
    fmt.Println(string(jsonFormatted))
}
```

## <a name="translate-response"></a>Resposta de Traduzir

Uma resposta bem-sucedida é retornada em JSON, conforme mostrado no seguinte exemplo:

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "translations": [
      {
        "text": "Hallo Welt!",
        "to": "de"
      },
      {
        "text": "Salve, mondo!",
        "to": "it"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Próximas etapas

Explore pacotes Go para APIs de Serviços Cognitivos do [SDK do Azure para linguagem Go](https://github.com/Azure/azure-sdk-for-go) no GitHub.

> [!div class="nextstepaction"]
> [Explore pacotes Go no GitHub](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices)
