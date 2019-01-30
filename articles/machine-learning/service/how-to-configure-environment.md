---
title: Configurar um ambiente de desenvolvimento do Python
titleSuffix: Azure Machine Learning service
description: Saiba como configurar um ambiente de desenvolvimento ao trabalhar com o serviço do Azure Machine Learning. Neste artigo, você aprenderá como usar os ambientes do Conda, criar arquivos de configuração e configurar Jupyter Notebooks, Azure Notebooks, Azure Databricks, IDEs, editores de códigos e Máquina Virtual de Ciência de Dados.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.component: core
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 01/18/2019
ms.custom: seodec18
ms.openlocfilehash: 0f53b3cec843ca8016c61a360025b5e731b96f55
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54815864"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Configurar um ambiente de desenvolvimento para Azure Machine Learning

Neste artigo, você aprenderá como configurar um ambiente de desenvolvimento para trabalhar com o serviço do Azure Machine Learning. O serviço do Machine Learning é independente de plataforma. 

Os únicos requisitos para o ambiente de desenvolvimento são o Python 3, Conda (para ambientes isolados) e um arquivo de configuração contendo as informações do Workspace do Azure Machine Learning.

Este artigo concentra-se nos ambientes e ferramentas a seguir:

* [Azure Notebooks](#aznotebooks): Um serviço Jupyter Notebooks hospedado na nuvem do Azure. Essa é a maneira mais fácil de começar, porque o SDK do Azure Machine Learning já está instalado.

* [DSVM (Máquina Virtual de Ciência de Dados)](#dsvm): Um ambiente de desenvolvimento ou experimentação pré-configurado na nuvem do Azure, projetado para o trabalho de ciência de dados e que pode ser implantado em instâncias de VM somente da CPU ou em instâncias baseadas em GPU. O Python 3, o Conda, o Jupyter Notebooks e o SDK do Azure Machine Learning já estão instalados. A VM é fornecida com estruturas de aprendizado profundo e aprendizado de máquina populares, ferramentas e editores para o desenvolvimento de soluções de aprendizado de máquina. É provavelmente o ambiente de desenvolvimento mais completo para aprendizado de máquina na plataforma do Azure.

* [Jupyter Notebook](#jupyter): Se você já estiver usando o Jupyter Notebook, o SDK terá alguns adicionais que deverão ser instalados.

* [Visual Studio Code](#vscode): Se você usar o Visual Studio Code, ele terá algumas extensões úteis que você poderá instalar.

* [Azure Databricks](#aml-databricks): Uma plataforma de análise de dados popular baseada no Apache Spark. Saiba como obter o SDK do Azure Machine Learning do cluster para que você possa implantar modelos.

Se você já tiver um ambiente de Python 3 ou desejar ver apenas as etapas básicas para instalar o SDK, confira a seção [Computador local](#local).

## <a name="prerequisites"></a>Pré-requisitos

- Um workspace de serviço do Azure Machine Learning. Para criar o workspace, consulte [Introdução ao serviço do Azure Machine Learning](quickstart-get-started.md).

- O gerenciador de pacotes [Continuum Anaconda](https://www.anaconda.com/download/) ou [Miniconda](https://conda.io/miniconda.html).

    > [!IMPORTANT]
    > Anaconda e Miniconda não serão necessários quando você estiver usando o Azure Notebooks.

- No Linux ou MacOS, será necessário o shell do bash.

    > [!TIP]
    > Se você estiver no Linux ou no macOS e usar um shell diferente do bash (por exemplo, zsh), poderá receber erros ao executar alguns comandos. Para contornar esse problema, use o comando `bash` para iniciar um novo shell do Bash e executar os comandos nele.

- No Windows, você precisa do prompt de comando ou do prompt Anaconda (instalado pelo Anaconda e pelo Miniconda).

## <a id="aznotebooks"></a>Azure Notebooks

O [Azure Notebooks](https://notebooks.azure.com) (versão prévia) é um ambiente de desenvolvimento interativo na nuvem do Azure. Essa é a maneira mais fácil para começar o desenvolvimento do Azure Machine Learning.

* O SDK do Azure Machine Learning já está instalado.
* Após criar um workspace de serviço do Azure Machine Learning no portal do Azure, você poderá clicar em um botão para configurar automaticamente o ambiente do Azure Notebook para trabalhar com o workspace.

Para começar a desenvolver com o Azure Notebooks, consulte [Introdução ao serviço do Azure Machine Learning](quickstart-get-started.md).

Por padrão, o Azure Notebooks usa uma camada de serviços gratuita que está limitada a 4 GB de memória e 1 GB de dados. No entanto, é possível remover esses limites anexando uma instância de Máquina Virtual de Ciência de Dados ao projeto do Azure Notebooks. Para saber mais, confira [Gerenciar e configurar projetos do Azure Notebooks: camada de computação](/azure/notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

## <a id="dsvm"></a>Máquina Virtual de Ciência de Dados

A DSVM é uma imagem de VM (máquina virtual) personalizada. Ela foi projetada para trabalhos de ciência de dados pré-configurados com:

  - Pacotes como TensorFlow, PyTorch, Scikit-learn, XGBoost e o SDK do Azure Machine Learning
  - Ferramentas de ciência de dados populares como o Spark Standalone e Drill
  - Ferramentas do Azure como a CLI do Azure, o AzCopy e o Gerenciador de Armazenamento
  - IDEs (Ambientes de Desenvolvimento Integrados) como o Visual Studio Code e o PyCharm
  - Servidor do Jupyter Notebook

O SDK do Azure Machine Learning funciona na versão da DSVM para Ubuntu ou Windows. Porém, se você também planeja usar a DSVM como um destino de computação, somente o Ubuntu tem suporte.

Para usar a DSVM como um ambiente de desenvolvimento, faça o seguinte:

1. Crie uma DSVM em um dos seguintes ambientes:

    * Portal do Azure:

        * [Criar uma Máquina Virtual de Ciência de Dados do Ubuntu](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Criar uma máquina virtual de Ciência de Dados do Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * CLI do Azure:

        > [!IMPORTANT]
        > * Ao usar a CLI do Azure, primeiro você deverá entrar na assinatura do Azure usando o comando `az login`.
        >
        > * Ao usar os comandos nessa etapa, será necessários fornecer um nome do grupo de recursos, um nome para a VM, um nome de usuário e uma senha.

        * Para criar uma Máquina Virtual de Ciência de Dados do Ubuntu, use o comando a seguir:

            ```azurecli
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Para criar uma Máquina Virtual de Ciência de Dados do Windows, use o comando a seguir:

            ```azurecli
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```    

2. O SDK do Azure Machine Learning já está instalado na DSVM. Para usar o ambiente do Conda que contém o SDK, use um dos seguintes comandos:

    * Para DSVM do Ubuntu:

        ```shell
        conda activate py36
        ```

    * Para DSVM do Windows:

        ```shell
        conda activate AzureML
        ```

1. Para confirmar que você pode acessar o SDK e verificar a versão, use o seguinte código Python:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Para configurar a DSVM para usar o workspace de serviço do Azure Machine Learning, consulte a seção [Criar um arquivo de configuração do workspace](#workspace).

Para obter mais informações, consulte [Máquinas Virtuais de Ciência de Dados](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a id="local"></a>Computador local

Ao utilizar um computador local (que também poderá ser uma máquina virtual remota), crie um ambiente Conda e instale o SDK conforme a seguir:

1. Abra um prompt de comando ou shell.

1. Crie um ambiente Conda com os seguintes comandos:

    ```shell
    # create a new Conda environment with Python 3.6, NumPy, and Cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the Conda environment
    conda activate myenv

    # On macOS run
    source activate myenv
    ```

    Poderá demorar vários minutos para criar o ambiente se for necessário baixar o Python 3.6 e outros componentes.

1. Instale o SDK do Azure Machine Learning com notebook adicionais e o SDK de Preparação de Dados usando o seguinte comando:

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep
    ```

   > [!NOTE]
   > Se você receber uma mensagem informando que o PyYAML não pode ser desinstalado, use o seguinte comando:
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   Pode demorar vários minutos para instalar o SDK.

1. Instalar pacotes de experimentação do Machine Learning. Use o seguinte comando e substitua *\<novo pacote>* pelo pacote que você quer instalar:

    ```shell
    conda install <new package>
    ```

1. Para verificar se o SDK está instalado, use o código Python a seguir:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

### <a id="jupyter"></a>Jupyter Notebooks

Jupyter Notebooks fazem parte do [Projeto Jupyter](https://jupyter.org/). Eles fornecem uma experiência interativa de codificação, em que você pode criar documentos que combinam código ao vivo com elementos gráficos e texto. O Jupyter Notebook também é uma ótima maneira de compartilhar seus resultados com outras pessoas, já que é possível salvar a saída das seções de código no documento. Você pode instalar os Jupyter Notebooks em uma variedade de plataformas.

O procedimento na seção [Computador local](#local) instala componentes opcionais do Jupyter Notebooks. Para habilitar esses componentes no ambiente do Jupyter Notebook, faça o seguinte:

1. Abra um prompt de comando ou shell.

1. Para instalar um servidor do Jupyter Notebook com reconhecimento de Conda, use o comando a seguir:

    ```shell
    # install Jupyter
    conda install nb_conda
    ```

1. Abra o Jupyter Notebook com o seguinte comando:

    ```shell
    jupyter notebook
    ```

1. Para verificar se o Jupyter Notebook pode usar o SDK, abra um novo notebook, selecione **myenv** como o kernel e execute o seguinte comando em uma célula do notebook:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Para configurar o Jupyter Notebook para usar o workspace de serviço do Azure Machine Learning, vá para a seção [Criar um arquivo de configuração do workspace](#workspace).

### <a id="vscode"></a>Visual Studio Code

O Visual Studio Code é um editor de código multiplataforma. Ele se baseia em uma instalação local do Python 3 e do Conda para suporte de Python, mas fornece ferramentas adicionais para trabalhar com Inteligência Artificial. Ele também dá suporte para selecionar o ambiente do Conda de dentro do editor de código.

Para usar o Visual Studio Code para desenvolvimento, faça o seguinte:

1. Para aprender a usar o Visual Studio Code para desenvolvimento em Python, consulte [Introdução ao Python no VSCode](https://code.visualstudio.com/docs/python/python-tutorial).

1. Para selecionar o ambiente Conda, abra o VS Code e, em seguida, selecione Ctrl+Shift+P (Linux e Windows) ou Command+Shift+P (Mac).  
    A __Paleta de Comandos__ abre. 

1. Insira __Python: Selecione Interpretador__e, em seguida, selecione o ambiente Conda.

1. Para validar que é possível usar o SDK, crie e execute um novo arquivo Python (.py) contendo o seguinte código:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Para instalar a extensão do Azure Machine Learning para Visual Studio Code, consulte [Ferramentas para IA](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

    Para obter mais informações, consulte [Usar o Azure Machine Learning para Visual Studio Code](how-to-vscode-tools.md).

<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks

Use uma versão personalizada do SDK do Azure Machine Learning para Azure Databricks para o aprendizado de máquina personalizado de ponta a ponta. Ou, você pode treinar seu modelo dentro do Databricks e implantá-lo usando o [Visual Studio Code](how-to-vscode-train-deploy.md#deploy-your-service-from-vs-code).

Para preparar o cluster do Databricks e obter notebooks de exemplo:

1. Crie um [cluster do Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) com as seguintes configurações:

    | Configuração | Valor |
    |----|---|
    | Nome do cluster | nomedoseucluster |
    | Databricks Runtime | Qualquer tempo de execução não ML (não ML 4.x, 5.x) |
    | Versão do Python | 3 |
    | Trabalhos | 2 ou superior |

    Use essas configurações somente se você for usar o aprendizado de máquina automatizado no Databricks:
    
    |   Configuração | Valor |
    |----|---|
    | Tipos de VM do nó de trabalho | Uma VM otimizada para memória é preferível |
    | Habilitar o dimensionamento automático | Desmarcar |
    
    O número de nós de trabalho no seu cluster do Databricks determina o número máximo de iterações simultâneas em configurações de ML Automatizado.  

    São necessários alguns minutos para criar o cluster. Aguarde até que o cluster está em execução antes de continuar.

1. Instale um pacote de SDK do Azure Machine Learning e anexe-o ao cluster.  

    * [Crie uma biblioteca](https://docs.databricks.com/user-guide/libraries.html#create-a-library) com uma dessas configurações (_escolher apenas uma dessas opções_):
    
        * Para instalar o SDK do Azure Machine Learning _sem_ a funcionalidade de aprendizado de máquina automatizado:
            | Configuração | Valor |
            |----|---|
            |Fonte | Carregar Python Egg ou PyPI
            |Nome do PyPi | azureml-sdk[databricks]
    
        * Para instalar o SDK do Azure Machine Learning _com_ aprendizado de máquina automatizado:
            | Configuração | Valor |
            |----|---|
            |Fonte | Carregar Python Egg ou PyPI
            |Nome do PyPi | azureml-sdk[automl_databricks]
    
    * Não selecione **Anexar automaticamente a todos os clusters**

    * Selecione **Anexar** ao lado do nome do cluster

    * Verifique se não há nenhum erro até que o status seja alterado para **Anexado**. Isso poderá levar alguns minutos.

    Se você tiver uma versão antiga do SDK, anule sua seleção das bibliotecas instaladas do cluster e mova-a para a lixeira. Instale a nova versão do SDK e reinicie o cluster. Se houver um problema depois de fazer isso, desanexe e anexe novamente o cluster.

    Quando concluir, a biblioteca será anexada conforme mostrado nas imagens a seguir. Esteja ciente destes [problemas comuns do Databricks](resource-known-issues.md#databricks).

    * Se você instalou o SDK do Azure Machine Learning _sem_ aprendizado de máquina automatizado ![SDK sem aprendizado de máquina automatizado instalado no Databricks ](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

    * Se você instalou o SDK do Azure Machine Learning _com_ aprendizado de máquina automatizado ![SDK com aprendizado de máquina automatizado instalado no Databricks ](./media/how-to-configure-environment/automlonadb.jpg)

   Se esta etapa falhar, reinicie o cluster fazendo o seguinte:

    a. No painel esquerdo, selecione **Clusters**. 
   
   b. Na tabela, selecione o nome do cluster. 

   c. Na guia **Bibliotecas**, selecione **Reiniciar**.

1. Baixe o [arquivo morto do notebook do SDK do Azure Machine Learning/Azure Databricks](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/Databricks_AMLSDK_1-4_6.dbc).

   >[!Warning]
   > Muitos notebooks de exemplo estão disponíveis para uso com o serviço do Azure Machine Learning. Apenas [esses notebooks de exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) funcionam com o Azure Databricks.

1.  [Importe o arquivo de arquivo morto](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive) no cluster do Databricks e comece a explorar, conforme descrito na página [Notebooks do Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks).


## <a id="workspace"></a>Criar um arquivo de configuração do workspace

O arquivo de configuração do workspace é um arquivo JSON que informa ao SDK como comunicar-se com o workspace de serviço do Azure Machine Learning. O arquivo é nomeado *config.json* e tem o seguinte formato:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Esse arquivo JSON deve estar na estrutura de diretório que contém os scripts Python ou Jupyter Notebooks. Pode estar no mesmo diretório, um subdiretório nomeado *aml_config* ou em um diretório pai.

Para usar esse arquivo de seu código, use `ws=Workspace.from_config()`. Esse código carrega as informações do arquivo e se conecta ao seu workspace.

É possível criar o arquivo de configuração de três maneiras:

* **Siga o [início rápido do Azure Machine Learning](quickstart-get-started.md)**: Um arquivo *config.json* é criado na biblioteca do Azure Notebooks. O arquivo contém as informações de configuração do workspace. É possível baixar ou copiar o *config.json* para outros ambientes de desenvolvimento.

* **Crie o arquivo manualmente**: Com esse método, você usa um editor de texto. É possível localizar os valores que entram no arquivo de configuração, visitando o workspace no [portal do Azure](https://portal.azure.com). Copie os valores de nome do workspace, grupo de recursos e ID de assinatura e use-os no arquivo de configuração.

     ![Portal do Azure](./media/how-to-configure-environment/configure.png)

* **Crie o arquivo programaticamente**: No snippet de código a seguir, você conecta um workspace, fornecendo a ID de assinatura, grupo de recursos e nome do workspace. Em seguida, salva a configuração do workspace no arquivo:

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

    Este código grava o arquivo de configuração no arquivo *aml_config/config.json*.

## <a name="next-steps"></a>Próximas etapas

- [Treinar um modelo](tutorial-train-models-with-aml.md) no Azure Machine Learning com o conjunto de dados do MNIST
- Veja a referência do [SDK do Azure Machine Learning para Python](https://aka.ms/aml-sdk)
- Saiba mais sobre o [SDK de preparação de dados do Azure Machine Learning](https://aka.ms/data-prep-sdk)
