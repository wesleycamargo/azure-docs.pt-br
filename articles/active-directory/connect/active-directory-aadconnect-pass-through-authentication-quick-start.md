---
title: "Autenticação de passagem do Azure AD – Início rápido | Microsoft Docs"
description: "Este artigo descreve como começar a usar a autenticação de passagem do Azure AD (Azure Active Directory)."
services: active-directory
keywords: "Autenticação de Passagem do Azure AD Connect, instalar o Active Directory, componentes necessários para o Azure AD, SSO, Logon único"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: 8adca38cc5a783abfe29725dbb0a201de4183dad
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2017
---
# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Autenticação de passagem do Azure Active Directory: início rápido

## <a name="deploy-azure-ad-pass-through-authentication"></a>Implantar a autenticação de passagem do Azure AD

A autenticação de passagem do Azure Active Directory (Azure AD) permite que os usuários entrem em aplicativos locais e baseados em nuvem usando as mesmas senhas. Ela permite a entrada de usuários validando suas senhas diretamente no Active Directory local.

>[!IMPORTANT]
>Se usar esse recurso por meio de uma versão prévia, verifique se você atualizou as versões prévias dos Agentes de autenticação usando as instruções fornecidas em [Autenticação de passagem do Azure Active Directory: atualizar versão prévia dos Agentes de autenticação](./active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md).

Siga estas instruções para implantar a Autenticação de passagem:

## <a name="step-1-check-the-prerequisites"></a>Etapa 1: Verificar os pré-requisitos

Verifique se os pré-requisitos a seguir estão em vigor.

### <a name="in-the-azure-active-directory-admin-center"></a>No centro de administração do Azure Active Directory

1. Crie uma conta de administrador global somente em nuvem no seu locatário do Azure AD. Dessa forma, você pode gerenciar a configuração do seu locatário caso seus serviços locais falhem ou fiquem indisponíveis. Saiba mais sobre [adicionar uma conta de administrador global somente em nuvem](../active-directory-users-create-azure-portal.md). A conclusão dessa etapa é essencial para garantir que você não seja bloqueado de seu locatário.
2. Adicione um ou mais [nomes de domínio personalizados](../active-directory-domains-add-azure-portal.md) ao seu locatário do Azure AD. Os usuários podem entrar com um desses nomes de domínio.

### <a name="in-your-on-premises-environment"></a>Em seu ambiente local

1. Identifique um servidor que execute o Windows Server 2012 R2 ou posterior para executar o Azure AD Connect. Adicione o servidor à mesma floresta do Active Directory dos usuários cujas senhas você precisa validar.
2. Instale a [última versão do Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) no servidor identificado na etapa anterior. Se o Azure AD Connect já está em execução, verifique se a versão é a 1.1.644.0 ou posterior.

    >[!NOTE]
    >As versões 1.1.557.0, 1.1.558.0, 1.1.561.0 e 1.1.614.0 do Azure AD Connect têm um problema relacionado à sincronização de hash de senha. Se você _não_ pretende usar a sincronização de hash de senha em conjunto com a Autenticação de passagem, leia as [Notas de versão do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470).

3. Identifique um servidor adicional (que execute o Windows Server 2012 R2 ou posterior) no qual você pode executar o Agente de Autenticação autônomo. A versão do Agente de autenticação precisa ser a 1.5.193.0 ou posterior. Esse servidor adicional é necessário para garantir a alta disponibilidade de solicitações para entrar. Adicione o servidor à mesma floresta do Active Directory dos usuários cujas senhas você precisa validar.
4. Se houver um firewall entre os servidores e o Azure AD, configure os seguintes itens:
   - Certifique-se de que os Agentes de Autenticação podem fazer solicitações de *saída* ao Azure AD sobre as seguintes portas:
   
    | Número da porta | Como ele é usado |
    | --- | --- |
    | **80** | Baixa as listas de certificados revogados (CRLs) enquanto valida o certificado SSL |
    | **443** | Lida com toda a comunicação de saída com o serviço |
   
    Se o firewall impõe as regras de acordo com os usuários originadores, abra essas portas para o tráfego proveniente dos serviços Windows que são executados como um serviço de rede.
   - Se o seu firewall ou proxy permitirem lista de permissões de DNS, adicione as conexões a **\*msappproxy.net** e **\*servicebus.windows.net** à lista de permissões. Caso contrário, permita o acesso aos [Intervalos de IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653), os quais são atualizados semanalmente.
   - Os seus Agentes de autenticação devem acessar **login.windows.net** e **login.microsoftonline.net** para o registro inicial. Abra seu firewall para essas URLs também.
   - Para validação de certificado, desbloqueie as seguintes URLs: **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80** e **www.microsoft.com:80**. Essas URLs são usadas para a validação de certificado com outros produtos da Microsoft. Talvez essas URLs já estejam desbloqueados.

