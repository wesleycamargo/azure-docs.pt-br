---
title: "Azure AD Connect: Logon Único Contínuo | Microsoft Docs"
description: "Este tópico descreve o Logon Único Contínuo do Azure AD (Azure Active Directory) e como ele permite que você forneça o verdadeiro logon único em para usuários do computador desktop corporativo dentro da rede corporativa."
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
ms.date: 05/08/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 2601850f99188445cf63a6a4f185bdc4ebb92c29
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---

# <a name="azure-active-directory-seamless-single-sign-on"></a>Logon Único Contínuo do Azure Active Directory

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>O que é o Logon Único Contínuo do Azure Active Directory?

O Logon Único Contínuo do Azure Active Directory (SSO contínuo do Azure AD) fornece o verdadeiro logon único para usuários entrando nos desktops corporativos deles conectados à rede corporativa. Quando habilitado, os usuários não precisam digitar as senhas para entrar no Azure AD; na maioria dos casos, não precisam nem mesmo digitar os nomes de usuário. Essa funcionalidade fornece aos usuários acesso fácil a seus serviços baseados em nuvem sem a necessidade de nenhum componente local adicional.

O SSO contínuo pode ser habilitado por meio do Azure AD Connect e pode ser combinado com a [sincronização de senha](active-directory-aadconnectsync-implement-password-synchronization.md) ou então a [autenticação de passagem](active-directory-aadconnect-pass-through-authentication.md).

>[!NOTE]
>Essa funcionalidade NÃO é aplicável aos Serviços de Federação do Active Directory (AD FS), que já fornecem essa funcionalidade.

Para que essa funcionalidade funcione para um usuário específico, as condições a seguir devem ser atendidas:

- Que o usuário esteja entrando em um computador desktop corporativo.
- Que o computador desktop tenha ingressado anteriormente no seu domínio do AD (Active Directory).
- Que o computador desktop tenha uma conexão direta com um DC (controlador de domínio), seja na rede corporativa com ou sem fio ou por meio de uma conexão de acesso remoto, como uma conexão VPN.
- Que nossos pontos de extremidade de serviço tenham sido incluídos na zona de Intranet do navegador.

Se qualquer uma das condições anteriores não for atendida, será solicitado que o usuário insira o nome de usuário e a senha como fazia antes.

![Logon Único Contínuo](./media/active-directory-aadconnect-sso/sso1.png)

As outras funcionalidades do SSO Contínuo são as seguintes:

- Se uma solicitação de conexão do Azure AD incluir o parâmetro `domain_hint` ou `login_hint` (iniciada por um aplicativo no locatário), o SSO Contínuo aproveitará isso e o usuário não precisará inserir seu nome de usuário e sua senha.
- O SSO Contínuo dá suporte a um nome de usuário que é o nome de usuário local padrão (normalmente, “userPrincipalName”) ou a outro atributo configurado no Azure AD Connect (conhecido como “ID Alternativa”).
- O SSO contínuo é um recurso oportunista, ou seja, se ele falhar por algum motivo, a experiência de entrada do usuário retorna ao comportamento normal, no qual o usuário precisará digitar sua senha na página de entrada.

## <a name="whats-available-during-preview"></a>O que está disponível durante a visualização?

>[!NOTE]
>O SSO Contínuo do Azure AD está atualmente em visualização. Essa é uma funcionalidade gratuita e você não precisa de nenhuma edição paga do Azure AD para usá-la.

