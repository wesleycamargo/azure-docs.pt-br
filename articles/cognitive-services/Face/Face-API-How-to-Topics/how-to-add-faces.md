---
title: 'Exemplo: Adicionar rostos – API de Detecção Facial'
titleSuffix: Azure Cognitive Services
description: Use a API de Detecção Facial para adicionar faces a imagens.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 722a09b782c902642b599460835151928c16c5f4
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55859021"
---
# <a name="example-how-to-add-faces"></a>Exemplo: Como adicionar faces

Este guia demonstra a prática recomendada para adicionar um grande número de pessoas e faces para um PersonGroup.
A mesma estratégia também se aplica a FaceList e LargePersonGroup.
As amostras são gravadas em C# usando a biblioteca de clientes da API de Detecção Facial.

## <a name="step-1-initialization"></a>Etapa 1: Inicialização

Diversas variáveis são declaradas e uma função auxiliar é implementada para agendar as solicitações.

- `PersonCount` é o número total de pessoas.
- `CallLimitPerSecond` é o máximo de chamadas por segundo de acordo com o nível de assinatura.
- `_timeStampQueue` é uma fila para registrar os carimbos de hora de solicitação.
- `await WaitCallLimitPerSecondAsync()` aguardará até que seja válida para enviar a próxima solicitação.

```CSharp
const int PersonCount = 10000;
const int CallLimitPerSecond = 10;
static Queue<DateTime> _timeStampQueue = new Queue<DateTime>(CallLimitPerSecond);

static async Task WaitCallLimitPerSecondAsync()
{
    Monitor.Enter(_timeStampQueue);
    try
    {
        if (_timeStampQueue.Count >= CallLimitPerSecond)
        {
            TimeSpan timeInterval = DateTime.UtcNow - _timeStampQueue.Peek();
            if (timeInterval < TimeSpan.FromSeconds(1))
            {
                await Task.Delay(TimeSpan.FromSeconds(1) - timeInterval);
            }
            _timeStampQueue.Dequeue();
        }
        _timeStampQueue.Enqueue(DateTime.UtcNow);
    }
    finally
    {
        Monitor.Exit(_timeStampQueue);
    }
}
```

## <a name="step-2-authorize-the-api-call"></a>Etapa 2: Autorizar a chamada à API

Ao usar uma biblioteca de clientes, a chave de assinatura é passada através do construtor da classe FaceServiceClient. Por exemplo: 

```CSharp
FaceServiceClient faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

A chave de assinatura pode ser obtida da página Marketplace do seu portal do Azure. Consulte [Assinaturas](https://www.microsoft.com/cognitive-services/en-us/sign-up).

## <a name="step-3-create-the-persongroup"></a>Etapa 3: Criar o PersonGroup

Um PersonGroup denominado "MyPersonGroup" é criado para salvar as pessoas.
O tempo da solicitação é enfileirado a `_timeStampQueue` para garantir a validação geral.

```CSharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceServiceClient.CreatePersonGroupAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-to-the-persongroup"></a>Etapa 4: Criar as pessoas para o PersonGroup

As pessoas são criadas simultaneamente e `await WaitCallLimitPerSecondAsync()` também é aplicado para evitar exceder o limite de chamada.

```CSharp
CreatePersonResult[] persons = new CreatePersonResult[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceServiceClient.CreatePersonAsync(personGroupId, personName);
});
```

## <a name="step-5-add-faces-to-the-persons"></a>Etapa 5: Adicionar faces às pessoas

Adicionar as faces de pessoas diferentes são processadas simultaneamente, enquanto para uma pessoa específica é sequencial.
Novamente, `await WaitCallLimitPerSecondAsync()` é invocado para garantir que a frequência de solicitação está dentro do escopo de limitação.

```CSharp
Parallel.For(0, PersonCount, async i =>
{
    Guid personId = persons[i].PersonId;
    string personImageDir = @"/path/to/person/i/images";

    foreach (string imagePath in Directory.GetFiles(personImageDir, "*.jpg"))
    {
        await WaitCallLimitPerSecondAsync();

        using (Stream stream = File.OpenRead(imagePath))
        {
            await faceServiceClient.AddPersonFaceAsync(personGroupId, personId, stream);
        }
    }
});
```

## <a name="summary"></a>Resumo

Neste guia, você aprendeu o processo de criação de um PersonGroup com grande número de pessoas e faces. Vários lembretes:

- Esta estratégia também se aplica a FaceList e LargePersonGroup.
- Adicionar/excluir as faces a FaceLists diferentes ou pessoas em LargePersonGroup diferentes pode ser processado simultaneamente.
- As mesmas operações para um FaceList específico ou pessoa em LargePersonGroup devem ser feitas em sequência.
- Para manter a simplicidade, a manipulação de exceção potencial é omitida neste guia. Se você quiser aumentar mais a robustez, a política de repetição adequada deve ser aplicada.

A seguir é um lembrete rápido dos recursos explicados e demonstrados anteriormente:

- Criar PersonGroups usando a API [PersonGroup - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)
- Criar pessoas usando a API [PersonGroup Person - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)
- Adicionar faces a pessoas usando a API [PersonGroup Person - Adicionar face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)

## <a name="related-topics"></a>Tópicos Relacionados

- [Como identificar faces em imagem](HowtoIdentifyFacesinImage.md)
- [Como detectar faces em imagem](HowtoDetectFacesinImage.md)
- [Como usar o recurso de larga escala](how-to-use-large-scale.md)
