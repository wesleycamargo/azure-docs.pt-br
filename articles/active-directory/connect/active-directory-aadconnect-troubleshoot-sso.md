---
title: "Azure Active Directory Connect: solucionar problemas do Logon Único Contínuo | Microsoft Docs"
description: "Este tópico descreve como solucionar problemas do Logon Único Contínuo do Azure Active Directory"
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
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: b4efb457a58d8b54c9ebb126a8d84fdef01b3847
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2017
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Solucionar problemas do Logon Único Contínuo do Azure Active Directory

Este artigo ajuda você a localizar informações de solução de problemas comuns relacionados ao SSO Contínuo (Logon Único Contínuo) do Azure AD (Azure Active Directory).

## <a name="known-problems"></a>Problemas conhecidos

- Em alguns casos, habilitar o SSO contínuo pode levar até 30 minutos.
- O suporte ao navegador Edge não está disponível.
- A inicialização de clientes do Office, especialmente em situações de computador compartilhado, gera prompts de entrada adicionais para usuários. Os usuários devem inserir seus nomes de usuário com frequência, mas não suas senhas.
- Se o SSO Contínuo for bem-sucedido, o usuário não terá a oportunidade de selecionar **Manter-me conectado**. Devido a esse comportamento, os cenários de mapeamento do SharePoint e do OneDrive não funcionam.
- O SSO Contínuo não funciona no modo de navegação particular no Firefox.
- O SSO contínuo não funciona no Internet Explorer quando o modo de Proteção Avançada está ativado.
- O SSO contínuo não funciona em navegadores de dispositivos móveis no iOS e no Android.
- Se você estiver sincronizando 30 ou mais florestas do Active Directory, não será possível habilitar o SSO Contínuo usando o Azure AD Connect. Como alternativa, você poderá [habilitar manualmente](#manual-reset-of-azure-ad-seamless-sso) o recurso em seu locatário.
- Adicionar URLs do serviço Azure AD (https://autologon.microsoftazuread-sso.com, https://aadg.windows.net.nsatc.net) à zona Sites confiáveis em vez da zona Intranet local *bloqueia a entrada dos usuários*.

## <a name="check-the-status-of-the-feature"></a>Verificar o status do recurso

Verifique se o recurso SSO ainda está **Habilitado** em seu locatário. Você pode verificar o status acessando o painel **Azure AD Connect** no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com/).

![Centro de administração do Azure Active Directory: painel do Azure AD Connect](./media/active-directory-aadconnect-sso/sso10.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Motivos de falha de conexão no centro de administração do Azure Active Directory (é necessário uma licença Premium)

Se o locatário tiver uma licença do Azure AD Premium associada a ele, você também poderá analisar o [relatório de atividade de entrada](../active-directory-reporting-activity-sign-ins.md) no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com/).

![Centro de administração do Azure Active Directory: relatório Entradas](./media/active-directory-aadconnect-sso/sso9.png)

Navegue até **Azure Active Directory** > **Entradas** no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com/) e selecione uma atividade de entrada de um usuário específico. Procure o campo **CÓDIGO DE ERRO DE LOGON**. Faça o mapeamento do valor desse campo até um motivo da falha e uma resolução usando a tabela a seguir:

