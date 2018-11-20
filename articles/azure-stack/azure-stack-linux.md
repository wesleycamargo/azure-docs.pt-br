---
title: Adicionar imagens do Linux para o Azure Stack
description: Saiba como adicionar imagens do Linux para o Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 3154c3a5a677f208973d442ff3875bb5ee8df6a1
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52160623"
---
# <a name="add-linux-images-to-azure-stack"></a>Adicionar imagens do Linux para o Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Você pode implantar máquinas virtuais do Linux (VMs) no Azure Stack, adicionando uma imagem baseada em Linux no Azure Stack Marketplace. É a maneira mais fácil para adicionar uma imagem do Linux para o Azure Stack por meio do gerenciamento do Marketplace. Essas imagens foram preparadas e testadas quanto à compatibilidade com o Azure Stack.

## <a name="marketplace-management"></a>Gerenciamento do Marketplace

Para fazer o download de imagens do Linux no Azure Marketplace, use os procedimentos de [baixar itens do marketplace do Azure para o Azure Stack](azure-stack-download-azure-marketplace-item.md) artigo. Selecione as imagens do Linux que você deseja oferecer aos usuários no Azure Stack. 

Observe que há atualizações frequentes para essas imagens, portanto, verifique o gerenciamento do Marketplace com frequência para manter atualizado.

## <a name="prepare-your-own-image"></a>Preparar sua própria imagem

Sempre que possível, baixe as imagens disponíveis por meio do gerenciamento do Marketplace que estejam preparados e testado para o Azure Stack. 
 
Agente Linux do Azure (normalmente chamado `WALinuxAgent` ou `walinuxagent`) é necessário, e nem todas as versões do agente funcionará no Azure Stack. Você deve usar a versão 2.2.18 ou posterior, se você cria sua própria imagem. Observe que [cloud_init](https://cloud-init.io/) não tem suporte no Azure Stack no momento.

Você pode preparar sua própria imagem do Linux usando as instruções a seguir:

* [Distribuições com base em CentOS](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
* [SLES e openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu Server](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

    
## <a name="add-your-image-to-the-marketplace"></a>Adicione sua imagem no Marketplace
 
Siga [adicionar a imagem do Marketplace](azure-stack-add-vm-image.md). Certifique-se de que o `OSType` parâmetro é definido como `Linux`.

Depois de adicionar a imagem no Marketplace, um item do Marketplace é criado e os usuários podem implantar uma máquina virtual Linux.

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos para obter mais informações:

- [Baixar itens do marketplace do Azure para o Azure Stack](azure-stack-download-azure-marketplace-item.md)
- [Visão geral do Azure Stack Marketplace](azure-stack-marketplace.md)