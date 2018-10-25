---
title: Introdução ao Azure MFA na nuvem
description: Introdução à Autenticação Multifator do Microsoft Azure com acesso condicional
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 09/01/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: bd2f796ab2feee4bb862d8de2c44efc742163f06
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167521"
---
# <a name="deploy-cloud-based-azure-multi-factor-authentication"></a>Implantar Autenticação Multifator do Microsoft Azure baseada em nuvem

A introdução ao Azure MFA (Autenticação Multifator do Microsoft Azure) é um processo simples.

Antes de iniciar, verifique se os pré-requisitos a seguir são cumpridos :

* Uma conta de administrador global no locatário do Azure AD. Se você precisar de ajuda para concluir esta etapa, consulte nosso artigo [Primeiros passos no Azure AD](../get-started-azure-ad.md).
* Licenças corretas atribuídas aos usuários. Se você precisar de mais informações, consulte o artigo [Como obter autenticação Multifator do Azure](concept-mfa-licensing.md).

## <a name="choose-how-to-enable"></a>Escolha como habilitar

**Habilitado pela política de acesso condicional** - Este método é abordado neste artigo. É o meio mais flexível para habilitar a verificação em duas etapas para os usuários. Habilitar usando política de acesso condicional somente funciona para MFA do Azure na nuvem e é um recurso Premium do Azure AD.

**Habilitado pelo Azure AD Identity Protection** - Esse método usa a política de risco do Azure AD Identity Protection para exigir a verificação em duas etapas com base apenas no risco de entrada para todos os aplicativos na nuvem. Este método requer o licenciamento do Azure Active Directory P2. Para obter mais informações sobre esse método podem ser encontradas em [como configurar a política de risco do usuário](../identity-protection/howto-user-risk-policy.md).

**Habilitado alterando o estado do usuário** - esse é o método tradicional para exigir a verificação em duas etapas. Ele funciona tanto para MFA do Azure na nuvem como Servidor de MFA do Azure. O uso desse método exigirá que os usuários realizem a verificação em duas etapas **sempre que** entrarem e substituírem as políticas de acesso condicional. Mais informações sobre esse método podem ser encontradas em [Como exigir a verificação em duas etapas para um usuário](howto-mfa-userstates.md).

