---
title: "Autenticação de passagem do Azure AD – início rápido | Microsoft Docs"
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
ms.date: 07/12/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: c1bc7cc5fe53d04019f68a520fb03c9187a6148b
ms.contentlocale: pt-br
ms.lasthandoff: 07/06/2017

---

# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Autenticação de passagem do Azure Active Directory: início rápido

A autenticação de passagem do Azure AD (Azure Active Directory) permite que os usuários entrem em aplicativos locais e baseados em nuvem usando as mesmas senhas. Ela permite a entrada de usuários validando suas senhas diretamente no Active Directory local.

## <a name="how-to-deploy-azure-ad-pass-through-authentication"></a>Como implantar a autenticação de passagem do Azure AD

Para implantar autenticação de passagem, você precisa seguir estas etapas:
1. *Verificar pré-requisitos*: configure o seu ambiente local e de locatário corretamente antes de habilitar o recurso.
2. *Habilitar o recurso*: ative a autenticação de passagem em seu locatário e instale um agente local leve para manipular as solicitações de validação de senha.
3. *Testar o recurso*: teste a entrada de usuário usando a autenticação de passagem.
4. *Garantir a alta disponibilidade*: instale um segundo agente autônomo para fornecer alta disponibilidade às solicitações de logon.

## <a name="step-1-check-prerequisites"></a>Etapa 1: verificar pré-requisitos

Verifique se os seguintes pré-requisitos estão em vigor:

### <a name="on-the-azure-portal"></a>No portal do Azure

1. Crie uma conta Administrador Global somente em nuvem no seu locatário do Azure AD. Dessa forma, você pode gerenciar a configuração do seu locatário caso seus serviços locais falhem ou fiquem indisponíveis. Saiba mais sobre [adicionar uma conta de Administrador Global somente de nuvem](../active-directory-users-create-azure-portal.md). Executar essa etapa é essencial para garantir que você não seja bloqueado de seu locatário.
2. Adicione um ou mais [nomes de domínio personalizados](../active-directory-add-domain.md) ao seu locatário do Azure AD. Os usuários entram usando um desses nomes de domínio.

### <a name="in-your-on-premises-environment"></a>Em seu ambiente local

