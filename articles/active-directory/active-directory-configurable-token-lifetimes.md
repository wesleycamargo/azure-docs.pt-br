---
title: "Tempos de vida de token configurável no Azure Active Directory | Microsoft Docs"
description: "Esse recurso é usado por administradores e desenvolvedores para especificar os tempos de vida de tokens emitidos pelo Azure AD."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 06f5b317-053e-44c3-aaaa-cf07d8692735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/06/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f61d23fec6badb8dd53379d183b177e4c19e5711


---
# <a name="configurable-token-lifetimes-in-azure-active-directory-public-preview"></a>Tempos de vida de token configurável no Azure Active Directory (Visualização Pública)
> [!NOTE]
> Esse recurso está atualmente em visualização pública.  Você deve estar preparado para reverter ou remover quaisquer alterações.  Estamos abrindo esse recurso para que qualquer pessoa o experimente durante a visualização pública. No entanto, certos aspectos poderão exigir uma [assinatura Premium do Azure AD](active-directory-get-started-premium.md) quando tiverem disponibilidade geral.
> 
> 

## <a name="introduction"></a>Introdução
Esse recurso é usado por administradores e desenvolvedores para especificar os tempos de vida de tokens emitidos pelo Azure AD. Os tempos de vida de token podem ser configurados para todos os aplicativos em um locatário, para um aplicativo multilocatário ou para uma entidade de serviço específica em um locatário.

No Azure AD, um objeto de política representa um conjunto de regras aplicadas a todos os aplicativos ou a aplicativos individuais em um locatário.  Cada tipo de política tem uma estrutura exclusiva com um conjunto de propriedades que são aplicadas aos objetos aos quais são atribuídas.

Uma política pode ser designada como o padrão para um locatário. Essa política entra em vigor em qualquer aplicativo que resida no locatário, desde que não seja substituída por uma política com uma prioridade mais alta. Políticas também podem ser atribuídas a aplicativos específicos. A ordem de prioridade varia por tipo de política.

As políticas de tempo de vida de token pode ser configuradas para tokens de atualização, tokens de acesso, tokens de sessão e tokens de ID.

### <a name="access-tokens"></a>Tokens de acesso
Um token de acesso é usado por um cliente para acessar um recurso protegido. Um token de acesso só pode ser usado para uma combinação específica de usuário, cliente e recurso. Tokens de acesso não podem ser revogados e são válidos até sua expiração. Um ator mal-intencionado que tenha obtido um token de acesso pode usá-lo pela extensão do tempo de vida.  Ajustar o tempo de vida do token de acesso é uma compensação entre a melhorar o desempenho do sistema e aumentar o tempo pelo qual o cliente retém acesso depois que a conta do usuário é desabilitada.  Um melhor desempenho do sistema é obtido, reduzindo o número de vezes que um cliente precisa adquirir um novo token de acesso. 

### <a name="refresh-tokens"></a>Tokens de atualização
Quando um cliente adquire um token de acesso para acessar um recurso protegido, ele recebe um token de atualização e um token de acesso. O token de atualização é usado para obter novos pares de tokens de acesso/atualização quando o token de acesso atual expira. Tokens de atualização são vinculados a combinações de usuário e cliente. Eles podem ser revogados, e sua validade é verificada sempre que são usados.

É importante fazer uma distinção entre clientes públicos e confidenciais. Clientes confidenciais são aplicativos que são capazes de armazenar com segurança uma senha do cliente, permitindo comprovar que as solicitações são provenientes do aplicativo cliente e não de um ator mal-intencionado. Como esses fluxos são mais seguros, os tempos de vida padrão de tokens de atualização emitidos para esses fluxos são maiores e não podem ser alterados usando a política de.

Devido às limitações do ambiente em que os aplicativos são executados, clientes públicos não podem armazenar com segurança uma senha do cliente. Políticas podem ser configuradas em recursos para impedir que tokens de atualização de clientes públicos mais antigos do que um período especificado obtenham um novo par de tokens de acesso/atualização (Tempo Máximo Inativo de Token de Atualização).  Além disso, as políticas podem ser usadas para definir um período de tempo além do qual os tokens de atualização não são mais aceitos (Idade Máx. do Token de Atualização).  Ajustar o tempo de vida do token de atualização permite que você controle quando e com que frequência o usuário precisa reinserir as credenciais em vez de ser autenticado novamente de forma silenciosa ao usar um aplicativo cliente público.

