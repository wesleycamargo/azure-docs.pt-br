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
ms.openlocfilehash: 7fc40945588c272ae0ae80ba17b7b3752cab4306
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54353304"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Implantar modelos com o serviço do Azure Machine Learning

O serviço do Azure Machine Learning fornece várias maneiras de implantar o modelo treinado usando o SDK. Neste documento, saiba como implantar seu modelo como um serviço Web na nuvem do Azure ou em dispositivos do IoT Edge.

> [!IMPORTANT]
> O CORS (compartilhamento de recurso entre origens) atualmente não é compatível com a ação de implantar um modelo como um serviço Web.

Você pode implantar modelos nos destinos de computação a seguir:

| Destino de computação | Tipo de implantação | DESCRIÇÃO |
| ----- | ----- | ----- |
| [ACI (Instâncias de Contêiner do Azure)](#aci) | Serviço Web | Implantação rápida. Bom para teste ou desenvolvimento. |
| [AKS (Serviço de Kubernetes do Azure)](#aks) | Serviço Web | Ideal para implantações de produção em grande escala. Fornece o dimensionamento automático e tempo de resposta rápido. |
| [Azure IoT Edge](#iotedge) | Módulo do IoT | Implante modelos em dispositivos do IoT. A inferência acontece no dispositivo. |
| [FPGA (matriz de porta programável no campo)](#fpga) | Serviço Web | Latência extremamente baixa para inferência em tempo real. |

O processo de implantação de um modelo é semelhante para todos os destinos de computação:

1. Treine e registre um modelo.
1. Configure e registre uma imagem que usa o modelo.
1. Implante a imagem em um destino de computação.
1. Teste a implantação

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Kwk3]


Para obter mais informações sobre os conceitos envolvidos no fluxo de trabalho de implantação, confira [Gerenciar, implantar e monitorar modelos com o Serviço do Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Teste hoje mesmo a [versão gratuita ou paga do Serviço do Azure Machine Learning](http://aka.ms/AMLFree).

- Um espaço de trabalho do serviço do Machine Learning do Azure e o SDK do Azure Machine Learning para Python instalados. Saiba como obter esses pré-requisitos usando a [Introdução ao início rápido do Azure Machine Learning](quickstart-get-started.md).

- Um modelo treinado. Se você não tiver um modelo treinado, use as etapas no tutorial [Treinar modelos](tutorial-train-models-with-aml.md) para treinar e registrar um com o serviço do Azure Machine Learning.

    > [!NOTE]
    > Enquanto o Serviço do Azure Machine Learning pode funcionar com qualquer modelo genérico que pode ser carregado em Python 3, os exemplos deste documento demonstram o uso de um modelo armazenado no formato do pickle.
    > 
    > Para obter mais informações sobre o uso de modelos ONNX, consulte o documento [ONNX e Azure Machine Learning](how-to-build-deploy-onnx.md).

## <a id="registermodel"></a> Registrar um modelo treinado

O registro de modelo é uma maneira de armazenar e organizar modelos treinados na nuvem do Azure. Os modelos são registrados em seu workspace do Serviço do Azure Machine Learning. O modelo pode ser treinado usando o Azure Machine Learning ou outro serviço. Para registrar um modelo do arquivo, use o código a seguir:

```python
from azureml.core.model import Model

model = Model.register(model_path = "model.pkl",
                       model_name = "Mymodel",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)
```

**Tempo estimado**: Cerca de 10 segundos.

Para obter mais informações, consulte a documentação de referência da [classe do modelo](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a id="configureimage"></a> Criar e registrar uma imagem

Os modelos implantados são empacotados como uma imagem. A imagem contém as dependências necessárias para executar o modelo.

Para implantações de **Instância de Contêiner do Azure**, **Serviço de Kubernetes do Azure** e **Azure IoT Edge**, a classe [azureml.core.image.ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py) é usada para criar uma configuração de imagem. Em seguida, a configuração da imagem é usada para criar uma nova imagem do Docker. 

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

**Tempo estimado**: Cerca de 10 segundos.

Os parâmetros importantes usados neste exemplo são descritos na tabela a seguir:

| Parâmetro | DESCRIÇÃO |
| ----- | ----- |
| `execution_script` | Especifica um script Python que é usado para receber solicitações enviadas para o serviço. Neste exemplo, o script está contido no arquivo `score.py`. Para obter mais informações, confira a seção [Script de execução](#script). |
| `runtime` | Indica que a imagem usa o Python. A outra opção é `spark-py`, que usa o Python com o Apache Spark. |
| `conda_file` | Usado para fornecer um arquivo de ambiente do Conda. Esse arquivo define o ambiente do Conda para o modelo implantado. Para obter mais informações sobre como criar esse arquivo, consulte [Criar um arquivo de ambiente (myenv.yml)](tutorial-deploy-models-with-aml.md#create-environment-file). |

Para obter mais informações, consulte a documentação de referência da [classe ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py)

### <a id="script"></a> Script de execução

O script de execução recebe os dados enviados para uma imagem implantada e passa-o para o modelo. Ele então envia de volta ao cliente a resposta retornada pelo modelo. O script é específico para seu modelo; ele deve compreender os dados que o modelo espera e retorna. O script geralmente contém duas funções que carregam e executam o modelo:

* `init()`: Normalmente, essa função carrega o modelo em um objeto global. Essa função é executada apenas uma vez quando o contêiner do Docker é iniciado. 

* `run(input_data)`: Essa função usa o modelo para prever um valor com base nos dados de entrada. Entradas e saídas para a execução normalmente usam JSON para serialização e desserialização. Você também pode trabalhar com os dados binários brutos. Você pode transformar os dados antes de enviá-los para o modelo ou antes de retorná-los ao cliente. 

#### <a name="working-with-json-data"></a>Trabalhando com dados JSON

A seguir está um exemplo de script que aceita e retorna dados JSON. A função `run` transforma os dados JSON em um formato que o modelo espera e, em seguida, transforma a resposta em JSON antes de retorná-la:

```python
# import things required by this script
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression

from azureml.core.model import Model

# load the model
def init():
    global model
    # retrieve the path to the model file using the model name
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)

# Passes data to the model and returns the prediction
def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    return json.dumps(y_hat.tolist())
```

#### <a name="working-with-binary-data"></a>Trabalhando com os dados binários

Se o modelo aceita __dados binários__, use `AMLRequest`, `AMLResponse` e `rawhttp`. Aqui está um exemplo de um script que aceita dados binários e retorna os bytes invertidos para solicitações POST. Para solicitações GET, ele retorna a URL completa no corpo da resposta:

```python
from azureml.contrib.services.aml_request  import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

# Accept and return binary data
@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    # handle GET requests
    if request.method == 'GET':
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    # handle POST requests
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        respBody = bytearray(reqBody)
        respBody.reverse()
        respBody = bytes(respBody)
        return AMLResponse(respBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> O namespace `azureml.contrib` muda com frequência enquanto trabalhamos para melhorar o serviço. Assim, tudo nesse namespace deve ser considerado como uma versão prévia e sem o suporte total da Microsoft.
>
> Se você precisar testar isso em seu ambiente de desenvolvimento local, instale os componentes no namespace `contrib` usando o seguinte comando: 
> ```shell
> pip install azureml-contrib-services
> ```

### <a id="createimage"></a> Registrar a imagem

Após criar a configuração da imagem, você poderá usá-la para registrar uma imagem. Essa imagem é armazenada no registro de contêiner para o workspace. Uma vez criada, será possível implantar a mesma imagem em vários serviços.

```python
# Register the image from the image configuration
image = ContainerImage.create(name = "myimage", 
                              models = [model], #this is the model object
                              image_config = image_config,
                              workspace = ws
                              )
```

**Tempo estimado**: Aproximadamente 3 minutos.

Quando você registra várias imagens com o mesmo nome, o controle de versão das imagens é automático. Por exemplo, para a primeira imagem registrada como `myimage` é atribuída uma ID de `myimage:1`. Na próxima vez que você registrar uma imagem como `myimage`, a ID da nova imagem será `myimage:2`.

Para obter mais informações, consulte a documentação de referência da [classe ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py).

## <a id="deploy"></a> Implantar a imagem

Ao obter a implantação, o processo será ligeiramente diferente dependendo do destino de computação da implantação. Use as informações nas seções a seguir para saber como implantar para:

* [Instâncias de Contêiner do Azure](#aci)
* [Serviço de Kubernetes do Azure](#aks)
* [Projeto Brainwave (matrizes de portas programáveis no campo)](#fpga)
* [Dispositivos do Azure IoT Edge](#iotedge)

> [!NOTE]
> Ao **implantar como um serviço Web**, há três métodos de implantação que podem ser usados:
>
> | Método | Observações |
> | ----- | ----- |
> | [deploy_from_image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none-) | Você precisa registrar o modelo e crie uma imagem antes de usar esse método. |
> | [deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) | Ao usar esse método, não será necessário registrar o modelo ou criar a imagem. No entanto, você não poderá controlar o nome do modelo ou da imagem, nem as marcas e descrições associadas. |
> | [deploy_from_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-) | Ao usar esse método, não será necessário criar uma imagem. Mas você não terá controle sobre o nome da imagem criada. |
>
> Os exemplos neste documento usam `deploy_from_image`.

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

#### <a name="create-a-new-cluster"></a>Crie um novo cluster

Para criar um novo cluster do Serviço de Kubernetes do Azure, use o seguinte código:

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

#### <a name="use-an-existing-cluster"></a>Usar um cluster existente

Se você já tiver o cluster do AKS na assinatura do Azure e a versão for 1.11.*, poderá usá-lo para implantar a imagem. O código a seguir demonstra como anexar um cluster existente ao workspace:

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attatch the cluster to your workgroup
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)

# Wait for the operation to complete
aks_target.wait_for_completion(True)
```

**Tempo estimado**: Aproximadamente 3 minutos.

#### <a name="deploy-the-image"></a>Implantar a imagem

Para implantar a imagem criada na seção [Criar a imagem](#createimage) deste documento no cluster do Serviço de Kubernetes do Azure, use o código a seguir:

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

**Tempo estimado**: Aproximadamente 3 minutos.

> [!TIP]
> Se houver erros durante a implantação, use `service.get_logs()` para visualizar os logs de serviço do AKS. As informações registradas podem indicar a causa do erro.

Para obter mais informações, consulte a documentação de referência para as classes [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) e [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py).

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

O serviço Web é uma API REST, portanto, você pode criar aplicativos de cliente em uma variedade de linguagens de programação. Para obter mais informações, confira [Criar aplicativos cliente para consumir serviços Web](how-to-consume-web-service.md).

## <a id="update"></a> Atualizar o serviço Web

Para atualizar o serviço Web, use o método `update`. O código a seguir demonstra como atualizar o serviço Web para usar uma nova imagem:

```python
from azureml.core.webservice import Webservice
from azureml.core.image import Image

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# point to a different image
new_image = Image(workspace = ws, id="myimage2:1")

# Update the image used by the service
service.update(image = new_image)
print(service.state)
```

> [!NOTE]
> Ao atualizar uma imagem, o serviço Web não é atualizado automaticamente. É necessário atualizar manualmente cada serviço para o qual deseja usar a nova imagem.

Para obter mais informações, confira a documentação de referência da classe [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py).

## <a name="clean-up"></a>Limpar

Para excluir um serviço Web implantado, use `service.delete()`.

Para excluir uma imagem, use `image.delete()`.

Para excluir um modelo registrado, use `model.delete()`.

Para obter mais informações, confira a documentação de referência para [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--), [Image.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#delete--) e [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Próximas etapas

* [Proteger serviços Web do Azure Machine Learning com SSL](how-to-secure-web-service.md)
* [Consumir um modelo de ML implantado como um serviço Web](how-to-consume-web-service.md)
* [Como executar previsões em lotes](how-to-run-batch-predictions.md)
* [Monitorar seus modelos do Azure Machine Learning com o Application Insights](how-to-enable-app-insights.md)
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md)
* [SDK do Serviço do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Usar o Serviço do Azure Machine Learning com redes virtuais do Azure](how-to-enable-virtual-network.md)
