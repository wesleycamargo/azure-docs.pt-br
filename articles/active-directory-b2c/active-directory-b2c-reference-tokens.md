---
title: Visão geral dos tokens - Azure Active Directory B2C | Microsoft Docs
description: Saiba mais sobre os tokens usados no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 11361bc6ab75e873e1b4081dcfc6492abc093b54
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680257"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>Visão geral dos tokens no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure B2C de diretório Active Directory (Azure AD) B2C emite vários tipos de tokens de segurança ao processar cada [fluxo de autenticação](active-directory-b2c-apps.md). Este documento descreve o formato, as características de segurança e o conteúdo de cada tipo de token.

## <a name="token-types"></a>Tipos de token

B2C do AD do Azure dá suporte a [protocolos OAuth 2.0 e OpenID Connect](active-directory-b2c-reference-protocols.md), que usa tokens de autenticação e acesso seguro aos recursos. São todos os tokens utilizados no Azure AD B2C [tokens web JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) que contêm asserções de informações sobre o portador e o assunto do token.

Os seguintes tokens são usados na comunicação com o Azure AD B2C:

- *Token de ID* -um JWT que contém declarações que você pode usar para identificar usuários em seu aplicativo. Esse token é enviado com segurança em solicitações HTTP para comunicação entre dois componentes do mesmo aplicativo ou serviço. Você pode usar as declarações em um token de ID como desejar. Normalmente, eles são usados para exibir informações de conta ou para tomar decisões de controle de acesso em um aplicativo. Tokens de ID são assinados, mas não são criptografados. Quando seu aplicativo ou a API recebe um token de ID, ele deve validar a assinatura para provar que o token é autêntico. Seu aplicativo ou a API também deve validar algumas declarações no token para provar que ele é válido. Dependendo dos requisitos do cenário, as declarações validadas por um aplicativo podem variar, mas seu aplicativo deve executar algumas validações de declaração comuns em cada cenário.
- *Token de acesso* -um JWT que contém declarações que você pode usar para identificar as permissões concedidas às suas APIs. Tokens de acesso são assinados, mas eles não são criptografados. Tokens de acesso são usados para fornecer acesso aos servidores APIs e recursos.  Quando sua API recebe um token de acesso, ele deve validar a assinatura para provar que o token é autêntico. Sua API também deve validar algumas declarações no token para provar que ele é válido. Dependendo dos requisitos do cenário, as declarações validadas por um aplicativo podem variar, mas seu aplicativo deve executar algumas validações de declaração comuns em cada cenário.
- *Token de atualização* -tokens de atualização são usados para adquirir novos tokens de ID e tokens de acesso em um fluxo OAuth 2.0. Eles fornecem o seu aplicativo com acesso de longo prazo a recursos em nome dos usuários sem a necessidade de interação com os usuários. Tokens de atualização são opacos para seu aplicativo. Eles são emitidos pelo Azure AD B2C e podem ser inspecionados e interpretados apenas pelo Azure AD B2C. Eles têm longa duração, mas seu aplicativo não deve ser escrito com a expectativa de que um token de atualização durará por um período específico. Os tokens de atualização podem ser invalidados a qualquer momento por vários motivos. A única maneira para seu aplicativo para saber se um token de atualização é válido é tentando resgatá-lo fazendo uma solicitação de token do Azure AD B2C. Ao resgatar um token de atualização para um novo token, você receberá um novo token de atualização na resposta de token. Salve o novo token de atualização. Ele substitui o token de atualização que você usou anteriormente na solicitação. Essa ação ajuda a garantir que seus tokens de atualização permaneçam válidos pelo máximo tempo possível. 

## <a name="endpoints"></a>Pontos de extremidade

Um [registrado o aplicativo](tutorial-register-applications.md) recebe tokens e se comunica com o Azure AD B2C, enviando solicitações para esses pontos de extremidade:

- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize`
- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token`

Tokens de segurança que seu aplicativo recebe do Azure AD B2C podem vir do `/authorize` ou `/token` pontos de extremidade. Quando os tokens de ID são adquiridos do `/authorize` ponto de extremidade, são feitos usando o [fluxo implícito](active-directory-b2c-reference-spa.md), que geralmente é usado para os usuários entrarem em aplicativos web baseados em javascript. Quando os tokens de ID são adquiridos do ponto de extremidade `/token`, isso é feito usando o [fluxo de código confidencial](active-directory-b2c-reference-oidc.md), que mantém o token escondido do navegador.

