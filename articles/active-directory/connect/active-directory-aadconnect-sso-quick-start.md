---
title: "Azure AD Connect: Logon Único Contínuo – Início Rápido| Microsoft Docs"
description: "Este artigo descreve como começar a usar o Logon Único Contínuo do Azure Active Directory."
services: active-directory
keywords: "o que é o Azure AD Connect, instalar o Active Directory, componentes necessários do Azure AD, SSO, Logon Único"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 05fb966e3e18b8d5242a2795248b9b72352d894d
ms.contentlocale: pt-br
ms.lasthandoff: 07/25/2017

---

# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Logon Único Contínuo do Azure Active Directory: Início Rápido

O SSO Contínuo do Azure AD (Logon Único Contínuo do Azure Active Directory) conecta usuários automaticamente quando estiverem nos respectivos desktops corporativos conectados à rede corporativa. Ele fornece aos usuários acesso fácil a seus aplicativos baseados em nuvem sem a necessidade de nenhum componente local adicional.

## <a name="how-to-deploy-azure-ad-seamless-sso"></a>Como implantar o SSO Contínuo do Azure AD

Para implantar o SSO Contínuo, você precisa seguir estas etapas:
1. *Verificar pré-requisitos*: configure o seu ambiente local e de locatário corretamente antes de habilitar o recurso.
2. *Habilitar o recurso*: ativar o SSO Contínuo em seu locatário usando o Azure AD Connect.
3. *Distribuir o recurso*: usar a Política de Grupo para distribuir o recurso para alguns ou todos os seus usuários.
4. *Testar o recurso*: testar a entrada do usuário usando o SSO Contínuo.
5. *Sobrepor chaves*: Frequentemente sobrepor chaves de descriptografia do Kerberos de contas de computador.

## <a name="step-1-check-prerequisites"></a>Etapa 1: verificar pré-requisitos

Verifique se os seguintes pré-requisitos estão em vigor:

