---
title: "Adicionar usuários de outros diretórios ou de empresas parceiras ao Azure Active Directory | Microsoft Docs"
description: "Explica como adicionar usuários ou alterar as informações de usuário no Azure Active Directory, incluindo usuários externos e convidados."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 564a04ec-53c1-470b-9ab9-f3db57da0a89
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/10/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 7b506668b3025774edec25b6a37b83a47d702cb0
ms.openlocfilehash: b2eaddc631087d8b7b94e40f508bdd5da5229736


---
# <a name="add-users-from-other-directories-or-partner-companies-in-azure-active-directory"></a>Adicionar usuários de outros diretórios ou de empresas parceiras no Azure Active Directory
> [!div class="op_single_selector"]
> * [Portal do Azure](active-directory-users-create-external-azure-portal.md)
> * [Portal clássico do Azure](active-directory-create-users-external.md)
>
>

Este artigo explica como adicionar usuários de outros diretórios ao Azure Active Directory ou como adicionar usuários de empresas parceiras. Para saber mais sobre como adicionar novos usuários da sua organização e como adicionar os usuários com contas da Microsoft, confira [Adicionar novos usuários ao Azure Active Directory](active-directory-create-users.md). Os usuários adicionados não têm permissões de administrador, mas você pode atribuir funções a eles a qualquer momento.

