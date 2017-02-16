---
title: Prepare o computador Mac e o Hub IoT do Azure | Microsoft Docs
description: Instalar as ferramentas no computador Mac, criar um Hub IoT e registrar seu dispositivo no Hub IoT.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "desenvolvimento de IoT, software de IoT, serviço de nuvem de IoT, software de Internet das Coisas, CLI do Azure, instalar o Python no Mac, instalar o git no Ubuntu, execução de Gulp, instalar Node.js em Mac"
ms.assetid: 42f9d186-e20c-4ef9-98cc-71d39e058b06
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 65a25dd7a2f6a8d518217512f9e10fc9008ee728
ms.openlocfilehash: f3bed8620c13372c3af50657bb54aa8dce115529


---
# <a name="get-the-tools-macos"></a>Obter as ferramentas (macOS)
> [!div class="op_single_selector"]
> * [Windows 7 ou superior](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-mac.md)

## <a name="what-you-will-do"></a>O que você fará

- Instalar o Git, Node.js, o Gulp e o Python.
- Instalar a interface de linha de comando do Azure (CLI do Azure). 

Se você tiver problemas, procure as soluções na [página de solução de problemas](iot-hub-gateway-kit-c-sim-troubleshooting.md).

## <a name="what-you-will-learn"></a>O que você aprenderá

Nesta lição, você aprenderá a:

- Como instalar o [Git](https://git-scm.com/) e o [Node.js](https://nodejs.org/en/).
  - O Git é um sistema de controle de versão distribuída de software livre. O aplicativo de exemplo para esta lição está armazenado em Git.
  - O Node.js é um tempo de execução de JavaScript com um avançado ecossistema de pacote.
- Como usar o [NPM](https://www.npmjs.com/) para instalar ferramentas de desenvolvimento do Node.js.
  - A versão mínima necessária do Node.js é a 4.5 LTS.
  - O NPM é um dos gerenciadores de pacote para o Node.js.
- Como instalar o Visual Studio Code.
  - O Visual Studio Code é um editor de código-fonte de plataforma cruzada leve mas poderoso para Windows, Linux e macOS. Ele tem excelente suporte para depuração, controle Git incorporado, realce de sintaxe, preenchimento de código inteligente, trechos de código e também refatoração de código.
- Como instalar o Python.
  - Python é uma linguagem de programação para fins gerais amplamente utilizada, de alto nível, interpretada e dinâmica.
- Como instalar a CLI do Azure.
  - A CLI do Azure fornece uma experiência de linha de comando multiplataforma do Azure. Você trabalha diretamente de uma linha de comando para provisionar e gerenciar recursos.
- Como usar a CLI do Azure para criar um Hub IoT.

## <a name="what-you-need"></a>O que você precisa

- Uma conexão com a Internet para baixar as ferramentas e o software.
- Um computador Mac que esteja executando o OS X Yosemite (10.10) ou posterior.

## <a name="install-git-and-nodejs"></a>Instalar o Git e o Node.js

Para instalar o Git e o Node.js, use o utilitário de gerenciamento de pacote Homebrew seguindo estas etapas:

1. [Baixar](http://brew.sh/) e instalar o Homebrew. Se você já tiver instalado o Homebrew, vá para a etapa 2.
   1. Pressione `Cmd + Space` e digite `Terminal` para abrir um terminal.
   2. Execute o comando a seguir:

      ```bash
      /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
      ```

2. Instale o Git e o Node.js executando o seguinte comando:

    ```bash
    brew install node git
    ```

## <a name="install-nodejs-development-tools"></a>Instalar ferramentas de desenvolvimento do Node.js

Você usa o [gulp.js](http://gulpjs.com/) para automatizar a implantação e execução de scripts.

Para instalar o gulp, execute o seguinte comando no terminal:

```bash
npm install -g gulp
```

Se você tiver problemas com a instalação, consulte o [guia de solução de problemas](iot-hub-gateway-kit-c-sim-troubleshooting.md) para obter soluções para problemas comuns.

> [!Note]
> Nó, NPM e Gulp são necessários para executar os scripts de automação desenvolvidos no Node.js.

## <a name="install-python"></a>Instalar o Python

Embora o Mac OS X venha com o Python 2.7, recomendamos que você instale o Python por meio do Homebrew. Veja [Instalando Python no Mac OS X](http://docs.python-guide.org/en/latest/starting/install/osx/).

Instale o Python e o pip executando o seguinte comando:

```bash
brew install python
```

## <a name="install-the-azure-cli"></a>Instalar a CLI do Azure

Para instalar a CLI do Azure, siga estas etapas:

1. Execute os seguintes comandos no terminal:
   ```bash
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```
   A instalação pode levar 5 minutos.

2. Verifique a instalação executando o comando a seguir:
   ```bash
   az iot -h
   ```
   Se a instalação for bem-sucedida, você verá a seguinte saída.

   ![Verificar a instalação da CLI do Azure](media/iot-hub-gateway-kit-lessons/lesson2/az_iot_help_osx.png)

## <a name="install-visual-studio-code"></a>Instalar o Visual Studio Code

Use o código do Visual Studio posteriormente no tutorial para editar arquivos de configuração.

[Baixe](https://code.visualstudio.com/docs/setup/osx) e instale o Visual Studio Code.

## <a name="summary"></a>Resumo

Você instalou todos os softwares e as ferramentas necessárias no computador Mac. A próxima tarefa é usar a CLI do Azure para criar um hub IoT e registrar seu dispositivo no seu hub IoT.

## <a name="next-steps"></a>Próximas etapas
[Criar um Hub IoT e registrar o dispositivo](iot-hub-gateway-kit-c-sim-lesson2-register-device.md)



<!--HONumber=Dec16_HO3-->


