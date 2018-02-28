---
title: Adicionar imagens Linux a pilha do Azure
description: Saiba como adicionar imagens Linux a pilha do Azure.
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 6e6f9ca3b314ee2f58d8007e7ddc93ddd213e361
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/24/2018
---
# <a name="add-linux-images-to-azure-stack"></a>Adicionar imagens Linux a pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Você pode implantar máquinas virtuais do Linux (VMs) na pilha do Azure, adicionando uma imagem baseada em Linux no Azure Marketplace de pilha. É a maneira mais fácil para adicionar uma imagem do Linux a pilha do Azure por meio do gerenciamento do Marketplace. Essas imagens foram preparadas e testadas quanto à compatibilidade com a pilha do Azure.

## <a name="marketplace-management"></a>Gerenciamento do Marketplace

Para fazer o download de imagens do Linux do Azure Marketplace, use os procedimentos no artigo a seguir. Selecione as imagens do Linux que você deseja oferecer aos usuários em sua pilha do Azure.

[Fazer o download de itens do marketplace do Azure para a pilha do Azure](azure-stack-download-azure-marketplace-item.md).

## <a name="prepare-your-own-image"></a>Preparar sua própria imagem

Você pode preparar sua própria imagem do Linux usando uma das seguintes instruções:

   * [Distribuições com base em CentOS](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [SLES e openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Ubuntu](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

1. Baixe e instale o [agente Linux do Azure](https://github.com/Azure/WALinuxAgent/).

    O Azure Linux Agent versão 2.2.2 ou versão posterior é necessário para provisionar a VM do Linux na pilha do Azure e algumas versões não funcionam (2.2.12 e 2.2.13, para obter exemplos). Muitas das distribuições listadas anteriormente incluem já uma versão do agente (geralmente chamado `WALinuxAgent` ou `walinuxagent`). Se você criar uma imagem personalizada, você deve instalar o agente manualmente. Pode ser determinada a versão instalada do nome do pacote ou executando `/usr/sbin/waagent -version` na VM.

    Siga as instruções abaixo para instalar o agente Linux do Azure manualmente-

   a. Primeiro, baixe o agente Linux do Azure mais recente do [GitHub](https://github.com/Azure/WALinuxAgent/releases), exemplo:

            # wget https://github.com/Azure/WALinuxAgent/archive/v2.2.21.tar.gz
   b. Desempacote o agente do Azure:

            # tar -vzxf v2.2.21.tar.gz
   c. Instalar o python setuptools

        **Debian / Ubuntu**

            # sudo apt-get update
            # sudo apt-get install python-setuptools

        **Ubuntu 16.04+**

            # sudo apt-get install python3-setuptools

        **RHEL / CentOS / Oracle Linux**

            # sudo yum install python-setuptools
   d. Instale o agente do Azure:

            # cd WALinuxAgent-2.2.21
            # sudo python3 setup.py install --register-service

     Sistemas com Python 2. x e 3. x instalados de Python lado a lado podem ser necessário executar o comando a seguir:

         sudo python3 setup.py install --register-service
     Para obter mais informações, consulte o Azure Linux Agent [Leiame](https://github.com/Azure/WALinuxAgent/blob/master/README.md).
2. [Adicionar a imagem do Marketplace](azure-stack-add-vm-image.md). Verifique se o `OSType` parâmetro está definido como `Linux`.
3. Depois de adicionar a imagem do Marketplace, um item do Marketplace é criado e os usuários podem implantar uma máquina virtual Linux.

## <a name="next-steps"></a>Próximas etapas
[Visão geral da oferta de serviços na pilha do Azure](azure-stack-offer-services-overview.md)
