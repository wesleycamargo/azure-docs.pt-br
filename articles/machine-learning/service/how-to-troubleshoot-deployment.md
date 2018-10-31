---
title: Guia de solução de problemas de implantação para o serviço do Azure Machine Learning
description: Saiba como aplicar uma solução alternativa, resolver e solucionar erros comuns de implantação do Docker com o serviço Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: haining
author: hning86
ms.reviewer: jmartens
ms.date: 10/01/2018
ms.openlocfilehash: a10b05e95fa719b80775191e48bd4117e3a785fd
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49321675"
---
# <a name="troubleshooting-azure-machine-learning-service-deployments"></a>Solucionando problemas de implantações de serviço do Azure Machine Learning

Neste artigo, você aprenderá a contornar ou resolver erros comuns de implantação do Docker com o serviço Azure Machine Learning.

Ao implantar um modelo de serviço do Azure Machine Learning, o sistema executa uma série de tarefas. Essa é uma sequência complexa de eventos e, às vezes, surgem problemas. As tarefas de implantação são:

1. Registre o modelo no Registro de modelo do workspace.

2. Crie uma imagem do Docker, incluindo:
    1. Baixe o modelo registrado do Registro. 
    2. Crie um dockerfile, com um ambiente Python baseado nas dependências especificadas no arquivo yaml do ambiente.
    3. Adicione seus arquivos de modelo e o script de pontuação que você fornece no dockerfile.
    4. Crie uma nova imagem do Docker usando o dockerfile.
    5. Registre a imagem do Docker com o Registro de Contêiner do Azure associado com o workspace.

3. Implante a imagem do Docker para o serviço ACI (Instância de Contêiner do Azure) ou o AKS (Serviço de Kubernetes do Azure).

4. Inicialize um novo contêiner (ou contêineres) em ACI ou AKS. 

Saiba mais sobre esse processo na introdução a [Gerenciamento de Modelos](concept-model-management-and-deployment.md).

## <a name="before-you-begin"></a>Antes de começar

Se você tiver qualquer problema, a primeira medida a adotar é dividir a tarefa de implantação (descrita anteriormente) em etapas individuais para isolar o problema. 

