---
title: Início rápido do C# para Visualização de URL de Projeto - Serviços Cognitivos da Microsoft | Microsoft Docs
description: Comece a usar a Visualização de URL de Projeto dos Serviços Cognitivos da Microsoft no Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/16/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 17d44bd0c23d0a1e67da5a0e91248700d3166c1a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364233"
---
# <a name="url-preview-query-in-c"></a>Consulta de Visualização de URL em C#

O exemplo de C# a seguir cria uma Visualização de Url para o site SwiftKey: https://swiftkey.com/en.

## <a name="prerequisites"></a>pré-requisitos

Você vai precisar do [Visual Studio 2017](https://www.visualstudio.com/downloads/) para executar este código no Windows. (A Edição Comunitária gratuita funcionará.)

Obtenha uma chave de acesso para a avaliação gratuita de [Laboratórios dos Serviços Cognitivos](https://aka.ms/answersearchsubscription)

## <a name="code-scenario"></a>Cenário do código

O código de C# a seguir cria uma Visualização de URL para o site SwiftKey: https://swiftkey.com/en. 

Ele é implementado nas etapas a seguir:
1. Declare variáveis para especificar o ponto de extremidade e uma URL de consulta para visualizar.  
2. Criar a solicitação.
3. Adicione o cabeçalho *Ocp-Apim-assinatura-chave*. 
4. Execute a solicitação da Web de forma assíncrona. 
5. Leia a resposta.
6. Imprima os cabeçalhos e resultados JSON no console.

**Código-fonte**

```
using System;
using System.IO;
using System.Net;
using System.Text;

namespace UrlPrevCshp
{
    class Program
    {
        static void Main(string[] args)
        {
            String uriBase = "https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search";
            String searchQuery = "https://swiftkey.com/en"; 
            var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery);

            // Do the Web request and get response
            WebRequest request = HttpWebRequest.Create(uriQuery);
            request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR-SUBSCRIPTION-KEY";
            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            Console.WriteLine("\nHTTP Headers:\n");
            foreach (String header in response.Headers)
            {
                if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
                    Console.WriteLine(header + ": " + response.Headers[header]);
            }

            Console.WriteLine(JsonPrettyPrint(json));
            

            Console.ReadKey();
        }


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

    }
}

```
## <a name="running-the-application"></a>Executando o aplicativo

Para executar o aplicativo:

1. Crie uma nova solução de console no Visual Studio.
2. Substitua `Program.cs` pelo código fornecido.
3. Substitua o valor `YOUR-ACCESS-KEY` por uma chave de acesso válida para a assinatura.
4. Execute o programa.

## <a name="next-steps"></a>Próximas etapas
- [Início rápido em Java](java-quickstart.md)
- [Início rápido em JavaScript](javascript.md)
- [Início rápido em Node](node-quickstart.md)
- [Início rápido em Python](python-quickstart.md)