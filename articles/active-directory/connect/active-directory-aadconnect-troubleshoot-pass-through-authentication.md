---
title: "Azure AD Connect: solução de problemas de autenticação de passagem | Microsoft Docs"
description: "Este artigo descreve como solucionar problemas de Autenticação de Passagem do Azure AD (Azure Active Directory)."
services: active-directory
keywords: "Solução de problemas de Autenticação de Passagem do Azure AD Connect, instalar o Active Directory, componentes necessários do Azure AD, SSO, Logon Único"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: aceba2cfeac03eae78e89ef46926b2a388663f09
ms.contentlocale: pt-br
ms.lasthandoff: 05/12/2017

---

# <a name="how-to-troubleshoot-azure-active-directory-pass-through-authentication"></a>Como solucionar problemas de Autenticação de Passagem do Azure Active Directory

Este artigo ajudará a localizar informações de solução de problemas comuns durante a instalação, o registro ou a desinstalação dos conectores de Autenticação de Passagem (por meio do Azure AD Connect ou de modo autônomos). E durante a habilitação e a operação do recurso de Autenticação de Passagem do Azure AD (Azure Active Directory) no seu locatário.

## <a name="issues-during-installation-of-connectors-either-via-azure-ad-connect-or-standalone"></a>Problemas durante a instalação dos conectores (sejam por meio do Azure AD Connect ou autônomos)

### <a name="an-azure-ad-application-proxy-connector-already-exists"></a>Já existe um conector de Proxy de Aplicativo do Azure AD

Um conector de Autenticação de Passagem não pode ser instalado no mesmo servidor que um conector de [Proxy de Aplicativo do Azure AD](../../active-directory/active-directory-application-proxy-get-started.md). Você precisará instalar o conector de Autenticação de Passagem em um servidor separado.

### <a name="an-unexpected-error-occured"></a>Erro inesperado

