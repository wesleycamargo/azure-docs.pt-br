---
title: "Problema ao instalar a extensão do navegador do painel de acesso do aplicativo | Microsoft Docs"
description: "Como corrigir erros comuns encontrados ao instalar a extensão do navegador do painel de acesso"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: asteen
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 40ace51c4ba74039b3a5d5997acdf2da7b98b4a9
ms.lasthandoff: 04/17/2017


---

# <a name="problem-installing-the-application-access-panel-browser-extension"></a>Problema ao instalar a extensão do navegador do painel de acesso do aplicativo

O Painel de Acesso é um portal baseado na Web que permite a um usuário que tenha uma conta corporativa ou de estudante no Azure Active Directory (Azure AD) exibir e iniciar aplicativos baseados em nuvem para os quais o administrador do Azure AD concedeu acesso. Um usuário com as edições do Azure AD também pode usar os recursos de gerenciamento de grupo de autoatendimento e aplicativo por meio do Painel de Acesso. O Painel de Acesso é separado do Portal do Azure e não exige que os usuários tenham uma assinatura do Azure.

Para usar o SSO (logon único) baseado em senha no Painel de Acesso, a extensão do Painel de Acesso deve estar instalada no navegador do usuário. Essa extensão é baixada automaticamente quando um usuário seleciona um aplicativo configurado para SSO baseado em senha.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Atender aos requisitos de navegador para o Painel de Acesso

O Painel de Acesso exige um navegador com suporte para JavaScript e CSS habilitado. Para usar o SSO (logon único) baseado em senha no Painel de Acesso, a extensão do Painel de Acesso deve estar instalada no navegador do usuário. Essa extensão é baixada automaticamente quando um usuário seleciona um aplicativo configurado para SSO baseado em senha.

Para SSO baseado em senha, os navegadores do usuário final podem ser:

-   Internet Explorer 8, 9, 10, 11 - no Windows 7 ou posterior

-   Chrome – No Windows 7 ou posterior e no MacOS X ou posterior

-   Firefox 26.0 ou posterior, no Windows XP SP2 ou posterior e no Mac OS X 10.6 ou posterior

**Observação**: a extensão de SSO baseada em senha será disponibilizada para o Edge no Windows 10 quando as extensões de navegador tiverem suporte no Edge.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Como instalar a extensão do Navegador do Painel de Acesso

Para instalar a extensão do Navegador do Painel de Acesso, siga as etapas a seguir:

1.  Abra o [Painel de Acesso](https://myapps.microsoft.com) em um dos navegadores compatíveis entre como um **usuário** no Azure AD.

2.  Clique em **aplicativo de SSO de senha** no Painel de Acesso.

3.  No prompt solicitando a instalação do software, selecione **Instalar Agora**.

4.  Com base no seu navegador, você será direcionado para o link de download. **Adicione** a extensão ao seu navegador.

5.  Se o navegador solicitar, selecione como **Habilitar** ou **Permitir** a extensão.

6.  Quando estiver instalado, **reinicie** a sessão do navegador.

7.  Entrar no Painel de Acesso e ver se é possível **iniciar** os aplicativos de SSO de senha

Também é possível baixar a extensão para Chrome e Firefox diretamente pelos links abaixo:

-   [Extensão do Painel de Acesso do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão do Painel de Acesso do Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Configurar uma política de grupo para o Internet Explorer

É possível configurar uma política de grupo que permita instalar remotamente a extensão do Painel de Acesso para o Internet Explorer nos computadores dos usuários.

Os pré-requisitos incluem:

-   Você configurou os [Serviços de Domínio do Active Directory](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)e os computadores dos usuários ingressaram no domínio.

-   Você deve ter a permissão "Editar configurações" para editar o GPO (Objeto de Política de Grupo). Por padrão, os membros dos grupos de segurança a seguir têm esta permissão: Administradores de Domínio, Administradores de Empresa e Proprietários Criadores de Política de Grupo. [Saiba mais](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Siga o tutorial [Como Implantar a Extensão do Painel de Acesso para o Internet Explorer usando Política de Grupo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy) para obter instruções passo a passo sobre como configurar política de grupo e implantá-la nos usuários.

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>Solucionar problemas do Painel de Acesso no Internet Explorer

Siga o guia [Solucionar problemas da Extensão do Painel de Acesso para o Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-Troubleshoot) para acessar uma ferramenta de diagnóstico e as instruções passo a passo sobre como configurar a extensão para o IE.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Se essas etapas de solução de problemas não resolverem o problema

Abra um tíquete de suporte com as informações a seguir, se estiverem disponíveis:

-   ID de erro de correlação

-   UPN (endereço de email de usuário)

-   TenantID

-   Tipo de navegador

-   Fuso horário e tempo/cronograma durante o erro

-   Rastreamento do Fiddler

## <a name="next-steps"></a>Próximas etapas
[O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

