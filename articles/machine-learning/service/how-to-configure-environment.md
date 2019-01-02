---
title: Configurar o ambiente de desenvolvimento do Python
titleSuffix: Azure Machine Learning service
description: Aprenda a configurar um ambiente de desenvolvimento ao trabalhar com o serviço de Aprendizado de Máquina do Azure. Neste documento, aprenda como usar os ambientes Conda, crie arquivos de configuração e configure os Jupyter Notebooks, os Azure Notebooks, os IDEs, os editores de código e a Data Science Virtual Machine.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.component: core
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 59f847dc38cddfd9185cfd169cf1ef34c744f8f7
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53192627"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Configurar um ambiente de desenvolvimento para Azure Machine Learning

Neste documento, aprenda a configurar seu ambiente de desenvolvimento para trabalhar com o serviço do Azure Machine Learning. O serviço do Azure Machine Learning é independente da plataforma. Os únicos requisitos para seu ambiente de desenvolvimento são ter o __Python 3__, o __Conda__ (para ambientes isolados) e um arquivo de configuração que contém as informações do Workspace do Azure Machine Learning.

Este documento tem como foco as seguintes ferramentas e ambientes específicos:

* [Azure Notebooks](#aznotebooks): Um serviço Jupyter Notebooks hospedado na nuvem do Azure. É a maneira __mais fácil__ de começar, pois o SDK do Azure Machine Learning já está instalado.
* [A Máquina Virtual de Ciência de Dados](#dsvm): Um __ambiente de desenvolvimento/experimentação pré-configurado__ na nuvem do Azure __projetado para o trabalho de ciência de dados__ e que pode ser implantado em instâncias de VM somente de CPU ou em instâncias baseadas em GPU. O Python 3, o Conda, o Jupyter Notebooks e o SDK do Azure Machine Learning já estão instalados. A VM é fornecida com estruturas, ferramentas e editores de ML/aprendizado profundo populares para desenvolver soluções de ML. Provavelmente, trata-se do ambiente de desenvolvimento __mais completo__ para ML na plataforma do Azure.
* [Jupyter Notebooks](#jupyter): Se você já estiver usando o Jupyter Notebooks, o SDK terá alguns recursos extras que você deverá instalar.
* [Visual Studio Code](#vscode): Se você usa o Visual Studio Code, há algumas extensões úteis que podem ser instaladas.
* [Azure Databricks](#aml-databricks): Uma plataforma de análise de dados popular baseada no Apache Spark. Saiba como obter o SDK do Azure Machine Learning para seu cluster, de modo que você possa implantar modelos.

Se você já tiver um ambiente de Python 3 ou desejar ver apenas as etapas básicas para instalar o SDK, confira a seção [Computador local](#local).

## <a name="prerequisites"></a>Pré-requisitos

- Um workspace de serviço do Azure Machine Learning. Siga as etapas em [Introdução ao serviço do Azure Machine Learning](quickstart-get-started.md) para criar um.

- O gerenciador de pacotes [Continuum Anaconda](https://www.anaconda.com/download/) ou [Miniconda](https://conda.io/miniconda.html).

    > [!IMPORTANT]
    > Anaconda e Miniconda não são necessários ao usar Azure Notebooks.

- No Linux ou no Mac OS, você precisa ter o shell do Bash.

    > [!TIP]
    > Se estiver no Linux ou no Mac OS e usar um shell diferente do Bash (por exemplo, zsh), você poderá receber erros ao executar alguns comandos. Para contornar esse problema, use o comando `bash` para iniciar um novo shell do Bash e executar os comandos nele.

- No Windows, você precisa do prompt de comando ou do prompt Anaconda (instalado pelo Anaconda e pelo Miniconda).

## <a id="anotebooks"></a>Azure Notebooks

O [Azure Notebooks](https://notebooks.azure.com) (versão prévia) é um ambiente de desenvolvimento interativo na nuvem do Azure. Trata-se da maneira __mais fácil__ de começar com o desenvolvimento no Azure Machine Learning.

* O SDK do Azure Machine Learning __já está instalado__.
* Depois de criar um workspace do serviço do Azure Machine Learning no portal do Azure, você pode clicar em um botão para configurar automaticamente o ambiente do Azure Notebook para trabalhar com o workspace.

Para começar a desenvolver com o Azure Notebooks, siga as instruções no documento [Introdução ao serviço do Azure Machine Learning](quickstart-get-started.md).

## <a id="dsvm"></a>Máquina Virtual de Ciência de Dados

A DSVM (Máquina Virtual de Ciência de Dados) é uma imagem de VM (máquina virtual) personalizada **projetada para trabalhar com ciência de dados** que é pré-configurada com:

  - Pacotes, como TensorFlow, PyTorch, Scikit-learn, Xgboost e SDK do Azure ML
  - Ferramentas de ciência de dados populares, como Spark independente, Drill
  - Ferramentas do Azure como a CLI, o AzCopy e o Gerenciador de Armazenamento
  - IDEs (ambientes de desenvolvimento integrados), como Visual Studio Code, PyCharm e RStudio
  - Servidor do Jupyter Notebook 

O SDK do Azure Machine Learning funciona na versão da DSVM para Ubuntu ou Windows. Para usar a Máquina Virtual de Ciência de Dados como um ambiente de desenvolvimento, use as seguintes etapas:

1. Para criar uma Máquina Virtual de Ciência de Dados, use um dos seguintes métodos:

    * Usando o portal do Azure:

        * [Criar uma Máquina Virtual de Ciência de Dados do __Ubuntu__](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Criar uma Máquina Virtual de Ciência de Dados do __Windows__](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * Usando a CLI do Azure:

        > [!IMPORTANT]
        > Ao usar a CLI do Azure, você primeiro deve entrar em sua assinatura do Azure usando o comando `az login`.
        >
        > Ao usar os comandos nesta etapa, você precisará fornecer um nome de grupo de recursos, um nome para a VM, um nome de usuário e uma senha.

        * Para criar uma Máquina Virtual de Ciência de Dados do __Ubuntu__, use o seguinte comando:

            ```azurecli
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Para criar uma Máquina Virtual de Ciência de Dados do __Windows__, use o seguinte comando:

            ```azurecli
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```    

2. O SDK do Azure Machine Learning **já está instalado** na DSVM. Para usar o ambiente do Conda que contém o SDK, use um dos seguintes comandos:

    * Na DSVM para __Ubuntu__, use este comando:

        ```shell
        conda activate py36
        ```

    * Na DSVM para __Windows__, use este comando:

        ```shell
        conda activate AzureML
        ```

1. Para confirmar que você pode acessar o SDK e verificar a versão, use o seguinte código Python:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Para configurar a DSVM para usar seu workspace do serviço do Azure Machine Learning, confira a seção [Configurar o workspace](#workspace).

Para obter mais informações sobre as Máquinas Virtuais do Data Science, consulte [Máquinas Virtuais de Ciência de Dados](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a id="local"></a>Computador local

Ao usar um computador local (que também pode ser uma máquina virtual remota), use as seguintes etapas para criar um ambiente do Conda e instalar o SDK:

1. Abra um prompt de comando ou shell.

1. Crie um ambiente do conda com os seguintes comandos:

    ```shell
    # create a new conda environment with Python 3.6, numpy, and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # On Mac OS run
    source activate myenv
    ```

    Poderá demorar vários minutos para criar o ambiente se for necessário baixar o Python 3.6 e outros componentes.

1. Instale o SDK do Azure Machine Learning com notebook adicionais e o SDK de Preparação de Dados usando o seguinte comando:

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep
    ```

   > [!NOTE]
   > Se você receber uma mensagem informando que `PyYAML` não pode ser desinstalado, use o seguinte comando:
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   Pode demorar vários minutos para instalar o SDK.

1. Instalar pacotes de experimentação do Machine Learning. Use o seguinte comando e substitua `<new package>` pelo pacote que você deseja instalar:

    ```shell
    conda install <new package>
    ```

1. Para verificar se o SDK está instalado, use o código Python a seguir:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

### <a id="jupyter"></a>Jupyter Notebooks

Jupyter Notebooks fazem parte do [Projeto Jupyter](https://jupyter.org/). Eles fornecem uma experiência interativa de codificação, em que você pode criar documentos que combinam código ao vivo com elementos gráficos e texto. Jupyter Notebooks também são uma ótima maneira de compartilhar seus resultados com outras pessoas, pois é possível salvar a saída de suas seções de código no documento. Você pode instalar os Jupyter Notebooks em uma variedade de plataformas.

As etapas na seção [Computador local](#local) instalam componentes opcionais do Jupyter Notebooks. Para habilitar esses componentes em seu ambiente do Jupyter Notebook, use as seguintes etapas:

1. Abra um prompt de comando ou shell.

1. Para instalar um servidor do Jupyter Notebook com reconhecimento do Conda usando o seguinte comando:

    ```shell
    # install Jupyter
    conda install nb_conda
    ```

1. Abra o Jupyter Notebook com o seguinte comando:

    ```shell
    jupyter notebook
    ```

1. Para confirmar que o Jupyter Notebook pode usar o SDK, abra um novo notebook e selecione "myenv" como seu kernel. Em seguida, execute o seguinte comando em uma célula do notebook:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Para configurar o Jupyter Notebook para usar seu workspace do serviço do Azure Machine Learning, confira a seção [Configurar o workspace](#workspace).

### <a id="vscode"></a>Visual Studio Code

O Visual Studio Code é um editor de código multiplataforma. Ele se baseia em uma instalação local do Python 3 e do Conda para suporte de Python, mas fornece ferramentas adicionais para trabalhar com Inteligência Artificial. Ele também dá suporte para selecionar o ambiente do Conda de dentro do editor de código.

Para usar o Visual Studio Code para desenvolvimento, use as seguintes etapas:

1. Para saber como usar o Visual Studio Code para desenvolvimento de Python, confira o documento [Introdução ao Python no VSCode](https://code.visualstudio.com/docs/python/python-tutorial).

1. Para selecionar o ambiente do Conda, abra o VS Code e, em seguida, use __Ctrl-Shift-P__ (Linux e Windows) ou __Comando-Shift-P__ (Mac) para obter o __Palete de Comando__. Insira __Python: Selecionar Interpretador__ e, em seguida, selecione o ambiente do Conda.

1. Para validar que você pode usar o SDK, crie um novo arquivo de Python (.py) que contém o código a seguir. Em seguida, execute o arquivo:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Para instalar a extensão do Azure Machine Learning para Visual Studio Code, confira a página [Ferramentas para IA](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

    Para obter mais informações, confira [Usando o Azure Machine Learning para Visual Studio Code](how-to-vscode-tools.md).

<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks

Use uma versão personalizada do SDK do Azure Machine Learning para Azure Databricks para o aprendizado de máquina personalizado de ponta a ponta. Ou treine o modelo no Databricks e use o [Visual Studio Code](how-to-vscode-train-deploy.md#deploy-your-service-from-vs-code) para implantá-lo

Para preparar o cluster do Databricks e obter notebooks de exemplo:

1. Crie um [cluster do Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) com o Databricks Runtime versão 4.x (alta simultaneidade preferencial) com o **Python 3**. 

1. Crie uma biblioteca para [instalar e anexar](https://docs.databricks.com/user-guide/libraries.html#create-a-library) o SDK do Azure Machine Learning para o pacote `azureml-sdk[databricks]` PyPi do Python ao cluster. Quando terminar, você verá a biblioteca anexada, conforme mostrado nesta imagem. Esteja ciente destes [problemas comuns do Databricks](resource-known-issues.md#databricks).

   ![SDK instalado no Databricks ](./media/how-to-azure-machine-learning-on-databricks/sdk-installed-on-databricks.jpg)

   Se esta etapa falhar, reinicie o cluster:
   1. Selecione `Clusters` no painel esquerdo. Selecione o nome do cluster na tabela. 
   1. Na guia `Libraries`, selecione `Restart`.

1. Baixe o [arquivo morto do notebook do SDK do Azure Machine Learning/Azure Databricks](https://github.com/Azure/MachineLearningNotebooks/blob/master/databricks/Databricks_AMLSDK_github.dbc).

   >[!Warning]
   > Muitos notebooks de exemplo estão disponíveis para uso com o serviço do Azure Machine Learning. Apenas esses notebooks de exemplo funcionam com o Azure Databricks: https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks

1.  [Importe este arquivo morto](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive) para o cluster do Databricks e comece a explorá-lo conforme [descrito aqui](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks).


## <a id="workspace"></a>Criar um arquivo de configuração do workspace

O arquivo de configuração do workspace é um documento JSON que informa ao SDK para se comunicar com o workspace do serviço do Azure Machine Learning. O arquivo é nomeado `config.json` e tem o seguinte formato:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Esse arquivo deve estar na estrutura de diretório que contém seus scripts de Python ou Jupyter Notebooks. Ele pode estar no mesmo diretório, em um subdiretório chamado `aml_config` ou em um diretório pai.

Para usar esse arquivo de seu código, use `ws=Workspace.from_config()`. Esse código carrega as informações do arquivo e se conecta ao seu workspace.

Há três maneiras de criar o arquivo de configuração:

* Se você seguir o [Guia de início rápido do Azure Machine Learning](quickstart-get-started.md), um arquivo `config.json` será criado em sua biblioteca do Azure Notebooks. Este arquivo contém as informações de configuração para o seu workspace. Você pode baixar ou copiar este `config.json` para outros ambientes de desenvolvimento.

* Você pode **criar manualmente o arquivo** usando um editor de texto. Para encontrar os valores dele, vá até o arquivo de configuração visitando seu workspace na [portal do Azure](https://portal.azure.com). Copie os valores do __Nome do workspace__, do __Grupo de recursos__ e da __ID da assinatura__ e use-os no arquivo de configuração.
        ![Portal do Azure](./media/how-to-configure-environment/configure.png)

* Você pode **criar o arquivo de forma programática**. O snippet de código a seguir demonstra como se conectar a um workspace fornecendo a ID da assinatura, o grupo de recursos e o nome do workspace. Em seguida, ele salva a configuração do workspace no arquivo:

    ```python
    from azureml.core import Workspace

    subscription_id = '<subscription-id>'
    resource_group  = '<resource-group>'
    workspace_name  = '<workspace-name>'

    try:
        ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
        ws.write_config()
        print('Library configuration succeeded')
    except:
        print('Workspace not found')
    ```

    Esse código grava o arquivo de configuração no arquivo `aml_config/config.json`.

## <a name="next-steps"></a>Próximas etapas

- [Treinar um modelo no Azure Machine Learning com o conjunto de dados MNIST](tutorial-train-models-with-aml.md)
- [SDK do Azure Machine Learning para Python](https://aka.ms/aml-sdk)
- [SDK de preparação de dados do Azure Machine Learning](https://aka.ms/data-prep-sdk)