|Código de erro de logon|Motivo da falha no logon|Resolução
| --- | --- | ---
| 81001 | O tíquete Kerberos do usuário é muito grande. | Reduza as associações de grupo do usuário e tente novamente.
| 81002 | Não é possível validar o tíquete Kerberos do usuário. | Veja a [Lista de verificação de solução de problemas](#troubleshooting-checklist).
| 81003 | Não é possível validar o tíquete Kerberos do usuário. | Veja a [Lista de verificação de solução de problemas](#troubleshooting-checklist).
| 81004 | Falha na tentativa de autenticação Kerberos. | Veja a [Lista de verificação de solução de problemas](#troubleshooting-checklist).
| 81008 | Não é possível validar o tíquete Kerberos do usuário. | Veja a [Lista de verificação de solução de problemas](#troubleshooting-checklist).
| 81009 | Não é possível validar o tíquete Kerberos do usuário. | Veja a [Lista de verificação de solução de problemas](#troubleshooting-checklist).
| 81010 | O SSO Contínuo falhou porque o tíquete Kerberos do usuário expirou ou é inválido. | O usuário precisa entrar em um dispositivo ingressado no domínio dentro da rede corporativa.
| 81011 | Não foi possível localizar o objeto de usuário com base nas informações no tíquete Kerberos do usuário. | Use o Azure AD Connect para sincronizar as informações do usuário no Azure AD.
| 81012 | O usuário que está tentando entrar no Azure AD é diferente do usuário conectado no dispositivo. | O usuário precisa entrar em um dispositivo diferente.
| 81013 | Não foi possível localizar o objeto de usuário com base nas informações no tíquete Kerberos do usuário. |Use o Azure AD Connect para sincronizar as informações do usuário no Azure AD. 

## <a name="troubleshooting-checklist"></a>Lista de verificação de solução de problemas

Use a lista de verificação a seguir para solucionar problemas de SSO Contínuo:

- Verifique se o recurso de SSO Contínuo está habilitado no Azure AD Connect. Se você não puder habilitar o recurso (por exemplo, devido a uma porta bloqueada), verifique se você cumpriu com todos os [pré-requisitos](active-directory-aadconnect-sso-quick-start.md#step-1-check-the-prerequisites).
- Certifique-se de que ambas as URLs do Azure AD (https://autologon.microsoftazuread-sso.com e https://aadg.windows.net.nsatc.net) façam parte das configurações da zona de Intranet do usuário.
- Certifique-se de que o dispositivo corporativo tenha ingressado no domínio do Active Directory.
- Certifique-se de que o usuário esteja conectado ao dispositivo por meio de uma conta de domínio do Active Directory.
- Verifique se a conta do usuário é de uma floresta do Active Directory na qual o SSO Contínuo foi configurado.
- Certifique-se de que o dispositivo esteja conectado à rede corporativa.
- Certifique-se de que a hora do dispositivo esteja sincronizada com a hora do Active Directory e dos Controladores de Domínio, e que tenham cinco minutos ou menos de diferença.
- Liste os tíquetes Kerberos existentes no dispositivo usando o comando `klist` em um prompt de comando. Certifique-se de que os tíquetes emitidos para a conta do computador `AZUREADSSOACCT` estejam presentes. Os tíquetes Kerberos dos usuários são normalmente válidos durante 12 horas. Você pode ter configurações diferentes no Active Directory.
- Limpe os tíquetes Kerberos existentes do dispositivo usando o comando `klist purge` e tente novamente.
- Para determinar se há problemas relacionados a JavaScript, examine os logs do console do navegador (em **Ferramentas do Desenvolvedor**).
- Examine os [logs do controlador de domínio](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Logs do controlador de domínio

Se a auditoria de êxito estiver habilitada em seu controlador de domínio, sempre que um usuário entrar usando SSO Contínuo, uma entrada de segurança será registrada no log de eventos. Encontre esses eventos de segurança usando a consulta a seguir. (Procure o evento **4769** associado à conta de computador **AzureADSSOAcc$**.)

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>Redefinição manual do recurso

Se a solução de problemas não ajudar, você poderá redefinir manualmente o recurso em seu locatário. Execute estas etapas no servidor local em que você está executando o Azure AD Connect.

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>Etapa 1: importar o módulo do PowerShell de SSO Contínuo

1. Baixe e instale o [Assistente de Conexão do Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Baixe e instale o [Módulo do Azure Active Directory de 64 bits para Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Navegue até a pasta `%programfiles%\Microsoft Azure Active Directory Connect`.
4. Importe o módulo do PowerShell de SSO Contínuo usando este comando: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Etapa 2: Obter a lista de florestas do Active Directory em que o SSO Contínuo foi habilitado

1. Execute o PowerShell como administrador. No PowerShell, chame `New-AzureADSSOAuthenticationContext`. Quando solicitado, insira as suas credenciais de Administrador global do locatário.
2. Chame `Get-AzureADSSOStatus`. Esse comando fornece a lista de florestas do Active Directory (veja a lista “Domínios”) em que esse recurso foi habilitado.

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>Etapa 3: Desabilitar o SSO Contínuo para cada floresta do Active Directory onde você configurou o recurso

1. Chame `$creds = Get-Credential`. Quando solicitado, insira as credenciais de administrador de domínio da floresta do Active Directory pretendida.
2. Chame `Disable-AzureADSSOForest -OnPremCredentials $creds`. Este comando remove a conta do computador `AZUREADSSOACCT` do controlador de domínio local dessa floresta do Active Directory específica.
3. Repita as etapas anteriores para cada floresta do Active Directory onde você configurou o recurso.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Etapa 4: Habilitar o SSO Contínuo para cada floresta do Active Directory

1. Chame `Enable-AzureADSSOForest`. Quando solicitado, insira as credenciais de administrador de domínio da floresta do Active Directory pretendida.
2. Repita as etapas anteriores para cada floresta do Active Directory onde você configurou o recurso.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Etapa 5. Habilitar o recurso em seu locatário

Para ativar o recurso em seu locatário, chame `Enable-AzureADSSO` e digite **true** no prompt `Enable:`.
