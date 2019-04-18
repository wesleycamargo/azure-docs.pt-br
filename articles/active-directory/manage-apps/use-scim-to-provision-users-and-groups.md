---
title: Automatizar o provisionamento de aplicativos usando SCIM no Active Directory do Azure | Microsoft Docs
description: O Azure Active Directory pode provisionar automaticamente usuários e grupos a qualquer repositório de identidades ou aplicativos que seja administrado por um serviço Web com a interface definida na especificação do protocolo SCIM.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 4/03/2019
ms.author: celested
ms.reviewer: asmalser
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: a404b5e6769c7bb91b4f7b5830cea18372ec456d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59007145"
---
# <a name="using-system-for-cross-domain-identity-management-scim-to-automatically-provision-users-and-groups-from-azure-active-directory-to-applications"></a>Usar o SCIM (Sistema de Gerenciamento de Identidades entre Domínios) para provisionar automaticamente usuários e grupos do Azure Active Directory para aplicativos

## <a name="overview"></a>Visão geral

SCIM é padronizada de protocolo e o esquema que tem como objetivo gerar maior consistência em como as identidades são gerenciadas em todos os sistemas. Quando um aplicativo dá suporte a um ponto de extremidade SCIM para gerenciamento de usuário, o serviço de provisionamento de usuário do Azure AD pode enviar solicitações para criar, modificar ou excluir usuários e grupos para esse ponto de extremidade atribuídos. 

