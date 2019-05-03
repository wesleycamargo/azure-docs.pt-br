---
title: Guia de solução de problemas de implantação
titleSuffix: Azure Machine Learning service
description: Saiba como contornar, resolver e solucionar erros comuns de implantação do Docker com o AKS e ACI usando o serviço de Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 05/02/2018
ms.custom: seodec18
ms.openlocfilehash: 90e85e0030a696dd024dd65d27a0f4dbdc7e3cdc
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65023667"
---
# <a name="troubleshooting-azure-machine-learning-service-aks-and-aci-deployments"></a>Solucionando problemas de implantações de serviço AKS e ACI do Azure Machine Learning

Aprenda a contornar ou resolver erros comuns de implantação do Docker com instâncias de contêiner do Azure (ACI) e o Azure o AKS (serviço) usando o serviço de Azure Machine Learning.

Ao implantar um modelo de serviço do Azure Machine Learning, o sistema executa uma série de tarefas. As tarefas de implantação são:

1. Registre o modelo no Registro de modelo do workspace.

2. Crie uma imagem do Docker, incluindo:
    1. Baixe o modelo registrado do Registro. 
    2. Crie um dockerfile, com um ambiente Python baseado nas dependências especificadas no arquivo yaml do ambiente.
    3. Adicione seus arquivos de modelo e o script de pontuação que você fornece no dockerfile.
    4. Crie uma nova imagem do Docker usando o dockerfile.
    5. Registre a imagem do Docker com o Registro de Contêiner do Azure associado com o workspace.

    > [!IMPORTANT]
    > Dependendo do seu código, criação de imagem ocorrem automaticamente sem a sua entrada.

3. Implante a imagem do Docker para o serviço ACI (Instância de Contêiner do Azure) ou o AKS (Serviço de Kubernetes do Azure).

4. Inicialize um novo contêiner (ou contêineres) em ACI ou AKS. 

Saiba mais sobre esse processo na introdução a [Gerenciamento de Modelos](concept-model-management-and-deployment.md).

## <a name="before-you-begin"></a>Antes de começar

Se você tiver qualquer problema, a primeira medida a adotar é dividir a tarefa de implantação (descrita anteriormente) em etapas individuais para isolar o problema.

Dividir a implantação em tarefas é útil se você estiver usando o [Webservice.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) API, ou [Webservice.deploy_from_model()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-) API, como ambas as funções, execute as etapas mencionadas anteriormente como um única ação. Normalmente, essas APIs são convenientes, mas é útil para dividir as etapas na solução de problemas, substituindo-os com o abaixo de chamadas à API.

1. Registre o modelo. Aqui está um exemplo de código:

    ```python
    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Crie a imagem. Aqui está um exemplo de código:

    ```python
    # configure the image
    image_config = ContainerImage.image_configuration(runtime="python",
                                                      execution_script="score.py",
                                                      conda_file="myenv.yml")

    # create the image
    image = Image.create(name='myimg', models=[model], image_config=image_config, workspace=ws)

    # wait for image creation to finish
    image.wait_for_creation(show_output=True)
    ```

3. Implante a imagem como serviço. Aqui está um exemplo de código:

    ```python
    # configure an ACI-based deployment
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)

    aci_service = Webservice.deploy_from_image(deployment_config=aci_config, 
                                               image=image, 
                                               name='mysvc', 
                                               workspace=ws)
    aci_service.wait_for_deployment(show_output=True)    
    ```

Depois de termos dividido o processo de implantação em tarefas individuais, podemos ver alguns dos erros mais comuns.

## <a name="image-building-fails"></a>Falhas na criação da imagem

Se a imagem do Docker não pode ser criada, o [image.wait_for_creation()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-) ou [service.wait_for_deployment()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#wait-for-deployment-show-output-false-) chamada falhar com algumas mensagens de erro que podem oferecer algumas dicas. Você também pode encontrar mais detalhes sobre os erros no log de build da imagem. Abaixo está um código de exemplo mostrando como descobrir o URI do log de build da imagem.

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images.items():
    print (img.name, img.version, img.image_build_log_uri)
```

O URI do log de imagem é uma URL SAS que aponta para um arquivo de log armazenado no Armazenamento de Blobs do Azure. Basta copiar e colar o URI em uma janela do navegador para poder baixar e exibir o arquivo de log.

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Política de acesso do Cofre de chaves do Azure e modelos do Azure Resource Manager

A compilação de imagem também pode falhar devido a um problema com a política de acesso no cofre de chaves do Azure. Essa situação pode ocorrer quando você usa um modelo do Azure Resource Manager para criar o espaço de trabalho e recursos associados (incluindo o Azure Key Vault), várias vezes. Por exemplo, usando o modelo várias vezes com os mesmos parâmetros como parte de um pipeline de implantação e integração contínua.

A maioria das operações de criação de recursos por meio de modelos são idempotentes, mas o Cofre de chaves limpa as políticas de acesso de cada vez que o modelo é usado. Limpando o acesso de quebras políticas de acesso para o Cofre de chaves para qualquer espaço de trabalho existente que está sendo usado. Essa condição resulta em erros ao tentar criar novas imagens. A seguir estão exemplos dos erros que você pode receber:

