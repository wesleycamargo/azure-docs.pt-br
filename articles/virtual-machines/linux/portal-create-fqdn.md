---
title: Criar FQDN para uma VM do Linux no Portal do Azure | Microsoft Docs
description: Saiba como criar um nome de domínio totalmente qualificado, ou FQDN, para uma máquina virtual baseada no Resource Manager no Portal do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2cd6c249-a737-4a0a-b5ba-e1c09e551b30
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 54d1f77823b982ee8f49122c46b0a01cb27390f2
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129410"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Criar um nome de domínio totalmente qualificado no Portal do Azure para uma VM Linux

Quando você cria uma máquina virtual (VM) no [portal do Azure](https://portal.azure.com), um recurso de IP público para a máquina virtual é criado automaticamente. Use esse endereço IP para acessar a VM remotamente. Embora o portal não crie um [nome de domínio totalmente qualificado](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) ou FQDN, você poderá adicionar um depois que a VM for criada. Este artigo apresenta as etapas para criar um nome DNS ou FQDN.

## <a name="create-a-fqdn"></a>Criar um FQDN
Este artigo pressupõe que você já tenha criado uma VM. Se necessário, [crie uma VM no portal](quick-create-portal.md) ou [com a CLI do Azure](quick-create-cli.md). Siga estas etapas depois que a VM estiver em execução:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Agora você pode se conectar à VM remotamente usando esse nome DNS, assim como com `ssh azureuser@mydns.westus.cloudapp.azure.com`.

## <a name="next-steps"></a>Próximas etapas
Agora que sua VM tem um IP público e um nome DNS, é possível implantar estruturas comuns do aplicativo ou serviços, como o nginx, MongoDB, Docker, etc.

Leia mais sobre como [usar o Resource Manager](../../azure-resource-manager/resource-group-overview.md) para obter dicas sobre a criação de implantações do Azure.

