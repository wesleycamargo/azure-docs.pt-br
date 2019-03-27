---
title: Configurações de cookie do Proxy de Aplicativo – Azure Active Directory | Microsoft Docs
description: O Azure AD (Azure Active Directory) tem cookies de sessão e de acesso para acessar aplicativos locais por meio do Proxy de Aplicativo. Neste artigo, você descobrirá como usar e definir as configurações de cookie.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: celested
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06fd83ee815e9e207c1fa5a1c6767280122c4d0c
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58482684"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Configurações de cookie para acessar aplicativos locais no Azure Active Directory

O Azure AD (Azure Active Directory) tem cookies de sessão e de acesso para acessar aplicativos locais por meio do Proxy de Aplicativo. Saiba como usar as configurações de cookie do Proxy de Aplicativo. 

## <a name="what-are-the-cookie-settings"></a>O que são as configurações de cookie?

O [Proxy de Aplicativo](application-proxy.md) utiliza as configurações de cookie de sessão e de acesso a seguir.

| Configuração de cookies | Padrão | DESCRIÇÃO | Recomendações |
| -------------- | ------- | ----------- | --------------- |
| Usar Cookie Somente HTTP | **Não** | **Sim** permite que o Proxy de Aplicativo inclua o sinalizador HTTPOnly nos cabeçalhos de resposta HTTP. Esse sinalizador oferece benefícios de segurança adicionais, por exemplo, impede que o script CSS (do lado do cliente) copie ou modifique os cookies.<br></br><br></br>Antes de darmos suporte à configuração Somente HTTP, o Proxy de Aplicativo criptografava e transmitia cookies por um canal SSL seguro SSL para impedir modificações. | Use **Sim** para ter os benefícios de segurança adicionais.<br></br><br></br>Use **Não** para clientes ou agentes de usuário que exijam acesso ao cookie de sessão. Por exemplo, use **Não** para um cliente RDP ou MTSC que se conecta a um servidor de Gateway de Área de Trabalho Remota por meio do Proxy de Aplicativo.|
| Usar um Cookie Seguro | **Não** | **Sim** permite que o Proxy de Aplicativo inclua o sinalizador Secure nos cabeçalhos de resposta HTTP. Os cookies seguros melhoram a segurança ao transmitir cookies em um canal TLS seguro, como HTTPS. Isso impede que os cookies sejam observados por partes não autorizadas devido à transmissão do cookie em texto não criptografado. | Use **Sim** para ter os benefícios de segurança adicionais.|
| Usar cookie persistente | **Não** | **Sim** permite que o Proxy de Aplicativo defina seus cookies de acesso para não expirarem quando o navegador da Web for fechado. A persistência dura até que o token de acesso expire ou até que o usuário exclua manualmente os cookies persistentes. | Use **Não** por causa do risco de segurança associado à autenticação contínua dos usuários.<br></br><br></br>Sugerimos usar **Sim** apenas para aplicativos mais antigos que não podem compartilhar cookies entre processos. É melhor atualizar seu aplicativo para lidar com o compartilhamento de cookies entre processos em vez de usar cookies persistentes. Por exemplo, você pode precisar de cookies persistentes para permitir que um usuário abra documentos do Office no modo de exibição de gerenciador em um site do SharePoint. Sem cookies persistentes, essa operação poderá falhar se os cookies de acesso não forem compartilhados entre o navegador, o processo do gerenciador e o processo do Office. |

## <a name="set-the-cookie-settings---azure-portal"></a>Definir as configurações de cookie – portal do Azure
Para definir as configurações de cookie usando o portal do Azure:

1. Entre no [Portal do Azure](https://portal.azure.com). 
2. Navegue até **Azure Active Directory** > **Aplicativos Empresariais** > **Todos os Aplicativos**.
3. Selecione o aplicativo para o qual você deseja habilitar a configuração de um cookie.
4. Clique em **Proxy de Aplicativo**.
5. Em **Configurações Adicionais**, defina a configuração do cookie como **Sim** ou **Não**.
6. Clique em **Salvar** para aplicar suas alterações. 

## <a name="view-current-cookie-settings---powershell"></a>Exibir configurações atuais de cookie - PowerShell

Para ver as configurações atuais de cookie para o aplicativo, use este comando do PowerShell:  

```powershell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## <a name="set-cookie-settings---powershell"></a>Definir configurações de cookie - PowerShell

Nos comandos do PowerShell a seguir, ```<ObjectId>``` é o ObjectId do aplicativo. 

**Cookie HTTP-Only** 

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**Cookie seguro**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**Cookies persistentes**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```
