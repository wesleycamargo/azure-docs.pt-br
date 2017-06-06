---
title: " Gerenciar um servidor de processo em execução em Azure(Classic) | Microsoft Docs"
description: Este artigo descreve como configurar um failback Server(Classic) do processo no Azure.
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 06/05/2017
ms.author: anoopkv
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: cceda131fc9eaa74f6328c8dd68a8e3885877061
ms.contentlocale: pt-br
ms.lasthandoff: 05/02/2017

---

# <a name="manage-a-process-server-running-in-azure-classic"></a>Gerenciar um servidor de processo em execução no Azure (clássico)
> [!div class="op_single_selector"]
> * [Azure Clássico ](./site-recovery-vmware-setup-azure-ps-classic.md)
> * [Gerenciador de Recursos](./site-recovery-vmware-setup-azure-ps-resource-manager.md)

Durante o failback, é recomendável implantar o servidor de processo no Azure se houver alta latência entre a rede Virtual do Azure e sua rede local. Este artigo descreve como você pode definir, configurar e gerenciar os servidores de processo em execução no Azure.

> [!NOTE]
> Use este artigo se você tiver utilizado o Classic como o modelo de implantação para as máquinas virtuais durante o failover. Se você usou o Gerenciador de Recursos como o modelo de implantação, siga as etapas em [Como configurar um servidor de processo de Failback (Gerenciador de recursos)](./site-recovery-vmware-setup-azure-ps-resource-manager.md)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [site-recovery-vmware-process-server-prereq](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]

## <a name="deploy-a-process-server-on-azure"></a>Implantar um Servidor de Processos no Azure

1. No Azure Marketplace, criar uma máquina virtual usando o **servidor Microsoft Azure Site Recovery processo V2** </br>
    ![Marketplace_image_1](./media/site-recovery-vmware-setup-azure-ps-classic/marketplace-ps-image.png)
2. Certifique-se de que você selecione o modelo de implantação como **clássico** </br>
  ![Marketplace_image_2](./media/site-recovery-vmware-setup-azure-ps-classic/marketplace-ps-image-classic.png)
3. No assistente criar máquina virtual > configurações básicas, certifique-se de selecionar a assinatura e o local para onde foi feito o failover de máquinas virtuais.</br>
  ![create_image_1](./media/site-recovery-vmware-setup-azure-ps-classic/azureps-classic-basic-info.png)
4. Verifique se a máquina virtual está conectada à rede Virtual do Azure ao qual a falha pela máquina virtual está conectado.</br>
  ![create_image_2](./media/site-recovery-vmware-setup-azure-ps-classic/azureps-classic-settings.png)
5. Depois de máquina virtual do servidor de processo é configurada, você precisa entrar e registrá-lo com o servidor de configuração.

> [!NOTE]
> Para poder usar este servidor de processo para failback, você precisa registrá-lo com o servidor de configuração local.

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Registrando o servidor de processo (em execução no Azure) para um servidor de configuração (em execução local)

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

## <a name="upgrading-the-process-server-to-latest-version"></a>Atualizar o servidor de processo para a versão mais recente.

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-upgrade-process-server.md)]

## <a name="unregistering-the-process-server-running-in-azure-from-a-configuration-server-running-on-premises"></a>Cancelando o registro de servidor de processo (em execução no Azure) de um servidor de configuração (em execução local)

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

