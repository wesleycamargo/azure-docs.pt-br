---
title: "Portas e protocolos exigidos para Identidade Híbrida- Azure | Microsoft Docs"
description: "Essa página é uma página de referência técnica para as portas que precisam estar abertas para o Azure AD Connect"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: de97b225-ae06-4afc-b2ef-a72a3643255b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: adf358a130fd20674cbf2585de93005a9e1cb3ec
ms.contentlocale: pt-br
ms.lasthandoff: 04/27/2017

---
# Portas e Protocolos Requeridos para Identidade Híbrida
<a id="hybrid-identity-required-ports-and-protocols" class="xliff"></a>
O documento a seguir é uma referência técnica sobre as portas e os protocolos para implementar uma solução de identidade híbrida. Use a ilustração a seguir e confira a tabela correspondente.

![O que é o Azure AD Connect](./media/active-directory-aadconnect-ports/required3.png)

## Tabela 1 - AD do Azure Connect e AD Local
<a id="table-1---azure-ad-connect-and-on-premises-ad" class="xliff"></a>
Esta tabela descreve as portas e protocolos que são necessários para a comunicação entre o servidor do Azure AD Connect e AD local.

| Protocolo | Portas | Descrição |
| --- | --- | --- |
| DNS |53 (TCP/UDP) |Pesquisas de DNS na floresta de destino. |
| Kerberos |88 (TCP/UDP) |Autenticação Kerberos na floresta do AD. |
| MS-RPC |135 (TCP/UDP) |Usado durante a configuração inicial do assistente do Azure AD Connect quando ele se vincula à floresta do AD e também durante a sincronização de senha. |
| LDAP |389 (TCP/UDP) |Usado para importar dados do AD. Dados são criptografados com Sinal e Selo do Kerberos. |
| LDAP/SSL |636 (TCP/UDP) |Usado para importar dados do AD. A transferência de dados é assinada e criptografada. Usado somente se você estiver usando SSL. |
| RPC |49152- 65535 (Porta RPC alta aleatória)(TCP/UDP) |Usado durante a configuração inicial do assistente do Azure AD Connect quando ele se vincula às florestas do AD e durante a sincronização de senha. Confira os artigos [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017) e [KB224196](https://support.microsoft.com/kb/224196) para saber mais. |

## Tabela 2 - AD do Azure Connect e Azure AD
<a id="table-2---azure-ad-connect-and-azure-ad" class="xliff"></a>
Esta tabela descreve as portas e protocolos que são necessários para a comunicação entre o servidor do Azure AD Connect e Azure AD.

| Protocolo | Portas | Descrição |
| --- | --- | --- |
| HTTP |80 (TCP/UDP) |Usada para baixar as CRLs (Listas de Certificados Revogados) para verificar os certificados SSL. |
| HTTPS |443(TCP/UDP) |Usado para sincronizar com o Azure AD. |

Para obter uma lista de URLs e endereços IP que você precisa abrir no firewall, confira [Intervalos de endereços IP e URLs do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## Tabela 3 – Azure AD Connect e Servidores de Federação AD FS/WAP
<a id="table-3---azure-ad-connect-and-ad-fs-federation-serverswap" class="xliff"></a>
Essa tabela descreve as portas e protocolos que são necessários para a comunicação entre o servidor do Azure AD Connect e os servidores de Federação AD FS/WAP.  

| Protocolo | Portas | Descrição |
| --- | --- | --- |
| HTTP |80 (TCP/UDP) |Usada para baixar as CRLs (Listas de Certificados Revogados) para verificar os certificados SSL. |
| HTTPS |443(TCP/UDP) |Usado para sincronizar com o Azure AD. |
| WinRM |5985 |Ouvinte do WinRM |

## Tabela 4 - Servidores de Federação e WAP
<a id="table-4---wap-and-federation-servers" class="xliff"></a>
Esta tabela descreve as portas e protocolos que são necessários para a comunicação entre os servidores de Federação e servidores WAP.

| Protocolo | Portas | Descrição |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Usado para autenticação. |

## Tabela 5 - WAP e Usuários
<a id="table-5---wap-and-users" class="xliff"></a>
Esta tabela descreve as portas e protocolos que são necessários para a comunicação entre os usuários e os servidores WAP.

| Protocolo | Portas | Descrição |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Usado para autenticação de dispositivo. |
| TCP |49443 (TCP) |Usado para autenticação de certificado. |

## Tabela 6a e 6b - Autenticação de passagem com SSO (logon único) e Sincronização de hash de senha com SSO (logon único)
<a id="table-6a--6b---pass-through-authentication-with-single-sign-on-sso-and-password-hash-sync-with-single-sign-on-sso" class="xliff"></a>
Esta tabela descreve as portas e protocolos necessários para a comunicação entre o Azure AD Connect e o Azure AD.

### Tabela 6a - Autenticação de passagem com SSO
<a id="table-6a---pass-through-authentication-with-sso" class="xliff"></a>
|Protocolo|Número da porta|Descrição
| --- | --- | ---
|HTTP|80|Habilite o tráfego HTTP de saída para a validação de segurança como o SSL. Também é necessário para o recurso de atualização automática funcionar corretamente.
|HTTPS|443| Habilite o tráfego HTTPS de saída para operações como habilitar e desabilitar o recurso, registrar conectores, baixar atualizações do conector e manipular todas as solicitações de entrada do usuário.

Além disso, o Azure AD Connect precisa ser capaz de estabelecer conexões IP diretas com os [intervalos de IP do data center do Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

### Tabela 6b - Sincronização de hash de senha com SSO
<a id="table-6b---password-hash-sync-with-sso" class="xliff"></a>

|Protocolo|Número da porta|Descrição
| --- | --- | ---
|HTTPS|443| Habilite o registro por SSO (necessário somente para o processo de registro com SSO).

Além disso, o Azure AD Connect precisa ser capaz de estabelecer conexões IP diretas com os [intervalos de IP do data center do Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653). Novamente, isso só é necessário para o processo de registro do SSO.

## Tabela 7a e 7b – Agente do Azure AD Connect Health para (AD FS/Sync) e Azure AD
<a id="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad" class="xliff"></a>
As tabelas a seguir descrevem os pontos de extremidade, portas e protocolos que são necessários para a comunicação entre agentes do Azure AD Connect Health e Azure AD

### Tabela 7a – Portas e protocolos para o agente do Azure AD Connect Health para o (AD FS/Sync) e Azure AD
<a id="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad" class="xliff"></a>
Esta tabela descreve as portas e protocolos de saída a seguir que são necessários para a comunicação entre os agentes do Azure AD Connect Health e o Azure AD.  

| Protocolo | Portas | Descrição |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Saída |
| Barramento de Serviço do Azure |5671 (TCP/UDP) |Saída |

### 7b – Pontos de extremidade de agente do Azure AD Connect Health para (AD FS/Sync) e Azure AD
<a id="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad" class="xliff"></a>
Para obter uma lista de pontos de extremidade, veja [a seção Requisitos para o agente do Azure AD Connect Health](../connect-health/active-directory-aadconnect-health-agent-install.md#requirements).


