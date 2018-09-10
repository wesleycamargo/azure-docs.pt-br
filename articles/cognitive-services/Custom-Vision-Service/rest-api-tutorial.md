---
title: Usar a API REST do Serviço de Visão Personalizada - Serviços Cognitivos do Azure | Microsoft Docs
description: Use a API REST para criar, treinar, testar e exportar um modelo de visão personalizada.
services: cognitive-services
author: blackmist
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 08/07/2018
ms.author: larryfr
ms.openlocfilehash: 44fa4d45c33f3064c089724ee761a70d0a8421ab
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2018
ms.locfileid: "40250235"
---
# <a name="tutorial-use-the-custom-vision-rest-api"></a>Tutorial: Usar API REST de Visão Personalizada

Aprenda a usar a API REST de Visão Personalizada para criar, treinar, testar e exportar um modelo.

As informações neste documento demonstram como usar um cliente REST para trabalhar com a API REST para treinamento do serviço de Visão Personalizada. Os exemplos mostram como usar a API usando o utilitário `curl` de um ambiente de bash`Invoke-WebRequest` do Windows PowerShell.

> [!div class="checklist"]
> * Obter chaves
> * Criar um projeto
> * Criar marcas
> * Adicionar imagens
> * Treinar e testar o modelo
> * Exportar o modelo

## <a name="prerequisites"></a>Pré-requisitos

* Uma familiaridade básica com REST (Transferência de Estado Representacional). Este documento não apresenta detalhes sobre verbos HTTP, JSON ou outros objetos comumente usados com REST.

