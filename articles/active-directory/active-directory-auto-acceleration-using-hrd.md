---
title: "Configurar entrada aceleração automática para um aplicativo usando a Política de Descoberta de Realm de início | Microsoft Docs"
description: "Explica o que é um locatário do Azure AD e como gerenciar o Azure por meio do Active Directory do Azure"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: it-pro
ms.date: 11/09/2017
ms.author: billmath
ms.openlocfilehash: b1f0e5da09ef1d6acd234b72878cc71d66bb551e
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2017
---
# <a name="configure-sign-in-auto-acceleration-for-an-application-using-home-realm-discovery-hrd-policy"></a>Configurar entrada aceleração automática para um aplicativo usando a política de descoberta de Realm de início | Microsoft Docs

## <a name="introduction-to-home-realm-discovery-and-auto-acceleration"></a>Introdução à descoberta de realm inicial e aceleração automática
O documento a seguir fornece uma introdução à descoberta de realm de início e aceleração automática.

### <a name="home-realm-discovery"></a>Descoberta de realm de início
Descoberta de Realm de início é o processo que permite que o Azure Active Directory determinar, em tempo de logon, em que um usuário precisa autenticar.  Ao entrar um locatário Azure AD para acessar um recurso ou o Azure AD comum página de entrada, o usuário digita um nome de usuário (UPN).  O Azure AD que usa para descobrir onde o usuário precisa entrar.   O usuário pode precisar ser tomado para um dos seguintes sejam autenticados:

- O locatário inicial do usuário (pode ser o mesmo locatário conforme o recurso que o usuário está tentando acessar). 
- Conta da Microsoft.   O usuário é um convidado no locatário do recurso
- Outro provedor de identidade federada com o locatário do Azure AD.  Um provedor de identidade local como o AD FS para a instância.

### <a name="auto-acceleration"></a>Aceleração automática 
Algumas organizações configuram seu locatário do Azure Active Directory para federar com outro IdP, como o AD FS para autenticação do usuário.  Nesses casos, ao entrar em um aplicativo, o usuário primeiro é apresentado com uma página de entrada do Azure AD e depois que eles digitou seu UPN é exibida para a página de logon IdP.  Em circunstâncias em que faz sentido, os administradores podem desejar que usuários sejam direcionados diretamente para a página de entrada ao entrar em aplicativos específicos, ignorando a página inicial do Azure Active Directory. Isso é chamado de "entrada aceleração automática".

Em casos onde o locatário for federado para outro IdP para entrar, que permite a aceleração automática faz com que usuário entre de forma mais simplificada em casos em que você sabe que todos podem ser autenticados por esse IdP.  Você pode configurar a aceleração automática para aplicativos individuais.

>[!NOTE]
>Se você configurar um aplicativo para aceleração automática, usuários convidados não podem entrar. Direcionando o usuário diretamente a um IdP federado para autenticação é uma rua unidirecional, pois não há nenhuma maneira para voltar para a página de logon do Azure Active Directory.  Usuários convidados, os que podem precisar serem direcionados para um IdP externo como conta da Microsoft ou de outros locatários seja autenticado, não conseguirão entrar no aplicativo conforme a etapa de Home Realm Discovery estiver sendo ignorada.  

Há duas maneiras de controlar a aceleração automática para um IdP federado.  Você pode:   

- Use uma dica de domínio em solicitações de autenticação para um aplicativo 
- Configurar uma política de HomeRealmDiscovery para permitir a aceleração automática

## <a name="domain-hints"></a>Dicas de domínio 
Dicas de domínio são incluídas na solicitação de autenticação de um aplicativo de diretivas.  Elas podem ser usadas para acelerar o usuário de seu federado IdP na página de entrada ou pode ser usados por um aplicativo multilocatário para acelerar o usuário diretamente para a página de entrada da marca do Azure AD de seu locatário.  Por exemplo, um aplicativo largeapp.com pode permitir que seus clientes acessar o aplicativo em um contoso.largeapp.com URL personalizado e pode incluir uma dica do domínio contoso.com na solicitação de autenticação. Sintaxe de dica de domínio varia dependendo do protocolo que está sendo usado e eles normalmente são configurados no aplicativo.

**Web Services Federation**: whr=contoso.com na cadeia de caracteres de consulta

**SAML**: ou uma solicitação de autenticação SAML que contém uma dica de domínio ou um whr=contoso.com de cadeia de caracteres de consulta

**Abra para conectar-se a ID**: um domain_hint=contoso.com de cadeia de caracteres de consulta 

