---
title: 'Azure Active Directory B2C: Chamar uma API da Web de um aplicativo Android | Microsoft Docs'
description: "Este artigo mostra como criar um aplicativo de &quot;lista de tarefas pendentes&quot; do Android que chama uma API Web do Node.js usando tokens de portador OAuth 2.0. Tanto o aplicativo do Android quanto a API Web usam o Azure Active Directory B2C para gerenciar identidades de usuário e autenticar usuários."
services: active-directory-b2c
documentationcenter: android
author: xerners
manager: mbaldwin
editor: 
ms.assetid: d00947c3-dcaa-4cb3-8c2e-d94e0746d8b2
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 01/31/2017
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: 3f89a0e06ea4596d38f7f7b5e52cea54c5e30780
ms.openlocfilehash: bb94ab8d794d1e836df57e1bc42df42825f2668d


---
# <a name="azure-ad-b2c-call-a-web-api-from-an-android-application"></a>Azure AD B2C: chamar uma API Web de um aplicativo do Android
> [!WARNING]
> Publicamos um exemplo de código atualizado para Android que pode ser encontrado [aqui](https://github.com/Azure-Samples/active-directory-b2c-android-native-nodejs-webapi).  Esse exemplo usa uma biblioteca de terceiros que foi testada com relação à compatibilidade em cenários básicos com o Azure AD B2C.  Essa biblioteca utiliza exibições da Web incorporadas em vez do navegador do sistema.  O Google [anunciou](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html) que a partir de 20 de abril de 2017 não oferecerá mais suporte à entrada na Conta do Google com exibições da Web incorporadas. A partir dessa data, quem quiser dar suporte a Contas do Google precisará atualizar as bibliotecas.  

>A Microsoft não fornece correções para bibliotecas de terceiros e não as analisou. Os problemas e solicitações de recursos devem ser direcionados ao projeto de software livre da biblioteca. Para saber mais, confira [este artigo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>




<!--HONumber=Feb17_HO1-->