### <a name="id-tokens"></a>Tokens de ID
Tokens de ID são passados para sites e clientes nativos e contêm informações de perfil sobre um usuário. Um token de ID é associado a uma combinação específica de cliente e usuário. Tokens de ID são considerados válidos até a expiração.  Normalmente, um aplicativo Web corresponde o tempo de vida de sessão de um usuário no aplicativo ao tempo de vida do token de ID emitido para o usuário.  Ajustar o tempo de vida do token de ID permite que você controle com que frequência o aplicativo Web expirará a sessão do aplicativo e exija que o usuário seja autenticado novamente com o Azure AD (de forma silenciosa ou interativa).

### <a name="single-sign-on-session-token"></a>Token de sessão de logon único
Quando um usuário se autentica no Azure AD e marca a caixa "Manter-me conectado", uma única sessão de logon é estabelecida com o navegador do usuário e o Azure AD.  O Token de Sessão de Logon Único, na forma de um cookie, representa essa sessão. É importante observar que o token de sessão SSO não está associado a um aplicativo cliente/recurso específico. Tokens de sessão de SSO podem ser revogados, e sua validade é verificada sempre que eles são usados.

Há dois tipos de tokens de sessão de SSO. Tokens de sessão persistentes são armazenados como cookies persistentes pelo navegador, e tokens de sessão não persistentes são armazenados como cookies de sessão (eles são destruídos quando o navegador é fechado).

Tokens de sessão não persistentes têm um tempo de vida de 24 horas, enquanto tokens persistentes têm um tempo de vida de 180 dias. Sempre que o token de sessão SSO é usado dentro do período de validade, o período de validade é estendido por 24 horas ou 180 dias. Se o token de sessão de SSO não for usado dentro do período de validade, será considerado expirado e não será aceito. 

Políticas podem ser usadas para definir um período de tempo após o primeiro token de sessão ser emitido, além do qual os tokens de sessão não são mais aceitos (Idade Máxima de Token de Sessão).  Ajustar o tempo de vida do token de sessão permite que você controle quando e com que frequência o usuário precisa reinserir as credenciais em vez de ser autenticado novamente de forma silenciosa ao usar um aplicativo Web.

### <a name="token-lifetime-policy-properties"></a>Propriedades da política de tempo de vida de token
Uma política de tempo de vida do token é um tipo de objeto de política que contém regras de tempo de vida do token.  As propriedades da política são usadas para controlar tempos de vida de tokens especificados.  Se nenhuma política for definida, o sistema aplicará o valor de tempo de vida padrão.

### <a name="configurable-token-lifetime-properties"></a>Propriedades de tempo de vida de token configurável
| Propriedade | Cadeia de caracteres de propriedade de política | Afeta | Padrão | Mínimo | Máximo |
| --- | --- | --- | --- | --- | --- |
| Tempo de Vida do Token de Acesso |AccessTokenLifetime |Tokens de acesso, tokens de ID, tokens SAML2 |1 hora |10 minutos |1 dia |
| Tempo Máximo Inativo de Token de Atualização |MaxInactiveTime |Tokens de atualização |14 dias |10 minutos |90 dias |
| Idade Máxima de Token de Atualização de Fator Único |MaxAgeSingleFactor |Tokens de atualização (para quaisquer usuários) |90 dias |10 minutos |Until-revoked* |
| Idade Máxima de Token de Atualização Multifator |MaxAgeMultiFactor |Tokens de atualização (para quaisquer usuários) |90 dias |10 minutos |Until-revoked* |
| Idade Máxima de Token de Sessão de Fator Único |MaxAgeSessionSingleFactor** |Tokens de sessão (persistentes e não persistentes) |Until-revoked |10 minutos |Until-revoked* |
| Idade Máxima de Token de Sessão Multifator |MaxAgeSessionMultiFactor*** |Tokens de sessão (persistentes e não persistentes) |Until-revoked |10 minutos |Until-revoked* |

