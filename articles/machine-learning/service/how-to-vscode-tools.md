---
title: Usar o Visual Studio Code para o machine learning
titleSuffix: Azure Machine Learning service
description: Saiba como instalar o Azure Machine Learning para Visual Studio Code e criar um experimento simples no Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 19873256f8253fff75cfd42df7b876106a9e98e5
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65464785"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Introdução ao Azure Machine Learning para Visual Studio Code

Neste artigo, você aprenderá a usar a extensão para o Azure Machine Learning para Visual Studio Code para treinar e implantar o aprendizado de máquina e de aprendizado modelos usando o serviço de Azure Machine Learning no Visual Studio Code.

O serviço de Azure Machine Learning fornece suporte para testes que são executados localmente e destinos de computação remota. Em cada experimento, é possível acompanhar várias execuções, já que muitas vezes é preciso testar iterativamente diferentes técnicas, hiperparâmetros e muito mais. Você pode usar o Azure Machine Learning para acompanhar métricas personalizadas e execuções de experimento, possibilitando a capacidade de reprodução e auditoria da ciência de dados.

Você também pode implantar esses modelos para suas necessidades de teste e produção.

## <a name="prerequisites"></a>Pré-requisitos

+ Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente o [versão gratuita ou paga do serviço Azure Machine Learning](https://aka.ms/AMLFree).

+ O Visual Studio Code deve ser instalado. Visual Studio Code é um editor de código-fonte leve mas poderoso que é executado em sua área de trabalho. Ele vem com suporte interno para o Python e outras linguagens de programação. Se você ainda não tiver instalado o Visual Studio Code [Descubra como](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Instalar o Python 3.5 ou posterior](https://www.anaconda.com/download/).


## <a name="install-the-extension-for-azure-machine-learning-for-visual-studio-code"></a>Instalar a extensão para o Azure Machine Learning para Visual Studio Code

Quando você instala a extensão do Azure Machine Learning, duas extensões mais são instaladas automaticamente (se você tiver acesso à internet). Eles são os [extensão conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) e o [extensão do Python de Microsoft](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

Para trabalhar com o Azure Machine Learning, você precisa transformar o código do Visual Studio em um ambiente de desenvolvimento do Python-integrado (IDE). Você precisa usar a extensão de Python de Microsoft [Python no Visual Studio Code](https://code.visualstudio.com/docs/languages/python). Esta extensão é instalada automaticamente com a extensão do Azure Machine Learning. A extensão faz um excelente IDE com Visual Studio Code e funciona em qualquer sistema operacional com uma variedade de interpretadores do Python. A extensão do Python de Microsoft usa toda a potência do Visual Studio Code para fornecer preenchimento automático, IntelliSense, linting, depuração e teste de unidade. A extensão também permite que você alterne facilmente entre ambientes de Python, incluindo virtual e os ambientes do conda. Para obter mais informações sobre como editar, executar e depurar o código do Python, consulte o [tutorial do Python Olá, mundo](https://code.visualstudio.com/docs/python/python-tutorial).

Para instalar a extensão do Azure Machine Learning:

1. Abra o Visual Studio Code.

1. Em um navegador da web, vá para [do Azure Machine Learning para a extensão do Visual Studio Code (visualização)](https://aka.ms/vscodetoolsforai).

1. Na página da web, selecione **instalar**. 

1. Na guia de extensão, selecione **instalar**.

1. Uma guia de boas-vinda para a extensão é aberta no Visual Studio Code e o símbolo do Azure (descrito em vermelho na captura de tela a seguir) é adicionado à barra de atividade.

   ![Ícone do Azure, na barra de atividade de código do Visual Studio](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. Na caixa de diálogo, selecione **entrar** e siga os prompts para autenticar com o Azure. 
   
   A extensão de conta do Azure, que foi instalada junto com o Azure Machine Learning para a extensão do Visual Studio Code, ajuda você a se autenticar com sua conta do Azure. Para obter uma lista de comandos, consulte a página para o [extensão conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

> [!Tip] 
> Confira a [extensão do IntelliCode para Visual Studio Code (visualização)](https://go.microsoft.com/fwlink/?linkid=2006060). IntelliCode fornece um conjunto de recursos assistido por IA para o IntelliSense no Python, como inferir o autocompletions mais relevantes com base no contexto de código atual.

## <a name="install-the-azure-machine-learning-sdk"></a>Instalar a SDK de aprendizado de máquina do Azure

1. Certifique-se de que o Python 3.5 ou posterior está instalado e é reconhecido pelo Visual Studio Code. Se você instalá-lo agora, reinicie o Visual Studio Code e [selecionar um interpretador do Python](https://code.visualstudio.com/docs/python/python-tutorial).

1. Na janela do terminal integrada, especifique o interpretador do Python para usar. Ou pressione Enter para usar seu interpretador de Python padrão.

   ![Escolha o interpretador](./media/vscode-tools-for-ai/python.png)

1. No canto inferior direito da janela, uma notificação será exibida, indicando que o SDK do Azure Machine Learning está sendo instalado automaticamente. O ambiente de Python recém-criado é local e privado, e ele possui os pré-requisitos do Visual Studio Code para trabalhar com o serviço de Azure Machine Learning.

   ![Instalar o SDK do Azure Machine Learning para Python](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>Introdução ao Azure Machine Learning

Antes de iniciar o treinamento e implantar modelos de aprendizado de máquina no Visual Studio Code, você precisará criar uma [espaço de trabalho de serviço de Azure Machine Learning](concept-azure-machine-learning-architecture.md#workspace) na nuvem. Este espaço de trabalho irá conter seus modelos e recursos. 

Para criar um espaço de trabalho e adicione seu primeiro experimento:

1. Na barra de atividade de código do Visual Studio, selecione o ícone do Azure. A barra lateral Azure Machine Learning é exibida.

   [![Criar um espaço de trabalho](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. Clique com o botão direito do mouse em sua assinatura do Azure e selecione **Criar Espaço de Trabalho**. É exibida uma lista. Na imagem animada de exemplo, é o nome da assinatura **avaliação gratuita**, e é o espaço de trabalho **TeamWorkspace**. 

1. Selecione um grupo de recursos na lista ou crie um novo usando o assistente na paleta de comandos.

1. No campo, digite um nome exclusivo e claro para o novo espaço de trabalho. Imagem de exemplo, o espaço de trabalho é denominado **TeamWorkspace**.

1. Selecione Enter para criar novo espaço de trabalho. Ele aparece na árvore, abaixo do nome da assinatura.

1. Clique com botão direito do **experimento** nó e escolha **criar experimento** no menu de contexto.  Os experimentos acompanham suas execuções usando o Azure Machine Learning.

1. No campo, insira um nome para o seu teste. As capturas de tela de exemplo, o teste é denominado **MNIST**.
 
1. Selecione Enter para criar o novo teste. O teste é exibido na árvore, abaixo do nome do espaço de trabalho.

1. No espaço de trabalho, você pode clique com botão direito um experimento para defini-lo como o **Active** experimentar. O **Active** experimento é seu teste atual. Sua pasta aberta no Visual Studio Code será vinculada a esse teste na nuvem. Essa pasta deve conter seus scripts do Python locais.

Agora suas métricas-chave serão armazenadas no histórico de teste. Da mesma forma, os modelos que você treina serão automaticamente carregados no Azure Machine Learning e armazenados junto com seus logs e métricas de teste. 

[![Anexar uma pasta no Visual Studio Code](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


## <a name="create-and-manage-compute-targets"></a>Criar e gerenciar destinos de computação

Com o Azure Machine Learning para Visual Studio Code, preparar seus dados, treinar modelos e implantá-los localmente e em destinos de computação remota.

A extensão dá suporte a vários destinos de computação remota para o Azure Machine Learning. Para obter mais informações, consulte a lista completa de com suporte [destinos de computação do Azure Machine Learning](how-to-set-up-training-targets.md).

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>Criar destinos de computação do Azure Machine Learning no Visual Studio Code

Para criar um destino de computação:

1. Na barra de atividade de código do Visual Studio, selecione o ícone do Azure. A barra lateral Azure Machine Learning é exibida.

2. No modo de exibição de árvore, expanda sua assinatura do Azure e o espaço de trabalho do Azure Machine Learning. Na imagem de exemplo a seguir, é o nome da assinatura **avaliação gratuita**, e é o espaço de trabalho **TeamWorkspace**. 

3. No nó do espaço de trabalho, clique com o botão direito do mouse no nó **Computação** e escolha **Criar Computação**.

4. Escolha o tipo de destino de computação na lista. 

5. Na paleta de comandos, selecione um tamanho de máquina virtual.

6. Na paleta de comandos, no campo, insira um nome para o destino de computação. 

7. No arquivo de configuração JSON é aberto em uma nova guia, especifica as propriedades avançadas. Você pode especificar as propriedades, como uma contagem máxima do nó.

8. Quando você terminar de configurar o destino de computação, no canto inferior direito da janela, selecione **enviar**.

Aqui está um exemplo de como criar uma computação de Azure Machine Learning (AMLCompute):

[![Criar a computação do AML no Visual Studio Code](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>O arquivo de configuração de execução

A extensão de código do Visual Studio cria automaticamente um destino de computação local e as configurações de execução para seus ambientes local e o docker em seu computador local. Você pode encontrar os arquivos de configuração de execução sob o nó de destino de computação associados. 

## <a name="train-and-tune-models"></a>Treinar e ajustar modelos

Use o Azure Machine Learning para Visual Studio Code (visualização) para iterar no código, percorrer e depurar e usar sua solução para controle do código-fonte rapidamente. 

Para executar seu teste localmente usando o Azure Machine Learning:

1. Na barra de atividade de código do Visual Studio, selecione o ícone do Azure. A barra lateral Azure Machine Learning é exibida.

1. No modo de exibição de árvore, expanda sua assinatura do Azure e o espaço de trabalho do Azure Machine Learning. 

1. Sob o nó de espaço de trabalho, expanda o **Compute** nó e o botão direito do mouse a **configuração de execução** da computação que você deseja usar. 

1. Selecione **Executar Experimento**.

1. No Gerenciador de arquivos, selecione o script que você deseja executar. 

1. Selecione **exibição de teste executado** para ver o portal do Azure Machine Learning integrado para monitorar as execuções e ver seus modelos treinados.

Aqui está um exemplo de como executar um experimento localmente:

[![Executar um experimento localmente](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-visual-studio-code"></a>Usar serviços de computação remotos para testes no Visual Studio Code

Para usar um destino de computação remota para treinamento, você precisa criar um arquivo de configuração de execução. Esse arquivo informa ao Azure Machine Learning não apenas onde executar o experimento, mas também como preparar o ambiente.

#### <a name="the-conda-dependencies-file"></a>O arquivo de dependências conda

Por padrão, um novo ambiente do conda é criado para você e suas dependências de instalação são gerenciadas. No entanto, você deve especificar as suas dependências e suas versões na *aml_config* arquivo. 

O trecho a seguir do padrão *aml_config* especifica `tensorflow=1.12.0`. Se você não especificar a versão da dependência, a versão mais recente será usada. É possível adicionar outras dependências no arquivo de configuração.

```yaml
# The dependencies defined in this file will be automatically provisioned for runs with userManagedDependencies=False.

name: project_environment
dependencies:
  # The python interpreter version.

  # Currently Azure ML only supports 3.5.2 and later.

- python=3.6.2
- tensorflow=1.12.0

- pip:
    # Required packages for AzureML execution, history, and data preparation.

  - --index-url https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
  - --extra-index-url https://pypi.python.org/simple
  - azureml-defaults

```

Para executar o destino de computação seu experimento com o Azure Machine Learning em um controle remoto:

1. Na barra de atividade de código do Visual Studio, selecione o ícone do Azure. A barra lateral Azure Machine Learning é exibida.

1. No modo de exibição de árvore, expanda sua assinatura do Azure e o espaço de trabalho do Azure Machine Learning. 

1. Na janela do editor, o script Python com o botão direito e selecione **AML: executar como experimento no Azure**. 

1. Na paleta de comandos, selecione o destino de computação. 

1. Na paleta de comandos, no campo, insira o nome de configuração de execução. 

1. Editar o *conda_dependencies* arquivo para especificar dependências de tempo de execução do experimento. Em seguida, no canto inferior direito da janela, selecione **enviar**. 

1. Selecione **exibição de teste executado** para ver o portal do Azure Machine Learning integrado para monitorar as execuções e ver seus modelos treinados.

Aqui está um exemplo de como executar um teste em um destino de computação remota:

[![Executar um teste em um destino remoto](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>Implantar e gerenciar modelos
No Azure Machine Learning, você pode implantar e gerenciar seus modelos de machine learning na nuvem e na borda. 

### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>Registrar seu modelo de aprendizado de máquina do Azure do Visual Studio Code

Agora que você treinou seu modelo, você pode registrá-lo em seu espaço de trabalho. Você pode acompanhar e implantar modelos registrados.

Para registrar seu modelo:

1. Na barra de atividade de código do Visual Studio, selecione o ícone do Azure. A barra lateral Azure Machine Learning é exibida.

1. No modo de exibição de árvore, expanda sua assinatura do Azure e o espaço de trabalho do Azure Machine Learning.

1. No nó do espaço de trabalho, clique com o botão direito do mouse em **Modelos** e escolha **Registrar Modelo**.

1. Na paleta de comandos, no campo, insira um nome de modelo. 

1. Na lista, escolha se deseja carregar um **arquivo de modelo** (para modelos de únicos) ou um **pasta de modelo** (para modelos com vários arquivos, como o TensorFlow). 

1. Selecione sua pasta ou arquivo.

1. Quando você terminar de configurar suas propriedades de modelo, no canto inferior direito da janela, selecione **enviar**. 

Aqui está um exemplo de como registrar seu modelo de Azure Machine Learning:

[![Registrar um modelo para AML](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-visual-studio-code"></a>Implantar o serviço do Visual Studio Code

No Visual Studio Code, você pode implantar seu serviço web:
+ Instâncias de contêiner do Azure (ACI) para teste.
+ Serviço do Azure Kubernetes (AKS) para a produção.

Você não precisa criar um contêiner ACI para testar com antecedência, porque os contêineres ACI são criados em tempo real. No entanto, você precisa configurar clusters AKS com antecedência. Para obter mais informações, consulte [implantar modelos com o serviço de Azure Machine Learning](how-to-deploy-and-where.md).

Para implantar um serviço web:

1. Na barra de atividade de código do Visual Studio, selecione o ícone do Azure. A barra lateral Azure Machine Learning é exibida.

1. No modo de exibição de árvore, expanda sua assinatura do Azure e o espaço de trabalho do Azure Machine Learning.

1. No nó do espaço de trabalho, expanda o nó **Modelos**.

1. Com o modelo que você deseja implantar e escolha o botão direito **implantar o serviço do modelo registrado** no menu de contexto.

1. Na paleta de comandos, escolha o destino de computação que você deseja implantar. 

1. Na paleta de comandos, no campo, insira um nome para este serviço.  

1. Na paleta de comandos, selecione a tecla Enter no teclado para procurar e selecionar o arquivo de script.

1. Na paleta de comandos, selecione a tecla Enter no teclado para procurar e selecionar o arquivo de dependência conda.

1. Quando você terminar de configurar as propriedades do serviço, no canto inferior direito da janela, selecione **enviar** para implantar. No arquivo de propriedades de serviço, você pode especificar um arquivo de docker local ou um arquivo Schema JSON.

O serviço Web está implantado.

Aqui está um exemplo de como implantar um serviço web:

[![Implantar um serviço web](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Usar atalhos do teclado

Você pode usar o teclado para acessar os recursos de aprendizado de máquina do Azure no Visual Studio Code. O atalho de teclado mais importante a saber é Ctrl + Shift + P, que exibe a paleta de comandos. Na paleta de comandos, você tem acesso a toda a funcionalidade do Visual Studio Code, incluindo atalhos de teclado para as operações mais comuns.

[![Atalhos de teclado para o Azure Machine Learning para Visual Studio Code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Próximas etapas

* Para obter uma explicação de como treinar com o Azure Machine Learning fora do Visual Studio Code, consulte [Tutorial: Treinar modelos com o Azure Machine Learning](tutorial-train-models-with-aml.md).
* Para obter uma explicação de como editar, executar e depurar o código localmente, consulte o [tutorial do Python Olá, mundo](https://code.visualstudio.com/docs/python/python-tutorial).
