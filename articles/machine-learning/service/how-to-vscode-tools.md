---
title: Usar a extensão Visual Studio Code Tools for AI com o Azure Machine Learning
description: Aprenda sobre a Visual Studio Code Tools for AI e como iniciar treinamento e implantar os modelos de aprendizado de máquina e aprendizagem profunda com o serviço do Azure Machine Learning no VS Code.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.reviewer: jmartens
ms.date: 10/1/2018
ms.openlocfilehash: 57fe511e5de0d73f2a372da0ecab3e9a3039b194
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2018
ms.locfileid: "51854196"
---
# <a name="vs-code-tools-for-ai-get-started-with-azure-machine-learning-from-visual-studio-code"></a>VS Code Tools para IA: Introdução ao Azure Machine Learning do Visual Studio Code

Neste artigo, você aprenderá sobre a extensão VS Code (Visual Studio Code), **Tools for AI**, e como iniciar treinamento e implantar os modelos de aprendizado de máquina e aprendizagem profunda com o serviço do Azure Machine Learning no VS Code.

Use a extensão Tools for AI no Visual Studio Code a fim de usar o serviço Azure Machine Learning para preparação de dados, treinamento e teste de modelos de ML no local e em destinos de computação remotos, implantar esses modelos e acompanhar experimentos e métricas personalizadas.

## <a name="prerequisite"></a>Pré-requisito

