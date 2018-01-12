---
title: "Configurar aceleração automática de entrada para um aplicativo usando a política Descoberta de Realm Inicial | Microsoft Docs"
description: "Explica o que é um locatário do Azure AD e como gerenciar o Azure por meio do Azure Active Directory."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: it-pro
ms.date: 11/09/2017
ms.author: billmath
ms.openlocfilehash: e2e6e5c40dc4a9f67f94c45f8394512db3f777f5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="configure-sign-in-auto-acceleration-for-an-application-by-using-a-home-realm-discovery-policy"></a>Configurar aceleração automática de entrada para um aplicativo usando a política Descoberta de Realm Inicial

O documento a seguir apresenta uma introdução a Descoberta de Realm Inicial e à aceleração automática.

## <a name="home-realm-discovery"></a>Descoberta de Realm Inicial
Descoberta de Realm Inicial (HRD) é o processo que permite que o Azure Active Directory (Azure AD) determine, no momento da entrada, onde um usuário precisa se autenticar.  Ao entrar em um locatário do Azure AD para acessar um recurso, ou a página de entrada comum do Azure AD, um usuário digita um nome (UPN). O Azure AD usa esse nome para descobrir onde o usuário precisa entrar. 

O usuário talvez precise ser levado para um dos seguintes locais para ser autenticado:

- O locatário inicial do usuário (pode ser o mesmo locatário do recurso que o usuário está tentando acessar). 

- Conta da Microsoft.  O usuário é um convidado no locatário do recurso.

- Outro provedor de identidade federado com o locatário do Azure AD.

-  Um provedor de identidade local como Serviços de Federação do Active Directory (AD FS).

## <a name="auto-acceleration"></a>Aceleração automática 
Algumas organizações configuram seu locatário do Azure Active Directory para federar com outro IdP, como AD FS para autenticação do usuário.  

Nesses casos, ao entrar em um aplicativo, um usuário recebe primeiro uma página de entrada do Azure AD. Depois que tiver digitado o UPN, ele é levado até a página de entrada IdP. Em determinadas circunstâncias, os administradores talvez queiram direcionar usuários para a página de entrada quando estes estiverem entrando em aplicativos específicos. 

Isso significa que os usuários podem ignorar a página do Azure Active Directory inicial. Esse processo é conhecido como "aceleração automática de entrada".

Em casos nos quais o locatário for federado com outro IdP para entrar, a aceleração automática aprimora mais a entrada.  Você pode configurar a aceleração automática para aplicativos individuais.

>[!NOTE]
>Se você configurar um aplicativo para aceleração automática, os usuários convidados não poderão entrar. Se você levar um usuário diretamente para um IdP federado para autenticação, não haverá maneira para ele voltar à página de entrada do Azure Active Directory. Usuários convidados, que talvez precisem ser direcionados para um outros locatários ou um IdP externo como uma conta Microsoft, não poderão entrar nesse aplicativo porque estão ignorando a etapa da Descoberta de Realm Inicial.  

Há duas maneiras de controlar a aceleração automática para um IdP federado:   

- Use uma dica de domínio em solicitações de autenticação para um aplicativo. 
- Configure uma política Descoberta de Realm Inicial para habilitar a aceleração automática.

## <a name="domain-hints"></a>Dicas de domínio 
Dicas de domínio são diretivas incluídas na solicitação de autenticação de um aplicativo. Elas podem ser usadas para agilizar o usuário até a página de entrada IdP. Ou elas podem ser usadas por um aplicativo multilocatário para agilizar o usuário diretamente para a página de entrada do Azure AD da marca do locatário.  

Por exemplo, o aplicativo “largeapp.com” pode permitir que os clientes acessem o aplicativo em um URL personalizado “contoso.largeapp.com”. O aplicativo também pode incluir uma dica de domínio para contoso.com na solicitação de autenticação. 

A sintaxe da dica de domínio varia de acordo com o protocolo usado, e ela é normalmente configurada no aplicativo.

**Web Services Federation**: whr=contoso.com na cadeia de caracteres de consulta.

**SAML**: uma solicitação de autenticação SAML que contém uma dica de domínio ou uma cadeia de caracteres de consulta whr=contoso.com.

**Abrir Conexão de ID**: uma cadeia de caracteres de consulta domain_hint=contoso.com. 

