---
title: Implantar modelos no Kubernetes a partir do serviço de aprendizado de máquina do Azure | Microsoft Docs
description: Saiba como implantar um modelo do serviço de Aprendizado de Máquina do Azure no Serviço de Kubernetes do Azure. O modelo é implantado como um serviço da web. O Serviço do Azure Kubernetes é bom para cargas de trabalho de produção em grande escala.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: raymondl
author: raymondlaghaeian
manager: cgronlun
ms.reviewer: larryfr
ms.date: 09/24/2018
ms.openlocfilehash: f74521f77420fcfc60e99dd3d70574d5e94cf084
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967737"
---
# <a name="how-to-deploy-models-from-azure-machine-learning-service-to-azure-kubernetes-service"></a>Como implantar modelos do serviço de Aprendizado de Máquina do Azure no Serviço do Azure Kubernetes

Para cenários de produção de alta escala, você pode implantar seu modelo no Azure Kubernetes Service (AKS). O Aprendizado de Máquina do Azure pode usar um cluster AKS existente ou um novo cluster criado durante a implantação. O modelo é implantado para o ASK como um serviço da web.

A implantação no AKS fornece escalonamento automático, criação de log, coleta de dados de modelo e tempos de resposta rápidos para seu serviço da web.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Um espaço de trabalho do Azure Machine Learning, um diretório local contendo seus scripts e o SDK de Aprendizado de Máquina do Azure para Python instalado. Aprenda como obter esses pré-requisitos usando o documento [Como configurar um ambiente de desenvolvimento](how-to-configure-environment.md).

- Um modelo de aprendizado de máquina treinado. Se você não tiver um, consulte o tutorial [modelo de classificação de imagens de trem](tutorial-train-models-with-aml.md).

## <a name="initialize-the-workspace"></a>Inicializar o espaço de trabalho

Para inicializar o espaço de trabalho, carregue o arquivo `config.json` que contém suas informações do espaço de trabalho.

```python
from azureml.coreazureml import Workspace

# Load existing workspace from the config file info.
ws = Workspace.from_config()
```

## <a name="register-the-model"></a>Registre o modelo

Para registrar um modelo existente, especifique o caminho do modelo, a descrição e as tags.

```python
from azureml.core.model import Model

model = Model.register(model_path = "model.pkl", # this points to a local file
                        model_name = "best_model", # this is the name the model is registered as
                        tags = {"data": "diabetes", "type": "regression"},
                        description = "Ridge regression model to predict diabetes",
                        workspace = ws)
```

## <a name="create-a-docker-image"></a>Criar uma imagem do Docker

O Serviço do Kubernetes do Azure usa imagens do Docker. Para criar a imagem, use as seguintes etapas:

1. Para configurar a imagem, você deve criar um script de pontuação e um arquivo de ambiente. Para um exemplo de criação do script e do arquivo de ambiente, consulte as seguintes seções do exemplo de classificação de imagem:

    * [Crie um script de pontuação (score.py)](tutorial-deploy-models-with-aml.md#create-scoring-script)

    * [Criar um arquivo de ambiente (myenv.yml)](tutorial-deploy-models-with-aml.md#create-environment-file) 

   O exemplo a seguir usa esses arquivos para configurar a imagem:

    ```python
    from azureml.core.image import ContainerImage

    # Image configuration
    image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                        runtime = "python",
                                                        conda_file = "myenv.yml",
                                                        description = "Image with ridge regression model",
                                                        tags = {"data": "diabetes", "type": "regression"}
                                                        )
    ```

1. Para criar a imagem, use o modelo e a configuração da imagem. Esta operação pode demorar cerca de 5 minutos para concluir:

    ```python
    image = ContainerImage.create(name = "myimage1",
                                    # this is the model object
                                    models = [model],
                                    image_config = image_config,
                                    workspace = ws)

    # Wait for the create process to complete
    image.wait_for_creation(show_output = True)
    ```

## <a name="create-the-aks-cluster"></a>Criar o Cluster do AKS

O trecho de código a seguir demonstra como criar o cluster AKS. Esse processo leva cerca de 20 minutos para ser concluído:

> [!IMPORTANT]
> Criar o cluster AKS é um processo único para o seu espaço de trabalho. Uma vez criado, você pode reutilizar este cluster para várias implantações. Se você excluir o cluster ou o grupo de recursos que o contém, será necessário criar um novo cluster na próxima vez que precisar implantar.


```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aml-aks-1' 
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws, 
                                    name = aks_name, 
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

### <a name="attach-existing-aks-cluster-optional"></a>Anexar existente cluster do AKS (opcional)

Se você tiver o cluster AKS existente na sua assinatura do Azure, poderá usá-lo para implantar sua imagem. O trecho de código a seguir demonstra como anexar um cluster ao seu espaço de trabalho. 

> [!IMPORTANT]
> Há suporte para o AKS versão 1.8.7.

```python
# Get the resource id from https://porta..azure.com -> Find your resource group -> click on the Kubernetes service -> Properties
resource_id = '/subscriptions/<your subscription id>/resourcegroups/<your resource group>/providers/Microsoft.ContainerService/managedClusters/<your aks service name>'

# Set to the name of the cluster
cluster_name='my-existing-aks' 

# Attatch the cluster to your workgroup
aks_target = AksCompute.attach(workspace=ws, name=cluster_name, resource_id=resource_id)

# Wait for the operation to complete
aks_target.wait_for_provisioning(True)
```

## <a name="deploy-your-web-service"></a>Implantar o serviço web

O trecho de código a seguir demonstra como implantar a imagem no cluster do AKS:

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set configuration and service name
aks_config = AksWebservice.deploy_configuration()
aks_service_name ='aks-service-1'
# Deploy from image
aks_service = Webservice.deploy_from_image(workspace = ws, 
                                            name = aks_service_name,
                                            image = image,
                                            deployment_config = aks_config,
                                            deployment_target = aks_target)
# Wait for the deployment to complete
aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!TIP]
> Se houver erros durante a implantação, use `aks_service.get_logs()` para visualizar os logs de serviço do AKS. As informações registradas podem indicar a causa do erro.

## <a name="test-the-web-service"></a>Testar o serviço Web

Use `aks_service.run()` para testar o serviço web. O trecho de código a seguir demonstra como passar dados para o serviço e exibir a previsão:

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

prediction = aks_service.run(input_data = test_sample)
print(prediction)
```

## <a name="cleanup"></a>Limpeza

Para excluir o serviço, imagem e modelo, usam o seguinte trecho de código:

```python
aks_service.delete()
image.delete()
model.delete()
```
