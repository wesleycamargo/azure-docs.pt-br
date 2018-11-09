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
ms.date: 10/24/2018
ms.openlocfilehash: 6c2d5a776f603161ef730028168b91844c120aec
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50158985"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Configurar um ambiente de desenvolvimento para Azure Machine Learning

Este artigo ensina como configurar um ambiente de desenvolvimento para trabalhar com o serviço do Azure Machine Learning, incluindo:

- Como criar um arquivo de configuração que associa o ambiente a uma área de trabalho do serviço do Azure Machine Learning.
- Como configurar os seguintes ambientes de desenvolvimento:
  - Jupyter Notebooks no computador
  - Visual Studio Code
  - Editor de código personalizado
- Como configurar um [ambiente virtual do conda](https://conda.io/docs/user-guide/tasks/manage-environments.html) e usá-lo para o Azure Machine Learning. É recomendável usar Continuum Anaconda para isolar o ambiente de trabalho e evitar conflitos de dependência entre pacotes.

## <a name="prerequisites"></a>Pré-requisitos

- Configure um espaço de trabalho do serviço do Azure Machine Learning. Siga as etapas em [Introdução ao serviço do Azure Machine Learning](quickstart-get-started.md).
- Instale o gerenciador de pacotes [Continuum Anaconda](https://www.anaconda.com/download/) ou [Miniconda](https://conda.io/miniconda.html).
- Se você estiver usando o código do Visual Studio Code, obtenha a [Extensão Python](https://code.visualstudio.com/docs/python/python-tutorial).

> [!NOTE]
> Você pode testar os comandos do shell mostrados neste artigo usando o bash (no Linux e no Mac OS) ou o prompt de comando (no Windows).

## <a name="create-a-workspace-configuration-file"></a>Criar um arquivo de configuração de espaço de trabalho

O SDK do Azure Machine Learning usa o arquivo de configuração do espaço de trabalho para comunicar-se com o espaço de trabalho do serviço do Azure Machine Learning.

- Para criar o arquivo de configuração, conclua o [início rápido do Azure Machine Learning](quickstart-get-started.md).
  - O processo de início rápido cria um arquivo `config.json` no Azure Notebooks. Este arquivo contém as informações de configuração para o seu workspace.
  - Baixe ou copie `config.json` no mesmo diretório que os scripts ou notebooks que fazem referência a ele.

- Como alternativa, é possível criar o arquivo manualmente seguindo estas etapas:

    1. Abra seu workspace na [portal do Azure](https://portal.azure.com). Cópia de __nome do workspace__, __grupo de recursos__, e __ID da assinatura__. Esses valores são usados para criar o arquivo de configuração.
        ![Portal do Azure](./media/how-to-configure-environment/configure.png)

    1. Crie o arquivo com o seguinte código Python e certifique-se de executar o código no mesmo diretório que os scripts ou notebooks que fazem referência ao espaço de trabalho:

        ```python
        from azureml.core import Workspace

        subscription_id ='<subscription-id>'
        resource_group ='<resource-group>'
        workspace_name = '<workspace-name>'

        try:
           ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
           ws.write_config()
           print('Library configuration succeeded')
        except:
           print('Workspace not found')
        ```
        O código grava o seguinte arquivo `aml_config/config.json`:

        ```json
        {
        "subscription_id": "<subscription-id>",
        "resource_group": "<resource-group>",
        "workspace_name": "<workspace-name>"
        }
        ```
        Copie o diretório `aml_config` ou apenas o arquivo `config.json` em outro diretório que referencie o workspace.

       > [!NOTE]
       > Outros scripts ou notebooks no mesmo diretório ou abaixo carregam o espaço de trabalho com `ws=Workspace.from_config()`.

## <a name="azure-notebooks-and-data-science-virtual-machines"></a>Azure Notebooks e Máquinas Virtuais de Ciência de Dados

O Azure Notebook e as DSVMs (Máquinas Virtuais de Ciência de Dados) vêm configuradas para funcionar com o serviço do Azure Machine Learning. Esses ambientes incluem componentes necessários como o SDK do Azure Machine Learning.

- Os Azure Notebooks são um serviço Jupyter Notebook na nuvem do Azure.
- A Máquina Virtual de Ciência de Dados é uma imagem de VM (máquina virtual) personalizada projetada para o trabalho de ciência de dados. Ele inclui:
  - Ferramentas populares
  - IDEs (ambientes de desenvolvimento integrado)
  - Pacotes como Notebooks Jupyter, PyCharm e Tensorflow
- Além disso, será necessário um arquivo de configuração do espaço de trabalho para usar esses ambientes.

Para obter um exemplo do uso do Azure Notebooks com o serviço do Azure Machine Learning, consulte [Introdução ao serviço do Azure Machine Learning](quickstart-get-started.md).

Para obter mais informações sobre as Máquinas Virtuais do Data Science, consulte [Máquinas Virtuais de Ciência de Dados](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="configure-jupyter-notebooks-on-your-computer"></a>Configurar Jupyter Notebooks no computador

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

   Você pode ver os documentos de referência do Python para classes e métodos nos seguintes SDKs:
   + [SDK do Azure Machine Learning para Python](https://aka.ms/aml-sdk)
   + [SDK de preparação de dados do Azure Machine Learning](https://aka.ms/data-prep-sdk)

   > [!NOTE]
   > Se você receber uma mensagem informando que `PyYAML` não pode ser desinstalado, use o seguinte comando:
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   Pode demorar vários minutos para instalar o SDK.

1. Instalar pacotes de experimentação do Machine Learning. Use o seguinte comando e substitua `<new package>` pelo pacote que você deseja instalar:

    ```shell
    conda install <new package>
    ```

1. Instale um servidor Jupyter Notebook com reconhecimento e habilite widgets de experimento (para exibir informações de execução). Use os seguintes comandos:

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

1. Abra um novo notebook, selecione "myenv" como o kernel e, em seguida, valide se você tiver o SDK do Azure Machine Learning instalado. Execute o seguinte comando em uma célula do notebook:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-visual-studio-code"></a>Configurar o código do Visual Studio

1. Abra um prompt de comando ou shell.

1. Crie um ambiente do conda com os seguintes comandos:

    ```shell
    # create a new conda environment with Python 3.6, numpy and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # If you are running Mac OS you should run
    source activate myenv
    ```

1. Instale o SDK do Azure Machine Learning e o SDK de Preparação de Dados com o seguinte comando:

    ```shell
    pip install --upgrade azureml-sdk[automl] azureml-dataprep
    ```

1. Instale a extensão do Visual Studio Code Tools for AI. Consulte [Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

1. Instalar pacotes de experimentação do Machine Learning. Use o seguinte comando, substituindo `<new package>` pelo pacote que você deseja instalar:

    ```shell
    conda install <new package>
    ```

1. Abra o Visual Studio Code e use **CTRL-SHIFT-P** (em Windows) ou **COMMAND-SHIFT-P** (em Mac OS) para obter a **Paleta de Comandos**. Insira _Python: selecionar interpretador_ e selecione o ambiente do conda que você criou.

   > [!NOTE]
   > Visual Studio Code reconhece automaticamente dos ambientes do conda no seu computador. Para obter mais informações, consulte [documentação do código do Visual Studio](https://code.visualstudio.com/docs/python/environments#_conda-environments).

1. Valide a configuração usando o código do Visual Studio Code para criar um novo arquivo de script Python do com o código a seguir e, em seguida, execute-o:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-a-custom-code-editor"></a>Configurar um editor de código personalizado

Você pode usar um editor de código de sua escolha com o SDK do Azure Machine Learning.

1. Crie o ambiente do conda conforme descrito na etapa 2 de [Configurar o Visual Studio Code](#configure-visual-studio-code) acima.
1. Siga as instruções para cada editor para usar o ambiente do conda. Por exemplo, você pode seguir as [instruções de PyCharm](https://www.jetbrains.com/help/pycharm/2018.2/conda-support-creating-conda-virtual-environment.html).

## <a name="next-steps"></a>Próximas etapas

- [Treinar um modelo no Azure Machine Learning com o conjunto de dados MNIST](tutorial-train-models-with-aml.md)
