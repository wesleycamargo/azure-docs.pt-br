---
title: 'Início Rápido: SDK do Python'
titleSuffix: Azure Cognitive Services
description: Neste Início Rápido, você aprenderá a usar o SDK do Python para tarefas comuns.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 02/15/2019
ms.author: pafarley
ms.openlocfilehash: 3043067f326f782c51be38382070ae0db0e90f4d
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56314163"
---
# <a name="azure-cognitive-services-computer-vision-sdk-for-python"></a>SDK da Pesquisa Visual Computacional dos Serviços Cognitivos do Azure para Python

O serviço de Pesquisa Visual Computacional fornece aos desenvolvedores o acesso a algoritmos avançados para processar imagens e retornar informações. Os algoritmos da Pesquisa Visual Computacional analisam o conteúdo de uma imagem de diferentes maneiras, dependendo das características visuais que interessam a você. Por exemplo, a Pesquisa Visual Computacional pode determinar se uma imagem apresenta conteúdo para adulto ou erótico, localizar todos os rostos em uma imagem e obter o texto manuscrito ou impresso. Esse serviço funciona com formatos de imagem populares, como JPEG e PNG. 

Use a Pesquisa Visual Computacional em seu aplicativo para:

- Analisar imagens quanto a insights
- Extrair texto de imagens
- Gerar miniaturas

Procurando mais documentação?

* [Documentação de referência do SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)
* [Documentação da Pesquisa Visual Computacional dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/computer-vision/)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma conta gratuita][azure_sub]
* [Recurso da Pesquisa Visual Computacional][computervision_resource] do Azure
* [Python 3.6 e posterior][python]

Caso precise de uma conta da API da Pesquisa Visual Computacional, crie uma com este comando da [CLI do Azure][azure_cli]:

```Bash
RES_REGION=westeurope 
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

az cognitiveservices account create \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --location $RES_REGION \
    --kind ComputerVision \
    --sku S1 \
    --yes
```

## <a name="installation"></a>Instalação

Instale o SDK da Pesquisa Visual Computacional dos Serviços Cognitivos do Azure com o [pip][pip], opcionalmente em um [ambiente virtual][venv].

### <a name="configure-a-virtual-environment-optional"></a>Configurar um ambiente virtual (opcional)

Embora não seja necessário, você poderá manter o sistema base e os ambientes do SDK do Azure isolados uns dos outros se você usar um [ambiente virtual][virtualenv]. Execute os seguintes comandos para configurar um ambiente virtual e, em seguida, entre nele com o [venv][venv], como `cogsrv-vision-env`:

```Bash
python3 -m venv cogsrv-vision-env
source cogsrv-vision-env/bin/activate
```

### <a name="install-the-sdk"></a>Instalar o SDK

Instale o [pacote][pypi_computervision] do SDK da Pesquisa Visual Computacional dos Serviços Cognitivos do Azure para Python com o [pip][pip]:

```Bash
pip install azure-cognitiveservices-vision-computervision
```

## <a name="authentication"></a>Authentication

Depois de criar o recurso da Pesquisa Visual Computacional, você precisará da **região** e de uma das **chaves de conta** para criar uma instância do objeto de cliente.

Use esses valores ao criar a instância do objeto de cliente da [ComputerVisionAPI][ref_computervisionclient]. 

### <a name="get-credentials"></a>Obter credenciais

Use o snippet da [CLI do Azure][cloud_shell] abaixo para popular duas variáveis de ambiente com a **região** e uma das **chaves** da conta da Pesquisa Visual Computacional (encontre também esses valores no [portal do Azure][azure_portal]). O trecho é formatado para o shell do Bash.

```Bash
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

export ACCOUNT_REGION=$(az cognitiveservices account show \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query location \
    --output tsv)

export ACCOUNT_KEY=$(az cognitiveservices account keys list \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query key1 \
    --output tsv)
```

### <a name="create-client"></a>Criar cliente

Depois de popular as variáveis de ambiente `ACCOUNT_REGION` e `ACCOUNT_KEY`, crie o objeto de cliente da [ComputerVisionAPI][ref_computervisionclient].

```Python
from azure.cognitiveservices.vision.computervision import ComputerVisionAPI
from azure.cognitiveservices.vision.computervision.models import VisualFeatureTypes
from msrest.authentication import CognitiveServicesCredentials

import os
region = os.environ['ACCOUNT_REGION']
key = os.environ['ACCOUNT_KEY']

credentials = CognitiveServicesCredentials(key)
client = ComputerVisionAPI(region, credentials)
```

## <a name="usage"></a>Uso

Depois de inicializar um objeto de cliente da [ComputerVisionAPI][ref_computervisionclient], você poderá:

* Analisar uma imagem: Analise uma imagem para detectar determinadas características, como rostos, cores e marcas.   
* Gerar miniaturas: Crie uma imagem JPEG personalizada a ser usada como uma miniatura da imagem original.
* Obter a descrição de uma imagem: Obtenha uma descrição da imagem com base em seu domínio de assunto. 

