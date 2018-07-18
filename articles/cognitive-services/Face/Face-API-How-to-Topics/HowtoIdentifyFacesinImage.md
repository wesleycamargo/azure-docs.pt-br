---
title: Identificar faces em imagens com API de Detecção Facial | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Use a API de Detecção Facial nos Serviços Cognitivos para identificar faces em imagens.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 3f75db176055d9f784ec978497d7cae077ff629f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364687"
---
# <a name="how-to-identify-faces-in-images"></a>Como identificar faces em imagens

Este guia demonstra como identificar faces desconhecidas usando PersonGroups, que são criados usando pessoas conhecidas com antecedência. As amostras são gravadas em C# usando a biblioteca de clientes da API de Detecção Facial.

## <a name="concepts"></a> Conceitos

Se você não estiver familiarizado com os seguintes conceitos apresentados neste guia, pesquise as definições no nosso [glossário](../Glossary.md) a qualquer momento:

- Face – Detectar
- Face – Identificar
- PersonGroup

## <a name="preparation"></a> Preparação

Nesta amostra, demonstramos o seguinte:

- Como criar um PersonGroup – esse PersonGroup contém uma lista de pessoas conhecidas.
- Como atribuir faces a cada pessoa – essas faces são usadas como uma linha de base para identificar pessoas. É recomendável usar faces frontais claras, como sua ID de foto. Um bom conjunto de fotos deve conter faces da mesma pessoa em poses, cores de roupa ou estilos de cabelo diferentes.

Para executar a demonstração desta amostra, você precisa preparar um grupo de imagens:

- Algumas fotos com a face da pessoa. [Clique aqui para baixar fotos de amostra](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) para Ana, Guilherme e Clara.
- Uma série de fotos de teste, que podem ou não conter as faces de Ana, Guilherme ou Clara, é usada para a identificação de teste. Você também pode selecionar algumas imagens de amostra do link anterior.

## <a name="step1"></a> Etapa 1: autorizar a chamada à API

Cada chamada à API de Detecção Facial exige uma chave de assinatura. Essa chave pode ser passada por um parâmetro de cadeia de caracteres de consulta ou especificada no cabeçalho da solicitação. Para passar a chave de assinatura por meio da cadeia de caracteres de consulta, veja a URL de solicitação para [Face – Detecção](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) como um exemplo:
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

Como alternativa, a chave de assinatura também pode ser especificada no cabeçalho da solicitação HTTP: **ocp-apim-subscription-key: &lt;Chave de Assinatura&gt;** Ao usar uma biblioteca de clientes, a chave de assinatura é passada por meio do construtor da classe FaceServiceClient. Por exemplo: 
 
```CSharp 
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```
 
