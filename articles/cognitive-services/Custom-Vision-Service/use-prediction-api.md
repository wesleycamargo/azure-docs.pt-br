---
title: 'Exemplo: usar o ponto de extremidade de previsão para testar imagens programaticamente com um classificador – Visão Personalizada'
titlesuffix: Azure Cognitive Services
description: Saiba como usar a API para testar programaticamente imagens com seu classificador de Serviço de Visão Personalizada.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: sample
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 3a81f3cef6aaeb5c98022d9fc93f4d84f3f58a6e
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46363642"
---
# <a name="use-the-prediction-endpoint-to-test-images-programmatically-with-a-custom-vision-service-classifier"></a>Usar o ponto de extremidade de previsão para testar imagens programaticamente com um classificador de Serviço de Visão Personalizada

Depois de treinar seu modelo, você poderá testar imagens programaticamente ao enviá-las para a API de Previsão. 

> [!NOTE]
> Este documento demonstra como usar o C# para enviar uma imagem para a API de Previsão. Para obter mais informações e exemplos de uso da API, veja a [referência da API de Previsão](https://go.microsoft.com/fwlink/?linkid=865445).

## <a name="get-the-url-and-prediction-key"></a>Obter a chave de URL e previsão

Na [página da Web Visão Personalizada](https://customvision.ai), selecione o projeto e então selecione a guia __Desempenho__. Para exibir informações sobre o uso da API de Previsão, incluindo a __Chave de previsão__, selecione __URL de Previsão__. Para projetos anexados a um Recurso do Azure, a __Chave de previsão__ também pode ser encontrada na página do [portal do Azure](https://portal.azure.com) do Recurso do Azure associado em __Chaves__. Copie as seguintes informações para uso no aplicativo:

* __URL__ para usar um __arquivo de imagem__.
* Valor __Prediction-key__.

> [!TIP]
> Se você tiver várias iterações, poderá controlar qual será usada definindo-a como padrão. Selecione a iteração da seção __Iterações__ e, em seguida, selecione __Tornar padrão__ na parte superior da página.

![A guia de desempenho é mostrada com um retângulo vermelho ao redor da URL da Previsão.](./media/use-prediction-api/prediction-url.png)

## <a name="create-the-application"></a>Criar o aplicativo

1. No Visual Studio, crie um novo aplicativo de console do C#.

2. Use o código a seguir como o corpo do arquivo __Program.cs__.

    > [!IMPORTANT]
    > Altere as seguintes informações:
    >
    > * Defina o __namespace__ como o nome do seu projeto.
    > * Defina o valor __Prediction-Key__ recebido anteriormente na linha que começa com `client.DefaultRequestHeaders.Add("Prediction-Key",`.
    > * Defina o valor de __URL__ recebido anteriormente na linha que começa com `string url =`.

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CSPredictionSample
    {
        static class Program
        {
            static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }

            static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid subscription key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "13hc77781f7e4b19b5fcdd72a8df7156");

                // Prediction URL - replace this example URL with your valid prediction URL.
                string url = "http://southcentralus.api.cognitive.microsoft.com/customvision/v1.0/prediction/d16e136c-5b0b-4b84-9341-6a3fff8fa7fe/image?iterationId=f4e573f6-9843-46db-8018-b01d034fd0f2";

                HttpResponseMessage response;

                // Request body. Try this sample with a locally stored image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (var content = new ByteArrayContent(byteData))
                {
                    content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                    response = await client.PostAsync(url, content);
                    Console.WriteLine(await response.Content.ReadAsStringAsync());
                }
            }
        }
    }
    ```

## <a name="use-the-application"></a>Use o aplicativo

Ao executar o aplicativo, você insere o caminho para um arquivo de imagem. A imagem é enviada para a API e os resultados são retornados como um documento JSON. No JSON a seguir há um exemplo da resposta

```json
{
    "Id":"3f76364c-b8ae-4818-a2b2-2794cfbe377a",
    "Project":"2277aca4-7aff-4742-8afb-3682e251c913",
    "Iteration":"84105bfe-73b5-4fcc-addb-756c0de17df2",
    "Created":"2018-05-03T14:15:22.5659829Z",
    "Predictions":[
        {"TagId":"35ac2ad0-e3ef-4e60-b81f-052a1057a1ca","Tag":"dog","Probability":0.102716163},
        {"TagId":"28e1a872-3776-434c-8cf0-b612dd1a953c","Tag":"cat","Probability":0.02037274}
    ]
}
```

## <a name="next-steps"></a>Próximas etapas

[Exportar o modelo para uso móvel](export-your-model.md)