* *365 dias é o comprimento máximo explícito que pode ser definido para esses atributos.
* **Se MaxAgeSessionSingleFactor não for definido, esse valor usará o valor de MaxAgeSingleFactor. Se nenhum parâmetro for definido, a propriedade usará o valor padrão (Until-revoked).
* ***Se MaxAgeSessionMultiFactor, não for definido esse valor usará o valor de MaxAgeMultiFactor. Se nenhum parâmetro for definido, a propriedade usará o valor padrão (Until-revoked).

### <a name="exceptions"></a>Exceções
| Propriedade | Afeta | Padrão |
| --- | --- | --- |
| Tempo Máximo Inativo de Token de Atualização (usuários federados com informações de revogação insuficientes) |Tokens de atualização (emitidos para usuários federados com informações de revogação insuficientes) |12 horas |
| Tempo Máximo Inativo de Token de Atualização (Clientes Confidenciais) |Tokens de atualização (Emitido para Clientes Confidenciais) |90 dias |
| Idade Máxima de Token de Atualização (Emitido para Clientes Confidenciais) |Tokens de atualização (Emitido para Clientes Confidenciais) |Until-revoked |

### <a name="priority-and-evaluation-of-policies"></a>Prioridade e avaliação de políticas
As políticas de tempo de vida de tokens podem ser criadas e atribuídas a aplicativos, locatários e entidades de serviço específicos. Isso significa que é possível que várias políticas se apliquem a um aplicativo específico. A política de Tempo de Vida de Token entra em vigor seguindo estas regras:

* Se uma política for atribuída explicitamente à entidade de serviço, será imposta. 
* Se nenhuma política for explicitamente atribuída à entidade de serviço, uma política explicitamente atribuída ao locatário pai da entidade de serviço será imposta. 
* Se nenhuma política foi atribuída explicitamente à entidade de serviço ou ao locatário, a política atribuída ao aplicativo será imposta. 
* Se nenhuma política tiver sido atribuída à entidade de serviço, ao locatário ou ao objeto de aplicativo, os valores padrão serão aplicados (confira a tabela acima).

Para obter mais informações sobre a relação entre objetos de aplicativo e de entidade de serviço no Azure AD, confira [Objetos de aplicativos e entidade de serviço no Azure Active Directory](active-directory-application-objects.md).

A validade do token é avaliada no momento em que ele é usado. A política com a prioridade mais alta no aplicativo que está sendo acessado entra em vigor.

> [!NOTE]
> Exemplo
> 
> Um usuário deseja acessar dois aplicativos Web, A e B. 
> 
> * Ambos os aplicativos estão no mesmo locatário pai. 
> * A política de tempo de vida de token 1 com uma Idade Máxima de Token de Sessão de oito horas é definida como o padrão do locatário pai.
> * O aplicativo Web A é um aplicativo de uso normal e não está vinculado a políticas. 
> * O aplicativo Web B é usado para processos altamente confidenciais, e sua entidade de serviço está vinculada à política de tempo de vida de token 2 com uma Idade Máxima de Token de Sessão de 30 minutos.
> 
> Às 12h, o usuário abre uma nova sessão do navegador e tenta acessar o aplicativo Web A. o usuário é redirecionado para o Azure AD e é solicitado a entrar. Isso coloca um cookie com um token de sessão no navegador. O usuário é redirecionado para o aplicativo Web A com um token de ID que permite acessar o aplicativo.
> 
> Às 12h15, o usuário tentar acessar o aplicativo Web B. O navegador é redirecionado para o Azure AD, que detecta o cookie de sessão. A entidade de serviço do aplicativo Web B está vinculada à política 2, mas também faz parte do locatário pai com a política padrão 1. A política 2 entra em vigor, pois as políticas vinculadas a entidades de serviço têm uma prioridade maior do que as políticas padrão de locatário. O token de sessão foi originalmente emitido nos últimos 30 minutos. Portanto, ele é considerado válido. O usuário é redirecionado para o aplicativo Web B com um token de ID que lhe concede acesso.
> 
> Às 13h, o usuário tentar navegar para o aplicativo Web A. O usuário é redirecionado para o Azure AD. O aplicativo Web A não está vinculado a políticas, mas como está em um locatário com a política padrão 1, essa política entra em vigor. Foi detectado um cookie de sessão que foi originalmente emitido nas últimas oito horas, e o usuário é redirecionado silenciosamente para o aplicativo Web A com um novo token de ID, sem precisar se autenticar.
> 
> O usuário tenta imediatamente acessar o aplicativo Web B. O usuário é redirecionado para o Azure AD. Como antes, a política 2 entra em vigor. Como o token foi emitido há mais de 30 minutos, o usuário é solicitado a digitar novamente suas credenciais, e uma nova sessão e token de ID são emitidos. O usuário pode acessar o aplicativo Web B.
> 
> 

