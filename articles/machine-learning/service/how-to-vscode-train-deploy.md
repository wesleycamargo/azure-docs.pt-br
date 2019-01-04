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
ms.openlocfilehash: 0910cce92c410a68dce6e2c44d29e72e594cd153
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53271701"
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

1. Clique no ícone do Azure na barra de atividades do Visual Studio Code. A barra lateral Azure: Machine Learning é exibida.

2. No modo de exibição de árvore, expanda sua assinatura do Azure e o espaço de trabalho do Azure Machine Learning. Na imagem animada, o nome da assinatura é 'Avaliação gratuita' e o workspace é 'TeamWorkspace'. 

3. No nó do espaço de trabalho, clique com o botão direito do mouse no nó **Computação** e escolha **Criar Computação**.

4. Escolha o tipo de destino de computação na lista. 

5. Especifique as propriedades avançadas no arquivo de configuração JSON que é aberto em uma nova guia. Você pode especificar um nome exclusivo para o destino de computação neste arquivo.

6. Ao terminar a configuração do seu destino de computação, clique em **Enviar** no canto inferior direito.

Aqui está um exemplo de Computação do Azure Machine Learning (AMLCompute): [![Criar a Computação do AML no VS Code](./media/vscode-tools-for-ai/CreateAMLCompute.gif)](./media/vscode-tools-for-ai/CreateAMLCompute.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-vs-code"></a>Usar serviços de computação remotos para experimentos no VS Code

Para usar um destino de computação remota durante o treinamento, crie um arquivo de configuração de execução. Esse arquivo informa ao Azure Machine Learning não apenas onde executar o experimento, mas também como preparar o ambiente.

#### <a name="the-run-configuration-file"></a>O arquivo de “configuração de execução”

A extensão do VS Code criará automaticamente uma configuração de execução para os ambientes **local** e **docker** no seu computador local.

Trata-se de um trecho de código do arquivo de configuração de execução padrão.

Se você quiser instalar todas as suas bibliotecas/dependências por conta própria, defina `userManagedDependencies: True` e então as execuções de experimento locais usarão seu ambiente padrão do Python conforme especificado pela extensão Python do VS Code.

```yaml
# user_managed_dependencies=True indicates that the environment will be user managed. False indicates that AzureML will manage the user environment.
    userManagedDependencies: False
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

- pip:
    # Required packages for AzureML execution, history, and data preparation.

  - --index-url https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
  - --extra-index-url https://pypi.python.org/simple
  - azureml-defaults

```

## <a name="train-and-tune-models"></a>Treinar e ajustar modelos

Use o Azure Machine Learning para VS Code (Versão Prévia) para fazer iteração rapidamente no seu código, percorrê-lo, depurá-lo e usar sua solução preferencial de controle do código-fonte. 

**Para executar seu experimento com o Azure Machine Learning:**

1. Clique no ícone do Azure na barra de atividades do Visual Studio Code. A barra lateral Azure: Machine Learning é exibida.

1. No modo de exibição de árvore, expanda sua assinatura do Azure e o espaço de trabalho do Azure Machine Learning. 

1. No nó do espaço de trabalho, expanda o nó **Computação** e clique com o botão direito do mouse em **Executar Configuração** da computação que você deseja usar. 

1. Selecione **Executar Experimento**.

1. Clique em **Exibir Execução do Experimento** para ver o portal do Azure Machine Learning integrado, monitorar as execuções e ver os modelos treinados.

## <a name="deploy-and-manage-models"></a>Implantar e gerenciar modelos
O Azure Machine Learning permite implantar e gerenciar modelos de aprendizado de máquina na nuvem e na borda. 

### <a name="register-your-model-to-azure-machine-learning-from-vs-code"></a>Registrar seu modelo no Azure Machine Learning pelo VS Code

Agora que você já treinou seu modelo, pode registrá-lo em seu espaço de trabalho.
Os modelos registrados podem ser controlados e implantados.

**Para registrar seu modelo:**

1. Clique no ícone do Azure na barra de atividades do Visual Studio Code. A barra lateral Azure: Machine Learning é exibida.

1. No modo de exibição de árvore, expanda sua assinatura do Azure e o espaço de trabalho do Azure Machine Learning.

1. No nó do espaço de trabalho, clique com o botão direito do mouse em **Modelos** e escolha **Registrar Modelo**.

1. Na lista, escolha se você deseja carregar um **arquivo de modelo** (para modelos únicos), uma **pasta de modelo** (para modelos com vários arquivos, como o Tensorflow). 

1. Selecione sua pasta ou arquivo.

1. Quando você termina de configurar as propriedades do modelo, clique em **Enviar** no canto inferior direito. 



### <a name="deploy-your-service-from-vs-code"></a>Implantar o serviço pelo VS Code

Usando o VS Code, você pode implantar seu serviço Web nos seguintes locais:
+ ACI (Instância do Contêiner do Azure): para teste
+ AKS (Serviço do Kubernetes do Azure): para produção 

Não será preciso criar um contêiner ACI para testar com antecedência, pois eles são criados em tempo real. No entanto, clusters AKS precisam ser configurados com antecedência. 

Saiba mais sobre [como implantar com Azure Machine Learning](how-to-deploy-and-where.md) em geral.

**Para implantar um serviço Web:**

1. Clique no ícone do Azure na barra de atividades do Visual Studio Code. A barra lateral Azure: Machine Learning é exibida.

1. No modo de exibição de árvore, expanda sua assinatura do Azure e o espaço de trabalho do Azure Machine Learning.

1. No nó do espaço de trabalho, expanda o nó **Modelos**.

1. Clique com o botão direito do mouse no modelo que você deseja implantar e escolha o comando **Implantar Serviço do Modelo Registrado** no menu de contexto.

1. Na paleta de comandos, escolha o destino de computação para o qual você deseja implantar. 

1. No campo, insira um nome para esse serviço. 

1. Na Paleta de Comandos, pressione a tecla Enter no teclado para navegar e selecionar o arquivo de script.

1. Na Paleta de Comandos, pressione a tecla Enter no teclado para navegar e selecione o arquivo de dependência Conda.

1. Quando você terminar de configurar as propriedades do serviço, clique em **Enviar** no canto inferior direito. Nesse arquivo de propriedades do serviço, você pode especificar um arquivo do Docker local ou um arquivo schema.json que você talvez queira usar.

O serviço Web está implantado.

## <a name="next-steps"></a>Próximas etapas

Para obter uma explicação passo a passo do treinamento com Machine Learning fora do VS Code, leia o [Tutorial: Treinar modelos com o Azure Machine Learning](tutorial-train-models-with-aml.md).

Para um passo a passo da edição, execução e depuração de código localmente, confira o [Tutorial “Olá, Mundo” do Python](https://code.visualstudio.com/docs/python/python-tutorial)
