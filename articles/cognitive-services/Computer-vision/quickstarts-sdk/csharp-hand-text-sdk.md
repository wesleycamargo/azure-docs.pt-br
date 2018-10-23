---
title: 'Início Rápido: extrair texto – SDK, C# – Pesquisa Visual Computacional'
titleSuffix: Azure Cognitive Services
description: Neste Início Rápido, você extrai texto de uma imagem usando a biblioteca de clientes C# do Windows da Pesquisa Visual Computacional.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 09/27/2018
ms.author: pafarley
ms.openlocfilehash: d819aee7c42759e2427e27e0f640d79536069ccb
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49343598"
---
# <a name="quickstart-extract-text-using-the-computer-vision-sdk-and-c"></a>Início Rápido: Extrair texto usando o SDK da Pesquisa Visual Computacional e C#

Neste Início Rápido, você pode extrair texto manuscrito ou impresso de uma imagem usando a biblioteca de clientes do Windows da Pesquisa Visual Computacional.

## <a name="prerequisites"></a>Pré-requisitos

* Para usar a Pesquisa Visual Computacional, você precisa de uma chave de assinatura. Veja [Obter chaves de assinatura](../Vision-API-How-to-Topics/HowToSubscribe.md).
* Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/).
* O pacote NuGet da biblioteca de clientes [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision). Não é necessário baixar o pacote. As instruções de instalação são fornecidas abaixo.

## <a name="recognizetextasync-method"></a>Método RecognizeTextAsync

> [!TIP]
> Obtenha o código mais recente como uma solução do Visual Studio a partir do [GitHub](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/ComputerVision).

Os métodos `RecognizeTextAsync` e `RecognizeTextInStreamAsync` encapsulam a [API de Reconhecimento de Texto](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) para imagens locais e remotas, respectivamente. O método `GetTextOperationResultAsync` encapsula a [Obter API de Resultados da Operação de Reconhecimento de Texto](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201).  Você pode usar esses métodos para detectar texto em uma imagem e extrair os caracteres reconhecidos para um fluxo de caracteres utilizável por computador.

Para executar a amostra, siga estas etapas:

1. Crie um novo aplicativo de console do Visual C# no Visual Studio.
1. Instale o pacote NuGet da biblioteca de clientes de Pesquisa Visual Computacional.
    1. No menu, clique em **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** e, em seguida, **Gerenciar Pacotes NuGet para a Solução**.
    1. Clique na guia **Procurar** e, na caixa **Pesquisar**, digite "Microsoft.Azure.CognitiveServices.Vision.ComputerVision".
    1. Selecione **Microsoft.Azure.CognitiveServices.Vision.ComputerVision** quando ela for exibida, então clique na caixa de seleção ao lado do nome do seu projeto e escolha **Instalar**.
1. Substitua `Program.cs` pelo código a seguir.
1. Substitua `<Subscription Key>` pela sua chave de assinatura válida.
1. Altere `computerVision.Endpoint` para a região do Azure associada às suas chaves de assinatura, se necessário.
1. Se desejar, defina `textRecognitionMode` para `TextRecognitionMode.Printed`.
1. Substitua `<LocalImage>` pelo nome de arquivo e caminho de uma imagem local.
1. Opcionalmente, defina `remoteImageUrl` para uma imagem diferente.
1. Execute o programa.

