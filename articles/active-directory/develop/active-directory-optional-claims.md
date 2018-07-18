---
title: Saiba como fornecer declarações opcionais para o aplicativo Azure AD | Microsoft Docs
description: Um guia para adicionar declarações adicionais ou personalizadas aos tokens SAML 2.0 e JSON Web Tokens (JWT) emitidos pelo Azure Active Directory.
documentationcenter: na
author: CelesteDG
services: active-directory
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: ffd774477881be6b7f46dd38bbc88c8d019223aa
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2018
ms.locfileid: "36317197"
---
# <a name="optional-claims-in-azure-ad-preview"></a>Declarações opcionais no Azure AD (versão prévia)

Esse recurso é usado por desenvolvedores de aplicativos para especificar quais declarações eles querem em tokens enviados para o aplicativo. Você pode usar declarações opcionais para:
-   Selecione declarações adicionais para incluir nos tokens para o aplicativo.
-   Altere o comportamento de determinadas declarações que o Azure AD retorna em tokens.
-   Adicione e acesse as declarações personalizadas para o aplicativo. 

> [!Note]
> Atualmente, essa capacidade está em visualização pública. Esteja preparado para reverter ou remover quaisquer alterações. O recurso está disponível em qualquer assinatura do Azure AD durante a visualização pública. No entanto, quando o recurso for disponibilizado para todos, alguns aspectos dele poderão exigir uma assinatura do Azure AD Premium.

Para obter a lista de declarações padrão e como elas são usadas nos tokens, confira [Noções básicas de tokens emitidos pelo Azure AD](active-directory-token-and-claims.md). 

Uma das metas do [ponto de extremidade v2.0 do Azure AD](active-directory-appmodel-v2-overview.md) é obter tamanhos menores de token para garantir um ótimo desempenho pelos clientes.  Como resultado, várias declarações anteriormente incluídas em tokens de acesso e ID não estão mais presentes nos tokens v2.0 e devem ser solicitadas especificamente, por aplicativo.  

**Tabela 1: Aplicabilidade**

| Tipo de Conta | Ponto de extremidade V1.0                      | Ponto de extremidade V2.0  |
|--------------|------------------------------------|----------------|
| MSA          | N/D ‒ são os usados os tíquetes RPS | Suporte em breve |
| AAD          | Com suporte                          | Com suporte      |

