---
title: "Conectar o Intel Edison (C) ao IoT do Azure - Lição 1: obter ferramentas (Windows)| Microsoft Docs"
description: "Baixe e instale as ferramentas e softwares necessários para o primeiro aplicativo de exemplo do Edison no Windows 7 e em versões posteriores."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: ferramentas de desenvolvimento arduino, desenvolvimento de iot, software de iot, software de Internet das coisas, instalar o git no windows, instalar node js no windows
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-c-get-started
ms.assetid: 7d29a358-544d-4657-a504-5ed9b79c2925
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: 79dcbe8cbefe4f607991359fbfc6948b5e90ace9
ms.contentlocale: pt-br
ms.lasthandoff: 01/25/2017

---
<a id="get-the-tools-windows-7-or-later" class="xliff"></a>

# Obtenha as ferramentas (Windows 7 ou superior)
> [!div class="op_single_selector"]
> * [Windows 7 ou posterior][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

<a id="what-you-will-do" class="xliff"></a>

## O que você fará
Baixará as ferramentas de desenvolvimento e o software para o primeiro aplicativo de exemplo do Intel Edison. Se você tiver problemas, procure por soluções na [página de solução de problemas][troubleshooting].

> [!NOTE]
> Embora a linguagem de programação da lógica principal seja C, ferramentas Node.js são usadas nas lições para compilar e implantar aplicativos de exemplo.

<a id="what-you-will-learn" class="xliff"></a>

## O que você aprenderá
Neste artigo, você aprenderá:

* Como instalar o Git e o Node.js.
  * [Git](https://git-scm.com) é um software livre de sistema de controle de versão distribuído. O aplicativo de exemplo para este artigo está armazenado em Git.
  * O [Node.js](https://nodejs.org/en/) é um tempo de execução de JavaScript com um avançado ecossistema de pacote.
* Como usar o NPM (gerenciador de pacotes Node.js) para instalar ferramentas de desenvolvimento adicionais do Node.js.
  * O requisito mínimo de versão do Node.js é 4.5 LTS.
  * O [NPM](https://www.npmjs.com) é um dos gerenciadores de pacote para o Node.js.

<a id="what-you-need" class="xliff"></a>

## O que você precisa

Para concluir esta operação, você precisará de:

* Uma conexão com a Internet para baixar as ferramentas de desenvolvimento e o software.
* Um computador que esteja executando o Windows.

<a id="install-git-and-nodejs" class="xliff"></a>

## Instalar o Git e o Node.js

Clique nos links abaixo para baixar e instalar o Git e o Node.js LTS para Windows.

* [Obtenha o Git para Windows](https://git-scm.com/download/win/)
* [Obtenha o Node.js LTS para o Windows](https://nodejs.org/en/)

<a id="install-additional-nodejs-development-tools" class="xliff"></a>

## Instalar ferramentas adicionais de desenvolvimento do Node.js

Use [gulp.js](http://gulpjs.com) para automatizar a implantação do aplicativo de exemplo no Edison.

Inicie um prompt de comando como administrador. Instale o `gulp` executando o seguinte comando:

```cmd
npm install -g gulp
```

Se tiver problemas ao instalar o Node.js e essas ferramentas de desenvolvimento adicionais do Node.js no seu computador, consulte o [guia de solução de problemas][troubleshooting] para soluções de problemas comuns.

<a id="install-visual-studio-code" class="xliff"></a>

## Instalar o Visual Studio Code

[Baixe](https://code.visualstudio.com/docs/setup/windows) e instale o Visual Studio Code. O Visual Studio Code é um editor de código-fonte leve mas poderoso para Windows, Linux e macOS. Use este editor posteriormente no tutorial para editar o código de exemplo.

<a id="summary" class="xliff"></a>

## Resumo

Você instalou as ferramentas de desenvolvimento e software necessários para o primeiro aplicativo de exemplo. A tarefa seguinte é criar, implantar e executar o aplicativo de exemplo no Edison.

<a id="next-steps" class="xliff"></a>

## Próximas etapas

[Criar e implantar o aplicativo de piscar][create-and-deploy-the-blink-application]

<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[create-and-deploy-the-blink-application]: iot-hub-intel-edison-kit-c-lesson1-deploy-blink-app.md
[windows]: iot-hub-intel-edison-kit-c-lesson1-get-the-tools-win32.md
[ubuntu]: iot-hub-intel-edison-kit-c-lesson1-get-the-tools-ubuntu.md
[macos]: iot-hub-intel-edison-kit-c-lesson1-get-the-tools-mac.md

