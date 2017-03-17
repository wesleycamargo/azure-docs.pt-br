---
title: Atribuir grupos aos aplicativos do Azure AD | Microsoft Docs
description: "Como implementar a atribuição de grupos para aplicativos do Azure."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: 29b5ba89-a1c7-4f1f-a294-248a40106617
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 015cc28903bfd366c653a51b0f73512bf8b578ea
ms.openlocfilehash: f58c051bc25544d2811738b8ade483c82f3901b2
ms.lasthandoff: 02/28/2017

---
# <a name="assign-azure-active-directory-groups-to-an-application"></a>Atribuir grupos do Azure Active Directory a um aplicativo
Antes de atribuir usuários e grupos a um aplicativo, você precisa exigir a atribuição de usuários. Para saber como exigir a atribuição de usuário, consulte o artigo [Exigindo Atribuição de Usuário](active-directory-applications-guiding-developers-requiring-user-assignment.md) .

Este artigo pressupõe que você já tenha criado grupos no Active Directory que está usando para este aplicativo.

## <a name="assigning-groups-to-an-application"></a>Atribuindo grupos a um aplicativo
1. Faça logon no Portal do Azure com uma conta de administrador.
2. Clique no item **Todos os Itens** no menu principal.
3. Escolha o diretório que você está usando para o aplicativo.
4. Clique na guia **APLICATIVOS** .
5. Selecione o aplicativo na lista de aplicativos associada ao diretório.
6. Clique na guia **USUÁRIOS E GRUPOS** .
7. Filtre a lista de grupos no Active Directory usando a lista suspensa **Grupos** .
8. Selecione o grupo.
9. Clique em **ATRIBUIR**.
10. Clique em **sim** quando solicitado.

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [active-directory-applications-guiding-developers-for-lob-applications-toc.md](../../includes/active-directory-applications-guiding-developers-for-lob-applications-toc.md)]

