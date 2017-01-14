---
title: Obtenha as ferramentas do Azure (Windows 7 e superior) | Microsoft Docs
description: "Instale o Python e a interface de linha de comando do Azure (CLI do Azure) no Windows 7 e versões posteriores."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "serviço de nuvem do iot, cli do azure"
ms.assetid: a3c083b5-0d76-46af-bc77-2ad7d8aadc1e
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 155e5d6280d86b06b1718fc3032c2c224539183d
ms.openlocfilehash: 59d6edc1af60c9850a97378ac8758f0f245e68d6


---
# <a name="get-azure-tools-windows-7-and-later"></a>Obtenha as ferramentas do Azure (Windows 7 e superior)
> [!div class="op_single_selector"]
> * [Windows 7 e superior](iot-hub-raspberry-pi-kit-c-lesson2-get-azure-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-raspberry-pi-kit-c-lesson2-get-azure-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-raspberry-pi-kit-c-lesson2-get-azure-tools-mac.md)

## <a name="what-you-will-do"></a>O que você fará
Instale o Python e a Interface de linha de comando do Azure (CLI do Azure). Se você tiver problemas, procure as soluções na [página de solução de problemas](iot-hub-raspberry-pi-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>O que você aprenderá
Neste artigo, você aprenderá:
* Como instalar o Python.
* Como instalar a CLI do Azure.

## <a name="what-you-need"></a>O que você precisa
* Um computador Windows com conexão com a Internet.
* Uma assinatura ativa do Azure. Se não tiver uma conta do Azure, crie uma [conta gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

## <a name="install-python"></a>Instalar o Python
[Instale o Python](https://www.python.org/downloads/) no seu computador Windows. Você pode instalar o Python 2.7, 3.4 ou 3.5. Esse tutorial se baseia no Python 2.7. Se você já tiver instalado o Python, vá para a próxima seção e instale a CLI do Azure.

Você também precisa adicionar o caminho das pastas em que python.exe e pip.exe estão instalados na variável de ambiente `PATH` do sistema. Por padrão, python.exe é instalado em `C:\Python27` e pip.exe é instalado em `C:\Python27\Scripts`.

## <a name="install-the-azure-cli"></a>Instalar a CLI do Azure
A CLI do Azure fornece uma experiência de linha de comando multiplataforma do Azure. Você trabalha diretamente a partir de sua linha de comando para provisionar e gerenciar recursos.

Para instalar a CLI do Azure, siga estas etapas:

1. Abra uma janela de Prompt de comando como administrador.
2. Execute os seguintes comandos:

   ```bash
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```
3. Verifique a instalação executando o comando a seguir:

   ```bash
   az iot -h
   ```

Você verá a seguinte saída se a instalação foi bem-sucedida.

![Saída que indica êxito](media/iot-hub-raspberry-pi-lessons/lesson2/az_iot_help_win.png)

## <a name="summary"></a>Resumo
Você instalou a CLI do Azure. Sua próxima tarefa é criar sua identidade de dispositivo e Hub IoT do Azure usando a CLI do Azure.

## <a name="next-steps"></a>Próximas etapas
[Criar seu Hub IoT e registrar seu Raspberry Pi 3](iot-hub-raspberry-pi-kit-c-lesson2-prepare-azure-iot-hub.md)




<!--HONumber=Dec16_HO1-->