[Colete logs do conector](#collecting-pass-through-authentication-connector-logs) do servidor e contate o Suporte da Microsoft com o seu problema.

## <a name="issues-during-registration-of-connectors"></a>Problemas durante o registro de conectores

### <a name="registration-of-the-connecter-failed-due-to-blocked-ports"></a>O registro do conector falhou devido a portas bloqueadas

Certifique-se de que o servidor no qual o conector foi instalado possa se comunicar com as URLs e portas de nosso serviço listadas [aqui](active-directory-aadconnect-pass-through-authentication.md#prerequisites).

### <a name="registration-of-the-connector-failed-due-to-token-or-account-authorization-errors"></a>Falha no registro do conector devido a erros de autorização de token ou conta

Use uma conta de Administrador Global somente de nuvem para todas as operações de registro e instalação de conector do Azure AD Connect ou autônomo. Há um problema conhecido com contas de Administrador Global habilitadas para MFA; desative a MFA temporariamente como uma solução alternativa (somente para concluir as operações).

### <a name="an-unexpected-error-occurred"></a>Erro inesperado

[Colete logs do conector](#collecting-pass-through-authentication-connector-logs) do servidor e contate o Suporte da Microsoft com o seu problema.

## <a name="issues-during-uninstallation-of-connectors"></a>Problemas durante a desinstalação de conectores

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Mensagem de aviso ao desinstalar o Azure AD Connect

Se você tiver a Autenticação de Passagem habilitada em seu locatário e tentar desinstalar o Azure AD Connect, será mostrada a seguinte mensagem de aviso: "Os usuários não poderão entrar no Azure AD, a menos que você tenha outros agentes de Autenticação de Passagem instalados em outros servidores.".

Você precisa ter uma configuração de [alta disponibilidade](active-directory-aadconnect-pass-through-authentication.md) em vigor antes de desinstalar o Azure AD Connect para evitar interrupção na entrada de usuários.

## <a name="issues-with-enabling-the-pass-through-authentication-feature"></a>Problemas com a habilitação do recurso de Autenticação de Passagem

### <a name="the-enabling-of-the-feature-failed-because-there-were-no-connectors-available"></a>A habilitação da funcionalidade falhou porque não havia nenhum conector disponível

Você precisa ter pelo menos um conector ativo para habilitar a Autenticação de Passagem no seu locatário. Você pode instalar um conector instalando o Azure AD Connect ou um conector autônomo.

### <a name="the-enabling-of-the-feature-failed-due-to-blocked-ports"></a>A habilitação da funcionalidade falhou devido a portas bloqueadas

Certifique-se de que o servidor no qual o Azure AD Connect está instalado possa se comunicar com as URLs e portas de nosso serviço listadas [aqui](active-directory-aadconnect-pass-through-authentication.md#prerequisites).

### <a name="the-enabling-of-the-feature-failed-due-to-token-or-account-authorization-errors"></a>Falha na habilitação do registro devido a erros de autorização de token ou conta

Use uma conta de Administrador Global somente de nuvem ao habilitar o recurso. Há um problema conhecido com contas de Administrador Global habilitadas para MFA (autenticação multifator); desative a MFA temporariamente como uma solução alternativa (somente para concluir as operações).

## <a name="issues-while-operating-the-pass-through-authentication-feature"></a>Problemas ao operar o recurso de Autenticação de Passagem

### <a name="user-facing-sign-in-errors"></a>Erros de entrada voltada para o usuário

A funcionalidade relata os erros voltados para o usuário a seguir na tela de entrada do Azure AD. Elas são detalhadas abaixo junto com as respectivas etapas de resolução apropriada.

|Erro|Descrição|Resolução
| --- | --- | ---
|AADSTS80001|Não é possível conectar ao Active Directory|Verifique se os servidores do conector são membros da mesma floresta do AD que os usuários cujas senhas precisam ser validadas e se são capazes de se conectar ao Active Directory.  
|AADSTS8002|Um tempo limite ao estabelecer conexão com o Active Directory|Verifique se o Active Directory está disponível e respondendo a solicitações dos conectores.
|AADSTS80004|O nome de usuário passado para o conector não era válido|Verifique se o usuário está tentando fazer logon com o nome de usuário correto.
|AADSTS80005|Validação encontrou WebException imprevisível|Esse é provavelmente um problema temporário. Tente novamente a solicitação. Caso a falha persista, contate o Suporte da Microsoft.
|AADSTS80007|Erro na comunicação com o Active Directory|Confira os logs de conector para obter mais informações e verifique se o Active Directory está funcionando conforme o esperado.

## <a name="collecting-pass-through-authentication-connector-logs"></a>Coletar logs do conector de Autenticação de Passagem

Dependendo do tipo de problema você tiver, será preciso examinar locais diferentes em busca de logs do conector de Autenticação de Passagem.

### <a name="connector-event-logs"></a>Logs de eventos do conector

Para erros relacionados ao conector, abra o aplicativo Visualizador de Eventos e verifique em **Application and Service Logs\Microsoft\AadApplicationProxy\Connector\Admin**.

Para análises detalhadas e logs de depuração, você pode habilitar o log de "Sessão". Não execute o conector com esse log habilitado durante operações normais; use somente para solução de problemas. Observe que o conteúdo do log é visível apenas depois que o log está desabilitado novamente.

### <a name="detailed-trace-logs"></a>Logs de rastreamento detalhados

Para solucionar problemas de falhas de conexão do usuário, procure logs de rastreamento em **C:\Programdata\Microsoft\Microsoft AAD Application Proxy Connector\Trace**. Esses logs incluem os motivos pelos quais um usuário específico falha ao entrar usando o recurso de Autenticação de Passagem. Abaixo está um exemplo de entrada de log:

```
    ApplicationProxyConnectorService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

Você pode obter detalhes descritivos do erro ('1328' no exemplo acima) abrindo o prompt de comando e executando o comando a seguir. Observação: Você precisará substituir '1328' pelo número real do erro que você vê em seus logs.

`Net helpmsg 1328`

O resultado deve ser semelhante a este:

![Autenticação de Passagem](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>Logs do controlador de domínio

Se o log de auditoria estiver habilitado, será possível encontrar informações adicionais no logs de segurança dos controladores de domínio. Uma maneira simples de consultar solicitações de entrada enviadas pelos conectores de Autenticação de Passagem é a seguinte:

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe')]]</Select>
    </Query>
    </QueryList>
```

