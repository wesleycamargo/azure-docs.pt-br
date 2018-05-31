---
title: Adicionando provedores de identidade OpenID Connect em políticas internas – Azure AD B2C | Microsoft Docs
description: Guia de visão geral de como adicionar provedores do OpenID Connect em políticas internas no Azure AD B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: parakhj
ms.assetid: 357d193a-e33b-469c-8a93-0a8f45a60a9f
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/27/2018
ms.author: davidmu
ms.openlocfilehash: 46223f1ec6b82828983861dae26ec8d777b77b86
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2018
ms.locfileid: "32308977"
---
# <a name="azure-active-directory-b2c-add-a-custom-openid-connect-identity-provider-in-built-in-policies"></a>Azure Active Directory B2C: adicionar um provedor de identidade do OpenID Connect personalizado em políticas internas

>[!NOTE]
> Esse recurso está em uma versão prévia. Não use o recurso em ambientes de produção.

O [OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) é um protocolo de autenticação, criado com base no OAuth 2.0, que pode ser usado para que os usuários entrem com segurança. A maioria dos provedores de identidade que usam esse protocolo, como o [Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md), é compatível com o Azure AD B2C. Este artigo explica como você pode adicionar provedores de identidade do OpenID Connect personalizados em suas políticas internas.

## <a name="configuring-a-custom-openid-connect-identity-provider"></a>Configurando um provedor de identidade do OpenID Connect personalizado

Para adicionar um provedor de identidade do OpenID Connect personalizado:

1. Siga estas etapas para [navegar até as configurações do Azure AD B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) no portal do Azure.
1. Clique em **Provedores de Identidade**.
1. Clique em **Adicionar**.
1. Para o **tipo de provedor de identidade**, selecione **OpenID Connect**.

### <a name="setting-up-the-openid-connect-identity-provider"></a>Configurando o provedor de identidade do OpenID Connect

#### <a name="metadata-url"></a>URL de metadados

De acordo com a especificação, todos os provedores de identidade do OpenID Connect descrevem um documento de metadados que contém a maioria das informações necessárias para entrar. Isso inclui informações como as URLs a serem usadas e o local das chaves de assinatura públicas do serviço. O documento de metadados do OpenID Connect sempre está localizado em um ponto de extremidade que termina com `.well-known\openid-configuration`.

Insira a URL de metadados do provedor de identidade do OpenID Connect que você deseja adicionar.

#### <a name="client-id-and-secret"></a>ID do cliente e segredo

Para permitir que os usuários entrem, o provedor de identidade exigirá que os desenvolvedores registrem um aplicativo em seus serviços. Este aplicativo terá uma ID (conhecida como o **ID do cliente**) e um **segredo do cliente**. Copie esses valores do provedor de identidade e insira-os nos campos correspondentes.

> [!NOTE]
> O segredo do cliente é opcional. No entanto, será necessário inserir um segredo do cliente se você desejar usar o [fluxo de código de autorização](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), que usa o segredo para trocar o código do token.

#### <a name="scope"></a>Escopo

Os escopos definem as informações e as permissões que você deseja coletar do provedor de identidade personalizado. As solicitações do OpenID Connect precisam conter o valor de escopo do `openid` para receber o token de ID do provedor de identidade. Sem o token de ID, os usuários não poderão entrar no Azure AD B2C usando o provedor de identidade personalizado.

É possível anexar outros escopos (separados por espaço). Veja a documentação do provedor de identidade personalizado para saber quais outros escopos podem estar disponíveis.

#### <a name="response-type"></a>Tipo de resposta

O tipo de resposta que descreve que tipo de informação será retornada na chamada inicial para o `authorization_endpoint` do provedor de identidade personalizado. 

* `code`: conforme o [fluxo de código de autorização](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), um código será retornado ao Azure AD B2C. Em seguida, o Azure AD B2C prosseguirá para chamar o `token_endpoint` para trocar o código pelo token.
* `token`: um token de acesso será retornado para o Azure AD B2C do provedor de identidade personalizado.
* `id_token`: um token de ID será retornado para o Azure AD B2C do provedor de identidade personalizado.


#### <a name="response-mode"></a>Modo de resposta

O modo de resposta define o método que deve ser usado para retornar os dados do provedor de identidade personalizado para o Azure AD B2C.

* `form_post`: esse modo de resposta é recomendado para obter a melhor segurança possível. A resposta é transmitida por meio do método HTTP `POST`, com o código ou o token codificado no corpo usando o formato `application/x-www-form-urlencoded`.
* `query`: o código ou o token será retornado como um parâmetro de consulta.


#### <a name="domain-hint"></a>Dica de domínio

A dica de domínio pode ser usada para pular diretamente para a página de entrada do provedor de identidade especificado, em vez de fazer com que o usuário faça uma seleção na lista de provedores de identidade disponíveis. Para permitir esse tipo de comportamento, insira um valor para a dica de domínio.

Para pular para o provedor de identidade personalizado, acrescente o parâmetro `domain_hint=<domain hint value>` ao final da solicitação ao chamar o Azure AD B2C para entrar.


### <a name="mapping-the-claims-from-the-openid-connect-identity-provider"></a>Mapeando as declarações do provedor de identidade do OpenID Connect

Depois que o provedor de identidade personalizado envia um token de ID para o Azure AD B2C, o Azure AD B2C precisa ser capaz de mapear as declarações do token recebido para as declarações que ele reconhece e usa. 

Para cada um dos mapeamentos abaixo, veja a documentação do provedor de identidade personalizado para entender as declarações que são retornadas nos tokens do provedor de identidade.

* `User ID`: insira a declaração que fornece o identificador exclusivo para o usuário conectado.
* `Display Name`: insira a declaração que fornece o nome de exibição ou o nome completo do usuário.
* `Given Name`: insira a declaração que fornece o nome do usuário.
* `Surname`: insira a declaração que fornece o sobrenome do usuário.
* `Email`: insira a declaração que fornece o endereço de email do usuário.

## <a name="next-steps"></a>Próximas etapas

Adicionar o provedor de identidade do OpenID Connect personalizado em sua [política interna](active-directory-b2c-reference-policies.md).