## <a name="claims"></a>Declarações

Ao usar o Azure AD B2C, você tem um controle refinado sobre o conteúdo de seus tokens. Você pode configurar [fluxos de usuário](active-directory-b2c-reference-policies.md) e [políticas personalizadas](active-directory-b2c-overview-custom.md) para enviar determinados conjuntos de dados do usuário em declarações que são necessárias para seu aplicativo. Essas declarações podem incluir propriedades padrão, como **displayName** e **emailAddress**. Seus aplicativos podem usar essas declarações para autenticar usuários e solicitações com segurança. 

As declarações em tokens de ID não são retornadas em uma ordem específica. Novas declarações podem ser introduzidas em tokens de ID a qualquer momento. Seu aplicativo não deve quebrar conforme novas declarações são introduzidas. Você também pode incluir [atributos de usuário personalizados](active-directory-b2c-reference-custom-attr.md) em suas declarações.

A tabela a seguir lista as declarações que você pode esperar nos tokens de ID e acessar os tokens emitidos pelo Azure AD B2C.

| NOME | Declaração | Valor de exemplo | DESCRIÇÃO |
| ---- | ----- | ------------- | ----------- |
| Público-alvo | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Identifica o destinatário pretendido do token. Para B2C do AD do Azure, o público-alvo é a ID do aplicativo. Seu aplicativo deve validar esse valor e rejeitar o token se ele não corresponder. Público-alvo é sinônimo de recurso. |
| Emissor | `iss` |`https://{tenant}.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Identifica o STS (serviço de token de segurança) que constrói e retorna o token. Ele também identifica o diretório no qual o usuário foi autenticado. Seu aplicativo deve validar a declaração do emissor para certificar-se de que o token venha do ponto de extremidade apropriado. |
| Emitido em | `iat` | `1438535543` | A hora em que o token foi emitido, representada na época. |
| Hora de expiração | `exp` | `1438539443` | A hora em que o token se torna inválido, representada na época. Seu aplicativo deve usar essa declaração para verificar a validade da vida útil do token. |
| Não antes de | `nbf` | `1438535543` | O horário em que o token se torna inválido, representado no horário da época. Esse tempo é geralmente o mesmo que o tempo que o token foi emitido. Seu aplicativo deve usar essa declaração para verificar a validade da vida útil do token. |
| Versão | `ver` | `1.0` | A versão do token de ID, conforme definido pelo Azure AD B2C. |
| Hash de código | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Um hash de código incluído em um token de ID apenas quando o token é emitido junto com um código de autorização do OAuth 2.0. Um hash de código pode ser usado para validar a autenticidade de um código de autorização. Para obter mais informações sobre como realizar essa validação, consulte o [especificação do OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html).  |
| Hash de token de acesso | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Um hash de token de acesso incluído em um token de ID apenas quando o token é emitido junto com um token de acesso OAuth 2.0. Um hash de token de acesso pode ser usado para validar a autenticidade de um token de acesso. Para obter mais informações sobre como realizar essa validação, consulte o [especificação do OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html)  |
| Nonce | `nonce` | `12345` | Um nonce é uma estratégia usada para reduzir ataques de reprodução de token. Seu aplicativo pode especificar um nonce em uma solicitação de autorização usando o `nonce` parâmetro de consulta. O valor que você fornece na solicitação é emitido sem modificações no `nonce` de declaração de um token de ID. Esta declaração permite que seu aplicativo verificar se o valor com relação ao valor especificado na solicitação. O aplicativo deve executar essa validação durante o processo de validação de token de ID. |
| Assunto | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | A entidade de segurança sobre o qual o token declara informações, como o usuário de um aplicativo. Esse valor é imutável e não pode ser reatribuído nem reutilizado. Ele pode ser usado para executar verificações de autorização com segurança, por exemplo, quando o token é usado para acessar um recurso. Por padrão, a declaração de entidade é preenchida com a ID de objeto do usuário no diretório. |
| Referência de classe de contexto de autenticação | `acr` | Não aplicável | Usado apenas com políticas mais antigas. |
| Política de estrutura confiável | `tfp` | `b2c_1_signupsignin1` | O nome da política que foi usada para adquirir o token de ID. |
| Hora da autenticação | `auth_time` | `1438535543` | A hora em que um usuário entrou pela última vez credenciais, representada na época. |
| Escopo | `scp` | `Read`| As permissões concedidas ao recurso para um token de acesso. Várias permissões concedidas são separadas por um espaço. |
| Entidade Autorizada | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | A **ID do aplicativo** do aplicativo cliente que iniciou a solicitação. |

## <a name="configuration"></a>Configuração

As propriedades a seguir são usadas para [gerenciar tempos de vida de tokens de segurança](configure-tokens.md) emitidos pelo Azure AD B2C:

- **Duração dos tokens de acesso e ID (minutos)**: o tempo de vida do token portador do OAuth 2.0 usado para obter acesso a um recurso protegido. O padrão é 60 minutos. O mínimo (inclusive) é de 5 minutos. O máximo (inclusive) é 1440 minutos.

- **Vida útil do token de atualização (dias)** – o período de tempo máximo antes que um token de atualização pode ser usado para adquirir um novo acesso ou token de ID. O período de tempo também aborda a aquisição de um novo token de atualização se seu aplicativo tiver sido concedido a `offline_access` escopo. O padrão é 14 dias. O mínimo (inclusive) é um dia. O máximo (inclusive) é de 90 dias.

- **Tempo de vida de janela deslizante token de atualização (dias)** – após o término do período esse tempo o usuário é forçado a autenticar novamente, independentemente do período de validade da mais recente adquirido pelo aplicativo de token de atualização. Ele só poderá ser fornecido se a opção estiver definida como **Bounded**. Ele precisa ser maior ou igual ao valor **Vida útil do token de atualização (dias)** . Se a opção estiver definida como **Unbounded**, você não poderá fornecer um valor específico. O padrão é de 90 dias. O mínimo (inclusive) é um dia. O máximo (inclusive) é 365 dias.

Os casos de uso a seguir são ativados usando estas propriedades:

- Permitir que um usuário permaneça conectado a um aplicativo móvel indefinidamente, contanto que o usuário esteja continuamente ativo no aplicativo. Você pode definir **Atualizar duração da janela deslizante do token (dias)** para **Não consolidado** em seu fluxo de usuário.
- Atenda aos requisitos de conformidade e segurança de seu setor definindo a vida de tokens de acesso adequadamente.

Essas configurações não estão disponíveis para os fluxos de usuário de redefinição de senha. 

## <a name="compatibility"></a>Compatibilidade

As propriedades a seguir são usadas para [gerenciar a compatibilidade de token](configure-tokens.md):

- **Emissor (iss) reivindicação** - essa propriedade identifica o locatário do Azure AD B2C que emitiu o token. O valor padrão é `https://<domain>/{B2C tenant GUID}/v2.0/`. O valor de `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` inclui ID para o locatário do Azure AD B2C e o fluxo de usuário que foi usado na solicitação de token. Se seu aplicativo ou biblioteca precisa do Azure AD B2C para estar em conformidade com o [especificação de OpenID Connect Discovery 1.0](https://openid.net/specs/openid-connect-discovery-1_0.html), use esse valor.