## <a name="standard-optional-claims-set"></a>Conjunto de declarações opcional padrão
O conjunto de declarações opcionais disponíveis por padrão para uso pelos aplicativos é listado abaixo.  Para adicionar declarações opcionais personalizadas para o aplicativo, confira [Extensões de Diretório](active-directory-optional-claims.md#Configuring-custom-claims-via-directory-extensions), abaixo. 

> [!Note]
>A maioria dessas declarações pode ser incluída em JWTs, mas não os tokens SAML, exceto quando indicado na coluna Tipo de Token.  Além disso, embora declarações opcionais tenham suporte apenas para usuários do AAD no momento, o suporte para MSA está sendo adicionado.  Quando a MSA tem suporte para declarações opcionais no ponto de extremidade v2.0, a coluna de tipo de usuário indica se uma declaração está disponível para um usuário do AAD ou MSA.  

**Tabela 2: Conjunto de declarações opcionais padrão**

| NOME                     | DESCRIÇÃO                                                                                                                                                                                     | Tipo de token | Tipo de Usuário | Observações                                                                                                                                                                                                                                                                                   |
|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------|-----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `auth_time`                | Hora em que o usuário foi autenticado pela última vez.  Confira especificações de OpenID Connect.                                                                                                                                | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `tenant_region_scope`      | Região do locatário do recurso                                                                                                                                                                   | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `signin_state`             | Entrar no estado de declaração                                                                                                                                                                             | JWT        |           | Seis valores de retorno, como sinalizadores:<br> "dvc_mngd": o dispositivo é gerenciado<br> "dvc_cmp": o dispositivo está em conformidade<br> "dvc_dmjd": o dispositivo ingressou no domínio<br> "dvc_mngd_app": o dispositivo é gerenciado por meio do MDM<br> "inknownntwk": o dispositivo está dentro de uma rede conhecida.<br> "kmsi": Manter-me Conectado foi usado. <br> |
| `controls`                 | Declaração com vários valores e que contém os controles de sessão impostos pelas políticas de Acesso Condicional.                                                                                                       | JWT        |           | Três valores:<br> "app_res": o aplicativo deve impor restrições mais granulares. <br> "ca_enf": a imposição de Acesso Condicional foi adiada e ainda é necessária. <br> "no_cookie": este token é insuficiente para troca para um cookie no navegador. <br>                              |
| `home_oid`                 | Para usuários convidados, a ID de objeto do usuário no locatário inicial do usuário.                                                                                                                           | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `sid`                      | ID de sessão usada para a saída de usuário por sessão.                                                                                                                                                  | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `platf`                    | Plataforma de dispositivos                                                                                                                                                                                 | JWT        |           | Restrito aos dispositivos gerenciados que podem verificar o tipo de dispositivo.                                                                                                                                                                                                                              |
| `verified_primary_email`   | Originado de PrimaryAuthoritativeEmail do usuário                                                                                                                                               | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `verified_secondary_email` | Originado de SecondaryAuthoritativeEmail do usuário                                                                                                                                             | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `enfpolids`                | IDs de política aplicada. Uma lista de IDs de política que foram avaliadas para o usuário atual.                                                                                                         | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `vnet`                     | Informações de especificador de VNET.                                                                                                                                                                     | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `fwd`                      | Endereço IP.  Adiciona o endereço IPv4 original do cliente solicitante (quando dentro de uma VNET)                                                                                                       | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `ctry`                     | O país do usuário                                                                                                                                                                                  | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `tenant_ctry`              | País do locatário de recursos                                                                                                                                                                       | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `acct`    | Status da conta de usuários no locatário.  Se o usuário for um membro do locatário, o valor será `0`.  Se eles forem convidado, o valor é `1`.  | JWT, SAML | | |
| `upn`                      | Declaração UserPrincipalName.  Embora essa declaração seja incluída automaticamente, você pode especificá-la como uma declaração opcional para anexar propriedades adicionais a fim de modificar seu comportamento, no caso do usuário convidado. | JWT, SAML  |           | Propriedades adicionais: <br> `include_externally_authenticated_upn` <br> `include_externally_authenticated_upn_without_hash`                                                                                                                                                                 |

### <a name="v20-optional-claims"></a>Declarações opcionais V2.0
Essas declarações são sempre incluídas em tokens da v1.0, mas são removidas de tokens da v2.0, a menos que solicitado.  Essas declarações só são aplicáveis a JWTs (tokens de ID e Tokens de Acesso).  

**Tabela 3: Somente declarações V2.0 opcionais**

| Declaração JWT     | NOME                            | DESCRIÇÃO                                                                                                                    | Observações |
|---------------|---------------------------------|--------------------------------------------------------------------------------------------------------------------------------|-------|
| `ipaddr`      | Endereço IP                      | O endereço IP com o qual o cliente se conectou.                                                                                      |       |
| `onprem_sid`  | Identificador de Segurança Local |                                                                                                                                |       |
| `pwd_exp`     | Hora da Expiração da Senha        | A data e a hora em que a senha expira.                                                                                    |       |
| `pwd_url`     | Alterar URL da Senha             | Uma URL que o usuário pode acessar para alterar sua senha.                                                                        |       |
| `in_corp`     | Dentro da Rede Corporativa        | Indica se o cliente está se conectando da rede corporativa. Caso não esteja, a declaração não será incluída                     |       |
| `nickname`    | Apelido                        | Um nome adicional para o usuário, separado do nome ou sobrenome.                                                             |       |                                                                                                                |       |
| `family_name` | Sobrenome                       | Fornece o último nome, o sobrenome ou o nome da família do usuário conforme definido no objeto de usuário do Azure AD. <br>"family_name":"Barros" |       |
| `given_name`  | Nome                      | Fornece o nome ou nome “determinado” do usuário, como definido no objeto de usuário do Azure AD.<br>"given_name": "Davi"                   |       |

### <a name="additional-properties-of-optional-claims"></a>Propriedades adicionais de declarações opcionais

Algumas declarações opcionais podem ser configuradas para alterar o modo como a declaração é retornada.  Essas propriedades adicionais são usadas principalmente para ajudar a migração de aplicativos locais com expectativas de dados diferentes (por exemplo, `include_externally_authenticated_upn_without_hash` ajuda com clientes que não podem manipular marcas (`#`) no UPN)

**Tabela 4: Valores de configuração para declarações opcionais padrão**

| Nome da propriedade                                     | Nome de Propriedade Adicional                                                                                                             | DESCRIÇÃO |
|---------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-------------|
| `upn`                                                 |                                                                                                                                      |             |
| | `include_externally_authenticated_upn`              | Inclui o UPN de convidado conforme armazenado no locatário do recurso.  Por exemplo, `foo_hometenant.com#EXT#@resourcetenant.com`                            |             
| | `include_externally_authenticated_upn_without_hash` | Igual ao que é indicado acima, exceto que as marcas de hash (`#`) são substituídas por sublinhados (`_`), por exemplo `foo_hometenant.com_EXT_@resourcetenant.com` |             

> [!Note]
>Especificar a declaração de upn opcional sem uma propriedade adicional não altera qualquer comportamento. Para ver uma nova declaração emitida no token, pelo menos uma das propriedades adicionais deve ser adicionada. 


#### <a name="additional-properties-example"></a>Exemplo de propriedades adicionais:

```json
 "optionalClaims": 
   {
       "idToken": [ 
             { 
                "name": "upn", 
            "essential": false,
                "additionalProperties": [ "include_externally_authenticated_upn"]  
              }
        ]
}
```

Esse objeto OptionalClaims faz com que o token de ID retornado ao cliente inclua outro UPN com o locatário de início adicional e as informações do locatário do recurso.  

## <a name="configuring-optional-claims"></a>Como configurar as declarações opcionais

Você pode configurar declarações opcionais para o aplicativo modificando o manifesto do aplicativo (veja o exemplo abaixo). Para saber mais, confira o [artigo Noções básicas sobre o manifesto do aplicativo Azure AD](active-directory-application-manifest.md).

**Esquema de exemplo:**

```json
"optionalClaims":  
   {
       "idToken": [
             { 
                   "name": "upn", 
                   "essential": false, 
                   "additionalProperties": [ "include_externally_authenticated_upn"]  
              }
        ],
 "accessToken": [ 
             {
                    "name": "auth_time", 
                    "essential": false
              }
        ],
"saml2Token": [ 
              { 
                    "name": "upn", 
                    "essential": true
               },
               { 
                    "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                    "source": "user", 
                    "essential": true
               }
       ]
   }
```

### <a name="optionalclaims-type"></a>Tipo OptionalClaims

Declara as declarações opcionais solicitadas por um aplicativo. Um aplicativo pode configurar as declarações opcionais a serem retornadas em cada um dos três tipos de tokens (token de ID, token de acesso, token SAML 2) que pode receber do serviço de token de segurança. O aplicativo pode configurar um conjunto de declarações opcionais a serem retornadas em cada tipo de token diferente. A propriedade OptionalClaims da entidade do aplicativo é um objeto OptionalClaims.

**Tabela 5: Propriedades do tipo OptionalClaims**

| NOME        | type                       | DESCRIÇÃO                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Coleção (OptionalClaim) | As declarações opcionais retornadas no token de ID JWT.     |
| `accessToken` | Coleção (OptionalClaim) | As declarações opcionais retornadas no token de acesso JWT. |
| `saml2Token`  | Coleção (OptionalClaim) | As declarações opcionais retornadas no token SAML.       |


### <a name="optionalclaim-type"></a>Tipo OptionalClaim

Contém uma declaração opcional associada a um aplicativo ou uma entidade de segurança. As propriedades idToken, accessToken e saml2Token do tipo [OptionalClaims](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) são uma coleção de OptionalClaim.
Caso haja suporte por uma declaração específica, você também poderá modificar o comportamento de OptionalClaim usando o campo AdditionalProperties.

**Tabela 6: Propriedades do tipo OptionalClaim**

| NOME                 | type                    | DESCRIÇÃO                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | O nome da declaração opcional.                                                                                                                                                                                                                                                                               |
| `source`               | Edm.String              | A origem (objeto de diretório) da declaração. Há declarações predefinidas e definidas pelo usuário de propriedades de extensão. Se o valor de origem for nulo, a declaração será uma declaração opcional predefinida. Se o valor de origem for um usuário, o valor na propriedade name será a propriedade de extensão do objeto de usuário. |
| `essential`            | Edm.Boolean             | Se o valor for true, a declaração especificada pelo cliente será necessária para garantir uma experiência de autorização sem problemas para a tarefa específica solicitada pelo usuário final. O valor padrão é falso.                                                                                                                 |
| `additionalProperties` | Coleção (Edm.String) | Propriedades adicionais da declaração. Se uma propriedade existir na coleção, ela modificará o comportamento da declaração opcional especificado na propriedade name.                                                                                                                                                   |

## <a name="configuring-custom-claims-via-directory-extensions"></a>Como configurar declarações personalizadas por meio de extensões de diretório

Além do conjunto de declarações opcional padrão, os tokens também podem ser configurados para incluir extensões de esquema de diretório (confira o [artigo Extensões de esquema de diretório](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) para saber mais).  Esse recurso é útil para anexar informações adicionais do usuário que o aplicativo pode usar; por exemplo, um identificador adicional ou uma opção de configuração importante que o usuário configurou. 

> [!Note]
> As extensões de esquema de diretório são um recurso somente do AAD. Portanto, se o manifesto do aplicativo solicitar uma extensão personalizada e um usuário de MSA fizer logon no aplicativo, essas extensões não serão retornadas. 

### <a name="values-for-configuring-additional-optional-claims"></a>Valores de configuração de declarações opcionais adicionais 

Para atributos de extensão, use o nome completo da extensão (no formato: `extension_<appid>_<attributename>`) no manifesto do aplicativo. O `<appid>` deve corresponder à id do aplicativo que está solicitando a declaração. 

No JWT, essas declarações serão emitidas com o seguinte formato de nome: `extn.<attributename>`.

Em tokens SAML, essas declarações serão emitidas com o seguinte formato de URI: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="optional-claims-example"></a>Exemplo de declarações opcional

Nesta seção, você pode examinar um cenário para ver como usar o recurso opcional de declarações para o aplicativo.
Há várias opções disponíveis para atualizar as propriedades na configuração de identidade do aplicativo para habilitar e configurar declarações opcionais:
-   Você pode modificar o manifesto do aplicativo. O exemplo a seguir usará esse método para executar a configuração. Primeiro leia o [documento Noções básicas sobre o manifesto do aplicativo Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) para obter uma introdução ao manifesto.
-   Também é possível escrever um aplicativo que usa a [API do Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) para atualizar o aplicativo. A [Referência de tipo complexo e de entidade](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) ne guia de referência da API do Graph pode ajudar você a configurar as declarações opcionais.

**Exemplo:** no exemplo a seguir, você modificará o manifesto do aplicativo para adicionar declarações a tokens de ID, acesso e SAML destinados ao aplicativo.
1.  Entre no [Portal do Azure](https://portal.azure.com).
2.  Depois de autenticado, escolha o locatário do Azure AD selecionando-o no canto superior direito da página.
3.  Escolha a extensão **Azure AD** no painel de navegação esquerdo e clique em **Registros de Aplicativo**.
4.  Localize o aplicativo para o qual você deseja configurar declarações opcionais na lista e clique nele.
5.  Na página do aplicativo, clique em **Manifesto** para abrir o editor de manifesto embutido. 
6.  Você pode editar diretamente o manifesto usando esse editor. O manifesto segue o esquema para [Entidade de aplicativo](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity)e formata automaticamente o manifesto quando é salvo. Novos elementos serão adicionados à propriedade `OptionalClaims`.

      ```json
      "optionalClaims": 
      {
            "idToken": [ 
                  { 
                        "name": "upn", 
                        "essential": false, 
                        "additionalProperties": [ "include_externally_authenticated_upn"]  
                  }
            ],
      "accessToken": [ 
                  {
                        "name": "auth_time", 
                        "essential": false
                  }
            ],
      "saml2Token": [ 
                  { 
                        "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                        "source": "user", 
                        "essential": true
                  }
            ]
      }
      ```
      Nesse caso, diferentes declarações opcionais foram adicionadas a cada tipo de token que o aplicativo pode receber. Os tokens de ID agora contêm o UPN para usuários federados no formato completo (`<upn>_<homedomain>#EXT#@<resourcedomain>`). Agora os tokens de acesso receberão a declaração auth_time. Agora os tokens SAML contêm a extensão do esquema de diretório skypeId (neste exemplo, a ID de aplicativo para esse aplicativo é ab603c56068041afb2f6832e2a17e237).  Os tokens SAML vão expor a ID do Skype como `extension_skypeId`.

7.  Quando terminar de atualizar o manifesto, clique em **Salvar** para salvar o manifesto


## <a name="related-content"></a>Conteúdo relacionado
* Saiba mais sobre as [declarações padrão](active-directory-token-and-claims.md) fornecidas pelo Azure AD. 
