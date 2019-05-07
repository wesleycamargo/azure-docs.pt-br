---
title: Considerações sobre a plataforma do Windows universal (biblioteca de autenticação do Microsoft para .NET) | Azure
description: Saiba mais sobre as considerações específicas ao usar a plataforma Universal do Windows com a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb8076df5482c91942349e0a052794f3fe945a5f
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076504"
---
# <a name="universal-windows-platform-specific-considerations-with-msalnet"></a>Considerações de específico da plataforma Windows universais com MSAL.NET
No Xamarin iOS, há várias considerações que você deve levar em conta ao usar MSAL.NET.

## <a name="the-usecorporatenetwork-property"></a>A propriedade UseCorporateNetwork
Na plataforma do WinRT, `PublicClientApplication` tem a seguinte propriedade boolean ``UseCorporateNetwork``. Essa propriedade permite que aplicativos Win8.1 e UWP para se beneficiar da autenticação integrada do Windows (e, portanto, o SSO com o usuário conectado com o sistema operacional) se o usuário estiver conectado-in com uma conta no Azure federado locatário do AD. Isso aproveita o WAB (Web Authentication Broker). 

> [!IMPORTANT]
> Definir essa propriedade como true pressupõe que o desenvolvedor do aplicativo tiver habilitado a autenticação integrada do Windows (IWA) no aplicativo. Para isso:
> - No ``Package.appxmanifest`` para seu aplicativo UWP, no **recursos** guia, habilite os seguintes recursos:
>   - Autenticação de Empresa
>   - Redes Privadas (Cliente e Servidor)
>   - Certificado de usuário compartilhado

IWA não está habilitada por padrão, como aplicativos solicitando os recursos de autenticação corporativa ou de certificados de usuário compartilhado requer um nível mais alto de verificação para ser aceito para a Windows Store, e nem todos os desenvolvedores talvez queiram realizar maior nível de verificação. 

A implementação subjacente na plataforma UWP (WAB) não funciona corretamente em cenários empresariais em que o acesso condicional foi habilitado. O sintoma é que o usuário tenta entrar com uma saudação do Windows e é proposto para escolher um certificado, mas o certificado para o pin não foi encontrado ou o usuário escolhe-lo, mas nunca solicitado a fornecer o Pin. Uma solução alternativa é usar um método alternativo (nome de usuário/senha + telefone autenticação), mas a experiência não é bom. 

## <a name="next-steps"></a>Próximas etapas
Mais detalhes são fornecidos nos exemplos a seguir:

Amostra | Plataforma | DESCRIÇÃO 
|------ | -------- | -----------|
|[active-directory-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Um aplicativo de cliente de plataforma Universal do Windows usando msal.net, acessando o Microsoft Graph para um usuário autenticar com o ponto de extremidade do Azure AD v 2.0. <br>![Topologia](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Um aplicativo Xamarin Forms simples, mostrando como usar MSAL para autenticar a MSA e o Azure AD por meio do ponto de extremidade v2.0 AAD e acessar o Microsoft Graph com o token resultante. <br>![Topologia](media/msal-net-uwp-considerations/topology-xamarin-native.png)|