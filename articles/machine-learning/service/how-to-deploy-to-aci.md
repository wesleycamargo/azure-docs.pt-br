---
title: Implantar serviços web para Instâncias de Contêiner do Azure (ACI) - Azure Machine Learning
description: Saiba como implantar um modelo treinado como um serviço web em Instâncias de Contêiner do Azure (ACI) com o serviço Azure Machine Learning. Este artigo mostra três maneiras diferentes de implantar um modelo em ACI. Elas diferem no número de linhas de código e o controle que você tem em nomear partes da implantação.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: raymondl
author: raymondlaghaeian
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: b004abb3959bbfe36fc200bf762114f88f3d2ead
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2018
ms.locfileid: "51345027"
---
# <a name="deploy-web-services-to-azure-container-instances"></a>Implantar serviços web nas Instâncias de Contêiner do Azure 

Você pode implantar o modelo treinado como um serviço web em [Instâncias de Contêiner do Azure](https://azure.microsoft.com/services/container-instances/) (ACI), [Serviço de Kubernetes do Azure](https://azure.microsoft.com/services/kubernetes-service/) (AKS), dispositivo IoT edge ou [matrizes de portas programáveis em campo (FPGAs)](concept-accelerate-with-fpgas.md) 

ACI é geralmente mais barato do que AKS e pode ser configurado em 4 a 6 linhas de código. ACI é a opção perfeita para implantações de teste. Posteriormente, quando você estiver pronto para usar seus modelos e serviços da web para uso de produção de grande escala, você poderá [implantá-los no AKS](how-to-deploy-to-aks.md).

Este artigo mostra três maneiras diferentes de implantar um modelo em ACI. Elas diferem no número de linhas de código e o controle que você tem em nomear partes da implantação. Do método com o mínimo de código e controle para o método com mais códigos e controle, as opções de ACI são:

* Implantar do arquivo de modelo usando `Webservice.deploy()` 
* Implantar do modelo registrado usando `Webservice.deploy_from_model()`
* Implantar o modelo registrado da imagem usando `Webservice.deploy_from_image()`

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

- Um espaço de trabalho do serviço do Machine Learning do Azure e o SDK do Azure Machine Learning para Python instalados. Saiba como obter esses pré-requisitos usando a [Introdução ao início rápido do Azure Machine Learning](quickstart-get-started.md).

- O objeto do espaço de trabalho do serviço do Azure Machine Learning

    ```python
    from azureml.core import Workspace
    ws = Workspace.from_config()
    ```

- Um modelo para implantar. Os exemplos neste documento usam o modelo criado quando você segue o tutorial "[treinar um modelo](tutorial-train-models-with-aml.md)". Se você não usar esse modelo, modifique as etapas para se referir ao nome do modelo.  Você também precisa escrever seu próprio script de pontuação para executar o seu modelo.


## <a name="configure-an-image"></a>Configurar uma imagem

Configure a imagem do Docker que é usada para armazenar todos os arquivos de modelo.
1. Crie um script de pontuação (score.py) [usando estas instruções](tutorial-deploy-models-with-aml.md#create-scoring-script).

    > [!IMPORTANT]
    > O script de pontuação recebe dados enviados de clientes e os transmite ao modelo para pontuação. Documente a estrutura de dados que o script e o modelo esperam. Ter esta documentação facilitará as etapas durante a criação de um cliente para consumir o serviço Web.

1. Crie um arquivo de ambiente (myenv.yml) [usando estas instruções](tutorial-deploy-models-with-aml.md#create-environment-file).

1. Use esses dois arquivos para configurar a imagem do Docker no Python usando o SDK da seguinte maneira:

    ```python
    from azureml.core.image import ContainerImage

    image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                      runtime = "python",
                                                      conda_file = "myenv.yml",
                                                      description = "Image with mnist model",
                                                      tags = {"data": "mnist", "type": "classification"}
                                                     )
    ```

## <a name="configure-the-aci-container"></a>Configurar o contêiner ACI

Configure o contêiner ACI ao especificar o número de CPUs e gigabyte de RAM necessários para seu contêiner ACI. O padrão de um núcleo e 1 GB de RAM são suficientes para muitos modelos. Se você acha que precisa de mais posteriormente, recrie a imagem e reimplante o serviço.  

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores = 1, 
                                               memory_gb = 1, 
                                               tags = {"data": "mnist", "type": "classification"},
                                               description = 'Handwriting recognition')
```

## <a name="register-a-model"></a>Registrar um modelo

> Ignore esse pré-requisito se estiver [Implantando de um arquivo de modelo](#deploy-from-model-file) (`Webservice.deploy()`).

Registrar um modelo a ser usado [Webservice.deploy_from_model](#deploy-from-registered-model) ou [Webservice.deploy_from_image](#deploy-from-image). Ou, se você já tiver um modelo registrado, recupere-o agora.

### <a name="retrieve-a-registered-model"></a>Recuperar um modelo registrado
Se você usar o Azure Machine Learning para treinar seu modelo, o modelo já pode ser registrado em seu workspace.  Por exemplo, a última etapa do [treinar um tutorial de modelo](tutorial-train-models-with-aml.md) registrou o modelo.  Você recupera o modelo registrado para implantar.

```python
from azureml.core.model import Model

model_name = "sklearn_mnist"
model=Model(ws, model_name)
```
  
### <a name="register-a-model-file"></a>Registrar um arquivo de modelo

Se seu modelo foi criado em outro lugar, você ainda pode registrá-lo em seu workspace.  Para registrar um modelo, o arquivo de modelo (`sklearn_mnist_model.pkl` neste exemplo) deve estar no diretório de trabalho atual. Em seguida, registre esse arquivo como um modelo chamado `sklearn_mnist` no workspace com `Model.register()`.
    
```python
from azureml.core.model import Model

model_name = "sklearn_mnist"
model = Model.register(model_path = "sklearn_mnist_model.pkl",
                        model_name = model_name,
                        tags = {"data": "mnist", "type": "classification"},
                        description = "Mnist handwriting recognition",
                        workspace = ws)
```

<a name='deploy-from-model-file'/>
## <a name="option-1-deploy-from-model-file"></a>Opção 1: Implantar do arquivo de modelo

A opção de implantar a partir de um arquivo de modelo requer o mínimo de códigos para escrever, mas também oferece a menor quantidade de controle sobre a nomenclatura de componentes. Esta opção começa com um arquivo de modelo e registra-o no workspace para você.  No entanto, você não pode nomear o modelo ou associar marcas ou uma descrição para ele.  

Essa opção usa o método do SDK, Webservice.deploy().  

**Tempo estimado**: a implantação leva cerca de 6 a 7 minutos.

1. Verifique se que o arquivo de modelo está no diretório de trabalho local.

1. Abra o arquivo de modelo de pré-requisito, score.py e altere a seção `init()` para:

    ```python
    def init():
        global model
        # retreive the local path to the model using the model name
        model_path = Model.get_model_path('sklearn_mnist_model.pkl')
        model = joblib.load(model_path)
    ```

1. Implantar o seu arquivo de modelo.

    ```python
    from azureml.core.webservice import Webservice
    
    service_name = 'aci-mnist-1'
    service = Webservice.deploy(deployment_config = aciconfig,
                                    image_config = image_config,
                                    model_paths = ['sklearn_mnist_model.pkl'],
                                    name = service_name,
                                    workspace = ws)
    
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```

1. Agora você pode [testar o serviço web](#test-web-service).

<a name='deploy-from-registered-model'/>
## <a name="option-2-deploy-from-registered-model"></a>Opção 2: Implantar do modelo registrado

A opção de implantar um arquivo de modelo registrado leva mais algumas linhas de código e permite algum controle sobre a nomenclatura de saídas. Essa opção é uma maneira conveniente para implantar um modelo registrado que você já tem.  No entanto, você não pode nomear a imagem do Docker.  

Essa opção usa o método do SDK, Webservice.deploy_from_model().

**Tempo estimado**: a implantação com esta opção leva cerca de 8 minutos.

1. Execute o código para configurar o contêiner do Docker e o contêiner ACI e especificar o modelo registrado.

    ```python
    from azureml.core.webservice import Webservice

    service_name = 'aci-mnist-2'
    service = Webservice.deploy_from_model(deployment_config = aciconfig,
                                           image_config = image_config,
                                           models = [model], # this is the registered model object
                                           name = service_name,
                                           workspace = ws)
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```

1. Agora você pode [testar o serviço web](#test-web-service).

<a name='deploy-from-image'/>
## <a name="option-3-deploy-from-image"></a>Opção 3: Implantar da imagem

Implantar um modelo registrado (`model`) usando `Webservice.deploy_from_image()`. Esse método permite que você crie a imagem do Docker separadamente e, em seguida, implante a partir dessa imagem.

1. Criar e registrar a imagem do Docker no workspace, usando `ContainerImage.create()`

    Esse método oferece mais controle sobre a imagem, criando-a em uma etapa separada.  O modelo registrado (`model`) está incluído na imagem.
    
    ```python
    from azureml.core.image import ContainerImage
    
    image = ContainerImage.create(name = "myimage1",
                                  models = [model], # this is the registered model object
                                  image_config = image_config,
                                  workspace = ws)
    
    image.wait_for_creation(show_output = True)
    ```
**Tempo estimado**: aproximadamente 3 minutos.

1. Implantar a imagem do Docker como um serviço usando `Webservice.deploy_from_image()`

    Agora, implante a imagem na ACI.  
    
    ```python
    from azureml.core.webservice import Webservice
    
    service_name = 'aci-mnist-3'
    service = Webservice.deploy_from_image(deployment_config = aciconfig,
                                                image = image,
                                                name = service_name,
                                                workspace = ws)
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```   
 
**Tempo estimado**: aproximadamente 3 minutos.

Esse método lhe dá mais controle sobre como criar e nomear os componentes na implantação.

Agora você pode testar o serviço web.

## <a name="a-nametest-web-servicetest-the-web-service"></a><a name='test-web-service'/>Testar o serviço Web

O serviço web é o mesmo, independentemente de qual método foi usado.  Para obter previsões, use o método do serviço `run`.  

```python
# Load Data
import os
import urllib

os.makedirs('./data', exist_ok = True)

urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/test-images.gz')

from utils import load_data
X_test = load_data('./data/test-images.gz', False) / 255.0

from sklearn import datasets
import numpy as np
import json

# find 5 random samples from test set
n = 5
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding = 'utf8')

# predict using the deployed model
prediction = service.run(input_data = test_samples)
print(prediction)
```


## <a name="clean-up-resources"></a>Limpar recursos

Se você não pretende usar esse serviço web, exclua-o para não incorrer em encargos.

```python
service.delete()
```

## <a name="next-steps"></a>Próximas etapas

* Saiba como [Consumir um modelo de ML implantado como um serviço Web](how-to-consume-web-service.md).
* Saiba como [implantar no Serviço de Kubernetes do Azure](how-to-deploy-to-aks.md) para uma implantação em escala maior. 
