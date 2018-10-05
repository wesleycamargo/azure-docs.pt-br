---
title: Referência de tokens de acesso do Azure Active Directory | Microsoft Docs
description: Aceitação de tokens de acesso emitidos pelos pontos de extremidade v1.0 e v2.0 do Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: df02ab01-3490-419b-90f5-be7adaeadd58
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: c6399c2662a655f1c4ba50380a5ac4dde6ddda78
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47221140"
---
# <a name="azure-active-directory-access-tokens"></a>Tokens de acesso do Azure Active Directory

Os tokens de acesso permitem que os clientes chamem APIs protegidas pelo Azure com segurança. Os tokens de acesso do Azure AD (Azure Active Directory) são objetos JSON [JWTs](https://tools.ietf.org/html/rfc7519) codificados em Base64 assinados pelo Azure. Os clientes devem tratar os tokens de acesso como cadeias de caracteres opacas, pois o conteúdo do token se destina apenas ao recurso. Para fins de validação e depuração, os desenvolvedores podem decodificar JWTs usando um site como [jwt.ms](https://jwt.ms). Seu cliente pode obter um token de acesso em qualquer ponto de extremidade (v1.0 ou v2.0) usando uma variedade de protocolos.

Quando você solicita um token de acesso, o Azure AD também retorna alguns metadados sobre o token de acesso para consumo do aplicativo. Essas informações incluem a data de expiração do token de acesso e os escopos para os quais ele é válido. Esses dados permitem ao aplicativo realizar o cache inteligente dos tokens de acesso sem precisar analisar o token de acesso em si.

Se o aplicativo é um recurso (API Web) ao qual os clientes podem solicitar acesso, os tokens de acesso fornecem informações úteis para uso em autenticação e autorização, como o usuário, o cliente, o emissor, as permissões e muito mais. 

Confira as seções a seguir para saber como um recurso pode validar e usar as declarações dentro de um token de acesso.

> [!NOTE]
> Ao testar seu aplicativo cliente com uma conta pessoal (como hotmail.com ou outlook.com), você pode achar que o token de acesso recebido pelo cliente é uma cadeia de caracteres opaca. Isso ocorre porque o recurso que está sendo acessado solicitou tíquetes MSA (conta Microsoft) herdados que são criptografados e não podem ser reconhecidos pelo cliente.

## <a name="sample-tokens"></a>Tokens de exemplo

Os tokens v1.0 e v2.0 são muito semelhantes e contêm muitas das mesmas declarações. Um exemplo de cada um é fornecido aqui.

### <a name="v10"></a>v1.0

   `eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0=.D3H6pMUtQnoJAGq6AHd`

Exiba esse token da v1.0 em [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0=.D3H6pMUtQnoJAGq6AHd).

### <a name="v20"></a>v2.0

   `eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt`

Exiba esse token v2.0 em [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt).

## <a name="claims-in-access-tokens"></a>Declarações em tokens de acesso

Os JWTs são divididos em três partes: 

* **Cabeçalho** – fornece informações sobre como [validar o token](#validating-tokens), incluindo informações sobre o tipo de token e como ele foi assinado.
* **Conteúdo** – contém todos os dados importantes sobre o usuário ou o aplicativo que está tentando chamar o serviço.
* **Assinatura** – é a matéria-prima usada para validar o token.

Cada parte é separada por um ponto (`.`) e codificada em Base64 separadamente.

As declarações estão presentes somente se existe um valor para preenchê-lo. Portanto, seu aplicativo não deve usar uma dependência em uma declaração que esteja presente. Os exemplos incluem `pwd_exp` (nem todo locatário exige a expiração das senhas) ou `family_name` (a [credencial do cliente](v1-oauth2-client-creds-grant-flow.md) flui são em nome dos aplicativos, que não têm nomes). As declarações usadas para validação do token de acesso sempre estarão presentes.

> [!NOTE]
> Algumas declarações são usadas para ajudar o Azure AD a proteger tokens no caso de reutilização. Elas são marcadas como não sendo para consumo público na descrição como "Opacas". Essas declarações podem ou não aparecer em um token, e novas declarações podem ser adicionadas sem aviso prévio.

### <a name="header-claims"></a>Declarações de cabeçalho

|Declaração | Formatar | DESCRIÇÃO |
|--------|--------|-------------|
| `typ` | Cadeia de caracteres – sempre "JWT" | Indica que o token é um JWT.|
| `nonce` | Cadeia de caracteres | Um identificador exclusivo usado para proteger contra ataques de reprodução de token. O recurso pode registrar esse valor para proteger contra reproduções. |
| `alg` | Cadeia de caracteres | Indica o algoritmo que foi usado para assinar o token, por exemplo, "RS256" |
| `kid` | Cadeia de caracteres | Especifica a impressão digital da chave pública que é usada para assinar esse token. Emitido nos tokens de acesso v1.0 e v2.0. |
| `x5t` | Cadeia de caracteres | Funciona da mesma forma (em uso e valor) que `kid`. Essa é uma declaração herdada emitida somente em tokens de acesso da v1.0 para fins de compatibilidade. |

### <a name="payload-claims"></a>Declarações de conteúdo

| Declaração | Formatar | DESCRIÇÃO |
|-----|--------|-------------|
| `aud` | Cadeia de caracteres, um URI da ID do Aplicativo | Identifica o destinatário pretendido do token. Em tokens de acesso, o público-alvo é a ID de Aplicativo do aplicativo atribuída a ele no portal do Azure. O aplicativo deve validar esse valor e rejeitar o token, caso o valor não seja correspondente. |
| `iss` | Cadeia de caracteres, um URI STS | Identifica o STS (Serviço de Token de Segurança) que constrói e retorna o token e o locatário do Azure AD no qual o usuário foi autenticado. Se o token tiver sido emitido pelo ponto de extremidade v2.0, o URI terminará com `/v2.0`. O GUID que indica que o usuário é um consumidor da conta da Microsoft é `9188040d-6c67-4c5b-b112-36a304b66dad`. O aplicativo deve usar a parte do GUID da declaração para restringir o conjunto de locatários que podem entrar no aplicativo, se aplicável. |
| `iat` | int, um carimbo de data/hora UNIX | "Emitido em" indica quando ocorreu a autenticação desse token. |
| `nbf` | int, um carimbo de data/hora UNIX | A declaração "nbf" (não antes) identifica a hora antes da qual o JWT não deve ser aceito para processamento. |
| `exp` | int, um carimbo de data/hora UNIX | A declaração "exp" (hora de expiração) identifica a hora de expiração ou a hora após ela na qual o JWT não deve ser aceito para processamento. É importante observar que um recurso pode rejeitar o token antes dessa hora também, como quando uma alteração na autenticação é necessária ou se uma revogação de token foi detectada. |
| `acr` | Cadeia de caracteres, um "0" ou "1" | A declaração da "Classe de contexto de autenticação". Um valor "0" indica que a autenticação do usuário final não atendeu aos requisitos da ISO/IEC 29115. |
| `aio` | Cadeia de caracteres opaca | Uma declaração interna usada pelo Azure AD para registrar os dados para reutilização de token. Os recursos não devem usar essa declaração. |
| `amr` | Matriz de cadeias de caracteres JSON | Presente apenas em tokens da v1.0. Identifica como o assunto do token foi autenticado. Confira [a seção de declarações de amr](#the-amr-claim) para obter mais detalhes. |
| `appid` | Cadeia de caracteres, um GUID | Presente apenas em tokens da v1.0. A ID do aplicativo do cliente que usa o token. O aplicativo pode agir como ele próprio ou em nome de um usuário. A ID do aplicativo normalmente representa um objeto de aplicativo, mas também pode representar um objeto de entidade de serviço no AD do Azure. |
| `appidacr` | "0", "1" ou "2" | Presente apenas em tokens da v1.0. Indica como o cliente foi autenticado. Para um cliente público, o valor é "0". Se a ID do cliente e o segredo do cliente são usados, o valor é "1". Se um certificado do cliente foi usado para autenticação, o valor é "2". |
| `azp` | Cadeia de caracteres, um GUID | Presente apenas em tokens v2.0. A ID do aplicativo do cliente que usa o token. O aplicativo pode agir como ele próprio ou em nome de um usuário. A ID do aplicativo normalmente representa um objeto de aplicativo, mas também pode representar um objeto de entidade de serviço no AD do Azure. |
| `azpacr` | "0", "1" ou "2" | Presente apenas em tokens v2.0. Indica como o cliente foi autenticado. Para um cliente público, o valor é "0". Se a ID do cliente e o segredo do cliente são usados, o valor é "1". Se um certificado do cliente foi usado para autenticação, o valor é "2". |
| `groups` | Matriz JSON de GUIDs | Fornece IDs de objetos que representam as associações de grupo do assunto. Esses valores são exclusivos (consulte a ID de objeto) e podem ser usados com segurança para gerenciar o acesso, como a imposição da autorização para acessar um recurso. Os grupos incluídos na declaração dos grupos são configurados por aplicativo, por meio da propriedade `groupMembershipClaims` do [manifesto do aplicativo](reference-app-manifest.md). Um valor nulo exclui todos os grupos; já um valor "SecurityGroup" inclui somente os membros do grupo de segurança do Active Directory, enquanto um valor "All" inclui tanto grupos de segurança quanto listas de distribuição do Office 365. <br><br>Consulte a declaração `hasgroups` abaixo para obter detalhes de como usar a declaração `groups` com a concessão implícita. <br>Para outros fluxos, se o número de grupos a que o usuário pertence ultrapassar determinado limite (150 para SAML, 200 para JWT), uma declaração excedente será adicionada às fontes de declaração que apontam para o ponto de extremidade do Grafo que contém a lista de grupos do usuário. |
| `hasgroups` | BOOLEAN | Se houver algum, sempre `true`, indicando que o usuário pertence a pelo menos um grupo. Usado no lugar da declaração `groups` de JWTs em fluxos de concessão implícitos se a declaração completa de grupos ultrapassar o fragmento de URI para além dos limites de extensão da URL (atualmente, 6 ou mais grupos). Indica que o cliente deve usar o Graph para determinar os grupos do usuário (`https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects`). |
| `groups:src1` | Objeto JSON | Para solicitações de token sem limite de tamanho (consulte `hasgroups` acima), mas ainda muito grandes para o token, será incluído um link para a lista completa de grupos do usuário. Para JWTs na forma de declaração distribuída, para SAML como uma nova declaração no lugar da declaração `groups`. <br><br>**Valor de exemplo de JWT**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects" }`|
| `preferred_name` | Cadeia de caracteres | Presente apenas em tokens v2.0. O nome de usuário principal que representa o usuário. Ele pode ser um endereço de email, número de telefone ou nome de usuário genérico sem um formato especificado. Seu valor é mutável e pode ser alterado ao longo do tempo. Uma vez que é mutável, esse valor não deve ser usado para tomar decisões de autorização. O escopo `profile` é necessário para receber essa declaração. |
| `name` | Cadeia de caracteres | Fornece um valor legível por humanos que identifica a entidade do token. Não há garantia de que o valor seja exclusivo. Ele é mutável e foi projetado para ser usado apenas para fins de exibição. O escopo `profile` é necessário para receber essa declaração. |
| `oid` | Cadeia de caracteres, um GUID | O identificador imutável de um objeto na plataforma de identidade da Microsoft, nesse caso, uma conta de usuário. Também pode ser usada para realizar verificações de autorização com segurança e como uma chave em tabelas de banco de dados. Essa ID identifica exclusivamente o usuário entre os aplicativos - dois aplicativos diferentes autenticando o mesmo usuário receberão o mesmo valor na declaração `oid`. Portanto, `oid` pode ser usada ao fazer consultas nos serviços online da Microsoft, como o Microsoft Graph. O Microsoft Graph retornará essa ID como a propriedade `id` para uma determinada conta de usuário. Como o `oid` permite que vários aplicativos correlacionem usuários, o escopo `profile` é necessário a fim de receber essa declaração. Observe que, se um único usuário existir em vários locatários, o usuário conterá uma ID de objeto diferentes em cada locatário - são consideradas contas diferentes, mesmo que o usuário faça logon em cada conta com as mesmas credenciais. |
| `rh` | Cadeia de caracteres opaca | Uma declaração interna usada pelo Azure para revalidar tokens. Os recursos não devem usar essa declaração. |
| `scp` | Cadeia de caracteres, uma lista de escopos separados por espaços | O conjunto de escopos expostos pelo aplicativo para o qual o aplicativo cliente solicitou (e recebeu) o consentimento. O aplicativo deve verificar se os escopos são escopos válidos expostos pelo aplicativo e tomar decisões de autorização de acordo com o valor desses escopos. Incluído apenas para [tokens de usuário](#user-and-application-tokens). |
| `roles`| Cadeia de caracteres, lista de permissões separada por espaços | O conjunto de permissões expostos pelo aplicativo para o qual o aplicativo solicitante recebeu permissão de chamar. Isso é usado durante o fluxo de [credenciais do cliente](v1-oauth2-client-creds-grant-flow.md) no lugar de escopos de usuário e só está presente em [tokens de aplicativos](#user-and-application-tokens). |
| `sub` | Cadeia de caracteres, um GUID | O item mais importante sobre o qual o token declara informações, como o usuário de um aplicativo. Esse valor é imutável e não pode ser reatribuído nem reutilizado. Pode ser usado para executar verificações de autorização de forma segura, por exemplo, quando o token é usado para acessar um recurso, e pode ser usado como uma chave nas tabelas de banco de dados. Como a entidade está sempre presente nos tokens emitidos pelo Azure AD, é recomendável usar esse valor em um sistema de autorização de uso geral. O assunto é, no entanto, um identificador de paridade - é exclusivo a uma ID de aplicativo específica. Portanto, se um único usuário entra em dois aplicativos diferentes usando duas IDs de cliente diferentes, esses aplicativos receberão dois valores diferentes para a declaração do assunto. Isso pode ou não ser desejável, dependendo dos requisitos de arquitetura e de privacidade. |
| `tid` | Cadeia de caracteres, um GUID | Representa o locatário do Azure AD do qual o usuário é proveniente. Para contas corporativas e de estudante, o GUID é a ID de locatário imutável da organização à qual o usuário pertence. Para contas pessoais, o valor é `9188040d-6c67-4c5b-b112-36a304b66dad`. O escopo `profile` é necessário para receber essa declaração. |
| `unique_name` | Cadeia de caracteres | Presente apenas em tokens da v1.0. Fornece um valor legível que identifica a entidade do token. Não há garantia de que esse valor seja exclusivo dentro de um locatário e ele deve ser usado apenas para fins de exibição. |
| `upn` | Cadeia de caracteres | O nome de usuário do usuário. Pode ser um número de telefone, um endereço de email ou uma cadeia de caracteres sem formatação. Deve ser usado apenas para fins de exibição e para fornecer dicas de nome de usuário em cenários de reautenticação. |
| `uti` | Cadeia de caracteres opaca | Uma declaração interna usada pelo Azure para revalidar tokens. Os recursos não devem usar essa declaração. |
| `ver` | Cadeia de caracteres, 1.0 ou 2.0 | Indica a versão do token de acesso. |

#### <a name="v10-basic-claims"></a>Declarações básicas v1.0

As declarações a seguir serão incluídas nos tokens v1.0, se aplicável, mas não são incluídas nos tokens v2.0 por padrão. Caso esteja usando a v2.0 e precise de uma dessas declarações, solicite-as usando [declarações opcionais](active-directory-optional-claims.md).

| Declaração | Formatar | DESCRIÇÃO |
|-----|--------|-------------|
| `ipaddr`| Cadeia de caracteres | O endereço IP por meio do qual o usuário se autenticou. |
| `onprem_sid`| Cadeia de caracteres, em [formato SID](https://docs.microsoft.com/windows/desktop/SecAuthZ/sid-components) | Nos casos em que o usuário tem uma autenticação local, essa declaração fornece o SID. Isso pode ser usado para autorização em aplicativos herdados. |
| `pwd_exp`| int, um carimbo de data/hora UNIX | Indica quando a senha do usuário expira. |
| `pwd_url`| Cadeia de caracteres | Uma URL para a qual os usuários podem ser enviados para redefinir suas senhas. |
| `in_corp`|booleano | Indica se o cliente está se conectando da rede corporativa. Caso não esteja, a declaração não será incluída. |
| `nickname`| Cadeia de caracteres | Um nome adicional para o usuário, separado do nome ou sobrenome.|
| `family_name` | Cadeia de caracteres | Fornece o sobrenome ou o nome da família do usuário, conforme definido no objeto de usuário. |
| `given_name` | Cadeia de caracteres | Fornece o nome ou nome especificado do usuário, conforme definido no objeto de usuário. |

#### <a name="the-amr-claim"></a>A declaração `amr`

As identidades da Microsoft podem se autenticadas em uma variedade de maneiras, que podem ser relevantes ao seu aplicativo. A declaração `amr` é uma matriz que pode conter vários itens, como `["mfa", "rsa", "pwd"]`, para uma autenticação que usou uma senha e o aplicativo Authenticator. 

| Valor | DESCRIÇÃO |
|-----|-------------|
| `pwd` | Autenticação de senha, uma senha da Microsoft do usuário ou um segredo do cliente do aplicativo. |
| `rsa` | A autenticação era baseada na prova de uma chave RSA, por exemplo, com o [aplicativo Microsoft Authenticator](https://aka.ms/AA2kvvu). Isso inclui se a autenticação foi realizada por um JWT autoassinado com um certificado X509 de propriedade do serviço. |
| `otp` | Uma senha única usando um email ou uma mensagem de texto. |
| `fed` | Uma declaração de autenticação federada (por exemplo, JWT ou SAML) foi usada. |
| `wia` | Autenticação Integrada do Windows |
| `mfa` | A autenticação multifator for usada. Quando isso estiver presente, os outros métodos de autenticação também serão incluídos. |
| `ngcmfa` | Equivalente a `mfa`, usado para o provisionamento de alguns tipos de credenciais avançadas. |
| `wiaormfa`| O usuário utilizou o Windows ou uma credencial MFA para se autenticar. |
| `none` | Nenhuma autenticação foi executada. |

## <a name="validating-tokens"></a>Validando tokens

Para validar um id_token ou access_token, o aplicativo deverá validar a assinatura e as declarações do token. Para validar os tokens de acesso, o aplicativo também deve validar o emissor, o público-alvo e os tokens de assinatura. Estes precisam ser validados em relação aos valores no documento de descoberta OpenID. Por exemplo, a versão independente de locatário do documento está localizada em [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration). 

O middleware do Azure AD tem funcionalidades internas para validar os tokens de acesso e você pode navegar pelas nossas [amostras](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples) para encontrar uma na linguagem de sua escolha. Para obter mais informações sobre como validar explicitamente um token JWT, confira a [amostra de validação manual de JWT](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation). 

Fornecemos bibliotecas e exemplos de código que mostram como manipular a validação de token com facilidade. As informações abaixo são fornecidas para aqueles que desejam entender o processo subjacente. Também há várias bibliotecas de software livre de terceiros disponíveis para validação de JWT – há, pelo menos, uma opção para quase todas as linguagens e plataformas. Para obter mais informações sobre bibliotecas de autenticação do Azure AD e exemplos de código, confira [bibliotecas de autenticação v1.0](active-directory-authentication-libraries.md) e [bibliotecas de autenticação v2.0](reference-v2-libraries.md).

### <a name="validating-the-signature"></a>Validação da assinatura

Um JWT contém três segmentos, que são separados pelo caractere `.` . O primeiro segmento é conhecido como o **cabeçalho**, o segundo como o **corpo** e o terceiro como a **assinatura**. O segmento de assinatura pode ser usado para validar a autenticidade do token, de modo que seu aplicativo possa confiar nele.

Os tokens emitidos pelo Azure AD são assinados usando algoritmos de criptografia assimétrica padrão do setor, como o RSA 256. O cabeçalho do JWT contém informações sobre o método de criptografia e a chave usados para assinar o token:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

A declaração `alg` indica o algoritmo que foi usado para assinar o token, enquanto a declaração `kid` indica a chave pública privada que foi usada para assinar o token.

Em qualquer ponto no tempo, o AD do Azure pode assinar um id_token usando qualquer um de um determinado conjunto de pares de chaves públicas-privadas. O AD do Azure gira o possível conjunto de chaves em intervalos periódicos, de modo que o aplicativo deve ser escrito para tratar essas mudanças de chave automaticamente. Uma frequência razoável para verificar se há atualizações para as chaves públicas usadas pelo Azure AD é a cada 24 horas.

Você pode adquirir os dados de chave de assinatura necessários para validar a assinatura usando o documento de metadados do OpenID Connect localizado em:

```
https://login.microsoftonline.com/common/.well-known/openid-configuration
```

> [!TIP]
> Experimente essa URL em um navegador!

Esses metadados documentam:

* É um objeto JSON que contém várias informações úteis, como o local dos vários pontos de extremidade necessários para a execução da autenticação do OpenID Connect. 
* Inclui um `jwks_uri`, que fornece o local do conjunto de chaves públicas usadas para assinar tokens. O documento de JSON localizado no `jwks_uri` contém todas as informações de chave pública em uso naquele momento específico. Seu aplicativo pode usar a declaração `kid` no cabeçalho do JWT para selecionar qual chave pública neste documento foi usada para assinar um token específico. Assim, ele pode executar a validação da assinatura usando a chave pública correta e o algoritmo indicado.

> [!NOTE]
> O ponto de extremidade v1.0 retorna as declarações `x5t` e `kid`. A declaração `x5t` está ausente de tokens v2.0. O ponto de extremidade v 2.0 responde com a declaração `kid`. No futuro, é recomendável usar a declaração `kid` para validar o token.

Executar a validação da assinatura está fora do escopo deste documento — há muitas bibliotecas de software livre disponíveis para ajudar você a fazer isso, caso seja necessário.

### <a name="claims-based-authorization"></a>Autorização baseada em declarações

A lógica de negócios do aplicativo determinará esta etapa; alguns métodos comuns de autorização são apresentados abaixo.

* Verifique a declaração `scp` ou `roles` para verificar se todos os escopos presentes correspondem àqueles expostos pela API e permitem que o cliente execute a ação solicitada.
* Verifique se o cliente de chamada pode chamar sua API usando a declaração `appid`.
* Valide o status de autenticação do cliente de chamada usando `appidacr` – ele não deve ser 0 se os clientes públicos não têm permissão para chamar a API.
* Verifique em uma lista das últimas declarações `nonce` para verificar se o token não está sendo reproduzido.
* Verifique se o `tid` corresponde a um locatário que tem permissão para chamar sua API.
* Use a declaração `acr` para verificar se o usuário realizou a MFA. Observe que isso deve ser imposto por meio do [acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).
* Se você solicitou as declarações `roles` ou `groups` no token de acesso, verifique se o usuário está no grupo com permissão para executar essa ação.
  * Para os tokens recuperados usando o fluxo implícito, provavelmente, você precisará consultar o [Graph](https://developer.microsoft.com/graph/) para esses dados, pois eles costumam ser muito grandes para se ajustarem ao token. 

## <a name="user-and-application-tokens"></a>Tokens de usuário e aplicativo

Seu aplicativo pode receber tokens em nome de um usuário (o fluxo normal) ou diretamente de um aplicativo (por meio do [fluxo de credenciais do cliente](v1-oauth2-client-creds-grant-flow.md)). Esses tokens somente de aplicativo indicam que essa chamada é proveniente de um aplicativo e não tem nenhum usuário dando suporte a ela. Esses tokens são manipulados basicamente da mesma forma, com algumas diferenças:

* Os tokens somente de aplicativo não terão uma declaração `scp`; em vez disso, terão uma declaração `roles`. Esse é o local em que a permissão de aplicativo (em vez de permissões delegadas) será registrada. Para obter mais informações sobre permissões delegadas e de aplicativo, confira permissões e consentimento em [v1.0](v1-permissions-and-consent.md) e [v2.0](v2-permissions-and-consent.md).
* Muitas declarações específicas de humanos estarão ausentes, como `name`.

## <a name="token-revocation"></a>Revogação de token

Os tokens de atualização podem ser invalidados ou revogados a qualquer momento por vários motivos. Eles se encaixam em duas categorias principais: tempos limite e revogações.

### <a name="token-timeouts"></a>Tempos limite de token

* MaxInactiveTime: Se o token de atualização não foi usado no tempo determinado pelo MaxInactiveTime, o Token de atualização não será válido. 
* MaxSessionAge: se MaxAgeSessionMultiFactor ou MaxAgeSessionSingleFactor tiver sido definido como algo diferente do padrão (até revogado), será necessária a reautenticação depois de decorrido o tempo definido em MaxAgeSession*. 
* Exemplos:
  * O locatário tem uma MaxInactiveTime de 5 dias e o usuário entrou em férias por uma semana e então AAD não obteve uma nova solicitação de token do usuário em 7 dias. Na próxima vez que o usuário solicitar um novo token, ele verá que seu Token de atualização foi revogado e deverá inserir suas credenciais novamente.
  * Um aplicativo confidencial tem um MaxAgeSessionSingleFactor de 1 dia. Se um usuário fizer logon na segunda-feira e na terça-feira (após decorridas 25 horas), ele deverá se autenticar novamente.

### <a name="revocation"></a>Revogação

|   | Cookie baseado em senha | Token baseado em senha | Cookie não baseado em senha | Token não baseado em senha | Token de cliente confidencial| 
|---|-----------------------|----------------------|---------------------------|--------------------------|--------------------------|
| A senha expira | Permanece ativo| Permanece ativo | Permanece ativo | Permanece ativo | Permanece ativo |
| Senha alterada pelo usuário | Revogado | Revogado | Permanece ativo | Permanece ativo | Permanece ativo |
| Usuário faz SSPR | Revogado | Revogado | Permanece ativo | Permanece ativo | Permanece ativo |
| Administrador redefine senha | Revogado | Revogado | Permanece ativo | Permanece ativo | Permanece ativo |
| Usuário revoga seus tokens de atualização [por meio do PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) | Revogado | Revogado |Revogado | Revogado |Revogado | Revogado |
| O administrador revoga todos os tokens de atualização do locatário [por meio do PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken) | Revogado | Revogado |Revogado | Revogado |Revogado | Revogado |
| [Logoff único](v1-protocols-openid-connect-code.md#single-sign-out) na Web | Revogado | Permanece ativo | Revogado | Permanece ativo | Permanece ativo |

> [!NOTE]
> Um logon "não baseado em senha" é um logon em que o usuário não digita uma senha para entrar. Por exemplo, usando seu rosto no Windows Hello, uma chave FIDO ou um PIN. 
>
> Existe um problema conhecido com o Token de atualização primário do Windows. Se o PRT for obtido por meio de uma senha e, em seguida, o usuário fizer logon usando o Hello, isso não alterará a origem do PRT e será revogado se o usuário alterar sua senha.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [`id_tokens` no Azure AD](id-tokens.md).
* Saiba mais sobre permissões e consentimento em [v1.0](v1-permissions-and-consent.md) e [v2.0](v2-permissions-and-consent.md).