Se uma dica de domínio está incluída na solicitação de autenticação do aplicativo e o locatário for federado com o domínio, o Azure AD tenta redirecionar a entrada para o IdP configurado para esse domínio.  Se a dica de domínio não se referir a um domínio federado verificado, ele será ignorado e a descoberta de realm de início normal é invocada.

Consulte esta [postagem de blog](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/) para obter mais informações sobre aceleração automática usando as dicas de domínio Azure Active Directory com suporte.

>[!NOTE]
>Se uma dica de domínio é incluída em uma solicitação de autenticação sua presença substitui qualquer política de HDR é definida para o aplicativo.

## <a name="home-realm-discovery-hrd-policy"></a>Política de descoberta de realm inicial (HRD)
Alguns aplicativos não fornecem uma maneira de configurar a solicitação de autenticação que emitem e, nesses casos, não é possível usar dicas de domínio para controlar a aceleração automática.   Aceleração automática pode ser configurada por meio da política para alcançar o mesmo comportamento.  

### <a name="setting-hrd-policy"></a>Configuração de política HRD
Há três etapas para configurar o logon aceleração automática em um aplicativo


1. Criando uma diretiva de HDR para aceleração automática
2. Localizando o princípio de serviço ao qual anexar a política
3. Anexando a política para a entidade de serviço.  Políticas podem ter sido criadas em um locatário, mas não têm qualquer efeito até que elas estejam anexadas a uma entidade.  Uma política HRD pode ser anexada a uma entidade de serviço e apenas uma política HRD pode estar ativa em uma determinada entidade a qualquer momento.  

Você pode usar o Microsoft Azure Active Directory API do Graph diretamente ou os Cmdlets do PowerShell do Azure Active Directory para configurar a aceleração automática usando a política de HRD

A API do Graph que manipula a política é descrita [aqui](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations).

Aqui está um exemplo de definição de política HRD:
    
 ```
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu"
    }
   }
```

O tipo de política é "HomeRealmDiscoveryPolicy".

Se **AccelerateToFederatedDomain** for false, a política não tem nenhum efeito **PreferredDomain** deve indicar um domínio para acelerar e pode ser omitida, caso o locatário tenha apenas um domínio federado.  Se ele for omitido e houver mais de um verificado, o domínio federado, a política não terá efeito.

Se **PreferredDomain** for especificado, ele deve corresponder a um domínio verificado, agrupado para o locatário e todos os usuários do aplicativo em questão devem ser capazes de entrar no domínio.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Prioridade e avaliação de políticas HRD
As políticas HRD podem ser criadas e atribuídas a organizações e entidades de serviço específicos. Isso significa que é possível que várias políticas se apliquem a um aplicativo específico. A política HRD que entra em vigor segue estas regras:


- Se uma dica de domínio estiver presente na solicitação de autenticação, qualquer política de HDR é ignorada, e o comportamento especificado pela dica de domínio é usado.
- Pelo contrário, se uma política for atribuída explicitamente à entidade de serviço, ela será imposta. 
- Se nenhuma política for explicitamente atribuída à entidade de serviço, uma política explicitamente atribuída à organização pai da entidade de serviço será imposta. 
- Se não há nenhuma dica de domínio, e nenhuma política foi atribuída à entidade de serviço principal ou da organização, o comportamento de HDR padrão será usado.

## <a name="tutorial-for-setting-sign-in-auto-acceleration-on-an-application-using-hrd-policy-with-azure-active-directory-powershell-cmdlets"></a>Tutorial para a configuração de entrada aceleração automática em um aplicativo usando a política de HDR com os cmdlets do PowerShell do Azure Active Directory
Vamos examinar alguns cenários, incluindo:


- Configurando a aceleração automática para um aplicativo para um locatário com um único domínio federado
- Configuração de aceleração automática para um aplicativo para um de vários domínios que são verificados para seu locatário
- Listando os aplicativos para os quais uma política é configurada

### <a name="prerequisites"></a>Pré-requisitos
Nos exemplos a seguir você cria, atualiza, vincula e exclui políticas em entidades de serviço de aplicativo no Azure AD.

1.  Para começar, faça o download da versão mais recente do Azure AD PowerShell Cmdlet Versão prévia. 
2.  Depois que tiver os Cmdlets do Azure AD PowerShell, execute o comando Conectar para entrar em sua conta de administrador do Azure AD.

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  Execute o comando a seguir para ver todas as políticas que foram criadas na organização.

    ``` powershell
    Get-AzureADPolicy
    ```

