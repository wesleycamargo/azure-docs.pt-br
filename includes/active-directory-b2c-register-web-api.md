---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/03/2016
ms.author: patricka
ms.openlocfilehash: fcd71f74e0b00934958828024094773e42496b66
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51017263"
---
[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

Para registrar sua API da Web, use as configurações especificadas na tabela.

![Exemplo de configurações de registro para nova API Web](./media/active-directory-b2c-register-web-api/b2c-new-web-api-settings.png)

| Configuração      | Valor de exemplo  | DESCRIÇÃO                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Nome** | API B2C da Contoso | Insira um **Nome** para o aplicativo que descreva sua API aos consumidores. | 
| **Incluir aplicativo Web/API Web** | SIM | Selecione **Sim** para uma API Web. |
| **Permitir fluxo implícito** | SIM | Escolha **Sim** se o seu aplicativo usar [Entrada do OpenID Connect](../articles/active-directory-b2c/active-directory-b2c-reference-oidc.md) |
| **URL de Resposta** | `https://localhost:44316/` | As URLs de Resposta são pontos de extremidade para onde o Azure AD B2C retorna quaisquer tokens que seus aplicativo solicitarem. Neste exemplo, a sua API Web é local e escuta na porta 44316. |
| **URI da ID do Aplicativo** | api | O URI da ID do Aplicativo é o identificador usado para sua API Web. O URI do identificador completo, incluindo o domínio, é gerado para você. |

Clique em **Criar** para registrar seu aplicativo.

Seu aplicativo recém-registrado é exibido na lista de aplicativos para o locatário B2C. Selecione sua API Web na lista. O painel de propriedade da API é exibido.

![Propriedades da API Web](./media/active-directory-b2c-register-web-api/b2c-web-api-properties.png)

Anote o identificador global exclusivo **ID do Cliente do Aplicativo**. Use a ID no código do seu aplicativo.