1. Identifique um servidor que execute o Windows Server 2012 R2 ou posterior para executar o Azure AD Connect. Adicione o servidor à mesma floresta do AD como os usuários cujas senhas precisam ser validadas.
2. Instale a [versão mais recente do Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) no servidor identificado na Etapa 2. Se o Azure AD Connect já estiver em execução, verifique se a versão é 1.1.486.0 ou posterior.
3. Identifique um servidor adicional executando o Windows Server 2012 R2 ou posterior no qual o Agente de Autenticação autônomo será executado. A versão do Agente de Autenticação precisa ser a 1.5.58.0 ou posterior. Este servidor é necessário para garantir a alta disponibilidade das solicitações de entrada. Adicione o servidor à mesma floresta do AD como os usuários cujas senhas precisam ser validadas.
4. Se houver um firewall entre os servidores e o Azure AD, será necessário configurar os seguintes itens:
   - Abra as portas: verifique se os agentes de autenticação nos seus servidores podem fazer solicitações de saída para o Azure AD pelas portas 80 e 443. Se o firewall impõe as regras de acordo com os usuários de origem, abra essas portas para o tráfego proveniente de serviços do Windows em execução como um serviço de rede.
   - Permita os pontos de extremidade do Azure AD: se a filtragem de URL estiver habilitada, verifique se os agentes de autenticação podem se comunicar com **\*.msappproxy.net** e **\*.servicebus.windows.net**.
   - Verifique as conexões de IP diretas: verifique se os agentes de autenticação nos seus servidores podem fazer conexões de IP diretas com os [Intervalos de IP do datacenter do Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

## <a name="step-2-enable-the-feature"></a>Etapa 2: habilitar o recurso

A autenticação de passagem pode ser habilitada usando o [Azure AD Connect](active-directory-aadconnect.md).

>[!IMPORTANT]
>A autenticação de passagem pode ser habilitada no servidor primário ou de preparo do Azure AD Connect. É altamente recomendável habilitá-la do servidor primário.

Se você estiver instalando o Azure AD Connect pela primeira vez, escolha o [caminho de instalação personalizado](active-directory-aadconnect-get-started-custom.md). Na página **Entrada de usuário** escolha **Autenticação de Passagem** como o método de logon. Após a conclusão bem-sucedida, um Agente de Autenticação de passagem estará instalado no mesmo servidor do Azure AD Connect. Além disso, o recurso de autenticação de passagem estará habilitado em seu locatário.

![Azure AD Connect – conexão do usuário](./media/active-directory-aadconnect-sso/sso3.png)

Se você já tiver instalado o Azure AD Connect (usando o caminho de [instalação expressa](active-directory-aadconnect-get-started-express.md) ou de [instalação personalizada](active-directory-aadconnect-get-started-custom.md)), selecione **Alterar página de entrada do usuário** no Azure AD Connect e clique em **Avançar**. Em seguida, selecione **Autenticação de passagem** como o método de logon. Após a conclusão bem-sucedida, um Agente de Autenticação de passagem estará instalado no mesmo servidor que o Azure AD Connect e o recurso estará habilitado em seu locatário.

![Azure AD Connect – Alterar entrada do usuário](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>A Autenticação de Passagem é um recurso no nível do locatário. A ativação desse recurso afeta a entrada de usuários em _todos_ os domínios gerenciados no seu locatário.

## <a name="step-3-test-the-feature"></a>Etapa 3: testar o recurso

Após a Etapa 2, os usuários de todos os domínios gerenciados no seu locatário entrarão usando a autenticação de passagem. No entanto, os usuários de domínios federados continuam a entrar usando o Serviços de Federação do Active Directory (AD FS) ou outro provedor de federação que já esteja configurado. Se você converter um domínio de federado para gerenciado, todos os usuários nesse domínio passarão automaticamente a entrar usando a autenticação de passagem. Os usuários somente em nuvem não são afetados pelo recurso de autenticação de passagem.

## <a name="step-4-ensure-high-availability"></a>Etapa 4: Verificar a alta disponibilidade

Se você planeja implantar autenticação de passagem em um ambiente de produção, instale um Agente de Autenticação autônomo. Instale esse segundo Agente de Autenticação em um servidor _que não seja_ o que está executando o Azure AD Connect e o primeiro Agente de Autenticação. Esta instalação fornece alta disponibilidade para solicitações de entrada. Siga estas instruções para implantar um Agente de Autenticação autônomo:

### <a name="download-and-install-the-authentication-agent-software-on-your-server"></a>Baixe e instale o software do Agente de Autenticação no servidor

1.  [Baixe](https://go.microsoft.com/fwlink/?linkid=837580) o software mais recente do Agente de Autenticação. Verifique se a versão é 1.5.58.0 ou posterior.
2.  Abra o prompt de comando como administrador.
3.  Execute o seguinte comando (a opção **/q** significa "instalação silenciosa" – a instalação não solicitará que você aceite os termos de licença):`
AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
`

>[!NOTE]
>Você pode instalar apenas um único Agente de Autenticação por servidor.

### <a name="register-the-authentication-agent-with-azure-ad"></a>Registrar o Agente de Autenticação no Azure AD

1.  Abra uma janela do PowerShell como administrador.
2.  Navegue até **C:\Program Files\Microsoft AAD App Proxy Conector** e execute o script conforme demonstrado a seguir: `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Feature PassthroughAuthentication`
3.  Quando solicitado, insira as credenciais de sua conta de Administrador Global no locatário do Azure AD.

## <a name="next-steps"></a>Próximas etapas
- [**Limitações atuais**](active-directory-aadconnect-pass-through-authentication-current-limitations.md) – esse recurso está na versão prévia no momento. Saiba quais cenários têm suporte e quais não têm.
- [**Aprofundamento técnico**](active-directory-aadconnect-pass-through-authentication-how-it-works.md) – entenda como esse recurso funciona.
- [**Perguntas frequentes**](active-directory-aadconnect-pass-through-authentication-faq.md) – respostas para perguntas frequentes.
- [**Solução de problemas**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) – Saiba como resolver problemas comuns do recurso.
- [**SSO contínuo do Azure AD**](active-directory-aadconnect-sso.md) – Saiba mais sobre esse recurso complementar.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – para registrar solicitações de novos recursos.

