---
title: "Conectar o Raspberry Pi (C) ao IoT do Azure - Lição 1: obter ferramentas (macOS) | Microsoft Docs"
description: "Baixe e instale as ferramentas e software necessários para o primeiro aplicativo de exemplo para o Pi no macOS."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "desenvolvimento de iot, software de iot, software de Internet das coisas, instalar o git no mac, execução de gulp, instalar node js no mac"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-raspberry-pi-kit-c-get-started
ms.assetid: fc6bd2c8-a847-4bf5-818f-6f7f9a6835ee
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 20cf1fd57f37c84342ba08954a913c0eceba415f
ms.contentlocale: pt-br
ms.lasthandoff: 01/24/2017

---
<a id="get-the-tools-macos-1010" class="xliff"></a>

# Obter as ferramentas (macOS 10.10)
> [!div class="op_single_selector"]
> * [Windows 7 ou superior](iot-hub-raspberry-pi-kit-c-lesson1-get-the-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-raspberry-pi-kit-c-lesson1-get-the-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-raspberry-pi-kit-c-lesson1-get-the-tools-mac.md)

<a id="what-you-will-do" class="xliff"></a>

## O que você fará
Baixe as ferramentas de desenvolvimento e o software para o primeiro aplicativo de exemplo para o Raspberry Pi 3. Se você tiver problemas, procure as soluções na [página de solução de problemas](iot-hub-raspberry-pi-kit-c-troubleshooting.md).

> [!NOTE]
> Embora a linguagem de programação da lógica principal seja C, as ferramentas Node.js são usadas nas lições para descobrir dispositivos e criar e implantar aplicativos de exemplo.

<a id="what-you-will-learn" class="xliff"></a>

## O que você aprenderá
Neste artigo, você aprenderá:

* Como instalar o Git e o Node.js.
  * [Git](https://git-scm.com) é um software livre de sistema de controle de versão distribuído. O aplicativo de exemplo para este artigo está armazenado em Git.
  * O [Node.js](https://nodejs.org/en/) é um tempo de execução de JavaScript com um avançado ecossistema de pacote.
* Como usar o NPM (gerenciador de pacotes Node.js) para instalar ferramentas de desenvolvimento adicionais do Node.js.
  * A versão mínima necessária do Node.js é a 4.5 LTS.
  * O [NPM](https://www.npmjs.com) é um dos gerenciadores de pacote para o Node.js.

<a id="what-you-need" class="xliff"></a>

## O que você precisa
Para concluir esta operação, você precisará de:

* Uma conexão com a Internet para baixar as ferramentas de desenvolvimento e o software.
* Um Mac que esteja executando o macOS Yosemite (10.10) ou posterior.

<a id="install-git-and-nodejs" class="xliff"></a>

## Instalar o Git e o Node.js
Para instalar o Git e o Node.js, use o utilitário de gerenciamento de pacote [Homebrew](http://brew.sh) seguindo estas etapas:

1. Instale o Homebrew. Se você já tiver instalado o Homebrew, vá para a etapa 2.
   
   1. Pressione `Cmd + Space` e digite `Terminal` para abrir um terminal.
   2. Execute o comando a seguir:
      
      ```bash
      /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
      ```
2. Instale o Git e o Node.js executando o seguinte comando:
   
   ```bash
   brew install node git
   ```

<a id="install-additional-nodejs-development-tools" class="xliff"></a>

## Instalar ferramentas adicionais de desenvolvimento do Node.js
Use [gulp.js](http://gulpjs.com) para automatizar a implantação do aplicativo de exemplo para o Pi. Use o [device-discovery-cli](https://github.com/Azure/device-discovery-cli) para recuperar informações de rede sobre os dispositivos IoT.

Instale `gulp` e `device-discovery-cli`, executando o seguinte comando no terminal:

```bash
sudo npm install -g device-discovery-cli gulp
```

Se você tiver problemas ao instalar o Node.js e essas ferramentas adicionais de desenvolvimento no macOS, consulte o [guia de solução de problemas](iot-hub-raspberry-pi-kit-c-troubleshooting.md) para soluções de problemas comuns.

<a id="install-visual-studio-code" class="xliff"></a>

## Instalar o Visual Studio Code
[Baixe](https://code.visualstudio.com/docs/setup/osx) e instale o Visual Studio Code. O Visual Studio Code é um editor de código-fonte leve mas poderoso para Windows, Linux e macOS. Use este editor posteriormente no tutorial para editar o código de exemplo.

<a id="summary" class="xliff"></a>

## Resumo
Você instalou as ferramentas de desenvolvimento e software necessários para o primeiro aplicativo de exemplo. A próxima tarefa é criar, implantar e executar o aplicativo de exemplo no Pi.

<a id="next-steps" class="xliff"></a>

## Próximas etapas
[Criar e implantar o aplicativo de intermitência](iot-hub-raspberry-pi-kit-c-lesson1-deploy-blink-app.md)


