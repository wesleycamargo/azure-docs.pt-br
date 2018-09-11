---
title: Início rápido de C# da API de Pesquisa Visual Computacional – analisar imagem local | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Neste início rápido, você analisa uma imagem local usando a Pesquisa Visual Computacional com C# nos Serviços Cognitivos.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 8196787df9f7fadedda72d525aee440afe7b7d34
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43768575"
---
# <a name="quickstart-analyze-a-local-image---rest-c35"></a>Início Rápido: Analisar uma imagem local – REST, C&#35;

Neste início rápido, você analisa uma imagem local para extrair recursos visuais usando a Pesquisa Visual Computacional.

## <a name="prerequisites"></a>Pré-requisitos

Para usar a Pesquisa Visual Computacional, você precisa de uma chave de assinatura. Veja [Obter chaves de assinatura](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="analyze-image-request"></a>Analisar a solicitação da Imagem

Com o [método Analisar Imagem](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa), você pode extrair recursos visuais com base no conteúdo de imagem. Você pode carregar uma imagem ou especificar uma URL de imagem e escolher quais recursos retornar, incluindo:

* Uma lista detalhada de marcas relacionadas ao conteúdo da imagem.
* Uma descrição do conteúdo de imagem em uma frase completa.
* As coordenadas, o gênero e a idade de qualquer face contida na imagem.
* O ImageType (clip-art ou desenho de linha).
* A cor dominante, a cor de ênfase ou se uma imagem é em preto e branco.
* A categoria definida nessa [taxonomia](../Category-Taxonomy.md).
* A imagem contém conteúdo adulto ou sexualmente sugestivo?

Para executar a amostra, siga estas etapas:

1. Crie um novo aplicativo de console do Visual C# no Visual Studio.
1. Instale o pacote do NuGet Newtonsoft.Json.
    1. No menu, clique em **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** e, em seguida, **Gerenciar Pacotes NuGet para a Solução**.
    1. Clique na guia **Procurar** e, na caixa **Pesquisar**, digite "Newtonsoft.Json".
    1. Selecione **Newtonsoft.Json** quando for exibida, clique na caixa de seleção ao lado do nome do projeto e escolha **Instalar**.
1. Substitua `Program.cs` pelo código a seguir.
1. Substitua `<Subscription Key>` pela sua chave de assinatura válida.
1. Altere o valor `uriBase` para o local em que você adquiriu suas chaves de assinatura, se necessário.
1. Execute o programa.
1. No prompt, digite o caminho para uma imagem local.

```csharp
using Newtonsoft.Json.Linq;
using System;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;

namespace CSHttpClientSample
{
    static class Program
    {
        // Replace <Subscription Key> with your valid subscription key.
        const string subscriptionKey = "<Subscription Key>";

        // You must use the same region in your REST call as you used to
        // get your subscription keys. For example, if you got your
        // subscription keys from westus, replace "westcentralus" in the URL
        // below with "westus".
        //
        // Free trial subscription keys are generated in the westcentralus region.
        // If you use a free trial subscription key, you shouldn't need to change
        // this region.
        const string uriBase =
            "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze";

        static void Main()
        {
            // Get the path and filename to process from the user.
            Console.WriteLine("Analyze an image:");
            Console.Write("Enter the path to the image you wish to analyze: ");
            string imageFilePath = Console.ReadLine();

            if (File.Exists(imageFilePath))
            {
                // Make the REST API call.
                Console.WriteLine("\nWait a moment for the results to appear.\n");
                MakeAnalysisRequest(imageFilePath).Wait();
            }
            else
            {
                Console.WriteLine("\nInvalid file path");
            }
            Console.WriteLine("\nPress Enter to exit...");
            Console.ReadLine();
        }

        /// <summary>
        /// Gets the analysis of the specified image file by using
        /// the Computer Vision REST API.
        /// </summary>
        /// <param name="imageFilePath">The image file to analyze.</param>
        static async Task MakeAnalysisRequest(string imageFilePath)
        {
            try
            {
                HttpClient client = new HttpClient();

                // Request headers.
                client.DefaultRequestHeaders.Add(
                    "Ocp-Apim-Subscription-Key", subscriptionKey);

                // Request parameters. A third optional parameter is "details".
                string requestParameters =
                    "visualFeatures=Categories,Description,Color";

                // Assemble the URI for the REST API Call.
                string uri = uriBase + "?" + requestParameters;

                HttpResponseMessage response;

                // Request body. Posts a locally stored JPEG image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (ByteArrayContent content = new ByteArrayContent(byteData))
                {
                    // This example uses content type "application/octet-stream".
                    // The other content types you can use are "application/json"
                    // and "multipart/form-data".
                    content.Headers.ContentType =
                        new MediaTypeHeaderValue("application/octet-stream");

                    // Make the REST API call.
                    response = await client.PostAsync(uri, content);
                }

                // Get the JSON response.
                string contentString = await response.Content.ReadAsStringAsync();

                // Display the JSON response.
                Console.WriteLine("\nResponse:\n\n{0}\n",
                    JToken.Parse(contentString).ToString());
            }
            catch (Exception e)
            {
                Console.WriteLine("\n" + e.Message);
            }
        }

        /// <summary>
        /// Returns the contents of the specified file as a byte array.
        /// </summary>
        /// <param name="imageFilePath">The image file to read.</param>
        /// <returns>The byte array of the image data.</returns>
        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            using (FileStream fileStream =
                new FileStream(imageFilePath, FileMode.Open, FileAccess.Read))
            {
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
}
```

## <a name="analyze-image-response"></a>Analisar a resposta da Imagem

Uma resposta bem-sucedida é retornada em JSON, por exemplo:

```json
{
   "categories": [
      {
         "name": "abstract_",
         "score": 0.00390625
      },
      {
         "name": "others_",
         "score": 0.0234375
      },
      {
         "name": "outdoor_",
         "score": 0.00390625
      }
   ],
   "description": {
      "tags": [
         "road",
         "building",
         "outdoor",
         "street",
         "night",
         "black",
         "city",
         "white",
         "light",
         "sitting",
         "riding",
         "man",
         "side",
         "empty",
         "rain",
         "corner",
         "traffic",
         "lit",
         "hydrant",
         "stop",
         "board",
         "parked",
         "bus",
         "tall"
      ],
      "captions": [
         {
            "text": "a close up of an empty city street at night",
            "confidence": 0.7965622853462756
         }
      ]
   },
   "requestId": "dddf1ac9-7e66-4c47-bdef-222f3fe5aa23",
   "metadata": {
      "width": 3733,
      "height": 1986,
      "format": "Jpeg"
   },
   "color": {
      "dominantColorForeground": "Black",
      "dominantColorBackground": "Black",
      "dominantColors": [
         "Black",
         "Grey"
      ],
      "accentColor": "666666",
      "isBWImg": true
   }
}
```

## <a name="next-steps"></a>Próximas etapas

Explore um aplicativo do Windows que usa a Pesquisa Visual Computacional para executar o OCR (reconhecimento óptico de caracteres), criar miniaturas com recorte inteligente e detectar, categorizar, marcar e descrever recursos visuais, incluindo rostos, em uma imagem. Para experimentar rapidamente as APIs de Pesquisa Visual Computacional, tente o [Console de teste de API aberta](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).


> [!div class="nextstepaction"]
> [Usar Pesquisa Visual Computacional com C#](../Tutorials/CSharpTutorial.md)