A chave de assinatura pode ser obtida da página Marketplace do seu portal do Azure. Veja [Assinaturas](https://azure.microsoft.com/try/cognitive-services/).

## <a name="step2"></a> Etapa 2: Criar o PersonGroup

Nesta etapa, criamos um PersonGroup denominado "MyFriends" que contém três pessoas: Ana, Guilherme e Clara. Cada pessoa tem várias faces registradas. As faces precisam ser detectadas entre as imagens. Depois de todas essas etapas, você tem um PersonGroup como o da imagem a seguir:

![HowToIdentify1](../Images/group.image.1.jpg)

### <a name="step2-1"></a> 2.1 Definir pessoas para o PersonGroup
Uma pessoa é uma unidade básica de identificação. Uma pessoa pode ter uma ou mais faces conhecidas registradas. No entanto, um PersonGroup é uma coleção de pessoas, e cada pessoa é definida dentro de um determinado PersonGroup. A identificação é feita com relação a um PersonGroup. Portanto, a tarefa é criar um PersonGroup e, em seguida, criar as pessoas nele, como Ana, Guilherme e Clara.

Primeiro, você precisa criar um novo PersonGroup. Isso é executado usando a API [PersonGroup – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244). A API da biblioteca de clientes correspondente é o método CreatePersonGroupAsync para a classe FaceServiceClient. A ID do grupo especificada para criar o grupo é exclusiva para cada assinatura – você também pode obter, atualizar ou excluir PersonGroups usando outras APIs de PersonGroup. Quando um grupo é definido, as pessoas podem ser definidas dentro dele usando a API [Pessoa do PersonGroup – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c). O método da biblioteca de clientes é CreatePersonAsync. Você poderá adicionar uma face a cada pessoa depois que elas tiverem sido criadas.

```CSharp 
// Create an empty PersonGroup
string personGroupId = "myfriends";
await faceServiceClient.CreatePersonGroupAsync(personGroupId, "My Friends");
 
// Define Anna
CreatePersonResult friend1 = await faceServiceClient.CreatePersonAsync(
    // Id of the PersonGroup that the person belonged to
    personGroupId,    
    // Name of the person
    "Anna"            
);
 
// Define Bill and Clare in the same way
```
### <a name="step2-2"></a> 2.2 Detectar faces e registrá-las para a pessoa correta
A detecção é feita enviando uma solicitação da Web "POST" para A API [Face – Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) com o arquivo de imagem no corpo da solicitação HTTP. Ao usar a biblioteca de clientes, a detecção facial é feita por meio do método DetectAsync para a classe FaceServiceClient.

Para cada face detectada, você pode chamar [Pessoa do PersonGroup – Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) para adicioná-la para a pessoa correta.

O código a seguir demonstra o processo de como detectar uma face de uma imagem e adicioná-la a uma pessoa:
```CSharp 
// Directory contains image files of Anna
const string friend1ImageDir = @"D:\Pictures\MyFriends\Anna\";
 
foreach (string imagePath in Directory.GetFiles(friend1ImageDir, "*.jpg"))
{
    using (Stream s = File.OpenRead(imagePath))
    {
        // Detect faces in the image and add to Anna
        await faceServiceClient.AddPersonFaceAsync(
            personGroupId, friend1.PersonId, s);
    }
}
// Do the same for Bill and Clare
``` 
Observe que, se a imagem tiver mais de uma face, apenas a face maior será adicionada. Você pode adicionar outras faces para a pessoa passando uma cadeia de caracteres no formato "targetFace = left, top, width, height" para o parâmetro de consulta targetFace da API [Pessoa do PersonGroup – Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) ou usando o parâmetro opcional targetFace para o método AddPersonFaceAsync para adicionar outras faces. Cada face adicionada para a pessoa receberá uma ID de face persistente exclusiva, que poderá ser usada em [Pessoa do PersonGroup – Excluir Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) e [Face – Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).
## <a name="step3"></a> Etapa 3: Treinar o PersonGroup

O PersonGroup deve ser treinado antes que se possa realizar a identificação utilizando-o. Além disso, ele precisará ser treinado novamente após a adição ou remoção de qualquer pessoa ou se a face registrada de qualquer pessoa for editada. O treinamento é feito com A API [PersonGroup – Treinar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249). Ao usar a biblioteca de clientes, isso é simplesmente uma chamada ao método TrainPersonGroupAsync:
 
```CSharp 
await faceServiceClient.TrainPersonGroupAsync(personGroupId);
```
 
O treinamento é um processo assíncrono. Poderá não estar concluído mesmo depois que o método TrainPersonGroupAsync tiver retornado. Talvez seja necessário consultar o status do treinamento pela API [PersonGroup – Obter Status do Treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) ou pelo método GetPersonGroupTrainingStatusAsync da biblioteca de clientes. O código a seguir demonstra uma lógica simples de esperar o fim do treinamento do PersonGroup:
 
```CSharp 
TrainingStatus trainingStatus = null;
while(true)
{
    trainingStatus = await faceServiceClient.GetPersonGroupTrainingStatusAsync(personGroupId);
 
    if (trainingStatus.Status != Status.Running)
    {
        break;
    }
 
    await Task.Delay(1000);
} 
``` 

## <a name="step4"></a> Etapa 4: Identificar uma face com relação a PersonGroup definido
Ao executar identificações, a API de Detecção Facial pode calcular a semelhança de uma face de teste entre todas as faces em um grupo, retornando a pessoa mais comparável à face de teste. Isso é feito por meio da API [Face – Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) ou pelo método IdentifyAsync da biblioteca de clientes.

A face de teste precisa ser detectada usando as etapas anteriores e, em seguida, a ID da face será passada para a API de identificação como um segundo argumento. Várias IDs de face podem ser identificadas ao mesmo tempo, e o resultado conterá todos os resultados de identificação. Por padrão, a identificação retornará somente uma pessoa que melhor corresponde à face de teste. Se preferir, você poderá especificar o parâmetro opcional maxNumOfCandidatesReturned para permitir que a identidade retorne mais candidatos.

O código a seguir demonstra o processo de identificação:
```CSharp 
string testImageFile = @"D:\Pictures\test_img1.jpg";

using (Stream s = File.OpenRead(testImageFile))
{
    var faces = await faceServiceClient.DetectAsync(s);
    var faceIds = faces.Select(face => face.FaceId).ToArray();
 
    var results = await faceServiceClient.IdentifyAsync(personGroupId, faceIds);
    foreach (var identifyResult in results)
    {
        Console.WriteLine("Result of face: {0}", identifyResult.FaceId);
        if (identifyResult.Candidates.Length == 0)
        {
            Console.WriteLine("No one identified");
        }
        else
        {
            // Get top 1 among all candidates returned
            var candidateId = identifyResult.Candidates[0].PersonId;
            var person = await faceServiceClient.GetPersonAsync(personGroupId, candidateId);
            Console.WriteLine("Identified as {0}", person.Name);
        }
    }
}
``` 

Quando você tiver concluído as etapas, poderá tentar identificar faces diferentes e ver se as faces de Ana, Guilherme ou Clara podem ser identificadas corretamente conforme as imagens carregadas para detecção facial. Veja os exemplos a seguir:

![HowToIdentify2](../Images/identificationResult.1.jpg )

## <a name="step5"></a> Etapa 5: Solicitação de larga escala

Como se sabe, um PersonGroup pode conter até 10 mil pessoas devido à limitação de design anterior.
Para obter mais informações sobre cenários em escala de até milhões, veja [Como usar o recurso de larga escala](how-to-use-large-scale.md).

## <a name="summary"></a> Resumo

Neste guia, você aprendeu o processo de criar um PersonGroup e identificar uma pessoa. A seguir está um lembrete rápido dos recursos explicados e demonstrados anteriormente:

- Detecção de faces usando A API [Face – Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d)
- Criação de PersonGroups usando a API [PersonGroup – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)
- Criação de pessoas usando a API [PersonGroup Person – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)
- Treinar um PersonGroup usando a API [PersonGroup – Treinar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)
- Identificação de faces desconhecidas com relação ao PersonGroup usando a API [Face – Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)

## <a name="related"></a> Tópicos relacionados

- [Como detectar faces em imagem](HowtoDetectFacesinImage.md)
- [Como adicionar faces](how-to-add-faces.md)
- [Como usar o recurso de larga escala](how-to-use-large-scale.md)
