---
title: "Conectar o Intel Edison (C) ao IoT do Azure - Lição 2: ferramentas do Azure (Windows) | Microsoft Docs"
description: "Instale o Python e a interface de linha de comando do Azure (CLI do Azure) no Windows 7 e versões posteriores."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "cli do azure, serviço de nuvem iot, nuvem arduino"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-c-get-started
ms.assetid: 1035760e-cdd1-4d99-8003-067e98b29762
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: c31847b0a4b630f999f64c139f6b9b44623d847e
ms.lasthandoff: 01/25/2017


---
# <a name="get-azure-tools-windows-7-and-later"></a>Obtenha as ferramentas do Azure (Windows 7 e superior)
> [!div class="op_single_selector"]
> * [Windows 7 e posterior][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

## <a name="what-you-will-do"></a>O que você fará
Instale o Python e a Interface de linha de comando do Azure (CLI do Azure). Se você tiver problemas, procure por soluções na [página de solução de problemas][troubleshooting].

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

## <a name="summary"></a>Resumo
Você instalou a CLI do Azure. Sua próxima tarefa é criar sua identidade de dispositivo e Hub IoT do Azure usando a CLI do Azure.

## <a name="next-steps"></a>Próximas etapas
[Criar seu Hub IoT e registrar o Intel Edison][create-your-iot-hub-and-register-intel-edison]
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[create-your-iot-hub-and-register-intel-edison]: iot-hub-intel-edison-kit-c-lesson2-prepare-azure-iot-hub.md
[windows]: iot-hub-intel-edison-kit-c-lesson2-get-azure-tools-win32.md
[ubuntu]: iot-hub-intel-edison-kit-c-lesson2-get-azure-tools-ubuntu.md
[macos]: iot-hub-intel-edison-kit-c-lesson2-get-azure-tools-mac.md