Isso é particularmente útil se você está usando a API `Webservice.deploy` ou a API `Webservice.deploy_from_model`, já que essas funções agrupam as etapas mencionadas anteriormente em uma única ação. Normalmente, essas APIs são bastante convenientes, mas é útil dividir as etapas na solução de problemas, substituindo-as pelas chamadas à API abaixo.

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
Se o sistema não puder compilar a imagem do Docker, a chamada `image.wait_for_creation()` falhará com algumas mensagens de erro que podem oferecer algumas dicas. Você também pode encontrar mais detalhes sobre os erros no log de build da imagem. Abaixo está um código de exemplo mostrando como descobrir o URI do log de build da imagem.

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images()['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images().items():
    print (img.name, img.version, img.image_build_log_uri)
```
O URI do log de imagem é uma URL SAS que aponta para um arquivo de log armazenado no Armazenamento de Blobs do Azure. Basta copiar e colar o URI em uma janela do navegador para poder baixar e exibir o arquivo de log.


## <a name="service-launch-fails"></a>Falhas na inicialização do serviço
Depois que a imagem é criada com êxito, o sistema tenta iniciar um contêiner no ACI ou AKS, dependendo da sua configuração de implantação. Geralmente, é recomendável tentar uma implantação de ACI primeiro, pois é uma implantação mais simples de contêiner único. Dessa forma, você pode descartar qualquer problema específico de AKS.

Como parte do processo de inicialização do contêiner, a função `init()` em seu script de pontuação é invocada pelo sistema. Se houver exceções não capturadas na função `init()`, você poderá ver o erro **CrashLoopBackOff** na mensagem de erro. Abaixo estão algumas dicas para ajudá-lo a solucionar o problema.

### <a name="inspect-the-docker-log"></a>Inspecionar o log do Docker
Você pode imprimir mensagens de log do mecanismo do Docker detalhadas do objeto de serviços.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices()['mysvc'].get_logs())
```

### <a name="debug-the-docker-image-locally"></a>Depurar a imagem do Docker localmente
Algumas vezes, o log do Docker não emite informações suficientes sobre o que está errado. Você pode ir além e obter a imagem do Docker interna, iniciar um contêiner local e depurar diretamente dentro do contêiner ao vivo de modo interativo. Para iniciar um contêiner local, você deve ter um mecanismo do Docker em execução localmente, e seria muito mais fácil se você também tivesse a [azure-cli](/cli/azure/install-azure-cli?view=azure-cli-latest) instalada.

Primeiro, precisamos descobrir a localização da imagem:

```python
# print image location
print(image.image_location)
```

O local da imagem tem este formato: `<acr-name>.azurecr.io/<image-name>:<version-number>`, como `myworkpaceacr.azurecr.io/myimage:3`. 

Agora vá até a janela de linha de comando. Se você tiver a azure-cli instalada, poderá digitar os comandos a seguir para entrar no ACR (Registro de Contêiner do Azure) associado ao workspace em que a imagem está armazenada. 

```sh
# log on to Azure first if you haven't done so before
$ az login

# make sure you set the right subscription in case you have access to multiple subscriptions
$ az account set -s <subscription_name_or_id>

# now let's log in to the workspace ACR
# note the acr-name is the domain name WITHOUT the ".azurecr.io" postfix
# e.g.: az acr login -n myworkpaceacr
$ az acr login -n <acr-name>
```
Se você não tiver a azure-cli instalada, poderá usar o comando `docker login` para fazer logon no ACR. Mas você precisará recuperar o nome de usuário e a senha do ACR no portal do Azure primeiro.

Depois de fazer logon no ACR, você pode obter a imagem do Docker e iniciar um contêiner localmente e, em seguida, inicializar uma sessão Bash para depuração usando o comando `docker run`:

```sh
# note the image_id is <acr-name>.azurecr.io/<image-name>:<version-number>
# for example: myworkpaceacr.azurecr.io/myimage:3
$ docker run -it <image_id> /bin/bash
```

Depois de inicializar uma sessão Bash para o contêiner em execução, você pode encontrar seus scripts de pontuação na pasta `/var/azureml-app`. Em seguida, você pode inicializar uma sessão do Python para depurar seus scripts de pontuação. 

```sh
# enter the directory where scoring scripts live
cd /var/azureml-app

# find what Python packages are installed in the python environment
pip freeze

# sanity-check on score.py
# you might want to edit the score.py to trigger init().
# as most of the errors happen in init() when you are trying to load the model.
python score.py
```
Caso você precise de um editor de texto para modificar seus scripts, poderá instalar o editor vim, nano, Emacs ou outro de sua preferência.

```sh
# update package index
apt-get update

# install a text editor of your choice
apt-get install vim
apt-get install nano
apt-get install emacs

# launch emacs (for example) to edit score.py
emacs score.py

# exit the container bash shell
exit
```

Você também pode iniciar o serviço Web localmente e enviar o tráfego HTTP para ele. O servidor Flask no contêiner do Docker está em execução na porta 5001. Você pode mapear para qualquer outra porta disponível no computador host.
```sh
# you can find the scoring API at: http://localhost:8000/score
$ docker run -p 8000:5001 <image_id>
```

## <a name="function-fails-getmodelpath"></a>Falha de função: get_model_path()
Geralmente, na função `init()` no script de pontuação, a função `Model.get_model_path()` é chamada para localizar um arquivo de modelo ou uma pasta de arquivos de modelo no contêiner. Isso geralmente será uma fonte de falha se o arquivo de modelo ou a pasta não puder ser localizado. A maneira mais fácil para depurar esse erro é executar o código do Python no shell do contêiner abaixo:

```python
from azureml.core.model import Model
print(Model.get_model_path(model_name='my-best-model'))
```

Isso imprime o caminho local (relativo ao `/var/azureml-app`) no contêiner em que o seu script de pontuação está esperando encontrar o arquivo ou a pasta de modelo. Em seguida, você pode verificar se o arquivo ou a pasta realmente está no local que deveria.


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
**Observação**: o retorno de mensagens de erro da chamada `run(input_data)` deve ser feito para apenas para fins de depuração. Pode não ser uma boa ideia fazer isso em um ambiente de produção por motivos de segurança.


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a implantação: 
* [Como implantar nas ACI](how-to-deploy-to-aci.md)

* [Como implantar no AKS](how-to-deploy-to-aks.md)

* [Tutorial parte 1: treinar modelo](tutorial-train-models-with-aml.md)

* [Tutorial parte 2: implantar o modelo](tutorial-deploy-models-with-aml.md)
