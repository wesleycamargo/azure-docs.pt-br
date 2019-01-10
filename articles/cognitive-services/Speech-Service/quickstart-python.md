---
title: 'Início Rápido: Reconhecer fala no Python usando o SDK do Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Aprenda a reconhecer fala no Python usando o SDK do Serviço de Fala
services: cognitive-services
author: chlandsi
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 12/18/2018
ms.author: chlandsi
ms.openlocfilehash: 7610b12b351b2652df7ade603711d4d92e587292
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53723902"
---
# <a name="quickstart-using-the-speech-service-from-python"></a>Início Rápido: Usando o serviço de fala do Python

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Este artigo mostra como usar o Serviço de Fala por meio do Speech SDK para Python. Ilustra como reconhecer fala da entrada do microfone.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, aqui está uma lista de pré-requisitos:

* Uma chave de assinatura do Azure para o Serviço de Fala. [Obtenha uma gratuitamente](get-started.md).
* [Python 3.5 (64 bits)](https://www.python.org/downloads/) ou posterior.
* O pacote Speech SDK para Python está disponível para Windows (x64), Mac (versão do macOS X 10.12 ou posterior) e Linux (Ubuntu 16.04 ou 18.04 em x64).
* No Ubuntu, execute os comandos a seguir para a instalação dos pacotes requeridos:

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

* No Windows, você também precisa de [Pacotes Redistribuíveis do Microsoft Visual C++ para Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) para sua plataforma.

## <a name="get-the-speech-sdk-python-package"></a>Obter o pacote do Python do Speech SDK

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

O pacote do Python de Speech SDK dos Serviços Cognitivos pode ser instalado de [PyPI](https://pypi.org/) usando este comando na linha de comando:

```sh
pip install azure-cognitiveservices-speech
```

A versão atual do SDK de Fala dos Serviços Cognitivos é `1.2.0`.

## <a name="support-and-updates"></a>Suporte e atualizações

Atualizações ao pacote do Python do Speech SDK serão distribuídas por meio de PyPI e anunciadas na página [Notas sobre a versão](./releasenotes.md).
Se uma nova versão estiver disponível, você poderá atualizar para ela com o comando `pip install --upgrade azure-cognitiveservices-speech`.
Você pode verificar qual versão está instalada no momento inspecionando a variável `azure.cognitiveservices.speech.__version__`.

Se você tiver um problema ou não tiver um recurso, dê uma olhada em nossa [página de suporte](./support.md).

## <a name="create-a-python-application-using-the-speech-sdk"></a>Criar um aplicativo Python usando o Speech SDK

### <a name="running-the-sample-in-a-terminal"></a>Executando o exemplo em um terminal

Você pode copiar o [código](#quickstart-code) neste início rápido para um arquivo de origem `quickstart.py` e executá-lo no seu IDE ou no console

```sh
python quickstart.py
```

ou você pode baixar este tutorial de início rápido como um [Jupyter](https://jupyter.org) Notebook do [repositório de exemplos de Fala de Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) e executá-lo como um notebook.

### <a name="quickstart-code"></a>Código de início rápido

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/quickstart.py#code)]

### <a name="installing-the-speech-sdk-python-package-and-running-the-sample-in-visual-studio-code"></a>Instalando o pacote do Python do Speech SDK e executando o exemplo no Visual Studio Code

1. [Baixe](https://www.python.org/downloads/) e instale uma versão de 64 bits (3.5 ou posterior) do Python em seu computador.
1. [Baixe](https://code.visualstudio.com/Download) e instale o Visual Studio Code.
1. Abra o Visual Studio Code e instale a extensão do Python selecionando **Arquivo** > **Preferências** > **Extensões** no menu e pesquisando "Python".
   ![Instalar a extensão do Python](media/sdk/qs-python-vscode-python-extension.png)
1. Crie uma pasta na qual armazenar o projeto, por exemplo, usando o Windows Explorer.
1. No Visual Studio Code, clique no ícone **Arquivo** e, em seguida, abra a pasta que você criou.
   ![Abrir Pasta](media/sdk/qs-python-vscode-python-open-folder.png)
1. Crie um novo arquivo de código-fonte Python `speechsdk.py` clicando no ícone de novo arquivo.
   ![Criar Arquivo](media/sdk/qs-python-vscode-python-newfile.png)
1. Copie, cole e salve o [código do Python](#quickstart-code) para o arquivo recém-criado.
1. Insira suas informações de assinatura do Serviço de Fala.
1. Se já tiver sido selecionado um interpretador do Python, ele será exibido no lado esquerdo da barra de status na parte inferior da janela.
   Caso contrário, você poderá exibir uma lista de interpretadores do Python disponíveis abrindo a **Paleta de Comandos** (`Ctrl+Shift+P`) e digitando **Python: Selecione Interpretador** e escolha uma configuração apropriada.
1. Se o pacote do Python do Speech SDK ainda não estiver instalado para o interpretador do Python selecionado, isso poderá ser feito facilmente de dentro do Visual Studio Code.
   Para instalar o pacote Speech SDK, abra um terminal ativando a Paleta de Comandos novamente (`Ctrl+Shift+P`) e digitando **Terminal: Criar um Novo Terminal Integrado**.
   No terminal que é aberto, digite o comando `python -m pip install azure-cognitiveservices-speech` ou o comando apropriado para seu sistema.
1. Para executar o código de exemplo, clique com o botão direito do mouse em algum lugar dentro do editor e selecione **Executar o Arquivo do Python no Terminal**.
   Diga algumas palavras quando solicitado e o texto transcrito deverá ser exibido em breve posteriormente.
   ![Executar o Exemplo](media/sdk/qs-python-vscode-python-run.png)

Se houver problemas em seguir estas instruções, veja o [tutorial do Python do Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial) mais amplo.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar exemplos do Python no GitHub](https://aka.ms/csspeech/samples)
