---
title: Termos de serviço e a declaração de privacidade para aplicativos | Azure
description: Saiba como você pode configurar os termos de declaração de privacidade e de serviço de aplicativo registrado para usar o AD do Azure.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviwer: lenalepa, sureshja
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97728fa70f0c5f58510e0e68d27a379b20887703
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60410514"
---
# <a name="how-to-configure-terms-of-service-and-privacy-statement-for-an-app"></a>Como: Configurar os termos de serviço e a declaração de privacidade para um aplicativo

Os desenvolvedores que criam e gerenciar aplicativos que se integram ao Azure Active Directory (AD do Azure) e contas da Microsoft devem incluir links para os termos do aplicativo de serviço e declarações de privacidade. Os termos de serviço e a declaração de privacidade são revelados aos usuários por meio da experiência de consentimento do usuário. Eles ajudam seus usuários a saberem que podem confiar em seu aplicativo. Os termos da declaração de privacidade e de serviço são especialmente importantes para aplicativos de multilocação voltadas para o usuário – aplicativos que são usados por vários diretórios ou estão disponíveis para qualquer conta da Microsoft.

Você é responsável por criar os termos de serviço e a privacidade documentos de instrução para seu aplicativo e para fornecer as URLs a esses documentos. Para aplicativos multilocatários que falham ao fornecer esses links, a experiência de consentimento do usuário para seu aplicativo mostrará um alerta, o que pode evitar que usuários de consentimento ao seu aplicativo.

> [!NOTE]
> * Aplicativos de único locatário não exibirá um alerta.
> * Se um ou ambos os links estiverem faltando, seu aplicativo mostrará um alerta.

## <a name="user-consent-experience"></a>Experiência de consentimento do usuário

Os exemplos a seguir mostram o usuário experiência de consentimento quando as condições de declaração de privacidade e de serviço são configuradas e quando esses links não estão configurados.

![Capturas de tela com e sem uma declaração de privacidade e termos de serviço fornecido](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>Links de formatação para os termos de serviço e a privacidade documentos de instrução

Antes de adicionar links aos termos de serviço e aos documentos da declaração de privacidade do seu aplicativo, verifique se os URLs seguem essas diretrizes.

| Diretriz     | DESCRIÇÃO                           |
|---------------|---------------------------------------|
| Formatar        | URL Válida                             |
| Esquemas válidos | HTTP e HTTPS<br/>Recomendamos HTTPS |
| Comprimento máximo    | 2048 caracteres                       |

Exemplos: `https://myapp.com/terms-of-service` e `https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>Adicionando links aos termos de serviço e declaração de privacidade

Quando os termos de serviço e a declaração de privacidade estiverem prontos, você poderá adicionar links para esses documentos no seu aplicativo usando um destes métodos:

* [ através do portal do Azure ](#registered-in-azure-portal)
* [ No Portal de Registro de Aplicativos ou no Centro de Desenvolvimento ](#registered-in-app-reg-portal)
* [ Usando o objeto de aplicativo JSON ](#app-object-json)
* [ Usando a API REST do MSGraph beta ](#msgraph-beta-rest-api)

### <a name="registered-in-azure-portal"></a>Se você registrou seu aplicativo no portal do Azure

Se você registrou seu aplicativo no portal do Azure, siga estas etapas.

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Navegue até a seção **Registros de aplicativo** e selecione seu aplicativo.
3. Abra a seção **Propriedades** do aplicativo.
4. Preencha os campos da **URL de termos de serviço** e **Política de privacidade**.
5. Salve suas alterações.

    ![Seção de propriedades do aplicativo com os termos de serviço e a privacidade URLs de instrução](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="registered-in-app-reg-portal"></a>Se você registrou seu aplicativo no Portal de registro do aplicativo

Siga estas etapas se você registrou seu aplicativo no Portal de registro de aplicativos ou no Centro de desenvolvimento.

1. Entrar no [Portal de Registro de Aplicativos](https://apps.dev.microsoft.com/).
2. Selecione seu aplicativo e role até a seção **Perfil**.
3. Preencha os campos da **URL de termos de serviço** e **Política de privacidade**.
4. Salve suas alterações.

    ![Seção de perfil do aplicativo com termos de serviço e URLs de declaração de privacidade](./media/howto-add-terms-of-service-privacy-statement/app-registration-portal-profile-terms-service-privacy-statement-urls.png)

### <a name="app-object-json"> </a> Usando o objeto de aplicativo JSON

Se preferir modificar diretamente o objeto JSON do aplicativo, você poderá usar o editor de manifesto no Portal do Azure ou no Portal de Registro do Aplicativo para incluir links para os termos de serviço e a declaração de privacidade do seu aplicativo.

```json
    "informationalUrls": { 
        "termsOfService": "<your_terms_of_service_url>", 
        "privacy": "<your_privacy_statement_url>" 
    }
```

### <a name="msgraph-beta-rest-api"> </a> Usando a API REST do MSGraph beta

Para atualizar programaticamente todos os seus aplicativos, você pode usar a API REST beta do MSGraph para atualizar todos os seus aplicativos para incluir links para os termos de serviço e documentos de declaração de privacidade.

```
PATCH https://graph.microsoft.com/beta/applications/{application id}
{ 
    "appId": "{your application id}", 
    "info": { 
        "termsOfServiceUrl": "<your_terms_of_service_url>", 
        "supportUrl": null, 
        "privacyStatementUrl": "<your_privacy_statement_url>", 
        "marketingUrl": null, 
        "logoUrl": null 
    }
}
```

> [!NOTE]
> * Tenha cuidado para não sobrescrever quaisquer valores preexistentes atribuídos a qualquer um desses campos: `supportUrl`, `marketingUrl` e `logoUrl`
> * A API REST beta do MSGraph só funcionará quando você fizer login com uma conta do AD do Azure. Contas pessoais da Microsoft não são suportadas.
