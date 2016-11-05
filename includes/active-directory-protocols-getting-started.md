---
title: Visão geral do protocolo .NET do Azure AD | Microsoft Docs
description: Como usar mensagens HTTP para autorizar o acesso a aplicativos Web e a APIs da Web em seu locatário usando o Azure AD.
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/21/2016
ms.author: priyamo

---
<!--TODO: Introduction -->

## Registrar seu aplicativo no seu locatário do AD
Primeiro, você precisará registrar seu aplicativo em seu locatário do Active Directory. Isso dará uma ID de cliente para seu aplicativo e permitirá que ele receba tokens.

* Entre no Portal de Gerenciamento do Azure.
* No painel de navegação à esquerda, clique em **Active Directory**.
* Selecione um locatário no qual registrar o aplicativo.
* Clique na guia **Aplicativos** e clique em **Adicionar** na gaveta inferior.
* Siga os avisos e crie um novo aplicativo. Para este tutorial, não importa se é um aplicativo Web ou um aplicativo nativo, mas se você quiser exemplos específicos para aplicativos Web ou para aplicativos nativos, confira nossos guias de início rápido [aqui](../articles/active-directory/active-directory-developers-guide.md).
* Para os Aplicativos Web, forneça a **URL de Entrada**, que é a URL base do seu aplicativo, onde os usuários podem entrar como `http://localhost:12345`. O **URI da ID do aplicativo** é um identificador exclusivo para seu aplicativo. A convenção é usar `https://<tenant-domain>/<app-name>`, por exemplo, `https://contoso.onmicrosoft.com/my-first-aad-app`
* Para os Aplicativos Nativos, forneça um **URI de redirecionamento**, que será usado pelo Azure AD para retornar respostas de token. Insira um valor específico para seu aplicativo, por exemplo, `http://MyFirstAADApp`.
* Depois de concluir o registro, o AAD atribuirá a seu aplicativo um identificador de cliente único. Você precisará desse valor nas próximas seções, então copie-o da guia **Configurar** do seu aplicativo.

<!---HONumber=AcomDC_0601_2016-->