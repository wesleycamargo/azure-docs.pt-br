---
title: Aplicativos de cliente (biblioteca de autenticação da Microsoft) | Azure
description: Saiba mais sobre o cliente público e o cliente confidencial aplicativos na autenticação biblioteca MSAL (Microsoft).
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
ms.date: 04/25/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f21aa62bae7599cf586ccf6f885ad9f58c148d1f
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077134"
---
# <a name="public-client-and-confidential-client-applications"></a>Cliente público e aplicativos de cliente confidencial
Biblioteca de autenticação da Microsoft (MSAL) define dois tipos de clientes: clientes públicos e clientes confidenciais. Os tipos de cliente de dois são diferenciados por sua capacidade de autenticar com segurança com o servidor de autorização e manter a confidencialidade das suas credenciais de cliente.  Por outro lado, a biblioteca de autenticação do Azure AD (ADAL) tem o conceito de contexto de autenticação (que é uma conexão com o Azure AD).

- **Aplicativos de cliente confidencial** são aplicativos que são executados em servidores (aplicativos Web, API da Web ou até mesmo aplicativos de serviço/daemon). Eles são considerados difíceis de acesso e, portanto, capaz de manter um segredo do aplicativo. Clientes confidenciais são capazes de armazenar segredos de tempo de configuração. Cada instância do cliente tem uma configuração de distinta (incluindo o clientId e o segredo). Esses valores são difíceis para os usuários finais extrair. Um aplicativo web é o cliente confidencial mais comuns. A ID do cliente é exposta por meio do navegador da web, mas o segredo é transmitido apenas no canal de retorno e nunca exposto diretamente.

    Aplicativos de cliente confidencial: <BR>
    ![Aplicativo Web](media/msal-client-applications/web-app.png) ![API Web](media/msal-client-applications/web-api.png) ![Daemon/serviço](media/msal-client-applications/daemon-service.png)

- **Aplicativos de cliente público** são aplicativos, que são executados em dispositivos ou computadores desktop ou em um navegador da web. Eles não são confiáveis para manter com segurança segredos do aplicativo e, portanto, apenas acessar as APIs da Web em nome do usuário (eles somente oferecem suporte a fluxos de cliente público). Clientes públicos não conseguem armazenar segredos de tempo de configuração e assim não ter nenhum segredo do cliente.

    Aplicativos de cliente público: <BR>
    ![Aplicativo de desktop](media/msal-client-applications/desktop-app.png) ![API Browserless](media/msal-client-applications/browserless-app.png) ![aplicativo móvel](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> No msal, não há nenhuma separação de aplicativos clientes públicos e confidenciais.  Msal representando aplicativos cliente como aplicativos baseados em agente de usuário, um cliente público no qual o código do cliente é executado em um agente do usuário, como um navegador da web.  Esses clientes não armazenarem segredos, como o contexto do navegador é acessível abertamente.

## <a name="comparing-the-client-types"></a>Comparando os tipos de cliente
Há algumas semelhanças e diferenças entre o cliente público e o cliente confidencial aplicativos:

- Os dois tipos de aplicativos de mantêm um cache de token de usuário e podem adquirir um token silenciosamente (em casos em que o token já está no cache de token). Aplicativos de cliente confidencial também têm um cache de token de aplicativo para tokens que são para o aplicativo em si.
- Ambos gerenciar contas de usuário e podem obter as contas do cache de token de usuário, obter uma conta de seu identificador ou remover uma conta.
- Aplicativos de cliente público têm quatro maneiras de adquirir um token (quatro fluxos de autenticação), enquanto aplicativos de cliente confidencial têm três (e o ponto de extremidade autorizado de um método para calcular a URL do provedor de identidade). Para obter mais informações, consulte cenários e aquisição de tokens.

Se você usou a ADAL no passado, você pode notar que, ao contrário do contexto de autenticação da ADAL, na MSAL a ID do cliente (também chamado de ID do aplicativo ou ID do aplicativo) é passada uma vez na construção do aplicativo e não está mais precisa ser repetida ao adquirir um token. Isso é o caso ambos os aplicativos cliente públicos e confidenciais. Construtores de aplicativos de cliente confidencial também são passados para as credenciais do cliente: o segredo que compartilham com o provedor de identidade.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre:
- [Opções de configuração de aplicativo do cliente](msal-client-application-configuration.md)
- [Criando aplicativos de cliente usando MSAL.NET](msal-net-initializing-client-applications.md).
- [Criando uma instância de aplicativos cliente usando a msal](msal-js-initializing-client-applications.md).
