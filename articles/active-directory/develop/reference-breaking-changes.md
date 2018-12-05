---
title: Referência de alterações da falha do Azure Active Directory | Microsoft Docs
description: Saiba mais sobre as alterações realizadas nos protocolos do Azure AD que podem afetar o aplicativo.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 68517c83-1279-4cc7-a7c1-c7ccc3dbe146
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 8e00674f331a56be9abe6f2356aa88d7dcf1d0b0
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2018
ms.locfileid: "52282377"
---
# <a name="whats-new-for-authentication"></a>Quais são as novidades para autenticação? 

>Seja notificado sobre atualizações nesta página. Basta adicionar [esta URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) ao leitor de RSS feed.

O sistema de autenticação altera e adiciona recursos em uma base contínua para melhorar a segurança e a conformidade com os padrões. Para manter-se atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre os seguintes detalhes:

- Os recursos mais recentes
- Problemas conhecidos
- Alterações do protocolo
- Funcionalidades preteridas

> [!TIP] 
> Esta página é atualizada regularmente, então, visite-a com frequência. Salvo indicação contrária, essas alterações só serão aplicadas para aplicativos registrados recentemente.  

## <a name="upcoming-changes"></a>Alterações futuras

Nenhum agendado neste momento. 

## <a name="october-2018"></a>Outubro de 2018

### <a name="authorization-codes-can-no-longer-be-reused"></a>Não é mais possível reutilizar códigos de autorização

**Data de efetivação**: 15 de novembro de 2018

**Pontos de extremidade afetados**: v1.0 e v2.0

**Protocolo afetado**: [Fluxo de código](v2-oauth2-auth-code-flow.md)

A partir de 15 de novembro de 2018, o Azure AD deixará de aceitar códigos de autenticação usados anteriormente para aplicativos. Essa alteração de segurança ajuda a deixar o Azure AD em conformidade com a especificação do OAuth e será aplicada aos pontos de extremidade v1 e v2.

Se seu aplicativo reutiliza códigos de autorização para obter tokens para vários recursos, recomendamos que você use o código para obter um token de atualização e, em seguida, utilize esse token de atualização para adquirir tokens adicionais para outros recursos. Os códigos de autorização só podem ser usados uma vez; porém, os códigos de atualização podem ser usados várias vezes em diferentes recursos. Qualquer aplicativo novo que tente reutilizar um código de autenticação durante o fluxo de código OAuth receberá um erro invalid_grant.

Para obter mais informações sobre tokens de atualização, consulte [Atualização de tokens de acesso](v1-protocols-oauth-code.md#refreshing-the-access-tokens).  Se usar a ADAL ou MSAL, isso é feito para você pela biblioteca – substituir a segunda instância de 'AcquireTokenByAuthorizationCodeAsync' por 'AcquireTokenSilentAsync'. 

## <a name="may-2018"></a>Maio de 2018

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>Os tokens de ID não podem ser usados para o fluxo OBO

**Data**: 1º de maio de 2018

**Pontos de extremidade afetados**: v1.0 e v2.0

**Protocolos afetados**: fluxo implícito e [fluxo OBO](v1-oauth2-on-behalf-of-flow.md)

Após 1 de maio de 2018, os id_tokens não podem ser usados como a declaração em um Fluxo OBO para novos aplicativos. Em vez disso, é necessário usar tokens de acesso para proteger as APIs, até mesmo entre um cliente e a camada intermediária do mesmo aplicativo. Os aplicativos registrados antes de 1º de maio de 2018 continuarão funcionando e poderão trocar id_tokens por um token de acesso, no entanto, esse padrão não é considerado uma melhor prática.

Para contornar essa alteração, é possível fazer o seguinte:

1. Crie uma API da Web para seu aplicativo, com um ou mais escopos. Esse ponto de entrada explícito permitirá que segurança e controle mais precisos.
1. No manifesto do aplicativo, no [portal do Azure](https://portal.azure.com) ou no [portal de registro do aplicativo](https://apps.dev.microsoft.com), verifique se o aplicativo tem permissão para emitir tokens de acesso por meio do fluxo implícito. Isso é controlado pela chave `oauth2AllowImplicitFlow`.
1. Quando o aplicativo cliente solicita um id_token via `response_type=id_token`, também solicita um token de acesso (`response_type=token`) para a API da Web criada acima. Portanto, ao usar o ponto de extremidade v 2.0, o parâmetro `scope` deverá ser semelhante ao `api://GUID/SCOPE`. No ponto de extremidade v1.0, o parâmetro `resource` deve ser o URI da API do aplicativo Web.
1. Passe esse token de acesso para a camada intermediária no lugar de id_token.  
