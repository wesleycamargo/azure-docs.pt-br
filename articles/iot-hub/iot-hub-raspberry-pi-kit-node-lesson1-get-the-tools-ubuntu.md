---
title: "Conecte o Raspberry Pi (Nó) ao IoT do Azure - Lição 1: obter ferramentas (Ubuntu) | Microsoft Docs"
description: "Baixe e instale as ferramentas e software necessários para o primeiro aplicativo de exemplo para o Pi no Ubuntu."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "desenvolvimento de iot, software de iot, software de internet das coisas, instalar o git no ubuntu, execução de gulp, instalar node js no ubuntu"
ms.assetid: 4d5e45c0-1db9-4662-a039-99ba26333085
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: a43690c4fd35a701472b9a1c747e969d1d3a2ea4


---
# <a name="get-the-tools-ubuntu-1604"></a>Obter as ferramentas (Ubuntu 16.04)

> [!div class="op_single_selector"]
> * [Windows 7 ou superior](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-mac.md)


## <a name="what-you-will-do"></a>O que você fará
Baixe as ferramentas de desenvolvimento e o software para o primeiro aplicativo de exemplo para o Raspberry Pi 3. Se você tiver problemas, procure as soluções na [página de solução de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="what-you-will-learn"></a>O que você aprenderá
Neste artigo, você aprenderá:

* Como instalar o Git e o Node.js.
  * [Git](https://git-scm.com) é um software livre de sistema de controle de versão distribuído. O aplicativo de exemplo para este artigo está armazenado em Git.
  * O [Node.js](https://nodejs.org/en/) é um tempo de execução de JavaScript com um avançado ecossistema de pacote.
* Como usar o NPM (gerenciador de pacotes Node.js) para instalar ferramentas de desenvolvimento adicionais do Node.js.
  * A versão mínima necessária do Node.js é a 4.5 LTS.
  * O [NPM](https://www.npmjs.com) é um dos gerenciadores de pacote para o Node.js.

## <a name="what-do-you-need"></a>Do que você precisa
Para concluir esta operação, você precisará de:

* Uma conexão com a Internet para baixar as ferramentas de desenvolvimento e o software.
* Um computador que esteja executando o Ubuntu 16.04 ou posterior.

## <a name="install-git-nodejs-and-npm"></a>Instale o Git, Node.js e o NPM
Use o atalho de teclado `Ctrl + Alt + T` para abrir um terminal e execute os seguintes comandos:

```bash
sudo apt-get update
curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
sudo apt-get install -y nodejs
sudo apt-get install git
```

## <a name="install-additional-nodejs-development-tools"></a>Instalar ferramentas adicionais de desenvolvimento do Node.js
Você usa o [gulp.js](http://gulpjs.com) para automatizar a implantação do aplicativo de exemplo para o Pi. E também usa o [device-discovery-cli](https://github.com/Azure/device-discovery-cli) para recuperar informações de rede sobre os dispositivos IoT.

Instale `gulp` e `device-discovery-cli`, executando o seguinte comando no terminal:

```bash
sudo npm install -g device-discovery-cli gulp
```

Se você tiver problemas ao instalar o Node.js e essas ferramentas adicionais de desenvolvimento no Ubuntu, consulte o [guia de solução de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md) para soluções de problemas comuns.

## <a name="install-visual-studio-code"></a>Instalar o Visual Studio Code
[Baixe](https://code.visualstudio.com/docs/setup/linux) e instale o Visual Studio Code. O Visual Studio Code é um editor de código-fonte leve mas poderoso para Windows, Linux e macOS. Use este editor posteriormente no tutorial para editar o código de exemplo.

## <a name="summary"></a>Resumo
Você instalou as ferramentas de desenvolvimento e software necessários para o primeiro aplicativo de exemplo. A próxima tarefa é criar, implantar e executar o aplicativo de exemplo no Pi.

## <a name="next-steps"></a>Próximas etapas
[Criar e implantar o aplicativo de exemplo intermitente](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md)




<!--HONumber=Jan17_HO4-->