Se nada for retornado, você não tem políticas criadas em seu locatário

### <a name="example-setting-auto-acceleration-for-an-application"></a>Exemplo: Configurando a aceleração automática para um aplicativo 
Neste exemplo, você deve criar uma política que autoacelera os usuários a uma tela de entrada do AD FS ao entrar um aplicativo.  Isso é feito sem eles precisarem inserir um nome de usuário na página de entrada do Azure AD pela primeira vez. 

#### <a name="step-1-create-an-hrd-policy"></a>Etapa 1: Criar uma política HRD
``` powershell
New-AzureADPoly -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Se você tiver um único domínio federado que autentica usuários para aplicativos, você precisa criar uma política de HDR.  
Para ver a nova política e obter seu ObjectID, execute o comando a seguir.

``` powershell
Get-AzureADPolicy
```


Uma vez que você tenha uma política de HDR, para permitir a aceleração de auto, atribua-a a vários princípios de aplicativo de serviço.

#### <a name="step-2-locate-the-service-principal-to-assign-the-policy-to"></a>Etapa 2: Localize a entidade de serviço para atribuir a política.  
É necessário o ObjectId das entidades de serviço que você deseja atribuir a política. Há várias maneiras de localizar a ID de objeto de entidades de serviço.    

Você pode usar o portal, você pode consultar o [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) ou vá para nossa [ferramenta Graph Explorer](https://graphexplorer.cloudapp.net/) e entre em sua conta do Azure AD para ver as entidades de serviço de todas as da sua organização, ou desde que você está usando PowerShell, você pode usar o cmdlet get-AzureADServicePrincipal para listar os princípios de serviço e suas Ids.

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Etapa 3: Atribuir a política à entidade de serviço  
Uma vez que a ID de objeto da entidade de serviço do aplicativo que você deseja configurar a aceleração automática, execute o seguinte comando para associar a política de HDR criado na etapa 1 com a entidade de serviço que você localizou na etapa 2.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Repita esse comando para cada entidade de serviço principal que você deseja adicionar a política.

#### <a name="step-4-check-which-application-service-principals-your-auto-acceleration-policy-is-assigned-to"></a>Etapa 4: Verificar quais entidades de serviço de aplicativo sua política de aceleração automática é atribuída para
Para verificar quais aplicativos têm política configurada de aceleração automática use o cmdlet Get-AzureADPolicyAppliedObject e passe a ID do objeto da política que você deseja verificar.

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>Etapa 5: Pronto!
Tente o aplicativo para verificar se a nova política está funcionando.

### <a name="example-listing-the-applications-for-which-an-auto-acceleration-policy-is-configured"></a>Exemplo: Listando os aplicativos para os quais uma política de aceleração automática está configurada

#### <a name="step-1-list-all-policies-created-in-your-organization"></a>Etapa 1: Liste todas as políticas criadas na sua organização 

``` powershell
Get-AzureADPolicy
```

Observe o **ID de objeto** da política que você deseja atribuições de lista.

#### <a name="step-2-list-the-service-principals-the-policy-is-assigned-to"></a>Etapa 2: Liste entidades de serviço a que a política é atribuída.  

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```

### <a name="example-removing-an-auto-acceleration-policy-for-an-application"></a>Exemplo: Remover uma política de aceleração automática para um aplicativo
#### <a name="step-1-use-the-previous-example-to-get-the-objectid-of-the-policy-and-that-of-the-application-service-principal-you-wish-to-remove-it-from"></a>Etapa 1: Use o exemplo anterior para obter o ID do objeto da política e que a entidade de serviço de aplicativo que você deseja removê-la
#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>Etapa 2: Remova a atribuição de política da entidade de serviço de aplicativo.  

``` powershell
Remove-AzureADApplicationPolicy -ObjectId <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-the-policy-is-assigned-to"></a>Etapa 3: Liste entidades de serviço a que a política é atribuída 

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
## <a name="next-steps"></a>Próximas etapas
- Para obter mais informações sobre como a autenticação funciona no Azure AD, consulte [Cenários de autenticação do Azure AD](develop/active-directory-authentication-scenarios.md).
- Para obter mais informações sobre o logon único de usuário consulte [acesso ao aplicativo e logon único com o Azure Active Directory](active-directory-enterprise-apps-manage-sso.md)
- Visite o [Guia do desenvolvedor do Active Directory](develop/active-directory-developers-guide.md) para obter uma visão geral de todo o conteúdo relacionado a desenvolvedor.
