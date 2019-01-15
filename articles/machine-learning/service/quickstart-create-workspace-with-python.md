---
title: 'Início Rápido: introdução ao Python'
titleSuffix: Azure Machine Learning service
description: Introdução ao Serviço do Azure Machine Learning no Python. Use o SDK do Python para criar um workspace, que é o bloco fundamental na nuvem para experimentação, treinamento e implantação de modelos de machine learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 2c8edd73a287d5bca2f3deb68448ba951b7a3367
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54106555"
---
# <a name="quickstart-use-the-python-sdk-to-get-started-with-azure-machine-learning"></a>Início Rápido: Usar o SDK do Python para começar a usar o Azure Machine Learning

Neste artigo, você usará o SDK do Azure Machine Learning para Python para criar e, em seguida, usará um [workspace](concept-azure-machine-learning-architecture.md) do Serviço do Azure Machine Learning. O workspace é o bloco fundamental na nuvem usado para experimentar, treinar e implantar modelos de machine learning com o Machine Learning. 

Comece configurando seu próprio ambiente do Python e o Servidor do Jupyter Notebook. Para executá-lo sem nenhuma instalação, confira [Início Rápido: usar o portal do Azure para começar a usar o Azure Machine Learning](quickstart-get-started.md).

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2G9N6]

Neste artigo você:
* Instalará o SDK do Python.
* Criar um workspace na assinatura do Azure.
* Criar um arquivo de configuração para esse workspace que será usado posteriormente em outros scripts e notebooks.
* Escrever código que registra em log os valores dentro do espaço de trabalho.
* Exibir os valores registrados em log em seu workspace.

Você criará um workspace e um arquivo de configuração para usá-los como pré-requisitos para outros tutoriais e artigos de instruções sobre o Machine Learning. Assim como ocorre em outros serviços do Azure, há limites e cotas associados ao Machine Learning. [Saiba mais sobre cotas e como solicitar mais.](how-to-manage-quotas.md)

Os seguintes recursos do Azure serão adicionados automaticamente ao workspace quando estiverem disponíveis regionalmente:
 