## <a name="step-2-enable-exchange-activesync-support-optional"></a>Etapa 2: Habilitar o suporte do Exchange ActiveSync (opcional)

Siga estas instruções para habilitar o suporte do Exchange ActiveSync:

1. Use o [PowerShell do Exchange](https://technet.microsoft.com/library/mt587043(v=exchg.150).aspx) para executar o comando a seguir:
```
Get-OrganizationConfig | fl per*
```

2. Verifique o valor da configuração `PerTenantSwitchToESTSEnabled`. Se o valor for **true**, o locatário está configurado corretamente. Geralmente é isso que acontece para a maioria dos clientes. Se o valor for **false**, execute o seguinte comando:
```
Set-OrganizationConfig -PerTenantSwitchToESTSEnabled:$true
```

3. Verifique se o valor da configuração `PerTenantSwitchToESTSEnabled` agora está definida como **true**. Aguarde uma hora antes de passar para a próxima etapa.

Se encontrar problemas durante essa etapa, verifique o [guia de solução de problemas](active-directory-aadconnect-troubleshoot-pass-through-authentication.md#exchange-activesync-configuration-issues).

## <a name="step-3-enable-the-feature"></a>Etapa 3: Habilitar o recurso

Habilite a Autenticação de passagem por meio do [Azure AD Connect](active-directory-aadconnect.md).

>[!IMPORTANT]
>É possível habilitar a Autenticação de passagem no servidor primário ou de preparo do Azure AD Connect. Você deve habilitá-lo a partir do servidor primário.

Se estiver instalando o Azure AD Connect pela primeira vez, escolha o [caminho de instalação personalizado](active-directory-aadconnect-get-started-custom.md). Na página **Entrada de usuário** escolha **Autenticação de Passagem** como o **Método de logon**. Após a conclusão bem-sucedida, um Agente de autenticação de passagem estará instalado no mesmo servidor do Azure AD Connect. Além disso, o recurso de autenticação de passagem estará habilitado em seu locatário.

![Azure AD Connect: entrada do usuário](./media/active-directory-aadconnect-sso/sso3.png)

Se já tiver instalado o Azure AD Connect usando o caminho de [instalação expressa](active-directory-aadconnect-get-started-express.md) ou de [instalação personalizada](active-directory-aadconnect-get-started-custom.md), selecione a tarefa **Alterar entrada do usuário** no Azure AD Connect e selecione **Avançar**. Depois selecione **Autenticação de Passagem** como o método de entrada. Após a conclusão bem-sucedida, um Agente de autenticação de passagem estará instalado no mesmo servidor que o Azure AD Connect, e o recurso estará habilitado em seu locatário.

![Azure AD Connect: alterar entrada do usuário](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>A Autenticação de Passagem é um recurso no nível do locatário. A ativação desse recurso afeta a entrada de usuários em _todos_ os domínios gerenciados no seu locatário. Se estiver alternando dos Serviços de Federação do Active Directory (AD FS) para Autenticação de passagem, você deve esperar pelo menos 12 horas antes de desligar a infraestrutura do AD FS. Esse tempo de espera é para garantir que os usuários continuem entrando no Exchange ActiveSync durante a transição.

## <a name="step-4-test-the-feature"></a>Etapa 4: testar o recurso

Siga estas instruções para verificar se você habilitou a Autenticação de passagem corretamente:

1. Entre no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) com as credenciais do administrador global do seu locatário.
2. Selecione **Azure Active Directory** no painel esquerdo.
3. Selecione **Azure AD Connect**.
4. Verifique se o recurso **Autenticação de passagem** aparece como **Habilitado**.
5. Selecione **Autenticação de passagem**. Esse painel da **Autenticação de passagem** lista os servidores em que os Agentes de autenticação estão instalados.

![Centro de administração do Azure Active Directory: painel do Azure AD Connect](./media/active-directory-aadconnect-pass-through-authentication/pta7.png)

![Centro de administração do Azure Active Directory: painel da Autenticação de passagem](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

Nesse momento, os usuários de todos os domínios gerenciados no seu locatário podem entrar usando a Autenticação de passagem. No entanto, os usuários de domínios federados continuam a entrar usando o AD FS ou outro provedor de federação que já esteja configurado. Se você converter um domínio de federado para gerenciado, todos os usuários nesse domínio passarão automaticamente a entrar usando a Autenticação de passagem. O recurso de Autenticação de passagem os não afeta os usuários somente de nuvem.

## <a name="step-5-ensure-high-availability"></a>Etapa 5: Verificar a alta disponibilidade

Se você planeja implantar autenticação de passagem em um ambiente de produção, instale um Agente de Autenticação autônomo. Instale esse segundo Agente de Autenticação em um servidor _que não seja_ o que está executando o Azure AD Connect e o primeiro Agente de Autenticação. Essa instalação fornece alta disponibilidade de solicitações para entrada. Siga estas instruções para implantar um Agente de Autenticação autônomo:

1. Baixe a versão mais recente do Agente de autenticação (versão 1.5.193.0 ou posterior). Entre no [centro de administração do Azure Active Directory](https://aad.portal.azure.com) com as credenciais do administrador global do seu locatário.
2. Selecione **Azure Active Directory** no painel esquerdo.
3. Selecione **Azure AD Connect**, **Autenticação de passagem** e depois **Baixar Agente**.
4. Selecione o botão **Aceitar termos e baixar**.
5. Instale a versão mais recente do Agente de autenticação por meio do executável baixado na etapa anterior. Quando solicitado, forneça as credenciais de administrador global do seu locatário.

![Centro de administração do Azure Active Directory: botão de Baixar Agente de Autenticação](./media/active-directory-aadconnect-pass-through-authentication/pta9.png)

![Centro de administração do Azure Active Directory: painel Baixar Agente](./media/active-directory-aadconnect-pass-through-authentication/pta10.png)

>[!NOTE]
>Você também pode baixar o [Agente de Autenticação do Azure Active Directory](https://aka.ms/getauthagent). Certifique-se de ler e aceitar os [Termos de Serviço](https://aka.ms/authagenteula) do Agente de Autenticação _antes_ de instalá-lo.

## <a name="next-steps"></a>Próximas etapas
- [Bloqueio Inteligente](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): saiba como configurar a capacidade de Bloqueio Inteligente no seu locatário para proteger as contas de usuário.
- [Limitações atuais](active-directory-aadconnect-pass-through-authentication-current-limitations.md): saiba quais cenários têm suporte na Autenticação de passagem e quais não têm.
- [Análise técnica aprofundada](active-directory-aadconnect-pass-through-authentication-how-it-works.md): entenda como funciona o recurso de Autenticação de passagem.
- [Perguntas frequentes](active-directory-aadconnect-pass-through-authentication-faq.md): encontre respostas para perguntas frequentes.
- [Solução de problemas](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): saiba como resolver problemas comuns com o recurso de Autenticação de Passagem.
- [Aprofundamento em segurança](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): obtenha informações técnicas sobre o recurso de Autenticação de passagem.
- [SSO contínuo do Azure AD](active-directory-aadconnect-sso.md): saiba mais sobre esse recurso complementar.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): use o Fórum do Azure Active Directory para arquivar novas solicitações.