* Um bash (Bourne Again Shell) com o utilitário [curl](https://curl.haxx.se) ou Windows PowerShell 3.0 (ou superior).

* Uma conta da Visão Personalizada. Para obter mais informações, consulte o documento [Compilar um classificador](getting-started-build-a-classifier.md).

## <a name="get-keys"></a>Obter chaves

Ao usar a API REST, é necessário autenticar usando uma chave. Ao realizar operações de gerenciamento ou treinamento, você usa a __chave de treinamento__. Ao usar o modelo para fazer previsões, você usa a __chave de previsão__.

Ao fazer uma solicitação, a chave é enviada como um cabeçalho de solicitação.

Para obter as chaves da conta, visite a [Página da Web da Visão Personalizada](https://customvision.ai) e selecione o __ícone de engrenagem__ no canto superior direito. Na seção __Contas__, copie os valores dos campos __Chave de Treinamento__ e __Chave de Previsão__.

![Imagem da interface do usuário de chaves](./media/rest-api-tutorial/training-prediction-keys.png)

> [!IMPORTANT]
> Como as chaves são usadas para autenticar todas as solicitações, os exemplos neste documento assumem que os valores de chave estão contidos nas variáveis de ambiente. Use os seguintes comandos para armazenar as chaves para variáveis de ambiente antes de usar qualquer outro trecho neste documento:
>
> ```bash
> read -p "Enter the training key: " TRAININGKEY
> read -p "Enter the prediction key: " PREDICTIONKEY
> ```
>
> ```powershell
> $trainingKey = Read-Host 'Enter the training key'
> $predictionKey = Read-Host 'Enter the prediction key'
> ```

## <a name="create-a-new-project"></a>Criar um novo projeto

Os exemplos a seguir criam um novo projeto chamado `myproject` na instância de serviço de Visão Personalizada. Este serviço é padronizado para o domínio `General`:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

A resposta à solicitação é semelhante ao seguinte documento JSON:

```json
{
  "id":"45d1b19b-69b6-4a22-8e7e-d1ca37504686",
  "name":"myproject",
  "description":"",
  "settings":{
    "domainId":"ee85a72c-405e-4adc-bb47-ffa8ca0c9f31",
    "useNegativeSet":true,
    "classificationType":"Multilabel",
    "detectionParameters":null
  },
  "created":"2018-08-10T17:39:02.5633333",
  "lastModified":"2018-08-10T17:39:02.5633333",
  "thumbnailUri":null
}
```

> [!TIP]
> A entrada `id` na resposta é a ID do novo projeto. Isso será usado em outros exemplos posteriormente neste documento.

Para obter mais informações sobre essa solicitação, consulte [CreateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8290).

### <a name="specific-domains"></a>Domínios específicos

Para criar um projeto para um domínio específico, você pode fornecer a __ID do domínio__ como um parâmetro opcional. Os exemplos a seguir mostram como recuperar uma lista de domínios disponíveis:

```bash
curl -X GET "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/domains" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'GET' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/domains" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

A resposta à solicitação é semelhante ao seguinte documento JSON:

```json
[
    {
        "id": "ee85a74c-405e-4adc-bb47-ffa8ca0c9f31",
        "name": "General",
        "type": "Classification",
        "exportable": false,
        "enabled": true
    },
    {
        "id": "c151d5b5-dd07-472a-acc8-15d29dea8518",
        "name": "Food",
        "type": "Classification",
        "exportable": false,
        "enabled": true
    },
    {
        "id": "ca455789-012d-4b50-9fec-5bb63841c793",
        "name": "Landmarks",
        "type": "Classification",
        "exportable": false,
        "enabled": true
    },
    ...
]
```

Para mais informações sobre essa solicitação, consulte [GetDomains](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a827d).

O exemplo a seguir demonstra a criação de um novo projeto que usa o domínio de __Ponto de Referência__:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject&domainId=ca455789-012d-4b50-9fec-5bb63841c793" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject&domainId=ca455789-012d-4b50-9fec-5bb63841c793" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

## <a name="create-tags"></a>Criar marcas

Para marcar imagens, é necessário usar uma ID da etiqueta. O exemplo a seguir demonstra como criar uma nova marca nomeada `cat` e obter uma ID da etiqueta. Substitua `{projectId}` pela ID do projeto. Use o parâmetro `name=` para especificar o nome da marca:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/tags?name=cat" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/tags?name=cat" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

A resposta à solicitação é semelhante ao documento JSON: 

```json
{"id":"ed6f7ab6-5132-47ad-8649-3ec42ee62d43","name":"cat","description":null,"imageCount":0}
```

Salve o valor `id`, como é usado durante a marcação de imagens.

Para obter mais informações sobre essa solicitação, consulte [CreateTag](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829d).

## <a name="add-images"></a>Adicionar imagens

Os exemplos a seguir demonstram como adicionar um arquivo da URL. Substitua `{projectId}` pela ID do projeto. Substitua `{tagId}` pela ID da etiqueta da imagem:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/images/urls" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii '{"images": [{"url": "http://myimages/cat.jpg","tagIds": ["{tagId}"],"regions": [{"tagId": "{tagId}","left": 119.0,"top": 94.0,"width": 240.0,"height": 140.0}]}], "tagIds": ["{tagId}"]}'
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/images/urls" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" } `
    -Body '{"images": [{"url": "http://myimages/cat.jpg","tagIds": ["{tagId}"],"regions": [{"tagId": "{tagId}","left": 119.0,"top": 94.0,"width": 240.0,"height": 140.0}]}], "tagIds": ["{tagId}"]}'
$resp.Content
```

A resposta à solicitação é semelhante ao seguinte documento JSON:

```json
{
    "isBatchSuccessful": true,
    "images": [
        {
            "sourceUrl": "http://myimages/cat.jpg",
            "status": "OK",
            "image": {
                "id": "081adaee-a76b-4d94-a70e-e4fd0935a28f",
                "created": "2018-08-13T13:24:22.0815638",
                "width": 640,
                "height": 480,
                "imageUri": "https://linktoimage",
                "thumbnailUri": "https://linktothumbnail",
                "tags": [
                    {
                        "tagId": "ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
                        "tagName": null,
                        "created": "2018-08-13T13:24:22.104936"
                    }
                ],
                "regions": [
                    {
                        "regionId": "40f206a1-3f8a-4de7-a6c3-c7b4643117df",
                        "tagName": null,
                        "created": "2018-08-13T13:24:22.104936",
                        "tagId": "ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
                        "left": 119,
                        "top": 94,
                        "width": 240,
                        "height": 140
                    }
                ]
            }
        }
    ]
}
```

Para obter mais informações sobre essa solicitação, consulte [CreateImagesFromUrls](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8287).

## <a name="train-the-model"></a>Treinar o modelo

Os exemplos a seguir demonstram como treinar o modelo. Substitua `{projectId}` pela ID do projeto:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/train" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/train" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" } 
$resp.Content
```

A resposta à solicitação é semelhante ao seguinte documento JSON:

```json
{
    "id": "23de09d6-42a1-413e-839e-8db6ee6d3496",
    "name": "Iteration 1",
    "isDefault": false,
    "status": "Training",
    "created": "2018-08-10T17:39:02.5766667",
    "lastModified": "2018-08-16T17:15:07.5250661",
    "projectId": "45d1b19b-69b8-4b22-8e7e-d1ca37504686",
    "exportable": false,
    "domainId": null
}
```

Salve o valor `id`, como é usado para testar e exportar o modelo.

Para mais informações, consulte [TrainProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8294).

## <a name="test-the-model"></a>Testar o modelo

Os exemplos a seguir demonstram como executar um teste do modelo. Substitua `{projectId}` pela ID do projeto. Substitua `{iterationId}` pela ID retornada do treinamento do modelo. Substitua `https://linktotestimage` pelo caminho para a imagem de teste.

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/quicktest/url?iterationId={iterationId}" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii '{"url":"https://linktotestimage"}'
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/quicktest/url?iterationId={iterationId}" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" } `
    -Body '{"url":"https://linktotestimage"}'
$resp.Content
```

A resposta à solicitação é semelhante ao seguinte documento JSON:

```json
{
    "id": "369b010b-2a92-4f48-a918-4c1a0af91888",
    "project": "45d1b19b-69b8-4b22-8e7e-d1ca37504686",
    "iteration": "23de09d6-42a1-413e-839e-8db6ee6d3496",
    "created": "2018-08-16T17:39:20.7944508Z",
    "predictions": [
        {
            "probability": 0.8390652,
            "tagId": "ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
            "tagName": "cat"
        }
    ]
}
```

Para obter mais informações, consulte [QuickTestImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a828d).

## <a name="export-the-model"></a>Exportar o modelo

Exportar um modelo é um processo de duas etapas. Primeiro você deve especificar o formato do modelo e, em seguida, solicitar a URL para o modelo exportado.

### <a name="request-a-model-export"></a>Solicitar uma exportação de modelo

Os exemplos a seguir demonstram como exportar um modelo `coreml`. Substitua `{projectId}` pela ID do projeto. Substitua `{iterationId}` pela ID retornada do treinamento do modelo.

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export?platform=coreml" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii ''
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export?platform=coreml" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" }
$resp.Content
```

A resposta à solicitação é semelhante ao seguinte documento JSON:

```json
{
    "platform": "CoreML",
    "status": "Exporting",
    "downloadUri": null,
    "flavor": null
}
```

Para obter mais informações, consulte [ExportIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829b).

### <a name="download-the-exported-model"></a>Baixar o modelo exportado

Os exemplos a seguir demonstram como recuperar a URL do modelo exportado. Substitua `{projectId}` pela ID do projeto. Substitua `{iterationId}` pela ID retornada do treinamento do modelo.

```bash
curl -X GET "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii ''
```

```powershell
$resp = Invoke-WebRequest -Method 'GET' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" }
$resp.Content
```

A resposta à solicitação é semelhante ao seguinte documento JSON:

```json
[
    {
        "platform": "CoreML",
        "status": "Done",
        "downloadUri": "https://linktoexportedmodel",
        "flavor": null
    }
]
```

Para obter mais informações, consulte [GetExports](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829a).