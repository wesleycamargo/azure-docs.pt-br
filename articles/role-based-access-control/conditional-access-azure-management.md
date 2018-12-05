---
title: Gerenciar o acesso ao gerenciamento do Azure com o acesso condicional no Azure Active Directory
description: Saiba mais sobre como usar o acesso condicional no Azure AD para gerenciar o acesso ao gerenciamento do Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2018
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 4cfd3c38631778373e99872fd305d8316cdadb75
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2018
ms.locfileid: "52274731"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Gerenciar o acesso ao gerenciamento do Azure com acesso condicional

O acesso condicional no Azure AD (Azure Active Directory) controla o acesso a aplicativos de nuvem com base em condições específicas que você especifica. Para permitir o acesso, você cria políticas de acesso condicional que permitem ou bloqueiam o acesso, dependendo de cumprirem-se ou não os requisitos na política. 

Normalmente, você usa o acesso condicional para controlar o acesso aos seus aplicativos de nuvem. Você também pode configurar políticas para controlar o acesso ao gerenciamento do Azure com base em determinadas condições (como o risco de entrada, localização ou dispositivo) e impor requisitos, assim como autenticação multifator.

Para criar uma política de gerenciamento do Azure, você seleciona **Microsoft Azure Management** em **aplicativos de nuvem** ao escolher o aplicativo no qual aplicar a política.

![Acesso condicional para o gerenciamento do Azure](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

A política que você cria se aplica a todos os pontos de gerenciamento do Azure, incluindo o Portal Clássico do Azure, o Portal do Azure, o provedor do Azure Resource Manager, APIs de Gerenciamento de Serviço clássicas e o Azure PowerShell. Observe que a política se aplica ao Azure PowerShell, que chama a API do Azure Resource Manager. Não se aplica ao [PowerShell do Azure Active Directory](/powershell/azure/active-directory/install-adv2), que chama o Microsoft Graph.

> [!CAUTION]
> Verifique se você entende como o acesso condicional funciona antes de configurar uma política para gerenciar o acesso ao gerenciamento do Azure. Verifique se você não criou nenhuma condição que possa bloquear o seu próprio acesso ao portal.

Para obter mais informações sobre como configurar e usar o acesso condicional, confira [Acesso condicional no Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md).