---
title: 'Azure AD e Aplicativos: Atribuindo Grupos a um Aplicativo | Microsoft Docs'
description: "Como implementar a atribuição de grupos para aplicativos do Azure."
services: active-directory
documentationcenter: 
author: IHenkel
manager: femila
editor: 
ms.assetid: 29b5ba89-a1c7-4f1f-a294-248a40106617
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2015
ms.author: inhenk
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 698eb6e48f8e7d270ae1b07bfc7e68fd22b02776


---
# <a name="azure-ad-and-applications-assigning-groups-to-an-application"></a>Aplicativos do Azure AD: Atribuindo grupos a um aplicativo
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




<!--HONumber=Nov16_HO3-->


