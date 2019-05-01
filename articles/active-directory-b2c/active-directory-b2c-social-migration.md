---
title: Migrar usuários com identidades sociais no Azure Active Directory B2C | Microsoft Docs
description: Descreve os principais conceitos sobre a migração de usuários com identidades sociais no Azure AD B2C usando uma API do Graph.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/03/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: cb52cc85ea4e09890cf7c489d817c0b01192c65e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64684597"
---
# <a name="azure-active-directory-b2c-migrate-users-with-social-identities"></a>Azure Active Directory B2C: Migrar usuários com identidades sociais
Quando você planeja migrar seu provedor de identidade para o Microsoft Azure AD B2C, talvez seja necessário migrar usuários com identidades sociais. Este artigo explica como migrar as contas de identidades sociais existentes, como: Contas do Facebook, LinkedIn, Microsoft e Google para o Azure AD B2C. Este artigo também aplica-se a identidades federadas, no entanto, essas migrações são menos comuns.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo é uma continuação do artigo de migração de usuário e concentra-se na migração de identidade social. Antes de começar, leia [migração de usuário](active-directory-b2c-user-migration.md).

## <a name="social-identities-migration-introduction"></a>Introdução de migração de identidades sociais

* No Microsoft Azure Active Directory B2C, **contas locais**, os nomes de entrada (nome de usuário ou endereço de email) são armazenados na coleção `signInNames` no registro do usuário. O `signInNames` contém um ou mais `signInName` registros que especificam os nomes de entrada para o usuário. Cada nome de entrada deve ser exclusivo no locatário.

* As identidades das **Contas sociais** são armazenadas na coleção`userIdentities`. A entrada especifica o `issuer` (nome do provedor de identidade) como facebook.com e `issuerUserId`, que é um identificador de usuário exclusivo para o emissor. O atributo `userIdentities` contém um ou mais registros UserIdentity que especificam o tipo de conta social e o identificador de usuário exclusivo do provedor de identidade social.

* **Combine conta local com identidade social**. Como mencionado, os nomes de entrada da conta local e as identidades da conta social são armazenados em atributos diferentes. `signInNames` é usado para conta local, enquanto `userIdentities` para conta social. Uma única conta do Microsoft Azure Active Directory B2C, pode ser uma conta local somente, conta social somente ou combinar uma conta local com identidade social em um registro do usuário. Esse comportamento permite gerenciar uma única conta, enquanto um usuário pode entrar com as credenciais da conta local ou com as identidades sociais.

* `UserIdentity` Tipo - Contém informações sobre a identidade de um usuário da conta social em um locatário do Microsoft Azure Active Directory B2C:
  * `issuer` A representação da cadeia de caracteres do provedor de identidade que emitiu o identificador de usuário, como facebook.com.
  * `issuerUserId` O identificador de usuário exclusivo utilizado pelo provedor de identidade social no formato Base64.

    ```JSON
    "userIdentities": [{
          "issuer": "Facebook.com",
          "issuerUserId": "MTIzNDU2Nzg5MA=="
      }
    ]
    ```

* Dependendo do provedor de identidade, a **ID do usuário social** é um valor exclusivo para um determinado usuário por aplicativo ou uma conta de desenvolvimento. Configure a política do Microsoft Azure Active Directory B2C com a mesma ID de aplicativo anteriormente atribuída pelo provedor social. Ou outro aplicativo dentro da mesma conta de desenvolvimento.

## <a name="use-graph-api-to-migrate-users"></a>Utilizar API do Graph para migrar usuários
Você cria a conta de usuário do Microsoft Azure Active Directory B2C por meio da [API do Graph](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet). Para se comunicar com a API do Graph, primeiro, você precisa ter uma conta de serviço com privilégios administrativos. No Azure AD, você registra um aplicativo e a autenticação para o Azure AD. As credenciais do aplicativo são ID do Aplicativo e Segredo do Aplicativo. O aplicativo atua em nome próprio e não como usuário, para chamar a API do Graph. Siga as instruções na etapa 1 do artigo [Migração de usuário](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-user-migration).

## <a name="required-properties"></a>Propriedades obrigatórias
A lista a seguir mostra as propriedades que são obrigatórias ao criar um usuário.
* **accountEnabled** - verdadeiro
* **displayName** - O nome a ser exibido no catálogo de endereços do usuário.
* **passwordProfile** - O perfil de senha do usuário. 