- **Subject (sub) declaração** - Esta propriedade identifica a entidade para a qual o token afirma informações. O valor padrão é **ObjectID**, que preenche o `sub` de declaração no token com a ID de objeto do usuário. O valor de **não tem suporte** é fornecida somente para compatibilidade com versões anteriores. É recomendável que você alterne para **ObjectID** assim que for possível.

- **Declaração que representa a ID de política** -essa propriedade identifica o tipo de declaração na qual o nome da política usado na solicitação de token é preenchido. O valor padrão é `tfp`. O valor de `acr` é fornecida somente para compatibilidade com versões anteriores.

## <a name="pass-through"></a>Passagem

Quando um percurso do usuário é iniciado, o Azure AD B2C recebe um token de acesso de um provedor de identidade. O Azure Active Directory B2C usa esse token para recuperar informações sobre o usuário. Você [habilitar uma declaração no seu fluxo de usuário](idp-pass-through-user-flow.md) ou [definir uma declaração em sua política personalizada](idp-pass-through-custom.md) para passar o token por meio para os aplicativos que você se registrar no Azure AD B2C. Seu aplicativo deve estar usando um [fluxo de usuário v2](user-flow-versions.md) para tirar proveito de passar o token como uma declaração.

B2C do AD do Azure atualmente suporta apenas passando o token de acesso OAuth 2.0 de provedores de identidade, que incluem o Facebook e Google. Para todos os outros provedores de identidade, a declaração é retornada em branco. 

