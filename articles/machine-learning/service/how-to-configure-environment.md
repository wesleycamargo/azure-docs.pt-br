---
title: Configurar um ambiente de desenvolvimento para o Aprendizado de Máquina do Azure | Microsoft Docs
description: Aprenda a configurar um ambiente de desenvolvimento ao trabalhar com o serviço de Aprendizado de Máquina do Azure. Neste documento, aprenda como usar os ambientes Conda, crie arquivos de configuração e configure os Jupyter Notebooks, os Azure Notebooks, os IDEs, os editores de código e a Data Science Virtual Machine.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 8/6/2018
ms.openlocfilehash: 675dae022376fc62292f3b079bd735939b9199c2
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220288"
---
# <a name="configure-a-development-environment-for-the-azure-machine-learning-service"></a>Configurar um ambiente de desenvolvimento para o serviço Azure Machine Learning

Aprenda a configurar seu ambiente de desenvolvimento para trabalhar com o serviço de Aprendizado de Máquina do Azure. Você aprenderá a criar um arquivo de configuração que associa seu ambiente a um espaço de trabalho do Azure Machine Learning. Você também aprenderá a configurar os seguintes ambientes de desenvolvimento:

* Notebooks Jupyter no seu próprio computador
* Visual Studio Code
* Editor de código da sua escolha

