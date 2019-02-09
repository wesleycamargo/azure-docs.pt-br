---
title: Notas de versão do Kit de desenvolvimento do Microsoft Azure Stack | Microsoft Docs
description: Problemas conhecidos do Kit de desenvolvimento do Azure Stack, correções e aprimoramentos.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 02/09/2019
ms.openlocfilehash: 35a3850cdda7f6844258439b74b29dcc20852a89
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55959588"
---
# <a name="asdk-release-notes"></a>Notas de versão ASDK

Este artigo fornece informações sobre problemas conhecidos no Azure Stack desenvolvimento ASDK (Kit), correções e alterações. Se você não tiver certeza de qual versão você está executando, você poderá [usar o portal para verificar](../azure-stack-updates.md#determine-the-current-version).

Mantenha-se atualizado com o que há de novo no ASDK assinando o [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [feed](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11901095"></a>Compilação 1.1901.0.95

### <a name="changes"></a>Alterações

Esta compilação inclui os seguintes aprimoramentos para o Azure Stack:

- Agora, os componentes NAT e BGP são implantados no host físico. Isso elimina a necessidade de ter dois endereços IP públicos ou corporativos para implantar o ASDK e também simplifica a implantação.
- Sistemas que backups agora podem integrados do Azure Stack [ser validado](asdk-validate-backup.md) usando o **asdk installer.ps1** script do PowerShell.

### <a name="new-features"></a>Novos recursos

- Para obter uma lista dos novos recursos nesta versão, consulte [esta seção](../azure-stack-update-1901.md#new-features) notas de versão do Azure Stack.

### <a name="fixed-and-known-issues"></a>Fixos e problemas conhecidos

- Para obter uma lista dos problemas corrigidos nesta versão, consulte [esta seção](../azure-stack-update-1901.md#fixed-issues) notas de versão do Azure Stack. Para obter uma lista dos problemas conhecidos, consulte [esta seção](../azure-stack-update-1901.md#known-issues-post-installation).
- Observe que [hotfixes disponíveis do Azure Stack](../azure-stack-update-1901.md#azure-stack-hotfixes) não são aplicáveis para o Azure Stack ASDK.

## <a name="build-118110101"></a>Compilação 1.1811.0.101

### <a name="changes"></a>Alterações

Esta compilação inclui os seguintes aprimoramentos para o Azure Stack:  

- Há um conjunto de novo hardware mínimo e recomendado e requisitos de software para o ASDK. Estas novas recomendadas especificações estão documentadas em [considerações de planejamento de implantação do Azure Stack](asdk-deploy-considerations.md). Como a plataforma do Azure Stack evoluiu, mais serviços agora estão disponíveis e podem ser necessários mais recursos. As especificações de aumento refletem essas recomendações revisadas.

### <a name="new-features"></a>Novos recursos

Para obter uma lista dos novos recursos nesta versão, consulte [esta seção](../azure-stack-update-1811.md#new-features) notas de versão do Azure Stack.

### <a name="fixed-and-known-issues"></a>Fixos e problemas conhecidos

Para obter uma lista dos problemas corrigidos nesta versão, consulte [esta seção](../azure-stack-update-1811.md#fixed-issues) notas de versão do Azure Stack. Para obter uma lista dos problemas conhecidos, consulte [esta seção](../azure-stack-update-1811.md#known-issues-post-installation).

## <a name="build-11809090"></a>Compilação 1.1809.0.90

### <a name="new-features"></a>Novos recursos

Para obter uma lista dos novos recursos nesta versão, consulte [esta seção](../azure-stack-update-1809.md#new-features) notas de versão do Azure Stack.

### <a name="fixed-issues"></a>Problemas corrigidos

Para obter uma lista dos problemas corrigidos nesta versão, consulte [esta seção](../azure-stack-update-1809.md#fixed-issues).

### <a name="known-issues"></a>Problemas conhecidos

Para obter uma lista dos problemas conhecidos nesta versão, consulte [esta seção](../azure-stack-update-1809.md#known-issues-post-installation).
