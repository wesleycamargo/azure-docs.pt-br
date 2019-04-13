---
title: Como migrar seus dados de detecção facial entre assinaturas — API de Detecção Facial
titleSuffix: Azure Cognitive Services
description: Este guia mostra como migrar seus dados de detecção facial armazenados de uma assinatura de API de Detecção Facial para outra.
services: cognitive-services
author: lewlu
manager: cgronlun
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: lewlu
ms.openlocfilehash: 30ceb0e396597530071c70c4448761d914acb4ac
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548397"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Migrar seus dados de detecção facial para uma assinatura diferente de Detecção Facial

Este guia mostra como mover dados de detecção facial (como um **PersonGroup** armazenado de faces) para uma assinatura diferente de API de Detecção Facial usando o recurso Instantâneo. Isso permite que você evite ter que criar e treinar repetidamente um **PersonGroup** ou **FaceList** ao mover ou expandir suas operações. Por exemplo, você pode ter criado um **PersonGroup** usando uma assinatura de avaliação gratuita e agora deseja migrá-lo para sua assinatura paga ou talvez precise sincronizar dados de detecção facial entre regiões para uma operação de grande porte.

Essa mesma estratégia de migração também se aplica aos objetos **LargePersonGroup** e **LargeFaceList**. Se você não estiver familiarizado com os conceitos deste guia, consulte suas definições no [glossário](../Glossary.md). Este guia usa a biblioteca cliente .NET da API de Detecção Facial com C#.

## <a name="prerequisites"></a>Pré-requisitos

- Duas chaves de assinatura de API de Detecção Facial (uma com os dados existentes e uma para a qual migrar). Siga as instruções em [Criar uma conta dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar o serviço API de Detecção Facial e obter sua chave.
- A cadeia de caracteres de ID de assinatura da API de Detecção Facial correspondente à assinatura de destino (localizada na folha **Visão geral** no portal do Azure). 
- Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/).


## <a name="create-the-visual-studio-project"></a>Criar o projeto do Visual Studio

Este guia usará um aplicativo de console simples para executar a migração de dados de detecção facial. Para uma implementação completa, confira [Exemplo de Instantâneo de API de Detecção Facial](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) no GitHub.

1. No Visual Studio, crie um novo projeto de **Aplicativo de Console (.NET Framework)** e dê o nome de **FaceApiSnapshotSample**. 
1. Obtenha os pacotes necessários do NuGet. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto e selecione **Gerenciar Pacotes NuGet**. Clique na guia **Procurar** e selecione **Incluir pré-lançamento**; depois, localize e instale o pacote a seguir:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)


## <a name="create-face-clients"></a>Criar clientes de detecção facial

No método **Main** em *Program.cs*, crie duas instâncias **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** para suas assinaturas de origem e de destino. Neste exemplo, usaremos uma assinatura de Detecção Facial na região do Pacífico Asiático como a origem e uma assinatura do Oeste dos EUA como o destino. Isso demonstrará como migrar dados de uma região do Azure para outra. Se suas assinaturas estiverem em regiões diferentes, você precisará alterar as cadeias de caracteres `Endpoint`.

```csharp
var FaceClientEastAsia = new FaceClient(new ApiKeyServiceClientCredentials("<East Asia Subscription Key>"))
    {
        Endpoint = "https://southeastasia.api.cognitive.microsoft.com/>"
    };

var FaceClientWestUS = new FaceClient(new ApiKeyServiceClientCredentials("<West US Subscription Key>"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com/"
    };
```

Você precisará preencher os valores da chave de assinatura e as URLs do ponto de extremidade para suas assinaturas de origem e de destino.


## <a name="prepare-a-persongroup-for-migration"></a>Preparar um PersonGroup para migração

Você precisa da ID do **PersonGroup** na sua assinatura de origem para migrá-la para a assinatura de destino. Use o método **[PersonGroupOperationsExtensions.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet)** para recuperar uma lista de seus objetos **PersonGroup**; em seguida, obtenha a propriedade **[PersonGroup.PersonGroupId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId)**. Esse processo será diferente dependendo de quais objetos **PersonGroup** você possui. Neste guia, a ID **PersonGroup** de origem é armazenada em `personGroupId`.

> [!NOTE]
> O [código de exemplo](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) cria e treina um novo **PersonGroup** para migrar, mas na maioria dos casos você já deve ter um **PersonGroup** para usar.

## <a name="take-snapshot-of-persongroup"></a>Fazer um instantâneo do PersonGroup

Um instantâneo é um armazenamento remoto temporário para determinados tipos de dados de Detecção Facial. Ele funciona como um tipo de área de transferência para copiar dados de uma assinatura para outra. Primeiro, o usuário "faz" um instantâneo dos dados da assinatura de origem e, em seguida, "aplica-o" em um novo objeto de dados na assinatura de destino.

Use a instância **FaceClient** da assinatura de origem para obter um instantâneo do **PersonGroup**, usando o **[TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet)** com a ID **PersonGroup** e a ID da assinatura de destino. Se você tiver várias assinaturas de destino, poderá adicioná-las como entradas matriz no terceiro parâmetro.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> O processo de obter e aplicar instantâneos não interromperá chamadas regulares para a origem ou o destino de **PersonGroup**s (ou **FaceList**s). No entanto, não recomendamos fazer chamadas simultâneas que alterar o objeto de origem ([chamadas de gerenciamento FaceList](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet) ou o [PersonGroup Train](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet) chamar, por exemplo), porque a operação de instantâneo pode executar antes ou depois que essas operações ou poderá encontrar erros.

