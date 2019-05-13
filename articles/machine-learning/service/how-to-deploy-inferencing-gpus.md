---
title: Como implantar um modelo de aprendizado profundo para inferência com GPU
titleSuffix: Azure Machine Learning service
description: Saiba como implantar um modelo de aprendizado profundo como um serviço web que usa uma GPU para inferência. Neste artigo, um modelo do Tensorflow é implantado em um cluster do serviço de Kubernetes do Azure. O cluster usa uma VM habilitada para GPU para hospedar o serviço web e solicitações de inferência de pontuação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: 5cc0fe0526937245d3ca913afc477f0259e2afd4
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65515168"
---
# <a name="how-to-do-gpu-inferencing"></a>Como fazer a inferência GPU

Saiba como usar a inferência GPU para um modelo implantado como um serviço web de aprendizado de máquina. Neste artigo, você aprenderá como usar o serviço de Azure Machine Learning para implantar um modelo de aprendizado profundo de Tensorflow do exemplo. O modelo é implantado em um cluster do serviço de Kubernetes do Azure (AKS) que usa uma VM habilitada para GPU para hospedar o serviço. Quando as solicitações são enviadas para o serviço, o modelo usa a GPU para realizar a inferência.

GPUs oferecem vantagens de desempenho sobre CPUs na computação altamente paralelizável. Modelos (especialmente para grandes lotes de solicitações) de aprendizagem profunda de inferência e treinamento são casos de uso excelente para GPUs.  

Este exemplo mostra como implantar um modelo do TensorFlow salvo no Azure Machine Learning. 

## <a name="goals-and-prerequisites"></a>Pré-requisitos e objetivos

Siga as instruções para:
* Criar uma GPU habilitada cluster AKS
* Implantar um modelo com a GPU do Tensorflow

