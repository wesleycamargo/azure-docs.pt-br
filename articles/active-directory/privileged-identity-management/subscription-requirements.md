---
title: Requisitos de assinatura para usar o PIM – Azure | Microsoft Docs
description: Descreve os requisitos de assinatura e licenciamento para usar o Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 06/01/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 1554895dcba0c09a3a2e19c284a1cd6f0416cfe1
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190603"
---
# <a name="subscription-requirements-to-use-pim"></a>Requisitos de assinatura para usar o PIM

O Azure AD Privileged Identity Management está disponível como parte da edição Premium P2 do Azure AD. Para saber mais sobre outros recursos do P2 e uma comparação com o Premium P1, consulte [Edições do Azure Active Directory](../active-directory-editions.md).

>[!NOTE]
Quando o Azure Active Directory (Azure AD) Privileged Identity Management estava no modo de visualização, não havia verificações de licença para um locatário experimentar o serviço.  Agora que o Azure AD Privileged Identity Management atingiu a disponibilidade geral, uma assinatura de avaliação ou paga deverá existir para que o locatário continue usando o Privileged Identity Management depois de dezembro de 2016.
  

## <a name="confirm-your-trial-or-paid-subscription"></a>Confirmar sua assinatura de avaliação ou paga

Se você não tiver certeza se a sua organização tem uma assinatura de avaliação ou paga, verifique se há uma assinatura em seu locatário usando os comandos incluídos no Módulo do Azure Active Directory para Windows PowerShell V1. 
1. Abra uma janela do PowerShell.
2. Insira `Connect-MsolService` para autenticar como um usuário em seu locatário.
3. Digite `Get-MsolSubscription | ft SkuPartNumber,IsTrial,Status`.

Esse comando recupera uma lista de assinaturas em seu locatário. Se nenhum linha retornar, será necessário obter uma avaliação do Azure AD Premium P2, comprar uma assinatura Premium P2 do Azure AD ou uma assinatura EMS E5 para usar o Azure AD Privileged Identity Management.  Para obter uma versão de avaliação e começar a usar o Azure AD Privileged Identity Management, leia [Introdução ao Azure AD Privileged Identity Management](pim-getting-started.md).

Se esse comando retornar uma linha na qual SkuPartNumber é "AAD_PREMIUM_P2" ou "EMSPREMIUM" e IsTrial é "True", isso indicará que uma versão de avaliação do Azure AD Premium P2 está presente no locatário.  Se o status da assinatura não estiver habilitado, e você não tiver uma compra de assinatura Premium P2 do Azure AD ou EMS E5, será necessário adquirir uma assinatura Premium P2 do Azure AD ou EMS E5 para continuar usando o Azure AD Privileged Identity Management.

O Azure AD Premium P2 está disponível por meio de um [Microsoft Enterprise Agreement](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx), o [Programa de Licenciamento Open Volume](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx) e o [programa Provedores de Soluções de Nuvem](https://partner.microsoft.com/cloud-solution-provider). Os assinantes do Azure e do Office 365 também podem adquirir o Azure AD Premium P2 online.  Saiba mais sobre preços do Azure AD Premium e como fazer pedidos online em [Preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="azure-ad-privileged-identity-management-is-not-available-in-tenant"></a>O Azure AD Privileged Identity Management não está disponível no locatário

O Azure AD Privileged Identity Management não estará mais disponível em seu locatário se:
- Sua organização usou o Azure AD Privileged Identity Management quando ele estava no modo de visualização e não comprou a assinatura Premium P2 do Azure AD ou EMS E5.
- Sua organização tinha uma avaliação do Premium P2 do Azure AD ou EMS E5 que expirou.
- Sua organização comprou uma assinatura que expirou.

Quando uma assinatura Premium P2 do Azure AD ou EMS E5 expira, ou uma organização que estava usando o Azure AD Privileged Identity Management no modo visualização não compra a assinatura Premium P2 do Azure AD ou EMS E5:

- Atribuições de função permanentes para funções do Azure AD não serão afetadas.
- A extensão Azure AD Privileged Identity Management no Portal do Azure, bem como os cmdlets de API do Graph e interfaces do PowerShell do Azure AD Privileged Identity Management, não estará mais disponível para os usuários ativarem funções privilegiadas, gerenciarem acesso privilegiado ou realizarem análises de acesso de funções privilegiadas.
- Atribuições de funções qualificadas de funções do Azure AD serão removidas, pois os usuários não poderão mais ativar funções privilegiadas.
- Quaisquer revisões de acesso contínuo de funções do Azure AD serão encerradas, e definições de configuração do Azure AD Privileged Identity Management serão removidas.
- O Azure AD Privileged Identity Management não enviará emails sobre alterações de atribuição de função.

## <a name="next-steps"></a>Próximas etapas

- [Começar a usar o PIM](pim-getting-started.md)
- [Funções de diretório do Azure AD que você pode gerenciar no PIM](pim-roles.md)
