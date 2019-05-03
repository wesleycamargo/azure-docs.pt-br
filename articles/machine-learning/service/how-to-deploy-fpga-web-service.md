---
title: Implantar modelos em FPGAs
titleSuffix: Azure Machine Learning service
description: Saiba como implantar um serviço Web com um modelo em execução em um FPGA com o serviço do Azure Machine Learning para inferência de latência ultrabaixa.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: tedway
author: tedway
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: cfe21d2119b92665c5950d792dec6500257c6316
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024174"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning-service"></a>Implantar um modelo como um serviço Web em uma FPGA com o serviço do Azure Machine Learning

Você pode implantar um modelo como um serviço web em [campo matrizes de portões programáveis (FPGAs)](concept-accelerate-with-fpgas.md) com modelos de acelerada de Hardware do Azure Machine Learning. O uso de FPGAs fornece inferência de latência extremamente baixa, mesmo com um tamanho de lote único.

Estes modelos estão disponíveis no momento:
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

Os FPGAs estão disponíveis nessas regiões do Azure:
  - Leste dos EUA
  - Oeste dos EUA 2
  - Europa Ocidental
  - Sudeste Asiático

> [!IMPORTANT]
> Para otimizar a latência e taxa de transferência, seu cliente enviando dados para o modelo FPGA deve ser em uma das regiões acima (aquele) que você implantou o modelo).

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Teste hoje mesmo a [versão gratuita ou paga do Serviço do Azure Machine Learning](https://aka.ms/AMLFree).

- Um espaço de trabalho do serviço do Machine Learning do Azure e o SDK do Azure Machine Learning para Python instalados. Aprenda como obter esses pré-requisitos usando o documento [Como configurar um ambiente de desenvolvimento](how-to-configure-environment.md).
 
  - Instale o SDK do Python para modelos de aceleração de hardware:

    ```shell
    pip install --upgrade azureml-accel-models
    ```

## <a name="sample-notebooks"></a>Notebooks de exemplo

Para sua conveniência [notebooks de exemplo](https://aka.ms/aml-notebooks) estão disponíveis para o exemplo abaixo e, além de outros exemplos.  Procure nas pastas how-to-use-azureml e implantação acelerados modelos.

## <a name="create-and-containerize-your-model"></a>Criar e colocar seu modelo em um contêiner

Este documento descrevem como criar um gráfico de TensorFlow para pré-processar a imagem de entrada, torná-lo um recurso usando ResNet 50 em um FPGA e, em seguida, executar os recursos por meio de um classificador treinado no conjunto de dados ImageNet.

Siga as instruções para:

* Definir o modelo do TensorFlow
* Implantar o modelo
* Consumir o modelo implantado
* Excluir serviços implantados

### <a name="load-azure-ml-workspace"></a>Carregar espaço de trabalho de AM do Azure

Carregar seu espaço de trabalho de AM do Azure.

```python
import os
import tensorflow as tf
 
from azureml.core import Workspace
 
ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')
```

### <a name="preprocess-image"></a>Imagem de pré-processamento

A entrada para o serviço web é uma imagem JPEG.  A primeira etapa é decodificar a imagem JPEG e reprocessá-lo.  As imagens JPEG são tratadas como cadeias de caracteres e o resultado são tensors serão a entrada para o modelo ResNet 50.

```python
# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.accel.models.utils as utils
in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="load-featurizer"></a>Recursos de carga

Inicialize o modelo e baixe um ponto de verificação TensorFlow da versão quantizada do ResNet50 para ser usado como um featurizer.  Você pode substituir "QuantizedResnet50" no trecho de código abaixo com a importação de outras redes neurais profundas:

- QuantizedResnet152
- QuantizedVgg16
- Densenet121

```python
from azureml.accel.models import QuantizedResnet50
save_path = os.path.expanduser('~/models')
model_graph = QuantizedResnet50(save_path, is_frozen = True)
feature_tensor = model_graph.import_graph_def(image_tensors)
print(model_graph.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Adicionar classificação

Este classificador foi treinado no conjunto de dados do ImageNet.  Exemplos para transferência de aprendizagem e treinamento seus pesos personalizados estão disponíveis no conjunto de [notebooks de exemplo](https://aka.ms/aml-notebooks).

```python
classifier_output = model_graph.get_default_classifier(feature_tensor)
print(classifier_output)
```

### <a name="save-the-model"></a>Salvar o modelo

Agora que o pré-processador, featurizer ResNet 50 e o classificador tiverem sido carregados, salve o grafo e as variáveis associadas como um modelo.

```python
model_name = "resnet50"
model_def_path = os.path.join(save_path, model_name)
print("Saving model in {}".format(model_def_path))

with tf.Session() as sess:
    model_graph.restore_weights(sess)
    tf.saved_model.simple_save(sess, model_def_path,
                                   inputs={'images': in_images},
                                   outputs={'output_alias': classifier_output})
```

### <a name="register-model"></a>Registrar modelo

[Registrar](./concept-model-management-and-deployment.md) o modelo que você criou.  Adicionar marcas e outros metadados sobre o modelo ajuda a manter o controle de seus modelos treinados.

```python
from azureml.core.model import Model

registered_model = Model.register(workspace = ws
                                  model_path = model_def_path,
                                  model_name = model_name)

print("Successfully registered: ", registered_model.name, registered_model.description, registered_model.version, sep = '\t')
```

Se você já tiver registrado um modelo e deseja carregá-lo, você pode recuperá-la.

```python
from azureml.core.model import Model
model_name = "resnet50"
# By default, the latest version is retrieved. You can specify the version, i.e. version=1
registered_model = Model(ws, name="resnet50")
print(registered_model.name, registered_model.description, registered_model.version, sep = '\t')
```

### <a name="convert-model"></a>Converter o modelo

O gráfico de TensorFlow precisa ser convertido para o formato do Open Neural Network Exchange ([ONNX](https://onnx.ai/)).  Você precisará fornecer os nomes dos tensors a entrada e saídas, e esses nomes serão usados pelo cliente quando você consome o serviço web.

```python
input_tensor = in_images.name
output_tensors = classifier_output.name

print(input_tensor)
print(output_tensors)


from azureml.accel.accel_onnx_converter import AccelOnnxConverter

convert_request = AccelOnnxConverter.convert_tf_model(ws, registered_model, input_tensor, output_tensors)
convert_request.wait_for_completion(show_output=True)

# If the above call succeeded, get the converted model
converted_model = convert_request.result
print(converted_model.name, converted_model.url, converted_model.version, converted_model.id,converted_model.created_time)
```

### <a name="create-docker-image"></a>Criar imagem do Docker

O modelo convertido e todas as dependências são adicionadas a uma imagem do Docker.  Essa imagem do Docker, em seguida, pode ser implantada e instanciada como na nuvem ou um dispositivo de borda com suporte [caixa de dados do Azure Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview).  Você também pode adicionar marcas e descrições para a imagem do Docker registrada.

```python
from azureml.core.image import Image
from azureml.accel.accel_container_image import AccelContainerImage

image_config = AccelContainerImage.image_configuration()
image_name = "{}-image".format(model_name)

image = Image.create(name = image_name,
                     models = [converted_model],
                     image_config = image_config, 
                     workspace = ws)


image.wait_for_creation(show_output = True)
```

Listar as imagens por marca e obter os logs detalhados para nenhuma depuração.

```python
for i in Image.list(workspace = ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="model-deployment"></a>Implantação de modelo

### <a name="deploy-to-the-cloud"></a>Implantar na nuvem

Para implantar o modelo como um serviço Web de produção em larga escala, use o AKS (Serviço de Kubernetes do Azure). Você pode criar um novo usando o SDK de aprendizado de máquina do Azure, CLI ou o portal do Azure.

```python
# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'my-aks-9' 
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws, 
                                  name = aks_name, 
                                  provisioning_configuration = prov_config)

%%time
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)

#Set the web service configuration (using default here)
aks_config = AksWebservice.deploy_configuration()

%%time
aks_service_name ='aks-service-1'

aks_service = Webservice.deploy_from_image(workspace = ws, 
                                           name = aks_service_name,
                                           image = image,
                                           deployment_config = aks_config,
                                           deployment_target = aks_target)
aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
print(aks_service.scoring_uri)
```

#### <a name="test-the-cloud-service"></a>Testar o serviço de nuvem
A imagem do Docker dá suporte a gRPC e o fornecimento de TensorFlow "prever" API.  Use o cliente de exemplo para chamar a imagem do Docker para obter previsões do modelo.  Código de cliente de exemplo está disponível:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Se você quiser usar servindo TensorFlow, você pode [baixar um cliente de exemplo](https://www.tensorflow.org/serving/setup).

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image using input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg", 
                             input_name=input_tensor, 
                             outputs=output_tensors)

# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
```

### <a name="clean-up-the-service"></a>O serviço de limpeza
Exclua seu serviço web, imagem e modelo (deve ser feito nessa ordem, pois há dependências).

```python
aks_service.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="deploy-to-a-local-edge-server"></a>Implantar em um servidor de borda locais

Todos os [dispositivos de borda de caixa de dados do Azure](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) contêm um FPGA para executar o modelo.  Apenas um modelo pode ser executado em FPGA ao mesmo tempo.  Para executar um modelo diferente, basta implante um novo contêiner. Instruções e exemplos de código podem ser encontrados no [Este exemplo Azure](https://github.com/Azure-Samples/aml-hardware-accelerated-models).

## <a name="secure-fpga-web-services"></a>Proteger serviços web FPGA

Para obter informações sobre como proteger serviços web FPGA, confira o documento [Proteger serviços web](how-to-secure-web-service.md).