---
title: 'Azure Active Directory B2C: solicitando tokens de acesso | Microsoft Docs'
description: Este artigo mostra como configurar um aplicativo cliente e adquirir um token de acesso.
services: active-directory-b2c
documentationcenter: android
author: parakhj
manager: krassk
editor: 
ms.assetid: 1c75f17f-5ec5-493a-b906-f543b3b1ea66
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: parakhj
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: 7a28c92e921354cd4a623af29098a40e1c8b56b0
ms.contentlocale: pt-br
ms.lasthandoff: 06/07/2017


---
# <a name="azure-ad-b2c-requesting-access-tokens"></a>Azure AD B2C: solicitando tokens de acesso

Um token de acesso (chamado de **token\_de acesso**) é uma forma de token de segurança que um cliente pode usar para acessar os recursos que são protegidos por um [servidor de autorização](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-protocols#the-basics), por exemplo, uma API Web. Tokens de acesso são representados como [JWTs](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#types-of-tokens) e contêm informações sobre o servidor do recurso pretendido e as permissões concedidas ao servidor. Ao chamar o servidor do recurso, o token de acesso deve estar presente na solicitação HTTP.

Este artigo descreve como configurar um aplicativo cliente e fazê-lo gerar uma solicitação para adquirir um **token\_de acesso** dos pontos de extremidade `authorize` e `token`.

> [!NOTE]
> **Não há suporte a cadeias de API Web (em nome de) no Azure AD B2C.**
>
> Muitas arquiteturas incluem uma API da Web que precisa chamar outra API da Web downstream, ambas protegidas pelo AD B2C do Azure. Este cenário é comum em clientes nativos que têm um back-end de API Web que, por sua vez, chama um serviço online da Microsoft, como a API do Graph do AD do Azure.
>
> Este cenário de API Web encadeada pode ter suporte usando a concessão Credencial de Portador Jwt do OAuth 2.0, também conhecido como fluxo Em nome de. No entanto, o fluxo Em Nome de não está implementado atualmente no Azure AD B2C.

## <a name="prerequisite"></a>Pré-requisito

Antes de solicitar um token de acesso, você primeiro precisa registrar uma API Web e publicar as permissões que podem ser concedidas ao aplicativo cliente. Comece seguindo as etapas abaixo na seção [Registrar uma API Web](active-directory-b2c-app-registration.md#register-a-web-api).

## <a name="granting-permissions-to-a-web-api"></a>Conceder permissões a uma API Web

Para um aplicativo cliente obter permissões específicas para uma API, o aplicativo cliente precisa receber tais permissões por meio do Portal do Azure. Para conceder permissões a um aplicativo cliente:

1. Navegue até o menu **Aplicativos** na folha de recursos do B2C.
1. Registre um aplicativo cliente ([aplicativo Web](active-directory-b2c-app-registration.md#register-a-web-application) ou [cliente nativo](active-directory-b2c-app-registration.md#register-a-mobilenative-application)) se você ainda não tem um.
1. Na folha de configurações do seu aplicativo, selecione **Acesso à Api**.
1. Clique em **Adicionar**.
1. Selecione sua API Web e os escopos (permissões) que você deseja conceder.
1. Clique em **OK**.

> [!NOTE]
> O Azure AD B2C não solicitará o consentimento dos usuários do aplicativo cliente. Em vez disso, todo o consentimento é fornecido pelo administrador, com base nas permissões configuradas entre os aplicativos descritos acima. Se uma concessão de permissão de um aplicativo for revogada, todos os usuários que antes podiam adquirir essa permissão não serão mais capazes de fazer isso.

## <a name="requesting-a-token"></a>Solicitar um token

Para obter um token de acesso para um aplicativo de recurso, o aplicativo cliente precisa especificar as permissões desejadas no parâmetro **scope** da solicitação. Por exemplo, para obter a permissão de "leitura" para o aplicativo de recurso que tem o URI de ID do aplicativo de `https://contoso.onmicrosoft.com/notes`, o escopo seria `https://contoso.onmicrosoft.com/notes/read`. Abaixo está um exemplo de uma solicitação de código de autorização para o ponto de extremidade `authorize`.

> [!NOTE]
> Neste momento, não há suporte para os domínios personalizados junto com os tokens de acesso. Você deve usar seu domínio yourtenantId.onmicrosoft.com na URL de solicitação.

```
https://login.microsoftonline.com/<yourTenantId>.onmicrosoft.com/oauth2/v2.0/authorize?p=<yourPolicyId>&client_id=<appID_of_your_client_application>&nonce=anyRandomValue&redirect_uri=<redirect_uri_of_your_client_application>&scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread&response_type=code 
```

Para adquirir várias permissões na mesma solicitação, você pode adicionar várias entradas separadas por espaços no único parâmetro **scope**. Por exemplo:

Decodificado em URL:

```
scope=https://contoso.onmicrosoft.com/notes/read openid offline_access
```

Codificado em URL:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread%20openid%20offline_access
```

Você pode solicitar mais escopos/permissões para um recurso do que são concedidas para seu aplicativo cliente. Quando esse for o caso, a chamada será bem-sucedida se pelo menos uma permissão for concedida. O **token\_de acesso** resultante terá sua declaração "scp" preenchida apenas com as permissões que foram concedidas com êxito.

> [!NOTE] 
> Não damos suporte à solicitação de permissões a dois recursos Web diferentes na mesma solicitação. Esse tipo de solicitação falhará.

### <a name="special-cases"></a>Casos especiais

O padrão de OpenID Connect especifica vários valores especiais de "scope". Os escopos especiais a seguir representam a permissão para "acessar o perfil do usuário":

* **openid**: isso solicita um token de ID
* **acesso\_offline**: Isso solicita um token de atualização (usando fluxos de código de autenticação).

Se o parâmetro “response\_type” parâmetro em uma solicitação `authorize` inclui "token", o parâmetro "scope" deve incluir pelo menos uma permissão de recurso (diferente de "openid" e “offline\_access”) que será concedida. Caso contrário, a solicitação `authorize` terminará com falha.

## <a name="the-returned-token"></a>O token retornado

Em um **token\_de acesso** obtido com êxito (do ponto de extremidade `authorize` ou `token`), as seguintes declarações estarão presentes:

| Nome | Declaração | Descrição |
| --- | --- | --- |
|Público-alvo |`aud` |A \*ID do aplicativo\* do recurso único ao qual o token concede acesso. |
|Escopo |`scp` |As permissões concedidas ao recurso. Várias permissões concedidas serão separadas por espaço. |
|Entidade Autorizada |`azp` |A \*ID do aplicativo\* do aplicativo cliente que iniciou a solicitação. |

Quando sua API recebe o **token\_de acesso**, ela deve [validar o token](active-directory-b2c-reference-tokens.md) para provar que o token é autêntico e que tem as declarações corretas.

Estamos sempre abertos a comentários e sugestões! Caso você tenha alguma dúvida sobre este tópico ou recomendações para melhorar o conteúdo, agradecemos seus comentários na parte inferior da página. Para solicitações de recursos, adicione-os ao [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