> [!Note]
> Mais informações sobre licenças e preços podem ser encontradas nas páginas de preço do [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) e [Autenticação Multifator do Microsoft Azure](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="choose-authentication-methods"></a>Escolher métodos de autenticação

Habilite pelo menos um método de autenticação para os usuários com base nos requisitos da organização. Descobrimos que, quando habilitado para usuários o aplicativo Microsoft Authenticator fornece a melhor experiência do usuário. Se você precisar entender quais métodos estão disponíveis e como configurá-los, consulte o artigo [O que são métodos de autenticação](concept-authentication-methods.md).

## <a name="get-users-to-enroll"></a>Obter registro dos usuários

Após habilitar a política de acesso condicional, os usuários serão obrigados a registrarem-se na próxima vez que usarem um aplicativo protegido pela política. Se você habilitar uma política exigindo MFA para todos os usuários em todos os aplicativos na nuvem, essa ação poderá causar problemas aos usuários e ao suporte técnico. A recomendação é pedir aos usuários que registrem previamente os métodos de autenticação usando o portal de registro em [ https://aka.ms/mfasetup ](https://aka.ms/mfasetup). Muitas organizações consideram que a criação de cartazes, cartões de mesa e mensagens de email ajuda a impulsionar a adoção.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Habilitar Autenticação Multifator com acesso condicional

Entre no [portal do Azure](https://portal.azure.com) usando uma conta de administrador global.

### <a name="choose-verification-options"></a>Escolher opções de verificação

Antes de habilitar a Autenticação Multifator do Microsoft Azure, a organização deverá determinar quais opções de verificação são permitidas. Para os propósitos deste exercício, você habilita chamada telefônica e mensagem de texto para telefone, pois são opções genéricas que a maioria pode usar. Mais informações sobre os métodos de autenticação e o uso podem ser encontradas no artigo, [Quais são os métodos de autenticação?](concept-authentication-methods.md)

1. Navegue até **do Azure Active Directory**, **Usuários**, **Autenticação Multifator**.

   ![Acessando o portal de Autenticação Multifator na folha de usuários do Azure Active Directory no portal do Azure](media/howto-mfa-getstarted/users-mfa.png)

1. Na nova guia que é aberta, navegue até **configurações do serviço**.
1. Em **opções de verificação**, verifique todas as caixas para os métodos disponíveis aos usuários.

   ![Configurar métodos de verificação na guia configurações do serviço de Autenticação Multifator](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

4. Clique em **Save**.
5. Feche a guia **configurações de serviço**.

### <a name="create-conditional-access-policy"></a>Criar política de acesso condicional

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta de administrador global.
1. Navegue até **Azure Active Directory**, **Acesso condicional**.
1. Selecione **Nova política**.
1. .Forneça um nome significativo para a política.
1. Em **usuários e grupos**:
   * Na guia **Incluir**, selecione o botão de opção **Todos os usuários**
   * RECOMENDADO: Na guia **Excluir**, marque a caixa **Usuários e grupos** e escolha um grupo a ser usado para exclusões quando os usuários não têm acesso aos métodos de autenticação.
   * Clique em **Concluído**.
1. Em **Aplicativos na nuvem**, selecione o botão de opção **Todos os aplicativos na nuvem**.
   * OPCIONALMENTE: Na guia **Excluir**, escolha os aplicativos de nuvem que sua organização não exige o MFA.
   * Clique em **Concluído**.
1. Na seção **Condições**:
   * OPCIONALMENTE: Se você tiver habilitado o Azure Identity Protection, será possível optar por avaliar o risco de entrada como parte da política.
   * OPCIONALMENTE: Se você tiver configurado locais confiáveis ou localizações nomeadas, será possível especificar para incluir ou excluir esses locais da política.
1. Em **Concessão**, verifique se o botão de opção **Conceder acesso** está selecionado.
    * Marque a caixa para **Exigir autenticação multifator**.
    * Clique em **Selecionar**.
1. Ignore a seção **Sessão**.
1. Defina a alternância **Habilitar política** para **Ativado**.
1. Clique em **Criar**.

![Criar uma política de acesso condicional para habilitar a MFA para usuários do portal do Azure no grupo piloto](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)

### <a name="test-azure-multi-factor-authentication"></a>Testar a Autenticação Multifator do Microsoft Azure

Para confirmar que a política de acesso condicional funciona, teste o logon em um recurso que não deve exigir o MFA e, em seguida, no portal do Azure que requer o MFA.

1. Abra uma nova janela do navegador no modo InPrivate ou incógnito e navegue até [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com).
   * Faça logon com o usuário de teste criado como parte da seção de pré-requisitos deste artigo e observe que ele não deve solicitar a conclusão do MFA.
   * Feche a janela do navegador.
2. Abra uma nova janela do navegador no modo InPrivate ou incógnito e navegue até [https://portal.azure.com](https://portal.azure.com).
   * Faça logon com o usuário de teste criado como parte da seção de pré-requisitos deste artigo e observe que agora você deve ser solicitado a se registrar e usar a Autenticação Multifator do Microsoft Azure.
   * Feche a janela do navegador.

## <a name="next-steps"></a>Próximas etapas

Parabéns, você configurou a Autenticação Multifator do Microsoft Azure na nuvem.

Por que foi um usuário solicitado ou não é solicitado a executar a MFA? Consulte a seção [relatório de entradas do Microsoft Azure Active Directory nos relatórios do documento de autenticação multifator do Azure](howto-mfa-reporting.md#azure-ad-sign-ins-report).

Para definir configurações adicionais, como IPs confiáveis, mensagens de voz personalizadas e alertas de fraudes, consulte o artigo [Definir configurações de Autenticação Multifator do Microsoft Azure](howto-mfa-mfasettings.md).

Informações sobre como gerenciar configurações de usuário para Autenticação Multifator do Microsoft Azure podem ser encontradas no artigo [Gerenciar configurações do usuário com Autenticação Multifator do Microsoft Azure na nuvem](howto-mfa-userdevicesettings.md).

[Habilitar registro convergido para a Autenticação Multifator do Microsoft Azure Active Directory e a redefinição de senha por autoatendimento do Microsoft Azure AD](concept-registration-mfa-sspr-converged.md).
