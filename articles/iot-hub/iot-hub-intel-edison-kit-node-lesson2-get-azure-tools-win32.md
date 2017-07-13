---
title: "Conectar o Intel Edison (Nó) ao IoT do Azure - Lição 2: ferramentas do Azure (Windows) | Microsoft Docs"
description: "Instale o Python e a interface de linha de comando do Azure (CLI do Azure) no Windows 7 e versões posteriores."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "cli do azure, serviço de nuvem iot, nuvem arduino"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-node-get-started
ms.assetid: 60631b54-6d2e-4e8a-88bf-7c2f8e7e1f29
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: 181d7113bda6e9d39bc24c1892940803fc03fd6b
ms.contentlocale: pt-br
ms.lasthandoff: 01/25/2017

---
<a id="get-azure-tools-windows-7-and-later" class="xliff"></a>

# Obtenha as ferramentas do Azure (Windows 7 e superior)
> [!div class="op_single_selector"]
> * [Windows 7 e posterior][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

<a id="what-you-will-do" class="xliff"></a>

## O que você fará
Instale o Python e a Interface de linha de comando do Azure (CLI do Azure). Se você tiver problemas, procure por soluções na [página de solução de problemas][troubleshooting].

<a id="what-you-will-learn" class="xliff"></a>

## O que você aprenderá
Neste artigo, você aprenderá:
* Como instalar o Python.
* Como instalar a CLI do Azure.

<a id="what-you-need" class="xliff"></a>

## O que você precisa
* Um computador Windows com conexão com a Internet.
* Uma assinatura ativa do Azure. Se não tiver uma conta do Azure, crie uma [conta gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

<a id="install-python" class="xliff"></a>

## Instalar o Python
[Instale o Python](https://www.python.org/downloads/) no seu computador Windows. Você pode instalar o Python 2.7, 3.4 ou 3.5. Esse tutorial se baseia no Python 2.7. Se você já tiver instalado o Python, vá para a próxima seção e instale a CLI do Azure.

Você também precisa adicionar o caminho das pastas em que python.exe e pip.exe estão instalados na variável de ambiente `PATH` do sistema. Por padrão, python.exe é instalado em `C:\Python27` e pip.exe é instalado em `C:\Python27\Scripts`.

<a id="install-the-azure-cli" class="xliff"></a>

## Instalar a CLI do Azure
A CLI do Azure fornece uma experiência de linha de comando multiplataforma do Azure. Você trabalha diretamente a partir de sua linha de comando para provisionar e gerenciar recursos.

Para instalar a CLI do Azure, siga estas etapas:

1. Abra uma janela de Prompt de comando como administrador.
2. Execute os seguintes comandos:

   ```cmd
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```
3. Verifique a instalação executando o comando a seguir:

   ```cmd
   az iot -h
   ```

Você verá a seguinte saída se a instalação foi bem-sucedida.

![Saída que indica êxito](media/iot-hub-intel-edison-lessons/lesson2/az_iot_help_win.png)

<a id="summary" class="xliff"></a>

## Resumo
Você instalou a CLI do Azure. Sua próxima tarefa é criar sua identidade de dispositivo e Hub IoT do Azure usando a CLI do Azure.

<a id="next-steps" class="xliff"></a>

## Próximas etapas
[Criar seu Hub IoT e registrar o Intel Edison][create-your-iot-hub-and-register-intel-edison]
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[create-your-iot-hub-and-register-intel-edison]: iot-hub-intel-edison-kit-node-lesson2-prepare-azure-iot-hub.md
[windows]: iot-hub-intel-edison-kit-node-lesson2-get-azure-tools-win32.md
[ubuntu]: iot-hub-intel-edison-kit-node-lesson2-get-azure-tools-ubuntu.md
[macos]: iot-hub-intel-edison-kit-node-lesson2-get-azure-tools-mac.md

