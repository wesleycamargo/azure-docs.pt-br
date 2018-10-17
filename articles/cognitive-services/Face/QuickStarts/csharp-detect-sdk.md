---
title: 'Início Rápido: detectar faces em uma imagem – SDK, C#'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você detecta faces de uma imagem usando a biblioteca de clientes em C# de Detecção Facial do Windows nos Serviços Cognitivos.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 09/14/2018
ms.author: nolachar
ms.openlocfilehash: a9a7efd89f8e7462812064615d07acf12acbc3a3
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364064"
---
# <a name="quickstart-detect-faces-in-an-image-using-c35---face"></a>Início Rápido: detectar faces em uma imagem usando C&#35; – Detecção Facial

Neste início rápido, você detecta faces humanas em uma imagem usando a biblioteca de clientes de Detecção Facial do Windows.

O código-fonte deste exemplo está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/Face).

## <a name="prerequisites"></a>Pré-requisitos

* Você precisa de uma chave de assinatura para executar o exemplo. É possível obter chaves de assinatura de avaliação gratuita em [Experimente os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=face-api).
* Qualquer edição do [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* O pacote NuGet da biblioteca de clientes [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview). Não é necessário baixar o pacote. As instruções de instalação são fornecidas abaixo.

## <a name="detectwithurlasync-method"></a>Método DetectWithUrlAsync

Os métodos `DetectWithUrlAsync` e `DetectWithStreamAsync` encapsulam a [API de Detecção Facial](https://westcentralus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) para imagens locais e remotas, respectivamente. Use esses métodos para detectar faces em uma imagem e retornar atributos de face, incluindo:

* ID de Detecção Facial: ID exclusiva usada em vários cenários de API de Detecção Facial.
* Retângulo de Detecção Facial: o lado esquerdo, a parte superior, a largura e a altura que indicam o local do rosto na imagem.
* Pontos de Referência: uma matriz de 27 pontos de referência de detecção facial que aponta para as posições importantes dos componentes de detecção facial.
* Atributos faciais, incluindo idade, gênero, intensidade do sorriso, pose da cabeça e pelos faciais.

Para executar a amostra, siga estas etapas:

1. Crie um novo aplicativo de console do Visual C# no Visual Studio.
1. Instale o pacote NuGet da biblioteca de clientes de Detecção Facial.
    1. No menu superior, clique em **Ferramentas**, selecione **Gerenciador de pacotes NuGet** e, em seguida, **Gerenciar pacotes NuGet para a Solução**.
    1. Clique na guia **Navegar** e, em seguida, selecione **Incluir pré-lançamento**.
    1. Na caixa **Pesquisa**, digite "Microsoft.Azure.CognitiveServices.Vision.Face".
    1. Selecione **Microsoft.Azure.CognitiveServices.Vision.Face** quando ela for exibida, então clique na caixa de seleção ao lado do nome do seu projeto e escolha **Instalar**.
1. Substitua *Program.cs* pelo código a seguir.
1. Substitua `<Subscription Key>` pela sua chave de assinatura válida.
1. Altere `faceEndpoint` para a região do Azure associada às suas chaves de assinatura, se necessário.
1. Opcionalmente, substitua <`LocalImage>` pelo nome de arquivo e caminho de uma imagem local (será ignorado se não for definido).
1. Opcionalmente, defina `remoteImageUrl` para uma imagem diferente.
1. Execute o programa.

```csharp
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;

using System;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;

namespace DetectFace
{
    class Program
    {
        // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // You must use the same region as you used to get your subscription
        // keys. For example, if you got your subscription keys from westus,
        // replace "westcentralus" with "westus".
        //
        // Free trial subscription keys are generated in the westcentralus
        // region. If you use a free trial subscription key, you shouldn't
        // need to change the region.
        // Specify the Azure region
        private const string faceEndpoint =
            "https://westcentralus.api.cognitive.microsoft.com";

        // localImagePath = @"C:\Documents\LocalImage.jpg"
        private const string localImagePath = @"<LocalImage>";

        private const string remoteImageUrl =
            "https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg";

        private static readonly FaceAttributeType[] faceAttributes =
            { FaceAttributeType.Age, FaceAttributeType.Gender };

        static void Main(string[] args)
        {
            FaceClient faceClient = new FaceClient(
                new ApiKeyServiceClientCredentials(subscriptionKey),
                new System.Net.Http.DelegatingHandler[] { });
            faceClient.Endpoint = faceEndpoint;

            Console.WriteLine("Faces being detected ...");
            var t1 = DetectRemoteAsync(faceClient, remoteImageUrl);
            var t2 = DetectLocalAsync(faceClient, localImagePath);

            Task.WhenAll(t1, t2).Wait(5000);
            Console.WriteLine("Press any key to exit");
            Console.ReadLine();
        }

        // Detect faces in a remote image
        private static async Task DetectRemoteAsync(
            FaceClient faceClient, string imageUrl)
        {
            if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
            {
                Console.WriteLine("\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                return;
            }

            try
            {
                IList<DetectedFace> faceList =
                    await faceClient.Face.DetectWithUrlAsync(
                        imageUrl, true, false, faceAttributes);

                DisplayAttributes(GetFaceAttributes(faceList, imageUrl), imageUrl);
            }
            catch (APIErrorException e)
            {
                Console.WriteLine(imageUrl + ": " + e.Message);
            }
        }

        // Detect faces in a local image
        private static async Task DetectLocalAsync(FaceClient faceClient, string imagePath)
        {
            if (!File.Exists(imagePath))
            {
                Console.WriteLine(
                    "\nUnable to open or read localImagePath:\n{0} \n", imagePath);
                return;
            }

            try
            {
                using (Stream imageStream = File.OpenRead(imagePath))
                {
                    IList<DetectedFace> faceList =
                            await faceClient.Face.DetectWithStreamAsync(
                                imageStream, true, false, faceAttributes);
                    DisplayAttributes(
                        GetFaceAttributes(faceList, imagePath), imagePath);
                }
            }
            catch (APIErrorException e)
            {
                Console.WriteLine(imagePath + ": " + e.Message);
            }
        }

        private static string GetFaceAttributes(
            IList<DetectedFace> faceList, string imagePath)
        {
            string attributes = string.Empty;

            foreach (DetectedFace face in faceList)
            {
                double? age = face.FaceAttributes.Age;
                string gender = face.FaceAttributes.Gender.ToString();
                attributes += gender + " " + age + "   ";
            }

            return attributes;
        }

        // Display the face attributes
        private static void DisplayAttributes(string attributes, string imageUri)
        {
            Console.WriteLine(imageUri);
            Console.WriteLine(attributes + "\n");
        }
    }
}
```

### <a name="detectwithurlasync-response"></a>Resposta DetectWithUrlAsync

Uma resposta bem-sucedida exibe o gênero e a idade para cada face na imagem.

Veja [Inícios Rápidos da API: detectar faces em uma imagem usando C#](CSharp.md) para obter um exemplo de saída JSON bruta.

```
https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg
Male 37   Female 56
```

## <a name="next-steps"></a>Próximas etapas

Saiba como criar um aplicativo do Windows do WPF que usa o serviço de Detecção Facial para detectar faces em uma imagem. O aplicativo desenha um quadro em volta de cada face e exibe uma descrição da face na barra de status.

> [!div class="nextstepaction"]
> [Tutorial: criar um aplicativo WPF para detectar e enquadrar faces em uma imagem](../Tutorials/FaceAPIinCSharpTutorial.md)
