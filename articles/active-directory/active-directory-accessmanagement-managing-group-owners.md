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
ms.date: 07/25/2017
ms.author: curtand
ms.custom: oldportal;it-pro;
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: 82fbeb379e90add09f7c569111053f6e9b1bc9c5
ms.contentlocale: pt-br
ms.lasthandoff: 07/26/2017

---
# <a name="managing-owners-for-a-group"></a>Gerenciando proprietários de um grupo
Após um proprietário de recurso ter o acesso a um grupo do Azure AD atribuído, a associação do grupo é gerenciada pelo proprietário do grupo. O proprietário do recurso delega efetivamente ao proprietário do grupo a permissão para atribuir usuários ao seu recurso.

> [!IMPORTANT]
> A Microsoft recomenda que você gerencie o Azure AD usando o [Centro de administração do AD do Azure](https://aad.portal.azure.com) no portal do Azure em vez de usar o portal clássico do Azure mencionado neste artigo. 

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

