---
title: "Azure Active Directory Domain Services: habilitar a sincronização de senha | Microsoft Docs"
description: "Introdução aos Serviços de Domínio do Active Directory do Azure"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 5a32a0df-a3ca-4ebe-b980-91f58f8030fc
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: b7b5f92c0093faa96a367fc95d459b1babd99789
ms.lasthandoff: 04/28/2017


---
# <a name="enable-password-synchronization-with-azure-active-directory-domain-services"></a>Habilitar a sincronização de senhas com o Azure Active Directory Domain Services
Nas tarefas anteriores, você habilitou o AD DS (Azure Active Directory Domain Services) para seu locatário do Azure AD (Azure Active Directory). A próxima tarefa é habilitar os hashes de credenciais, que são necessários para autenticação NTLM (NT LAN Manager) e Kerberos a fim de sincronizar com o Azure Active Directory Domain Services. Depois que a sincronização de credenciais é configurada, os usuários podem entrar no domínio gerenciado com suas credenciais corporativas.

Os procedimentos variam dependendo de sua organização ser um locatário do Azure AD somente na nuvem ou estar configurada para sincronização com seu diretório local usando o Azure AD Connect.

> [!div class="op_single_selector"]
> * [Locatário do Azure AD somente na nuvem](active-directory-ds-getting-started-password-sync.md)
> * [Locatário do Azure AD sincronizado](active-directory-ds-getting-started-password-sync-synced-tenant.md)
>
>

## <a name="task-5-enable-password-synchronization-with-azure-active-directory-domain-services-for-a-cloud-only-azure-ad-tenant"></a>Tarefa 5: habilitar a sincronização de senhas com o Azure Active Directory Domain Services para um locatário do Azure AD somente na nuvem
Para autenticar os usuários no domínio gerenciado, o Azure Active Directory Domain Services precisa de hashes de credenciais em um formato adequado para a autenticação NTLM e Kerberos. A menos que você habilite o Azure Active Directory Domain Services para seu locatário, o Azure AD não gera nem armazena hashes de credenciais no formato necessário para a autenticação NTLM ou Kerberos. Por motivos de segurança óbvios, o Azure AD também não armazena credenciais no formato de texto não criptografado. Portanto, o Azure AD não tem uma maneira de gerar esses hashes de credenciais NTLM ou Kerberos com base em credenciais de usuários existentes.

> [!NOTE]
> Se a sua organização tem um locatário do Azure AD somente na nuvem, os usuários que precisam usar o Azure Active Directory Domain Services devem alterar suas senhas.
>
>

Esse processo de alteração de senhas faz com que os hashes de credencial requeridos pelo Azure Active Directory Domain Services para a autenticação Kerberos e NTLM sejam gerados no AD do Azure. Você também pode expirar senhas para todos os usuários no locatário que precisam usar o Azure Active Directory Domain Services ou instrui-los para alterar suas senhas.

### <a name="enable-ntlm-and-kerberos-credential-hash-generation-for-a-cloud-only-azure-ad-tenant"></a>Habilitar a geração de hashes de credencial Kerberos e NTLM para um locatário do Azure AD somente na nuvem
Aqui estão as instruções que você precisa fornecer aos usuários para que eles possam alterar suas senhas:

1. Vá para a página do [Painel de acesso do Azure AD](http://myapps.microsoft.com) da sua organização.
2. Na janela Painel de acesso, selecione a guia **perfil**.
3. Clique no bloco **Alterar senha**.

    ![O bloco "Alterar senha" do Painel de acesso do Azure AD](./media/active-directory-domain-services-getting-started/user-change-password.png)

   > [!NOTE]
   > Se a opção **Alterar senha** não for exibida na janela Painel de Acesso, verifique se sua organização configurou [gerenciamento de senhas no Azure AD](../active-directory/active-directory-passwords-getting-started.md).
   >
   >
4. Na página **Alterar senha** , digite sua senha (antiga) existente, digite uma nova senha e confirme-a.

    ![Criar uma rede virtual para os Serviços de Domínio do AD do Azure.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

5. Clique em **Enviar**.

A nova senha poderá ser utilizada no Azure Active Directory Domain Services alguns minutos após a sua alteração. Depois de mais alguns minutos (normalmente, cerca de 20 minutos), você pode entrar computadores que ingressaram no domínio gerenciado usando a senha recém-alterada.

## <a name="next-steps"></a>Próximas etapas
* [Como atualizar sua própria senha](../active-directory/active-directory-passwords-update-your-own-password.md)
* [Introdução ao Gerenciamento de Senhas no Azure AD](../active-directory/active-directory-passwords-getting-started.md)
* [Habilitar a sincronização de senhas para o Azure Active Directory Domain Services em um locatário do Azure AD sincronizado](active-directory-ds-getting-started-password-sync-synced-tenant.md)
* [Administrar um domínio gerenciado do Azure Active Directory Domain Services](active-directory-ds-admin-guide-administer-domain.md)
* [Ingressar em uma máquina virtual do Windows para um domínio gerenciado do Azure Active Directory Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Ingressar em uma máquina virtual do Red Hat Enterprise Linux para um domínio gerenciado do Azure Active Directory Domain Services](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

