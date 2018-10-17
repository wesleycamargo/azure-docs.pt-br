---
title: 'Início Rápido: usar o SDK do Python para criar um workspace do serviço do Machine Learning – Azure Machine Learning'
description: Comece a usar o Azure Machine Learning.  Instale o SDK do Python e use-o para criar um workspace. Este workspace é o bloco fundamental na nuvem para experimentação, treinamento e implantação de modelos de aprendizado de máquina com o serviço de Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 09/24/2018
ms.openlocfilehash: ee24c1797d0f52d2529ed583a0cfe90cc9e27035
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49067747"
---
# <a name="quickstart-use-python-to-get-started-with-azure-machine-learning"></a>Início Rápido: usar Python para começar a usar o Azure Machine Learning

Neste início rápido, você usará o SDK do Azure Machine Learning para Python para criação e, em seguida, usará um [workspace](concept-azure-machine-learning-architecture.md) do serviço do Machine Learning. Este workspace é o bloco fundamental para experimentação, treinamento e implantação de modelos de aprendizado de máquina com o serviço do Azure Machine Learning.

Neste tutorial, você instalará o SDK do Python e:
* Criar um workspace na sua assinatura do Azure
* Criará um arquivo de configuração para esse workspace para ser usado posteriormente em outros scripts e notebooks
* Escreverá código que registra em log os valores dentro do workspace
* Exibir os valores registrados em log no seu workspace

O workspace e o arquivo de configuração que você criar neste Início Rápido poderão ser usados como pré-requisitos em outros tutoriais e artigos de instruções do Azure Machine Learning. Assim como em outros serviços do Azure, há limites e cotas associadas ao serviço do Azure Machine Learning. [Saiba mais sobre cotas e como solicitar mais.](how-to-manage-quotas.md)

