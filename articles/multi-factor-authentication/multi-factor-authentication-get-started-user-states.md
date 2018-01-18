---
title: "Estados de usuário da Autenticação Multifator do Microsoft Azure"
description: "Saiba mais sobre os estados do usuário na Autenticação Multifator do Azure."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 0b9fde23-2d36-45b3-950d-f88624a68fbd
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: ad8d531d633eb65fe90404fdab0499b8e5332db6
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-require-two-step-verification-for-a-user-or-group"></a>Como exigir verificação em duas etapas para um usuário ou grupo

Você pode usar uma das duas abordagens para exigir a verificação em duas etapas. A primeira opção é habilitar cada usuário para a Autenticação Multifator do Azure (MFA). Quando os usuários são habilitados individualmente, eles executam a verificação em duas etapas em cada entrada (com algumas exceções, como ao entrarem a partir de endereços IP confiáveis, ou quando o recurso de _dispositivos lembrados_ estiver ativado). A segunda opção é configurar uma política de acesso condicional que exige a verificação em duas etapas sob determinadas condições.

>[!TIP] 
>Escolha um destes métodos para exigir a verificação em duas etapas, não ambos. A habilitação de um usuário para a Autenticação Multifator do Azure substitui quaisquer políticas de acesso condicional.

## <a name="which-option-is-right-for-you"></a>Qual opção é ideal para você?

**A habilitação da Autenticação Multifator do Azure pela alteração de estados do usuário** é a abordagem tradicional para exigir a verificação em duas etapas. Isso funciona para a MFA do Azure na nuvem e para o Servidor de MFA do Azure. Todos os usuários habilitados executarão a verificação em duas etapas sempre que entrarem. A habilitação de um usuário substitui qualquer política de acesso condicional que possa afetá-lo. 

**A habilitação da Autenticação Multifator do Azure com uma política de acesso condicional** é uma abordagem mais flexível para exigir a verificação em duas etapas. No entanto, isso só funciona para a MFA do Azure na nuvem, e o _acesso condicional_ é um [recurso pago do Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features). Você pode criar políticas de acesso condicional que se aplicam a grupos, bem como a usuários individuais. Grupos de alto risco podem receber mais restrições do que grupos de baixo risco, ou a verificação em duas etapas pode ser exigida apenas para aplicativos de nuvem de alto risco e ignorada para os de baixo risco. 

As duas opções solicitam que os usuários se registrem na Autenticação Multifator do Azure na primeira vez que entrarem após a ativação dos requisitos. As duas opções também funcionam com as [definições configuráveis de Autenticação Multifator do Azure](multi-factor-authentication-whats-next.md).

## <a name="enable-azure-mfa-by-changing-user-status"></a>Habilitar a MFA do Azure alterando o status do usuário

As contas de usuário na Autenticação Multifator do Azure apresentam os três estados distintos a seguir:

| Status | DESCRIÇÃO | Aplicativos que não usam navegador afetados | Aplicativos que usam o navegador afetados | Autenticação moderna afetada |
|:---:|:---:|:---:|:--:|:--:|
| Desabilitado |O estado padrão para um novo usuário não inscrito na MFA do Azure. |Não  |Não  |Não  |
| habilitado |O usuário foi inscrito no MFA do Azure, mas não foi registrado. Eles receberão uma solicitação para se registrarem na próxima vez que entrarem. |Nº  Eles continuarão a trabalhar até o processo ser concluído. | Sim. Depois que a sessão expirar, será exigido o registro da MFA do Azure.| Sim. Depois que o token de acesso expirar, será exigido o registro da MFA do Azure. |
| Imposto |O usuário foi inscrito e concluiu o processo de registro para usar a MFA do Azure. |Sim.  Os aplicativos exigem senhas de aplicativo. |Sim. A MFA do Azure é exigida no logon. | Sim. A MFA do Azure é exigida no logon. |

O estado de um usuário reflete se um administrador o registrou na MFA do Azure e se ele concluiu o processo de registro.

Todos os usuários começam com o status *Desabilitado*. Quando você registra os usuários na MFA do Azure, seu estado é alterado para *Habilitado*. Quando usuários habilitados entram e concluem o processo de registro, seu estado é alterado para *Imposto*.  

### <a name="view-the-status-for-a-user"></a>Exibir o status de um usuário

