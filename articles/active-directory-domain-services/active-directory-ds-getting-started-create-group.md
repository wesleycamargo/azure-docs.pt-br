---
title: "Azure Active Directory Domain Services: Criar o grupo de administradores de controladores de domínio do Azure AD | Microsoft Docs"
description: "Habilitar o Azure Active Directory Domain Services usando o portal clássico do Azure"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: maheshu
ms.translationtype: HT
ms.sourcegitcommit: 19be73fd0aec3a8f03a7cd83c12cfcc060f6e5e7
ms.openlocfilehash: 5ed0125e05928cf0f6d9941e099b433ecb46e6e2
ms.contentlocale: pt-br
ms.lasthandoff: 07/13/2017

---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-classic-portal"></a>Habilitar o Azure Active Directory Domain Services usando o portal clássico do Azure
Este artigo descreve e explica em detalhes as tarefas de configuração necessárias para habilitar o Azure AD DS (Azure Active Directory Domain Services) para seu locatário do Azure AD (Azure Active Directory).

> [!NOTE]
> [**Teste a experiência do novo portal do Azure (Versão prévia)**](active-directory-ds-getting-started.md). 
>

## <a name="task-1-create-the-azure-ad-dc-administrators-group"></a>Tarefa 1: Criar o grupo de administradores de controladores de domínio do Azure AD
A primeira tarefa é criar um grupo administrativo em seu locatário do Azure AD. Este grupo administrativo especial é chamado *AAD DC Administrators*. Os membros desse grupo recebem permissões administrativas nos computadores ingressados no domínio ao domínio gerenciado pelo Azure Active Directory Domain Services. Em computadores ingressados no domínio, esse grupo é adicionado ao grupo de administradores. Além disso, os membros desse grupo também poderão usar a Área de Trabalho Remota para se conectar remotamente a computadores ingressados no domínio.  

> [!NOTE]
> Você não tem permissões de Administrador de Domínio nem de Administrador Enterprise no domínio gerenciado criado com o Azure Active Directory Domain Services. Em domínios gerenciados, essas permissões são reservadas pelo serviço e não são disponibilizadas aos usuários dentro do locatário. No entanto, você poderá usar o grupo administrativo especial criado nesta tarefa de configuração para executar algumas operações privilegiadas. Essas operações incluem ingressar os computadores no domínio, pertencer ao grupo de administração em computadores ingressados no domínio e configurar a Política de Grupo.
>

Nessa tarefa de configuração, você cria o grupo administrativo e adicionará um ou mais usuários em seu diretório ao grupo. Para criar o grupo administrativo para o Azure Active Directory Domain Services, faça o seguinte:

1. Acesse o [portal clássico do Azure](https://manage.windowsazure.com).
2. No painel esquerdo, selecione o botão **Active Directory**.
3. Selecione o locatário do Azure AD (diretório) para o qual você deseja habilitar o Azure Active Directory Domain Services. Você pode criar apenas um domínio para cada diretório do Azure AD.

    ![Selecionar um diretório do Azure AD](./media/active-directory-domain-services-getting-started/select-aad-directory.png)
4. Na página **Diretório de visualização**, clique na guia **Grupos**.
5. Para adicionar um grupo ao seu locatário do Azure AD, no painel de tarefas na parte inferior da página, clique em **Adicionar Grupo**.

    ![O botão Adicionar Grupo](./media/active-directory-domain-services-getting-started/add-group-button.png)
6. Na caixa de diálogo **Adicionar Grupo**, crie um grupo chamado **Administradores de controladores de domínio do AAD** e, em seguida, defina **Tipo do Grupo** como **Segurança**.

   > [!WARNING]
   > Para habilitar o acesso no domínio gerenciado pelo Azure Active Directory Domain Services, crie um grupo com esse mesmo nome.
   >
   >

    ![A caixa de diálogo Adicionar Grupo](./media/active-directory-domain-services-getting-started/create-admin-group.png)
7. Na caixa **Descrição**, insira uma descrição que permite que outras pessoas entendam que esse grupo concede permissões administrativas no Azure Active Directory Domain Services.
8. Depois de criar o grupo, clique no nome do grupo para exibir suas propriedades.
9. Para adicionar usuários como membros do grupo, na parte inferior da janela, clique no botão **Adicionar Membros**.

    ![Botão Adicionar membros do grupo](./media/active-directory-domain-services-getting-started/add-group-members-button.png)
10. Na caixa de diálogo **Adicionar membros**, selecione os usuários que devem ser membros desse grupo e, em seguida, clique no ícone de marca de seleção no canto inferior direito.

    ![Adicionar usuários ao grupo de administradores](./media/active-directory-domain-services-getting-started/add-group-members.png)


## <a name="next-step"></a>Próxima etapa
[Tarefa 2: Criar ou selecionar uma rede virtual do Azure](active-directory-ds-getting-started-vnet.md)