Para obter mais informações sobre esse serviço, confira [O que é a Pesquisa Visual Computacional?][computervision_docs].

## <a name="examples"></a>Exemplos

As seguintes seções fornecem vários snippets de código que abrangem algumas das tarefas mais comuns da Pesquisa Visual Computacional, incluindo:

* [Analisar uma imagem](#analyze-an-image)
* [Obter a lista de domínios de assunto](#get-subject-domain-list)
* [Analisar uma imagem por domínio](#analyze-an-image-by-domain)
* [Obter uma descrição de texto de uma imagem](#get-text-description-of-an-image)
* [Obter o texto manuscrito de uma imagem](#get-text-from-image)
* [Gerar uma miniatura](#generate-thumbnail)

### <a name="analyze-an-image"></a>Analisar uma imagem

Analise uma imagem para detectar determinadas características com [`analyze_image`][ref_computervisionclient_analyze_image]. Use a propriedade [`visual_features`][ref_computervision_model_visualfeatures] para definir os tipos de análises a serem realizados na imagem. Os valores comuns são `VisualFeatureTypes.tags` e `VisualFeatureTypes.description`.

```Python
url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"

image_analysis = client.analyze_image(url,visual_features=[VisualFeatureTypes.tags])

for tag in image_analysis.tags:
    print(tag)
```

### <a name="get-subject-domain-list"></a>Obter a lista de domínios de assunto

Examine os domínios de assunto usados para analisar a imagem com [`list_models`][ref_computervisionclient_list_models]. Esses nomes de domínio são usados durante a [análise de uma imagem por domínio](#analyze-an-image-by-domain). Um exemplo de um domínio é `landmarks`.

```Python
models = client.list_models()

for x in models.models_property:
    print(x)
```

### <a name="analyze-an-image-by-domain"></a>Analisar uma imagem por domínio

Analise uma imagem por domínio de assunto com [`analyze_image_by_domain`][ref_computervisionclient_analyze_image_by_domain]. Obtenha a [lista de domínios de assunto com suporte](#get-subject-domain-list) para usar o nome de domínio correto.  

```Python
domain = "landmarks"
url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"
language = "en"

analysis = client.analyze_image_by_domain(domain, url, language)

for landmark in analysis.result["landmarks"]:
    print(landmark["name"])
    print(landmark["confidence"])
```

### <a name="get-text-description-of-an-image"></a>Obter uma descrição de texto de uma imagem

Obtenha uma descrição de texto baseada na linguagem de uma imagem com [`describe_image`][ref_computervisionclient_describe_image]. Solicite várias descrições com a propriedade `max_description` se estiver fazendo uma análise de texto para detectar palavras-chave associadas à imagem. Entre os exemplos de uma descrição de texto para a imagem a seguir estão `a train crossing a bridge over a body of water`, `a large bridge over a body of water` e `a train crossing a bridge over a large body of water`.

```Python
domain = "landmarks"
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"
language = "en"
max_descriptions = 3

analysis = client.describe_image(url, max_descriptions, language)

for caption in analysis.captions:
    print(caption.text)
    print(caption.confidence)
```

### <a name="get-text-from-image"></a>Obter o texto de uma imagem

Obtenha qualquer texto manuscrito ou impresso de uma imagem. Isso exige duas chamadas ao SDK: [`recognize_text`][ref_computervisionclient_recognize_text] e [`get_text_operation_result`][ref_computervisionclient_get_text_operation_result]. A chamada a recognize_text é assíncrona. Nos resultados da chamada a get_text_operation_result, você precisa verificar se a primeira chamada é concluída com [`TextOperationStatusCodes`][ref_computervision_model_textoperationstatuscodes] antes de extrair os dados de texto. Os resultados incluem o texto, bem como as coordenadas da caixa delimitadora para o texto. 

```Python
url = "https://azurecomcdn.azureedge.net/cvt-1979217d3d0d31c5c87cbd991bccfee2d184b55eeb4081200012bdaf6a65601a/images/shared/cognitive-services-demos/read-text/read-1-thumbnail.png"
mode = TextRecognitionMode.handwritten
raw = True
custom_headers = None
numberOfCharsInOperationId = 36

# SDK call
rawHttpResponse = client.recognize_text(url, mode, custom_headers,  raw)

# Get ID from returned headers
operationLocation = rawHttpResponse.headers["Operation-Location"]
idLocation = len(operationLocation) - numberOfCharsInOperationId
operationId = operationLocation[idLocation:]

# SDK call
result = client.get_text_operation_result(operationId)

# Get data
if result.status == TextOperationStatusCodes.succeeded:

    for line in result.recognition_result.lines:
        print(line.text)
        print(line.bounding_box)
```

### <a name="generate-thumbnail"></a>Gerar uma miniatura

Gere uma miniatura (JPG) de uma imagem com [`generate_thumbnail`][ref_computervisionclient_generate_thumbnail]. A miniatura não precisa estar nas mesmas proporções da imagem original. 

Este exemplo usa o pacote do [Pillow][pypi_pillow] para salvar a nova imagem em miniatura localmente.

```Python
from PIL import Image
import io

width = 50
height = 50
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"

thumbnail = client.generate_thumbnail(width, height, url)

for x in thumbnail:
    image = Image.open(io.BytesIO(x))

image.save('thumbnail.jpg')
```

## <a name="troubleshooting"></a>solução de problemas

### <a name="general"></a>Geral

Quando você interage com o objeto de cliente da [ComputerVisionAPI][ref_computervisionclient] usando o SDK do Python, a classe [`ComputerVisionErrorException`][ref_computervision_computervisionerrorexception] é usada para retornar erros. Os erros retornados pelo serviço correspondem aos mesmos códigos de status HTTP retornados para as solicitações da API REST.

Por exemplo, se você tentar analisar uma imagem com uma chave inválida, um erro `401` será retornado. No snippet a seguir, o [erro][ref_httpfailure] é tratado normalmente pela captura da exceção e pela exibição de informações adicionais sobre o erro.

```Python

domain = "landmarks"
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"
language = "en"
max_descriptions = 3

try:
    analysis = client.describe_image(url, max_descriptions, language)

    for caption in analysis.captions:
        print(caption.text)
        print(caption.confidence)
except HTTPFailure as e:
    if e.status_code == 401:
        print("Error unauthorized. Make sure your key and region are correct.")
    else:
        raise
```

### <a name="handle-transient-errors-with-retries"></a>Tratar erros transitórios com novas tentativas

Ao trabalhar com o cliente da [ComputerVisionAPI][ref_computervisionclient], você poderá encontrar falhas transitórias causadas por [limites de taxa][computervision_request_units] impostos pelo serviço ou outros problemas transitórios, como interrupções de rede. Para obter informações sobre como lidar com esses tipos de falhas, confira [Padrão de repetição][azure_pattern_retry] no guia Padrões de Design de Nuvem e o [padrão de Disjuntor][azure_pattern_circuit_breaker] relacionado.

## <a name="next-steps"></a>Próximas etapas

### <a name="more-sample-code"></a>Mais códigos de exemplo

Várias amostras do SDK da Pesquisa Visual Computacional para Python estão disponíveis para você no repositório GitHub do SDK. Essas amostras fornecem códigos de exemplo para cenários adicionais comumente encontrados ao trabalhar com a Pesquisa Visual Computacional:

* [recognize_text][recognize-text]

### <a name="additional-documentation"></a>Documentação adicional

Para obter uma documentação mais abrangente sobre o serviço de Pesquisa Visual Computacional, confira a [documentação da Pesquisa Visual Computacional do Azure][computervision_docs] em docs.microsoft.com.

<!-- LINKS -->
[pip]: https://pypi.org/project/pip/
[python]: https://www.python.org/downloads/

[azure_cli]: https://docs.microsoft.com/cli/azure
[azure_pattern_circuit_breaker]: https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker
[azure_pattern_retry]: https://docs.microsoft.com/azure/architecture/patterns/retry
[azure_portal]: https://portal.azure.com
[azure_sub]: https://azure.microsoft.com/free/

[cloud_shell]: https://docs.microsoft.com/azure/cloud-shell/overview

[venv]: https://docs.python.org/3/library/venv.html
[virtualenv]: https://virtualenv.pypa.io

[source_code]: https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-vision-computervision

[pypi_computervision]:https://pypi.org/project/azure-cognitiveservices-vision-computervision/
[pypi_pillow]:https://pypi.org/project/Pillow/

[ref_computervision_sdk]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision?view=azure-python
[ref_computervision_computervisionerrorexception]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.computervisionerrorexception?view=azure-python
[ref_httpfailure]: https://docs.microsoft.com/python/api/msrest/msrest.exceptions.httpoperationerror?view=azure-python


[computervision_resource]: https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtosubscribe

[computervision_docs]: https://docs.microsoft.com/azure/cognitive-services/computer-vision/home

[ref_computervisionclient]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python


[ref_computervisionclient_analyze_image]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#analyze-image-url--visual-features-none--details-none--language--en---custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_list_models]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#list-models-custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_analyze_image_by_domain]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#analyze-image-by-domain-model--url--language--en---custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_describe_image]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#describe-image-url--max-candidates--1---language--en---custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_recognize_text]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#recognize-text-url--mode--custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_get_text_operation_result]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#get-text-operation-result-operation-id--custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_generate_thumbnail]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#generate-thumbnail-width--height--url--smart-cropping-false--custom-headers-none--raw-false--callback-none----operation-config-


[ref_computervision_model_visualfeatures]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-python

[ref_computervision_model_textoperationstatuscodes]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.textoperationstatuscodes?view=azure-python

[computervision_request_units]:https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/

[recognize-text]:https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/vision/computer_vision_samples.py

