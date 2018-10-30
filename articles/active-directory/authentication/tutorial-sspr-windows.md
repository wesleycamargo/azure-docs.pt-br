---
title: SSPR do Azure AD na tela de logon do Windows 10
description: Neste tutorial, você habilitará a redefinição de senha na tela de logon do Windows 10 para reduzir as chamadas de assistência técnica.
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 79a6636043499cffb7eded409cdc27c56de98e33
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430214"
---
# <a name="tutorial-azure-ad-password-reset-from-the-login-screen"></a>Tutorial: Redefinição de senha do Azure AD a partir da tela de logon

Neste tutorial, você permitirá que os usuários redefinam suas senhas na tela de logon do Windows 10. Com o novo Windows 10 Atualização de abril de 2018, os usuários com dispositivos **Ingressados no Azure AD** ou **Ingressados no Azure AD híbrido** podem usar um link "Redefinir senha" na tela de logon. Quando os usuários clicam nesse link, eles têm a mesma experiência de redefinição de senha de autoatendimento (SSPR) com a qual já estão familiarizados.

> [!div class="checklist"]
> * Configurar o link Redefinir senha usando o Intune
> * Opcionalmente, configure usando o Registro do Windows
> * Entenda o que os usuários irão ver

## <a name="prerequisites"></a>Pré-requisitos

* Atualização de 10 de abril de 2018 do Windows, ou o cliente mais recente é:
   * [Ingressado no Azure AD](../device-management-azure-portal.md) ou 
   * [Ingressado no Azure AD híbrido](../device-management-hybrid-azuread-joined-devices-setup.md)
* A redefinição de senha de autoatendimento do Azure AD deve ser habilitada.

## <a name="configure-reset-password-link-using-intune"></a>Configurar o link Redefinir senha usando o Intune

Implantar a alteração de configuração para habilitar a redefinição de senha da tela de logon usando o Intune é o método mais flexível. O Intune permite que você implante a alteração de configuração para um grupo específico de computadores que você definir. Esse método requer o registro do dispositivo no Intune.

### <a name="create-a-device-configuration-policy-in-intune"></a>Criar uma política de configuração do dispositivo no Intune

1. Entre no [portal do Azure](https://portal.azure.com) e clique em **Intune**.
2. Criar um novo perfil de configuração do dispositivo acessando **Configuração do dispositivo** > **Perfis** > **Criar perfil**
   * Forneça um nome significativo para o perfil
   * Opcionalmente, forneça uma descrição significativa do perfil
   * Plataforma **Windows 10 e superior**
   * Tipo de perfil **Personalizado**

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

1. Entre no [portal do Azure](https://portal.azure.com) e clique em **Azure Active Directory**.
2. Navegue até **Usuários e grupos** > **Todos os grupos de** > **Novo grupo**
3. Forneça um nome para o grupo e, em **Tipo de associação** escolha **Atribuído**
   * Em **Membros**, escolha os dispositivos Windows 10 ingressados pelo Azure AD nos quais você deseja aplicar a política.
   * Clique em **Selecionar**
4. Clique em **Criar**

Mais informações sobre como criar grupos podem ser encontradas no artigo [Gerenciar o acesso a recursos com grupos do Azure Active Directory](../fundamentals/active-directory-manage-groups.md).

#### <a name="assign-device-configuration-policy-to-device-group"></a>Atribuir a política de configuração de dispositivo para o grupo de dispositivos

1. Entre no [portal do Azure](https://portal.azure.com) e clique em **Intune**.
2. Localize o perfil de configuração do dispositivo criado anteriormente acessando **Configuração do dispositivo** > **Perfis** > Clique no perfil criado anteriormente
3. Atribuir o perfil a um grupo de dispositivos 
   * Clique em **Atribuições** > em **Incluir** > **Selecionar grupos para incluir**
   * Selecione o grupo criado anteriormente e clique em **Selecionar**
   * Clique em **Salvar**

   ![Atribuição][Assignment]

Agora você criou e atribuiu uma política de configuração de dispositivo para habilitar o link Redefinir senha na tela de logon usando o Intune.

## <a name="configure-reset-password-link-using-the-registry"></a>Configurar o link Redefinir senha usando o registro

1. Faça logon no PC Windows usando as credenciais administrativas
2. Execute o **regedit** como administrador
3. Defina a seguinte chave do registro
   * `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      * `"AllowPasswordReset"=dword:00000001`

## <a name="what-do-users-see"></a>O que os usuários veem

Agora que a política foi configurada e atribuída, o que muda para o usuário? Como eles sabem que podem redefinir sua senha na tela de logon?

![LoginScreen][LoginScreen]

Quando os usuários tentam fazer logon, eles agora veem um link Redefinir senha que abre a experiência de redefinição de senha de autoatendimento na tela de logon. Essa funcionalidade permite aos usuários redefinir a senha sem a necessidade de usar outro dispositivo para acessar um navegador da Web.

Os usuários podem encontrar orientações sobre esse recuso em [Redefinir sua senha corporativa ou de estudante](../user-help/active-directory-passwords-update-your-own-password.md#reset-password-at-sign-in)

## <a name="common-issues"></a>Problemas comuns

Ao testar essa funcionalidade usando o Hyper-V, o link de "Redefinir senha" não aparece.

* Acesse a máquina virtual que você está usando para teste, clique em **Exibição** e, em seguida, desmarque a opção **Sessão aprimorada**.

Ao testar essa funcionalidade usando a Área de Trabalho Remota, o link de “Redefinir senha” não aparece.

* Atualmente não há suporte para a redefinição de senha a partir de uma Área de Trabalho Remota.

Se a tela de bloqueio do Windows for desabilitada usando uma chave do Registro ou uma política de grupo, **Redefinir senha** não estará disponível.

Se for exigido o Ctrl + Alt + Del pela política ou as notificações de tela de bloqueio estiverem desativadas, a **Redefinição de senha** não funcionará. O Windows 10 19H1 resolverá esse requisito.

O log de auditoria do Microsoft Azure AD inclui informações sobre o endereço IP e o ClientType em que a redefinição de senha ocorreu.

![Exemplo de redefinição de senha na tela de logon no log de auditoria do Microsoft Azure AD](media/tutorial-sspr-windows/windows-sspr-azure-ad-audit-log.png)

Se os seus computadores com Windows 10 estiverem atrás de um servidor proxy ou firewall, o tráfego HTTPS (443) para passwordreset.microsoftonline.com e ajax.aspnetcdn.com deverá ter permissão.

## <a name="clean-up-resources"></a>Limpar recursos

Se você decidir que não deseja mais usar a funcionalidade que você configurou como parte deste tutorial, exclua o perfil de configuração de dispositivo do Intune que você criou ou a chave do registro.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você permitirá que os usuários redefinam suas senhas na tela de logon do Windows 10. Continue com o próximo tutorial para ver como a Proteção de Identidade do Azure pode ser integrada na redefinição de senha de autoatendimento e as experiências da Autenticação Multifator.

> [!div class="nextstepaction"]
> [Avaliar o risco ao entrar](tutorial-risk-based-sspr-mfa.md)

[Assignment]: ./media/tutorial-sspr-windows/profile-assignment.png "Atribuir a política de configuração de dispositivo do Intune a um grupo de dispositivos Windows 10"
[LoginScreen]: ./media/tutorial-sspr-windows/logon-reset-password.png "Link Redefinir senha na tela de logon do Windows 10"
