---
title: "Explicador da visualização do Azure Active Directory | Microsoft Docs"
description: "Um tópico que explica as diferenças entre o Azure Active Directory no portal clássico e a visualização do Azure Active Directory no portal do Azure."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: e717c11c-e7c9-4565-8e47-1950905e5b3c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 3b31bd036d9c3ff8036b314b93cbddd94874ff63
ms.openlocfilehash: 8f7a679dc5b5726107503a9f7363ab162b1770d0


---
# <a name="preview-of-the-azure-active-directory-management-experience-in-the-azure-portal"></a>Visualização da experiência de gerenciamento do Azure Active Directory no portal do Azure
A experiência de gerenciamento do Azure Active Directory (Azure AD) está em visualização no portal do Azure. Você pode experimentar ao entrar no [portal do Azure](https://portal.azure.com) como um administrador global do seu diretório. Em seguida, selecione Azure Active Directory na lista de serviços, se estiver visível, ou selecione **Mais serviços** para exibir a lista de todos os serviços. Você não precisa de uma assinatura do Azure para usar a experiência de gerenciamento do Azure AD no portal do Azure.

## <a name="learn-about-what-you-can-do-in-the-preview-experience"></a>Saiba mais sobre o que você pode fazer na experiência de visualização
A experiência de visualização permite que você gerencie vários recursos de diretório, como usuários, grupos, aplicativos e configurações de diretório no portal do Azure. Estamos aprimorando essa experiência para incluir todos os recursos existentes na experiência de gerenciamento do Azure AD no [portal clássico do Azure](https://manage.windowsazure.com). Até lá, há algumas tarefas de gerenciamento do diretório que você ainda deve concluir no portal clássico.

## <a name="manage-the-same-azure-ad-tenants"></a>Gerenciar os mesmos locatários do Azure AD
A experiência de visualização lê e grava o mesmo locatário do Azure Active Directory como o portal clássico e o Centro de administração do Office 365. As alterações feitas em qualquer um desses portais são refletidas em todos os outros portais.

## <a name="use-the-same-authorization-logic"></a>Usar a mesma lógica de autorização
A experiência de visualização usa a mesma lógica de autorização que os clientes existentes do Active Directory. Os usuários estão autorizados a fazer alterações aos recursos de diretório com base em suas funções de diretório, como administrador global, administrador de usuário e administrador de senha. Ter uma função em recursos do Azure ou em uma assinatura do Azure não dá autorização aos usuários de gerenciar recursos de diretório. Para obter mais informações sobre as funções de gerenciamento do Azure AD, consulte [Atribuindo funções de administrador no Azure Active Directory](active-directory-assign-admin-roles.md).

A experiência de visualização é otimizada para os administradores globais. Se você usar a experiência de visualização enquanto estiver conectado como um usuário que não é um administrador global, poderá ter uma experiência inadequada. Por exemplo, você poderá selecionar um botão que permite que você inicie uma tarefa que não pode ser concluída no diretório. Aprimoraremos essa experiência em breve.

## <a name="next-steps"></a>Próximas etapas
Você pode fornecer comentários sobre a experiência de visualização na seção do portal admin do [Fórum de comentários do Azure AD](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&filter=alltypes&sort=lastpostdesc).



<!--HONumber=Feb17_HO3-->