Pré-requisitos:
* O espaço de trabalho do Azure Machine Learning services
* Python
* Tensorflow SavedModel registrado. Para saber como registrar modelos, consulte [implantar modelos](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#registermodel)

Este artigo se baseia [implantando modelos de Tensorflow no AKS](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/production-deploy-to-aks-gpu/production-deploy-to-aks-gpu.ipynb), que usa o TensorFlow salvada modelos e implanta um cluster do AKS. No entanto, com pequenas alterações para o arquivo de pontuação e o arquivo de ambiente é aplicável a qualquer estrutura de aprendizado de máquina que dão suporte a GPUs.  

## <a name="provision-aks-cluster-with-gpus"></a>Cluster do AKS provisionar com GPUs
O Azure tem várias opções diferentes de GPU, que pode ser usado para inferência. Ver [a lista de série N](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) para obter uma análise completa de recursos e os custos. 

Para obter mais informações sobre como usar o AKS com o serviço Azure Machine Learning, consulte o [como implantar e onde o artigo.](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#create-a-new-cluster)

```python
# Provision AKS cluster with GPU machine
prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

# Create the cluster
aks_target = ComputeTarget.create(
    workspace=ws, name=aks_name, provisioning_configuration=prov_config
)

aks_target.wait_for_deployment()
```

> [!IMPORTANT]
> Azure cobrará desde que o cluster do AKS é provisionado. Certifique-se de excluir o cluster do AKS depois que você terminar de usá-lo.


## <a name="write-entry-script"></a>Escrever script de entrada

Salve o seguinte em seu diretório de trabalho como `score.py`. Esse arquivo será usado para classificar imagens conforme elas são enviadas ao seu serviço. Esse arquivo carrega o TensorFlow salvo o modelo e, em seguida, em cada POSTAGEM solicitação passa a imagem de entrada para a sessão de TensorFlow e retorna as pontuações resultantes.
Outras estruturas de inferência exigirão diferentes arquivos de pontuação.

```python
import tensorflow as tf
import numpy as np
import ujson
from azureml.core.model import Model
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    global session
    global input_name
    global output_name
    
    session = tf.Session()

    model_path = Model.get_model_path('resnet50')
    model = tf.saved_model.loader.load(session, ['serve'], model_path)
    if len(model.signature_def['serving_default'].inputs) > 1:
        raise ValueError("This score.py only supports one input")
    input_name = [tensor.name for tensor in model.signature_def['serving_default'].inputs.values()][0]
    output_name = [tensor.name for tensor in model.signature_def['serving_default'].outputs.values()]
    

@rawhttp
def run(request):
    if request.method == 'POST':
        reqBody = request.get_data(False)
        resp = score(reqBody)
        return AMLResponse(resp, 200)
    if request.method == 'GET':
        respBody = str.encode("GET is not supported")
        return AMLResponse(respBody, 405)
    return AMLResponse("bad request", 500)

def score(data):
    result = session.run(output_name, {input_name: [data]})
    return ujson.dumps(result[1])

if __name__ == "__main__":
    init()
    with open("lynx.jpg", 'rb') as f: #load file for testing locally
        content = f.read()
        print(score(content))

```

## <a name="define-conda-environment"></a>Definir o ambiente do Conda
Crie um arquivo de ambiente do conda chamado `myenv.yml` para especificar as dependências para o seu serviço. É importante especificar que você está usando `tensorflow-gpu` para alcançar desempenho acelerado.
```yaml
name: aml-accel-perf
channels:
  - defaults
dependencies:
  - tensorflow-gpu = 1.12
  - numpy
  - ujson
  - pip:
    - azureml-core
    - azureml-contrib-services
```

## <a name="define-gpu-inferenceconfig"></a>Definir InferenceConfig GPU

Criar uma [ `InferenceConfig` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) que especifica que você está ativando GPU. Isso garantirá que o CUDA for instalado com sua imagem.

```python
from azureml.core.model import Model
from azureml.core.model import InferenceConfig

aks_service_name ='gpu-rn'
gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled = False, 
                                                    num_replicas = 3, 
                                                    cpu_cores=2, 
                                                    memory_gb=4)
model = Model(ws,"resnet50")

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml", 
                                   gpu_enabled=True)
```

Para obter mais informações, consulte [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) e [AksServiceDeploymentConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py).
## <a name="deploy-the-model"></a>Implantar o modelo

Implantar o modelo em seu cluster do AKS e aguarde a criação do seu serviço.

```python
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config, 
                           deployment_config=aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!NOTE]
> O serviço do Azure Machine Learning não implantará um modelo com um `InferenceConfig` GPU que espera um cluster sem a GPU.

Para obter mais informações, consulte [modelo](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-sample-query-to-deployed-model"></a>Consulta de exemplo do problema para implantar o modelo

Emita uma consulta de exemplo para seu modelo implantado. Esse modelo será pontuação de qualquer imagem jpeg que você enviar a ele como uma solicitação post. 

```python
scoring_url = aks_service.scoring_uri
api_key = aks_service.get_key()(0)
IMAGEURL = "https://upload.wikimedia.org/wikipedia/commons/thumb/6/68/Lynx_lynx_poing.jpg/220px-Lynx_lynx_poing.jpg"

headers = {'Authorization':('Bearer '+ api_key)}
img_data = read_image_from(IMAGEURL).read()
r = requests.post(scoring_url, data = img_data, headers=headers)
```

> [!IMPORTANT]
> Para otimizar a latência e a taxa de transferência, seu cliente deve estar na mesma região do Azure que o ponto de extremidade.  Atualmente, as APIs são criadas na região do Azure Leste dos EUA.

## <a name="cleaning-up-the-resources"></a>Limpando recursos

Exclua os recursos depois de concluir a demonstração.

> [!IMPORTANT]
> Azure cobrará você com base em quanto tempo o cluster do AKS é implantado. Certifique-se para limpá-los depois que terminar com ele.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Próximas etapas

* [Implantar o modelo de FPGA](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service)
* [Implantar o modelo com ONNX](https://docs.microsoft.com/azure/machine-learning/service/how-to-build-deploy-onnx#deploy)
* [Treinar modelos de DNN Tensorflow](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-tensorflow)
