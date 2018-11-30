---
title: Implantar modelos para dispositivos IoT Edge do serviço de aprendizado de máquina do Azure | Microsoft Docs
description: Aprenda como implantar um modelo do serviço de Aprendizado de Máquina do Azure para os dispositivos do Azure IoT Edge. A implantação de um modelo em um dispositivo de borda permite que você marque dados no dispositivo em vez de enviá-lo para a nuvem e aguardar uma resposta.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: shipatel
author: shivanipatel
manager: cgronlun
ms.reviewer: larryfr
ms.date: 09/24/2018
ms.openlocfilehash: dc6119bdca850a71064795a80f3087c15189a2e5
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51709994"
---
# <a name="prepare-to-deploy-models-on-iot-edge"></a>Prepare-se para implantar modelos no IoT Edge

Neste documento, saiba como usar o serviço de Aprendizado de Máquina do Azure para preparar um modelo treinado para implantação em um dispositivo Azure IoT Edge.

Um dispositivo Azure IoT Edge é um dispositivo baseado em Linux ou Windows que executa o tempo de execução do Azure IoT Edge. Os modelos de aprendizado de máquina podem ser implantados nesses dispositivos como módulos IoT Edge. A implantação de um modelo em um dispositivo IoT Edge permite que o dispositivo use o modelo diretamente, em vez de precisar enviar dados para a nuvem para processamento. Você obtém tempos de resposta mais rápidos e menos transferência de dados.

Antes de implantar um modelo em um dispositivo de borda, use as etapas deste documento para preparar o modelo e o dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://aka.ms/AMLfree) antes de começar.

* Um workspace de serviço do Azure Machine Learning. Para criar um, use as etapas no documento [Introdução ao serviço de aprendizado de máquina do Azure](quickstart-get-started.md).

* Um ambiente de desenvolvimento. Para obter mais informações, consulte o [como configurar um ambiente de desenvolvimento](how-to-configure-environment.md) documento.

* Uma [IoT Hub do Azure](../../iot-hub/iot-hub-create-through-portal.md) na sua assinatura do Azure. 

* Um modelo treinado. Para obter um exemplo de como treinar um modelo, consulte o documento [Treinar um modelo de classificação de imagem com o Azure Machine Learning](tutorial-train-models-with-aml.md). Um modelo pré-treinado está disponível no [AI Toolkit para o repositório GitHub do Azure IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial).

## <a name="prepare-the-iot-device"></a>Preparar o dispositivo IoT

Para aprender como registrar seu dispositivo e instalar o tempo de execução da IoT, siga as etapas em [Guia de início rápido: implantar seu primeiro módulo IoT Edge em um dispositivo Linux x64](../../iot-edge/quickstart-linux.md).

## <a name="register-the-model"></a>Registre o modelo

Os módulos do Azure IoT Edge se baseiam em imagens de contêiner. Para implantar seu modelo em um dispositivo IoT Edge, use as etapas a seguir para registrar seu modelo em um espaço de trabalho do Serviço de Aprendizado de Máquina do Azure e criar uma imagem do Docker. 

1. Inicialize o workspace e carregue o arquivo config.json:

    ```python
    from azureml.core  import Workspace

    #Load existing workspace from the config file info.
    ws  = Workspace.from_config()
    ```    

1. Registre o modelo no seu workspace. Substitua o texto padrão pelo caminho, nome, tags e descrição do modelo:

    > [!IMPORTANT]
    > Se você usou o Aprendizado de Máquina do Azure para treinar o modelo, ele já pode estar registrado no espaço de trabalho. Nesse caso, ignore esta etapa. Para ver uma lista de modelos registrados com este espaço de trabalho, use `Model.list(ws)`.

    ```python
    from azureml.core.model import Model
    model = Model.register(model_path = "model.pkl", # this path points to the local file
                        model_name = "best_model", # the model gets registered as this name
                        tags = {'attribute': "myattribute", 'classification': "myclassification"},
                        description = "My awesome model",
                        workspace = ws)
    ```    

1. Recupere o modelo registrado: 

    ```python
    from azureml.core.model import Model

    model_name = "best_model"
    model = Model(ws, model_name)                     
    ```    

## <a name="create-a-docker-image"></a>Criar uma imagem do Docker

1. Crie um **script de pontuação** chamado `score.py`. Esse arquivo é usado para executar o modelo dentro da imagem. Ele deve incluir as seguintes funções:

    * A função `init()`, que normalmente carrega o modelo em um objeto global. Essa função é executada apenas uma vez quando o contêiner do Docker é iniciado. 

    * A função `run(input_data)` usa o modelo para prever um valor com base nos dados de entrada. Entradas e saídas para a execução normalmente usam JSON para serialização e desserialização, mas outros formatos são suportados.

    Por exemplo, veja o [tutorial de classificação de imagens](tutorial-deploy-models-with-aml.md#make-script).

1. Criar um **arquivo de ambiente** denominado `myenv.yml`. Este arquivo é uma especificação do ambiente Conda e lista todas as dependências necessárias ao script e ao modelo. Por exemplo, veja o [tutorial de classificação de imagens](tutorial-deploy-models-with-aml.md#make-myenv).

1. Configure a imagem do Docker usando os arquivos `score.py` e `myenv.yml`:
    
    ```python
    from azureml.core.image import Image, ContainerImage
    
    #Image configuration
    image_config = ContainerImage.image_configuration( runtime = "python", 
                           execution_script = "score.py",
                           conda_file = "myenv.yml", 
                           tags = {"attributes", "calssification"},
                           description = "Image that contains my model",
                           
                        )
    ```    

1. Crie a imagem usando o modelo e a configuração da imagem:

    ```python
    image = ContainerImage.create (name = "myimage", 
                           models = [model], #this is the model object
                           image_config = image_config,
                           workspace = ws
                        )
    ```     

    A criação da imagem demora cerca de 5 minutos.

## <a name="get-the-container-registry-credentials"></a>Obter as credenciais do registro do contêiner

O Azure IoT precisa das credenciais do registro do contêiner nas quais o serviço do Azure Machine Learning armazena imagens do docker. Use as etapas a seguir para obter as credenciais:

1. Entre no [Portal do Azure](https://portal.azure.com/signin/index).

1. Vá para o espaço de trabalho do serviço de Aprendizagem no Computador do Azure e selecione __Visão Geral__. Para ir para as configurações do registro de contêiner, selecione o link __registro__.

    ![Uma imagem da entrada do registro de contêiner](./media/how-to-deploy-to-iot/findregisteredcontainer.png)

1. Uma vez no registro do contêiner, selecione **Access Keys** e, em seguida, ative o usuário admin.

    ![Uma imagem da tela de chaves de acesso](./media/how-to-deploy-to-iot/findaccesskey.png)

1. Salve os valores para o servidor de login, nome de usuário e senha. 

   Essas credenciais são necessárias para fornecer o acesso do dispositivo de borda IoT às imagens em seu registro de contêiner particular.

## <a name="next-steps"></a>Próximas etapas

Você concluiu os preparativos para a implantação. Agora, use as etapas em [Implantar módulos do Azure IoT Edge no documento do portal do Azure](../../iot-edge/how-to-deploy-modules-portal.md) para implantar em seu dispositivo de borda. Ao configurar o __configurações do registro__ para o dispositivo, use as credenciais que você configurou anteriormente.
