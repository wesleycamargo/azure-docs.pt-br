---
title: "Dispositivo SensorTag e Gateway do IoT do Azure - Lição 2: obter ferramentas (Ubuntu) | Microsoft Docs"
description: Instale as ferramentas e o software no computador host executando o Ubuntu, crie um Hub IoT e registrar seu dispositivo no Hub IoT.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "desenvolvimento de iot, software de iot, serviço de nuvem de IoT, software de Internet das coisas, CLI do Azure, instalar o git no ubuntu, execução de gulp, instalar node js no ubuntu"
ms.assetid: 0bac1412-385b-4255-a33f-9d44c35feb3e
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 6414a4f37367d8acc0bab9356b8a09aadf13bfc9
ms.lasthandoff: 01/25/2017


---
# <a name="get-the-tools-ubuntu-1604"></a>Obter as ferramentas (Ubuntu 16.04)
> [!div class="op_single_selector"]
> * [Windows 7 ou superior](iot-hub-gateway-kit-c-lesson2-get-the-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-gateway-kit-c-lesson2-get-the-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-gateway-kit-c-lesson2-get-the-tools-mac.md)

## <a name="what-you-will-do"></a>O que você fará

- Instalar o Git, Node.js, o Gulp e o Python.
- Instalar a interface de linha de comando do Azure (CLI do Azure). 

Se você tiver problemas, procure as soluções na [página de solução de problemas](iot-hub-gateway-kit-c-troubleshooting.md).
## <a name="what-you-will-learn"></a>O que você aprenderá

Nesta lição, você aprenderá a:

- Como instalar o Git e o Node.js.
  - O Git é um sistema de controle de versão distribuída de software livre. O aplicativo de exemplo para esta lição está armazenado em Git.
  - O Node.js é um tempo de execução de JavaScript com um avançado ecossistema de pacote.
- Como usar o NPM para instalar ferramentas de desenvolvimento do Node.js.
  - A versão mínima necessária do Node.js é a 4.5 LTS.
  - O NPM é um dos gerenciadores de pacote para o Node.js.
- Como instalar o Visual Studio Code.
  - O Visual Studio Code é um editor de código-fonte de plataforma cruzada leve mas poderoso para Windows, Linux e macOS. Ele tem excelente suporte para depuração, controle Git incorporado, realce de sintaxe, preenchimento de código inteligente, trechos de código e também refatoração de código.
- Como instalar a CLI do Azure
  - A CLI do Azure fornece uma experiência de linha de comando multiplataforma do Azure. Você trabalha diretamente de uma linha de comando para provisionar e gerenciar recursos.
- Como usar a CLI do Azure para criar um Hub IoT.

## <a name="what-you-need"></a>O que você precisa

- Uma conexão com a Internet para baixar as ferramentas e o software.
- Um computador que esteja executando o Ubuntu 16.04 ou posterior.

## <a name="install-git-and-nodejs"></a>Instalar o Git e o Node.js

Para instalar o Git e Node.js, siga estas etapas:

1. Pressione `Ctrl + Alt + T` para abrir um terminal.
2. Execute os seguintes comandos:

   ```bash
   sudo apt-get update
   curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
   sudo apt-get install -y nodejs
   sudo apt-get install git
   ```

## <a name="install-nodejs-development-tools"></a>Instalar ferramentas de desenvolvimento do Node.js

Você usa o [gulp.js](http://gulpjs.com/) para automatizar a implantação e execução de scripts.

Para instalar o gulp, execute o seguinte comando no terminal:

```bash
sudo npm install -g gulp
```

Se você tiver problemas com a instalação, consulte o [guia de solução de problemas](iot-hub-gateway-kit-c-troubleshooting.md) para obter soluções para problemas comuns.

> [!Note]
> Nó, NPM e Gulp são necessários para executar os scripts de automação desenvolvidos no Node.js.

## <a name="install-the-azure-cli"></a>Instalar a CLI do Azure

Para instalar a CLI do Azure, siga estas etapas:

1. Execute os seguintes comandos no terminal:

   ```bash
   sudo apt-get update
   sudo apt-get install -y libssl-dev libffi-dev
   sudo apt-get install -y python-dev
   sudo apt-get install -y build-essential
   sudo apt-get install -y python-pip
   sudo pip install --upgrade azure-cli
   sudo pip install --upgrade azure-cli-iot
   ```

   A instalação pode levar 5 minutos.

2. Verifique a instalação executando o comando a seguir:

   ```bash
   az iot -h
   ```
Se a instalação for bem-sucedida, você verá a seguinte saída.
![Verificar a instalação da CLI do Azure](media/iot-hub-gateway-kit-lessons/lesson2/az_iot_help_ubuntu.png)

### <a name="install-visual-studio-code"></a>Instalar o Visual Studio Code

Use o código do Visual Studio posteriormente no tutorial para editar arquivos de configuração.

[Baixe](https://code.visualstudio.com/docs/setup/linux) e instale o Visual Studio Code.

## <a name="summary"></a>Resumo

Você instalou todos os softwares e as ferramentas necessárias no computador host. A próxima tarefa é usar a CLI do Azure para criar um hub IoT e registrar seu dispositivo no seu hub IoT.

## <a name="next-steps"></a>Próximas etapas
[Criar um Hub IoT e registrar seu dispositivo](iot-hub-gateway-kit-c-lesson2-register-device.md)