Use as etapas a seguir para acessar a página em que você pode exibir e gerenciar estados de usuário:

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador.
2. Acesse **Azure Active Directory** > **Usuários e grupos** > **Todos os usuários**.
3. Selecione **Autenticação Multifator**.
   ![Selecionar a Autenticação Multifator](./media/multi-factor-authentication-get-started-user-states/selectmfa.png)
4. Uma nova página que exibe os estados do usuário é aberta.
   ![estados do usuário da Autenticação Multifator do Azure – captura de tela](./media/multi-factor-authentication-get-started-user-states/userstate1.png)

### <a name="change-the-status-for-a-user"></a>Alterar o status de um usuário

1. Use as etapas anteriores para chegar à página de **usuários** da Autenticação Multifator do Azure.
2. Localize o usuário que deseja habilitar para a MFA do Azure. Talvez seja necessário alterar o modo de exibição na parte superior. 
   ![Localizar usuário – captura de tela](./media/multi-factor-authentication-get-started-cloud/enable1.png)
3. Marque a caixa, ao lado do nome.
4. À direita, em **etapas rápidas**, escolha **Habilitar** ou **Desabilitar**.
   ![Habilitar o usuário selecionado – captura de tela](./media/multi-factor-authentication-get-started-cloud/user1.png)

   >[!TIP]
   >Usuários *Habilitados* mudam automaticamente para *Imposto* ao se registrarem na MFA do Azure. Não altere manualmente o estado do usuário para *Imposto*. 

5. Confirme sua seleção na janela pop-up que é aberta. 

Depois de habilitar os usuários, notifique-os por email. Informe que eles receberão uma solicitação de registro na próxima vez que entrarem. Além disso, se sua organização usar aplicativos sem navegador que não dão suporte à autenticação moderna, eles precisam criar senhas de aplicativo. Você também pode incluir um link para o [Guia do usuário final da MFA do Azure](./end-user/multi-factor-authentication-end-user.md) para ajudá-los a começar.

### <a name="use-powershell"></a>Usar o PowerShell
Para alterar o estado do usuário usando o [PowerShell do Azure AD](/powershell/azure/overview), altere `$st.State`. Há três opções de estado possíveis:

* habilitado
* Imposto
* Desabilitado  

Não mova os usuários diretamente para o estado *Imposto*. Caso os mova, aplicativos não baseados em navegador deixarão de funcionar porque o usuário não passou pelo registro do MFA do Azure nem obteve uma [senha de aplicativo](multi-factor-authentication-whats-next.md#app-passwords). 

Usar o PowerShell é uma boa opção quando você precisa habilitar usuários em massa. Crie um script do PowerShell que percorre uma lista de usuários e os habilita:

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

O script a seguir é um exemplo:

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }

## <a name="enable-azure-mfa-with-a-conditional-access-policy"></a>Habilitar a MFA do Azure com uma política de acesso condicional

O _acesso condicional_ é um recurso pago do Azure Active Directory, com várias opções de configuração. Estas etapas explicam uma maneira de criar uma política. Para saber mais, leia sobre o [Acesso condicional no Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md).

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador.
2. Acesse **Azure Active Directory** > **Acesso Condicional**.
3. Selecione **Nova política**.
4. Em **Atribuições**, selecione **Usuários e grupos**. Use as guias **Incluir** e **Excluir** para especificar quais usuários e grupos são gerenciados pela política.
5. Em **Atribuições**, selecione **Aplicativos de nuvem**. Opte por incluir **Todos os aplicativos de nuvem**.
6. Em **Controles de acesso**, selecione **Grant**. Escolha **Exigir autenticação multifator**.
7. Alterne **Habilitar política** para **Ativado** e selecione **Salvar**.

As outras opções na política de acesso condicional lhe dão a capacidade de especificar exatamente quando a verificação em duas etapas é obrigatória. Por exemplo, você pode criar uma política como esta: quando prestadores de serviço tentarem acessar nosso aplicativo de compras em redes não confiáveis e em dispositivos que não estão associados ao domínio, exigir a verificação em duas etapas. 

## <a name="next-steps"></a>Próximas etapas

- Obtenha dicas sobre as [Práticas recomendadas para acesso condicional](../active-directory/active-directory-conditional-access-best-practices.md).

- Gerenciar configurações de Autenticação Multifator do Azure para [seus usuários e os dispositivos deles](multi-factor-authentication-manage-users-and-devices.md).
