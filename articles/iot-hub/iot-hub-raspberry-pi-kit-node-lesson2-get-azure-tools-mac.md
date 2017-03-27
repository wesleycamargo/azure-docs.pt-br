---
title: "Conecte o Raspberry Pi (Nó) ao IoT do Azure - Lição 2: Obter ferramentas (Ubuntu) | Microsoft Docs"
description: Instale o Python e a interface de linha de comando do Azure (CLI do Azure) no macOS.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "serviço de nuvem do iot, cli do azure"
ms.assetid: 1814b703-2d81-45db-aff0-eb338c97f120
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 455e5a28f54fb0af4e7d4601ecef666b74950169
ms.lasthandoff: 03/10/2017


---
# <a name="get-azure-tools-macos-1010"></a>Obtenha as ferramentas do Azure (macOS 10.10)
> [!div class="op_single_selector"]
> * [Windows 7 e superior](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-mac.md)

## <a name="what-you-will-do"></a>O que você fará
Instalar a interface de linha de comando do Azure (CLI do Azure). Se você tiver problemas, procure as soluções na [página de solução de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="what-you-will-learn"></a>O que você aprenderá
Neste artigo, você aprenderá:
* Como instalar a CLI do Azure.
* Como adicionar um subgrupo de IoT da CLI do Azure.

## <a name="what-you-need"></a>O que você precisa
* Um Mac com conexão com a Internet.
* Uma assinatura ativa do Azure. Se não tiver uma conta do Azure, você pode [criar uma conta gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

## <a name="install-python"></a>Instalar o Python
Embora o macOS venha com o Python 2.7 integrado, recomendamos que você instale o Python por meio do Homebrew. Consulte [Instalando Python no macOS](http://docs.python-guide.org/en/latest/starting/install/osx/).

Instale o Python e o pip executando o seguinte comando:

```bash
brew install python
```

## <a name="install-the-azure-cli"></a>Instalar a CLI do Azure
A CLI do Azure fornece uma experiência de linha de comando multiplataforma do Azure. Você trabalha diretamente da linha de comando para provisionar e gerenciar recursos. 

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

![Saída que indica êxito](media/iot-hub-raspberry-pi-lessons/lesson2/az_iot_help_osx.png)

## <a name="summary"></a>Resumo
Você instalou a CLI do Azure. Sua próxima tarefa é criar sua identidade de dispositivo e Hub IoT do Azure usando a CLI do Azure.

## <a name="next-steps"></a>Próximas etapas
[Criar seu Hub IoT e registrar seu Raspberry Pi 3](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md)


