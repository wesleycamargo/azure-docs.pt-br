---
title: Adicionar imagens Linux a pilha do Azure
description: Saiba como adicionar imagens Linux a pilha do Azure.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 64a860bc925b9c7499363c1fe39d03df88a9a51d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="add-linux-images-to-azure-stack"></a>Adicionar imagens Linux a pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Você pode implantar máquinas virtuais do Linux (VMs) na pilha do Azure, adicionando uma imagem baseada em Linux no Azure Marketplace de pilha. É a maneira mais fácil para adicionar uma imagem do Linux a pilha do Azure por meio do gerenciamento do Marketplace. Essas imagens foram preparadas e testadas quanto à compatibilidade com a pilha do Azure.

## <a name="marketplace-management"></a>Gerenciamento do Marketplace

Para fazer o download de imagens do Linux do Azure Marketplace, use os procedimentos no artigo a seguir. Selecione as imagens do Linux que você deseja oferecer aos usuários em sua pilha do Azure. 

[Fazer o download de itens do marketplace do Azure para a pilha do Azure](azure-stack-download-azure-marketplace-item.md).

Observe que há atualizações frequentes para essas imagens, por isso verifique gerenciamento Marketplace com frequência para manter atualizadas.

## <a name="prepare-your-own-image"></a>Preparar sua própria imagem

 Sempre que possível, baixe as imagens disponíveis por meio do gerenciamento de mercado que estejam preparados e testado para a pilha do Azure. 
 
 O agente Linux do Azure (geralmente chamado `WALinuxAgent` ou `walinuxagent`) é necessária, e nem todas as versões do agente funcionará na pilha do Azure. Você deve usar a versão 2.2.18 ou posterior se você criar sua própria imagem. Observe que [init nuvem](https://cloud-init.io/) não tem suporte na pilha do Azure no momento.

 Você pode preparar sua própria imagem do Linux usando as instruções a seguir:

   * [Distribuições com base em CentOS](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
   * [SLES e openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Ubuntu Server](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

    
## <a name="add-your-image-to-the-marketplace"></a>Adicionar a imagem do Marketplace
 
Execute [adicionar a imagem do Marketplace](azure-stack-add-vm-image.md). Verifique se o `OSType` parâmetro está definido como `Linux`.

Depois de adicionar a imagem do Marketplace, um item do Marketplace é criado e os usuários podem implantar uma máquina virtual Linux.
