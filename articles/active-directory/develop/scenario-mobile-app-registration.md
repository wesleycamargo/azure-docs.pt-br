---
title: Aplicativo móvel que chamadas às APIs - configuração de código do aplicativo da web | Plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo móvel que chamadas de web APIs (configuração de código do aplicativo)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9eac9d1dfce79ac4ad9d49a6cfe6b7dee7f6681a
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075154"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>Aplicativo móvel que chamadas às APIs - registro de aplicativo da web

Este artigo contém as instruções de registro de aplicativo para a criação de um aplicativo móvel.

## <a name="supported-account-types"></a>Tipos de conta com suporte

Os tipos de conta com suporte em aplicativos móveis dependem dos usuários que seu aplicativo for direcionado e a experiência que você deseja habilitar.

## <a name="platform-configuration-and-redirect-uris"></a>Configuração de plataforma e URIs de redirecionamento  

Ao criar um aplicativo móvel, a etapa mais importante do registro é o URI de redirecionamento. Isso pode ser definido por meio de [configuração de plataforma na folha de autenticação](https://aka.ms/MobileAppReg).

Essa experiência permitirá que seu aplicativo para obter logon único (SSO) por meio do Microsoft Authenticator (e o Portal da empresa do Intune no Android), bem como suporte a políticas de gerenciamento de dispositivo.

Se você preferir configurar manualmente o URI de redirecionamento, você pode fazer isso por meio do manifesto do aplicativo. O formato recomendado é o seguinte:

- ***iOS***: `msauth.<BUNDLE_ID>://auth`
- ***Android***: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - O hash de assinatura Android pode ser gerado usando as chaves de versão ou depuração por meio do comando KeyTool.

## <a name="api-permissions"></a>Permissões de API

Aplicativos móveis chamam APIs em nome do usuário conectado. Seu aplicativo precisará solicitar permissões delegadas, também conhecidas como escopos. Dependendo da experiência desejada, isso pode ser feito estaticamente por meio do portal do Azure ou dinamicamente em tempo de execução. Registrar estaticamente permissões permite que os administradores aprovar facilmente seu aplicativo e é recomendado.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adquirir um token](scenario-mobile-acquire-token.md)
