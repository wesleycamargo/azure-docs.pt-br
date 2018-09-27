---
title: Atualizar o Windows Defender antivírus no Azure Stack
description: Obter detalhes sobre como antivírus é mantido atualizado no Azure Stack
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 09/26/2018
ms.author: patricka
ms.reviewer: fiseraci
ms.openlocfilehash: 59c59705e840d3cdd0d3c5310d84d711cd00c96a
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393987"
---
# <a name="update-windows-defender-antivirus-on-azure-stack"></a>Atualizar o Windows Defender antivírus no Azure Stack

[Windows Defender antivírus](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10) é uma solução antimalware que fornece segurança e proteção contra vírus. Cada componente de infraestrutura do Azure Stack (hosts do Hyper-V e máquinas virtuais) é protegido com o Windows Defender antivírus. Para proteção atualizada, plataforma, mecanismo e as definições do Windows Defender antivírus exigem atualizações periódicas. Como as atualizações são aplicadas depende da configuração.

## <a name="connected-scenario"></a>Cenário conectado

Para obter atualizações de mecanismos e definições antimalware, o Azure Stack [provedor de recursos de atualização](azure-stack-updates.md#the-update-resource-provider) baixa as definições antimalware e atualizações de mecanismo várias vezes por dia. Cada componente de infraestrutura do Azure Stack obtém a atualização do provedor de recursos de atualização e aplica a atualização automaticamente.

Para obter atualizações de plataforma antimalware, aplicar a [atualização mensal do Azure Stack](azure-stack-apply-updates.md). A atualização mensal do Azure Stack inclui atualizações de plataforma do Windows Defender antivírus para o mês.

## <a name="disconnected-scenario"></a>Cenário desconectado

 Aplicar a [atualização mensal do Azure Stack](azure-stack-apply-updates.md). A atualização mensal do Azure Stack inclui definições do Windows Defender antivírus, mecanismo e as atualizações de plataforma para o mês.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre a segurança do Azure Stack](azure-stack-security-foundations.md)