## <a name="configurable-policy-properties-in-depth"></a>Propriedades de regras configuráveis: detalhada
### <a name="access-token-lifetime"></a>Tempo de vida do token de acesso
**Cadeia de caracteres:** AccessTokenLifetime

**Afeta:** tokens de acesso, tokens de ID

**Resumo:** essa política controla por quanto tempo tokens de acesso e ID para esse recurso são considerados válidos. Reduzir o tempo de vida de token de acesso minimiza o risco de que um token de acesso ou ID seja usado por um ator mal-intencionado por um longo período de tempo (pois eles não podem ser revogados), mas prejudica o desempenho, pois os tokens terão que ser substituídos com mais frequência.

### <a name="refresh-token-max-inactive-time"></a>Tempo máximo inativo de token de atualização
**Cadeia de caracteres:** MaxInactiveTime

**Afeta:** tokens de atualização

**Resumo:** essa política controla a idade que um token de atualização pode ter antes que um cliente não possa mais usá-lo para recuperar um novo par de tokens de atualização/acesso ao tentar acessar esse recurso. Como um novo token de atualização normalmente é retornado quando um token de atualização é usado, o cliente não deve ter utilizado qualquer recurso usando o token de atualização atual pelo período especificado de tempo antes que essa política impedisse o acesso. 

Essa política forçará os usuários que não estão ativos no cliente a se autenticarem novamente para recuperar um novo token de atualização. 

É importante observar que o Tempo Máximo Inativo de Token de Atualização deve ser definido como um valor menor do que Idade Máxima de Token de Fator Único e Idade Máxima de Token de Atualização multifator.

### <a name="single-factor-refresh-token-max-age"></a>Idade máxima de token de atualização de fator único
**Cadeia de caracteres:** MaxAgeSingleFactor

**Afeta:** tokens de atualização

**Resumo:** essa política controla por quanto tempo um usuário pode continuar a usar tokens de atualização para obter novos pares de tokens de acesso/atualização após a última vez em que se autenticou com êxito com fator único. Quando um usuário for autenticado e receber um novo token de atualização, poderá usar o fluxo de tokens de atualização (desde que o token de atualização atual não tenha sido revogado e não permaneça não utilizado por mais tempo do que o tempo de inatividade) para o período de tempo especificado. Nesse ponto, os usuários serão forçados a se autenticar novamente para receber um novo token de atualização. 

Reduzir a idade máxima forçará os usuários a se autenticarem com mais frequência. Como a autenticação de fator único é considerada menos segura do que uma autenticação multifator, é recomendável que essa política seja definida com um valor igual ou menor do que a Política de Idade Máxima de Token de Atualização Multifator.

### <a name="multi-factor-refresh-token-max-age"></a>Idade máxima de token de atualização multifator
**Cadeia de caracteres:** MaxAgeMultiFactor

**Afeta:** tokens de atualização

**Resumo:** essa política controla por quanto tempo um usuário pode continuar a usar tokens de atualização para obter novos pares de tokens de acesso/atualização após a última vez em que se autenticou com êxito com multifator. Quando um usuário for autenticado e receber um novo token de atualização, poderá usar o fluxo de tokens de atualização (desde que o token de atualização atual não tenha sido revogado e não permaneça não utilizado por mais tempo do que o tempo de inatividade) para o período de tempo especificado. Nesse ponto, os usuários serão forçados a se autenticar novamente para receber um novo token de atualização. 

Reduzir a idade máxima forçará os usuários a se autenticarem com mais frequência. Como a autenticação de fator único é considerada menos segura do que uma autenticação multifator, é recomendável que essa política seja definida com um valor igual ou maior do que a Política de Idade Máxima de Token de Atualização de Fator Único.

