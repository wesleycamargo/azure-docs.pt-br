---
title: "Conecte o Arduino ao IoT do Azure - Lição 1: obter ferramentas (Ubuntu) | Microsoft Docs"
description: "Baixe e instale as ferramentas e software necessários para o primeiro aplicativo de exemplo para o Adafruit Feather M0 WiFi no Ubuntu."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: ferramentas de desenvolvimento arduino, desenvolvimento de iot, software de iot, software de Internet das coisas, instalar o git no ubuntu, instalar node js no ubuntu
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started
ms.assetid: 7572f191-420d-41f0-923b-7ea86c0bfa73
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: d33fc9b04f69b161e92a266cd3604488e35e74af
ms.contentlocale: pt-br
ms.lasthandoff: 01/24/2017

---
<a id="get-the-tools-ubuntu-1604" class="xliff"></a>

# Obter as ferramentas (Ubuntu 16.04)

> [!div class="op_single_selector"]
> * [Windows 7 ou posterior][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

<a id="what-you-will-do" class="xliff"></a>

## O que você fará

Baixe as ferramentas de desenvolvimento e o software para o primeiro aplicativo de exemplo para a placa do Adafruit Feather M0 WiFi Arduino. 

Se você tiver problemas, procure por soluções na [página de solução de problemas][troubleshooting].

> [!NOTE]
> Embora a linguagem de programação da lógica principal seja o Arduino, ferramentas Node.js são usadas nas lições para compilar e implantar aplicativos de exemplo.

<a id="what-you-will-learn" class="xliff"></a>

## O que você aprenderá
Neste artigo, você aprenderá:

* Como instalar o Git e o Node.js
  * [Git](https://git-scm.com) é um software livre de sistema de controle de versão distribuído. O aplicativo de exemplo para este artigo está armazenado em Git.
  * O [Node.js](https://nodejs.org/en/) é um tempo de execução de JavaScript com um avançado ecossistema de pacote.
* Como usar o NPM (gerenciador de pacotes Node.js) para instalar ferramentas de desenvolvimento adicionais do Node.js.
  * A versão mínima necessária do Node.js é a 4.5 LTS.
  * O [NPM](https://www.npmjs.com) é um dos gerenciadores de pacote para o Node.js.

<a id="what-you-need" class="xliff"></a>

## O que você precisa
Para concluir esta operação, você precisará de:
* Uma conexão com a Internet para baixar as ferramentas de desenvolvimento e o software.
* Um computador que esteja executando o Ubuntu 16.04 ou posterior.

<a id="install-git-nodejs-and-npm" class="xliff"></a>

## Instale o Git, Node.js e o NPM
Use o atalho de teclado `Ctrl + Alt + T` para abrir um terminal e execute os seguintes comandos:

```bash
sudo apt-get update
curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
sudo apt-get install -y nodejs
sudo apt-get install git
```

<a id="install-additional-nodejs-development-tools" class="xliff"></a>

## Instalar ferramentas adicionais de desenvolvimento do Node.js
Use [gulp.js](http://gulpjs.com) para automatizar a implantação do aplicativo de exemplo para sua placa Arduino.

Instale `gulp`, `device-discovery-cli` executando o seguinte comando no terminal:

```bash
sudo npm install -g gulp device-discovery-cli
```

Se você tiver problemas ao instalar o Node.js e essas ferramentas adicionais de desenvolvimento no Ubuntu, consulte o [guia de solução de problemas][troubleshooting] para soluções de problemas comuns.

<a id="install-visual-studio-code" class="xliff"></a>

## Instalar o Visual Studio Code
[Baixe](https://code.visualstudio.com/docs/setup/linux) e instale o Visual Studio Code. O Visual Studio Code é um editor de código-fonte leve mas poderoso para Windows, Linux e macOS. Use este editor posteriormente no tutorial para editar o código de exemplo.

<a id="summary" class="xliff"></a>

## Resumo
Você instalou as ferramentas de desenvolvimento e software necessários para o primeiro aplicativo de exemplo. A tarefa seguinte é criar, implantar e executar o aplicativo de exemplo na placa Arduino.

<a id="next-steps" class="xliff"></a>

## Próximas etapas
[Criar e implantar o aplicativo de exemplo de piscar][create-and-deploy-the-blink-sample-application]

<!-- Images and links -->

[windows]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md
[ubuntu]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-ubuntu.md
[macos]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-mac.md
[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[create-and-deploy-the-blink-sample-application]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-deploy-blink-app.md
