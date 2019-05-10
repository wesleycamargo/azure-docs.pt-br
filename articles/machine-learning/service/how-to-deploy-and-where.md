---
title: Como e onde implantar modelos
titleSuffix: Azure Machine Learning service
description: 'Saiba como e onde implantar os modelos do serviço do Azure Machine Learning incluindo: Instâncias de Contêiner do Azure, Serviço de Kubernetes do Azure, Azure IoT Edge e matriz de porta programável no campo.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.custom: seoapril2019
ms.openlocfilehash: 45421a249642abf37c89aa33e2e8a1b4a9e5e497
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65506998"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Implantar modelos com o serviço do Azure Machine Learning

Saiba como implantar seu modelo de machine learning como um serviço web na nuvem do Azure ou em dispositivos IoT Edge. As informações neste documento ensina como implantar para os destinos de computação a seguir:

| Destino de computação | Tipo de implantação | Descrição |
| ----- | ----- | ----- |
| [Serviço da web local](#local) | Teste/depuração | BOM para testar e solucionar problemas limitado.
| [AKS (Serviço de Kubernetes do Azure)](#aks) | Inferência de tipos em tempo real | Ideal para implantações de produção em grande escala. Fornece o dimensionamento automático e tempo de resposta rápido. |
| [ACI (Instâncias de Contêiner do Azure)](#aci) | Testes | BOM para escala baixa, cargas de trabalho baseadas na CPU. |
| [Computação do Azure Machine Learning](how-to-run-batch-predictions.md) | (Visualização) Inferência de tipos de lote | Execute computação sem servidor de pontuação em lote. Dá suporte a VMs normais e de baixa prioridade. |
| [Azure IoT Edge](#iotedge) | (Visualização) Módulo do IoT | Implantar e fornecer modelos de ML em dispositivos IoT. |

## <a name="deployment-workflow"></a>Fluxo de trabalho de implantação

O processo de implantação de um modelo é semelhante para todos os destinos de computação:

1. Registre o modelo (s).
1. Implante o modelo (s).
1. Teste implantados modelo (s).

Para obter mais informações sobre os conceitos envolvidos no fluxo de trabalho de implantação, confira [Gerenciar, implantar e monitorar modelos com o Serviço do Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites-for-deployment"></a>Pré-requisitos para implantação

- Um modelo. Se você não tiver um modelo treinado, você pode usar o modelo e arquivos de dependência fornecida no [este tutorial](https://aka.ms/azml-deploy-cloud).

- O [extensão de CLI do Azure para o serviço de Machine Learning](reference-azure-machine-learning-cli.md), ou o [SDK do Python do Azure Machine Learning](https://aka.ms/aml-sdk).

## <a id="registermodel"></a> Registrar um modelo de aprendizado de máquina

O registro de modelo é uma maneira de armazenar e organizar modelos treinados na nuvem do Azure. Os modelos são registrados em seu workspace do Serviço do Azure Machine Learning. O modelo pode ser treinado usando o Azure Machine Learning ou importados de um modelo treinado em outro lugar. Os exemplos a seguir demonstram como registrar um modelo de arquivo:

### <a name="register-a-model-from-an-experiment-run"></a>Registrar um modelo de uma execução de teste

**Exemplo Scikit-Learn, com a CLI**
```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment
```
**Usando o SDK**
```python
model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep='\t')
```

### <a name="register-an-externally-created-model"></a>Registrar um modelo criado externamente
Você pode registrar um modelo criado externamente, fornecendo uma **caminho local** ao modelo. Você pode fornecer uma pasta ou um único arquivo.

**Exemplo ONNX com o SDK do Python:**
```python
onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
!tar xvzf mnist.tar.gz

model = Model.register(workspace = ws,
                       model_path ="mnist/model.onnx",
                       model_name = "onnx_mnist",
                       tags = {"onnx": "demo"},
                       description = "MNIST image classification CNN from ONNX Model Zoo",)
```

**Usando a CLI**
```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

**Tempo estimado**: Cerca de 10 segundos.

Para obter mais informações, consulte a documentação de referência da [classe do modelo](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="how-to-deploy"></a>Como implantar

Para implantar como um serviço web, você deve criar uma configuração de inferência de tipos (`InferenceConfig`) e uma configuração de implantação. A configuração de inferência de tipos, você pode especificar os scripts e as dependências necessárias para atender seu modelo. A configuração de implantação você deve especificar os detalhes de como fornecer o modelo de destino de computação.


### <a id="script"></a> 1. Definir o seu script de entrada & dependências

O script de entrada recebe os dados enviados para um serviço web implantado e passa-o para o modelo. Ele então envia de volta ao cliente a resposta retornada pelo modelo. **O script é específico para seu modelo**; ele deve compreender os dados que o modelo de espera e retorna.

O script contém duas funções que carregam e executam o modelo:

* `init()`: Normalmente, essa função carrega o modelo em um objeto global. Essa função é executada apenas uma vez quando o contêiner do Docker para o serviço web é iniciado.

* `run(input_data)`: Essa função usa o modelo para prever um valor com base nos dados de entrada. Entradas e saídas para a execução normalmente usam JSON para serialização e desserialização. Você também pode trabalhar com os dados binários brutos. Você pode transformar os dados antes de enviá-los para o modelo ou antes de retorná-los ao cliente.

#### <a name="optional-automatic-swagger-schema-generation"></a>(Opcional) Geração automática do esquema de Swagger

Para automaticamente gerar um esquema para o serviço web, fornecer um exemplo de entrada e/ou de saída no construtor para um dos objetos do tipo definido e o tipo e o exemplo são usados para criar automaticamente o esquema. Serviço de Machine Learning do Azure, em seguida, cria uma [OpenAPI](https://swagger.io/docs/specification/about/) (Swagger) a especificação para o serviço web durante a implantação.

Atualmente, há suporte para os seguintes tipos:

* `pandas`
* `numpy`
* `pyspark`
* objeto padrão do Python

Para usar a geração de esquema, inclua o `inference-schema` pacote em seu arquivo de ambiente do conda. O exemplo a seguir usa `[numpy-support]` , pois o script de entrada usa um tipo de parâmetro numpy: 

#### <a name="example-dependencies-file"></a>Exemplo de arquivo de dependências
O exemplo a seguir é um exemplo de um arquivo de dependências de Conda para inferência de tipos.
```python
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn
    - inference-schema[numpy-support]
```

Se você quiser usar a geração de esquema automático, o script de entrada **devem** importar o `inference-schema` pacotes. 

Definir a entrada e formatos de exemplo na saída de `input_sample` e `output_sample` variáveis que representam os formatos de solicitação e resposta para o serviço web. Usar esses exemplos na entrada e saída decoradores de função no `run()` função. Scikit-Saiba o exemplo a seguir usa a geração de esquema.

> [!TIP]
> Depois de implantar o serviço, use o `swagger_uri` propriedade para recuperar o documento de esquema JSON.

#### <a name="example-entry-script"></a>Script de exemplo de entrada

O exemplo a seguir demonstra como aceitar e retornar dados JSON:

**Exemplo de Scikit-learn com geração de Swagger:**
```python
import json
import numpy as np
from sklearn.externals import joblib
from sklearn.linear_model import Ridge
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType

def init():
    global model
    # note here "sklearn_regression_model.pkl" is the name of the model registered under
    # this is a different behavior than before when the code is run locally, even though the code is the same.
    model_path = Model.get_model_path('sklearn_regression_model.pkl')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)

input_sample = np.array([[10,9,8,7,6,5,4,3,2,1]])
output_sample = np.array([3726.995])

@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Para obter mais exemplos de script, consulte os exemplos a seguir:

* Pytorch: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* TensorFlow: [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* Keras: [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* ONNX: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* Pontuação em relação aos dados binários: [Como consumir um serviço web](how-to-consume-web-service.md)

### <a name="2-define-your-inferenceconfig"></a>2. Definir seu InferenceConfig

A configuração de inferência de tipos descreve como configurar o modelo para fazer previsões. O exemplo a seguir demonstra como criar uma configuração de inferência de tipos:

```python
inference_config = InferenceConfig(source_directory="C:/abc",
                                   runtime= "python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

Neste exemplo, a configuração contém os seguintes itens:

* Um diretório que contém os ativos necessários para inferência de tipos
* Esse modelo requer o Python
* O [script de entrada](#script), que é usado para manipular solicitações da web enviadas para o serviço implantado
* O arquivo conda que descreve os pacotes Python necessários para executar inferências

Para obter informações sobre a funcionalidade de InferenceConfig, consulte o [configuração avançada](#advanced-config) seção.

### <a name="3-define-your-deployment-configuration"></a>3. Definir sua configuração de implantação

Antes de implantar, você deve definir a configuração de implantação. A configuração de implantação é específica para o destino de computação que irá hospedar o serviço web. Por exemplo, ao implantar localmente deve especificar a porta em que o serviço aceita solicitações.

Você também precisará criar o recurso de computação. Por exemplo, se você fizer isso ainda não tiver um serviço de Kubernetes do Azure associado com seu espaço de trabalho.

A tabela a seguir fornece um exemplo de criação de uma configuração de implantação para cada destino de computação:

| Destino de computação | Exemplo de configuração de implantação |
| ----- | ----- |
| Local | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Instância do Contêiner do Azure | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Serviço de Kubernetes do Azure | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

As seções a seguir demonstram como criar a configuração de implantação e, em seguida, usá-lo para implantar o serviço web.

## <a name="where-to-deploy"></a>Onde implantar

### <a id="local"></a> Implantar localmente

Os exemplos desta seção usam [deploy_from_image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-), que requer que você registre o modelo e a imagem antes de fazer uma implantação. Para obter mais informações sobre outros métodos de implantação, consulte [implante](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) e [deploy_from_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-).

**Para implantar localmente, você precisa ter o Docker instalado no computador local.**

**Usando o SDK**

```python
deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

**Usando a CLI**

```azurecli-interactive
az ml model deploy -m sklearn_mnist:1 -ic inferenceconfig.json -dc deploymentconfig.json
```

### <a id="aci"></a> Implantar instâncias de contêiner do Azure (desenvolvimento/teste)

Use as Instâncias de Contêiner do Azure para implantar os modelos como um serviço Web, se uma ou mais das seguintes condições forem verdadeiras:
- Você precisa implantar rapidamente e validar o modelo.
- Você está testando um modelo ainda em desenvolvimento. 

Para ver a disponibilidade de região e de cota do ACI, consulte o [cotas e disponibilidade de região para instâncias de contêiner do Azure](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) artigo.

**Usando o SDK**

```python
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

**Usando a CLI**

```azurecli-interactive
az ml model deploy -m sklearn_mnist:1 -n aciservice -ic inferenceconfig.json -dc deploymentconfig.json
```

Para obter mais informações, consulte a documentação de referência para as classes [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) e [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

### <a id="aks"></a> Implantar o serviço de Kubernetes do Azure (produção)

Você pode usar um cluster AKS existente ou criar outro usando o SDK do Azure Machine Learning, a CLI ou o portal do Azure.


> [!IMPORTANT]
> A criação de um cluster AKS é um processo único em seu espaço de trabalho. Você pode reutilizar esse cluster para várias implantações.
> Se você não tiver criado ou anexado a um AKS cluster go <a href="#create-attach-aks">aqui</a>.

#### Implantar no AKS <a id="deploy-aks"></a>

Você pode implantar no AKS com a CLI do Azure ML:
```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n aksservice -ic inferenceconfig.json -dc deploymentconfig.json
```

Você também pode usar o SDK do Python:
```python
aks_target = AksCompute(ws,"myaks")
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aksservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

Para obter mais informações sobre como configurar sua implantação do AKS, incluindo o dimensionamento automático, consulte o [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice) referência.

**Tempo estimado:** Aproximadamente 5 minutos.

#### Criar ou anexar a um cluster do AKS <a id="create-attach-aks"></a>
Criar ou anexar a um cluster do AKS é um **um processo de tempo** para seu espaço de trabalho. Depois que um cluster tenha sido associado a seu espaço de trabalho, você pode usá-lo para várias implantações. 

Se você excluir o cluster ou o grupo de recursos que o contém, você deve criar um novo cluster na próxima vez que você precisa implantar.

##### <a name="create-a-new-aks-cluster"></a>Criar um novo cluster do AKS
Para obter mais informações sobre a configuração `autoscale_target_utilization`, `autoscale_max_replicas`, e `autoscale_min_replicas`, consulte o [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none-) referência.
O exemplo a seguir demonstra como criar um novo cluster do serviço Kubernetes do Azure:

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

Para obter mais informações sobre como criar um cluster do AKS fora do SDK do Azure Machine Learning, consulte os seguintes artigos:
* [Criar um cluster AKS](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Criar um cluster do AKS (portal)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)


> [!IMPORTANT]
> Para [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), se você escolher valores personalizados para agent_count e vm_size, será necessário certificar-se de que agent_count multiplicado por vm_size será maior ou igual a 12 CPUs virtuais. Por exemplo, se você usar um vm_size de "Standard_D3_v2", que tenha 4 CPUs virtuais, será necessário escolher um agent_count de 3 ou maior.

**Tempo estimado**: Aproximadamente 20 minutos.

##### <a name="attach-an-existing-aks-cluster"></a>Anexar a um cluster existente do AKS

Se você já tiver o cluster do AKS em sua assinatura do Azure, e é a versão 1.12. # # e tiver pelo menos 12 CPUs virtuais, você pode usá-lo para implantar sua imagem. O código a seguir demonstra como anexar um 1.12 existente do AKS. # # cluster ao espaço de trabalho:

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)
```

## <a name="consume-web-services"></a>Consumir serviços Web
Cada serviço web implantado fornece uma API REST, portanto, você pode criar aplicativos de cliente em uma variedade de linguagens de programação. Se você tiver habilitado a autenticação para seu serviço, você precisa fornecer uma chave de serviço como um token em seu cabeçalho de solicitação.

Aqui está um exemplo de como chamar o serviço no Python:
```python
import requests
import json

headers = {'Content-Type':'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]

print(headers)
    
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Para obter mais informações, confira [Criar aplicativos cliente para consumir serviços Web](how-to-consume-web-service.md).

## <a id="update"></a> Atualizar o serviço Web

Quando você cria um novo modelo, você deve atualizar manualmente cada serviço que você deseja usar o novo modelo. Para atualizar o serviço Web, use o método `update`. O código a seguir demonstra como atualizar o serviço web para usar um novo modelo:

```python
from azureml.core.webservice import Webservice
from azureml.core.model import Model

# register new model
new_model = Model.register(model_path = "outputs/sklearn_mnist_model.pkl",
                       model_name = "sklearn_mnist",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)

service_name = 'myservice'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# Update to new model(s)
service.update(models = [new_model])
print(service.state)
print(service.get_logs())
```

## <a name="clean-up"></a>Limpar
Para excluir um serviço Web implantado, use `service.delete()`.
Para excluir um modelo registrado, use `model.delete()`.

Para obter mais informações, consulte a documentação de referência [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--), e [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## Configurações avançadas <a id="advanced-config"></a>

### <a id="customimage"></a> Usar uma imagem de base personalizada

Internamente, InferenceConfig cria uma imagem do Docker que contém o modelo e outros ativos necessários para o serviço. Se não for especificado, uma imagem de base padrão é usada.

Ao criar uma imagem para usar com a sua configuração de inferência de tipos, a imagem deve atender aos seguintes requisitos:

* Ubuntu 16.04 ou posterior.
* 4.5 Conda. # ou maior.
* Python 3.5. # ou 3.6. #.

Para usar uma imagem personalizada, defina o `base_image` propriedade de inferência configuração para o endereço da imagem. O exemplo a seguir demonstra como usar uma imagem de um registro de contêiner do Azure públicas e privadas:

```python
# use an image available in public Container Registry without authentication
inference_config.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"

# or, use an image available in a private Container Registry
inference_config.base_image = "myregistry.azurecr.io/mycustomimage:1.0"
inference_config.base_image_registry.address = "myregistry.azurecr.io"
inference_config.base_image_registry.username = "username"
inference_config.base_image_registry.password = "password"
```

A imagem a seguir URIs são para imagens fornecidas pela Microsoft e pode ser usado sem fornecer um valor de nome ou a senha do usuário:

* `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-cuda10.0-cudnn7`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-tensorrt19.03`

Para usar essas imagens, defina o `base_image` para o URI da lista acima. Defina `base_image_registry.address` como `mcr.microsoft.com`.

> [!IMPORTANT]
> Imagens da Microsoft que usam o CUDA ou o TensorRT devem ser usadas apenas nos serviços do Microsoft Azure.

Para obter mais informações sobre como carregar suas próprias imagens para um registro de contêiner do Azure, consulte [envie sua primeira imagem para um registro de contêiner do Docker privado](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-docker-cli).

Se seu modelo é treinado em computação do Azure Machine Learning, usando __versão 1.0.22 ou maior__ do SDK do Azure Machine Learning, uma imagem é criada durante o treinamento. O exemplo a seguir demonstra como usar esta imagem:

```python
# Use an image built during training with SDK 1.0.22 or greater
image_config.base_image = run.properties["AzureML.DerivedImageName"]
```

## <a name="other-inference-options"></a>Outras opções de inferência de tipos

### <a id="azuremlcompute"></a> Inferência de tipos de lote
Destinos de computação do Machine Learning do Azure são criados e gerenciados pelo serviço de Azure Machine Learning. Eles podem ser usados para previsão de lote de Pipelines de Machine Learning do Azure.

Para obter uma explicação de inferência de lote com a computação do Azure Machine Learning, leia as [como executar previsões de lote](how-to-run-batch-predictions.md) artigo.

## <a id="iotedge"></a> Inferência de tipos no IoT Edge
Suporte à implantação para a borda está em visualização. Para obter mais informações, consulte o [implantar o Azure Machine Learning como um módulo IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning) artigo.

## <a name="next-steps"></a>Próximas etapas
* [Solução de problemas de implantação](how-to-troubleshoot-deployment.md)
* [Proteger serviços Web do Azure Machine Learning com SSL](how-to-secure-web-service.md)
* [Consumir um modelo de ML implantado como um serviço Web](how-to-consume-web-service.md)
* [Monitorar seus modelos do Azure Machine Learning com o Application Insights](how-to-enable-app-insights.md)
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md)