### <a name="single-factor-session-token-max-age"></a>Idade máxima de token de sessão de fator único
**Cadeia de caracteres:** MaxAgeSessionSingleFactor

**Afeta:** tokens de sessão (persistentes e não persistentes)

**Resumo:** essa política controla por quanto tempo um usuário pode continuar a usar tokens de sessão para obter novos tokens de ID e de sessão desde a última vez em que se autenticou com êxito com fator único. Quando um usuário for autenticado e receber um novo token de sessão, poderá usar o fluxo de tokens de sessão (desde que o token de sessão atual não tenha sido revogado ou expirado) pelo período de tempo especificado. Nesse ponto, os usuários serão forçados a se autenticar novamente para receber um novo token de sessão. 

Reduzir a idade máxima forçará os usuários a se autenticarem com mais frequência. Como a autenticação de fator único é considerada menos segura do que uma autenticação multifator, é recomendável que essa política seja definida com um valor igual ou menor do que a Política de Idade Máxima de Token de Sessão Multifator.

### <a name="multi-factor-session-token-max-age"></a>Idade máxima de token de sessão multifator
**Cadeia de caracteres:** MaxAgeSessionMultiFactor

**Afeta:** tokens de sessão (persistentes e não persistentes)

**Resumo:** essa política controla por quanto tempo um usuário pode continuar a usar tokens de sessão para obter novos tokens de ID e de sessão desde a última vez em que se autenticou com êxito com multifator. Quando um usuário for autenticado e receber um novo token de sessão, poderá usar o fluxo de tokens de sessão (desde que o token de sessão atual não tenha sido revogado ou expirado) pelo período de tempo especificado. Nesse ponto, os usuários serão forçados a se autenticar novamente para receber um novo token de sessão. 

Reduzir a idade máxima forçará os usuários a se autenticarem com mais frequência. Como a autenticação de fator único é considerada menos segura do que uma autenticação multifator, é recomendável que essa política seja definida com um valor igual ou maior do que a Política de Idade Máxima de Token de Sessão de Fator Único.

## <a name="sample-token-lifetime-policies"></a>Exemplos de políticas de tempo de vida do token
A capacidade de criar e gerenciar tempos de vida de token para aplicativos, entidades de serviço e seu locatário geral expõe todos os tipos de novos cenários possíveis no Azure AD.  Vamos examinar alguns cenários comuns de políticas que o ajudarão a impor novas regras para:

* Tempos de vida do token
* Tempos Máximos Inativos de Token
* Idade Máxima de Token

Vamos examinar alguns cenários, incluindo:

* Gerenciar a política padrão de um locatário
* Criar uma política para entrada na Web
* Criar uma política para aplicativos nativos chamando uma API Web
* Gerenciar uma política avançada 

### <a name="prerequisites"></a>Pré-requisitos
Nos cenários de exemplo, criaremos, atualizaremos, vincularemos e excluiremos políticas em aplicativos, em entidades de serviço e no locatário geral.  Se você for novo no Azure AD, confira [este artigo](active-directory-howto-tenant.md) para ajudá-lo a começar antes de prosseguir com estes exemplos.  

