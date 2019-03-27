---
title: 'Exemplo: Usar o ponto de extremidade de previsão para testar imagens programaticamente com um classificador – Visão Personalizada'
titlesuffix: Azure Cognitive Services
description: Saiba como usar a API para testar programaticamente imagens com seu classificador de Serviço de Visão Personalizada.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: article
ms.date: 03/26/2019
ms.author: anroth
ms.openlocfilehash: 715fa526c83608c9922315e3a0d89b67b31e0d16
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58472712"
---
#  <a name="use-your-model-with-the-prediction-api"></a>Usar o modelo com a API de previsão

Depois de treinar seu modelo, você poderá testar imagens programaticamente ao enviá-las para a API de Previsão.

> [!NOTE]
> Este documento demonstra como usar o C# para enviar uma imagem para a API de Previsão. Para obter mais informações e exemplos de uso da API, veja a [referência da API de Previsão](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>Publicar sua iteração treinada

Na [página da Web Visão Personalizada](https://customvision.ai), selecione o projeto e então selecione a guia __Desempenho__.

Para enviar imagens para a API de previsão, você primeiro precisará publicar sua iteração para previsão, que pode ser feito selecionando __publicar__ e especificar um nome para a iteração publicado. Isso permitirá que seu modelo seja acessível para a API de previsão do seu recurso do Azure de visão personalizada. 

![É mostrada na guia desempenho, com um retângulo vermelho ao redor do botão Publicar.](./media/use-prediction-api/unpublished-iteration.png)

Depois que seu modelo tiver sido publicado com êxito, você verá um rótulo "Publicado" aparecer ao lado de sua iteração na barra lateral esquerda, bem como o nome da iteração publicado na descrição da iteração.

![É mostrada na guia desempenho, com um retângulo vermelho em torno do rótulo publicado e o nome da iteração publicado.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Obter a chave de URL e previsão

Depois que seu modelo tiver sido publicado, você pode recuperar informações sobre como usar a API de previsão, selecionando __URL de previsão__. Isso abrirá uma caixa de diálogo semelhante à mostrada abaixo, com informações sobre como usar a API de previsão, incluindo o __URL de previsão__ e __chave de previsão__.

![A guia de desempenho é mostrada com um retângulo vermelho em torno do botão URL de previsão.](./media/use-prediction-api/published-iteration-prediction-url.png)

![A guia de desempenho é mostrada com um retângulo vermelho em torno do valor da URL de previsão para usar um arquivo de imagem e o valor de chave de previsão.](./media/use-prediction-api/prediction-api-info.png)

> [!TIP]
> Sua __chave de previsão__ também podem ser encontradas na [Portal do Azure](https://portal.azure.com) página para o recurso do Azure de visão personalizada associada ao seu projeto, em __chaves__. 

Na caixa de diálogo, copie as seguintes informações para uso no aplicativo:

* __URL de previsão__ para usar um __arquivo de imagem__.
* __Chave de previsão__ valor.

## <a name="create-the-application"></a>Criar o aplicativo

1. No Visual Studio, crie um novo aplicativo de console do C#.

1. Use o código a seguir como o corpo do arquivo __Program.cs__.

    > [!IMPORTANT]
    > Altere as seguintes informações:
    >
    > * Defina o __namespace__ como o nome do seu projeto.
    > * Defina as __chave de previsão__ valor recuperado anteriormente na linha que começa com `client.DefaultRequestHeaders.Add("Prediction-Key",`.
    > * Defina as __URL de previsão__ valor recuperado anteriormente na linha que começa com `string url =`.

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CVSPredictionSample
    {
        public static class Program
        {
            public static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            public static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid Prediction-Key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "3b9dde6d1ae1453a86bfeb1d945300f2");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "https://southcentralus.api.cognitive.microsoft.com/customvision/v3.0/Prediction/8622c779-471c-4b6e-842c-67a11deffd7b/classify/iterations/Cats%20vs.%20Dogs%20-%20Published%20Iteration%203/image";

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

            private static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
    ```

## <a name="use-the-application"></a>Use o aplicativo

Ao executar o aplicativo, você inserirá o caminho para um arquivo de imagem no console. A imagem é enviada para a API de previsão e os resultados da previsão são retornados como um documento JSON. O JSON a seguir está um exemplo da resposta.

```json
{
    "Id":"7796df8e-acbc-45fc-90b4-1b0c81b73639",
    "Project":"8622c779-471c-4b6e-842c-67a11deffd7b",
    "Iteration":"59ec199d-f3fb-443a-b708-4bca79e1b7f7",
    "Created":"2019-03-20T16:47:31.322Z",
    "Predictions":[
        {"TagId":"d9cb3fa5-1ff3-4e98-8d47-2ef42d7fb373","TagName":"cat", "Probability":1.0},
        {"TagId":"9a8d63fb-b6ed-4462-bcff-77ff72084d99","TagName":"dog", "Probability":0.1087869}
    ]
}
```

## <a name="next-steps"></a>Próximas etapas

[Exportar o modelo para uso móvel](export-your-model.md)

[Introdução ao .NET SDKs](csharp-tutorial.md)

[Introdução ao Python SDKs](python-tutorial.md)

[Comece com SDKs de Java](java-tutorial.md)

[Introdução ao nó SDKs](node-tutorial.md)

[Introdução ao ir SDKs](go-tutorial.md)
