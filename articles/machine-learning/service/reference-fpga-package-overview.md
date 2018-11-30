---
title: Pacote de FPGA para aceleração de hardware para o Azure Machine Learning
description: Saiba mais sobre os pacotes do Python disponíveis para usuários do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: reference
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 83c5a788f85fcc47c221f5c8f9e6944c4448fbf9
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51712119"
---
# <a name="azure-machine-learning-hardware-acceleration-package"></a>Pacote de aceleração de hardware do Azure Machine Learning

>[!Note]
>**Este artigo está obsoleto.** Este pacote FPGA foi preterido. A compatibilidade com essa funcionalidade foi adicionada ao SDK do Azure Machine Learning. A compatibilidade com esse pacote será progressivamente encerrada. [Exibir linha do tempo de suporte](overview-what-happened-to-workbench.md#timeline). Saiba mais sobre a [compatibilidade com FPGA](concept-accelerate-with-fpgas.md) atualizada.

O pacote de aceleração de hardware do Azure Machine Learning é uma extensão que pode ser instalada com pip do Python para o Azure Machine Learning que permite aos cientistas de dados e aos desenvolvedores de AI fazer o seguinte com maior rapidez:

+ Apresentar imagens com uma versão quantificada de ResNet 50

+ Treinar classificadores com base nesses recursos

+ Implantar modelos de [matriz FPGA](concept-accelerate-with-fpgas.md) no Azure para inferência de latência muito baixa no Azure

## <a name="prerequisites"></a>Pré-requisitos

1. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://aka.ms/AMLfree) antes de começar.

1. Uma conta do Gerenciamento de Modelos do Azure Machine Learning. Para saber mais informações sobre a criação da conta, consulte p documento [Início Rápido do Azure Machine Learning e instalação do Workbench](../desktop-workbench/quickstart-installation.md). 

1. O pacote deve estar instalado. 

 
## <a name="how-to-install-the-package"></a>Como instalar o pacote

1. Baixe e instale a versão mais recente do [Git](https://git-scm.com/downloads).

2. Instale o [Anaconda (Python 3.6)](https://conda.io/miniconda.html)

   Para baixar um ambiente Anaconda pré-configurado, use o seguinte comando em um prompt do Git:

    ```
    git clone https://aka.ms/aml-real-time-ai
    ```
1. Para criar o ambiente, abra um **Prompt do Anaconda** e use o seguinte comando:

    ```
    conda env create -f aml-real-time-ai/environment.yml
    ```

1. Para ativar o ambiente, use o seguinte comando:

    ```
    conda activate amlrealtimeai
    ```

## <a name="sample-code"></a>Exemplo de código

Esse código de exemplo mostra como usar o SDK para implantar um modelo em uma matriz FPGA.

1. Importar o pacote:
   ```python
   import amlrealtimeai
   from amlrealtimeai import resnet50
   ```

1. Pré-processar a imagem:
   ```python 
   from amlrealtimeai.resnet50.model import LocalQuantizedResNet50
   model_path = os.path.expanduser('~/models')
   model = LocalQuantizedResNet50(model_path)
   print(model.version)
   ```

1. Apresentar as imagens:
   ```python 
   from amlrealtimeai.resnet50.model import LocalQuantizedResNet50
   model_path = os.path.expanduser('~/models')
   model = LocalQuantizedResNet50(model_path)
   print(model.version)
   ```

1. Criar um classificador:
   ```python
   model.import_graph_def(include_featurizer=False)
   print(model.classifier_input)
   print(model.classifier_output)
   ```

1. Criar a definição do serviço:
   ```python
   from amlrealtimeai.pipeline import ServiceDefinition, TensorflowStage, BrainWaveStage
   save_path = os.path.expanduser('~/models/save')
   service_def_path = os.path.join(save_path, 'service_def.zip')

   service_def = ServiceDefinition()
   service_def.pipeline.append(TensorflowStage(tf.Session(), in_images, image_tensors))
   service_def.pipeline.append(BrainWaveStage(model))
   service_def.pipeline.append(TensorflowStage(tf.Session(), model.classifier_input, model.classifier_output))
   service_def.save(service_def_path)
   print(service_def_path)
   ```
 
1. Preparar o modelo para execução em uma matriz FPGA:
   ```python
   from amlrealtimeai import DeploymentClient

   subscription_id = "<Your Azure Subscription ID>"
   resource_group = "<Your Azure Resource Group Name>"
   model_management_account = "<Your AzureML Model Management Account Name>"

   model_name = "resnet50-model"
   service_name = "quickstart-service"

   deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account)
   ```

1. Implantar o modelo para execução em uma matriz FPGA:
   ```python
   service = deployment_client.get_service_by_name(service_name)
   model_id = deployment_client.register_model(model_name, service_def_path)

   if(service is None):
      service = deployment_client.create_service(service_name, model_id)    
   else:
      service = deployment_client.update_service(service.id, model_id)
   ```

1. Criar o cliente:
    ```python
   from amlrealtimeai import PredictionClient
   client = PredictionClient(service.ipAddress, service.port)  
   ```

1. Chamar a API:
   ```python
   image_file = R'C:\path_to_file\image.jpg'
   results = client.score_image(image_file)
   ```

## <a name="reporting-issues"></a>Problemas de relatórios

Use o [fórum](https://aka.ms/aml-forum-service) para relatar quaisquer problemas que você encontrar com o pacote.

## <a name="next-steps"></a>Próximas etapas

[Implantar um modelo como um serviço Web em uma FPGA](how-to-deploy-fpga-web-service.md)