Se uma dica de domínio estiver incluída na solicitação de autenticação do aplicativo e o locatário for federado com esse domínio, o Azure AD tentará redirecionar a entrada para o IdP configurado para esse domínio. 

Se a dica de domínio não se referir a um domínio federado verificado, ele será ignorado, e a Descoberta de Realm Inicial normal será invocada.

Para obter mais informações sobre aceleração automática usando as dicas de domínio compatíveis com o Azure Active Directory, consulte o blog [Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/).

>[!NOTE]
>Se uma dica de domínio estiver incluída em uma solicitação de autenticação, a presença dela substituirá qualquer política HDR definida para o aplicativo.

## <a name="home-realm-discovery-policy"></a>Política Descoberta de Realm Inicial
Alguns aplicativos não oferecem uma maneira de configurar a solicitação de autenticação emitida. Nesses casos, não é possível usar dicas de domínio para controlar a aceleração automática. Aceleração automática pode ser configurada por meio da política para alcançar o mesmo comportamento.  

### <a name="set-hrd-policy"></a>Definir política HRD
Há três etapas para definir a aceleração automática de entrada em um aplicativo:


1. Criando uma política HDR para aceleração automática.

2. Localizando o princípio de serviço ao qual anexar a política.

3. Anexando a política para a entidade de serviço. As políticas podem ter sido criadas em um locatário, mas não terão efeito até serem anexadas a uma entidade. 

Uma política HRD pode ser anexada a uma entidade de serviço, e apenas uma política HRD pode estar ativa em uma determinada entidade a qualquer momento.  

Você pode usar a API do Graph do Microsoft Azure Active Directory diretamente ou os cmdlets do PowerShell do Azure Active Directory para configurar a aceleração automática usando a política HRD.

A API do Graph que manipula a política é descrita no artigo [Operações na política](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) no MSDN.

Aqui está uma definição de política HRD de exemplo:
    
 ```
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu"
    }
   }
```

O tipo de política é “HomeRealmDiscoveryPolicy”.

Se **AccelerateToFederatedDomain** for falso, a política não entrará em vigor.

**PreferredDomain** deve indicar um domínio para o qual acelerar. Ele poderá ser omitido se o locatário tiver apenas um domínio federado.  Se ele for omitido e houver mais de um domínio federado verificado, a política não entrará em vigor.

Se **PreferredDomain** for especificado, ele deverá corresponder a um domínio federado, verificado, para o locatário. Todos os usuários do aplicativo devem ser capazes de entrar nesse domínio.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Prioridade e avaliação de políticas HRD
As políticas HRD podem ser criadas e atribuídas a organizações e entidades de serviço específicos. Isso significa que é possível que várias políticas se apliquem a um aplicativo específico. A política HRD que entra em vigor segue estas regras:


- Se uma dica de domínio estiver presente na solicitação de autenticação, qualquer política HDR será ignorada. O comportamento especificado pela dica de domínio é usado.

- Pelo contrário, se uma política for atribuída explicitamente à entidade de serviço, ela será imposta. 

- Se nenhuma dica de política e nenhuma política forem atribuídas explicitamente à entidade de serviço, uma política atribuída explicitamente à organização pai da entidade de serviço será imposta. 

- Se não há nenhuma dica de domínio, e nenhuma política foi atribuída à entidade de serviço principal ou da organização, o comportamento de HDR padrão será usado.

## <a name="tutorial-for-setting-sign-in-auto-acceleration-on-an-application-by-using-an-hrd-policy"></a>Tutorial para definir aceleração automática de entrada em um aplicativo usando-se uma política HRD
Usaremos os cmdlets do PowerShell do Azure AD para percorrer a alguns cenários, inclusive:


- Configurando aceleração automática de um aplicativo para um locatário com um único domínio federado.

- Configurando aceleração automática de um aplicativo para vários domínios verificados para o locatário.

- Listando os aplicativos para os quais uma política é configurada.

### <a name="prerequisites"></a>Pré-requisitos
Nos exemplos a seguir, você cria, atualiza, vincula e exclui políticas em entidades de serviço do aplicativo no Azure AD.

1.  Para começar, faça o download da visualização do cmdlet do PowerShell do Azure AD mais recente. 

