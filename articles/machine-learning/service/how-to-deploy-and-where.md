---
title: Implantar modelos como serviços Web
titleSuffix: Azure Machine Learning service
description: 'Saiba como e onde implantar os modelos do serviço do Azure Machine Learning incluindo: Instâncias de Contêiner do Azure, Serviço de Kubernetes do Azure, Azure IoT Edge e matriz de porta programável no campo.'
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 0c171ff768395540c123c4ef2a19168d926b0661
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53633820"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Implantar modelos com o serviço do Azure Machine Learning

O serviço do Azure Machine Learning fornece várias maneiras de implantar o modelo treinado usando o SDK. Neste documento, saiba como implantar seu modelo como um serviço Web na nuvem do Azure ou em dispositivos do IoT Edge.

Você pode implantar modelos nos destinos de computação a seguir:

| Destino de computação | Tipo de implantação | DESCRIÇÃO |
| ----- | ----- | ----- |
| [ACI (Instâncias de Contêiner do Azure)](#aci) | Serviço Web | Implantação rápida. Bom para teste ou desenvolvimento. |
| [AKS (Serviço de Kubernetes do Azure)](#aks) | Serviço Web | Ideal para implantações de produção em grande escala. Fornece o dimensionamento automático e tempo de resposta rápido. |
| [Azure IoT Edge](#iotedge) | Módulo do IoT | Implante modelos em dispositivos do IoT. A inferência acontece no dispositivo. |
| [FPGA (matriz de porta programável no campo)](#fpga) | Serviço Web | Latência extremamente baixa para inferência em tempo real. |

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Kwk3]

## <a name="prerequisites"></a>Pré-requisitos

- Um espaço de trabalho do serviço do Machine Learning do Azure e o SDK do Azure Machine Learning para Python instalados. Saiba como obter esses pré-requisitos usando a [Introdução ao início rápido do Azure Machine Learning](quickstart-get-started.md).

- Um modelo treinado em formato pickle (`.pkl`) ou ONNX (`.onnx`). Se você não tiver um modelo treinado, use as etapas no tutorial [Treinar modelos](tutorial-train-models-with-aml.md) para treinar e registrar um com o serviço do Azure Machine Learning.

- As seções de código assumem que `ws` faz referência ao workspace do Machine Learning. Por exemplo, `ws = Workspace.from_config()`.

## <a name="deployment-workflow"></a>Fluxo de trabalho de implantação

O processo de implantação de um modelo é semelhante para todos os destinos de computação:

1. Treinar um modelo.
1. Registre o modelo.
1. Crie uma configuração de imagem.
1. Crie a imagem.
1. Implante a imagem em um destino de computação.
1. Teste a implantação
1. (Opcional) Limpe os artefatos.

    * Ao **implantar como um serviço Web**, haverá três opções de implantação:

        * [deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-): Ao usar esse método, não será necessário registrar o modelo ou criar a imagem. No entanto, você não poderá controlar o nome do modelo ou da imagem, nem as marcas e descrições associadas.
        * [deploy_from_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-): Ao usar esse método, não será necessário criar uma imagem. Mas você não terá controle sobre o nome da imagem criada.
        * [deploy_from_image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none-): Registre o modelo e crie uma imagem antes de usar esse método.

        Os exemplos neste documento usam `deploy_from_image`.

    * Ao **implantar como um módulo do IoT Edge**, será necessário registrar o modelo e criar a imagem.

## <a name="register-a-model"></a>Registrar um modelo

Somente modelos treinados podem ser implantados. O modelo pode ser treinado usando o Azure Machine Learning ou outro serviço. Para registrar um modelo do arquivo, use o código a seguir:

```python
from azureml.core.model import Model

model = Model.register(model_path = "model.pkl",
                       model_name = "Mymodel",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)
```

> [!NOTE]
> Enquanto o exemplo mostrar que está usando um modelo armazenado como um arquivo pickle, você também poderá usar o modelo ONNX. Para obter mais informações sobre o uso de modelos ONNX, consulte o documento [ONNX e Azure Machine Learning](how-to-build-deploy-onnx.md).

Para obter mais informações, consulte a documentação de referência da [classe do modelo](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a id="configureimage"></a> Criar uma configuração de imagem

Os modelos implantados são empacotados como uma imagem. A imagem contém as dependências necessárias para executar o modelo.

Para implantações de **Instância de Contêiner do Azure**, **Serviço de Kubernetes do Azure** e **Azure IoT Edge**, a classe `azureml.core.image.ContainerImage` é usada para criar uma configuração de imagem. Em seguida, a configuração da imagem é usada para criar uma nova imagem do Docker. 

O código a seguir demonstra como criar uma nova configuração de imagem:

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

Essa configuração usa um arquivo `score.py` para passar solicitações ao modelo. Esse arquivo contém duas funções:

* `init()`: Normalmente, essa função carrega o modelo em um objeto global. Essa função é executada apenas uma vez quando o contêiner do Docker é iniciado. 

* `run(input_data)`: Essa função usa o modelo para prever um valor com base nos dados de entrada. Entradas e saídas para a execução normalmente usam JSON para serialização e desserialização, mas outros formatos têm suporte.

Para um exemplo de arquivo `score.py`, consulte o [tutorial de classificação de imagens](tutorial-deploy-models-with-aml.md#make-script). Para um exemplo que usa um modelo ONNX, consulte o documento [ONNX e Azure Machine Learning](how-to-build-deploy-onnx.md).

O parâmetro `conda_file` é usado para fornecer um arquivo de ambiente do Conda. Esse arquivo define o ambiente do Conda para o modelo implantado. Para obter mais informações sobre como criar esse arquivo, consulte [Criar um arquivo de ambiente (myenv.yml)](tutorial-deploy-models-with-aml.md#create-environment-file).

Para obter mais informações, consulte a documentação de referência da [classe ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py)

## <a id="createimage"></a> Criar a imagem

Após criar a configuração da imagem, você poderá usá-la para criar uma imagem. Essa imagem é armazenada no registro de contêiner para o workspace. Uma vez criada, será possível implantar a mesma imagem em vários serviços.

```python
# Create the image from the image configuration
image = ContainerImage.create(name = "myimage", 
                              models = [model], #this is the model object
                              image_config = image_config,
                              workspace = ws
                              )
```

**Tempo estimado**: Aproximadamente 3 minutos.

Quando você registra várias imagens com o mesmo nome, o controle de versão das imagens é automático. Por exemplo, para a primeira imagem registrada como `myimage` é atribuída uma ID de `myimage:1`. Na próxima vez que você registrar uma imagem como `myimage`, a ID da nova imagem será `myimage:2`.

Para obter mais informações, consulte a documentação de referência da [classe ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py).

## <a name="deploy-the-image"></a>Implantar a imagem

Ao obter a implantação, o processo será ligeiramente diferente dependendo do destino de computação da implantação. Use as informações nas seções a seguir para saber como implantar para:

* [Instâncias de Contêiner do Azure](#aci)
* [Serviço de Kubernetes do Azure](#aks)
* [Projeto Brainwave (matrizes de portas programáveis no campo)](#fpga)
* [Dispositivos do Azure IoT Edge](#iotedge)

### <a id="aci"></a> Implantar em Instâncias de Contêiner do Azure

Use as Instâncias de Contêiner do Azure para implantar os modelos como um serviço Web, se uma ou mais das seguintes condições forem verdadeiras:

- Você precisa implantar rapidamente e validar o modelo. A implantação da ACI termina em menos de cinco minutos.
- Você está testando um modelo ainda em desenvolvimento. Para ver a disponibilidade de região e cotas para ACI, consulte o documento [Cotas e disponibilidade de região para Instâncias de Contêiner do Azure](https://docs.microsoft.com/azure/container-instances/container-instances-quotas).

Para implantar nas Instâncias de Contêiner do Azure, use as etapas a seguir:

1. Defina a configuração de implantação. O exemplo a seguir define uma configuração que usa um núcleo da CPU e 1 GB de memória:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=configAci)]

2. Para implantar a imagem criada na seção [Criar a imagem](#createimage) deste documento, use o código a seguir:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=option3Deploy)]

    **Tempo estimado**: Aproximadamente 3 minutos.

    > [!TIP]
    > Se houver erros durante a implantação, use `service.get_logs()` para visualizar os logs de serviço do AKS. As informações registradas podem indicar a causa do erro.

Para obter mais informações, consulte a documentação de referência para as classes [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) e [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

### <a id="aks"></a> Implantar o Serviço de Kubernetes do Azure

Para implantar o modelo como um serviço Web de produção em larga escala, use o AKS (Serviço de Kubernetes do Azure). Você pode usar um cluster AKS existente ou criar outro usando o SDK do Azure Machine Learning, a CLI ou o portal do Azure.

A criação de um cluster AKS é um processo único em seu espaço de trabalho. Você pode reutilizar esse cluster para várias implantações. Se você excluir o cluster, será necessário criar outro cluster na próxima vez que precisar implantar.

O Serviço de Kubernetes do Azure fornece os seguintes recursos:

* Dimensionamento automático
* Registro em log
* Coleta de dados de modelo
* Tempo de resposta rápido para seus serviços Web

Para implantar no Serviço de Kubernetes do Azure, use as etapas a seguir:

1. Para criar um cluster do AKS, use o código a seguir:

    > [!IMPORTANT]
    > Criar o cluster AKS é um processo único para o seu workspace. Uma vez criado, você pode reutilizar este cluster para várias implantações. Se você excluir o cluster ou o grupo de recursos que o contém, será necessário criar outro cluster na próxima vez que precisar implantar.
    > Para [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), se você escolher valores personalizados para agent_count e vm_size, será necessário certificar-se de que agent_count multiplicado por vm_size será maior ou igual a 12 CPUs virtuais. Por exemplo, se você usar um vm_size de "Standard_D3_v2", que tenha 4 CPUs virtuais, será necessário escolher um agent_count de 3 ou maior.

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

    **Tempo estimado**: Aproximadamente 20 minutos.

    > [!TIP]
    > Se você já tiver o cluster do AKS na assinatura do Azure e a versão for 1.11.*, poderá usá-lo para implantar a imagem. O código a seguir demonstra como anexar um cluster existente ao workspace:
    >
    > ```python
    > from azureml.core.compute import AksCompute, ComputeTarget
    > # Set the resource group that contains the AKS cluster and the cluster name
    > resource_group = 'myresourcegroup'
    > cluster_name = 'mycluster'
    > 
    > # Attatch the cluster to your workgroup
    > attach_config = AksCompute.attach_configuration(resource_group = resource_group,
    >                                          cluster_name = cluster_name)
    > aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)
    > 
    > # Wait for the operation to complete
    > aks_target.wait_for_completion(True)
    > ```

2. Para implantar a imagem criada na seção [Criar a imagem](#createimage) deste documento, use o código a seguir:

    ```python
    from azureml.core.webservice import Webservice, AksWebservice

    # Set configuration and service name
    aks_config = AksWebservice.deploy_configuration()
    aks_service_name ='aks-service-1'
    # Deploy from image
    service = Webservice.deploy_from_image(workspace = ws, 
                                                name = aks_service_name,
                                                image = image,
                                                deployment_config = aks_config,
                                                deployment_target = aks_target)
    # Wait for the deployment to complete
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```

    > [!TIP]
    > Se houver erros durante a implantação, use `service.get_logs()` para visualizar os logs de serviço do AKS. As informações registradas podem indicar a causa do erro.

Para obter mais informações, consulte a documentação de referência para as classes [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) e [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py).

### <a id="fpga"></a> Implantar para FPGA (matriz de porta programável no campo)

O projeto Brainwave torna possível obter latência extremamente baixa para solicitações de inferência em tempo real. O Project Brainwave acelera as DNN (redes neurais profundas) implantadas em matrizes de portas programáveis em campo na nuvem do Azure. As DNNs mais usadas estão disponíveis como recursos para aprendizados de transferência ou personalizável com pesos treinados de seus próprios dados.

Para obter um passo a passo de implantação de um modelo usando o Projeto Brainwave, consulte o documento [Implantar para FPGA](how-to-deploy-fpga-web-service.md).

### <a id="iotedge"></a> Implantar para Azure IoT Edge

Um dispositivo Azure IoT Edge é um dispositivo baseado em Linux ou Windows que executa o tempo de execução do Azure IoT Edge. Os modelos de aprendizado de máquina podem ser implantados nesses dispositivos como módulos IoT Edge. A implantação de um modelo em um dispositivo IoT Edge permite que o dispositivo use o modelo diretamente, em vez de precisar enviar dados para a nuvem para processamento. Você obtém tempos de resposta mais rápidos e menos transferência de dados.

Os módulos do Azure IoT Edge são implantados no dispositivo a partir de um registro de contêiner. Ao criar uma imagem a partir do modelo, a imagem é armazenada no registro de contêiner do workspace.

#### <a name="set-up-your-environment"></a>Configure seu ambiente

* Um ambiente de desenvolvimento. Para obter mais informações, consulte o [como configurar um ambiente de desenvolvimento](how-to-configure-environment.md) documento.

* Uma [IoT Hub do Azure](../../iot-hub/iot-hub-create-through-portal.md) na sua assinatura do Azure. 

* Um modelo treinado. Para obter um exemplo de como treinar um modelo, consulte o documento [Treinar um modelo de classificação de imagem com o Azure Machine Learning](tutorial-train-models-with-aml.md). Um modelo pré-treinado está disponível no [AI Toolkit para o repositório GitHub do Azure IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial).

#### <a name="prepare-the-iot-device"></a>Preparar o dispositivo IoT
É necessário criar um hub IoT e registrar um dispositivo ou reutilizá-lo com [este script](https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/createNregister).

``` bash
ssh <yourusername>@<yourdeviceip>
sudo wget https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/createNregister
sudo chmod +x createNregister
sudo ./createNregister <The Azure subscriptionID you want to use> <Resourcegroup to use or create for the IoT hub> <Azure location to use e.g. eastus2> <the Hub ID you want to use or create> <the device ID you want to create>
```

Salve a cadeia de conexão resultante após "cs": "{copie esta cadeia de caracteres}".

Inicialize o dispositivo, baixando [este script](https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/installIoTEdge) em um nó do IoT Edge do UbuntuX64 ou DSVM para executar os comandos a seguir:

```bash
ssh <yourusername>@<yourdeviceip>
sudo wget https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/installIoTEdge
sudo chmod +x installIoTEdge
sudo ./installIoTEdge
```

O nó do IoT Edge está pronto para receber a cadeia de conexão do Hub IoT. Procure a linha ```device_connection_string:``` e cole a cadeia de conexão acima entre as aspas.

Saiba mais sobre como registrar o dispositivo e instalar o tempo de execução do IoT passo a passo, seguindo o [Início rápido: Implante o primeiro módulo do IoT Edge em um documento do dispositivo Linux x64](../../iot-edge/quickstart-linux.md).


#### <a name="get-the-container-registry-credentials"></a>Obter as credenciais do registro do contêiner
Para implantar um módulo do IoT Edge no dispositivo, o Azure IoT precisa das credenciais de registro de contêiner, nas quais o serviço do Azure Machine Learning armazena imagens do docker.

É possível recuperar facilmente as credenciais de registro de contêiner necessárias de duas maneiras:

+ **No portal do Azure**:

  1. Entre no [Portal do Azure](https://portal.azure.com/signin/index).

  1. Vá para o espaço de trabalho do serviço de Aprendizagem no Computador do Azure e selecione __Visão Geral__. Para ir para as configurações do registro de contêiner, selecione o link __registro__.

     ![Uma imagem da entrada do registro de contêiner](./media/how-to-deploy-and-where/findregisteredcontainer.png)

  1. Uma vez no registro do contêiner, selecione **Access Keys** e, em seguida, ative o usuário admin.
 
     ![Uma imagem da tela de chaves de acesso](./media/how-to-deploy-and-where/findaccesskey.png)

  1. Salve os valores para **servidor de logon**, **nome de usuário** e **senha**. 

+ **Com um script do Python**:

  1. Use o seguinte script do Python após o código executado acima para criar um contêiner:

     ```python
     # Getting your container details
     container_reg = ws.get_details()["containerRegistry"]
     reg_name=container_reg.split("/")[-1]
     container_url = "\"" + image.image_location + "\","
     subscription_id = ws.subscription_id
     from azure.mgmt.containerregistry import ContainerRegistryManagementClient
     from azure.mgmt import containerregistry
     client = ContainerRegistryManagementClient(ws._auth,subscription_id)
     result= client.registries.list_credentials(resource_group_name, reg_name, custom_headers=None, raw=False)
     username = result.username
     password = result.passwords[0].value
     print('ContainerURL{}'.format(image.image_location))
     print('Servername: {}'.format(reg_name))
     print('Username: {}'.format(username))
     print('Password: {}'.format(password))
     ```
  1. Salve os valores de ContainerURL, nome do servidor, nome de usuário e senha. 

     Essas credenciais são necessárias para fornecer o acesso do dispositivo do IoT Edge às imagens no registro de contêiner particular.

#### <a name="deploy-the-model-to-the-device"></a>Implantar o modelo no dispositivo

É possível implantar um modelo facilmente executando [este script](https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/deploymodel) e fornecendo as seguintes informações das etapas acima: nome do registro de contêiner, nome de usuário, senha, URL do local da imagem, nome de implantação desejado, nome do Hub IoT e ID do dispositivo você criou. É possível fazer isso na VM, seguindo estas etapas: 

```bash 
wget https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/deploymodel
sudo chmod +x deploymodel
sudo ./deploymodel <ContainerRegistryName> <username> <password> <imageLocationURL> <DeploymentID> <IoTHubname> <DeviceID>
```

Como alternativa, você pode seguir as etapas em [Implantar módulos do Azure IoT Edge no documento do portal do Azure](../../iot-edge/how-to-deploy-modules-portal.md) para implantar a imagem no dispositivo. Ao definir as __Configurações do Registro__ do dispositivo, use o __servidor de logon__, __nome de usuário__ e __senha__ do registro de contêiner do workspace.

> [!NOTE]
> Se você não estiver familiarizado com o Azure IoT, consulte os documentos a seguir para obter informações sobre como começar a usar o serviço:
>
> * [Início rápido: Implantar o primeiro módulo do IoT Edge em um dispositivo Linux](../../iot-edge/quickstart-linux.md)
> * [Início rápido: Implantar o primeiro módulo do IoT Edge em um dispositivo Windows](../../iot-edge/quickstart.md)


## <a name="testing-web-service-deployments"></a>Implantar o primeiro módulo do IoT Edge em implantações do serviço Web de teste

Para testar uma implantação do serviço Web, você pode usar o método `run` do objeto Webservice. No exemplo a seguir, um documento JSON é configurado para um serviço Web e o resultado é exibido. Os dados enviados devem corresponder ao que o modelo espera. Neste exemplo, o formato de dados corresponde à entrada esperada pelo modelo de diabetes.

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

prediction = service.run(input_data = test_sample)
print(prediction)
```

## <a name="update-the-web-service"></a>Atualizar o serviço Web

Para atualizar o serviço Web, use o método `update`. O código a seguir demonstra como atualizar o serviço Web para usar uma nova imagem:

```python
from azureml.core.webservice import Webservice

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)
# Update the image used by the service
service.update(image = new-image)
print(service.state)
```

> [!NOTE]
> Ao atualizar uma imagem, o serviço Web não é atualizado automaticamente. É necessário atualizar manualmente cada serviço para o qual deseja usar a nova imagem.

## <a name="clean-up"></a>Limpar

Para excluir um serviço Web implantado, use `service.delete()`.

Para excluir uma imagem, use `image.delete()`.

Para excluir um modelo registrado, use `model.delete()`.

## <a name="next-steps"></a>Próximas etapas

* [Proteger serviços Web do Azure Machine Learning com SSL](how-to-secure-web-service.md)
* [Consumir um modelo de ML implantado como um serviço Web](how-to-consume-web-service.md)
* [Como executar previsões em lotes](how-to-run-batch-predictions.md)
