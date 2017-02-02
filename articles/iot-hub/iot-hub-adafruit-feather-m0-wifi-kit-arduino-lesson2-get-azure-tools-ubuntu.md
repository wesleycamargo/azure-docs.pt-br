---
title: "Obter ferramentas do Azure para o Kit de início de IoT do Azure (Ubuntu 16.04) | Microsoft Docs"
description: Instale o Python e a CLI do Azure (Interface de Linha de Comando do Azure) no Ubuntu.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "cli do azure, serviço de nuvem iot, nuvem arduino"
ms.assetid: bb5cb602-7292-4772-ac90-c0b52ebc8340
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 9e8084fe26229ef9ed1676c0a3c34e0ee7be88b1
ms.openlocfilehash: 1ad2afc531bfa4ce4afe599d30f9f9a699339976


---
# <a name="get-azure-tools-ubuntu-1604"></a>Obtenha as ferramentas do Azure (Ubuntu 16.04)

> [!div class="op_single_selector"]
> * [Windows 7 ou posterior][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

## <a name="what-you-will-do"></a>O que você fará

Instalar a interface de linha de comando do Azure (CLI do Azure). Se tiver problemas, procure por soluções na [página de solução de problemas](iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md) de sua placa Adafruit Feather M0 WiFi Arduino.

## <a name="what-you-will-learn"></a>O que você aprenderá
Neste artigo, você aprenderá:
* Como instalar a CLI do Azure.
* Como adicionar um subgrupo de IoT da CLI do Azure.

## <a name="what-you-need"></a>O que você precisa
* Um computador Ubuntu com conexão com a Internet.
* Uma assinatura ativa do Azure. Se você não tiver uma conta, poderá criar uma [conta de avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

## <a name="install-the-azure-cli"></a>Instalar a CLI do Azure
A CLI do Azure fornece uma experiência de linha de comando multiplataforma para o Azure, permitindo que você trabalhe diretamente da linha de comando para provisionar e gerenciar recursos.

Para instalar a CLI do Azure mais recente, siga estas etapas:

1. Execute os seguintes comandos em uma janela de terminal. Pode levar cinco minutos para instalar a CLI do Azure.

   ```bash
   sudo apt-get update
   sudo apt-get install -y libssl-dev libffi-dev
   sudo apt-get install -y python-dev
   sudo apt-get install -y build-essential
   sudo apt-get install -y python-pip
   sudo pip install --upgrade azure-cli
   sudo pip install --upgrade azure-cli-iot
   ```
2. Verifique a instalação executando o comando a seguir:

   ```bash
   az iot -h
   ```

Se a instalação for bem-sucedida, você verá a seguinte saída.

![Saída que indica êxito][output]

## <a name="summary"></a>Resumo
Você instalou a CLI do Azure. Sua próxima tarefa é criar sua identidade de dispositivo e Hub IoT do Azure usando a CLI do Azure.

## <a name="next-steps"></a>Próximas etapas
[Criar seu Hub IoT e registrar sua placa Arduino][create-your-iot-hub-and-register-your-arduino-board]
<!-- Images and links -->

[windows]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-win32.md
[ubuntu]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-ubuntu.md
[macos]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-mac.md
[output]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson2/az_iot_help_ubuntu.png
[create-your-iot-hub-and-register-your-arduino-board]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md


<!--HONumber=Dec16_HO2-->


