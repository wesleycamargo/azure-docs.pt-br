---
title: Criar o FQDN para uma VM no portal do Azure | Microsoft Docs
description: "Saiba como criar um nome de domínio totalmente qualificado, ou FQDN, para uma máquina virtual baseada no Resource Manager no Portal do Azure."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2cd6c249-a737-4a0a-b5ba-e1c09e551b30
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/14/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: f556fd0318accc19f0fa56fa7f2a8716ee6f1c02
ms.openlocfilehash: 9bd1032c8a831ab22bbebad8881a0f6ea434e360


---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal"></a>Criar um nome de domínio totalmente qualificado no portal do Azure
Quando você cria uma VM (máquina virtual) no [portal do Azure](https://portal.azure.com) usando o modelo de implantação do Gerenciador de Recursos, um recurso de IP público para a máquina virtual é criado automaticamente. Use esse endereço IP para acessar a VM remotamente. Embora o portal não crie um [nome de domínio totalmente qualificado](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), ou FQDN, por padrão, você pode adicionar um depois que a VM for criada. Este artigo apresenta as etapas para criar um nome DNS ou FQDN.

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../includes/virtual-machines-common-portal-create-fqdn.md)]

Agora você pode se conectar à VM remotamente usando esse nome DNS, assim como com `ssh ops@mydns.westus.cloudapp.azure.com`.

## <a name="next-steps"></a>Próximas etapas
Agora que sua VM tem um IP público e um nome DNS, é possível implantar estruturas comuns do aplicativo ou serviços, como o nginx, MongoDB, Docker, etc.

Leia mais sobre como [usar o Resource Manager](../azure-resource-manager/resource-group-overview.md) para obter dicas sobre a criação de implantações do Azure.




<!--HONumber=Nov16_HO3-->


