---
title: "Conecte o Arduino ao IoT do Azure - Lição 2: obter ferramentas (macOS) | Microsoft Docs"
description: Instale o Python e a CLI do Azure (Interface de Linha de Comando do Azure) no macOS.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "cli do azure, serviço de nuvem iot, nuvem arduino"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started
ms.assetid: 9b719293-01d2-4a2d-9c49-476e67f4816d
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: ad8ee9d524dcb096ac7db4bb23ec16d56518742b
ms.contentlocale: pt-br
ms.lasthandoff: 01/24/2017

---
<a id="get-azure-tools-macos-1010" class="xliff"></a>

# Obtenha as ferramentas do Azure (macOS 10.10)

> [!div class="op_single_selector"]
> * [Windows 7 ou posterior][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

<a id="what-you-will-do" class="xliff"></a>

## O que você fará

Instalar a interface de linha de comando do Azure (CLI do Azure). Se tiver problemas, procure por soluções na [página de solução de problemas](iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md) de sua placa Adafruit Feather M0 WiFi Arduino.

<a id="what-you-will-learn" class="xliff"></a>

## O que você aprenderá
Neste artigo, você aprenderá:
* Como instalar a CLI do Azure.
* Como adicionar um subgrupo de IoT da CLI do Azure.

<a id="what-you-need" class="xliff"></a>

## O que você precisa
* Um Mac com conexão com a Internet.
* Uma assinatura ativa do Azure. Se não tiver uma conta do Azure, você pode [criar uma conta gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

<a id="install-python" class="xliff"></a>

## Instalar o Python
Embora o macOS venha com o Python 2.7 integrado, recomendamos que você instale o Python por meio do Homebrew. Consulte [Instalando Python no macOS](http://docs.python-guide.org/en/latest/starting/install/osx/).

Instale o Python e o pip executando o seguinte comando:

```bash
brew install python
```

<a id="install-the-azure-cli" class="xliff"></a>

## Instalar a CLI do Azure
A CLI do Azure fornece uma experiência de linha de comando multiplataforma do Azure. Você trabalha diretamente a partir de sua linha de comando para provisionar e gerenciar recursos.

Para instalar a CLI do Azure mais recente, siga estas etapas:

1. Execute os seguintes comandos em uma janela de terminal. Pode levar cinco minutos para instalar a CLI do Azure.

   ```bash
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```
2. Verifique a instalação executando o comando a seguir:

   ```bash
   az iot -h
   ```

Se a instalação for bem-sucedida, você verá a seguinte saída.

![Saída que indica êxito][output]

<a id="summary" class="xliff"></a>

## Resumo
Você instalou a CLI do Azure. Sua próxima tarefa é criar sua identidade de dispositivo e Hub IoT do Azure usando a CLI do Azure.

<a id="next-steps" class="xliff"></a>

## Próximas etapas
[Criar seu Hub IoT e registrar sua placa Arduino][create-your-iot-hub-and-register-your-arduino-board]


<!-- Images and links -->

[windows]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-win32.md
[ubuntu]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-ubuntu.md
[macos]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-mac.md
[output]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson2/az_iot_help_osx.png
[create-your-iot-hub-and-register-your-arduino-board]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md
