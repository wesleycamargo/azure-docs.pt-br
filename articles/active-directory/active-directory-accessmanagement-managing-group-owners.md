---
title: "Próximas etapas para o gerenciamento de acesso usando grupos | Microsoft Docs"
description: "Método avançado para gerenciamento dos grupos de segurança e como usar esses grupos para gerenciar o acesso a um recurso."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 44350a3c-8ea1-4da1-aaac-7fc53933dd21
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: 8c4920107b5d05716021bcef80d3c52c25f6eade


---
# <a name="managing-owners-for-a-group"></a>Gerenciando proprietários de um grupo
Após um proprietário de recurso ter o acesso a um grupo do Azure AD atribuído, a associação do grupo é gerenciada pelo proprietário do grupo. O proprietário do recurso delega efetivamente ao proprietário do grupo a permissão para atribuir usuários ao seu recurso.

## <a name="assigning-group-ownership"></a>Atribuindo a propriedade do grupo
**Para adicionar um proprietário a um grupo**

1. No [portal clássico do Azure](https://manage.windowsazure.com), selecione **Active Directory**e abra o diretório da sua organização.
2. Selecione a guia **Grupos** e, em seguida, abra o grupo ao qual você deseja adicionar os proprietários.
3. Selecione **Adicionar Proprietários**.
4. Na página **Adicionar proprietários**, selecione o usuário que deseja adicionar como o proprietário deste grupo e verifique se esse nome é adicionado ao painel **Selecionado**.

**Para remover um proprietário de um grupo**

1. No [portal clássico do Azure](https://manage.windowsazure.com), selecione **Active Directory**e abra o diretório da sua organização.
2. Selecione a guia **Grupos** e, em seguida, abra o grupo do qual deseja remover um proprietário.
3. Selecione a guia **Proprietários** .
4. Selecione o proprietário que você deseja remover deste grupo e, em seguida, selecione **Remover**.

## <a name="additional-information"></a>Informações adicionais
Esses artigos fornecem mais informações sobre o Active Directory do Azure.

* [Gerenciamento de acesso a recursos com grupos do Active Directory do Azure](active-directory-manage-groups.md)
* [Cmdlets do Azure Active Directory para definir configurações de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)
* [O que é o Active Directory do Azure?](active-directory-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)




<!--HONumber=Dec16_HO5-->


