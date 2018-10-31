---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 9bc8f30d2bbf6a084ad680306da9b1e330d488e3
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988303"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>Conectar usuários e chamar o Microsoft Graph em um aplicativo Android

Neste tutorial, você aprenderá como criar um aplicativo Android e integrá-lo à plataforma de identidade da Microsoft. Especificamente, este aplicativo conectará um usuário, obterá um token de acesso para chamar a API do Microsoft Graph e fará uma solicitação básica para a API do Microsoft Graph.  

Após concluir este guia, seu aplicativo aceitará conexões de contas Microsoft pessoais (incluindo outlook.com, live.com e outras) e contas corporativas ou de estudante de qualquer empresa ou organização que utilize o Azure Active Directory.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Como o aplicativo de exemplo gerado por este guia funciona

![Como funciona esta amostra](media/active-directory-develop-guidedsetup-android-intro/android-intro.png)

O aplicativo nesse exemplo conectará usuários e obterá dados em seu nome.  Esses dados serão acessados por meio de uma API remota (API do Microsoft Graph neste caso) que requer autorização e também é protegida pela plataforma de identidade da Microsoft.

Mais especificamente:

* Seu aplicativo iniciará uma página da Web para conectar o usuário.
* Será emitido um token de acesso para seu aplicativo para a API do Microsoft Graph.
* O token de acesso será incluído na solicitação HTTP para API Web.
* Processe a resposta do Microsoft Graph.

Este exemplo usa a biblioteca de autenticação da Microsoft para Android (MSAL) para estar coordenado e ajudando com a autenticação. A MSAL automaticamente renovará tokens, fornecerá o SSO entre outros aplicativos no dispositivo, ajudará a gerenciar as contas e lidará da maioria dos casos de acesso condicional.

## <a name="prerequisites"></a>Pré-requisitos

* Esta configuração guiada usa o Android Studio 3.0.
* O Android 21 ou posterior é necessário (recomendável o 25+).
* É necessário o Google Chrome ou um navegador da Web que usa Guias Personalizadas para esta versão da MSAL para Android.

## <a name="library"></a>Biblioteca

Este guia usa a seguinte biblioteca de autenticação:

|Biblioteca|DESCRIÇÃO|
|---|---|
|[com.microsoft.identity.client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|MSAL (Biblioteca de Autenticação da Microsoft)|
