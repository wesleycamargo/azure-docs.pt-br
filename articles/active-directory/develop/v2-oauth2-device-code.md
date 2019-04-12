---
title: Use plataforma de identidade Microsoft para conectar usuários em dispositivos sem navegador | Azure
description: Crie fluxos de autenticação inseridos e sem navegador usando a concessão de código do dispositivo.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 780eec4d-7ee1-48b7-b29f-cd0b8cb41ed3
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 14291a6e8f9c4cde3c8777969047ebaa77e42b59
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59500440"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-code-flow"></a>Plataforma de identidade da Microsoft e o fluxo de código de dispositivo do OAuth 2.0

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Dá suporte à plataforma de identidade do Microsoft as [concessão de código de dispositivo](https://tools.ietf.org/html/draft-ietf-oauth-device-flow-12), que permite aos usuários entrar em dispositivos restritos a entrada como uma smart TV, dispositivo IoT ou impressora.  Para habilitar esse fluxo, o dispositivo exige que o usuário visite uma página da Web no navegador em outro dispositivo para entrar.  Depois que o usuário entra, o dispositivo é capaz de acessar e atualizar tokens, conforme o necessário.  

> [!IMPORTANT]
> Neste momento, o ponto de extremidade de plataforma de identidade do Microsoft só suporta o fluxo de dispositivo para locatários do Azure AD, mas as contas não pessoais.  Isso significa que você deve usar um ponto de extremidade configurado como um locatário, ou o `organizations` ponto de extremidade.  
>
> As contas pessoais que forem convidadas para um locatário do Azure AD poderão usar a concessão de fluxo do dispositivo, mas somente no contexto do locatário.

> [!NOTE]
> O ponto de extremidade de plataforma de identidade do Microsoft não oferece suporte a todos os recursos e cenários do Active Directory do Azure. Para determinar se deve usar o ponto de extremidade de plataforma do Microsoft identity, leia sobre [limitações da plataforma Microsoft identity](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Diagrama de protocolo

O fluxo de código do dispositivo inteiro é semelhante ao diagrama a seguir. Descrevemos cada uma das etapas neste artigo.

![Fluxo de código do dispositivo](media/v2-oauth2-device-flow/v2-oauth-device-flow.png)

## <a name="device-authorization-request"></a>Solicitação de autorização de dispositivo

Primeiro, o cliente precisa verificar com o servidor de autenticação se há um código do dispositivo e do usuário, usado para iniciar a autenticação.  O cliente coleta essa solicitação do ponto de extremidade `/devicecode`. Nessa solicitação, o cliente também deve incluir as permissões que precisa adquirir do usuário.  A partir do momento em que essa solicitação é enviada, o usuário tem apenas 15 minutos para entrar (o valor normal para `expires_in`), portanto, somente faça essa solicitação quando o usuário indicar que está pronto para entrar.

> [!TIP]
> Tente executar a solicitação no Postman!
> [![Executar no Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parâmetro | Condição | DESCRIÇÃO |
| --- | --- | --- |
| `tenant` | Obrigatório |O locatário do diretório para o qual você deseja solicitar permissão. Pode estar no formato de nome amigável ou de GUID.  |
| `client_id` | Obrigatório | O **ID do aplicativo (cliente)** que o [portal do Azure – registros do aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) experiência atribuída ao seu aplicativo. |
| `scope` | Recomendadas | Uma lista separada por espaços de [escopos](v2-permissions-and-consent.md) para os quais você deseja o consentimento do usuário.  |

### <a name="device-authorization-response"></a>Resposta de autorização de dispositivo

Uma resposta bem-sucedida será um objeto JSON contendo as informações necessárias para permitir que o usuário faça login.  

| Parâmetro | Formatar | DESCRIÇÃO |
| ---              | --- | --- |
|`device_code`     | Cadeia de caracteres | Uma cadeia de caracteres longa usada para verificar a sessão entre o cliente e o servidor de autorização.  Ela é usada pelo cliente para solicitar o token de acesso do servidor de autorização. |
|`user_code`       | Cadeia de caracteres | Uma cadeia de caracteres curta mostrada ao usuário, usada para identificar a sessão em um dispositivo secundário.|
|`verification_uri`| URI | O URI que o usuário deve acessar com o `user_code` para entrar. |
|`verification_uri_complete`|URI| Um URI que combina o `user_code` e o `verification_uri`, usado para transmissão não textual ao usuário (por exemplo, via Bluetooth para um dispositivo ou por meio de um código QR).  |
|`expires_in`      |  int| O número de segundos antes que o `device_code` e o `user_code` expirem. |
|`interval`        | int | O número de segundos que o cliente deve aguardar entre as solicitações de sondagem. |
| `message`        | Cadeia de caracteres | Uma cadeia de caracteres legível com instruções para o usuário.  Ela pode ser localizada incluindo um **parâmetro de consulta** na solicitação do formulário `?mkt=xx-XX`, preenchendo o código de cultura do idioma apropriado. |

## <a name="authenticating-the-user"></a>Autenticação do usuário

Após o recebimento de `user_code` e `verification_uri`, o cliente é exibido para o usuário, instruindo-os a entrar usando seu telefone celular ou o navegador do PC.  Além disso, o cliente pode usar um código QR ou um mecanismo semelhante para exibir o `verfication_uri_complete`, o que levará à etapa de inserir o `user_code` para o usuário.

Enquanto o usuário está se autenticando no `verification_uri`, o cliente deverá estar sondando o ponto de extremidade `/token` para o token solicitado usando o `device_code`.

``` 
POST https://login.microsoftonline.com/tenant/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8
```

| Parâmetro | Obrigatório | DESCRIÇÃO|
| -------- | -------- | ---------- |
| `grant_type` | Obrigatório | deve ser `urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Obrigatório | Precisa corresponder à `client_id` usada na solicitação inicial. |
| `device_code`| Obrigatório | O `device_code` retornado na solicitação de autorização de dispositivo.  |

### <a name="expected-errors"></a>Erros esperados

Como o fluxo de código do dispositivo é um protocolo de sondagem, o cliente deve esperar receber erros antes que o usuário termine a autenticação.  

| Erro | DESCRIÇÃO | Ação do Cliente |
| ------ | ----------- | -------------|
| `authorization_pending` | O usuário ainda não terminou a autenticação, mas não cancelou o fluxo. | Repita a solicitação depois de pelo menos `interval` segundos. |
| `authorization_declined` | O usuário final negou a solicitação de autorização.| Interrompa a sondagem e reverta para um estado não autenticado.  |
| `bad_verification_code`|O `device_code` enviado ao ponto de extremidade `/token` não foi reconhecido. | Verifique se o cliente está enviando o `device_code` correto na solicitação. |
| `expired_token` | Pelo menos `expires_in` segundos foram decorridos e a autenticação não é mais possível com este `device_code`. | Interrompa a sondagem e reverta para um estado não autenticado. |


### <a name="successful-authentication-response"></a>Resposta de autenticação bem sucedida

Uma resposta de token bem-sucedida se parecerá com esta:

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| Parâmetro | Formatar | DESCRIÇÃO |
| --------- | ------ | ----------- |
| `token_type` | Cadeia de caracteres| Sempre "Portador. |
| `scope` | Cadeia de caracteres separadas por espaço | Se um token de acesso for retornado, isso listará os escopos em que o token de acesso é válido. |
| `expires_in`| int | Número de segundos antes que o token de acesso incluído seja válido. |
| `access_token`| Cadeia de caracteres opaca | Emitido para os [escopos](v2-permissions-and-consent.md) que foram solicitados.  |
| `id_token`   | JWT | Emitido quando o parâmetro original `scope` inclui o escopo `openid`.  |
| `refresh_token` | Cadeia de caracteres opaca | Emitido quando o parâmetro original `scope` inclui `offline_access`.  |

O token de atualização pode ser usado para adquirir novos tokens de acesso e atualizar tokens usando o mesmo fluxo detalhado na [Documentação do fluxo de código OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  
