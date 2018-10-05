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
ms.date: 09/13/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: a1cd25012461ae8bb445dcb1de8fe5be49e04760
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47060533"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>Conectar usuários e chamar o Microsoft Graph em um aplicativo Android

Neste tutorial, você aprenderá como criar um aplicativo Android e integrá-lo à plataforma de identidade da Microsoft. Especificamente, este aplicativo conectará um usuário, obterá um token de acesso para chamar a API do Microsoft Graph e fará uma solicitação básica para a API do Microsoft Graph.  

Após concluir este guia, seu aplicativo aceitará conexões de contas Microsoft pessoais (incluindo outlook.com, live.com e outras) e contas corporativas ou de estudante de qualquer empresa ou organização que utilize o Azure Active Directory. 

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Como o aplicativo de exemplo gerado por este guia funciona
![Como funciona esta amostra](media/active-directory-develop-guidedsetup-android-intro/android-intro.png)

O aplicativo nesse exemplo conectará usuários e obterá dados em seu nome.  Esses dados serão acessados por meio de uma API remota (API do Microsoft Graph neste caso) que requer autorização e também é protegida pela plataforma de identidade da Microsoft. 

Mais especificamente, 
* Seu aplicativo iniciará uma página da Web para conectar o usuário.
* Será emitido um token de acesso para seu aplicativo para a API do Microsoft Graph.
* O token de acesso será incluído na solicitação HTTP para API Web.
* Processe a resposta do Microsoft Graph. 

Este exemplo usa a biblioteca de autenticação da Microsoft para Android (MSAL) para estar coordenado e ajudando com a autenticação. A MSAL automaticamente renovará tokens, fornecerá o SSO entre outros aplicativos no dispositivo, ajudará a gerenciar as contas e lidará da maioria dos casos de acesso condicional. 

## <a name="prerequisites"></a>Pré-requisitos
* Esta instalação guiada usa o Android Studio 3.0. 
* O Android 21 ou posterior é necessário (recomendável o 25+).
* É necessário o Google Chrome ou um navegador da Web que usa Guias Personalizadas para esta versão da MSAL para Android.

## <a name="library"></a>Biblioteca

Este guia usa a seguinte biblioteca de autenticação:

|Biblioteca|DESCRIÇÃO|
|---|---|
|[com.microsoft.identity.client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|MSAL (Biblioteca de Autenticação da Microsoft)|
