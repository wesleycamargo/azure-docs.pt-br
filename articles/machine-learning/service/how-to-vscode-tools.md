---
title: Usar o Visual Studio Code com
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
ms.openlocfilehash: d6f49eb0359db6184b5ecd146d7328a64611a9f9
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55245558"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Introdução ao Azure Machine Learning para Visual Studio Code

Neste artigo, você aprenderá a instalar a extensão **Azure Machine Learning para Visual Studio Code** e criar seu primeiro experimento com o serviço do Azure Machine Learning no VS Code (Visual Studio Code).

Use a extensão Azure Machine Learning no Visual Studio Code para usar o serviço do Azure Machine Learning para preparação de dados, treinamento e teste de modelos de aprendizado de máquina no local e em destinos de computação remotos, implantar esses modelos e acompanhar experimentos e métricas personalizadas.

## <a name="prerequisite"></a>Pré-requisito


+ Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Teste hoje mesmo a [versão gratuita ou paga do Serviço do Azure Machine Learning](http://aka.ms/AMLFree).

+ O Visual Studio Code deve ser instalado. O VS Code é um editor de código-fonte leve, mas poderoso que é executado na área de trabalho. Ele vem com suporte interno para o Python e muito mais.  [Saiba como instalar o VS Code](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Instale o Python 3.5 ou superior](https://www.anaconda.com/download/).


## <a name="install-the-azure-machine-learning-for-vs-code-extension"></a>Instalar a extensão Azure Machine Learning para VS Code

Quando você instala a extensão **Azure Machine Learning**, mais duas outras extensões são instaladas automaticamente (se você tiver acesso à Internet). Elas são a extensão [Conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) e a extensão [Microsoft Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)

Para trabalhar com o Azure Machine Learning, precisamos transformar o VS Code em um IDE Python. Trabalhar com [Python no Visual Studio Code](https://code.visualstudio.com/docs/languages/python) requer a extensão Microsoft Python, que é instalada automaticamente com a extensão do Azure Machine Learning. A extensão transforma o VS Code em um excelente IDE e funciona em qualquer sistema operacional, com uma variedade de interpretadores do Python. Ela aproveita toda a capacidade do VS Code para fornecer preenchimento automático e IntelliSense, linting, depuração e testes de unidade, além da capacidade de alternar facilmente entre ambientes Python, incluindo ambientes virtuais e conda. Confira este passo a passo de edição, execução e depuração de código Python. Confira o [Tutorial Olá Mundo do Python](https://code.visualstudio.com/docs/python/python-tutorial)

**Para instalar a extensão do Azure Machine Learning:**

1. Inicie o VS Code.

1. Em um navegador, acesse: Extensão [Azure Machine Learning para Visual Studio Code (Versão Prévia)](https://aka.ms/vscodetoolsforai)

1. Na página da Web, clique em **Instalar**. 

1. Na guia de extensão, clique em **Instalar**.

1. Uma guia de boas-vinda é aberta no VS Code para a extensão, e o símbolo do Azure é adicionado à barra de atividade.

   ![Ícone do Azure na barra de atividade do Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. Na caixa de diálogo, clique em **Entrar** e siga o prompt na tela para se autenticar no Azure. 
   
   A extensão da Conta do Azure, que foi instalada junto com o Azure Machine Learning para a extensão do VS Code, ajuda você a se autenticar com sua conta do Azure. Veja a lista de comandos na página [Extensão Conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

> [!Tip] 
> Confira a [Extensão do IntelliCode para VS Code (versão prévia)](https://go.microsoft.com/fwlink/?linkid=2006060). O IntelliCode fornece um conjunto de recursos assistidos por IA para o IntelliSense no Python, como inferir os preenchimentos automáticos mais relevantes com base no contexto de código atual.

## <a name="install-the-sdk"></a>Instalar o SDK

1. Verifique se o Python 3.5 ou posterior está instalado e é reconhecido pelo VS Code. Se você instalá-lo agora, reinicie o VS Code e selecione um interpretador do Python seguindo as instruções em https://code.visualstudio.com/docs/python/python-tutorial.

1. No VS Code, abra a Paleta de Comandos **Ctrl+Shift+P**.

1. Digite 'Instalar SDK do Azure ML' para localizar o comando pip install para o SDK. Um ambiente do Python local privado é criado com os pré-requisitos do Visual Studio Code para funcionar com o Azure Machine Learning.

   ![instalar o SDK do Azure Machine Learning para Python](./media/vscode-tools-for-ai/install-sdk.png)

1. Na janela de terminal integrada, especifique o interpretador do Python a ser usado ou pressione **Enter** para usar seu interpretador do Python padrão.

   ![Escolha o interpretador](./media/vscode-tools-for-ai/python.png)

## <a name="get-started-with-azure-machine-learning"></a>Introdução ao Azure Machine Learning

Antes de iniciar o treinamento e implantar modelos de Machine Learning usando o VS Code, você precisará criar um [workspace de serviço do Azure Machine Learning](concept-azure-machine-learning-architecture.md#workspace) na nuvem para conter os modelos e recursos. Saiba como criá-lo e criar seu primeiro experimento no espaço de trabalho.

1. Clique no ícone do Azure na barra de atividades do Visual Studio Code. A barra lateral Azure Machine Learning é exibida.

   [![instalar](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. Clique com o botão direito do mouse em sua assinatura do Azure e selecione **Criar Espaço de Trabalho**. É exibida uma lista. Na imagem animada, o nome da assinatura é 'Avaliação gratuita' e o workspace é 'TeamWorkspace'. 

1. Selecione um grupo de recursos existente na lista ou crie um novo usando o assistente na Paleta de Comandos.

1. No campo, digite um nome exclusivo e claro para o novo espaço de trabalho. Nas capturas de tela, o workspace é denominado 'TeamWorkspace'.

1. Pressione enter, e o novo espaço de trabalho será criado. Ele aparece na árvore abaixo do nome da assinatura.

1. Clique com o botão direito do mouse no nó Experimento e escolha **Criar Experimento** no menu de contexto.  Os experimentos acompanham suas execuções usando o Azure Machine Learning.

1. No campo, insira um nome para o experimento. Nas capturas de tela, o experimento é denominado 'MNIST'.
 
1. Pressione enter, e o novo experimento será criado. Ele aparece na árvore abaixo do nome do espaço de trabalho.

1. Você pode clicar com o botão direito em um Experimento em um Workspace e selecionar “Definir como Experimento Ativo”. O experimento “**Ativo**” é o que você está usando no momento e sua pasta aberta no VS Code será vinculada a esse experimento na nuvem. Essa pasta deve conter seus scripts do Python locais.

   Agora, cada um de seus experimentos é executado com o experimento, assim, todas as métricas essenciais são armazenadas no histórico de experimentos e os modelos que você treinar são automaticamente carregados no Azure Machine Learning e armazenados com seus logs e métricas de experimentos.

   [![Anexar uma pasta ao VS Code](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Usar atalhos do teclado

Como a maior parte do VS Code, os recursos do Azure Machine Learning no VS Code são acessíveis por meio do teclado. A combinação de teclas mais importante a ser aprendida é Ctrl+Shift+P, que abrirá a Paleta de Comandos. Da paleta, você tem acesso a toda a funcionalidade do VS Code, incluindo atalhos de teclado para as operações mais comuns.

[![Atalhos de teclado para o Azure Machine Learning para VS Code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Próximas etapas

Agora você pode usar o Visual Studio Code para trabalhar com o Azure Machine Learning.

Saiba como [criar destinos de computação, treinar e implantar modelos no Visual Studio Code](how-to-vscode-train-deploy.md).