1. Para começar, baixe a versão mais recente do [Azure AD PowerShell Cmdlet Preview](https://www.powershellgallery.com/packages/AzureADPreview). 
2. Depois que tiver os Cmdlets do Azure AD PowerShell, execute o comando Conectar para entrar em sua conta de administrador do Azure AD. Você precisará fazer isso sempre que iniciar uma nova sessão.
   
     Connect-AzureAD -Confirm
3. Execute o comando a seguir para ver todas as políticas que foram criadas no locatário.  Este comando deve ser usado após a maioria das operações nos cenários a seguir.  Ele também o ajuda a obter a **ID de objeto** de suas políticas. 
   
     Get-AzureADPolicy

### <a name="sample-managing-a-tenants-default-policy"></a>Exemplo: gerenciamento de política padrão de um locatário
Neste exemplo, criaremos uma política que permite que os usuários entrem com menos frequência no locatário inteiro. 

Para fazer isso, podemos criar uma política de tempo de vida de token para Tokens de Atualização de Fator Único que é aplicada em seu locatário. Essa política será aplicada a todos os aplicativos no locatário e a cada entidade de serviço que ainda não tenha uma política definida para ela. 

1. **Crie uma Política de Tempo de Vida do Token.** 

Defina o Token de Atualização de Fator Único como "until-revoked", o que significa que ele não expira até que o acesso seja revogado.  A definição de política abaixo é a que vamos criar:

        @("{
          `"TokenLifetimePolicy`":
              {
                 `"Version`":1, 
                 `"MaxAgeSingleFactor`":`"until-revoked`"
              }
        }")

Em seguida, execute este comando para criar a política. 

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1, `"MaxAgeSingleFactor`":`"until-revoked`"}}") -DisplayName TenantDefaultPolicyScenario -IsTenantDefault $true -Type TokenLifetimePolicy

Para ver a nova política e obter seu ObjectID, execute o comando a seguir.

    Get-AzureADPolicy
&nbsp;&nbsp;2.    **Atualizar a política**

Você decidiu que a primeira política não é tão rígida quanto o serviço requer e decidiu que deseja que os Tokens de Atualização de Fator Único expirem em dois dias. Execute o comando a seguir. 

    Set-AzureADPolicy -ObjectId <ObjectID FROM GET COMMAND> -DisplayName TenantDefaultPolicyUpdatedScenario -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxAgeSingleFactor`":`"2.00:00:00`"}}")

&nbsp;&nbsp;3. **Pronto!** 

### <a name="sample-creating-a-policy-for-web-sign-in"></a>Exemplo: criar uma política para entrada na Web
Neste exemplo, criaremos uma política que exige que os usuários se autentiquem com mais frequência no aplicativo Web. Essa política definirá o tempo de vida dos Tokens de acesso/Id e Max Age de um Token de Sessão Multifator para a entidade de serviço do aplicativo Web.

1. **Crie uma Política de Tempo de Vida do Token.**

Essa política para Entrada na Web definirá o tempo de vida do Token de Acesso/Id e a Idade Máxima do de Token de Sessão de Fator Único como duas horas.

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"AccessTokenLifetime`":`"02:00:00`",`"MaxAgeSessionSingleFactor`":`"02:00:00`"}}") -DisplayName WebPolicyScenario -IsTenantDefault $false -Type TokenLifetimePolicy

Para ver a nova política e obter seu ObjectID, execute o comando a seguir.

    Get-AzureADPolicy
&nbsp;&nbsp;2.    **Atribuir a política à entidade de serviço.**

Vamos vincular essa nova política a uma entidade de serviço.  Você também precisará de uma maneira de acessar o **ObjectId** de sua entidade de serviço. Você pode consultar o [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) ou acessar a [Graph Explorer Tool](https://graphexplorer.cloudapp.net/) e entrar em sua conta do Azure AD para ver todas as entidades de serviço do locatário. 

Depois que você tiver o **ObjectId**, execute o comando a seguir.

    Add-AzureADServicePrincipalPolicy -ObjectId <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
&nbsp;&nbsp;3.    **Pronto!** 

 

### <a name="sample-creating-a-policy-for-native-apps-calling-a-web-api"></a>Exemplo: criar uma política para aplicativos nativos chamando uma API Web
> [!NOTE]
> A vinculação de políticas a aplicativos está desabilitada atualmente.  Estamos trabalhando para habilitar isso em breve.  Essa página será atualizada assim que o recurso estiver disponível.
> 
> 

Neste exemplo, criaremos uma política que exige que os usuários se autentiquem menos e aumentaremos o tempo pelo qual podem ficar inativos sem precisarem se autenticar novamente. A política será aplicada à API Web. Assim, quando o Aplicativo Nativo a solicitar como um recurso, essa política será aplicada.

1. **Crie uma Política de Tempo de Vida do Token.** 

Esse comando criará uma política estrita para uma API Web. 

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxInactiveTime`":`"30.00:00:00`",`"MaxAgeMultiFactor`":`"until-revoked`",`"MaxAgeSingleFactor`":`"180.00:00:00`"}}") -DisplayName WebApiDefaultPolicyScenario -IsTenantDefault $false -Type TokenLifetimePolicy

Para ver a nova política e obter seu ObjectID, execute o comando a seguir.

    Get-AzureADPolicy

