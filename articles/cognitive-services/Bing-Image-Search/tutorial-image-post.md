---
title: Carregamento de imagem do Bing para Insights | Microsoft Docs
description: Aplicativo de console que usa a API de Pesquisa de Imagem do Bing para carregar uma imagem e obter insights de imagem.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 12/07/2017
ms.author: v-gedod
ms.openlocfilehash: f0bf32a9951527a072fffe464f6b5f50d0f237a2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363377"
---
# <a name="tutorial-bing-image-upload-for-insights"></a>Tutorial: Carregamento de imagem do Bing para insights

API de Pesquisa de Imagem do Bing fornece uma opção `POST` para carregar uma imagem e pesquisar informações pertinentes para a imagem. Este aplicativo de console C# carrega uma imagem usando o ponto de extremidade de Pesquisa de Imagem para obter detalhes sobre a imagem.
Os resultados rapidamente, são objetos JSON, como o seguinte:

![[Resultados JSON]](media/cognitive-services-bing-images-api/jsonResult.jpg)

Este tutorial explica como:

> [!div class="checklist"]
> * Usar o ponto de extremidade `/details` Pesquisa de Imagem em uma solicitação `POST`
> * Especificar os cabeçalhos da solicitação
> * Usar parâmetros de URL para especificar os resultados
> * Carregar os dados da imagem e enviar a solicitação `POST`
> * Imprimir os resultados JSON para o console

## <a name="app-components"></a>Componentes do aplicativo

O aplicativo tutorial inclui três partes:

> [!div class="checklist"]
> * Configuração de ponto de extremidade para especificar o ponto de extremidade de Pesquisa de Imagem do Bing e cabeçalhos necessários
> * Carregamento de arquivo de imagem para solicitação `POST` ao ponto de extremidade
> * Analisar os resultados JSON que são os detalhes retornados da solicitação `POST`

## <a name="scenario-overview"></a>Visão geral do cenário
Há [três pontos de extremidade de Pesquisa de Imagem](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-endpoint). O ponto de extremidade `/details` pode usar uma solicitação `POST` com dados de imagem no corpo da solicitação.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
O parâmetro de URL `modules` seguido de `/details?` especifica que tipo de detalhes os resultados contêm:
* `modules=All`
* `modules=RecognizedEntities` (pessoas ou locais visíveis na imagem)

Especifique `modules=All` na solicitação `POST` para obter o texto JSON que inclui a lista a seguir:
* `bestRepresentativeQuery` - uma consulta do Bing que retorna imagens semelhantes à imagem carregada
* `detectedObjects` como uma caixa delimitadora ou pontos de acesso na imagem
* `image` metadados
* `imageInsightsToken` - token para uma solicitação `GET` subsequente que obtém `RecognizedEntities` (pessoas ou locais visíveis na imagem) 
* `imageTags`
* `pagesIncluding` - Páginas da Web que incluem a imagem
* `relatedSearches`
* `visuallySimilarImages`

Especifique `modules=RecognizedEntities` na solicitação `POST` para obter apenas a `imageInsightsToken`, que é usado em uma solicitação `GET` subsequente. Ele identifica as pessoas ou locais visíveis na imagem.

## <a name="webclient-and-headers-for-the-post-request"></a>WebClient e cabeçalhos para a solicitação POST
Crie um objeto `WebClient` e defina os cabeçalhos. Todas as solicitações para a API de Pesquisa do Bing requerem um `Ocp-Apim-Subscription-Key`. Uma solicitação `POST` para carregar uma imagem também deve especificar `ContentType: multipart/form-data`.

```
            WebClient client = new WebClient();
            client.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
            client.Headers["ContentType"] = "multipart/form-data"; 
```

## <a name="upload-the-image-and-get-results"></a>Fazer upload da imagem e obter resultados

A classe `WebClient` inclui o método `UpLoadFile` que formata os dados para a solicitação `POST`. Ele formata o `RequestStream` e chamadas `HttpWebRequest`, evitando uma grande parte da complexidade.
Chame `WebClient.UpLoadFile` com o ponto de extremidade `/details` e o arquivo de imagem a ser carregado. A resposta é dados binários que são facilmente convertidos em JSON. 

Use o texto JSON para inicializar uma instância da estrutura `SearchResult` (consulte o [código fonte do aplicativo](tutorial-image-post-source.md) para contexto).
```        
         const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/details";

        // The image to upload. Replace with your file and path.
        const string imageFile = "ansel-adams-tetons-snake-river.jpg";
            
        byte[] resp = client.UploadFile(uriBase + "?modules=All", imageFile);
        var json = System.Text.Encoding.Default.GetString(resp);

        // Create result object for return
        var searchResult = new SearchResult()
        {
            jsonResult = json,
            relevantHeaders = new Dictionary<String, String>()
        };
```

## <a name="print-the-results"></a>Imprima os resultados
O restante do código analisa o resultado JSON e o imprime no console.

```
        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            char last = ' ';
            int offset = 0;
            int indentLength = 2;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\\':
                        if (quote && last != '\\') ignore = true;
                        break;
                }

                if (quote)
                {
                    sb.Append(ch);
                    if (last == '\\' && ignore) ignore = false;
                }
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (quote || ch != ' ') sb.Append(ch);
                            break;
                    }
                }
                last = ch;
            }

            return sb.ToString().Trim();
        }
```
## <a name="get-request-using-the-imageinsightstoken"></a>Solicitação GET usando o ImageInsightsToken
Para usar o `ImageInsightsToken` retornado com os resultados de uma `POST`, crie uma solicitação `GET` semelhante à seguinte:
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details?InsightsToken="bcid_A2C4BB81AA2C9EF8E049C5933C546449*ccid_osS7gaos*mid_BF7CC4FC4A882A3C3D56E644685BFF7B8BACEAF2
```
Se houver pessoas ou locais identificáveis na imagem, essa solicitação retornará informações sobre eles.
O [Início Rápido](https://docs.microsoft.com/azure/cognitive-services/bing-image-search) contém vários exemplos de código.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API de Pesquisa de Imagem do Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)

