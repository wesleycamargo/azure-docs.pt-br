---
title: Configurar um ambiente de desenvolvimento para o Aprendizado de Máquina do Azure | Microsoft Docs
description: Aprenda a configurar um ambiente de desenvolvimento ao trabalhar com o serviço de Aprendizado de Máquina do Azure. Neste documento, aprenda como usar os ambientes Conda, crie arquivos de configuração e configure os Jupyter Notebooks, os Azure Notebooks, os IDEs, os editores de código e a Data Science Virtual Machine.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.component: core
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 11/6/2018
ms.openlocfilehash: 2fd2d35bde95a3e268f46b398f2163f9d40ab1ee
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51613946"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Configurar um ambiente de desenvolvimento para Azure Machine Learning

Neste documento, aprenda a configurar seu ambiente de desenvolvimento para trabalhar com o serviço do Azure Machine Learning. O serviço do Azure Machine Learning é independente da plataforma. Os únicos requisitos para seu ambiente de desenvolvimento são ter o __Python 3__, o __Conda__ (para ambientes isolados) e um arquivo de configuração que contém as informações do Workspace do Azure Machine Learning.

Este documento tem como foco as seguintes ferramentas e ambientes específicos:

* [Azure Notebooks](#aznotebooks): um serviço Jupyter Notebooks hospedado na nuvem do Azure. É a maneira __mais fácil__ de começar, pois o SDK do Azure Machine Learning já está instalado.
* [Máquina Virtual de Ciência de Dados](#dsvm): uma máquina virtual na nuvem do Azure __projetada para trabalhar com ciência de dados__. O Python 3, o Conda, o Jupyter Notebooks e o SDK do Azure Machine Learning já estão instalados. A VM tem estruturas, ferramentas e editores de ML populares para desenvolver soluções de ML. Provavelmente, trata-se do ambiente de desenvolvimento __mais completo__ para ML na plataforma do Azure.
* [Jupyter Notebooks](#jupyter): se você já está usando Jupyter Notebooks, o SDK tem alguns recursos extra que você deve instalar.
* [Visual Studio Code](#vscode): se você usa o Visual Studio Code, há algumas extensões úteis que podem ser instaladas.

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

A DSVM (Máquina Virtual de Ciência de Dados) é uma imagem de VM (máquina virtual) personalizada **projetada para trabalhar com ciência de dados**.  Ele inclui:

  - Ferramentas de ciência de dados populares
  - Ambientes de desenvolvimento integrados (IDEs), como PyCharm e RStudio
  - Pacotes como Notebooks Jupyter e Tensorflow

O SDK do Azure Machine Learning funciona na versão da DSVM para Ubuntu ou Windows. Para usar a DSVM como ambiente de desenvolvimento, siga estas etapas:

1. Para criar uma Máquina Virtual de Ciência de Dados, siga as etapas em um dos seguintes documentos:

    * [Criar uma máquina virtual de Ciência de Dados do Ubuntu](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)
    * [Criar uma máquina virtual de Ciência de Dados do Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

1. O SDK do Azure Machine Learning **já está instalado** na DSVM. Para usar o ambiente do Conda que contém o SDK, use um dos seguintes comandos:

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

1. Para instalar um servidor do Jupyter Notebook com reconhecimento de Conda e habilitar widgets experimentais, use os seguintes comandos:

    ```shell
    # install Jupyter
    conda install nb_conda

    # install experiment widget
    jupyter nbextension install --py --user azureml.train.widgets

    # enable experiment widget
    jupyter nbextension enable --py --user azureml.train.widgets
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

1. Para selecionar o ambiente do Conda, abra o VS Code e, em seguida, use __Ctrl-Shift-P__ (Linux e Windows) ou __Comando-Shift-P__ (Mac) para obter o __Palete de Comando__. Insira __Python: selecionar interpretador__ e selecione o ambiente do Conda.

1. Para validar que você pode usar o SDK, crie um novo arquivo de Python (.py) que contém o código a seguir. Em seguida, execute o arquivo:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Para instalar a extensão Visual Studio Code Tools for AI, confira a página [Ferramentas para IA](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

    Para obter mais informações, confira o documento [Usar as VS Code Tools for AI with Azure Machine Learning](how-to-vscode-tools.md).

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