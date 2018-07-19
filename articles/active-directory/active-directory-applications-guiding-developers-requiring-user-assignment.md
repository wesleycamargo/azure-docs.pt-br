---
title: Exigir a atribuição de usuário – Azure AD| Microsoft Docs
description: Como exigir a atribuição de usuários para aplicativos do Azure.
services: active-directory
documentationcenter: ''
author: kgremban
manager: mtillman
editor: ''
ms.assetid: 30b78cba-1e0f-472f-8314-f2250a9b91c3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: kgremban
robots: noindex
ms.openlocfilehash: 4519681d9b91383d27c00a992f85b0cb5d74f235
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2018
ms.locfileid: "37859455"
---
# <a name="azure-ad-and-applications-require-user-assignment"></a>Azure AD e aplicativos: exigir atribuição de usuário
## <a name="requiring-user-assignment"></a>Exigindo atribuição do usuário
1. Faça logon no Portal do Azure com uma conta de administrador.
2. Clique no item **Todos os serviços** no menu principal.
3. Escolha o diretório que você está usando para o aplicativo.
4. Clique na guia **Aplicativos empresariais**.
5. Selecione o aplicativo na lista de aplicativos associada ao diretório.
6. Clique no guia **Propriedades**.
7. Altere a opção **Atribuição de usuário necessária?** para Sim.
8. Clique no botão **Salvar** na parte superior da tela.

Agora, você precisará atribuir usuários e/ou grupos ao aplicativo. Consulte [Atribuir usuários a um aplicativo](active-directory-applications-guiding-developers-assigning-users.md) e [Atribuir grupos a um aplicativo](active-directory-applications-guiding-developers-assigning-groups.md).

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [active-directory-applications-guiding-developers-for-lob-applications-toc.md](../../includes/active-directory-applications-guiding-developers-for-lob-applications-toc.md)]
