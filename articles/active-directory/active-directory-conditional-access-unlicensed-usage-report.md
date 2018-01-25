---
title: "Relatório de uso não licenciado | Microsoft Docs"
description: "O relatório de uso não licenciado ajuda a identificar usuários sem licença que estejam usando recursos pagos do Azure AD."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 92138f43-9528-4c8a-b834-66a47da476e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.openlocfilehash: 8bcd814ee7b7bfc158e62ad26e6cc23781f5352d
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2018
---
# <a name="unlicensed-usage-report"></a>Relatório de uso não licenciado
O relatório de uso não licenciado ajuda a identificar usuários sem licença que estejam usando recursos pagos do Azure AD. Isso permite fazer melhor uso das licenças que você comprou e identificar quando você poderá precisar de mais licenças. 

O relatório mostra o uso ativo dos recursos pagos nos últimos 30 dias. 

## <a name="report-structure"></a>Estrutura de relatório
| Nome da coluna | DESCRIÇÃO |
|:--- |:--- |
| Usuário Não Licenciado |Nome do usuário |
| Recurso |O nome do recurso. Por exemplo: acesso condicional |
| Aplicativo acessado |O nome do aplicativo que está sendo acessado com o recurso. Por exemplo: Office 365 SharePoint Online |

> [!NOTE]
> Se uma conta de usuário tiver sido excluída, a coluna 'Usuário Não Licenciado' será preenchida com uma ID, como 1003000090D8B285
> 
> 

## <a name="conditional-access-feature"></a>Recurso de acesso condicional
Os usuários não licenciados serão sinalizados quando acessarem um serviço com a política de acesso condicional aplicada se não tiverem uma licença do Azure AD Premium. 

Isso se aplica a políticas de MFA/Local, bem como a políticas de dispositivos que usam o Intune.

## <a name="see-also"></a>Consulte também
* [Usar o acesso condicional com o Office 365 e com outros aplicativos conectados ao Azure Active Directory](active-directory-conditional-access-azure-portal.md)
* [Introdução ao acesso condicional ao Azure AD](active-directory-conditional-access-azure-portal-get-started.md) 