__Portal__:
```text
Create image "myimage": An internal server error occurred. Please try again. If the problem persists, contact support.
```

__SDK__:
```python
image = ContainerImage.create(name = "myimage", models = [model], image_config = image_config, workspace = ws)
Creating image
Traceback (most recent call last):
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 341, in create
    resp.raise_for_status()
  File "C:\Python37\lib\site-packages\requests\models.py", line 940, in raise_for_status
    raise HTTPError(http_error_msg, response=self)
requests.exceptions.HTTPError: 500 Server Error: Internal Server Error for url: https://eastus.modelmanagement.azureml.net/api/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>/images?api-version=2018-11-19

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 346, in create
    'Content: {}'.format(resp.status_code, resp.headers, resp.content))
azureml.exceptions._azureml_exception.WebserviceException: Received bad response from Model Management Service:
Response Code: 500
Headers: {'Date': 'Tue, 26 Feb 2019 17:47:53 GMT', 'Content-Type': 'application/json', 'Transfer-Encoding': 'chunked', 'Connection': 'keep-alive', 'api-supported-versions': '2018-03-01-preview, 2018-11-19', 'x-ms-client-request-id': '3cdcf791f1214b9cbac93076ebfb5167', 'x-ms-client-session-id': '', 'Strict-Transport-Security': 'max-age=15724800; includeSubDomains; preload'}
Content: b'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}'
```

__CLI__:
```text
ERROR: {'Azure-cli-ml Version': None, 'Error': WebserviceException('Received bad response from Model Management Service:\nResponse Code: 500\nHeaders: {\'Date\': \'Tue, 26 Feb 2019 17:34:05
GMT\', \'Content-Type\': \'application/json\', \'Transfer-Encoding\': \'chunked\', \'Connection\': \'keep-alive\', \'api-supported-versions\': \'2018-03-01-preview, 2018-11-19\', \'x-ms-client-request-id\':
\'bc89430916164412abe3d82acb1d1109\', \'x-ms-client-session-id\': \'\', \'Strict-Transport-Security\': \'max-age=15724800; includeSubDomains; preload\'}\nContent:
b\'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}\'',)}
```

Para evitar esse problema, recomendamos que uma das seguintes abordagens:

* Não implante o modelo mais de uma vez para os mesmos parâmetros. Ou exclua os recursos existentes antes de usar o modelo para recriá-los.
* Examinar as políticas de acesso do Cofre de chaves e, em seguida, use essas políticas para definir o `accessPolicies` propriedade do modelo.
* Verifique se o recurso de Cofre de chaves já existe. Se isso acontecer, não recriá-lo por meio do modelo. Por exemplo, adicione um parâmetro que permite que você desabilite a criação do recurso Cofre de chaves, se ele já existe.

## <a name="debug-locally"></a>Depurar localmente

Se você encontrar problemas ao implantar um modelo no ACI ou AKS, tente implantá-lo como um serviço da web local. Usando um serviço da web local torna mais fácil solucionar problemas. A imagem do Docker que contém o modelo é baixada e iniciada no seu sistema local.