+ O Visual Studio Code deve ser instalado. O VS Code é um editor de código-fonte leve, mas poderoso que é executado na área de trabalho. Ele vem com suporte interno para o Python e muito mais.  [Saiba como instalar o VS Code](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Instale o Python 3.5 ou superior](https://www.anaconda.com/download/).

+ Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://aka.ms/AMLfree) antes de começar.

## <a name="install-vs-code-tools-for-ai-extension"></a>Instalar a extensão VS Code Tools for AI

Quando você instala a extensão **Tools for AI**, duas outras extensões são instaladas automaticamente (se você tem acesso à Internet). Elas são a extensão [Conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) e a extensão [Microsoft Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)

Para trabalhar com o Azure Machine Learning, precisamos transformar o VS Code em um IDE Python. O trabalho com o [Python no Visual Studio Code](https://code.visualstudio.com/docs/languages/python) requer a extensão Microsoft Python, que é instalada com Tools for AI automaticamente. A extensão transforma o VS Code em um excelente IDE e funciona em qualquer sistema operacional, com uma variedade de interpretadores do Python. Ela aproveita toda a capacidade do VS Code para fornecer preenchimento automático e IntelliSense, linting, depuração e testes de unidade, além da capacidade de alternar facilmente entre ambientes Python, incluindo ambientes virtuais e conda. Confira este passo a passo de edição, execução e depuração de código Python. Confira o [Tutorial Olá Mundo do Python](https://code.visualstudio.com/docs/python/python-tutorial)

**Para instalar a extensão Tools for AI:**

1. Inicie o VS Code.

1. Em um navegador, acesse: https://aka.ms/vscodetoolsforai. 

1. Na página da Web, clique em **Instalar**. 

1. Na guia de extensão, clique em **Instalar**.

1. Uma guia de boas-vinda é aberta no VS Code para a extensão, e o símbolo do Azure é adicionado à barra de atividade.

   ![Ícone do Azure na barra de atividade do Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. Na caixa de diálogo, clique em **Entrar** e siga o prompt na tela para se autenticar no Azure. 
   
   A extensão Conta do Azure, que foi instalada junto com o VS Code Tools for AI, ajuda você a se autenticar com sua conta do Azure. Veja a lista de comandos na página [Extensão Conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

> [!Tip] 
> Confira a [Extensão do IntelliCode para VS Code (versão prévia)](https://go.microsoft.com/fwlink/?linkid=2006060). O IntelliCode fornece um conjunto de recursos assistidos por IA para o IntelliSense no Python, como inferir os preenchimentos automáticos mais relevantes com base no contexto de código atual.

## <a name="install-the-sdk"></a>Instalar o SDK

1. Verifique se o Python 3.5 ou posterior está instalado e é reconhecido pelo VS Code. Se você instalá-lo agora, reinicie o VS Code e selecione um interpretador do Python seguindo as instruções em https://code.visualstudio.com/docs/python/python-tutorial.

1. No VS Code, abra a paleta de comandos **Ctrl+Shift+P**.

1. Digite 'Instalar SDK do Azure ML' para localizar o comando pip install para o SDK. Um ambiente do Python local privado é criado com os pré-requisitos do Visual Studio Code para funcionar com o Azure Machine Learning.
   ![install](./media/vscode-tools-for-ai/install-sdk.png)

1. Na janela de terminal integrada, especifique o interpretador do Python a ser usado ou pressione **Enter** para usar seu interpretador do Python padrão.

   ![instalar](./media/vscode-tools-for-ai/python.png)

## <a name="get-started-with-azure-machine-learning"></a>Introdução ao Azure Machine Learning

Antes de iniciar o treinamento e implantar modelos de ML usando o VS Code, você precisará criar um [espaço de trabalho de serviço do Azure Machine Learning](concept-azure-machine-learning-architecture.md#workspace) na nuvem para conter os modelos e recursos. Saiba como criá-lo e criar seu primeiro experimento no espaço de trabalho.

1. Clique no ícone do Azure na barra de atividades do Visual Studio Code. A barra lateral Azure: Machine Learning é exibida.

   ![instalar](./media/vscode-tools-for-ai/createworkspace.gif)

1. Clique com o botão direito do mouse em sua assinatura do Azure e selecione **Criar Espaço de Trabalho**. É exibida uma lista. Na imagem animada, o nome da assinatura é “OpenMind Studio”, e o espaço de trabalho é “MyWorkspace”. 

1. Selecione um grupo de recursos existente na lista ou crie um novo usando o assistente na paleta de comandos.

1. No campo, digite um nome exclusivo e claro para o novo espaço de trabalho. Nas capturas de tela, o espaço de trabalho é denominado 'MyWorkspace'.

1. Pressione enter, e o novo espaço de trabalho será criado. Ele aparece na árvore abaixo do nome da assinatura.

1. Clique com o botão direito do mouse no nome do espaço de trabalho e escolha **Criar Experimento** no menu de contexto.  Os experimentos acompanham suas execuções usando o Azure Machine Learning.

1. No campo, insira um nome para o experimento. Nas capturas de tela, o experimento é denominado 'MNIST'.
 
1. Pressione enter, e o novo experimento será criado. Ele aparece na árvore abaixo do nome do espaço de trabalho.

1. Clique com o botão direito do mouse no nome do experimento e escolha **Anexar uma pasta local**. Essa pasta deve conter seus scripts do Python locais. Em seguida, a pasta é vinculada ao experimento na nuvem. 

   Agora, cada um de seus experimentos é executado com o experimento. Todas as métricas-chave serão armazenadas no histórico de experimentos. Os modelos que você treinar serão automaticamente carregados no Azure Machine Learning e armazenados com seus logs e métricas de experimentos.

   [![Anexar uma pasta ao VS Code](./media/vscode-tools-for-ai/attachfolder.gif)](./media/vscode-tools-for-ai/attachfolder.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Usar atalhos do teclado

Como a maior parte do VS Code, os recursos do Azure Machine Learning no VS Code são acessíveis por meio do teclado. A combinação de teclas mais importante a ser aprendida é Ctrl+Shift+P, que abrirá a Paleta de Comandos. Da paleta, você tem acesso a toda a funcionalidade do VS Code, incluindo atalhos de teclado para as operações mais comuns.

[![Atalhos de teclado do VS Code Tools for AI](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Próximas etapas

Agora você pode usar o Visual Studio Code para trabalhar com o Azure Machine Learning.

Saiba como [criar destinos de computação, treinar e implantar modelos no Visual Studio Code](how-to-vscode-train-deploy.md).