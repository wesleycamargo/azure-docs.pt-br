---
title: "Dispositivo simulado e Gateway do IoT do Azure - Lição 2: obter ferramentas (Windows) | Microsoft Docs"
description: Instale as ferramentas e o software no computador host executando o Windows, crie um Hub IoT e registre seu dispositivo no Hub IoT.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "desenvolvimento de IoT, software de IoT, serviço de nuvem de IoT, software de Internet das Coisas, CLI do Azure, instalar o git no windows, executar gulp, instalar node js no windows, instalar npm no windows, instalar Python no windows"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-gateway-kit-c-lesson1-set-up-nuc
ms.assetid: c16eee4c-8756-454b-82bf-3eb0dd51aa5f
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: b07c6c383f31f77a78628593a60b7b76a49bca4f
ms.contentlocale: pt-br
ms.lasthandoff: 01/25/2017

---
<a id="get-the-tools-windows-7-and-later" class="xliff"></a>

# Obter as ferramentas do Azure (Windows 7 e superior)
> [!div class="op_single_selector"]
> * [Windows 7 ou superior](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-mac.md)

<a id="what-you-will-do" class="xliff"></a>

## O que você fará

- Instalar o Git, Node.js, o Gulp e o Python.
- Instalar a interface de linha de comando do Azure (CLI do Azure). 

Se você tiver problemas, procure as soluções na [página de solução de problemas](iot-hub-gateway-kit-c-sim-troubleshooting.md).

<a id="what-you-will-learn" class="xliff"></a>

## O que você aprenderá

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

<a id="what-you-need" class="xliff"></a>

## O que você precisa

- Uma conexão com a Internet para baixar as ferramentas e o software.
- Um computador com Windows.

<a id="install-git-and-nodejs" class="xliff"></a>

## Instalar o Git e o Node.js

Clique nos links a seguir para baixar e instalar o Git e o Node.js LTS para Windows.

- [Obtenha o Git para Windows](https://git-scm.com/download/win/)
- [Obtenha o Node.js LTS para o Windows](https://nodejs.org/en/)

<a id="install-nodejs-development-tools" class="xliff"></a>

## Instalar ferramentas de desenvolvimento do Node.js

Você usa o [gulp.js](http://gulpjs.com/) para automatizar a implantação e execução de scripts.

Pressione `Windows + R`, digite `cmd` e pressione `Enter` para abrir uma janela do prompt de comando como administrador e execute o seguinte comando:

```cmd
npm install -g gulp
```

Se você tiver problemas com a instalação, consulte o [guia de solução de problemas](iot-hub-gateway-kit-c-sim-troubleshooting.md) para obter soluções para problemas comuns.

> [!Note]
> Nó, NPM e Gulp são necessários para executar os scripts de automação desenvolvidos no Node.js.

<a id="install-python" class="xliff"></a>

## Instalar o Python

Você pode escolher entre Python 2.7, 3.4 ou 3.5. Neste tutorial, usamos o Python 2.7. Se você já tiver instalado o Python, vá para a próxima seção.

[Obter o Python para Windows](https://www.python.org/downloads/)

Você também precisa adicionar o caminho das pastas em que Python.exe e pip.exe estão instalados na variável de ambiente `PATH` do sistema. Por padrão, python.exe é instalado em `C:\Python27` e pip.exe é instalado em `C:\Python27\Scripts`.

<a id="install-the-azure-cli" class="xliff"></a>

## Instalar a CLI do Azure

Para instalar a CLI do Azure, siga estas etapas:

1. Abra uma janela de Prompt de comando como administrador.

2. Instale a CLI do Azure executando os seguintes comandos:

   ```cmd
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```

   A instalação pode levar 5 minutos.

3. Verifique a instalação executando o comando a seguir:

   ```cmd
   az iot -h
   ```

   Se a instalação for bem-sucedida, você verá a seguinte saída.

   ![Verificar a instalação da CLI do Azure](media/iot-hub-gateway-kit-lessons/lesson2/az_iot_help_win.png)

<a id="install-visual-studio-code" class="xliff"></a>

## Instalar o Visual Studio Code

Use o código do Visual Studio posteriormente no tutorial para editar arquivos de configuração.

[Baixe](https://code.visualstudio.com/docs/setup/windows) e instale o Visual Studio Code.

<a id="summary" class="xliff"></a>

## Resumo

Você instalou todos os softwares e as ferramentas necessárias no computador host. A próxima tarefa é usar a CLI do Azure para criar um hub IoT e registrar seu dispositivo no seu hub IoT.

<a id="next-steps" class="xliff"></a>

## Próximas etapas
[Criar um Hub IoT e registrar seu dispositivo](iot-hub-gateway-kit-c-sim-lesson2-register-device.md)