Para sua conveniência, os seguintes recursos do Azure são adicionados automaticamente ao seu workspace quando regionalmente disponíveis: [Registro de Contêiner](https://azure.microsoft.com/services/container-registry/), [Armazenamento](https://azure.microsoft.com/services/storage/), [Application Insights](https://azure.microsoft.com/services/application-insights/) e [Key Vault](https://azure.microsoft.com/services/key-vault/).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


##  <a name="install-the-sdk"></a>Instalar o SDK

**Ignore esta seção se você estiver usando** uma DSVM (Máquina Virtual de Ciência de Dados) criada após 27 de setembro de 2018, já que essas DSVMs vêm com o SDK de Python pré-instalado.

Antes de instalar o SDK, é recomendável que você crie um ambiente de Python isolado primeiro. Embora este início rápido use [Miniconda](https://conda.io/docs/user-guide/install/index.html), você também pode usar o [Anaconda](https://www.anaconda.com/) completo instalado ou o [Python virtualenv](https://virtualenv.pypa.io/en/stable/).

### <a name="install-miniconda"></a>Instalar o Miniconda


[Baixe](https://conda.io/miniconda.html) e instale o Miniconda. Escolha a versão do Python 3.7 ou posterior. Não escolha a versão do Python 2. x.

### <a name="create-an-isolated-python-environment"></a>Criar um ambiente isolado do Python 

Inicie uma janela de linha de comando e crie um novo ambiente conda chamado `myenv` com o Python 3.6.

```sh
conda create -n myenv -y Python=3.6
```

Ative o ambiente.

  ```sh
  conda activate myenv
  ```

### <a name="install-the-sdk"></a>Instalar o SDK

No ambiente conda ativado, instale o SDK. Esse código instala os componentes principais do SDK do Azure Machine Learning, bem como um servidor do Jupyter Notebook no ambiente conda `myenv`.  Este procedimento leva **cerca de 4 minutos** para ser concluído.

```sh
pip install azureml-sdk[notebooks]
```

## <a name="create-a-workspace"></a>Criar um espaço de trabalho

Inicie o Jupyter Notebook digitando este comando.
```sh
jupyter notebook
```

Na janela do navegador, crie um novo notebook usando o kernel `Python 3` padrão. 

Exiba a versão do SDK digitando o seguinte código Python em uma célula do notebook e execute-a.

```python
import azureml.core
print(azureml.core.VERSION)
```

Crie um novo grupo de recursos do Azure e um novo workspace.

Localize um valor para `<azure-subscription-id>` na [lista de assinaturas no portal do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Use qualquer assinatura em que sua função seja proprietário ou colaborador.

```python
from azureml.core import Workspace
ws = Workspace.create(name='myworkspace',
                      subscription_id='<azure-subscription-id>',
                      resource_group='myresourcegroup',
                      create_resource_group=True,
                      location='eastus2' # or other supported Azure region
                     )
```

Executar o código anterior pode disparar uma nova janela do navegador para que você entre na sua conta do Azure. Depois de entrar, o token de autenticação será armazenado em cache local.

Para ver os detalhes do workspace, incluindo o armazenamento associado, o registro de contêiner e o cofre de chaves, digite:

```python
ws.get_details()
```

## <a name="write-a-configuration-file"></a>Escrever um arquivo de configuração

Salve os detalhes do seu workspace em um arquivo de configuração no diretório atual. Esse arquivo é denominado “aml_config\config.json”.  

Esse arquivo de configuração do workspace facilita o carregamento desse mesmo workspace mais tarde com outros notebooks e scripts no mesmo diretório ou subdiretório. 

```python
# Create the configuration file.
ws.write_config()

# Use this code to load the workspace from 
# other scripts and notebooks in this directory.
# ws = Workspace.from_config()
```

A chamada à API `write_config()` cria o arquivo de configuração no diretório atual. O arquivo `config.json` contém o seguinte:

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

## <a name="use-the-workspace"></a>Usar o workspace

Escreva um código que usa as APIs básicas do SDK para acompanhar as execuções de experimento.

```python
from azureml.core import Experiment

# create a new experiemnt
exp = Experiment(workspace=ws, name='myexp')

# start a run
run = exp.start_logging()

# log a number
run.log('my magic number', 42)

# log a list (Fibonacci numbers)
run.log_list('my list', [1, 1, 2, 3, 5, 8, 13, 21, 34, 55]) 

# finish the run
run.complete()
```

## <a name="view-logged-results"></a>Exibir os valores registrados em log
Quando a execução for concluída, você pode exibir o execução do experimento no portal do Azure. Use o código a seguir para imprimir uma URL para os resultados da última execução.

```python
print(run.get_portal_url())
```

Use o link para exibir os valores registrados no portal do Azure no seu navegador.

![valores registrados no portal](./media/quickstart-create-workspace-with-python/logged-values.png)

## <a name="clean-up-resources"></a>Limpar recursos 
>[!IMPORTANT]
>Os recursos que você criou podem ser usados como pré-requisitos em outros tutoriais e artigos de instruções do Azure Machine Learning.

Se você não pretender usar o que criou aqui, exclua os recursos criados com este início rápido para não incorrer em encargos.

```python
ws.delete(delete_dependent_resources=True)
```

## <a name="next-steps"></a>Próximas etapas

Agora você criou os recursos necessários para começar a experimentar e a implantar modelos. Você também executou um código em um notebook e explorou o histórico de execuções desse o código no seu workspace na nuvem.

Você precisará de mais alguns pacotes em seu ambiente para usá-lo com os tutoriais do Azure Machine Learning:

1. No seu navegador, feche o notebook.
1. Na janela de linha de comando, use `Ctrl`+`C` para parar o servidor do notebook.
1. Instalar pacotes adicionais.

    ```sh
    conda install -y cython matplotlib scikit-learn pandas numpy
    pip install azureml-sdk[automl]
    ```

Depois de instalar esses pacotes, siga os tutoriais para treinar e implantar um modelo.  

> [!div class="nextstepaction"]
> [Tutorial: Treinar um modelo de classificação de imagem](tutorial-train-models-with-aml.md)

Você também pode explorar [exemplos mais avançados no GitHub](https://aka.ms/aml-notebooks).
