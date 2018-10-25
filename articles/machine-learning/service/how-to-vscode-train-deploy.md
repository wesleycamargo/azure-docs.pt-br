---
title: Usar a extensão Visual Studio Code Tools for AI com o Azure Machine Learning
description: Aprenda sobre a Visual Studio Code Tools for AI e como iniciar treinamento e implantar os modelos de aprendizado de máquina e aprendizagem profunda com o serviço do Azure Machine Learning no VS Code.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: jmartens
author: j-martens
ms.reviewer: jmartens
ms.date: 10/1/2018
ms.openlocfilehash: 3a5ee8795dabcbf9e35e16a8ba0f9c0eb5490ead
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48240370"
---
# <a name="vs-code-tools-for-ai-train-and-deploy-ml-models-from-vs-code"></a>VS Code Tools for AI: treinar e implantar modelos de ML pelo VS Code
Neste artigo, você aprenderá como usar a extensão **VS Code Tools for AI** para treinar e implantar os modelos de aprendizado de máquina e aprendizagem profunda com o serviço do Azure Machine Learning no VS Code.

O Azure Machine Learning oferece suporte para a execução de experimentos localmente e em destinos de computação remota. Em cada experimento, é possível acompanhar várias execuções, já que muitas vezes é preciso testar iterativamente diferentes técnicas, hiperparâmetros e muito mais. Você pode usar o Azure Machine Learning para acompanhar métricas personalizadas e execuções de experimento, possibilitando a capacidade de reprodução e auditoria da ciência de dados.

Além disso, é possível implantar esses modelos para suas necessidades de teste e produção.

## <a name="prerequisites"></a>Pré-requisitos

+ [Configure a VS Code Tools for AI](how-to-vscode-tools.md) para o Azure Machine Learning.

+ Instale o [SDK do Azure Machine Learning para Python](how-to-vscode-tools.md) com VS Code.

+ Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-and-manage-compute-targets"></a>Criar e gerenciar destinos de computação

Com a Visual Studio Code Tools for AI, você pode preparar seus dados, treinar modelos e implantá-los localmente e em destinos de computação remota.

Essa extensão é compatível com vários destinos de computação remota diferentes para o Azure Machine Learning. Consulte a [lista completa de destinos de computação compatíveis](how-to-set-up-training-targets.md) do Azure Machine Learning.

### <a name="create-compute-targets-for-azure-machine-learning-in-vs-code"></a>Criar destinos de computação para Azure Machine Learning no VS Code

**Para criar um destino de computação:**

1. Clique no ícone do Azure na barra de atividades do Visual Studio Code. A barra lateral Azure: Machine Learning é exibida.

2. No modo de exibição de árvore, expanda sua assinatura do Azure e o espaço de trabalho do Azure Machine Learning. Na imagem animada, o nome da assinatura é “OpenMind Studio”, e o espaço de trabalho é “MyWorkspace”. 

3. No nó do espaço de trabalho, clique com o botão direito do mouse no nó **Computação** e escolha **Criar Computação**.

4. Escolha o tipo de destino de computação na lista. 

5. No campo, insira um nome exclusivo para esse destino de computação e especifique o tamanho da máquina virtual.

6. Especifique as propriedades avançadas no arquivo de configuração JSON que é aberto em uma nova guia. 

7. Ao terminar a configuração do seu destino de computação, clique em **Concluir** no canto inferior direito.

Veja aqui um exemplo da IA do Lote do Azure: [![Criar computação da IA do Lote do Azure no VS Code](./media/vscode-tools-for-ai/createcompute.gif)](./media/vscode-tools-for-ai/createcompute.gif#lightbox)

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

Use o Azure Machine Learning do VS Code para fazer iteração rapidamente no seu código, percorrê-lo, depurá-lo e usar sua solução preferencial de controle do código-fonte. 

**Para executar seu experimento com o Azure Machine Learning:**

1. Clique no ícone do Azure na barra de atividades do Visual Studio Code. A barra lateral Azure: Machine Learning é exibida.

1. No modo de exibição de árvore, expanda sua assinatura do Azure e o espaço de trabalho do Azure Machine Learning. Na imagem animada, o nome da assinatura é “OpenMind Studio”, e o espaço de trabalho é “MyWorkspace”. 

1. No nó do espaço de trabalho, expanda o nó **Computação** e clique com o botão direito do mouse em **Executar Configuração** da computação que você deseja usar. 

1. Selecione **Executar Experimento**.

1. Clique em **Exibir Execução do Experimento** para ver o portal do Azure Machine Learning integrado, monitorar as execuções e ver os modelos treinados.

   [![Executar o experimento de aprendizado de máquina pelo VS Code](./media/vscode-tools-for-ai/runexperiment.gif)](./media/vscode-tools-for-ai/runexperiment.gif#lightbox)

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

1. Use a caixa de diálogo do seletor de arquivo para selecionar o arquivo ou a pasta.

   [computação![](./media/vscode-tools-for-ai/registermodel.gif)](./media/vscode-tools-for-ai/registermodel.gif#lightbox)

> [!Warning]
> Por enquanto, remova as marcas do arquivo json gerado.

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

1. Na paleta de comandos do VS Code, escolha o destino de computação para o qual você deseja implantar. 

1. No campo, insira um nome para esse serviço. 

1. Na caixa de diálogo, no canto inferior direito, clique em **Procurar** e selecione seu script de pontuação. A caixa de diálogo é fechada.

1. Se você tiver um arquivo de Docker local, clique em **Procurar** na segunda caixa de diálogo exibida. 

   Se você cancelar a caixa de diálogo e não especificar um arquivo de Docker local, um do “Azure Machine Learning” será usado por padrão.

1. Na terceira caixa de diálogo exibida, clique em **Procurar** e selecione o caminho do arquivo conda local ou insira o caminho do arquivo no editor de json mais tarde.

1. Se houver um arquivo schema.json que você queira usar, clique em **Procurar** na quarta caixa de diálogo exibida e selecione o arquivo.

O serviço Web está implantado.

Veja aqui um exemplo para a Instância de Contêiner do Azure: [![Instância do Contêiner do Azure do VS Code](./media/vscode-tools-for-ai/deploy.gif)](./media/vscode-tools-for-ai/deploy.gif#lightbox)

## <a name="next-steps"></a>Próximas etapas

Para ver uma demonstração de treinamento com o Machine Learning fora do VS Code, leia [Tutorial: treinar modelos com o Azure Machine Learning](tutorial-train-models-with-aml.md).

Para um passo a passo da edição, execução e depuração de código localmente, confira o [Tutorial “Olá, Mundo” do Python](https://code.visualstudio.com/docs/languages/python/docs/python/python-tutorial)
