---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 03/20/2019
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: ace6c36f1bc4582b3210c049a4ff8cb1f770bf88
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203452"
---
# <a name="call-the-microsoft-graph-api-from-an-ios-application"></a>Chamar a API do Microsoft Graph em um aplicativo iOS

Este guia mostra como um aplicativo iOS nativo (Swift) pode chamar as APIs que requerem tokens de acesso do endpoint do Microsoft Azure Active Directory (Azure AD) v2.0. O guia explica como obter tokens de acesso e usá-los em chamadas para a API do Microsoft Graph e outras APIs.

Depois de concluir os exercícios neste guia, o aplicativo pode chamar uma API protegida de qualquer empresa ou organização que tem o Azure AD. Seu aplicativo pode fazer chamadas de API protegidas usando contas pessoais, como outlook.com, live.com e outros, bem como contas corporativas ou de estudante.

## <a name="prerequisites"></a>Pré-requisitos

- É necessário XCode versão 10.x para o exemplo criado neste guia. Você pode fazer o download do XCode na [URL de download do XCode](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "do site iTunes").
- É necessário o gerenciador de dependência [Carthage](https://github.com/Carthage/Carthage) para o pacote de gerenciamento.

## <a name="how-this-guide-works"></a>Como funciona este guia

![Mostra como o aplicativo de exemplo gerado por esse funciona tutoriais](media/active-directory-develop-guidedsetup-ios-introduction/iosintro-updated.png)

Neste guia, o aplicativo de exemplo permite que um aplicativo iOS consulte a API do Microsoft Graph ou uma API Web que aceita tokens do endpoint do Azure AD v2.0. Para esse cenário, um token é adicionado às solicitações HTTP usando o cabeçalho de **Autorização**. A aquisição e a renovação de tokens são manipuladas pela MSAL (Biblioteca de Autenticação da Microsoft).

### <a name="handle-token-acquisition-for-access-to-protected-web-apis"></a>Manipulando a aquisição de token para acessar APIs de web protegidas

Depois que o usuário é autenticado, o aplicativo de exemplo recebe um token. O token é usado para consultar a API do Microsoft Graph ou uma API da web que está protegida pelo endpoint do Azure AD v2.0.

APIs, como o Microsoft Graph, exigem um token de acesso para permitir o acesso a recursos específicos. Os tokens são necessários para ler o perfil do usuário, acessar o calendário do usuário, enviar um email e assim por diante. O aplicativo pode solicitar um token de acesso que usa a MSAL e escopos de API específicos. O token de acesso é adicionado ao cabeçalho de **Autorização** HTTP de cada chamada que é feita no recurso protegido.

A MSAL gerencia o armazenamento em cache e a atualização de tokens de acesso para você, de forma que o aplicativo não precise fazer isso.

## <a name="libraries"></a>Bibliotecas

Este guia usa a seguinte biblioteca:

|Biblioteca|DESCRIÇÃO|
|---|---|
|[MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|Versão prévia da Biblioteca de Autenticação da Microsoft para iOS|
