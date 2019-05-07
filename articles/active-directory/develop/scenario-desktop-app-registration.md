---
title: Aplicativo de desktop que chamadas de web APIs (registro de aplicativo) - plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo da área de trabalho que chama APIs (registro de aplicativo) de web
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5da934709274d90668d94dfea3a9c223e191d032
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076054"
---
# <a name="desktop-app-that-calls-web-apis---app-registration"></a>Aplicativo da área de trabalho que chama APIs - registro do aplicativo de web

Este artigo contém os specificities de registro de aplicativo para um aplicativo da área de trabalho.

## <a name="supported-accounts-types"></a>Tipos de contas com suporte

Os tipos de conta com suporte no aplicativo da área de trabalho dependem da experiência que você deseja light-up, e, portanto, nos fluxos você deseja usar.

### <a name="audience-for-interactive-token-acquisition"></a>Público-alvo para aquisição de token interativa

Se seu aplicativo da área de trabalho usa a autenticação interativa, você pode conectar usuários de qualquer [tipo de conta](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)

### <a name="audience-for-desktop-app-silent-flows"></a>Público-alvo para fluxos silenciosa do aplicativo da área de trabalho

- Se você pretende usar a autenticação do Windows integrada ou nome de usuário/senha, seu aplicativo precisa conectar usuários em seu próprio locatário (desenvolvedor LOB) ou no Azure Active directory que as organizações (cenário de ISV). Esses fluxos de autenticação não são compatíveis com contas pessoais da Microsoft
- Se você quiser usar o fluxo de código do dispositivo, você não pode conectar usuários com suas contas pessoais da Microsoft ainda
- Se você conectar usuários com identidades sociais, passando uma autoridade de B2C e a política, você só poderá usar a autenticação interativa e a senha do nome de usuário.

## <a name="redirect-uris"></a>URIs de redirecionamento

Novamente o redirecionamento de URIs a serem usados no aplicativo da área de trabalho dependerá do fluxo que você deseja usar.

- Se você estiver usando a autenticação interativa, você desejará usar `https://login.microsoftonline.com/common/oauth2/nativeclient`. Você alcançará essa configuração clicando na URL correspondente na **autenticação** seção para seu aplicativo
  
  > [!IMPORTANT]
  > Hoje em dia MSAL.NET usa outro URI de redirecionamento por padrão em aplicativos da área de trabalho em execução no Windows (`urn:ietf:wg:oauth:2.0:oob`). No futuro, vai querer alterar esse padrão e, portanto, é recomendável que você use `https://login.microsoftonline.com/common/oauth2/nativeclient`

- Se seu aplicativo só está usando a autenticação do Windows integrada, nome de usuário/senha ou o fluxo de código de dispositivo, você não precisará registrar um URI de redirecionamento para seu aplicativo. Na verdade, esses fluxos de fazer uma ida e volta para o ponto de extremidade de v 2.0 de plataforma de identidade de Microsoft e seu aplicativo não seja chamado novamente no qualquer URI específico. Para distingui-las a partir de um fluxo de aplicativo cliente confidencial, que não tem URIs de redirecionamento ambos (fluxo do cliente credencial usado em aplicativos daemon), você precisa expressar o que seu aplicativo é um aplicativo cliente público. Essa configuração é obtida, vá para o **autenticação** seção para seu aplicativo e, no **configurações avançadas** subseção, escolha **Sim**, para a pergunta **Trate o aplicativo como um cliente público** (na **tipo de cliente padrão** parágrafo)

  ![Permitir que o cliente público](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>Permissões de API

Aplicativos da área de trabalho chamam APIs em nome do usuário conectado. Eles precisam solicitar permissões delegadas. Eles não podem solicitar permissões de aplicativo (que só são tratados no [aplicativos daemon](scenario-daemon-overview.md))

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aplicativo de desktop - configuração de aplicativo](scenario-desktop-app-configuration.md)