> [!IMPORTANT]
> Implantações de serviço da web local requerem uma instalação do Docker em seu sistema local de trabalho. Docker deve estar em execução antes de implantar um serviço web local. Para obter informações sobre como instalar e usar o Docker, consulte [ https://www.docker.com/ ](https://www.docker.com/).

> [!WARNING]
> Não há suporte para implantações de serviço da web local para cenários de produção.

Para implantar localmente, modificar seu código para usar `LocalWebservice.deploy_configuration()` para criar uma configuração de implantação. Em seguida, use `Model.deploy()` para implantar o serviço. O exemplo a seguir implanta um modelo (contido no `model` variável) como um serviço da web local:

```python
from azureml.core.model import InferenceConfig
from azureml.core.webservice import LocalWebservice

# Create inferencing configuration. This creates a docker image that contains the model.
inference_config = InferenceConfig(runtime= "python", 
                                   execution_script="score.py",
                                   conda_file="myenv.yml")

# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

Neste ponto, você pode trabalhar com o serviço como de costume. Por exemplo, o código a seguir demonstra o envio de dados para o serviço:

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

test_sample = bytes(test_sample,encoding = 'utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

### <a name="update-the-service"></a>Atualizar o serviço

Durante o teste local, talvez você precise atualizar o `score.py` arquivo para adicionar o registro em log ou tentar resolver os problemas que você descobriu. Para recarregar as alterações para o `score.py` do arquivo, use `reload()`. Por exemplo, o código a seguir recarrega o script para o serviço e, em seguida, envia dados para ele. Os dados serão pontuados usando atualizada `score.py` arquivo:

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> O script é recarregado do local especificado pelo `InferenceConfig` objeto usado pelo serviço.

Para alterar o modelo, as dependências de Conda ou configuração de implantação, use [Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-). O exemplo a seguir atualiza o modelo usado pelo serviço:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Excluir o serviço

Para excluir o serviço, use [Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a id="dockerlog"></a> Inspecionar o log do Docker

Você pode imprimir mensagens de log do mecanismo do Docker detalhadas do objeto de serviços. Você pode exibir o log de implantações locais de ACI e AKS. O exemplo a seguir demonstra como imprimir os logs.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>Falhas na inicialização do serviço

Depois que a imagem é criada com êxito, o sistema tenta iniciar um contêiner usando a sua configuração de implantação. Como parte do processo de inicialização do contêiner, a função `init()` em seu script de pontuação é invocada pelo sistema. Se houver exceções não capturadas na função `init()`, você poderá ver o erro **CrashLoopBackOff** na mensagem de erro.

Use as informações na [inspecionar o log do Docker](#dockerlog) seção verificar os logs.

## <a name="function-fails-getmodelpath"></a>Falha de função: get_model_path()

Muitas vezes, nos `init()` função no script de pontuação [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) função é chamada para localizar um arquivo de modelo ou uma pasta de arquivos de modelo no contêiner. Se o arquivo de modelo ou a pasta não for encontrada, a função falhará. A maneira mais fácil para depurar esse erro é executar o código do Python no shell do contêiner abaixo:

```python
import logging
logging.basicConfig(level=logging.DEBUG)
from azureml.core.model import Model
print(Model.get_model_path(model_name='my-best-model'))
```

Este exemplo imprime o caminho local (relativo ao `/var/azureml-app`) no contêiner em que o seu script de pontuação está esperando para localizar o arquivo de modelo ou a pasta. Em seguida, você pode verificar se o arquivo ou a pasta realmente está no local que deveria.

Definir o nível de log como depuração, informações adicionais a serem registrados, que podem ser útil para identificar a falha pode causar.

## <a name="function-fails-runinputdata"></a>Falha de função: run(input_data)

Se o serviço for implantado com êxito, mas falhar quando você publicar dados no ponto de extremidade de pontuação, você poderá adicionar o erro capturando instrução na função `run(input_data)` de modo que ele retorne a mensagem de erro detalhada em vez disso. Por exemplo: 

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```

**Observação**: O retorno de mensagens de erro da chamada `run(input_data)` deve ser feito para apenas para fins de depuração. Por motivos de segurança, você não deve retornar mensagens de erro dessa forma em um ambiente de produção.

## <a name="http-status-code-503"></a>Código de status HTTP 503

Implantações de Kubernetes Service do Azure dão suporte a dimensionamento automático, que permite que as réplicas a ser adicionado para dar suporte à carga adicional. No entanto, o dimensionador automático foi projetado para tratar **gradual** as alterações na carga. Se você receber grandes picos nas solicitações por segundo, os clientes podem receber um código de status HTTP 503.

Há duas coisas que podem ajudar a impedir que os códigos de status 503:

* Alterar o nível de utilização em quais dimensionamento automático cria novas réplicas.
    
    Por padrão, a utilização de destino de dimensionamento automático é definida como 70%, o que significa que o serviço pode lidar com picos de solicitações por segundo (RPS) de até 30%. Você pode ajustar a meta de utilização, definindo o `autoscale_target_utilization` para um valor mais baixo.

    > [!IMPORTANT]
    > Essa alteração não faz com que as réplicas a ser criado *com mais rapidez*. Em vez disso, eles são criados em um limite inferior de utilização. Em vez de esperar até que o serviço seja 70% de utilização, alterando o valor como 30% faz com que as réplicas a ser criado quando ocorre 30% da utilização.
    
    Se o serviço web já está usando as réplicas máximo atuais e você ainda estiver vendo os códigos de status 503, aumente o `autoscale_max_replicas` valor para aumentar o número máximo de réplicas.

* Altere o número mínimo de réplicas. Aumentar as réplicas mínimas fornece um pool maior para lidar com os picos de entrada.

    Para aumentar o número mínimo de réplicas, defina `autoscale_min_replicas` para um valor mais alto. Você pode calcular as réplicas necessárias usando o código a seguir, substituindo os valores pelos valores específicos ao seu projeto:

    ```python
    from math import ceil
    # target requests per second
    targetRps = 20
    # time to process the request (in seconds)
    reqTime = 10
    # Maximum requests per container
    maxReqPerContainer = 1
    # target_utilization. 70% in this example
    targetUtilization = .7

    concurrentRequests = targetRps * reqTime / targetUtilization

    # Number of container replicas
    replicas = ceil(concurrentRequests / maxReqPerContainer)
    ```

    > [!NOTE]
    > Se você receber maior do que as novas réplicas mínimo podem lidar com picos de solicitação, você pode receber 503s ao novamente. Por exemplo, como o tráfego para seu serviço aumenta, você talvez precise aumentar as réplicas mínimo.

Para obter mais informações sobre a configuração `autoscale_target_utilization`, `autoscale_max_replicas`, e `autoscale_min_replicas` , consulte o [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) referência de módulo.


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a implantação:

* [Como e onde implantar](how-to-deploy-and-where.md)
* [Tutorial: Treinar e implantar modelos](tutorial-train-models-with-aml.md)
