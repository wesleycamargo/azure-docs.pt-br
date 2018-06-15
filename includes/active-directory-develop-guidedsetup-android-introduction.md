---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: aa21168f1e19ccddc190379b30e1b612d96b56d5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32202658"
---
# <a name="sign-in-users-and-all-the-microsoft-graph-api-from-an-android-app"></a>Conecte usuários e toda a API do Microsoft Graph em um aplicativo do Android

Este guia demonstra como um aplicativo nativo Android pode obter um token de acesso e chamar a API do Microsoft Graph ou outras APIs que exigem tokens de acesso por meio do ponto de extremidade do Azure Active Directory v2.

Após completar este guia, seu aplicativo poderá aceitar conexões de contas pessoais (incluindo outlook.com, live.com e outras) e contas corporativas ou de estudante de qualquer empresa ou organização que utilize o Azure Active Directory. O aplicativo, em seguida, chama uma API que é protegida pelo ponto de extremidade do Azure Active Directory v2.  

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Como o aplicativo de exemplo gerado por este guia funciona
![Como funciona esta amostra](media/active-directory-develop-guidedsetup-android-intro/android-intro.png)

O aplicativo de exemplo que você criou com este guia se baseia em um cenário no qual um aplicativo Android é usado para consultar uma API Web que aceita tokens do ponto de extremidade do Azure Active Directory v2 (nesse caso, a API do Microsoft Graph). Para esse cenário, o seu aplicativo adiciona o token adquirido às solicitações HTTP por meio do cabeçalho de Autorização. A Biblioteca de Autenticação da Microsoft (MSAL) lida com a aquisição e a renovação de tokens para você.

## <a name="prerequisites"></a>pré-requisitos
* Esta Instalação Guiada se concentra no Android Studio, mas qualquer outro ambiente de desenvolvimento de aplicativos do Android também é aceitável. 
* O SDK 21 ou mais novo do Android é necessário (o recomendado é o SDK 25).
* É necessário o Google Chrome ou um navegador da Web que usa Guias Personalizadas para esta versão da MSAL para Android.

> [!NOTE]
> O Google Chrome não está incluído com o Emulador do Visual Studio para Android. Recomendamos testar esse código em um Emulador com a API 25 ou uma imagem com a API 21 ou mais recente que tenha o Google Chrome instalado.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Manipulando a aquisição de token para acessar APIs Web protegidas

Após a autenticação do usuário, o aplicativo de exemplo recebe um token de acesso que pode ser usado para consultar a API do Microsoft Graph ou uma API Web protegida pelo Azure Active Directory v2.

APIs, como o Microsoft Graph, exigem um token de acesso para permitir o acesso a recursos específicos. Por exemplo, um token de acesso é necessário para ler o perfil ou acessar o calendário de um usuário, ou enviar um email. O aplicativo pode solicitar um token de acesso usando a MSAL para acessar esses recursos especificando escopos de API. Esse token de acesso é então adicionado ao cabeçalho de Autorização HTTP de cada chamada feita no recurso protegido. 

A MSAL gerencia o armazenamento em cache e a atualização de tokens de acesso para você, de forma que o aplicativo não precise fazer isso.

## <a name="libraries"></a>Bibliotecas

Este guia usa as seguintes bibliotecas:

|Biblioteca|DESCRIÇÃO|
|---|---|
|[com.microsoft.identity.client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|MSAL (Biblioteca de Autenticação da Microsoft)|
