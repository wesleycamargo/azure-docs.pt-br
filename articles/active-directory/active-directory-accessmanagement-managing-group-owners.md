---
title: "Gerenciamento de proprietários grupos no Azure Active Directory | Microsoft Docs"
description: "Gerenciamento de proprietários de grupos e como usar esses grupos para gerenciar o acesso a um recurso."
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
ms.date: 09/13/2017
ms.author: curtand
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 70be82fdd673c4f245e306b1e1cfdcd94d4dac53
ms.contentlocale: pt-br
ms.lasthandoff: 09/14/2017

---
# <a name="managing-owners-for-a-group"></a>Gerenciando proprietários de um grupo
Após um proprietário de recurso atribuir o acesso a um grupo do Azure AD, a associação do grupo é gerenciada pelo proprietário do grupo. O proprietário do recurso delega efetivamente ao proprietário do grupo a permissão para atribuir usuários ao seu recurso.

## <a name="add-an-owner-to-a-group"></a>Adicionar um proprietário a um grupo

1. No [Centro de administração do Azure AD](https://aad.portal.azure.com), selecione **Usuários e grupos**.
2. Selecione **Todos os grupos** e, em seguida, abra o grupo ao qual você deseja adicionar os proprietários.
3. Selecione **Proprietários**e, em seguida, selecione **Adicionar proprietários**.
4. Na página **Adicionar proprietários**, selecione o usuário que deseja adicionar como o proprietário deste grupo e, em seguida, clique ou toque em **Selecionar**. 

## <a name="remove-an-owner-from-a-group"></a>Remover um proprietário de um grupo

1. No [Centro de administração do Azure AD](https://aad.portal.azure.com), selecione **Usuários e grupos**.
2. Selecione **Todos os grupos** e, em seguida, abra o grupo do qual você deseja remover os proprietários.
3. Selecione **Proprietários**, selecione o proprietário que você deseja remover deste grupo e, em seguida, clique ou toque em **Selecionar**.
4. No painel aberto para o proprietário selecionado, selecione **Remover**.

## <a name="additional-information"></a>Informações adicionais
Esses artigos fornecem mais informações sobre grupos do Azure Active Directory.

* [Consultar grupos existentes](active-directory-groups-view-azure-portal.md)
* [Criar um novo grupo e adicionando membros](active-directory-groups-create-azure-portal.md)
* [Gerenciar configurações de um grupo](active-directory-groups-settings-azure-portal.md)
* [Gerenciar associações de um grupo](active-directory-groups-membership-azure-portal.md)
* [Gerenciar regras dinâmicas para usuários em um grupo](active-directory-groups-dynamic-membership-azure-portal.md)

