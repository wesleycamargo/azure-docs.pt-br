---
title: Treinar e implantar modelos do VS Code
titleSuffix: Azure Machine Learning service
description: Saiba mais sobre o Azure Machine Learning para Visual Studio Code e como começar a treinar e implantar modelos de aprendizado profundo e aprendizado de máquina no serviço do Azure Machine Learning usando o Visual Studio Code de aprendizado.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: e7df9086fa5ffc6273a6cb063bdee3cfdfa73e34
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54013308"
---
# <a name="use-visual-studio-code-to-train-and-deploy-machine-learning-models"></a>Usar o Visual Studio Code para treinar e implantar modelos de aprendizado de máquina

Neste artigo, você aprenderá a usar a extensão **Azure Machine Learning para Visual Studio Code** para treinar e implantar modelos de aprendizado profundo e aprendizado de máquina com o serviço do Azure Machine Learning no VS Code (Visual Studio Code).

O Azure Machine Learning oferece suporte para a execução de experimentos localmente e em destinos de computação remota. Em cada experimento, é possível acompanhar várias execuções, já que muitas vezes é preciso testar iterativamente diferentes técnicas, hiperparâmetros e muito mais. Você pode usar o Azure Machine Learning para acompanhar métricas personalizadas e execuções de experimento, possibilitando a capacidade de reprodução e auditoria da ciência de dados.

Além disso, é possível implantar esses modelos para suas necessidades de teste e produção.

## <a name="prerequisites"></a>Pré-requisitos

+ Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Teste hoje mesmo a [versão gratuita ou paga do Serviço do Azure Machine Learning](http://aka.ms/AMLFree).

+ Tenha a extensão [Azure Machine Learning para VS Code](how-to-vscode-tools.md) configurada.

+ Instale o [SDK do Azure Machine Learning para Python](how-to-vscode-tools.md) com VS Code.

## <a name="create-and-manage-compute-targets"></a>Criar e gerenciar destinos de computação

Com o Azure Machine Learning para VS Code, você pode preparar seus dados, treinar modelos e implantá-los tanto localmente quanto em destinos de computação remota.

Essa extensão é compatível com vários destinos de computação remota diferentes para o Azure Machine Learning. Consulte a [lista completa de destinos de computação compatíveis](how-to-set-up-training-targets.md) do Azure Machine Learning.

### <a name="create-compute-targets-for-azure-machine-learning-in-vs-code"></a>Criar destinos de computação para Azure Machine Learning no VS Code

**Para criar um destino de computação:**

1. Clique no ícone do Azure na barra de atividades do Visual Studio Code. A barra lateral Azure Machine Learning é exibida.

2. No modo de exibição de árvore, expanda sua assinatura do Azure e o espaço de trabalho do Azure Machine Learning. Na imagem animada, o nome da assinatura é 'Avaliação gratuita' e o workspace é 'TeamWorkspace'. 

3. No nó do espaço de trabalho, clique com o botão direito do mouse no nó **Computação** e escolha **Criar Computação**.

4. Escolha o tipo de destino de computação na lista. 

5. Na Paleta de Comandos, selecione um tamanho de Máquina Virtual.

6. Na Paleta de Comandos, insira um nome para o destino de computação no campo. 

7. Especifique as propriedades avançadas no arquivo de configuração JSON que é aberto em uma nova guia. Você pode especificar propriedades, como uma contagem máxima de nós.

8. Ao terminar a configuração do seu destino de computação, clique em **Enviar** no canto inferior direito da tela.

Este é um exemplo para criar uma Computação do Microsoft Azure Machine Learning (AMLCompute): [![Criar a Computação do AML no VS Code](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>O arquivo de “configuração de execução”

A extensão do VS Code criará automaticamente um destino de computação local e executará configurações para seus ambientes **local** e **docker** em seu computador local. Os arquivos de configuração de execução podem ser encontrados no destino de computação associado. 

Trata-se de um trecho de código do arquivo de configuração da execução local padrão. Por padrão, `userManagedDependencies: True` então você deve instalar todas as suas bibliotecas/dependências por conta própria e, então, as execuções de experimentos locais usarão seu ambiente padrão do Python conforme especificado pela extensão Python do VS Code.

```yaml
# user_managed_dependencies = True indicates that the environment will be user managed. False indicates that AzureML will manage the user environment.
    userManagedDependencies: True
# The python interpreter path
    interpreterPath: python
# Path to the conda dependencies file to use for this run. If a project
# contains multiple programs with different sets of dependencies, it may be
# convenient to manage those environments with separate files.
    condaDependenciesFile: aml_config/conda_dependencies.yml
# Docker details
    docker:
# Set True to perform this run inside a Docker container.
    enabled: false
```

## <a name="train-and-tune-models"></a>Treinar e ajustar modelos

Use o Azure Machine Learning para VS Code (Versão Prévia) para fazer iteração rapidamente no seu código, percorrê-lo, depurá-lo e usar sua solução preferencial de controle do código-fonte. 

**Para executar seu experimento localmente com o Azure Machine Learning:**

1. Clique no ícone do Azure na barra de atividades do Visual Studio Code. A barra lateral Azure Machine Learning é exibida.

1. No modo de exibição de árvore, expanda sua assinatura do Azure e o espaço de trabalho do Azure Machine Learning. 

1. No nó do espaço de trabalho, expanda o nó **Computação** e clique com o botão direito do mouse em **Executar Configuração** para a computação que você deseja usar. 

1. Selecione **Executar Experimento**.

1. Selecione o script para executar no Explorador de Arquivos. 

1. Clique em **Exibir Execução do Experimento** para ver o portal do Azure Machine Learning integrado, monitorar as execuções e ver os modelos treinados.

Este é um exemplo de como executar um experimento localmente: [![Executar um experimento localmente](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-vs-code"></a>Usar serviços de computação remotos para experimentos no VS Code

Para usar um destino de computação remota durante o treinamento, crie um arquivo de configuração de execução. Esse arquivo informa ao Azure Machine Learning não apenas onde executar o experimento, mas também como preparar o ambiente.

#### <a name="the-conda-dependencies-file"></a>O arquivo de dependências conda

Por padrão, um novo ambiente conda é criado, e suas dependências de instalação são gerenciadas. No entanto, você precisa especificar suas dependências no arquivo `aml_config/conda_dependencies.yml`.

Trata-se de um trecho de código do padrão “aml_config/conda_dependencies.yml”.
É possível adicionar outras dependências no arquivo de configuração.

```yaml
# The dependencies defined in this file will be automatically provisioned for runs with userManagedDependencies=False.

name: project_environment
dependencies:
  # The python interpreter version.

  # Currently Azure ML only supports 3.5.2 and later.

- python=3.6.2
- tensorflow

- pip:
    # Required packages for AzureML execution, history, and data preparation.

  - --index-url https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
  - --extra-index-url https://pypi.python.org/simple
  - azureml-defaults

```

**Para executar seu experimento com o Azure Machine Learning em um destino de computação remoto:**

1. Clique no ícone do Azure na barra de atividades do Visual Studio Code. A barra lateral Azure Machine Learning é exibida.

1. No modo de exibição de árvore, expanda sua assinatura do Azure e o espaço de trabalho do Azure Machine Learning. 

1. Clique com o botão direito em seu script Python na janela do editor e selecione **AML: executar como experimento no Azure**. 

1. Na Paleta de Comandos, selecione o destino de computação. 

1. Na Paleta de Comandos, insira o nome da configuração de execução no campo. 

1. Edite o arquivo conda_dependencies.yml para especificar as dependências de tempo de execução do experimento e, em seguida, clique em **Enviar** no canto inferior direito da tela. 

1. Clique em **Exibir Execução do Experimento** para ver o portal do Azure Machine Learning integrado, monitorar as execuções e ver os modelos treinados.

Este é um exemplo de como executar um experimento em um destino de computação remoto: [![Executar um experimento em um destino remoto](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>Implantar e gerenciar modelos
O Azure Machine Learning permite implantar e gerenciar modelos de aprendizado de máquina na nuvem e na borda. 

### <a name="register-your-model-to-azure-machine-learning-from-vs-code"></a>Registrar seu modelo no Azure Machine Learning pelo VS Code

Agora que você já treinou seu modelo, pode registrá-lo em seu espaço de trabalho.
Os modelos registrados podem ser controlados e implantados.

**Para registrar seu modelo:**

1. Clique no ícone do Azure na barra de atividades do Visual Studio Code. A barra lateral Azure Machine Learning é exibida.

1. No modo de exibição de árvore, expanda sua assinatura do Azure e o espaço de trabalho do Azure Machine Learning.

1. No nó do espaço de trabalho, clique com o botão direito do mouse em **Modelos** e escolha **Registrar Modelo**.

1. Na Paleta de Comandos, insira um nome de modelo no campo. 

1. Na lista, escolha se você deseja carregar um **arquivo de modelo** (para modelos únicos), uma **pasta de modelo** (para modelos com vários arquivos, como o Tensorflow). 

1. Selecione sua pasta ou arquivo.

1. Ao terminar a configuração das propriedades do modelo, clique em **Enviar** no canto inferior direito da tela. 

Este é um exemplo de como registrar seu modelo para AML: [![Registrar um modelo para AML](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-vs-code"></a>Implantar o serviço pelo VS Code

Usando o VS Code, você pode implantar seu serviço Web nos seguintes locais:
+ ACI (Instância do Contêiner do Azure): para teste
+ AKS (Serviço do Kubernetes do Azure): para produção 

Não será preciso criar um contêiner ACI para testar com antecedência, pois eles são criados em tempo real. No entanto, clusters AKS precisam ser configurados com antecedência. 

Saiba mais sobre [como implantar com Azure Machine Learning](how-to-deploy-and-where.md) em geral.

**Para implantar um serviço Web:**

1. Clique no ícone do Azure na barra de atividades do Visual Studio Code. A barra lateral Azure Machine Learning é exibida.

1. No modo de exibição de árvore, expanda sua assinatura do Azure e o espaço de trabalho do Azure Machine Learning.

1. No nó do espaço de trabalho, expanda o nó **Modelos**.

1. Clique com o botão direito do mouse no modelo que você deseja implantar e escolha o comando **Implantar Serviço do Modelo Registrado** no menu de contexto.

1. Na paleta de comandos, escolha o destino de computação para o qual você deseja implantar. 

1. Na Paleta de Comandos, digite um nome para este serviço no campo.  

1. Na Paleta de Comandos, pressione a tecla Enter no teclado para navegar e selecionar o arquivo de script.

1. Na Paleta de Comandos, pressione a tecla Enter no teclado para navegar e selecione o arquivo de dependência Conda.

1. Ao terminar a configuração das propriedades do serviço, clique em **Enviar** no canto inferior direito da tela para implantar. Nesse arquivo de propriedades do serviço, você pode especificar um arquivo do Docker local ou um arquivo schema.json que talvez queira usar.

O serviço Web está implantado.

Este é um exemplo de como implantar um serviço da Web: [![Implantar um serviço da Web](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

## <a name="next-steps"></a>Próximas etapas

Para obter uma explicação passo a passo do treinamento com Machine Learning fora do VS Code, leia o [Tutorial: Treinar modelos com o Azure Machine Learning](tutorial-train-models-with-aml.md).

Para um passo a passo da edição, execução e depuração de código localmente, confira o [Tutorial “Olá, Mundo” do Python](https://code.visualstudio.com/docs/python/python-tutorial)