## <a name="validation"></a>Validação

Para validar um token, o aplicativo deve verificar a assinatura e declarações do token. Muitas bibliotecas de software livre estão disponíveis para validar JWTs, dependendo do idioma de sua preferência. É recomendável que você explore essas opções em vez de implementa sua própria lógica de validação.

### <a name="validate-signature"></a>Validar assinatura

Um JWT contém três segmentos, uma *cabeçalho*, um *corpo*e um *assinatura*. O segmento de assinatura pode ser usado para validar a autenticidade do token para que ele pode ser confiável para seu aplicativo. Os tokens do Azure AD B2C são assinados usando algoritmos de criptografia assimétrica padrão do setor, como o RSA 256. 

O cabeçalho do token contém informações sobre o método de criptografia e a chave usados para assinar o token:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

O valor de **alg** declaração é o algoritmo que foi usado para assinar o token. O valor de **kid** declaração é a chave pública que foi usada para assinar o token. Em um determinado momento, o Azure AD B2C pode assinar um token usando qualquer um de um conjunto de pares de chaves pública / privada. B2C do AD do Azure gira o possível conjunto de chaves periodicamente. Seu aplicativo deve ser escrito para tratar essas mudanças de chave automaticamente. Uma frequência razoável para verificar se há atualizações para as chaves públicas usadas pelo Azure AD B2C é a cada 24 horas.

O Azure AD B2C tem um ponto de extremidade de metadados OpenID Connect. Usando esse ponto de extremidade, os aplicativos podem solicitar informações sobre o Azure AD B2C em tempo de execução. Essas informações incluem pontos de extremidade, conteúdos de token e chaves de assinatura de token. Seu locatário do Azure AD B2C contém um documento de metadados JSON para cada política. O documento de metadados é um objeto JSON que contém várias informações úteis. Contém os metadados **jwks_uri**, que fornece o local do conjunto de chaves públicas que são usados para assinar tokens. Melhor buscar o local dinamicamente usando o documento de metadados e de análise do que o local é fornecido aqui, mas ele **jwks_uri**:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_signupsignin1
```
O documento JSON localizado nessa URL contém todas as informações de chave pública em uso em um momento específico. O aplicativo pode usar a declaração `kid` no cabeçalho do JWT para selecionar a chave pública no documento JSON que é usada para assinar um token específico. Assim, ele pode executar a validação da assinatura usando a chave pública correta e o algoritmo indicado.

O documento de metadados para o `B2C_1_signupsignin1` diretiva no `contoso.onmicrosoft.com` locatário está localizado em:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_signupsignin1
```

Para determinar qual política foi usada para assinar um token (e para onde ir para solicitar os metadados), você tem duas opções. Primeiro, o nome da política é incluído na declaração `acr` no token. Você pode analisar as declarações fora do corpo do JWT decodificando em base 64 o corpo e desserializando a cadeia de caracteres JSON resultante. O `acr` declaração é o nome da política que foi usado para emitir o token. A outra opção é codificar a política no valor da `state` parâmetro quando você emite a solicitação e, em seguida, decodificá-lo para determinar qual política foi usada. Ambos os métodos são válidos.

Uma descrição de como executar a validação de assinatura está fora do escopo deste documento. Muitas bibliotecas de software livre estão disponíveis para ajudá-lo a validar um token.

### <a name="validate-claims"></a>Validar declarações

Quando seus aplicativos ou a API recebe um token de ID, ele também deve executar diversas verificações nas declarações no token de ID. As seguintes declarações devem ser verificadas:

- **público-alvo** -verifica se o token de ID foi destinado a ser dado ao seu aplicativo.
- **não antes** e **tempo de expiração** -verifica se o token de ID não expirou.
- **emissor** -verifica se o token foi emitido para seu aplicativo pelo Azure AD B2C.
- **nonce** -uma estratégia de mitigação de ataques de reprodução de token.

Para obter uma lista completa das validações de seu aplicativo deve executar, consulte o [especificação do OpenID Connect](https://openid.net).  

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como [usar tokens de acesso](active-directory-b2c-access-tokens.md).