Muitos dos aplicativos para o qual dá suporte ao Azure AD [pré-integrados provisionamento automático de usuário](../saas-apps/tutorial-list.md) implementar SCIM, como os meios para usuário de receber as notificações de alteração.  Além disso, os clientes podem conectar aplicativos que dão suporte a um perfil específico do [especificação do protocolo SCIM 2.0](https://tools.ietf.org/html/rfc7644) usando a opção de integração de "inexistente na Galeria" genérico no portal do Azure. 

O foco principal deste artigo é o perfil do SCIM 2.0 do Azure AD implementa como parte do seu conector SCIM genérico para inexistente na Galeria de aplicativos. No entanto, com êxito de teste de um aplicativo que dá suporte a SCIM do Azure AD genérico conector é uma etapa à obtenção de um aplicativo listado na Galeria do Azure AD como o provisionamento de usuário de suporte. Para obter mais informações sobre o aplicativo listado na Galeria de aplicativos do Azure AD, consulte o [rede do aplicativo Microsoft](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx).
 

>[!IMPORTANT]
>O comportamento da implementação do SCIM do Azure AD foi atualizado pela última vez em 18 de dezembro de 2018. Para obter informações sobre o que mudou, consulte [Conformidade do protocolo SCIM 2.0 do serviço de provisionamento de usuário do Azure AD](application-provisioning-config-problem-scim-compatibility.md).

![][0]
*Figura 1: Provisionamento do Azure Active Directory para um repositório de identidades ou aplicativos que implementa a SCIM*

Este artigo é dividido em quatro seções:

* **[Provisionamento de usuários e grupos para aplicativos de terceiros que dão suporte a SCIM 2.0](#provisioning-users-and-groups-to-applications-that-support-scim)**  - se a sua organização estiver usando um aplicativo de terceiros que implementa o perfil do SCIM 2.0 que o Azure AD dá suporte a, você pode começar a automatizar ambos provisionamento e desprovisionamento de usuários e grupos de hoje.

* **[Noções básicas sobre a implementação de SCIM do Azure AD](#understanding-the-azure-ad-scim-implementation)**  -se você estiver criando um aplicativo que dá suporte a uma API de gerenciamento de usuário SCIM 2.0, esta seção descreve detalhadamente como o cliente de SCIM do Azure AD é implementado e como você deve modelar o protocolo SCIM solicitar tratamento e respostas.
  
* **[Criando um ponto de extremidade SCIM usando bibliotecas Microsoft CLI](#building-a-scim-endpoint-using-microsoft-cli-libraries)**  -bibliotecas Common Language Infrastructure (CLI), juntamente com exemplos de código mostram como desenvolver um ponto de extremidade SCIM e converter mensagens SCIM.  

* **[Referência de esquema de usuário e grupo](#user-and-group-schema-reference)**  -descreve o esquema de usuário e grupo suportado pela implementação de SCIM do Azure AD para inexistente na Galeria de aplicativos. 

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Provisionamento de usuários e grupos a aplicativos que dão suporte a SCIM
Azure AD pode ser configurado para provisionar atribuído automaticamente usuários e grupos para aplicativos que implementam um perfil específico do [protocolo SCIM 2.0](https://tools.ietf.org/html/rfc7644). As especificações do perfil estão documentadas em [Noções básicas sobre a implementação de SCIM do Azure AD](#understanding-the-azure-ad-scim-implementation).

Verifique com o seu provedor de aplicativo ou na documentação do seu provedor de aplicativo as declarações de compatibilidade com esses requisitos.

>[!IMPORTANT]
>A implementação de SCIM do Azure AD é criada sobre o serviço, que foi projetado para manter constantemente os usuários em sincronia entre o Azure AD de provisionamento de usuário do Azure AD e o aplicativo de destino e implementa um conjunto específico de operações padrão. É importante entender esses comportamentos para entender o comportamento do cliente do SCIM do Azure AD. Para obter mais informações, consulte [o que acontece durante o provisionamento de usuário?](user-provisioning.md#what-happens-during-provisioning).

### <a name="getting-started"></a>Introdução
Os aplicativos que dão suporte ao perfil SCIM descrito neste artigo podem ser conectados ao Azure Active Directory usando o recurso de "aplicativo não galeria" na galeria de aplicativos do Azure AD. Uma vez conectado, o Azure AD executa um processo de sincronização a cada 40 minutos, em que ele consulta o ponto de extremidade SCIM do aplicativo para os usuários e grupos atribuídos e os cria ou modifica de acordo com os detalhes da atribuição.

**Para conectar um aplicativo que dê suporte a SCIM:**

1. Entrar para o [portal do Azure Active Directory](https://aad.portal.azure.com). 

1. Selecione **aplicativos empresariais** no painel esquerdo. Uma lista de todos os aplicativos configurados, incluindo os aplicativos que foram adicionados da galeria, é mostrada.

1. Selecione **+ novo aplicativo** > **todos os** > **aplicativo inexistente na galeria**.

1. Insira um nome para seu aplicativo e selecione **adicionar** para criar um objeto de aplicativo. O novo aplicativo é adicionado à lista de aplicativos empresariais e abre a tela de gerenciamento de seu aplicativo.
    
   ![][1]
   *Figura 2: Galeria de aplicativos do Azure AD*
    
1. Na tela de gerenciamento de aplicativo, selecione **provisionamento** no painel esquerdo.
1. No menu **Modo de Provisionamento**, selecione **Automático**.
    
   ![][2]
   *Figura 3: Configurar o provisionamento no portal do Azure*
    
1. No campo **URL do locatário** , insira a URL do ponto de extremidade do SCIM do aplicativo. Exemplo: https://api.contoso.com/scim/v2/
1. Se o ponto de extremidade SCIM exigir um token de portador OAuth de um emissor diferente do Azure AD, copie o token de portador OAuth necessário para o campo opcional **Token Secreto**. Se esse campo for deixado em branco, o Azure AD inclui um token de portador OAuth emitido do Azure AD com cada solicitação. Aplicativos que usam o Azure AD como provedor de identidade podem validar esse token emitido pelo Azure AD.
1. Selecione **Conexão de teste** ter o Azure Active Directory tente se conectar ao ponto de extremidade SCIM. Se a tentativa falhar, as informações de erro são exibidas.  

    >[!NOTE]
    >**Testar Conexão** consulta o ponto de extremidade SCIM para um usuário que não existe, usando um GUID aleatório como a propriedade correspondente selecionada na configuração do Azure AD. A resposta correta esperada é HTTP 200 OK com uma mensagem de SCIM ListResponse vazia. 

1. Se as tentativas de conexão para o aplicativo forem bem-sucedidas, em seguida, selecione **salvar** para salvar as credenciais de administrador.
1. Na seção **Mapeamento**, há dois conjuntos selecionáveis de mapeamentos de atributos: um para objetos de usuário e outro para objetos de grupo. Selecione cada um para revisar os atributos que são sincronizados do Azure Active Directory para seu aplicativo. Os atributos selecionados como propriedades **Correspondentes** serão usados para fazer a correspondência entre os usuários e os grupos no seu aplicativo para operações de atualização. Para confirmar eventuais alterações, selecione **Salvar**.

    >[!NOTE]
    >Opcionalmente, você pode desabilitar a sincronização dos objetos de grupo desabilitando o mapeamento "grupos". 

1. Em **Configurações**, o campo **Escopo** define quais usuários e grupos são sincronizados. Selecione **sincronizar apenas os usuários e grupos atribuídos** (recomendado) para sincronizar somente usuários e grupos atribuídos na **usuários e grupos** guia.
1. Depois que sua configuração for concluída, defina as **Status de provisionamento** ao **em**.
1. Selecione **salvar** para iniciar o serviço de provisionamento do AD do Azure. 
1. Se sincronizando apenas usuários e grupos atribuídos (recomendados), certifique-se de selecionar o **usuários e grupos** guia e atribua os usuários ou grupos que você deseja sincronizar.

Depois que a sincronização inicial foi iniciada, você pode selecionar **logs de auditoria** no painel esquerdo para monitorar o progresso, que mostra todas as ações feitas pelo serviço de provisionamento em seu aplicativo. Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](check-status-user-account-provisioning.md).

> [!NOTE]
> A sincronização inicial levará mais tempo do que as sincronizações posteriores, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço está em execução. 

## <a name="understanding-the-azure-ad-scim-implementation"></a>Noções básicas sobre a implementação de SCIM do Azure AD

Se você estiver criando um aplicativo que dá suporte a uma API de gerenciamento de usuário SCIM 2.0, esta seção descreve detalhadamente como o cliente de SCIM do Azure AD é implementado e como você deve modelar seu protocolo SCIM solicitar tratamento e respostas. Depois de implementar o seu ponto de extremidade do SCIM, você pode testá-lo, seguindo o procedimento descrito na seção anterior.

Dentro de [especificação do protocolo SCIM 2.0](http://www.simplecloud.info/#Specification), seu aplicativo deve atender a estes requisitos:

* Dá suporte à criação de usuários e, opcionalmente, também grupos, de acordo com a seção [3.3 do protocolo SCIM](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Dar suporte à modificação de usuários ou grupos com solicitações PATCH, de acordo [seção 3.5.2 do protocolo SCIM](https://tools.ietf.org/html/rfc7644#section-3.5.2).  
* Dar suporte à recuperação de um recurso conhecido para um usuário ou grupo criado anteriormente, de acordo [seção 3.4.1 do protocolo SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Dar suporte a consultas de usuários ou grupos, de acordo com a seção [3.4.2 do protocolo SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Por padrão, os usuários são recuperados por seus `id` e consultados por seus `username` e `externalid`, e os grupos são consultados por `displayName`.  
* Dá suporte a consultas de usuário por ID e pelo Gerenciador, de acordo com a seção 3.4.2 do protocolo SCIM.  
* Dá suporte a consultas de grupos por ID e por membro, de acordo com a seção 3.4.2 do protocolo SCIM.  
* Aceita um token de portador único para autenticação e autorização do AD do Azure para seu aplicativo.

Siga estas diretrizes gerais ao implementar um ponto de extremidade SCIM para garantir a compatibilidade com o Azure AD:

* `id` é uma propriedade necessária para todos os recursos. Todas as respostas que retorna um recurso deve garantir que cada recurso tem essa propriedade, exceto para `ListResponse` com zero membros.
* Resposta a uma solicitação de consulta ou o filtro deve ser sempre uma `ListResponse`.
* Grupos são opcionais, mas só terá suporte se a implementação de SCIM dá suporte a solicitações de PATCH.
* Não é necessário incluir o recurso inteiro na resposta do PATCH.
* Microsoft Azure AD usa apenas os operadores a seguir:  
     - `eq`
     - `and`
* Não exigem uma correspondência diferencia maiusculas de minúsculas em elementos estruturais do SCIM, em particular PATCH `op` valores de operação, conforme definido em https://tools.ietf.org/html/rfc7644#section-3.5.2. Azure AD emite os valores de 'op' como `Add`, `Replace`, e `Remove`.
* Microsoft Azure AD faz solicitações para buscar um usuário aleatório e o grupo para garantir que o ponto de extremidade e as credenciais são válidas. Ele também é feito como parte da **Conexão de teste** fluir na [portal do Azure](https://portal.azure.com). 
* O atributo que os recursos podem ser consultados no deve ser definido como um atributo correspondente no aplicativo na [portal do Azure](https://portal.azure.com). Para obter mais informações, consulte [personalizando usuário provisionamento mapeamentos de atributo](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

### <a name="user-provisioning-and-de-provisioning"></a>Provisionamento e desprovisionamento de usuários
A ilustração a seguir mostra as mensagens que o Azure Active Directory envia a um serviço SCIM para gerenciar o ciclo de vida de um usuário no repositório de identidades do seu aplicativo.  

![][4]
*Figura 4: Sequência de provisionamento e desprovisionamento de usuário*

### <a name="group-provisioning-and-de-provisioning"></a>Provisionamento e desprovisionamento de grupo
Provisionamento e desprovisionamento de grupo é opcional. Quando implementado e habilitada, a ilustração a seguir mostra as mensagens que o Azure AD envia para um serviço SCIM para gerenciar o ciclo de vida de um grupo no repositório de identidades do seu aplicativo.  Essas mensagens são diferentes das mensagens sobre usuários de duas maneiras: 

* As solicitações para recuperar grupos de especificam que o atributo de membros a serem excluídos de qualquer recurso fornecido em resposta à solicitação.  
* As solicitações para determinar se um atributo de referência tem um determinado valor são sobre o atributo de membros.  

![][5]
*Figura 5: Sequência de provisionamento e desprovisionamento de grupo*

### <a name="scim-protocol-requests-and-responses"></a>Solicitações do protocolo SCIM e respostas
Esta seção fornece a solicitações SCIM do exemplo emitida pelo cliente do SCIM do Azure AD e exemplo respostas esperadas. Para obter melhores resultados, você deve codificar seu aplicativo para lidar com essas solicitações nesse formato e emitir as respostas esperadas.

>[!IMPORTANT]
>Para entender como e quando o serviço de provisionamento de usuário do Azure AD emite as operações descritas abaixo, consulte [o que acontece durante o provisionamento de usuário?](user-provisioning.md#what-happens-during-provisioning).

- [Operações de usuário](#user-operations)
  - [Criar usuário](#create-user)
    - [Solicitação](#request)
    - [Resposta](#response)
  - [Obter usuário](#get-user)
    - [Solicitação](#request-1)
    - [Resposta](#response-1)
  - [Obter usuário por consulta](#get-user-by-query)
    - [Solicitação](#request-2)
    - [Resposta](#response-2)
  - [Obter usuário por consulta - Zero resultados](#get-user-by-query---zero-results)
    - [Solicitação](#request-3)
    - [Resposta](#response-3)
  - [Atualizar usuário [propriedades com múltiplos]](#update-user-multi-valued-properties)
    - [Solicitação](#request-4)
    - [Resposta](#response-4)
  - [Atualizar usuário [Propriedades de valor único]](#update-user-single-valued-properties)
    - [Solicitação](#request-5)
    - [Resposta](#response-5)
  - [Excluir usuário](#delete-user)
    - [Solicitação](#request-6)
    - [Resposta](#response-6)
- [Operações de grupo](#group-operations)
  - [Criar grupo](#create-group)
    - [Solicitação](#request-7)
    - [Resposta](#response-7)
  - [Obter grupo](#get-group)
    - [Solicitação](#request-8)
    - [Resposta](#response-8)
  - [Obter grupo por displayName](#get-group-by-displayname)
    - [Solicitação](#request-9)
    - [Resposta](#response-9)
  - [Grupo de atualização [atributos não-membro]](#update-group-non-member-attributes)
    - [Solicitação](#request-10)
    - [Resposta](#response-10)
  - [Grupo de atualização [adicionar membros]](#update-group-add-members)
    - [Solicitação](#request-11)
    - [Resposta](#response-11)
  - [Grupo de atualização [remover membros]](#update-group-remove-members)
    - [Solicitação](#request-12)
    - [Resposta](#response-12)
  - [Excluir grupo](#delete-group)
    - [Solicitação](#request-13)
    - [Resposta](#response-13)

### <a name="user-operations"></a>Operações de usuário

* Os usuários podem ser consultados por `userName` ou `email[type eq "work"]` atributos.  

#### <a name="create-user"></a>Criar Usuário

###### <a name="request"></a>Solicitação
*POST /Users*
```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "active": true,
    "emails": [{
        "primary": true,
        "type": "work",
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com"
    }],
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName"
    },
    "roles": []
}
```

##### <a name="response"></a>Response
*HTTP/1.1 201 criado*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "48af03ac28ad4fb88478",
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```


#### <a name="get-user"></a>Obter usuário

###### <a name="request"></a>Solicitação
*GET /Users/5d48a0a8e9f04aa38008* 

###### <a name="response"></a>Response
*HTTP/1.1 200 OKEY*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5d48a0a8e9f04aa38008",
    "externalId": "58342554-38d6-4ec8-948c-50044d0a33fd",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_feed3ace-693c-4e5a-82e2-694be1b39934",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_22370c1a-9012-42b2-bf64-86099c2a1c22@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```
#### <a name="get-user-by-query"></a>Obter usuário por consulta

##### <a name="request"></a>Solicitação
*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response"></a>Response
*HTTP/1.1 200 OKEY*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
        "id": "2441309d85324e7793ae",
        "externalId": "7fce0092-d52e-4f76-b727-3955bd72c939",
        "meta": {
            "resourceType": "User",
            "created": "2018-03-27T19:59:26.000Z",
            "lastModified": "2018-03-27T19:59:26.000Z"
            
        },
        "userName": "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081",
        "name": {
            "familyName": "familyName",
            "givenName": "givenName"
        },
        "active": true,
        "emails": [{
            "value": "Test_User_91b67701-697b-46de-b864-bd0bbe4f99c1@testuser.com",
            "type": "work",
            "primary": true
        }]
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="get-user-by-query---zero-results"></a>Obter usuário por consulta - Zero resultados

##### <a name="request"></a>Solicitação
*GET /Users?filter=userName eq "non-existent user"*

##### <a name="response"></a>Response
*HTTP/1.1 200 OKEY*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="update-user-multi-valued-properties"></a>Atualizar usuário [propriedades com múltiplos]

##### <a name="request"></a>Solicitação
*PATCH/usuários/6764549bef60420686bc HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [
            {
            "op": "Replace",
            "path": "emails[type eq \"work\"].value",
            "value": "updatedEmail@microsoft.com"
            },
            {
            "op": "Replace",
            "path": "name.familyName",
            "value": "updatedFamilyName"
            }
    ]
}
```

##### <a name="response"></a>Response
*HTTP/1.1 200 OKEY*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "6764549bef60420686bc",
    "externalId": "6c75de36-30fa-4d2d-a196-6bdcdb6b6539",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_fbb9dda4-fcde-4f98-a68b-6c5599e17c27",
    "name": {
        "formatted": "givenName updatedFamilyName",
        "familyName": "updatedFamilyName",
        "givenName": "givenName"
    },
    "active": true,
    "emails": [{
        "value": "updatedEmail@microsoft.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="update-user-single-valued-properties"></a>Atualizar usuário [Propriedades de valor único]

##### <a name="request"></a>Solicitação
*PATCH/usuários/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "userName",
        "value": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com"
    }]
}
```

##### <a name="response"></a>Response
*HTTP/1.1 200 OKEY*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5171a35d82074e068ce2",
    "externalId": "aa1eca08-7179-4eeb-a0be-a519f7e5cd1a",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "userName": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_49dc1090-aada-4657-8434-4995c25a00f7@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="delete-user"></a>Excluir usuário

##### <a name="request"></a>Solicitação
*Excluir /Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response"></a>Response
*HTTP/1.1 204 sem conteúdo*

### <a name="group-operations"></a>Operações de grupo

* Grupos sempre deverão ser criados com uma lista de membros vazios.
* Grupos podem ser consultados pelo `displayName` atributo.
* Atualização para a solicitação de PATCH do grupo deve produzir uma *HTTP 204 sem conteúdo* na resposta. Retornar um corpo de uma lista de todos os membros não é aconselhável.
* Não é necessário dar suporte a retornar todos os membros do grupo.

#### <a name="create-group"></a>Criar Grupo

##### <a name="request"></a>Solicitação
*/Groups de POSTAGEM HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "id": "c4d56c3c-bf3b-4e96-9b64-837018d6060e",
    "displayName": "displayName",
    "members": [],
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response"></a>Response
*HTTP/1.1 201 criado*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "927fa2c08dcb4a7fae9e",
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "displayName": "displayName",
    "members": []
}
```

#### <a name="get-group"></a>Obter Grupo

##### <a name="request"></a>Solicitação
*GET /Groups/40734ae655284ad3abcc?excludedAttributes=members HTTP/1.1*

##### <a name="response"></a>Response
*HTTP/1.1 200 OKEY*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "40734ae655284ad3abcc",
    "externalId": "60f1bb27-2e1e-402d-bcc4-ec999564a194",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "displayName": "displayName",
}
```

#### <a name="get-group-by-displayname"></a>Obter grupo por displayName

##### <a name="request"></a>Solicitação
*GET /Groups?excludedAttributes=members&filter=displayName eq "displayName" HTTP/1.1*

##### <a name="response"></a>Response
*HTTP/1.1 200 OKEY*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
        "id": "8c601452cc934a9ebef9",
        "externalId": "0db508eb-91e2-46e4-809c-30dcbda0c685",
        "meta": {
            "resourceType": "Group",
            "created": "2018-03-27T22:02:32.000Z",
            "lastModified": "2018-03-27T22:02:32.000Z",
            
        },
        "displayName": "displayName",
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```
#### <a name="update-group-non-member-attributes"></a>Grupo de atualização [atributos não-membro]

##### <a name="request"></a>Solicitação
*PATCH/grupos/fa2ce26709934589afc5 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "displayName",
        "value": "1879db59-3bdf-4490-ad68-ab880a269474updatedDisplayName"
    }]
}
```

##### <a name="response"></a>Response
*HTTP/1.1 204 sem conteúdo*

### <a name="update-group-add-members"></a>Grupo de atualização [adicionar membros]

##### <a name="request"></a>Solicitação
*PATCH/grupos/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Add",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a>Response
*HTTP/1.1 204 sem conteúdo*

#### <a name="update-group-remove-members"></a>Grupo de atualização [remover membros]

##### <a name="request"></a>Solicitação
*PATCH/grupos/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Remove",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a>Response
*HTTP/1.1 204 sem conteúdo*

#### <a name="delete-group"></a>Excluir grupo

##### <a name="request"></a>Solicitação
*Excluir /Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response"></a>Response
*HTTP/1.1 204 sem conteúdo*


## <a name="building-a-scim-endpoint-using-microsoft-cli-libraries"></a>Criando um ponto de extremidade SCIM usando bibliotecas CLI da Microsoft
Criando um serviço web SCIM que interage com o Azure Active Directory, você pode habilitar o provisionamento automático de usuário para praticamente qualquer repositório de identidades ou aplicativos.

Veja como ele funciona:

1. Azure AD fornece uma common language infrastructure (CLI) biblioteca denominada systemforcrossdomainidentitymanagement, incluído no código exemplos descrevem a abaixo. Os desenvolvedores e integradores de sistema podem usar esta biblioteca para criar e implantar um ponto de extremidade de serviço web baseado em SCIM que conectam o Azure AD para o repositório de identidades de qualquer aplicativo.
2. Os mapeamentos são implementados no serviço Web para mapear o esquema de usuário padronizado para o esquema de usuário e o protocolo exigido pelo aplicativo. 
3. A URL do ponto de extremidade é registrada no AD do Azure como parte de um aplicativo personalizado na galeria de aplicativos.
4. Os usuários e grupos são atribuídos a esse aplicativo no AD do Azure. Após a atribuição, eles são enfileirados e esperarão a serem sincronizados com o aplicativo de destino. O processo de sincronização que trata a fila é executado a cada 40 minutos.

### <a name="code-samples"></a>Exemplos de código
Para facilitar esse processo, [exemplos de código](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) forem fornecidos, que criam um SCIM ponto de extremidade de serviço web e demonstre o provisionamento automático. O exemplo é de um provedor que mantém um arquivo com linhas de valores separados por vírgula representando usuários e grupos.    

**Pré-requisitos**

* Visual Studio 2013 ou posterior.
* [SDK do Azure para .NET](https://azure.microsoft.com/downloads/)
* Computador com Windows que ofereça suporte à estrutura ASP.NET 4.5 a ser usado como o ponto de extremidade SCIM. Essa máquina deve ser acessível da nuvem.
* [Uma assinatura do Azure com uma versão de avaliação ou licenciada do Azure AD Premium](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>Introdução
A maneira mais fácil de implementar um ponto de extremidade SCIM que possa aceitar solicitações de provisionamento do AD do Azure é criando e implantando o exemplo de código que gera os usuários provisionados em um arquivo CSV (valores separados por vírgula).

#### <a name="to-create-a-sample-scim-endpoint"></a>Para criar um exemplo de ponto de extremidade SCIM

1. Baixe o pacote de exemplo de código em [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
1. Descompacte o pacote e coloque-o no seu computador com Windows em um local como C:\AzureAD-BYOA-Provisioning-Samples\.
1. Nessa pasta, inicie o projeto FileProvisioning\Host\FileProvisioningService.csp no Visual Studio.
1. Selecione **ferramentas** > **Gerenciador de pacotes NuGet** > **Package Manager Console**e execute os seguintes comandos para o Projeto FileProvisioningService para resolver as referências de solução:

   ```
    Update-Package -Reinstall
   ```

1. Compile o projeto FileProvisioningService.
1. Inicie o aplicativo Prompt de Comando no Windows (como administrador) e use o comando **cd** para alterar o diretório para a sua pasta **\AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug**.
1. Execute o seguinte comando, substituindo `<ip-address>` com o nome de domínio ou endereço IP do computador Windows:

   ```
    FileSvc.exe http://<ip-address>:9000 TargetFile.csv
   ```

1. No Windows, sob **configurações do Windows** > **rede e as configurações da Internet**, selecione o **Firewall do Windows**  >   **Configurações avançadas**e crie um **regra de entrada** que permite o acesso de entrada na porta 9000.
1. Se o computador do Windows estiver atrás de um roteador, o roteador precisa ser configurado para executar a Network Access Translation entre sua porta 9000 exposta à internet e a porta 9000 no computador do Windows. Essa configuração é necessária para o Azure AD acessar esse ponto de extremidade na nuvem.

#### <a name="to-register-the-sample-scim-endpoint-in-azure-ad"></a>Para registrar o exemplo de ponto de extremidade SCIM no Azure AD

1. Entrar para o [portal do Azure Active Directory](https://aad.portal.azure.com). 

1. Selecione **aplicativos empresariais** no painel esquerdo. Uma lista de todos os aplicativos configurados, incluindo os aplicativos que foram adicionados da galeria, é mostrada.

1. Selecione **+ novo aplicativo** > **todos os** > **aplicativo inexistente na galeria**.

1. Insira um nome para seu aplicativo e selecione **adicionar** para criar um objeto de aplicativo. O objeto de aplicativo criado destina-se a representar o aplicativo de destino para o qual você estará provisionando e implementando o logon único, e não apenas o ponto de extremidade SCIM.

1. Na tela de gerenciamento de aplicativo, selecione **provisionamento** no painel esquerdo.

1. No menu **Modo de Provisionamento**, selecione **Automático**.
    
   ![][2]
   *Figura 6: Configurar o provisionamento no portal do Azure*
    
1. No campo **URL do locatário**, insira a URL exposta à Internet e a porta do seu ponto de extremidade SCIM. A entrada é algo como http://testmachine.contoso.com:9000 ou http://\<endereço-ip>:9000/, em que \<endereço-ip> é o endereço exposto na Internet. 

1. Se o ponto de extremidade SCIM exigir um token de portador OAuth de um emissor diferente do Azure AD, copie o token de portador OAuth necessário para o campo opcional **Token Secreto**. Se esse campo for deixado em branco, o Azure AD incluirá um token de portador OAuth emitido do Azure AD com cada solicitação. Aplicativos que usam o Azure AD como provedor de identidade podem validar esse token emitido pelo Azure AD.

1. Selecione **Conexão de teste** ter o Azure Active Directory tente se conectar ao ponto de extremidade SCIM. Se a tentativa falhar, as informações de erro são exibidas.  

    >[!NOTE]
    >**Testar Conexão** consulta o ponto de extremidade SCIM para um usuário que não existe, usando um GUID aleatório como a propriedade correspondente selecionada na configuração do Azure AD. A resposta correta esperada é HTTP 200 OK com uma mensagem de SCIM ListResponse vazia

1. Se as tentativas de conexão para o aplicativo forem bem-sucedidas, em seguida, selecione **salvar** para salvar as credenciais de administrador.

1. Na seção **Mapeamento**, há dois conjuntos selecionáveis de mapeamentos de atributos: um para objetos de usuário e outro para objetos de grupo. Selecione cada um para revisar os atributos que são sincronizados do Azure Active Directory para seu aplicativo. Os atributos selecionados como propriedades **Correspondentes** serão usados para fazer a correspondência entre os usuários e os grupos no seu aplicativo para operações de atualização. Para confirmar eventuais alterações, selecione **Salvar**.

1. Em **Configurações**, o campo **Escopo** define quais usuários e/ou grupos são sincronizados. Selecione **"Sincronizar somente usuários e grupos atribuídos** (recomendado) para sincronizar somente usuários e grupos atribuídos na **usuários e grupos** guia.

1. Depois que sua configuração for concluída, defina as **Status de provisionamento** ao **em**.

1. Selecione **salvar** para iniciar o serviço de provisionamento do AD do Azure. 

1. Se sincronizando apenas usuários e grupos atribuídos (recomendados), certifique-se de selecionar o **usuários e grupos** guia e atribua os usuários ou grupos que você deseja sincronizar.

Depois que a sincronização inicial foi iniciada, você pode selecionar **logs de auditoria** no painel esquerdo para monitorar o progresso, que mostra todas as ações feitas pelo serviço de provisionamento em seu aplicativo. Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](check-status-user-account-provisioning.md).

A etapa final da verificação do exemplo é abrir o arquivo TargetFile.csv da pasta \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug no seu computador com Windows. Depois que o processo de provisionamento é executado, esse arquivo mostra os detalhes de todos os usuários e grupos provisionados e atribuídos.

### <a name="development-libraries"></a>Bibliotecas de desenvolvimento
Para desenvolver seu próprio serviço Web em conformidade com a especificação do SCIM, em primeiro lugar, familiarize-se com as seguintes bibliotecas fornecidas pela Microsoft, que ajudam a acelerar o processo de desenvolvimento: 

- As bibliotecas Common Language Infrastructure (CLI) são oferecidas para uso com linguagens que se baseiam na infraestrutura em questão, como C#. Uma dessas bibliotecas, Microsoft, declara uma interface, iprovider, mostrada na ilustração a seguir. Um desenvolvedor que usa as bibliotecas implementa essa interface com uma classe que pode ser referenciada, de modo genérico, como um provedor. As bibliotecas permitem que o desenvolvedor implantar um serviço web que está em conformidade com a especificação do SCIM. O serviço web ou pode ser hospedado em serviços de informações da Internet ou qualquer assembly executável da CLI. A solicitação é convertida em chamadas aos métodos do provedor, que podem ser programadas pelo desenvolvedor para operar em algum repositório de identidades.
  
   ![][3]
  
- [Manipuladores de ExpressRoute](https://expressjs.com/guide/routing.html) estão disponíveis para análise de objetos de solicitação node.js que representam chamadas (como definido pela especificação do SCIM) feitas para um serviço Web node.js.   

### <a name="building-a-custom-scim-endpoint"></a>Criando um ponto de extremidade SCIM personalizado
Os desenvolvedores que usam as bibliotecas CLI podem hospedar seus serviços em qualquer assembly executável da CLI, ou nos serviços de informações da Internet. Veja um código de exemplo para hospedagem de um serviço em um assembly executável, no endereço http://localhost:9000: 

    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }

Esse serviço deve ter um endereço HTTP e um certificado de autenticação de servidor do qual a autoridade de certificação raiz é destes nomes: 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

Um certificado de autenticação de servidor pode ser associado a uma porta em um host do Windows usando o utilitário de shell de rede: 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  

Aqui, o valor fornecido para o argumento certhash é a impressão digital do certificado, enquanto o valor fornecido para o argumento appid é um identificador global exclusivo arbitrário.  

Para hospedar o serviço nos serviços de informações da Internet, um desenvolvedor cria um assembly de biblioteca de código CLI com uma classe chamada Startup no namespace padrão do assembly.  Veja um exemplo dessa classe: 

    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }

### <a name="handling-endpoint-authentication"></a>Manipulando a autenticação do ponto de extremidade
As solicitações do Active Directory do Azure incluem um token de portador do OAuth 2.0.   Qualquer serviço que recebe a solicitação deve autenticar o emissor como sendo o Active Directory do Azure para o locatário esperado do Active Directory do Azure, para acesso ao serviço web do Azure Active Directory Graph.  No token, o emissor é identificado por uma declaração iss, como "iss": "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/".  Neste exemplo, o endereço base do valor de declaração, https://sts.windows.net, identifica o Azure Active Directory como o emissor, enquanto o endereço relativo segmento, cbb1a5ac-f33b-45fa-9bf5-f37db0fed422, é um identificador exclusivo do locatário do Azure Active Directory para qual o token foi emitido.  Se o token tiver sido emitido para acessar um serviço Web Graph do Azure Active Directory, o identificador desse serviço, 00000002-0000-0000-c000-000000000000, deverá estar no valor da declaração aud do token.  Cada um dos aplicativos que são registrados em um único locatário pode receber o mesmo `iss` declarações com solicitações SCIM.

Os desenvolvedores que usam as bibliotecas CLI fornecidas pela Microsoft para a criação de um serviço SCIM podem autenticar solicitações do Azure Active Directory usando o pacote do ActiveDirectory seguindo estas etapas: 

1. Em um provedor, implemente a propriedade Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior fazendo com que ela retorne um método a ser chamado sempre que o serviço é iniciado: 

   ```
     public override Action\<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration\> StartupBehavior
     {
       get
       {
         return this.OnServiceStartup;
       }
     }

     private void OnServiceStartup(
       Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
       System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
     {
     }
   ```

2. Adicione o seguinte código ao método para que as solicitações para qualquer um dos pontos de extremidade do serviço sejam autenticados como sendo um token emitido pelo Azure Active Directory para um locatário especificado para o acesso ao serviço web do Azure AD Graph: 

   ```
     private void OnServiceStartup(
       Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
       System.Web.Http.HttpConfiguration HttpConfiguration configuration)
     {
       // IFilter is defined in System.Web.Http.dll.  
       System.Web.Http.Filters.IFilter authorizationFilter = 
         new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

       // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
       // System.IdentityModel.Token.Jwt.dll.
       SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
         new TokenValidationParameters()
         {
           ValidAudience = "00000002-0000-0000-c000-000000000000"
         };

       // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
       // Microsoft.Owin.Security.ActiveDirectory.dll
       Microsoft.Owin.Security.ActiveDirectory.
       WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
         new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
         TokenValidationParameters = tokenValidationParameters,
         Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                       // identifier for this one.  
       };

       applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
     }
   ```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Tratamento de provisionamento e desprovisionamento de usuários

1. O Azure Active Directory consulta o serviço para procurar um usuário com um valor de atributo externalId correspondente ao valor de atributo mailNickname de um usuário no Azure AD. A consulta é expressa como uma solicitação de protocolo HTTP (Hypertext Transfer), como neste exemplo, na qual jyoung é um exemplo de um mailNickname de um usuário no Azure Active Directory.

    >[!NOTE]
    > Isso é apenas um exemplo. Nem todos os usuários terão um atributo mailNickname, e o valor que tem um usuário pode não ser exclusivo no diretório. Além disso, o atributo usado para correspondência (que nesse caso é externalId) é configurável na [mapeamentos de atributo do AD do Azure](customize-application-attributes.md).

   ````
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
   ````
   Se o serviço foi criado usando as bibliotecas CLI fornecidas pela Microsoft para implementação de serviços SCIM, a solicitação é convertida em uma chamada ao método Query do provedor de serviços.  Veja a assinatura desse método: 
   ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);
   ````
   Veja a definição da interface Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters: 
   ````
    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
      { get; }
    }

   ```
     GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
     Authorization: Bearer ...
   ```

   If the service was built using the Common Language Infrastructure libraries provided by Microsoft for implementing SCIM services, then the request is translated into a call to the Query method of the service’s provider.  Here is the signature of that method: 

   ```
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  
 
     System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]>  Query(
       Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
       string correlationIdentifier);
   ```

   Here is the definition of the Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters interface: 

   ```
     public interface IQueryParameters: 
       Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
     {
         System.Collections.Generic.IReadOnlyCollection  <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
         { get; }
     }

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
     {
       system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
       { get; }
       System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
       { get; }
       string SchemaIdentifier 
       { get; }
     }

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
     {
         Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
           { get; set; }
         string AttributePath 
           { get; } 
         Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
           { get; }
         string ComparisonValue 
           { get; }
     }

     public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
     {
         Equals
     }
   ```

   In the following sample of a query for a user with a given value for the externalId attribute, values of the arguments passed to the Query method are: 
   * parameters.AlternateFilters.Count: 1
   * parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
   * parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"
   * correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

2. If the response to a query to the web service for a user with an externalId attribute value that matches the mailNickname attribute value of a user doesn't return any users, then Azure Active Directory requests that the service provision a user corresponding to the one in Azure Active Directory.  Here is an example of such a request: 

   ````
    Pós-autorização de HTTP/1.1 https://.../scim/Users: Portador...  Tipo de conteúdo: aplicativo/scim + json {"esquemas": ["urn:IETF:params:scim:schemas:core:2.0:User", "urn: ietf:params:scim:schemas:extension:enterprise:2.0User"], "externalId": "jyoung", "userName": "jyoung", "active": true, "endereços": null,    "displayName": "Joy Young", "emails": [{"type": "trabalho", "value": "jyoung@Contoso.com", "primary": true}], "meta": {"resourceType": "Usuário"}, "name": {"Nomedefamília": "Jovens", "givenName": "Joy"}, "phoneNumbers": null, "preferredLa nguage": null,"title": null,"Departamento": null,"manager": null}
   ````
   The CLI libraries provided by Microsoft for implementing SCIM services would translate that request into a call to the Create method of the service’s provider.  The Create method has this signature: 
   ````
    System.Threading.Tasks.Tasks é definido em mscorlib. dll.  
    Microsoft.SystemForCrossDomainIdentityManagement.Resource é definido em / / Microsoft.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(    Microsoft.SystemForCrossDomainIdentityManagement.Resource resource,    string correlationIdentifier);
   ````
   In a request to provision a user, the value of the resource argument is an instance of the Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser class, defined in the Microsoft.SystemForCrossDomainIdentityManagement.Schemas library.  If the request to provision the user succeeds, then the implementation of the method is expected to return an instance of the Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser class, with the value of the Identifier property set to the unique identifier of the newly provisioned user.  

3. To update a user known to exist in an identity store fronted by an SCIM, Azure Active Directory proceeds by requesting the current state of that user from the service with a request such as: 
   ````
    OBTER autorização de HTTP/1.1 ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682: Portador...
   ````
   In a service built using the CLI libraries provided by Microsoft for implementing SCIM services, the request is translated into a call to the Retrieve method of the service’s provider.  Here is the signature of the Retrieve method: 
   ````
    System.Threading.Tasks.Tasks é definido em mscorlib. dll.  
    Microsoft.SystemForCrossDomainIdentityManagement.Resource e / / Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters / / são definidos em Microsoft.  
    Tasks < Microsoft.SystemForCrossDomainIdentityManagement.Resource > recuperar (parâmetros Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters, correlationIdentifier de cadeia de caracteres);

    interface pública Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters {Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier ResourceIdentifier {get;}} a interface pública Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier {cadeia de caracteres de identificador {get; conjunto;} cadeia de caracteres Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier {get; conjunto;}}
   ````
   In the example of a request to retrieve the current state of a user, the values of the properties of the object provided as the value of the parameters argument are as follows: 
  
   * Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

4. If a reference attribute is to be updated, then Azure Active Directory queries the service to determine whether the current value of the reference attribute in the identity store fronted by the service already matches the value of that attribute in Azure Active Directory. For users, the only attribute of which the current value is queried in this way is the manager attribute. Here is an example of a request to determine whether the manager attribute of a particular user object currently has a certain value: 

   If the service was built using the CLI libraries provided by Microsoft for implementing SCIM services, then the request is translated into a call to the Query method of the service’s provider. The value of the properties of the object provided as the value of the parameters argument are as follows: 
  
   * parameters.AlternateFilters.Count: 2
   * parameters.AlternateFilters.ElementAt(x).AttributePath: "ID"
   * parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
   * parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
   * parameters.RequestedAttributePaths.ElementAt(0): "ID"
   * parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

   Here, the value of the index x can be 0 and the value of the index y can be 1, or the value of x can be 1 and the value of y can be 0, depending on the order of the expressions of the filter query parameter.   

5. Here is an example of a request from Azure Active Directory to an SCIM service to update a user: 
   ````
    Autorização de HTTP/1.1 ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 do PATCH: Portador...  Tipo de conteúdo: aplicativo/scim + json {"esquemas": ["urn: ietf:params:scim:api:messages:2.0:PatchOp"], "Operações": [{"op": "Adicionar", "path": "manager", "value": [{"$ref": "http://.../scim/Users/2819c223-7f76-453a-919d-413861904646", "value": "2819c223-7f76-453a-919d-413861904646"}]}]}
   ````
   The Microsoft CLI libraries for implementing SCIM services would translate the request into a call to the Update method of the service’s provider. Here is the signature of the Update method: 
   ````
    System.Threading.Tasks.Tasks e / / System.Collections.Generic.IReadOnlyCollection<T> / / são definidos em mscorlib. dll.  
    Microsoft.SystemForCrossDomainIdentityManagement.IPatch, / / Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, / / Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, / / Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, / / Microsoft.SystemForCrossDomainIdentityManagement.OperationName, / / Microsoft.SystemForCrossDomainIdentityManagement.IPath e / / Microsoft.SystemForCrossDomainIdentityManagement.OperationValue / / são Microsoft.SystemForCrossDomainIdentityManagement.Protocol definido em todos os. 

    System.Threading.Tasks.Task Update(    Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch,    string correlationIdentifier);

    interface pública Microsoft.SystemForCrossDomainIdentityManagement.IPatch {Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase PatchRequest {get; conjunto;} Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier ResourceIdentifier {get; conjunto;}        
    }

    classe pública PatchRequest2:    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase {público System.Collections.Generic.IReadOnlyCollection < Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation > operações {get;}


   Se o serviço foi criado usando as bibliotecas Common Language Infrastructure fornecidas pela Microsoft para implementação de serviços SCIM, a solicitação é convertida em uma chamada ao método Query do provedor de serviços. O valor das propriedades do objeto fornecido como o valor do argumento de parâmetros é: 
  
* parameters.AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: “ID”
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue:  "54D382A4-2050-4C03-94D1-E769F1D15682"
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue:  "2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0): “ID”
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

  Aqui, o valor do índice x pode ser 0 e o valor do índice y pode ser 1, ou o valor de x pode ser de 1 e o valor de y pode ser 0, dependendo da ordem das expressões de parâmetro de consulta de filtro.   

1. Veja um exemplo de uma solicitação do Azure Active Directory a um serviço SCIM para atualizar um usuário: 

   ```
     PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
     Content-type: application/scim+json
     {
       "schemas": 
       [
         "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
       "Operations":
       [
         {
           "op":"Add",
           "path":"manager",
           "value":
             [
               {
                 "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                 "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
   ```

   As bibliotecas Common Language Infrastructure fornecidas pela Microsoft para implementação dos serviços SCIM convertem a solicitação em uma chamada ao método Update do provedor de serviços. Veja a assinatura do método Update: 

   ```
     // System.Threading.Tasks.Tasks and 
     // System.Collections.Generic.IReadOnlyCollection<T>
     // are defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
     // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
     // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
     // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
     // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
     // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
     // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

     System.Threading.Tasks.Task Update(
       Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
       string correlationIdentifier);

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
     {
     Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
       PatchRequest 
         { get; set; }
     Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
       ResourceIdentifier 
         { get; set; }        
     }

     public class PatchRequest2: 
       Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
     {
     public System.Collections.Generic.IReadOnlyCollection
       <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
         Operations
         { get;}

     public void AddOperation(
       Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
     }

     public class PatchOperation
     {
     public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
       Name
       { get; set; }

     public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
       Path
       { get; set; }

     public System.Collections.Generic.IReadOnlyCollection
       <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
       { get; }

     public void AddValue(
       Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
     }

     public enum OperationName
     {
       Add,
       Remove,
       Replace
     }

     public interface IPath
     {
       string AttributePath { get; }
       System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
       Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
     }

     public class OperationValue
     {
       public string Reference
       { get; set; }

       public string Value
       { get; set; }
     }
   ```

    No exemplo de uma solicitação para atualizar um usuário, o objeto fornecido como valor do argumento de patch tem estes valores de propriedade: 
  
   * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier.SchemaIdentifier:  "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
   * (PatchRequest as PatchRequest2).Operations.Count: 1
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).OperationName: OperationName.Add
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Path.AttributePath: "manager"
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.Count: 1
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Reference: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Value: 2819c223-7f76-453a-919d-413861904646

1. Para desprovisionar um usuário de um repositório de identidades administrado por um serviço SCIM, o Azure AD envia uma solicitação como esta: 

   ```
     DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
   ```

   Se o serviço tiver sido criado usando as bibliotecas Common Language Infrastructure fornecidas pela Microsoft para implementação de serviços SCIM, a solicitação é convertida em uma chamada ao método Delete do provedor de serviços.   Esse método tem esta assinatura: 

   ```
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
     // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
     System.Threading.Tasks.Task Delete(
       Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
         resourceIdentifier, 
       string correlationIdentifier);
   ```

   O objeto fornecido como valor do argumento resourceIdentifier tem estes valores de propriedade no exemplo de uma solicitação para desprovisionar um usuário: 

1. Para desprovisionar um usuário de um repositório de identidades administrado por um serviço SCIM, o Azure AD envia uma solicitação como esta: 
   ````
    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
   ````
   Se o serviço foi criado usando as bibliotecas CLI fornecidas pela Microsoft para implementação de serviços SCIM, a solicitação é convertida em uma chamada ao método Delete do provedor de serviços.   Esse método tem esta assinatura: 
   ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
   ````
   O objeto fornecido como valor do argumento resourceIdentifier tem estes valores de propriedade no exemplo de uma solicitação para desprovisionar um usuário: 
  
   * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="user-and-group-schema-reference"></a>Referência de esquema de usuário e grupo
O Azure Active Directory pode provisionar dois tipos de recurso aos serviços Web SCIM.  Esses tipos de recurso são usuários e grupos.  

Recursos de usuário são identificados pelo identificador do esquema, `urn:ietf:params:scim:schemas:extension:enterprise:2.0:User`, que está incluído nesta especificação de protocolo: https://tools.ietf.org/html/rfc7643.  O mapeamento padrão dos atributos de usuários no Azure Active Directory para os atributos dos recursos do usuário é fornecido na tabela 1.  

Os recursos do grupo são identificados pelo identificador de esquema, `urn:ietf:params:scim:schemas:core:2.0:Group`. Tabela 2 mostra o mapeamento padrão dos atributos de grupos no Azure Active Directory para os atributos do grupo de recursos.  

### <a name="table-1-default-user-attribute-mapping"></a>Tabela 1: Mapeamento padrão de atributo do usuário

| Usuário do Active Directory do Azure | “urn:ietf:params:scim:schemas:extension:enterprise:2.0:User” |
| --- | --- |
| IsSoftDeleted |ativo |
| displayName |displayName |
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |título |
| mail |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |manager |
| Serviço Móvel |phoneNumbers[type eq "mobile"].value |
| ID do objeto |ID |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| sobrenome |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |

### <a name="table-2-default-group-attribute-mapping"></a>Tabela 2: Mapeamento padrão de atributo do grupo

| Grupo do Active Directory do Azure | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |externalId |
| mail |emails[type eq "work"].value |
| mailNickname |displayName |
| membros |membros |
| ID do objeto |ID |
| proxyAddresses |emails[type eq "other"].Value |


## <a name="related-articles"></a>Artigos relacionados
* [Automatizar o provisionamento/desprovisionamento de usuários para aplicativos SaaS](user-provisioning.md)
* [Personalizando os mapeamentos de atributos para provisionamento de usuários](customize-application-attributes.md)
* [Escrevendo expressões para mapeamentos de atributo](functions-for-customizing-application-data.md)
* [Filtros de escopo para provisionamento de usuários](define-conditional-rules-for-provisioning-user-accounts.md)
* [Notificações de provisionamento de conta](user-provisioning.md)
* [Lista de tutoriais sobre como integrar aplicativos SaaS](../saas-apps/tutorial-list.md)

<!--Image references-->
[0]: ./media/use-scim-to-provision-users-and-groups/scim-figure-1.png
[1]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2a.png
[2]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2b.png
[3]: ./media/use-scim-to-provision-users-and-groups/scim-figure-3.png
[4]: ./media/use-scim-to-provision-users-and-groups/scim-figure-4.png
[5]: ./media/use-scim-to-provision-users-and-groups/scim-figure-5.png