```csharp
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

using System;
using System.IO;
using System.Threading.Tasks;

namespace ExtractText
{
    class Program
    {
        // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // For printed text, change to TextRecognitionMode.Printed
        private const TextRecognitionMode textRecognitionMode =
            TextRecognitionMode.Handwritten;

        // localImagePath = @"C:\Documents\LocalImage.jpg"
        private const string localImagePath = @"<LocalImage>";

        private const string remoteImageUrl =
            "https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/" +
            "Cursive_Writing_on_Notebook_paper.jpg/" +
            "800px-Cursive_Writing_on_Notebook_paper.jpg";

        private const int numberOfCharsInOperationId = 36;

        static void Main(string[] args)
        {
            ComputerVisionClient computerVision = new ComputerVisionClient(
                new ApiKeyServiceClientCredentials(subscriptionKey),
                new System.Net.Http.DelegatingHandler[] { });

            // You must use the same region as you used to get your subscription
            // keys. For example, if you got your subscription keys from westus,
            // replace "westcentralus" with "westus".
            //
            // Free trial subscription keys are generated in the westcentralus
            // region. If you use a free trial subscription key, you shouldn't
            // need to change the region.

            // Specify the Azure region
            computerVision.Endpoint = "https://westcentralus.api.cognitive.microsoft.com";

            Console.WriteLine("Images being analyzed ...");
            var t1 = ExtractRemoteTextAsync(computerVision, remoteImageUrl);
            var t2 = ExtractLocalTextAsync(computerVision, localImagePath);

            Task.WhenAll(t1, t2).Wait(5000);
            Console.WriteLine("Press ENTER to exit");
            Console.ReadLine();
        }

        // Recognize text from a remote image
        private static async Task ExtractRemoteTextAsync(
            ComputerVisionClient computerVision, string imageUrl)
        {
            if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
            {
                Console.WriteLine(
                    "\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                return;
            }

            // Start the async process to recognize the text
            RecognizeTextHeaders textHeaders =
                await computerVision.RecognizeTextAsync(
                    imageUrl, textRecognitionMode);

            await GetTextAsync(computerVision, textHeaders.OperationLocation);
        }

        // Recognize text from a local image
        private static async Task ExtractLocalTextAsync(
            ComputerVisionClient computerVision, string imagePath)
        {
            if (!File.Exists(imagePath))
            {
                Console.WriteLine(
                    "\nUnable to open or read localImagePath:\n{0} \n", imagePath);
                return;
            }

            using (Stream imageStream = File.OpenRead(imagePath))
            {
                // Start the async process to recognize the text
                RecognizeTextInStreamHeaders textHeaders =
                    await computerVision.RecognizeTextInStreamAsync(
                        imageStream, textRecognitionMode);

                await GetTextAsync(computerVision, textHeaders.OperationLocation);
            }
        }

        // Retrieve the recognized text
        private static async Task GetTextAsync(
            ComputerVisionClient computerVision, string operationLocation)
        {
            // Retrieve the URI where the recognized text will be
            // stored from the Operation-Location header
            string operationId = operationLocation.Substring(
                operationLocation.Length - numberOfCharsInOperationId);

            Console.WriteLine("\nCalling GetHandwritingRecognitionOperationResultAsync()");
            TextOperationResult result =
                await computerVision.GetTextOperationResultAsync(operationId);

            // Wait for the operation to complete
            int i = 0;
            int maxRetries = 10;
            while ((result.Status == TextOperationStatusCodes.Running ||
                    result.Status == TextOperationStatusCodes.NotStarted) && i++ < maxRetries)
            {
                Console.WriteLine(
                    "Server status: {0}, waiting {1} seconds...", result.Status, i);
                await Task.Delay(1000);

                result = await computerVision.GetTextOperationResultAsync(operationId);
            }

            // Display the results
            Console.WriteLine();
            var lines = result.RecognitionResult.Lines;
            foreach (Line line in lines)
            {
                Console.WriteLine(line.Text);
            }
            Console.WriteLine();
        }
    }
}
```

## <a name="recognizetextasync-response"></a>Resposta de RecognizeTextAsync

Uma resposta bem-sucedida exibe as linhas do texto reconhecido para cada imagem.

Veja [Início Rápido: Extrair texto manuscrito – REST, C#](../QuickStarts/CSharp-hand-text.md#examine-the-response) para obter um exemplo de saída JSON bruta.

```cmd
Calling GetHandwritingRecognitionOperationResultAsync()

Calling GetHandwritingRecognitionOperationResultAsync()
Server status: Running, waiting 1 seconds...
Server status: Running, waiting 1 seconds...

dog
The quick brown fox jumps over the lazy
Pack my box with five dozen liquor jugs
```

## <a name="next-steps"></a>Próximas etapas

Explore as APIs de Pesquisa Visual Computacional usadas para analisar uma imagem, detectar celebridades e marcos, criar uma miniatura e extrair textos manuscritos e impressos.

> [!div class="nextstepaction"]
> [Explorar APIs de Pesquisa Visual Computacional](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
