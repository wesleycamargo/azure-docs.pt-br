---
title: Requisitos de licença para uso do PIM – Azure | Microsoft Docs
description: Descreve os requisitos de licenciamento para uso do Azure AD PIM (Privileged Identity Management).
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
ms.subservice: pim
ms.date: 01/16/2019
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: ad64fba0288aa5663e008484d6f0d2cbdff0bca6
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55187276"
---
# <a name="license-requirements-to-use-pim"></a>Requisitos de licença para uso do PIM

Para usar o Azure AD (Azure Active Directory) PIM (Privileged Identity Management), um diretório precisa ter uma licença válida. Além disso, as licenças precisam ser atribuídas aos administradores e aos usuários relevantes. Este artigo descreve os requisitos de licença para uso do PIM.

## <a name="prerequisites"></a>Pré-requisitos

Para usar o PIM, o diretório precisa ter uma das seguintes licenças pagas ou de avaliação:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Para obter mais informações, confira [O que é Azure Active Directory?](../fundamentals/active-directory-whatis.md).

## <a name="which-users-must-have-licenses"></a>Quais usuários precisam ter licenças?

Cada administrador ou usuário que interage com o PIM ou que recebe um benefício dele precisa ter uma licença. Os exemplos incluem:

- Administradores com funções do Azure AD gerenciadas usando o PIM
- Administradores com funções de recurso do Azure gerenciadas usando o PIM
- Administradores atribuídos à função Administrador com Função com Privilégios
- Usuários atribuídos como qualificados para funções de diretório gerenciadas usando o PIM
- Usuários que podem aprovar/rejeitar solicitações no PIM
- Usuários atribuídos a uma função de recurso do Azure com atribuições Just-In-Time ou diretas (por tempo limitado)  
- Usuários atribuídos a uma revisão de acesso
- Usuários que executam revisões de acesso

Para obter informações sobre como atribuir licenças a seus usos, confira [Atribuir ou remover licenças usando o portal do Azure Active Directory](../fundamentals/license-users-groups.md).

## <a name="what-happens-when-a-license-expires"></a>O que acontece quando uma licença expira?

Se uma licença do Azure AD Premium P2, do EMS E5 ou de avaliação expirar, os recursos do PIM não estarão mais disponíveis no diretório:

- Atribuições de função permanentes para funções do Azure AD não serão afetadas.
- O serviço PIM no portal do Azure, bem como os cmdlets da API do Graph e as interfaces do PowerShell do PIM, não estarão mais disponíveis para os usuários ativarem funções privilegiadas, gerenciarem o acesso privilegiado ou realizarem revisões de acesso de funções privilegiadas.
- Atribuições de funções qualificadas de funções do Azure AD serão removidas, pois os usuários não poderão mais ativar funções privilegiadas.
- As revisões de acesso em andamento de funções do Azure AD serão encerradas e as definições de configuração do PIM serão removidas.
- O PIM não enviará mais emails sobre alterações na atribuição de função.

## <a name="next-steps"></a>Próximas etapas

- [Começar a usar o PIM](pim-getting-started.md)
- [Funções que não podem ser gerenciadas no PIM](pim-roles.md)