O SSO Contínuo tem suporte via clientes baseados em navegador da Web e clientes do Office que dão suporte à [autenticação moderna](https://aka.ms/modernauthga) em computadores desktop que são compatíveis com a autenticação Kerberos, como desktops baseados em Windows. A matriz a seguir fornece detalhes dos clientes baseados em navegador em vários sistemas operacionais.

| Sistema operacional\Navegador |Internet Explorer|Google Chrome|Mozilla Firefox|Edge
| --- | --- |--- | --- | --- |
|Windows 10|Sim|Sim|Sim\*|Não
|Windows 8.1|Sim|Sim|Sim\*|N/D
|Windows 8|Sim|Sim|Sim\*|N/D
|Windows 7|Sim|Sim|Sim\*|N/D
|Mac OS X|N/D|Sim\*|Sim\*|N/D 

\*Exige configuração adicional.

>[!NOTE]
>Para o Windows 10, a recomendação é usar o [Ingresso do Azure AD](../active-directory-azureadjoin-overview.md) para obter a experiência ideal com o Azure AD.

## <a name="how-does-azure-ad-seamless-sso-work"></a>Como funciona o SSO contínuo do Azure AD?

Você pode habilitar o SSO contínuo no Azure AD Connect conforme demonstrado [abaixo](#how-to-enable-azure-ad-seamless-sso?). Uma vez habilitado, uma conta de computador chamada AZUREADSSOACCT é criada no AD (Active Directory) local e a respectiva chave de descriptografia Kerberos é compartilhada com segurança com o Azure AD. Além disso, os dois SPNs (nomes de entidade de serviço) Kerberos são criados para representar duas URLs de nuvem que são usadas durante a entrada no Azure AD.

>[!NOTE]
> A conta do computador e os SPNs Kerberos precisam ser criados em para cada floresta do AD que você sincronizar ao Azure AD (por meio do Azure AD Connect) e para cujos usuários você deseja habilitar o SSO contínuo. Se sua floresta do AD tiver UOs (unidades organizacionais) para contas de computador, depois de habilitar o recurso de SSO contínuo, mova a conta de computador AZUREADSSOACCT para uma UO a fim de garantir que não seja excluída e seja gerenciada da mesma maneira que outras contas de computador.

Quando essa configuração é concluída, entrar no Azure AD funciona da mesma maneira que qualquer outra entrada que usa a IWA (autenticação integrada do Windows). O processo de SSO contínuo funciona da seguinte maneira:

Digamos que o usuário tenta acessar um recurso baseado em nuvem que é protegido pelo Azure AD, por exemplo, o SharePoint Online. O SharePoint Online redireciona o navegador do usuário para o Azure AD para entrar.

Se a solicitação de entrada ao Azure AD incluir um parâmetro `domain_hint` (identifica o locatário do Azure AD; por exemplo, contoso.onmicrosoft.com) ou `login_hint` (identifica o nome de usuário; por exemplo, user@contoso.onmicrosoft.com ou user@contoso.com), as etapas um a cinco ocorrerão.

Se um desses dois parâmetros não estiver incluído na solicitação, o usuário receberá uma solicitação para fornecer o nome de usuário na página de entrada do Azure AD. As etapas um a cinco só ocorrem depois que o usuário pressiona TAB para sair do campo de nome de usuário ou clica no botão "Continuar".

1. O Azure AD desafia o cliente, por meio da resposta 401 Não autorizado, para fornecer um tíquete Kerberos.
2. O cliente solicita um tíquete do Active Directory para o Azure AD (representado pela conta de computador que foi configurada anteriormente).
3. O Active Directory localiza a conta do computador e retorna um tíquete Kerberos para o cliente criptografado com o segredo da conta do computador. O tíquete inclui a identidade do usuário atualmente conectado ao computador desktop.
4. O cliente envia o tíquete Kerberos que adquiriu do Active Directory para o Azure AD.
5. O Azure AD descriptografa o tíquete Kerberos usando a chave compartilhada anteriormente. Se for bem-sucedido, o Azure AD retornará um token ou solicitará que o usuário execute provas adicionais como a autenticação multifator, conforme exigido pelo recurso.

O SSO contínuo é um recurso oportunista, ou seja, se ele falhar por algum motivo, a experiência de entrada do usuário retorna ao comportamento normal, no qual o usuário precisará digitar sua senha na página de entrada.

O processo também está ilustrado no diagrama abaixo:

![Logon Único Contínuo](./media/active-directory-aadconnect-sso/sso2.png)

## <a name="how-to-enable-azure-ad-seamless-sso"></a>Como habilitar o SSO contínuo do Azure AD?

### <a name="pre-requisites"></a>Pré-requisitos

Se você estiver habilitando SSO contínuo com autenticação de passagem, não haverá nenhum pré-requisito adicional além do que é necessário para a funcionalidade de autenticação de passagem.

Se você estiver habilitando o SSO contínuo com sincronização de senha e se houver um firewall entre o Azure AD Connect e o Azure AD, certifique-se de que:

- O servidor do Azure AD Connect possa se comunicar com URLs do `*.msappproxy.net`.
- O Azure AD Connect (versões 1.1.484.0 ou superiores) pode fazer solicitações HTTPS para o Azure AD pela porta 443. Isso só é usado para habilitar o recurso, não para conexões de usuário reais.
- O Azure AD Connect também estabelece conexões IP diretas com os [intervalos de IP do data center do Azure](https://www.microsoft.com/download/details.aspx?id=41653). Novamente, isso só é usado para habilitar o recurso.

>[!NOTE]
> As versões mais antigas do Azure AD Connect (inferiores a 1.1.484.0) precisam conseguir se comunicar com o Azure AD pela porta 9090.

### <a name="enabling-the-azure-ad-seamless-sso-feature"></a>Habilitando a funcionalidade SSO contínuo do Azure AD

O SSO contínuo do Azure AD pode ser habilitado por meio do Azure AD Connect.

Se você estiver executando uma instalação nova do Azure AD Connect, escolha o [caminho de instalação personalizado](active-directory-aadconnect-get-started-custom.md). Na página "Entrada do usuário", marque a opção "Habilitar logon único".

![Azure AD Connect – Entrada do usuário](./media/active-directory-aadconnect-sso/sso8.png)

Se você já tiver uma instalação do Azure AD Connect, instale usando o caminho de [instalação expressa](active-directory-aadconnect-get-started-express.md) ou [instalação personalizada](active-directory-aadconnect-get-started-custom.md), escolha "Alterar página de entrada do usuário" no Azure AD Connect e clique em "Avançar". Em seguida, marque a opção "Habilitar logon único".

![Azure AD Connect – Alterar entrada do usuário](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

Prossiga com o assistente até você chegar à página “Habilitar logon único”. Você precisará fornecer as credenciais de administrador de domínio para cada floresta do AD que você sincronizar ao Azure AD (por meio do Azure AD Connect) e para cujos usuários você deseja habilitar o SSO contínuo. Observe que as credenciais de administrador de domínio não são armazenadas no Azure AD Connect ou Azure AD, mas são usadas somente para criar a conta do computador e configurar os SPNs Kerberos conforme descrito anteriormente.

Após a conclusão do assistente, o SSO Contínuo é habilitado no locatário. Observe que você ainda precisa concluir as etapas na próxima seção antes que os usuários possam se beneficiar dessa funcionalidade.

## <a name="rolling-the-feature-out-to-your-users"></a>Distribuindo a funcionalidade para os usuários

Para distribuir o recurso SSO contínuo para seus usuários, você precisará adicionar duas URLs do Azure AD (https://autologon.microsoftazuread-sso.com e https://aadg.windows.net.nsatc.net) às configurações de zona de Intranet dos usuários por meio da política de grupo no Active Directory. Observe que isso funciona apenas para o Internet Explorer e o Google Chrome (se compartilhar o mesmo conjunto de URLs de sites confiáveis que o Internet Explorer). Você precisará configurar separadamente para o Mozilla Firefox.

### <a name="why-do-you-need-this"></a>Por que você precisa fazer isso?

Por padrão, os navegadores não enviam tíquetes Kerberos para um ponto de extremidade de nuvem, a menos que a URL seja definida como parte da zona de Intranet do navegador. O navegador calcula automaticamente a zona correta (Internet ou Intranet) segundo a URL. Por exemplo, http://contoso/ será mapeada para a zona da Intranet, enquanto http://intranet.contoso.com/ será mapeada para a zona da Internet (porque a URL contém um ponto).

Já que as URLs do Azure AD usadas para SSO contínuo contêm um ponto, elas precisam ser adicionadas explicitamente às configurações de zona de Intranet de cada navegador. Isso faz com que o navegador envie automaticamente os tíquetes Kerberos do usuário atualmente conectado para o Azure AD. Embora você possa fazer isso manualmente em cada desktop, a maneira mais fácil de adicionar as URLs necessárias à zona da Intranet para todos os usuários é simplesmente criar uma política de grupo no Active Directory.

### <a name="detailed-steps"></a>Etapas detalhadas

1. Abra a ferramenta de Gerenciamento de Política de Grupo.
2. Edite a política de grupo aplicada a todos os usuários, por exemplo a **Política de Domínio Padrão**.
3. Navegue até **Configuração do Usuário\Modelos Administrativos\Componentes do Windows\Internet Explorer\Painel de Controle da Internet\Página de Segurança** e selecione **Lista de Atribuição de Site para Zona**.
![Logon Único](./media/active-directory-aadconnect-sso/sso6.png)  
4. Habilite a política e insira os valores/dados a seguir na caixa de diálogo. Essas são as URLs do Azure AD para as quais os tíquetes Kerberos são enviados.

        Value: https://autologon.microsoftazuread-sso.com
        Data: 1
        Value: https://aadg.windows.net.nsatc.net
        Data: 1
5. Clique em **OK** e em **OK** novamente.

O resultado deve ser assim:

![Logon único](./media/active-directory-aadconnect-sso/sso7.png)

>[!NOTE]
>Por padrão, o Chrome usa o mesmo conjunto de URLs de sites confiáveis que o Internet Explorer. Se tiver definido configurações diferentes para o Chrome, você precisará atualizá-las separadamente.

## <a name="disabling-azure-ad-seamless-sso"></a>Desabilitando o SSO Contínuo do Azure AD

O SSO Contínuo do Azure AD pode ser desabilitado por meio do Azure AD Connect.

Execute o Azure AD Connect, escolha “Alterar página de conexão do usuário” e clique em “Avançar”. Em seguida, desmarque a opção “Habilitar logon único”. Continue com o assistente. Após a conclusão do assistente, o SSO Contínuo é desabilitado no locatário. No entanto, você verá uma mensagem na tela que informa o seguinte:

“O logon único agora está desabilitado, mas há etapas manuais adicionais a serem realizadas para concluir a limpeza. Saiba mais”

Estas são as etapas manuais de que você precisa:

- Obter a lista de florestas do AD em que o SSO Contínuo foi habilitado
  - No PowerShell, chame `New-AzureADSSOAuthenticationContext`. Isso deve fornecer a você um pop-up para inserir suas credenciais de administrador de locatários do Azure AD.
  - Chame `Get-AzureADSSOStatus`. Isso fornecerá a lista de florestas do AD (consulte a lista “Domínios”) em que esse recurso foi habilitado.
- Exclua manualmente a conta do computador AZUREADSSOACCT de cada floresta do AD que você encontrar listada acima.

## <a name="next-steps"></a>Próximas etapas

- Leia nosso [guia de solução de problemas](active-directory-aadconnect-troubleshoot-sso.md) para saber como resolver problemas comuns com o SSO Contínuo do Azure AD.

## <a name="feedback"></a>Comentários

Seus comentários são importantes para nós. Use a seção de comentários abaixo caso tenha dúvidas. Use nosso [fórum do UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) para fazer solicitações de novos recursos.

