---
title: 'Início Rápido: usar o SDK do Python para criar um workspace do serviço do Machine Learning – Azure Machine Learning'
description: Comece a usar o Azure Machine Learning. Instale o SDK do Python e use-o para criar um workspace. Este espaço de trabalho é o bloco fundamental na nuvem para experimentação, treinamento e implantação de modelos de aprendizado de máquina com o serviço do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 09/24/2018
ms.openlocfilehash: cc348ca50b942b6b8b1474ed4dac4067d107a4af
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377992"
---
# <a name="quickstart-use-python-to-get-started-with-azure-machine-learning"></a>Início Rápido: usar Python para começar a usar o Azure Machine Learning

Neste início rápido, você usa o SDK do Azure Machine Learning para Python a fim de criar e usar um [workspace](concept-azure-machine-learning-architecture.md) do serviço do Machine Learning. Esse workspace é o bloco fundamental na nuvem para experimentação, treinamento e implantação de modelos de aprendizado de máquina com o serviço do Machine Learning.

Neste tutorial, você vai instalar o SDK do Python e:

* Criar um workspace na assinatura do Azure.
* Criar um arquivo de configuração para esse workspace que será usado posteriormente em outros scripts e notebooks.
* Escrever código que registra em log os valores dentro do espaço de trabalho.
* Exibir os valores registrados em log em seu workspace.

Neste início rápido, você criará um espaço de trabalho e um arquivo de configuração. Você pode usá-los como pré-requisitos para outros tutoriais de Machine Learning e artigos de instruções. Assim como em outros serviços do Azure, há limites e cotas associados ao Machine Learning. [Saiba mais sobre cotas e como solicitar mais.](how-to-manage-quotas.md)

Os seguintes recursos do Azure serão adicionados automaticamente ao workspace quando estiverem disponíveis regionalmente:
 
- [Registro de Contêiner do Azure](https://azure.microsoft.com/services/container-registry/)
- [Armazenamento do Azure](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Cofre da Chave do Azure](https://azure.microsoft.com/services/key-vault/)

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="install-the-sdk"></a>Instalar o SDK

*Se você usa uma máquina virtual de ciência de dados criada após 27 de setembro de 2018, ignore esta seção.* Essas máquinas virtuais de ciência de dados vem com o SDK de Python pré-instalado.

Antes de instalar o SDK, recomendamos que você crie um ambiente de Python isolado primeiro. Embora este início rápido use [Miniconda](https://conda.io/docs/user-guide/install/index.html), você também pode usar o [Anaconda](https://www.anaconda.com/) completo instalado ou o [Python virtualenv](https://virtualenv.pypa.io/en/stable/).

### <a name="install-miniconda"></a>Instalar o Miniconda


[Baixe](https://conda.io/miniconda.html) e instale o Miniconda. Escolha a versão do Python 3.7 ou posterior. Não selecione a versão do Python 2.x.

### <a name="create-an-isolated-python-environment"></a>Criar um ambiente isolado do Python 

Abra uma janela de linha de comando. Em seguida, crie um novo ambiente do Conda chamado `myenv` com o Python 3.6.

```sh
conda create -n myenv -y Python=3.6
```

Ative o ambiente.

  ```sh
  conda activate myenv
  ```

### <a name="install-the-sdk"></a>Instalar o SDK

No ambiente conda ativado, instale o SDK. Esse código instala os componentes principais do SDK do Machine Learning. Ele também instala um servidor do Jupyter Notebook no ambiente do Conda `myenv`. A instalação demora **aproximadamente quatro minutos** para ser concluída.

```sh
pip install azureml-sdk[notebooks]
```

## <a name="create-a-workspace"></a>Criar um espaço de trabalho

Para iniciar o Jupyter Notebook, digite este comando.
```sh
jupyter notebook
```

Na janela do navegador, crie um novo notebook usando o kernel `Python 3` padrão. 

Para exibir a versão do SDK, insira o código Python a seguir em uma célula do notebook e execute-o.

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

A execução do código acima pode disparar uma nova janela do navegador para que você entre na conta do Azure. Depois de entrar, o token de autenticação será armazenado no cache local.

Para ver detalhes do workspace, como o armazenamento, o registro de contêiner e o cofre de chaves associados, digite o código a seguir.

```python
ws.get_details()
```

## <a name="write-a-configuration-file"></a>Escrever um arquivo de configuração

Salve os detalhes do seu workspace em um arquivo de configuração no diretório atual. Esse arquivo é denominado “aml_config\config.json”.  

Esse arquivo de configuração do workspace facilita o carregamento do mesmo workspace mais tarde. Você pode carregá-lo com outros notebooks e scripts no mesmo diretório ou subdiretório. 

```python
# Create the configuration file.
ws.write_config()

# Use this code to load the workspace from 
# other scripts and notebooks in this directory.
# ws = Workspace.from_config()
```

A chamada à API `write_config()` cria o arquivo de configuração no diretório atual. O arquivo `config.json` contém o script a seguir.

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

# create a new experiment
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
Quando a execução for concluída, você poderá exibir o execução do experimento no portal do Azure. Use o código a seguir para imprimir uma URL para os resultados da última execução.

```python
print(run.get_portal_url())
```

Use o link para exibir os valores registrados no portal do Azure no seu navegador.

![Valores registrados em log no portal](./media/quickstart-create-workspace-with-python/logged-values.png)

## <a name="clean-up-resources"></a>Limpar recursos 
>[!IMPORTANT]
>Os recursos que você criou podem ser usados como pré-requisitos em outros tutoriais e artigos de instruções do Machine Learning.

Se você não planeja usar os recursos que criou aqui, exclua-os para não incorrer em encargos.

```python
ws.delete(delete_dependent_resources=True)
```

## <a name="next-steps"></a>Próximas etapas

Você criou os recursos necessários para experimentar e implantar modelos. Você também executou código em um notebook. E explorou o histórico de execução desse código em seu workspace na nuvem.

Você precisa de mais alguns pacotes em seu ambiente para usá-lo com os tutoriais do Machine Learning.

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