## <a name="add-a-user"></a>Adicionar um usuário
1. Entre no [portal clássico do Azure](https://manage.windowsazure.com) com uma conta que seja um administrador global para o diretório.
2. Selecione **Active Directory**e abra seu diretório.
3. Selecione a guia **Usuários** e, na barra de comandos, selecione **Adicionar Usuário**.
4. Na página **Conte-nos sobre este usuário**, em **Tipo de usuário**, selecione:

   * **Usuário em outro diretório do AD do Azure** – adiciona uma conta de usuário a seu diretório originado de outro diretório do AD do Azure. Você só poderá selecionar um usuário em outro diretório se também for membro desse diretório.
   * **Usuários em empresas parceiras** - para convidar e autorizar os usuários da empresa parceira para o diretório (confira [Colaboração B2B do Azure Active Directory](active-directory-b2b-what-is-azure-ad-b2b.md)). Você precisará [carregar um arquivo CSV especificando endereços de email](active-directory-b2b-references-csv-file-format.md).
5. Na página **Perfil** do usuário, forneça um nome e um sobrenome, um nome amigável e uma função de usuário da lista **Funções**. Para obter mais informações sobre funções de usuário e administrador, consulte [Atribuindo funções de administrador no Azure AD](active-directory-assign-admin-roles.md). Especifique se deseja **Habilitar Multi-Factor Authentication** para o usuário.
6. Na página **Obter senha temporária**, selecione **Criar**.

> [!IMPORTANT]
> Se sua organização usa mais de um domínio, você deve saber sobre os seguintes problemas ao adicionar uma conta de usuário:
>
> * PARA adicionar contas de usuário com o mesmo nome UPN entre domínios, **primeiro** adicione, por exemplo, geoffgrisso@contoso.onmicrosoft.com, **seguido de** geoffgrisso@contoso.com.
> * **Não** adicione geoffgrisso@contoso.com antes de adicionar geoffgrisso@contoso.onmicrosoft.com. Essa ordem é extremamente importante e pode ser inconveniente para desfazer.
>
>

Se você alterar as informações de um usuário cuja identidade esteja sincronizada com o serviço do Active Directory local, não será possível alterar as informações do usuário no portal clássico do Azure. Para alterar as informações do usuário, use suas ferramentas de gerenciamento do Active Directory local.

## <a name="add-external-users"></a>Adicionar usuários externos
Você também pode adicionar usuários de outro diretório do AD do Azure ao qual você pertence, ou de empresas parceiras, ao carregar um arquivo CSV. Para adicionar um usuário externo, em **Tipo de Usuário**, especifique **Usuário em outro diretório do Microsoft Azure AD** ou **Usuários em empresas parceiras**.

Os usuários de ambos os tipos são originados de outro diretório e adicionados como **usuários externos**. Os usuários externos podem colaborar com outros usuários em um diretório sem qualquer requisito de adicionar novas contas e credenciais. Os usuários externos autenticados em seu diretório base quando entrarem e a autenticação funcionará para quaisquer outros diretórios aos quais eles forem adicionados.

## <a name="external-user-management-and-limitations"></a>Limitações e gerenciamento de usuários externos
Quando você adiciona um usuário de outro diretório ao seu diretório, esse usuário será um usuário externo no diretório. O nome de exibição e o nome de usuário são copiados do diretório base do usuário e usados para o usuário externo no seu diretório. Daí em seguida diante, as propriedades da conta de usuário externo serão completamente independentes. Se forem feitas alterações de propriedade para o usuário no seu diretório base, essas alterações não serão propagadas para a conta de usuário externo no diretório.

O único vínculo entre as duas contas é que o usuário sempre se autentica no diretório base ou com a conta da Microsoft dele. É por isso que você não vê uma opção para redefinir a senha ou habilitar a autenticação multifator para um usuário externo. Atualmente, a política de autenticação do diretório base ou da conta da Microsoft é a única que é avaliada quando o usuário entra.

> [!NOTE]
> Você ainda pode desabilitar o usuário externo no diretório, o que bloqueará o acesso ao seu diretório.
>
>

Se um usuário for excluído em seu diretório base ou cancelar a sua conta da Microsoft, o usuário externo ainda existirá no seu diretório. No entanto, o usuário em seu diretório não pode acessar recursos porque ele não pode ser autenticado com um diretório base ou uma conta da Microsoft.

### <a name="services-that-currently-support-access-by-azure-ad-external-users"></a>Serviços que oferecem suporte ao acesso por usuários externos do AD do Azure:
* **Portal clássico do Azure**: permite que um usuário que é administrador de vários diretórios gerencie cada um desses diretórios.
* **SharePoint Online**: se o compartilhamento externo estiver habilitado, permite que um usuário externo acesse recursos autorizados do SharePoint Online.
* **Dynamics CRM**: se o usuário for licenciado por meio do PowerShell, permite que um usuário externo acesse recursos autorizados no Dynamics CRM.
* **Dynamics AX**: se o usuário for licenciado por meio do PowerShell, permite que um usuário externo acesse recursos autorizados no Dynamics AX. As limitações de [usuários externos do Azure AD](#known-limitations-of-azure-ad-external-users) e também se aplicam a usuários externos no Dynamics AX.

### <a name="known-limitations-of-azure-ad-external-users"></a>Limitações conhecidas dos usuários externos do AD do Azure:
* Os usuários externos que são administradores não podem adicionar usuários de empresas parceiras a diretórios (colaboração B2B) fora de seu diretório base
* Os usuários externos não podem consentir aplicativos multilocatários em diretórios fora de seu diretório base
* No momento, o PowerBI não dá suporte ao acesso por usuários externos
* O Portal do Office não dá suporte ao licenciamento de usuários externos
* Em relação ao PowerShell do Azure AD, os usuários externos são conectados em seu diretório base e não podem gerenciar diretórios nos quais são usuários externos
* Ao configurar o plug-in do Dynamics CRM Outlook, talvez os usuários externos precisem inserir manualmente a URL do locatário do Dynamics CRM Online, em vez de selecionar 'CRM Online'

## <a name="whats-next"></a>O que vem a seguir
* [Adicionar novos usuários ao Azure Active Directory](active-directory-create-users.md)
* [Administrando o Azure AD](active-directory-administer.md)
* [Gerenciar senhas no Azure AD](active-directory-manage-passwords.md)
* [Gerenciar grupos no Azure AD](active-directory-manage-groups.md)



<!--HONumber=Jan17_HO5-->


