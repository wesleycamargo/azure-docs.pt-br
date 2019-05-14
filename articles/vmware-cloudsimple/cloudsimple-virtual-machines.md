---
title: Solução CloudSimple - visão geral de máquinas virtuais do VMware
description: Saiba mais sobre as máquinas virtuais de CloudSimple e seus benefícios.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 77a2901ae2d81f42780110b1576c1f32c7ff397b
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65209497"
---
# <a name="cloudsimple-virtual-machines-overview"></a>Visão geral de máquinas virtuais CloudSimple

CloudSimple permite que você gerencie as VMs VMware no portal do Azure.  Um cluster ou em um pool de recursos do seu cluster do vSphere é gerenciado por meio do Azure, mapeando-o à sua assinatura.  Máquina de virtual CloudSimple traz o gerenciamento de autoatendimento de máquinas virtuais VMware do portal do Azure.  

Para criar uma VM CloudSimple do Azure, um modelo de VM deve existir no seu vCenter de nuvem privada.  O modelo é usado para personalizar o sistema operacional e aplicativos.  O modelo de VM pode ser protegido para atender às políticas de segurança da empresa.  Você pode usar o modelo para criar VMs e consumi-los no portal do Azure usando um modelo de autoatendimento.

## <a name="benefits"></a>Benefícios

Máquinas virtuais de CloudSimple do portal do Azure fornecem um mecanismo de autoatendimento para usuários criar e gerenciar máquinas virtuais VMware.

* Criar uma VM de CloudSimple no vCenter sua nuvem privada
* Gerenciar as propriedades da VM
  * Adicionar ou remover discos
  * Adicionar ou remover NICs
* Operações de energia da VM CloudSimple
  * Ligar e desligar
  * Redefinir VM
* Excluir VM

## <a name="next-steps"></a>Próximas etapas

* Saiba como [consuma a VMs do VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba como [mapear sua assinatura do Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)