&nbsp;&nbsp;2.    **Atribuir a política à API Web**.

Vamos vincular essa nova política a um aplicativo.  Você também precisará de uma maneira de acessar o **ObjectId** do aplicativo. A melhor maneira de localizar o aplicativo **ObjectId** é usar o [Portal do Azure](https://portal.azure.com/). 

Depois que você tiver o **ObjectId**, execute o comando a seguir.

    Add-AzureADApplicationPolicy -ObjectId <ObjectID of the App> -RefObjectId <ObjectId of the Policy>

&nbsp;&nbsp;3.    **Pronto!** 

### <a name="sample-managing-an-advanced-policy"></a>Exemplo: gerenciamento de uma política avançada
Neste exemplo, criaremos algumas políticas para demonstrar como o sistema de prioridade funciona e como você pode gerenciar várias políticas aplicadas a vários objetos. Isso fornecerá alguns esclarecimentos sobre a prioridade das políticas explicadas acima e também o ajudará a gerenciar cenários mais complexos. 

1. **Criar uma Política de Tempo de Vida de Token**

Até aqui, é bastante simples. Criamos uma política padrão de locatário que define o tempo de vida do Token de Atualização de Fator Único como 30 dias. 

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxAgeSingleFactor`":`"30.00:00:00`"}}") -DisplayName ComplexPolicyScenario -IsTenantDefault $true -Type TokenLifetimePolicy
Para ver a nova política e obter seu ObjectID, execute o comando a seguir.

    Get-AzureADPolicy

&nbsp;&nbsp;2.    **Atribuir a política a uma entidade de serviço**

Agora temos uma política no locatário inteiro.  Digamos que queiramos manter essa política de 30 dias para uma entidade de serviço específica, mas alterar a política padrão de locatário para ser o limite superior de "until-revoked". 

Primeiro, vamos vincular essa nova política à entidade de serviço.  Você também precisará de uma maneira de acessar o **ObjectId** de sua entidade de serviço. Você pode consultar o [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) ou acessar a [Graph Explorer Tool](https://graphexplorer.cloudapp.net/) e entrar em sua conta do Azure AD para ver todas as entidades de serviço do locatário. 

Depois que você tiver o **ObjectId**, execute o comando a seguir.

    Add-AzureADServicePrincipalPolicy -ObjectId <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>

&nbsp;&nbsp;3.    **Defina o sinalizador IsTenantDefault como false usando o seguinte comando**. 

    Set-AzureADPolicy -ObjectId <ObjectId of Policy> -DisplayName ComplexPolicyScenario -IsTenantDefault $false
&nbsp;&nbsp;4.    **Criar uma nova Política Padrão de Locatário**

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxAgeSingleFactor`":`"until-revoked`"}}") -DisplayName ComplexPolicyScenarioTwo -IsTenantDefault $true -Type TokenLifetimePolicy

&nbsp;&nbsp;5.     **Pronto!** 

Agora você tem a política original vinculada à entidade de serviço e a nova política definida como a política padrão de locatário.  É importante lembrar que as políticas aplicadas a entidades de serviço têm prioridade sobre as políticas padrão de locatário. 

## <a name="cmdlet-reference"></a>Referência de cmdlet
### <a name="manage-policies"></a>Gerenciar políticas
Os cmdlets a seguir podem ser usados para gerenciar políticas.</br></br>

