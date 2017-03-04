---
title: "Adicionar novos usuários ao Azure Active Directory | Microsoft Docs"
description: "Explica como adicionar novos usuários ou como alterar informações do usuário no Azure Active Directory."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: e3673727-6bec-4fdc-87a4-d65b213c4c3c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/10/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 8f171456f74925eba16ebd6f56ced558a11f9d99
ms.openlocfilehash: 6c366a2b7e5017bbe67cf1dbefc0baec6cf1aed3
ms.lasthandoff: 02/11/2017


---
# <a name="add-new-users--or-users-with-microsoft-accounts-to-azure-active-directory"></a>Adicionar novos usuários ou usuários com contas da Microsoft ao Azure Active Directory
Adicione usuários para preencher seu diretório. Este artigo explica como adicionar novos usuários na sua organização e como adicionar usuários que tenham contas da Microsoft. Para saber mais sobre como adicionar usuários de outros diretórios ao Azure Active Directory ou como adicionar usuários de empresas parceiras, veja [Adicionar usuários de outros diretórios ou de empresas parceiras ao Azure Active Directory](active-directory-create-users-external.md). Os usuários adicionados não têm permissões de administrador, mas você pode atribuir funções a eles a qualquer momento.

## <a name="add-a-user"></a>Adicionar um usuário
1. Entre no [portal clássico do Azure](https://manage.windowsazure.com) com uma conta que seja um administrador global para o diretório.
2. Selecione **Active Directory**e selecione o nome do diretório da sua organização.
3. Selecione a guia **Usuários** e, na barra de comandos, selecione **Adicionar Usuário**.
4. Na página **Conte-nos sobre este usuário**, em **Tipo de usuário**, selecione:

   * **Novo usuário em sua organização** – adiciona uma nova conta de usuário a seu diretório.
   * **Usuário com uma conta da Microsoft existente** – adiciona uma conta de consumidor da Microsoft existente a seu diretório (por exemplo, uma conta do Outlook)
5. Dependendo da **Tipo de usuário**, insira um nome de usuário (para o novo usuário) ou um endereço de email (para um usuário com uma conta da Microsoft).
6. Na página **Perfil** do usuário, forneça um nome e um sobrenome, um nome amigável e uma função de usuário da lista **Funções**. Para obter mais informações sobre funções de usuário e administrador, consulte [Atribuindo funções de administrador no Azure AD](active-directory-assign-admin-roles.md). Especifique se deseja **Habilitar Multi-Factor Authentication** para o usuário.
7. Na página **Obter senha temporária**, selecione **Criar**.

> [!IMPORTANT]
> Se sua organização usa mais de um domínio, você deve saber sobre os seguintes problemas ao adicionar uma conta de usuário:
>
> * PARA adicionar contas de usuário com o mesmo nome UPN entre domínios, **primeiro** adicione, por exemplo, geoffgrisso@contoso.onmicrosoft.com, **seguido de** geoffgrisso@contoso.com.
> * **Não** adicione geoffgrisso@contoso.com antes de adicionar geoffgrisso@contoso.onmicrosoft.com. Essa ordem é extremamente importante e pode ser inconveniente para desfazer.
>
>

## <a name="change-user-information"></a>Alterar as informações do usuário
Você pode alterar qualquer atributo de usuário, exceto a ID de objeto.

1. Abra seu diretório.
2. Selecione a guia **Usuários** e selecione o nome de exibição do usuário que você deseja alterar.
3. Conclua suas alterações e, em seguida, clique em **Salvar**.

Se o usuário que você está alterando estiver sincronizado com seu serviço do Active Directory local, não poderá alterar as informações do usuário usando este procedimento. Para alterar o usuário, use suas ferramentas de gerenciamento do Active Directory local.

## <a name="guest-user-management-and-limitations"></a>Limitações e gerenciamento de usuário convidado
Contas de convidados são usuários de outros diretórios que foram convidados a seu diretório para acessar documentos do SharePoint, aplicativos ou outros recursos do Azure. Uma conta de convidado no diretório que tem o atributo UserType subjacente definido como "Convidado". Usuários normais (especificamente, membros de seu diretório) têm o atributo UserType "Membro".

Os convidados têm um conjunto limitado de direitos no diretório. Esses direitos limitam a capacidade dos Convidados para descobrir informações sobre outros usuários no diretório. No entanto, os usuários convidados ainda podem interagir com os usuários e os grupos associados aos recursos em que estão trabalhando. Os usuários convidados podem:

* Ver outros usuários e grupos associados a uma assinatura do Azure à qual estão atribuídos
* Ver os membros de grupos aos quais eles pertencem
* Pesquisar outros usuários no diretório, desde que saibam o endereço de email completo do usuário
* ver apenas um conjunto limitado de atributos dos usuários pesquisados - limitados ao nome de exibição, o endereço de email, o nome UPN e a foto em miniatura
* Obter uma lista dos domínios verificados no diretório
* consentir o acesso a aplicativos, concedendo a eles o mesmo acesso que os Membros têm em seu diretório

## <a name="set-guest-user-access-policies"></a>Definir políticas de acesso de usuário convidado
A guia **Configurar** do diretório inclui opções para controlar o acesso para usuários convidados. Essas opções só podem ser alteradas no portal clássico do Azure por um administrador global. Atualmente, não existe um método de API ou do PowerShell.

Para abrir a guia **Configurar** no Portal Clássico do Azure, selecione **Active Directory** e selecione o nome do diretório.

![Configurar a guia no Azure Active Directory][1]

Então você pode editar as opções para controlar o acesso para os usuários convidados.

![opções de controle de acesso para usuários convidados][2]

## <a name="whats-next"></a>O que vem a seguir
* [Adicionar usuários de outros diretórios ou de empresas parceiras no Azure Active Directory](active-directory-create-users-external.md)
* [Administrando o Azure AD](active-directory-administer.md)
* [Gerenciar senhas no Azure AD](active-directory-manage-passwords.md)
* [Gerenciar grupos no Azure AD](active-directory-manage-groups.md)

<!--Image references-->
[1]: ./media/active-directory-create-users/RBACDirConfigTab.png
[2]: ./media/active-directory-create-users/RBACGuestAccessControls.png

