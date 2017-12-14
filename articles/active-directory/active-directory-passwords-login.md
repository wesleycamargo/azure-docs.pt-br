---
title: SSPR do Azure AD na tela de login do Windows 10 | Microsoft Docs
description: "Configurar a redefinição de senha do Azure AD da tela de login do Windows 10 e esqueci meu PIN"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/16/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 46d5a673c530c6fbcbd1a88a8e74a753ae04998b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-password-reset-from-the-login-screen"></a>Redefinição de senha do Azure AD a partir da tela de logon

Você já implantou a redefinição de senha de autoatendimento do Azure AD (SSPR), mas os usuários ainda ligam para a assistência técnica quando esquecem a senha. Eles ligam para a assistência técnica porque não conseguem acessar um navegador da Web para poder acessar a SSPR.

Com o novo Windows 10 Fall Creators Update, os usuários com dispositivos do Azure AD unidos podem ver um link de "Redefinir senha" na sua tela de logon. Quando clicam nesse link, eles têm a mesma experiência de redefinição de senha de autoatendimento (SSPR) com a qual já estão familiarizados.

Para permitir que os usuários redefinam sua senha do Azure AD na tela de logon do Windows 10, os requisitos a seguir precisam ser atendidos:

* Windows 10, versão 1709 ou mais recente do cliente que está [ingressado no Azure AD](device-management-azure-portal.md).
* A redefinição de senha de autoatendimento do Azure AD deve ser habilitada.
* Configurar e implantar a configuração para habilitar o link Redefinir senha por meio de um dos seguintes métodos:
   * [Perfil de configuração de dispositivo do Intune](active-directory-passwords-login.md#configure-reset-password-link-using-intune)
   * [Chave do registro](active-directory-passwords-login.md#configure-reset-password-link-using-the-registry)

## <a name="configure-reset-password-link-using-intune"></a>Configurar o link Redefinir senha usando o Intune

### <a name="create-a-device-configuration-policy-in-intune"></a>Criar uma política de configuração do dispositivo no Intune

1. Faça logon no [portal do Azure](https://portal.azure.com) e clique em **Intune**.
2. Criar um novo perfil de configuração do dispositivo acessando **Configuração do dispositivo** > **Perfis** > **Criar perfil**
   * Forneça um nome significativo para o perfil
   * Opcionalmente, forneça uma descrição significativa do perfil
   * Plataforma **Windows 10 e superior**
   * Tipo de perfil **Personalizado**

   ![CreateProfile][CreateProfile]

3. Definir **Configurações**
   * **Adicionar** a seguinte configuração de OMA-URI para habilitar o link Redefinir senha
      * Forneça um nome significativo para explicar o que a configuração está fazendo
      * Opcionalmente, forneça uma descrição significativa da configuração
      * **OMA-URI** definido como `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      * **Tipo de dados** definido como **Inteiro**
      * **Valor** definido como **1**
      * Clique em **OK**
   * Clique em **OK**
4. Clique em **Criar**

### <a name="assign-a-device-configuration-policy-in-intune"></a>Atribuir uma política de configuração do dispositivo no Intune

#### <a name="create-a-group-to-apply-device-configuration-policy-to"></a>Criar um grupo no qual será aplicada a política de configuração de dispositivo

1. Faça logon no [portal do Azure](https://portal.azure.com) e clique em **Azure Active Directory**.
2. Navegue até **Usuários e grupos** > **Todos os grupos de** > **Novo grupo**
3. Forneça um nome para o grupo e, em **Tipo de associação** escolha **Atribuído** 
   * Em **Membros**, escolha os dispositivos Windows 10 ingressados pelo Azure AD nos quais você deseja aplicar a política.
   * Clique em **Selecionar**
4. Clique em **Criar**

Mais informações sobre como criar grupos podem ser encontradas no artigo [Gerenciar o acesso a recursos com grupos do Azure Active Directory](active-directory-manage-groups.md).

#### <a name="assign-device-configuration-policy-to-device-group"></a>Atribuir a política de configuração de dispositivo para o grupo de dispositivos

1. Faça logon no [portal do Azure](https://portal.azure.com) e clique em **Intune**.
2. Localize o perfil de configuração do dispositivo criado anteriormente acessando **Configuração do dispositivo** > **Perfis** > Clique no perfil criado anteriormente
3. Atribuir o perfil a um grupo de dispositivos 
   * Clique em **Atribuições** > em **Incluir** > **Selecionar grupos para incluir**
   * Selecione o grupo criado anteriormente e clique em **Selecionar**
   * Clique em **Salvar**

   ![Atribuição][Assignment]

Agora você criou e atribuiu uma política de configuração de dispositivo para habilitar o link Redefinir senha na tela de logon usando o Intune.

## <a name="configure-reset-password-link-using-the-registry"></a>Configurar o link Redefinir senha usando o registro

É recomendável usar esse método somente para testar a alteração da configuração.

1. Faça logon no dispositivo ingressado no Azure AD usando as credenciais administrativas
2. Execute o **regedit** como administrador
3. Defina a seguinte chave do registro
   * `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      * `"AllowPasswordReset"=dword:00000001`

## <a name="what-do-users-see"></a>O que os usuários veem

Agora que a política foi configurada e atribuída, o que muda para o usuário? Como eles sabem que podem redefinir sua senha na tela de logon?

![LoginScreen][LoginScreen]

Quando os usuários tentam fazer logon, eles agora veem um link Redefinir senha que abre a experiência de redefinição de senha de autoatendimento na tela de logon. Essa funcionalidade permite aos usuários redefinir a senha sem a necessidade de usar outro dispositivo para acessar um navegador da Web.

Os usuários podem encontrar orientações sobre esse recuso em [Redefinir sua senha corporativa ou de estudante](active-directory-passwords-update-your-own-password.md#reset-password-at-sign-in)

## <a name="common-issues"></a>Problemas comuns

Ao testar essa funcionalidade usando o Hyper-V, o link de "Redefinir senha" não aparece.

* Acesse a máquina virtual que você está usando para teste, clique em **Exibição** e, em seguida, desmarque a opção **Sessão aprimorada**.

Ao testar essa funcionalidade usando a Área de Trabalho Remota, o link de "Redefinir senha" não aparece.

* Atualmente não há suporte para a redefinição de senha a partir de uma Área de Trabalho Remota.

## <a name="next-steps"></a>Próximas etapas
Os links a seguir fornecem informações adicionais sobre a redefinição de senha usando o Azure AD

* [Como fazer para implantar a SSPR?](active-directory-passwords-best-practices.md)
* [Como habilitar a redefinição do PIN a partir da tela de logon?](https://docs.microsoft.com/intune/device-windows-pin-reset)
* [Mais informações sobre as políticas de autenticação do MDM](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-authentication)

[CreateProfile]: ./media/active-directory-passwords-login/create-profile.png "Criar perfil de configuração de dispositivo do Intune para habilitar o link Redefinir senha na tela de logon do Windows 10"
[Assignment]: ./media/active-directory-passwords-login/profile-assignment.png "Atribuir a política de configuração de dispositivo do Intune a um grupo de dispositivos Windows 10"
[LoginScreen]: ./media/active-directory-passwords-login/logon-reset-password.png "Link Redefinir senha na tela de logon do Windows 10"
