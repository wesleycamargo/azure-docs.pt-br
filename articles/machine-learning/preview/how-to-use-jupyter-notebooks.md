---
title: Como usar Notebooks Jupyter no Azure Machine Learning Workbench | Microsoft Docs
description: Um guia para o uso do recurso de Notebook Jupyter do Azure Machine Learning Workbench
services: machine-learning
author: rastala
ms.author: roastala
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/09/2017
ms.openlocfilehash: c21b7096f689efedacd6e7d55d83912d35dff803
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2018
---
# <a name="use-jupyter-notebooks-in-azure-machine-learning-workbench"></a>Usar Notebooks Jupyter no Azure Machine Learning Workbench

O Azure Machine Learning Workbench tem suporte para experimentação de ciência de dados interativa por meio da integração com os Notebooks Jupyter. Este artigo descreve como usar de maneira eficiente esse recurso para aumentar a velocidade e a qualidade da sua experimentação de ciência de dados interativa.

## <a name="prerequisites"></a>pré-requisitos
- [Criar contas do Azure Machine Learning e instalar o Azure Machine Learning Workbench](quickstart-installation.md).
- Familiarize-se com o [Notebook Jupyter](http://jupyter.org/). Este artigo não pretende ensinar a usar o Jupyter.

## <a name="jupyter-notebook-architecture"></a>Arquitetura do Notebook Jupyter
De um modo geral, a arquitetura do Notebook Jupyter inclui três componentes. Cada um pode ser executado em ambientes de computação diferentes:

- **Cliente**: recebe entrada do usuário e exibe a saída renderizada.
- **Servidor**: servidor Web que hospeda os arquivos do bloco de anotações (arquivos .ipynb).
- **Kernel**: o ambiente de tempo de execução em que ocorre a execução das células do bloco de anotações.

Para saber mais, veja a [documentação do Jupyter](http://jupyter.readthedocs.io/en/latest/architecture/how_jupyter_ipython_work.html). A seguir está um diagrama que ilustra como esse cliente, servidor e arquitetura de kernel são mapeados para os componentes no Azure Machine Learning:

![Arquitetura do Notebook Jupyter](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-architecture.png)

## <a name="kernels-in-azure-machine-learning-workbench-notebooks"></a>Kernels nos blocos de anotações do Azure Machine Learning Workbench
Você pode acessar kernels diferentes no Azure Machine Learning Workbench simplesmente definindo configurações de execução e destinos de computação na pasta `aml_config` do seu projeto. Adicionando um novo destino de computação emitindo o comando `az ml computetarget attach` é equivalente a adicionar um novo kernel.

>[!NOTE]
>Leia [Configurando o serviço de Experimentação do Azure Machine Learning](experimentation-service-configuration.md) para obter mais detalhes sobre configurações de execução e destinos de computação.

### <a name="kernel-naming-convention"></a>Convenção de nomenclatura do kernel
O Azure Machine Learning Workbench gera kernels do Jupyter personalizados. Esses kernels se chamam *\<project name> \<run config name>*. Por exemplo, se você tiver uma configuração de execução chamada _docker-python_ em um projeto chamado _myIris_, o Azure Machine Learning disponibiliza um kernel chamado *myIris docker-python.* Defina o kernel em execução do menu **Kernel** do Jupyter Notebook, no submenu **Alterar kernel**. O nome do kernel em execução é exibido na extremidade direita da barra de menus.
 
Atualmente, o Azure Machine Learning Workbench tem suporte para os tipos de kernels a seguir.

### <a name="local-python-kernel"></a>Kernel Python local
Esse kernel Python dá suporte à execução em computadores locais. Ele é integrado ao suporte do Histórico de Execução do Azure Machine Learning. O nome do kernel normalmente é *my_project_name local.*

>[!NOTE]
>Não use o kernel Python 3. Ele é um kernel autônomo fornecido pelo Jupyter por padrão e não está integrado com os recursos do Azure Machine Learning. Por exemplo, as funções mágicas `%azureml` do Jupyter retornam erros "não encontrados". 

### <a name="python-kernel-in-docker-local-or-remote"></a>Kernel Python no Docker (local ou remoto)
Esse kernel Python é executado em um contêiner do Docker em seu computador local ou em uma máquina virtual do Linux remota. O nome do kernel normalmente é *my_project docker.* O arquivo `docker.runconfig` associado tem o campo `Framework` definido como `Python`.

### <a name="pyspark-kernel-in-docker-local-or-remote"></a>Kernel PySpark no Docker (local ou remoto)
Este kernel PySpark executa scripts em um contexto do Spark em execução dentro de um contêiner do Docker, seja no computador local ou em uma VM Linux remota. O nome do kernel normalmente é *my_project docker.* O arquivo `docker.runconfig` associado tem o campo `Framework` definido como `PySpark`.

### <a name="pyspark-kernel-in-an-azure-hdinsight-cluster"></a>Kernel PySpark em um cluster HDInsight
Esse kernel é executado no cluster do Azure HDInsight remoto conectado como um destino de computação para seu projeto. O nome do kernel normalmente é *my_project my_hdi.* 

>[!IMPORTANT]
>No arquivo `.compute` do destino de computação HDI, você deve alterar o campo `yarnDeployMode` para `client` (o valor padrão é `cluster`) para usar este kernel. 

## <a name="start-a-jupyter-server-from-azure-machine-learning-workbench"></a>Iniciar um servidor Jupyter do Azure Machine Learning Workbench
No Azure Machine Learning Workbench, você pode acessar os blocos de anotações pela guia **Blocos de Anotações**. O exemplo de projeto _Íris de Classificação_ inclui um exemplo de bloco de anotações `iris.ipynb`.

![Guia de blocos de anotações](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-01.png)

Quando um bloco de anotações é aberto no Azure Machine Learning Workbench, ele é exibido em sua própria guia do documento no **Modo de Visualização**. É uma exibição somente leitura que não requer um kernel e servidor Jupyter em execução.

![Visualização do Notebook](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-02.png)

Selecionar o botão **Iniciar Servidor do Notebook** inicializa o servidor Jupyter e coloca o Notebook no **Modo de Edição**. A interface do usuário familiar do Notebook Jupyter aparece inserida no Workbench. Agora você pode definir um núcleo do menu **Kernel** e iniciar a sessão interativa do bloco de anotações. 

>[!NOTE]
>Com kernels não locais, pode levar um minuto ou dois para iniciar se você o estiver usando pela primeira vez. Você pode executar o comando `az ml experiment prepare` na janela CLI para preparar o destino de computação para que o kernel inicia muito mais rapidamente depois que o destino de computação estiver preparado.

![Modo de edição](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-04.png)

Esta é uma experiência de Notebook Jupyter totalmente interativa. Há suporte para todas as operações do bloco de notas e atalhos de teclado normais nesta janela, com exceção de algumas operações de arquivo que podem ser executadas por meio da guia **Blocos de Notas** e da guia **Arquivo** do Workbench.

## <a name="start-a-jupyter-server-from-the-command-line"></a>Iniciar o servidor Jupyter da linha de comando
Você também pode inicializar uma sessão do bloco de anotações emitindo um `az ml notebook start` da janela da linha de comando:
```
$ az ml notebook start
[I 10:14:25.455 NotebookApp] The port 8888 is already in use, trying another port.
[I 10:14:25.464 NotebookApp] Serving notebooks from local directory: /Users/johnpelak/Desktop/IrisDemo
[I 10:14:25.465 NotebookApp] 0 active kernels 
[I 10:14:25.465 NotebookApp] The Jupyter Notebook is running at: http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.465 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 10:14:25.466 NotebookApp] 
    
Copy and paste this URL into your browser when you connect for the first time, to login with a token: http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.759 NotebookApp] Accepting one-time-token-authenticated connection from ::1
[I 10:16:52.970 NotebookApp] Kernel started: 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
[I 10:16:53.854 NotebookApp] Adapting to protocol v5.1 for kernel 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
```
Seu navegador padrão é inicializado automaticamente com o servidor Jupyter apontando para o diretório inicial do projeto. Você também pode usar a URL e o token exibidos na janela da CLI para inicializar outras janelas do navegador localmente. 

![Painel do projeto](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-07.png)

Agora você pode selecionar um arquivo do bloco de anotações `.ipynb`, abri-lo, definir o kernel (se não tiver sido definido) e iniciar a sessão interativa.

![Painel do projeto](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-08.png)

## <a name="use-magic-commands-to-manage-experiments"></a>Use os comandos mágicos para gerenciar experiências

Você pode usar [comandos mágicos](http://ipython.readthedocs.io/en/stable/interactive/magics.html) dentro das células de seu notebook para acompanhar o histórico de execução e salvar saídas, como modelos ou conjuntos de dados.

Para controlar o bloco de anotações individual célula é executado, use o comando mágico `%azureml history on`. Após ativar o histórico, cada célula aparecerá como entrada no histórico de execução:

```
%azureml history on
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()
logger.log("Cell","Load Data")
```

Para desativar o controle de execução de célula, use o comando mágico `%azureml history off`.

Você pode usar o comando mágico `%azureml upload` para salvar arquivos de modelo e dados de seu tempo de execução. Os objetos salvos aparecem como saídas no modo de exibição de histórico de execução:

```
modelpath = os.path.join("outputs","model.pkl")
with open(modelpath,"wb") as f:
    pickle.dump(model,f)
%azureml upload outputs/model.pkl
```

>[!NOTE]
>As saídas devem ser salva em uma pasta chamada *saídas.*

## <a name="next-steps"></a>Próximas etapas
- Para saber como usar o Notebook Jupyter, veja a [documentação oficial do Jupyter](http://jupyter-notebook.readthedocs.io/en/latest/).    
- Para obter uma compreensão mais profunda do ambiente de execução de experimentação do Azure Machine Learning, examine [Configuração do serviço de Experimentação do Azure Machine Learning](experimentation-service-configuration.md).