2.  Depois que você tiver baixado os cmdlets do PowerShell do Azure AD, execute o comando Conectar para entrar no Azure AD com a conta de administrador:

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  Execute o comando a seguir para ver todas as políticas na organização:

    ``` powershell
    Get-AzureADPolicy
    ```

Se não retornar nada, você não terá políticas criadas no locatário.

### <a name="example-set-auto-acceleration-for-an-application"></a>Exemplo: definir aceleração automática para um aplicativo 
Neste exemplo, você cria uma política que agiliza automaticamente usuários até a uma tela de entrada do AD FS quando eles entram em um aplicativo. Os usuários podem entrar no AD FS sem precisar inserir um nome de usuário na página de entrada do Azure AD primeiro. 

#### <a name="step-1-create-an-hrd-policy"></a>Etapa 1: Criar uma política HRD
``` powershell
New-AzureADPoly -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Se tiver um único domínio federado que autentique usuários para aplicativos, você precisará criar apenas uma política HDR.  

Para ver a nova política e obter a **ObjectID**, execute o seguinte comando:

``` powershell
Get-AzureADPolicy
```


Para habilitar a aceleração automática depois que tiver uma política HRD, você poderá atribuí-la a vários princípios de serviço do aplicativo.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>Etapa 2: localize a entidade de serviço à qual atribuir a política.  
Você precisa da **ObjectID** das entidades de serviço às quais deseja atribuir a política. Há várias maneiras de encontrar a **ObjectID** de entidades de serviço.    

Você pode usar o portal ou consultar [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Você também pode ir até a [Ferramenta Explorador do Graph](https://graphexplorer.cloudapp.net/) e entrar na conta do Azure AD para ver todas as entidades de serviço da organização. Como está usando o PowerShell, você pode usar o cmdlet get-AzureADServicePrincipal para listar os princípios de serviço e as IDs.

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Etapa 3: Atribuir a política à entidade de serviço  
Depois que você tiver a **ObjectID** da entidade de serviço do aplicativo para o qual deseja configurar a aceleração automática, execute o comando a seguir. Esse comando associa a política HRD que você criou na etapa 1 à entidade de serviço que você localizou na etapa 2.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Você pode repetir esse comando para cada entidade de serviço principal à qual deseja adicionar a política.

#### <a name="step-4-check-which-application-service-principals-your-auto-acceleration-policy-is-assigned-to"></a>Etapa 4: Verificar quais entidades de serviço de aplicativo sua política de aceleração automática é atribuída para
Para verificar quais aplicativos têm a política de aceleração automática configurada, use o cmdlet **Get-AzureADPolicyAppliedObject**. Passe o **ObjectID** da política que você deseja verificar.

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>Etapa 5: Pronto!
Tente o aplicativo para verificar se a nova política está funcionando.

### <a name="example-list-the-applications-for-which-an-auto-acceleration-policy-is-configured"></a>Exemplo: listar os aplicativos para os quais uma política de aceleração automática está configurada

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>Etapa 1: listar todas as políticas que foram criadas na organização 

``` powershell
Get-AzureADPolicy
```

Observe o **ObjectID** da política cujas atribuições você deseja listar.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>Etapa 2: listar as entidades de serviço às quais a política está atribuída  

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```

### <a name="example-remove-an-auto-acceleration-policy-for-an-application"></a>Exemplo: remover uma política de aceleração automática para um aplicativo
#### <a name="step-1-get-the-objectid"></a>Etapa 1: obter a ObjectID
Use o exemplo anterior para obter a **ObjectID** da política e a entidade de serviço do aplicativo da qual você deseja removê-la. 

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>Etapa 2: remover a atribuição de política da entidade de serviço do aplicativo  

``` powershell
Remove-AzureADApplicationPolicy -ObjectId <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>Etapa 3: verificar a remoção listando as entidades de serviço às quais a política está atribuída 

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
## <a name="next-steps"></a>Próximas etapas
- Para obter mais informações sobre como a autenticação funciona no Azure AD, consulte [Cenários de autenticação do Azure AD](develop/active-directory-authentication-scenarios.md).
- Para obter mais informações sobre o logon único de usuário consulte [Acesso ao aplicativo e logon único com o Azure Active Directory](active-directory-enterprise-apps-manage-sso.md).
- Visite o [Guia do desenvolvedor do Active Directory](develop/active-directory-developers-guide.md) para obter uma visão geral de todo o conteúdo relacionado a desenvolvedor.