A abordagem recomendada é usar o Continuum Anaconda [conda virtual environments](https://conda.io/docs/user-guide/tasks/manage-environments.html) para isolar seu ambiente de trabalho para evitar conflitos de dependência entre pacotes. Este artigo mostra as etapas de configuração de um ambiente conda e seu uso no Aprendizado de Máquina do Azure.


## <a name="prerequisites"></a>Pré-requisitos

* Um espaço de trabalho do serviço de aprendizado de máquina do Azure. Para criar um, use as etapas no documento [Introdução ao serviço de aprendizado de máquina do Azure](quickstart-get-started.md).

* [Continuum Anaconda](https://www.anaconda.com/download/) ou [Miniconda](https://conda.io/miniconda.html) gerenciador de pacotes.

 * Para o ambiente do Visual Studio Code, a [Extensão Python](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="create-workspace-configuration-file"></a>Criar arquivo de configuração do espaço de trabalho

O arquivo de configuração da área de trabalho é usado pelo SDK para se comunicar com a área de trabalho do serviço de Aprendizado de Máquina do Azure.  Há duas maneiras de obter esse arquivo:

* Conclua o [Início Rápido](quickstart-get-started.md) para criar um workspace e um arquivo de configuração. O arquivo `config.json` é criado para você em Azure Notebooks.  Este arquivo contém as informações de configuração para o seu espaço de trabalho.  Baixe ou copie-o no mesmo diretório que os scripts ou blocos de anotações que o referenciam.


* Crie o arquivo de configuração você mesmo com as seguintes etapas:

    1. Abra seu espaço de trabalho na [portal do Azure](https://portal.azure.com). Cópia de __nome do espaço de trabalho__, __grupo de recursos__, e __ID da assinatura__. Esses valores são usados para criar o arquivo de configuração.

        ![Portal do Azure](./media/how-to-configure-environment/configure.png) 
    
    1. Crie o arquivo com esse código Python. Execute o código no mesmo diretório que os scripts ou blocos de anotações que referenciam o workspace:
        ```
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
        Isso grava o seguinte arquivo `aml_config/config.json`: 
    
        ```json
        {
        "subscription_id": "<subscription-id>",
        "resource_group": "<resource-group>",
        "workspace_name": "<workspace-name>"
        }
        ```
        Copie o diretório `aml_config` ou apenas o arquivo `config.json` em outro diretório que referencie o workspace.

>[!NOTE] 
>Outros scripts ou blocos de anotações no mesmo diretório ou abaixo serão carregados com o workspace `ws=Workspace.from_config()`

## <a name="azure-notebooks-and-data-science-virtual-machine"></a>Computadores portáteis do Azure e máquina virtual de dados de ciência

Os Notebooks do Azure e as Máquinas Virtuais do Windows Azure Data Science (DSVM) são pré-configurados para funcionar com o serviço de Aprendizado de Máquina do Azure. Os componentes necessários, como o SDK do Aprendizado de Máquina do Azure, são pré-instalados nesses ambientes.

Os Azure Notebooks são um serviço Jupyter Notebook na nuvem do Azure. A Data Science Virtual Machine é uma imagem de VM pré-configurada para o trabalho de ciência de dados. A VM inclui ferramentas, IDEs e pacotes populares, como os Jupyter Notebooks, PyCharm e Tensorflow.

Você ainda precisará de um arquivo de configuração do espaço de trabalho para usar esses ambientes.

Para obter mais informações sobre as Máquinas Virtuais do Data Science, consulte a documentação da [Data Science Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

Para obter um exemplo do uso de Azure Notebooks com o serviço de Aprendizado de Máquina do Azure, consulte o documento [Introdução ao serviço de Aprendizado de Máquina do Azure](quickstart-get-started.md).

## <a name="configure-jupyter-notebooks-on-your-own-computer"></a>Configurar os notebooks Jupyter no seu próprio computador

1. Abra um prompt de comando ou shell.

2. Para criar um ambiente conda, use os seguintes comandos:

    ```shell
    # create a new conda environment with Python 3.6, numpy and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # If you are running Mac OS you should run
    source activate myenv
    ```

    Pode levar vários minutos para criar o ambiente, pois o Python 3.6 e outros componentes podem precisar ser baixados.

3. Para instalar o SDK do Aprendizado de Máquina do Azure com extras de notebook, use o seguinte comando:

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl]
    ```

    Pode levar vários minutos para instalar o SDK.

4. Para instalar pacotes para sua experimentação de aprendizado de máquina, use o seguinte comando e substitua `<new package>` pelo pacote que você deseja instalar:

    ```shell
    conda install <new package>
    ```

5. Para instalar um servidor Jupyter Notebook com reconhecimento e habilitar widgets experimentais (para exibir informações de execução), use os seguintes comandos:

    ```shell
    # install Jupyter 
    conda install nb_conda

    # install experiment widget
    jupyter nbextension install --py --user azureml.train.widgets

    # enable experiment widget
    jupyter nbextension enable --py --user azureml.train.widgets
    ```

6. Para iniciar o Jupyter Notebook, use o seguinte comando:

    ```shell
    jupyter notebook
    ```

7. Abra um novo notebook e selecione "myenv" como seu kernel. Em seguida, valide se você possui o SDK do Azure Machine Learning instalado, executando o seguinte comando em uma célula do bloco de anotações:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-visual-studio-code"></a>Configurar o código do Visual Studio

1. Abra um prompt de comando ou shell.

2. Para criar um ambiente conda, use os seguintes comandos:

    ```shell
    # create a new conda environment with Python 3.6, numpy and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # If you are running Mac OS you should run
    source activate myenv
    ```

2. Para instalar o SDK do Aprendizado de Máquina do Azure, use o seguinte comando:
 
    ```shell
    pip install --upgrade azureml-sdk[automl]
    ```

4. Para instalar o código do Visual Studio Tools for AI, consulte a entrada do mercado do Visual Studio para [Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai). 

5. Para instalar pacotes para sua experimentação de aprendizado de máquina, use o seguinte comando e substitua `<new package>` pelo pacote que você deseja instalar:

    ```shell
    conda install <new package>
    ```

6. Inicie o Visual Studio Code e use __CTRL-SHIFT-P__ para obter a __Command Palette__. Insira *Python: selecionar interpretador*e selecione o ambiente de conda que você criou.

    > [!NOTE]
    > Visual Studio Code reconhece automaticamente dos ambientes do conda no seu computador. Para obter mais informações, consulte [documentação do código do Visual Studio](https://code.visualstudio.com/docs/python/environments#_conda-environments).

7. Para validar a configuração, use o código do Visual Studio para criar um novo arquivo de script Python com o código a seguir e execute-o:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-code-editor-of-your-choice"></a>Configure o editor de código de sua escolha

Para usar um editor de código personalizado com o SDK do Aprendizado de Máquina do Azure, crie primeiro o ambiente conda, conforme descrito acima. Em seguida, siga as instruções de cada editor para usar o ambiente conda. Por exemplo, as instruções para o PyCharm estão localizadas em [https://www.jetbrains.com/help/pycharm/2018.2/conda-support-creating-conda-virtual-environment.html](https://www.jetbrains.com/help/pycharm/2018.2/conda-support-creating-conda-virtual-environment.html).
 
## <a name="next-steps"></a>Próximas etapas

* [Treinar um modelo no Azure Machine Learning com o conjunto de dados MNIST](tutorial-train-models-with-aml.md)