- [Registro de Contêiner do Azure](https://azure.microsoft.com/services/container-registry/)
- [Armazenamento do Azure](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Cofre da Chave do Azure](https://azure.microsoft.com/services/key-vault/)

Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Serviço do Azure Machine Learning](http://aka.ms/AMLFree) hoje mesmo.

## <a name="install-the-sdk"></a>Instalar o SDK

> [!IMPORTANT]
> Ignore esta seção se você usar uma máquina virtual de ciência de dados criada após 27 de setembro de 2018.
> As máquinas virtuais de ciência de dados criadas após essa data vêm com o SDK do Python pré-instalado.

O código deste artigo exige o SDK do Azure Machine Learning versão 1.0.2 ou posterior.

Antes de instalar o SDK, recomendamos que você crie um ambiente de Python isolado primeiro. Embora este artigo use o [Miniconda](https://conda.io/docs/user-guide/install/index.html), você também poderá usar o [Anaconda](https://www.anaconda.com/) completo instalado ou o [Python virtualenv](https://virtualenv.pypa.io/en/stable/).

### <a name="install-miniconda"></a>Instalar o Miniconda

[Baixe e instale o Miniconda](https://conda.io/miniconda.html). Selecione o Python 3.7 ou posterior. Não selecione o Python 2.x.

### <a name="create-an-isolated-python-environment"></a>Criar um ambiente isolado do Python 

1. Abra uma janela de linha de comando e, em seguida, crie um ambiente do Conda chamado *myenv* com o Python 3.6.

    ```shell
    conda create -n myenv -y Python=3.6
    ```

1. Ative o ambiente.

    ```shell
    conda activate myenv
    ```

### <a name="install-the-sdk"></a>Instalar o SDK

No ambiente conda ativado, instale o SDK. Esse código instala os componentes principais do SDK do Machine Learning. Ele também instala um servidor do Jupyter Notebook no ambiente do Conda. A instalação leva alguns minutos para ser concluída, dependendo da configuração do computador.

```shell
# Install Jupyter
conda install nb_conda

# Install the base SDK and Jupyter Notebook
pip install azureml-sdk[notebooks]
```

Use palavras-chave adicionais para instalar outros componentes do SDK:

```shell
# Install the base SDK and auto ml components
pip install azureml-sdk[automl]

# Install the base SDK and the model explainability component
pip install azureml-sdk[explain]

# Install the base SDK and experimental components
pip install azureml-sdk[contrib]
```

No ambiente do Azure Databricks, siga as [etapas de instalação do Databricks](how-to-configure-environment.md#azure-databricks
).


## <a name="create-a-workspace"></a>Criar um workspace

1. Para iniciar o Jupyter Notebook, insira este comando:

    ```shell
    jupyter notebook
    ```

1. Na janela do navegador, crie um novo notebook usando o kernel `Python 3` padrão. 

1. Para exibir a versão do SDK, insira e, em seguida, execute o seguinte código Python em uma célula do notebook:

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

1. Encontre um valor para o parâmetro `<azure-subscription-id>` na [lista de assinaturas no portal do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Use qualquer assinatura em que sua função seja proprietário ou colaborador.

   ```python
   from azureml.core import Workspace
   ws = Workspace.create(name='myworkspace',
                         subscription_id='<azure-subscription-id>', 
                         resource_group='myresourcegroup',
                         create_resource_group=True,
                         location='eastus2' # Or other supported Azure region   
                        )
   ```

   Ao executar o código, talvez você precise entrar em sua conta do Azure. Depois de entrar, o token de autenticação será armazenado no cache local.

1. Para exibir os detalhes do workspace, como o armazenamento, o registro de contêiner e o cofre de chaves associados, insira o seguinte código:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=getDetails)]


## <a name="write-a-configuration-file"></a>Escrever um arquivo de configuração

Salve os detalhes do workspace em um arquivo de configuração no diretório atual. Esse arquivo é chamado *aml_config\config.json*.  

A chamada à API `write_config()` cria o arquivo de configuração no diretório atual. O arquivo *config.json* contém o seguinte:

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

Esse arquivo de configuração do workspace facilita o carregamento do mesmo workspace mais tarde. Você pode carregá-lo com outros notebooks e scripts no mesmo diretório ou subdiretório. 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]



## <a name="use-the-workspace"></a>Usar o workspace

Escreva um código que usa as APIs básicas do SDK para acompanhar as execuções de experimento.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=useWs)]

## <a name="view-logged-results"></a>Exibir os valores registrados em log
Quando a execução for concluída, você poderá exibir o execução do experimento no portal do Azure. Para imprimir uma URL que direciona você aos resultados da última execução, use o seguinte código:

```python
print(run.get_portal_url())
```

Use o link para exibir os valores registrados no portal do Azure no seu navegador.

![Valores registrados em log no portal do Azure](./media/quickstart-create-workspace-with-python/logged-values.png)

## <a name="clean-up-resources"></a>Limpar recursos 
>[!IMPORTANT]
>Use os recursos criados aqui como pré-requisitos para outros tutoriais e artigos de instruções sobre o Machine Learning.

Se você não pretende usar os recursos criados neste artigo, exclua-os para evitar a geração de encargos.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=delete)]

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou os recursos necessários para experimentar e implantar modelos. Você executou um código em um notebook e explorou o histórico de execuções desse código no workspace na nuvem.

Para usar o código com os tutoriais sobre o Machine Learning, você precisará de mais alguns pacotes em seu ambiente.

1. No seu navegador, feche o notebook.
1. Na janela de linha de comando, selecione Ctrl+C para interromper o servidor do Jupyter Notebook.
1. Instale os pacotes adicionais.  Se você não instalou o `azureml-sdk[automl]` acima, faça isso agora.

    ```shell
    conda install -y cython matplotlib scikit-learn pandas numpy
    pip install azureml-sdk[automl]
    ```

Depois de instalar esses pacotes, continue com os tutoriais para treinar e implantar um modelo. 

> [!div class="nextstepaction"]
> [Tutorial: treinar um modelo de classificação de imagem](tutorial-train-models-with-aml.md)

Você também pode explorar [exemplos mais avançados no GitHub](https://aka.ms/aml-notebooks).