> [!NOTE]
> Apenas para conta social (sem credenciais de conta local), você ainda deve especificar a senha. O Microsoft Azure Active Directory B2C ignora a senha que você especifica para contas sociais.

* **userPrincipalName** - O nome UPN (someuser@contoso.com). O nome UPN deve conter um dos domínios verificados para o locatário. Para especificar o UPN, gere o novo valor de GUID, concatenar com `@` e o nome do seu locatário.
* **mailNickname** - O alias de email do usuário. Esse valor pode ser a mesmo ID utilizada para o userPrincipalName. 
* **signInNames** - Um ou mais registros SignInName que especificam os nomes de entrada do usuário. Cada nome de entrada deve ser exclusivo na empresa/locatário. Apenas para conta social, essa propriedade pode ser deixada vazia.
* **userIdentities** - Um ou mais registros UserIdentity que especificam o tipo de conta social e o identificador de usuário exclusivo do provedor de identidade social.
* [opcional] **otherMails** - Apenas para conta social, os endereços de email do usuário 

Para obter mais informações, consulte: [Referência da API do Graph](/previous-versions/azure/ad/graph/api/users-operations#CreateLocalAccountUser)

## <a name="migrate-social-account-only"></a>Migrar conta social (somente)
Para criar somente uma conta social, sem credenciais da conta local. Envie solicitação HTTPS POST para a API do Graph. O corpo da solicitação contém as propriedades do usuário da conta social para criar. No mínimo, é necessário especificar as propriedades necessárias. 


**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Envie os dados de formulário a seguir: 

```JSON
{
    "objectId": null,
    "accountEnabled": true,
    "mailNickname": "c8c3d3b8-60cf-4c76-9aa7-eb3235b190c8",
    "signInNames": [],
    "creationType": null,
    "displayName": "Sara Bell",
    "givenName": "Sara",
    "surname": "Bell",
    "passwordProfile": {
        "password": "Test1234",
        "forceChangePasswordNextLogin": false
    },
    "passwordPolicies": null,
    "userIdentities": [{
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ],
    "otherMails": ["sara@live.com"],
    "userPrincipalName": "c8c3d3b8-60cf-4c76-9aa7-eb3235b190c8@tenant-name.onmicrosoft.com"
}
```
## <a name="migrate-social-account-with-local-account"></a>Migrar conta social com conta local
Para criar uma conta local combinada com identidades sociais. Envie solicitação HTTPS POST para a API do Graph. O corpo da solicitação contém as propriedades do usuário da conta social para criar. No mínimo, é necessário especificar as propriedades necessárias. 

**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Envie os dados de formulário a seguir: 

```JSON
{
    "objectId": null,
    "accountEnabled": true,
    "mailNickname": "5164db16-3eee-4629-bfda-dcc3326790e9",
    "signInNames": [{
            "type": "emailAddress",
            "value": "david@contoso.com"
        }
    ],
    "creationType": "LocalAccount",
    "displayName": "David Hor",
    "givenName": "David",
    "surname": "Hor",
    "passwordProfile": {
        "password": "1234567",
        "forceChangePasswordNextLogin": false
    },
    "passwordPolicies": "DisablePasswordExpiration,DisableStrongPassword",
    "userIdentities": [{
            "issuer": "contoso.com",
            "issuerUserId": "ZGF2aWRAY29udG9zby5jb20="
        }
    ],
    "otherMails": [],
    "userPrincipalName": "5164db16-3eee-4629-bfda-dcc3326790e9@tenant-name.onmicrosoft.com"
}
```

## <a name="frequently-asked-questions"></a>Perguntas frequentes
### <a name="how-can-i-know-the-issuer-name"></a>Como é possível saber o nome do emissor?
O nome do emissor ou o nome do provedor de identidade é configurado na política. Se você não souber o valor a ser especificado em `issuer`, siga este procedimento:
1. Entre com uma das contas sociais
2. Do token JWT, copie o valor `sub`. O `sub` geralmente contém a ID de objeto do usuário no Microsoft Azure Active Directory B2C. Ou pelo Portal do Azure, abra as propriedades do usuário e copie a ID de objeto.
3. Abrir o [Explorador do Graph do Microsoft Azure AD](https://graphexplorer.azurewebsites.net)
4. Entre com seu administrador.
5. Execute a seguinte solicitação GET. Substitua o userObjectId pela ID de usuário que você copiou. **GET** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId
6. Localize o elemento `userIdentities` dentro do retorno JSON do Microsoft Azure Active Directory B2C.
7. [Opcional] Também é possível decodificar o valor `issuerUserId`.

> [!NOTE]
> Utilize uma conta de administrador de locatários B2C que seja local para o locatário B2C. A sintaxe do nome da conta é admin@tenant-name.onmicrosoft.com.

### <a name="is-it-possible-to-add-social-identity-to-an-existing-local-account"></a>É possível adicionar identidade social a uma conta local existente?
Sim. É possível adicionar a identidade social depois que a conta local for criada. Execute a solicitação HTTPS PATCH. Substitua o userObjectId pela ID de usuário que você deseja atualizar. 

**PATCH** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Envie os dados de formulário a seguir: 

```JSON
{
    "userIdentities": [
        {
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
}
```

### <a name="is-it-possible-to-add-multiple-social-identities"></a>É possível adicionar várias identidades sociais?
Sim. Você pode adicionar várias identidades sociais a uma única conta do Microsoft Azure Active Directory B2C. Execute a solicitação HTTPS PATCH. Substitua o userObjectId pela ID de usuário. 

**PATCH** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Envie os dados de formulário a seguir: 

```JSON
{
    "userIdentities": [
        {
            "issuer": "google.com",
            "issuerUserId": "MjQzMjE2NTc4NTQ="
        },
        {
            "issuer": "facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
}
```

## <a name="optional-user-migration-application-sample"></a>[Opcional] Amostra de aplicativo de migração de usuário
[Baixar e executar o aplicativo de exemplo V2](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration). O aplicativo de exemplo V2 usa um arquivo JSON que contém dados de usuário fictícios, incluindo: conta local, conta social e identidades locais e sociais em uma única conta.  Para editar o arquivo JSON, abra a solução do Visual Studio `AADB2C.UserMigration.sln`. No projeto `AADB2C.UserMigration`, abra o arquivo `UsersData.json`. O arquivo contém uma lista de entidades do usuário. Cada entidade de usuário tem as seguintes propriedades:
* **signInName** - Para conta local, endereço de email para entrar
* **displayName** - Nome de exibição do usuário
* **firstName** - Nome do usuário
* **lastName** - Sobrenome do usuário
* **password** Para conta local, a senha do usuário (pode estar vazia)
* **issuer** - Para conta social, o nome do provedor de identidade
* **issuerUserId** - Para conta social, o identificador de usuário exclusivo usado pelo provedor de identidade social. O valor deve estar com texto não criptografado. O aplicativo de exemplo codifica esse valor para Base64.
* **email** Apenas para conta social (não combinada), o endereço de email do usuário

```JSON
{
  "userType": "emailAddress",
  "Users": [
    {
      // Local account only
      "signInName": "James@contoso.com",
      "displayName": "James Martin",
      "firstName": "James",
      "lastName": "Martin",
      "password": "Pass!w0rd"
    },
    {
      // Social account only
      "issuer": "Facebook.com",
      "issuerUserId": "1234567890",
      "email": "sara@contoso.com",
      "displayName": "Sara Bell",
      "firstName": "Sara",
      "lastName": "Bell"
    },
    {
      // Combine local account with social identity
      "signInName": "david@contoso.com",
      "issuer": "Facebook.com",
      "issuerUserId": "0987654321",
      "displayName": "David Hor",
      "firstName": "David",
      "lastName": "Hor",
      "password": "Pass!w0rd"
    }
  ]
}
```

> [!NOTE]
> Se você não atualizar o arquivo UsersData.json no exemplo com seus dados, poderá entrar com as credenciais da conta local de exemplo, mas não com os exemplos de conta social. Para migrar suas contas sociais, forneça dados reais.

Para obter mais informações sobre como utilizar o aplicativo de exemplo, confira [Azure Active Directory B2C: migração de usuário](active-directory-b2c-user-migration.md)
