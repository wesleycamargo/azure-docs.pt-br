---
title: "Introdução ao Azure MFA na nuvem | Microsoft Docs"
description: "Esta é a página da Microsoft Azure Multi-Factor Authentication que descreve como começar a usar o Azure MFA na nuvem."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 6b2e6549-1a26-4666-9c4a-cbe5d64c4e66
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/14/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 79c6ef627d20be52b6067d1f6dfea2a53505c590
ms.lasthandoff: 03/18/2017


---
# <a name="getting-started-with-azure-multi-factor-authentication-in-the-cloud"></a>Introdução ao Servidor Azure Multi-Factor Authentication na nuvem
Este artigo explica como começar a usar a autenticação multifator do Azure na nuvem.

> [!NOTE]
> A documentação a seguir fornece informações sobre como habilitar os usuários usando o **Portal Clássico do Azure**. Se você estiver procurando informações sobre como configurar a Autenticação Multifator do Azure para os usuários O365, confira [Configurar a autenticação multifator para o Office 365.](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US)

![MFA na Nuvem](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## <a name="prerequisite"></a>Pré-requisito
[Inscreva-se para ter uma assinatura do Azure](https://azure.microsoft.com/pricing/free-trial/) - se você ainda não tem uma assinatura do Azure, precisará inscrever-se para ter uma. Se você estiver apenas começando a usar o Azure MFA, poderá usar uma assinatura de avaliação.

## <a name="enable-azure-multi-factor-authentication"></a>Habilitar a Autenticação Multifator do Microsoft Azure
Se os usuários tiverem licenças que incluam a Autenticação Multifator do Microsoft Azure, nada precisa ser feito para ativar o Azure MFA. Você pode começar solicitando uma verificação em duas etapas em base de usuário individual. As licenças que habilitam o Azure MFA são:
- Autenticação Multifator do Azure
- Azure Active Directory Premium
- Enterprise Mobility + Security

Se você não tiver uma dessas três licenças, ou se não possuir licenças suficientes para cobrir todos os seus usuários, também não há problema. Você só precisa cumprir uma etapa extra e [Criar um Provedor de Autenticação Multifator](multi-factor-authentication-get-started-auth-provider.md) em seu diretório.

## <a name="turn-on-two-step-verification-for-users"></a>Ativar a verificação em duas etapas para usuários
Para começar a exigir uma verificação inicial em duas etapas para um usuário, altere o estado do usuário de desabilitado para habilitado.  Para obter mais informações sobre os estados do usuário, confira [Estados do Usuário na Autenticação Multifator do Azure](multi-factor-authentication-get-started-user-states.md)

Use o procedimento a seguir para habilitar o MFA para os usuários.

### <a name="to-turn-on-multi-factor-authentication"></a>Para ativar o Multi-Factor Authentication
1. Entre no [portal clássico do Azure](https://manage.windowsazure.com) como um administrador.
2. À esquerda, clique em **Active Directory**.
3. Em Diretório, selecione o diretório do usuário que deseja habilitar.
   ![Clicar no Diretório](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4. Na parte superior, clique em **Usuários**.
5. Na parte inferior da página, clique em **Gerenciar Multi-Factor Auth**. Uma nova guia do navegador é aberta.
   ![Clicar no Diretório](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6. Encontre o usuário para o qual você deseja habilitar a verificação em duas etapas. Talvez seja necessário alterar o modo de exibição na parte superior. Certifique-se de que o status é **desabilitado.**
   ![Habilitar usuário](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7. Coloque uma **marca** na caixa ao lado do nome.
8. À direita, clique em **Habilitar**.
   ![Habilitar o usuário](./media/multi-factor-authentication-get-started-cloud/user1.png)
9. Clique em **Habilitar autenticação multifator**.
   ![Habilitar o usuário](./media/multi-factor-authentication-get-started-cloud/enable2.png)
10. Observe que o estado do usuário foi alterado de **desabilitado** para **habilitado**.
    ![Habilitar os Usuários](./media/multi-factor-authentication-get-started-cloud/user.png)

Depois de habilitar os usuários, você deve notificá-los por email. Na próxima vez que eles tentarem fazer logon, serão solicitados a registrar sua conta para a verificação em duas etapas. Depois de começar a usar a verificação em duas etapas, eles também precisam configurar senhas de aplicativo para evitar o bloqueio de aplicativos sem navegador.

## <a name="use-powershell-to-automate-turning-on-two-step-verification"></a>Usar o PowerShell para automatizar a ativação da verificação em duas etapas
Para alterar o [estado](multi-factor-authentication-whats-next.md) usando o [PowerShell do Azure AD](/powershell/azureps-cmdlets-docs), você poderá usar o seguinte.  É possível alterar `$st.State` para ser igual a um dos estados a seguir:

* habilitado
* Imposto
* Desabilitado  

> [!IMPORTANT]
> Desencorajamos a migração dos usuários diretamente do estado Desabilitar para o estado Imposto. Aplicativos não baseados em navegador deixarão de funcionar porque o usuário não passou pelo registro MFA nem obteve uma [senha de aplicativo](multi-factor-authentication-whats-next.md#app-passwords). Se você tiver aplicativos sem navegador e exigir senhas de aplicativo, será recomendável passar de um estado Desabilitado para Habilitado. Isso permite que os usuários se registrem e obtenham suas senhas de aplicativo. Depois disso, você pode movê-los para Imposto.

Usar o PowerShell seria uma opção para habilitar os usuários em massa. Atualmente, não há nenhum recurso de habilitação em massa no portal do Azure e você precisa selecionar cada usuário individualmente. Essa poderá ser uma tarefa trabalhosa, se você tiver muitos usuários. Criando um script do PowerShell usando o método a seguir, você poderá percorrer uma lista de usuários e habilitá-los.

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

Veja um exemplo:

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }


Para obter mais informações, confira [Estados do usuário na autenticação multifator do Azure](multi-factor-authentication-get-started-user-states.md)

## <a name="next-steps"></a>Próximas etapas
Agora que você configurou a Autenticação Multifator do Azure na nuvem, poderá configurar e instalar sua implantação. Confira [Configurr a autenticação multifator do Azure](multi-factor-authentication-whats-next.md) para obter mais detalhes.


