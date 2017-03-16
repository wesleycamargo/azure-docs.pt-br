---
title: Criar FQDN para uma VM do Windows no Portal do Azure | Microsoft Docs
description: "Saiba como criar um nome de domínio totalmente qualificado, ou FQDN, para uma máquina virtual baseada no Resource Manager no Portal do Azure."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: a2ae5887-76df-485e-ae19-0efd96df8600
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: 08c4d73714f2f78a2c2b2a61d4325b8b8264660f
ms.lasthandoff: 03/03/2017


---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-windows-vm"></a>Criar um nome de domínio totalmente qualificado no portal do Azure para uma VM Windows

Quando você cria uma VM (máquina virtual) no [portal do Azure](https://portal.azure.com) usando o modelo de implantação do Gerenciador de Recursos, um recurso de IP público para a máquina virtual é criado automaticamente. Use esse endereço IP para acessar a VM remotamente. Embora o portal não crie um [nome de domínio totalmente qualificado](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), ou FQDN, por padrão, você pode criar um após a criação da VM. Este artigo apresenta as etapas para criar um nome DNS ou FQDN.

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../includes/virtual-machines-common-portal-create-fqdn.md)]

Agora você pode se conectar à VM remotamente usando esse nome DNS, como para o protocolo RDP.

## <a name="next-steps"></a>Próximas etapas
Agora que sua VM tem um IP público e um nome DNS, é possível implantar estruturas comuns do aplicativo ou serviços, como IIS, SQL ou SharePoint.

Leia mais sobre como [usar o Resource Manager](../azure-resource-manager/resource-group-overview.md) para obter dicas sobre a criação de implantações do Azure.