## <a name="retrieve-the-snapshot-id"></a>Recuperar a ID do instantâneo

O instantâneo usando o método é assíncrono, portanto, você precisará aguardar sua conclusão (instantâneo operações não podem ser canceladas). Nesse código, o método `WaitForOperation` monitora a chamada assíncrona, verificando o status a cada 100 ms. Quando a operação estiver concluída, você poderá recuperar uma ID de operação. Você pode obtê-la analisando o campo `OperationLocation`. 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Um valor típico de `OperationLocation` será semelhante a este:

```csharp
"/operations/a63a3bdd-a1db-4d05-87b8-dbad6850062a"
```

O método auxiliar `WaitForOperation` está aqui:

```csharp
/// <summary>
/// Waits for the take/apply operation to complete and returns the final operation status.
/// </summary>
/// <returns>The final operation status.</returns>
private static async Task<OperationStatus> WaitForOperation(IFaceClient client, Guid operationId)
{
    OperationStatus operationStatus = null;
    do
    {
        if (operationStatus != null)
        {
            Thread.Sleep(TimeSpan.FromMilliseconds(100));
        }

        // Get the status of the operation.
        operationStatus = await client.Snapshot.GetOperationStatusAsync(operationId);

        Console.WriteLine($"Operation Status: {operationStatus.Status}");
    }
    while (operationStatus.Status != OperationStatusType.Succeeded
            && operationStatus.Status != OperationStatusType.Failed);

    return operationStatus;
}
```

Quando o status da operação é marcado como `Succeeded`, você pode obter a ID do instantâneo analisando o campo `ResourceLocation` da instância **OperationStatus** retornada.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Um valor típico de `resourceLocation` será semelhante a este:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-snapshot-to-target-subscription"></a>Aplicar instantâneo em uma assinatura de destino

Em seguida, crie o novo **PersonGroup** na assinatura de destino, usando uma ID gerada aleatoriamente. Em seguida, use a instância **FaceClient** da assinatura de destino para aplicar o instantâneo a esse PersonGroup, transmitindo a ID do instantâneo e a nova ID do **PersonGroup**. 

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Um objeto de instantâneo só é válido por 48 horas. Você só deve fazer um instantâneo se pretende usá-lo para a migração de dados logo em seguida.

Uma solicitação de aplicação de instantâneo retornará outra ID de operação. Você pode obter essa ID analisando o campo `OperationLocation` da instância retornada **applySnapshotResult**. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

O processo do aplicativo de captura de instantâneo também é assíncrono, portanto, use novamente `WaitForOperation` para aguardar a conclusão.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>Testar a migração de dados

Depois de aplicar o instantâneo, o novo **PersonGroup** no destino de assinatura deve ser preenchido com os dados de detecção facial originais. Por padrão, os resultados de treinamento também são copiados, portanto, o novo **PersonGroup** estará pronto para chamadas de identificação com detecção facial sem a necessidade de novos treinamentos.

Para testar a migração de dados, você pode executar as seguintes operações e comparar os resultados que eles imprimem ao console.

```csharp
await DisplayPersonGroup(FaceClientEastAsia, personGroupId);
await IdentifyInPersonGroup(FaceClientEastAsia, personGroupId);

await DisplayPersonGroup(FaceClientWestUS, newPersonGroupId);
// No need to retrain the person group before identification,
// training results are copied by snapshot as well.
await IdentifyInPersonGroup(FaceClientWestUS, newPersonGroupId);
```

Use os seguintes métodos auxiliares:

```csharp
private static async Task DisplayPersonGroup(IFaceClient client, string personGroupId)
{
    var personGroup = await client.PersonGroup.GetAsync(personGroupId);
    Console.WriteLine("Person Group:");
    Console.WriteLine(JsonConvert.SerializeObject(personGroup));

    // List persons.
    var persons = await client.PersonGroupPerson.ListAsync(personGroupId);

    foreach (var person in persons)
    {
        Console.WriteLine(JsonConvert.SerializeObject(person));
    }

    Console.WriteLine();
}
```

```csharp
private static async Task IdentifyInPersonGroup(IFaceClient client, string personGroupId)
{
    using (var fileStream = new FileStream("data\\PersonGroup\\Daughter\\Daughter1.jpg", FileMode.Open, FileAccess.Read))
    {
        var detectedFaces = await client.Face.DetectWithStreamAsync(fileStream);

        var result = await client.Face.IdentifyAsync(detectedFaces.Select(face => face.FaceId.Value).ToList(), personGroupId);
        Console.WriteLine("Test identify against PersonGroup");
        Console.WriteLine(JsonConvert.SerializeObject(result));
        Console.WriteLine();
    }
}
```

Agora você pode começar a usar o novo **PersonGroup** na assinatura de destino. 

Se você desejar atualizar o **PersonGroup** de destino novamente no futuro, será preciso criar um novo **PersonGroup** (seguindo as etapas deste guia) para receber o instantâneo. Um único objeto **PersonGroup** só pode ter um instantâneo aplicado a ele uma vez.

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar de migrar os dados de detecção facial, recomendamos que você exclua manualmente o objeto de instantâneo.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="related-topics"></a>Tópicos Relacionados

- [Documentação de referência do instantâneo (SDK do .NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [Exemplo de Instantâneo de API de Detecção Facial](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Como adicionar faces](how-to-add-faces.md)
- [Como detectar faces em imagem](HowtoDetectFacesinImage.md)
- [Como identificar faces em imagem](HowtoIdentifyFacesinImage.md)
