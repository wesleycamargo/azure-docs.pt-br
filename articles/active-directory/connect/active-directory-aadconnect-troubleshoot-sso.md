---
title: "Azure AD Connect: Solucionar Problemas do Logon Único Contínuo | Microsoft Docs"
description: "Este tópico descreve como solucionar problemas do Logon Único Contínuo do Azure Active Directory (SSO Contínuo do Azure AD)."
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
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: a543be452abbbe3057a5e275612968cd52c8b7aa
ms.contentlocale: pt-br
ms.lasthandoff: 05/09/2017

---

# <a name="how-to-troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Como solucionar problemas do Logon Único Contínuo do Azure Active Directory

## <a name="known-issues"></a>Problemas conhecidos

- Se você estiver sincronizando mais de 30 florestas do AD usando o Azure AD Connect, o assistente usado para configurar o SSO Contínuo não funcionará corretamente. Como alternativa, você pode [habilitar manualmente](#manual-reset-of-azure-ad-seamless-sso) o recurso de SSO Contínuo no seu locatário.
- Adicionar URLs do serviço Azure AD (https://autologon.microsoftazuread-sso.com, https://aadg.windows.net.nsatc.net) à zona "Sites confiáveis" em vez da zona "Intranet local".

## <a name="troubleshooting-checklist"></a>Lista de verificação de solução de problemas

Use a lista de verificação a seguir para solucionar problemas de SSO contínuo do Azure AD:

1. Verifique se a funcionalidade SSO contínuo está habilitada em seu locatário na ferramenta Azure AD Connect. Se você não puder habilitar a funcionalidade (por exemplo, devido a uma porta bloqueada), certifique-se de cumprir todos os [pré-requisitos](active-directory-aadconnect-sso.md#pre-requisites). Se você ainda estiver enfrentando problemas para habilitar a funcionalidade, entre em contato com o Suporte da Microsoft.
2. Ambas as URLs de serviço https://autologon.microsoftazuread-sso.com e https://aadg.windows.net.nsatc.net são definidas como parte das configurações da zona de Intranet.
3. Certifique-se de que o computador desktop corporativo seja ingressado no domínio do AD.
4. Certifique-se de que o usuário faça logon no computador desktop usando uma conta de domínio do AD.
5. Verifique se a conta do usuário é de uma floresta do AD na qual o SSO contínuo foi configurado.
6. Certifique-se de que o computador desktop esteja conectado à rede corporativa.
7. Certifique-se de que a hora do computador desktop esteja sincronizada com a hora do Active Directory e a dos controladores de domínio e também que elas tenham 5 minutos ou menos de diferença entre si.
8. Limpe os tíquetes Kerberos existentes dos computadores desktop. Isso pode ser feito executando o comando **klist purge** em um prompt de comando. Os tíquetes Kerberos dos usuários são normalmente válidos por 12 horas; observe que você pode tê-lo configurado diferentemente no Active Directory.
9. Examine os logs do console do navegador (em "Ferramentas de Desenvolvedor)" para ajudar a determinar problemas potenciais.
10. Examine também os [logs do controlador de domínio](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Logs do controlador de domínio

Se a auditoria de êxito está habilitada no seu controlador de domínio, sempre que um usuário entra usando SSO contínuo, uma entrada de segurança (evento 4769 associado à conta de computador **AzureADSSOAcc$**) é registrada no log de eventos. Você pode encontrar esses eventos de segurança usando a consulta a seguir:

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-azure-ad-seamless-sso"></a>Redefinição manual do SSO contínuo do Azure AD

Se a solução de problemas não ajudar, use as seguintes etapas para redefinir manualmente/habilitar o recurso em seu locatário:

### <a name="1-import-the-seamless-sso-powershell-module"></a>1. Importar o módulo do PowerShell de SSO Contínuo

- Primeiro, baixe e instale o [Assistente de Conexão do Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=286152).
- Em seguida, baixe e instale o [Módulo do Azure Active Directory de 64 bits para Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
- Navegue até a pasta `%programfiles%\Microsoft Azure Active Directory Connect`.
- Importe o módulo do PowerShell de SSO Contínuo usando este comando: `Import-Module .\AzureADSSO.psd1`.

### <a name="2-get-the-list-of-ad-forests-on-which-seamless-sso-has-been-enabled"></a>2. Obter a lista de florestas do AD em que o SSO Contínuo foi habilitado

- No PowerShell, chame `New-AzureADSSOAuthenticationContext`. Isso deve fornecer a você um pop-up para inserir suas credenciais de administrador de locatários do Azure AD.
- Chame `Get-AzureADSSOStatus`. Isso fornecerá a lista de florestas do AD (consulte a lista de "Domínios") em que esse recurso foi habilitado.

### <a name="3-disable-seamless-sso-for-each-ad-forest-that-it-was-set-it-up-on"></a>3. Desabilitar o SSO Contínuo para cada floresta do AD em que ele foi configurado

- No PowerShell, chame `New-AzureADSSOAuthenticationContext`. Isso deve fornecer a você um pop-up para inserir suas credenciais de administrador de locatários do Azure AD.
- Chame `$creds = Get-Credential`. Isso deve fornecer a você um pop-up para inserir as credenciais de administrador de domínio para a floresta do AD pretendida.
- Chame `Disable-AzureADSSOForest -OnPremCredentials $creds`. Isso removerá a conta de computador AZUREADSSOACCT do controlador de domínio local e também desabilitará esse recurso para essa floresta do AD específica.
- Repita as etapas acima para cada floresta do AD em que você configurou o recurso.

### <a name="4-enable-seamless-sso-for-each-ad-forest"></a>4. Habilitar o SSO Contínuo para cada floresta do AD

- Chame `New-AzureADSSOAuthenticationContext`. Isso deve fornecer a você um pop-up para inserir suas credenciais de administrador de locatários do Azure AD.
- Chame `Enable-AzureADSSOForest`. Isso deve fornecer a você um pop-up para inserir credenciais de administrador de domínio para a floresta do AD pretendida.
- Repita as etapas acima para cada floresta do AD em que você deseja configurar o recurso.

### <a name="5-enable-seamless-sso-on-your-tenant"></a>5. Habilitar o SSO Contínuo no seu locatário

- Chame `New-AzureADSSOAuthenticationContext`. Isso deve fornecer a você um pop-up para inserir suas credenciais de administrador de locatários do Azure AD.
- Chame `Enable-AzureADSSO` e digite "true" no prompt `Enable: ` ativar o recurso em seu locatário.