1. Configurar seu servidor do Azure AD Connect: se você usa a [Autenticação de Passagem](active-directory-aadconnect-pass-through-authentication.md) como seu método de entrada, nenhuma ação adicional é necessária. Se você usa a [Sincronização de Hash de Senha](active-directory-aadconnectsync-implement-password-synchronization.md) como seu método de entrada e se há um firewall entre o Azure AD Connect e Azure AD, verifique se:
- Você está usando versões 1.1.484.0 ou posteriores do Azure AD Connect.
- O Azure AD Connect pode se comunicar com URLs `*.msappproxy.net` e sobre a porta 443. Esse pré-requisito é aplicável somente quando você habilita o recurso e não para entradas reais de usuário.
- O Azure AD Connect pode realizar conexões IP diretas com os [intervalos de IP do data center do Azure](https://www.microsoft.com/download/details.aspx?id=41653). Novamente, esse pré-requisito é aplicável somente quando você habilita o recurso.
2. Você precisa de credenciais de Administrador de Domínio para cada floresta do AD que você sincronizar com o Azure AD (usando o Azure AD Connect) e para aqueles usuários que você deseja habilitar o SSO Contínuo.

## <a name="step-2-enable-the-feature"></a>Etapa 2: habilitar o recurso

O SSO Contínuo pode ser habilitado usando o [Azure AD Connect](active-directory-aadconnect.md).

Se você está realizando uma instalação nova do Azure AD Connect, escolha o [caminho de instalação personalizada](active-directory-aadconnect-get-started-custom.md). Na página "Entrada do usuário", marque a opção "Habilitar logon único".

![Azure AD Connect – Entrada do usuário](./media/active-directory-aadconnect-sso/sso8.png)

Se você já tem uma instalação do Azure AD Connect, escolha "Alterar página de entrada do usuário" no Azure AD Connect e clique em "Avançar". Em seguida, marque a opção "Habilitar logon único".

![Azure AD Connect – Alterar entrada do usuário](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

Prossiga com o assistente até chegar à página “Habilitar logon único”. Forneça credenciais de Administrador de Domínio para cada floresta do AD que você sincronizar com o Azure AD (usando o Azure AD Connect) e para aqueles usuários que você deseja habilitar o SSO Contínuo. 

Após a conclusão do assistente, o SSO Contínuo está habilitado no seu locatário.

>[!NOTE]
> As credenciais de Administrador de Domínio não são armazenadas no Azure AD Connect ou no Azure AD, mas são usadas somente para habilitar o recurso.

## <a name="step-3-roll-out-the-feature"></a>Etapa 3: distribuir o recurso

Para distribuir o recurso aos seus usuários, você precisará adicionar duas URLs do Azure AD (https://autologon.microsoftazuread-sso.com e https://aadg.windows.net.nsatc.net) às configurações de zona de Intranet dos usuários por meio da Política de Grupo no Active Directory.

>[!NOTE]
> As instruções a seguir só funcionam para o Internet Explorer e Google Chrome no Windows (se ele compartilha o conjunto de URLs de sites confiáveis com o Internet Explorer). Leia a próxima seção para obter instruções para configurar o Mozilla Firefox e Google Chrome no Mac.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Por que você precisa modificar as configurações de Zona da Intranet dos usuários?

Por padrão, o navegador calcula automaticamente a zona correta (Internet ou Intranet) com base em uma URL. Por exemplo, http://contoso/ é mapeada para a Zona da Intranet, enquanto que http://intranet.contoso.com/ é mapeada para a Zona da Internet (porque a URL contém um ponto). Os navegadores não enviam tíquetes Kerberos a um ponto de extremidade de nuvem – como as duas URLs do Azure AD – a menos que a URL seja explicitamente adicionada à Zona da Intranet do navegador.

### <a name="detailed-steps"></a>Etapas detalhadas

1. Abra a ferramenta de Gerenciamento de Política de Grupo.
2. Edite a Política de Grupo que é aplicada a alguns ou todos os seus usuários. Neste exemplo, usamos a **Política de Domínio Padrão**.
3. Navegue até **Configuração do Usuário\Modelos Administrativos\Componentes do Windows\Internet Explorer\Painel de Controle da Internet\Página de Segurança** e selecione **Lista de Atribuição de Site para Zona**.
![Logon Único](./media/active-directory-aadconnect-sso/sso6.png)  
4. Habilite a política e insira os seguintes valores (URLs do Azure AD em que os tíquetes Kerberos são encaminhados) e dados (*1* indica a Zona da Intranet) na caixa de diálogo.

        Value: https://autologon.microsoftazuread-sso.com
        Data: 1
        Value: https://aadg.windows.net.nsatc.net
        Data: 1
>[!NOTE]
> Se você quiser cancelar a permissão de uso do SSO Contínuo de alguns usuários – por exemplo, se esses usuários estiverem entrando em quiosques compartilhados – defina os valores anteriores como *4*. Essa ação adiciona as URLs do Azure AD à Zona Restrita e ocasiona a falha do SSO Contínuo todas as vezes.

5. Clique em **OK** e em **OK** novamente.

![Logon único](./media/active-directory-aadconnect-sso/sso7.png)

### <a name="browser-considerations"></a>Considerações de navegador

#### <a name="mozilla-firefox"></a>Mozilla Firefox

O Mozilla Firefox não faz a autenticação Kerberos automaticamente. Cada usuário tem que adicionar manualmente as URLs do Azure AD às suas configurações do Firefox através das seguintes etapas:
1. Execute o Firefox e digite `about:config` na barra de endereços. Ignore as notificações que aparecerem.
2. Pesquise a preferência **network.negotiate-auth.trusted-uris**. Esta preferência lista os sites confiáveis do Firefox para a autenticação Kerberos.
3. Clique com o botão direito do mouse e selecione "Modificar".
4. Insira "https://autologon.microsoftazuread-sso.com, https://aadg.windows.net.nsatc.net" no campo.
5. Clique em "OK" e reabra o navegador.

#### <a name="safari-on-mac-os"></a>Safari no Mac OS

Certifique-se de que o computador executando o Mac OS é associado ao AD. Para obter instruções sobre como fazer isso [aqui](http://training.apple.com/pdf/Best_Practices_for_Integrating_OS_X_with_Active_Directory.pdf).

#### <a name="google-chrome-on-mac-os"></a>Google Chrome no Mac OS

Para o Google Chrome no Mac OS e outras plataformas que não sejam Windows, consulte [este artigo](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) para obter informações sobre como adicionar as URLs do Azure AD à lista de permissões para uma autenticação integrada.

O uso de extensões de Política de Grupo do Active Directory de terceiros para distribuir as URLs do Azure AD para o Firefox e o Google Chrome em usuários do Mac está fora do escopo deste artigo.

#### <a name="known-limitations"></a>Limitações conhecidas

O SSO Contínuo não funciona no modo de navegação particular em navegadores Firefox e Edge. Também não funciona no Internet Explorer se o navegador estiver em execução no modo de proteção aprimorada.

## <a name="step-4-test-the-feature"></a>Etapa 4: testar o recurso

Para testar o recurso para um usuário específico, verifique se _todas_ as seguintes condições estão em vigor:
  - O usuário está entrando em um dispositivo corporativo.
  - O dispositivo ingressou anteriormente em seu domínio do AD (Active Directory).
  - O dispositivo tem uma conexão direta com seu DC (Controlador de Domínio), seja na rede corporativa com ou sem fio ou por meio de uma conexão de acesso remoto, como uma conexão VPN.
  - Você [distribuiu o recurso](##step-3-roll-out-the-feature) a esse usuário usando a Política de Grupo.

Para testar o cenário em que o usuário insere somente o nome de usuário, mas não a senha:
- Entre no *https://myapps.microsoft.com/* em uma nova sessão privativa do navegador.

Para testar o cenário em que o usuário não tenha que inserir o nome de usuário ou a senha: 
- Entre no *https://myapps.microsoft.com/contoso.onmicrosoft.com* em uma nova sessão privativa do navegador. Substitua "*contoso*" pelo nome do seu locatário.
- Ou entre no *https://myapps.microsoft.com/contoso.com* em uma nova sessão privativa do navegador. Substitua "*contoso.com*" por um domínio verificado (não um domínio federado) em seu locatário.

## <a name="step-5-roll-over-keys"></a>Etapa 5: Sobrepor chaves

Na etapa 2, o Azure AD Connect cria contas de computador (representando o AD do Azure) em todas as florestas do AD no qual você habilitou o SSO contínuo. Saiba mais detalhes [aqui](active-directory-aadconnect-sso-how-it-works.md). Para maior segurança, é recomendável que você sobreponha frequentemente chaves de descriptografia Kerberos dessas contas de computador.

>[!IMPORTANT]
>Você não precisa executar essa etapa _imediatamente_ depois de habilitar o recurso. Sobrepor as chaves de descriptografia Kerberos pelo menos a cada 30 dias.

## <a name="next-steps"></a>Próximas etapas

- [**Aprofundamento técnico**](active-directory-aadconnect-sso-how-it-works.md) – entenda como esse recurso funciona.
- [**Perguntas frequentes**](active-directory-aadconnect-sso-faq.md) – respostas para perguntas frequentes.
- [**Solução de problemas**](active-directory-aadconnect-troubleshoot-sso.md) – Saiba como resolver problemas comuns do recurso.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – para registrar solicitações de novos recursos.