#### <a name="new-azureadpolicy"></a>New-AzureADPolicy
Cria uma nova política.

    New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsTenantDefault <boolean> -Type <Policy Type> 

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| -Definition |A matriz de stringified JSON que contém todas as regras da política. |-Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxInactiveTime`":`"20:00:00`"}}") |
| -DisplayName |Cadeia de caracteres do nome da política |-DisplayName MyTokenPolicy |
| -IsTenantDefault |Se true, define a política como política padrão do locatário; se false, não faz nada |-IsTenantDefault $true |
| -Type |O tipo de política; para tempos de vida de token, sempre use "TokenLifetimePolicy" |-Type TokenLifetimePolicy |
| -AlternativeIdentifier [Opcional] |Define uma id alternativa para a política. |-AlternativeIdentifier myAltId |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
Obtém todas as Políticas do AzureAD ou a política especificada 

    Get-AzureADPolicy 

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| -ObjectId [Opcional] |A Id de objeto da Política que você deseja obter. |-ObjectId &lt;ObjectID da Política&gt; |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
Obtém todos os aplicativos e entidades de serviço vinculados a uma política

    Get-AzureADPolicyAppliedObject -ObjectId <object id of policy> 

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| -ObjectId |A Id de objeto da Política que você deseja obter. |-ObjectId &lt;ObjectID da Política&gt; |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
Atualiza uma política existente

    Set-AzureADPolicy -ObjectId <object id of policy> -DisplayName <string> 

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| -ObjectId |A Id de objeto da Política que você deseja obter. |-ObjectId &lt;ObjectID da Política&gt; |
| -DisplayName |Cadeia de caracteres do nome da política |-DisplayName MyTokenPolicy |
| -Definition [Opcional] |A matriz de stringified JSON que contém todas as regras da política. |-Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxInactiveTime`":`"20:00:00`"}}") |
| -IsTenantDefault [Opcional] |Se true, define a política como política padrão do locatário; se false, não faz nada |-IsTenantDefault $true |
| -Type [Opcional] |O tipo de política; para tempos de vida de token, sempre use "TokenLifetimePolicy" |-Type TokenLifetimePolicy |
| -AlternativeIdentifier [Opcional] |Define uma id alternativa para a política. |-AlternativeIdentifier myAltId |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remove-AzureADPolicy
Exclui a política especificada

     Remove-AzureADPolicy -ObjectId <object id of policy>

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| -ObjectId |A Id de objeto da Política que você deseja obter. |-ObjectId &lt;ObjectID da Política&gt; |

</br></br>

### <a name="application-policies"></a>Políticas de aplicativo
Os cmdlets a seguir podem ser usados para políticas de aplicativos.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADApplicationPolicy
Vincula a política especificada a um aplicativo

    Add-AzureADApplicationPolicy -ObjectId <object id of application> -RefObjectId <object id of policy>

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| -ObjectId |A Id de objeto do Aplicativo. |-ObjectId &lt;ObjectID do Aplicativo&gt; |
| -RefObjectId |A Id de objeto da Política. |-RefObjectId &lt;ObjectID da Política&gt; |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
Obtém a política atribuída a um aplicativo

    Get-AzureADApplicationPolicy -ObjectId <object id of application>

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| -ObjectId |A Id de objeto do Aplicativo. |-ObjectId &lt;ObjectID do Aplicativo&gt; |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remove-AzureADApplicationPolicy
Remove uma política de um aplicativo

    Remove-AzureADApplicationPolicy -ObjectId <object id of application> -PolicyId <object id of policy>

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| -ObjectId |A Id de objeto do Aplicativo. |-ObjectId &lt;ObjectID do Aplicativo&gt; |
| -PolicyId |O ObjectId da política. |-PolicyId &lt;ObjectID da Política&gt; |

</br></br>

### <a name="service-principal-policies"></a>Políticas de entidade de serviço
Os cmdlets a seguir podem ser usados para políticas de entidade de serviço.</br></br>

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
Vincula a política especificada a uma entidade de serviço

    Add-AzureADServicePrincipalPolicy -ObjectId <object id of service principal> -RefObjectId <object id of policy>

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| -ObjectId |A Id de objeto do Aplicativo. |-ObjectId &lt;ObjectID do Aplicativo&gt; |
| -RefObjectId |A Id de objeto da Política. |-RefObjectId &lt;ObjectID da Política&gt; |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
Obtém qualquer política vinculada à entidade de serviço especificada

    Get-AzureADServicePrincipalPolicy -ObjectId <object id of service principal>

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| -ObjectId |A Id de objeto do Aplicativo. |-ObjectId &lt;ObjectID do Aplicativo&gt; |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
Remove a política da entidade de serviço especificada

    Remove-AzureADServicePrincipalPolicy -ObjectId <object id of service principal>  -PolicyId <object id of policy>

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| -ObjectId |A Id de objeto do Aplicativo. |-ObjectId &lt;ObjectID do Aplicativo&gt; |
| -PolicyId |O ObjectId da política. |-PolicyId &lt;ObjectID da Política&gt; |




<!--HONumber=Nov16_HO3-->


