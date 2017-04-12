---
title: Criar uma identidade corporativa ou de estudante no AAD para Windows | Microsoft Docs
description: "Saiba como criar uma identidade corporativa ou de estudante no Azure Active Directory para usar com suas máquinas virtuais Windows."
services: virtual-machines-windows
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: d07dca34-618a-48aa-9971-03d9c1210f4a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/23/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 7694b959a384aaed213adc31e02debca31b7c131
ms.lasthandoff: 04/03/2017


---
# <a name="creating-a-work-or-school-identity-in-azure-active-directory-to-use-with-windows-vms"></a>Criando uma identidade corporativa ou de estudante no Azure Active Directory para usar com VMs do Windows
Se você tiver criado uma conta pessoal do Azure ou tiver uma assinatura pessoal do MSDN e criou a conta do Azure para aproveitar os créditos Azure no MSDN, você usou uma identidade da *conta da Microsoft* para criá-la. Para que muitos recursos excelentes do Azure (como os [modelos de grupo de recursos](../../azure-resource-manager/resource-group-overview.md)) funcionem, é necessário ter uma conta corporativa ou de estudante (uma identidade gerenciada pelo Azure Active Directory). Você pode seguir as instruções abaixo para criar uma nova conta corporativa ou de estudante, porque, felizmente, uma das melhores coisas sobre sua conta pessoal do Azure pessoal é que ela vem com um domínio do Active Directory do Azure padrão, que você pode usar para criar uma nova corporativa ou de estudante, que pode usar com os recursos do Azure que necessitam dela.

No entanto, alterações recentes possibilitam gerenciar sua assinatura com qualquer tipo de conta do Azure usando o `azure login` método de logon interativo descrito [aqui](../../xplat-cli-connect.md). Você pode usar esse mecanismo, ou seguir as instruções abaixo. Você também pode [criar uma identidade corporativa ou de estudante no Azure Active Directory para usar com VMs do Linux](../linux/create-aad-work-id.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

[!INCLUDE [virtual-machines-common-create-aad-work-id](../../../includes/virtual-machines-common-create-aad-work-id.md)]


