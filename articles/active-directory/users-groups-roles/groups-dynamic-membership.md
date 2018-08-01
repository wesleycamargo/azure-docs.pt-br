---
title: Regras de associação de grupo dinâmica baseada em atributos no Azure Active Directory | Microsoft Docs
description: Como criar regras avançadas para uma associação de grupo dinâmico, incluindo parâmetros e operadores de regra de expressões com suporte.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 07/24/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: e49da237584a48c01e72552abae01da2514da3c1
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248882"
---
# <a name="create-dynamic-groups-with-attribute-based-membership-in-azure-active-directory"></a>Criar grupos dinâmicos com associação baseada em atributo no Active Directory do Azure

No Active Directory do Azure (Azure AD), você pode criar regras complexas baseadas em atributos para habilitar associações dinâmicas para grupos. Este artigo detalha os atributos e a sintaxe para criar regras de associação dinâmica para usuários ou dispositivos. Você pode configurar uma regra de associação dinâmica em grupos de segurança ou em grupos do Office 365.

Quando os atributos de um usuário ou um dispositivo são alterados, o sistema avalia todas as regras de grupo dinâmicas em um diretório para ver se a alteração dispararia adições ou remoções de grupo. Se um usuário ou dispositivo atender a uma regra em um grupo, ele será adicionado como membro desse grupo. Se não atenderem mais à regra, eles serão removidos.

> [!NOTE]
> Este recurso requer uma licença do Azure AD Premium P1 para cada usuário exclusivo que for um membro de um ou mais grupos dinâmicos. Você não precisa atribuir licenças aos usuários para que eles sejam membros de grupos dinâmicos, mas é necessário ter o número mínimo de licenças no locatário para cobrir todos esses usuários. Por exemplo, se você tiver o total de 1.000 usuários exclusivos em todos os grupos dinâmicos no locatário, serão necessárias pelo menos 1.000 licenças do Azure AD Premium P1 para atender ao requisito de licença.
>
> Você pode criar um grupo dinâmico para dispositivos ou usuários, mas não pode criar uma regra que contenha tanto usuários quanto dispositivos.
> 
> Nesse momento não é possível criar um grupo de dispositivos com base em atributos do usuário proprietário. As regras de associação de dispositivo só podem fazer referência a atributos imediatos dos objetos de dispositivo no diretório.

## <a name="to-create-an-advanced-rule"></a>Para criar uma regra avançada

1. Entre no [centro de administração do Azure AD](https://aad.portal.azure.com) com uma conta que seja um administrador global ou um administrador da conta de usuário.
2. Selecione **Usuários e grupos**.
3. Selecione **Todos os grupos** e selecione **Novo grupo**.

   ![Adicione o novo grupo](./media/groups-dynamic-membership/new-group-creation.png)

4. Na folha **Grupo** , insira um nome e uma descrição para o novo grupo. Selecione o **Tipo de associação** **Usuário Dinâmico** ou **Dispositivo Dinâmico**, dependendo se você deseja criar uma regra para usuários ou dispositivos e, em seguida, selecione **Adicionar consulta dinâmica**. Você pode usar o construtor de regra para criar uma regra simples ou gravar uma regra avançada por conta própria. Este artigo contém mais informações sobre os atributos de usuário e dispositivo disponíveis, bem como exemplos de regras avançadas.

   ![Adicionar regra de associação dinâmica](./media/groups-dynamic-membership/add-dynamic-group-rule.png)

5. Depois de criar a regra, selecione **Adicionar consulta** na parte inferior da folha.
6. Selecione **Criar** on the **Grupo** para criar o grupo.

> [!TIP]
> A criação de grupo falha quando a regra inserida é formada incorretamente ou não é válida. Uma notificação é exibida no canto superior direito do portal, contendo uma explicação de por que a regra não pôde ser processada. Leia com cuidado para entender como você precisa ajustar a regra para torná-la válida.

## <a name="status-of-the-dynamic-rule"></a>Status da regra dinâmica

Veja o status do processamento de associação e a data da última atualização na página Visão Geral do grupo dinâmico.
  
  ![exibição de status do grupo dinâmico](./media/groups-dynamic-membership/group-status.png)


As seguintes mensagens de status podem ser mostradas para o status **Processamento de associação**:

* **Avaliando**: a alteração do grupo foi recebida e as atualizações estão sendo avaliadas.
* **Processando**: as atualizações estão sendo processadas.
* **Atualização concluída**: o processamento foi concluído e todas as atualizações aplicáveis foram feitas.
* **Erro de processamento**: ocorreu um erro ao avaliar a regra de associação e o processamento não pôde ser concluído.
* **Atualização em pausa**: as atualizações da regra de associação dinâmica foram pausadas pelo administrador. MembershipRuleProcessingState é definido como "Em pausa".

As seguintes mensagens de status podem ser mostradas para o status da **Última atualização da associação**:

* &lt;**Data e hora**&gt;: a última vez em que a associação foi atualizada.
* **Em Andamento**: as atualizações estão em andamento no momento.
* **Desconhecido**: não é possível recuperar a hora da última atualização. O motivo pode ser que o grupo foi criado recentemente.

Se ocorrer um erro ao processar a regra de associação de um grupo específico, um alerta será mostrado na parte superior da **página Visão Geral** do grupo. Se nenhuma atualização de associação dinâmica pendente puder ser processada de nenhum dos grupos do locatário por mais de 24 horas, um alerta será mostrado na parte superior da **Todos os grupos**.

![mensagem de erro de processamento](./media/groups-dynamic-membership/processing-error.png)

## <a name="constructing-the-body-of-an-advanced-rule"></a>Construção do corpo de uma regra avançada

A regra avançada que você pode criar para os membros dinâmicos para grupos é essencialmente uma expressão binária que consiste em três partes e resulta em um resultado verdadeiro ou falso. As três partes são:

* Parâmetro da esquerda
* Operador binário
* Constante à direita

Uma regra avançada completa é semelhante a esta: (leftParameter binaryOperator "RightConstant"), em que os parênteses de abertura e fechamento são opcionais para toda a expressão binária, as aspas duplas também são opcionais, sendo necessárias somente para a constante à direita quando ela for uma cadeia de caracteres e a sintaxe do parâmetro esquerdo é user.property. Uma regra avançada pode consistir em mais de uma expressão binária separada pelos operadores lógicos -and, -or e -not.

A seguir é exemplos de uma regra avançada construída de maneira adequada:
```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```
Para a lista completa de parâmetros com suporte e operadores de regra de expressão, consulte as seções a seguir. Para os atributos usados para regras de dispositivo, consulte [Usando atributos para criar regras para objetos de dispositivo](#using-attributes-to-create-rules-for-device-objects).

O comprimento total do corpo da sua regra avançada não pode exceder 2048 caracteres.

> [!NOTE]
> Operações de cadeia de caracteres e regex não diferenciam maiúsculas de minúsculas. Você também pode executar verificações de Null, usando *null* como uma constante, por exemplo, user.department - eq *null*.
> As cadeias de caracteres que contém aspas " devem ser ignoradas usando caracteres ', por exemplo, user.department -eq \`"Sales".

## <a name="supported-expression-rule-operators"></a>Operadores de regra de expressão com suporte

A tabela a seguir lista todos os operadores de regra de expressão com suporte e sua sintaxe a ser usada no corpo da regra avançada:

| Operador | Sintaxe |
| --- | --- |
| Não é igual a |-ne |
| É igual a |-eq |
| Não começa com |-notStartsWith |
| Começa com |-startsWith |
| Não contém |-notContains |
| Contém: |-contains |
| Não corresponde |-notMatch |
| Corresponde |-match |
| No | -in |
| Não está em | -notIn |

## <a name="operator-precedence"></a>Precedência do operador

Todos os operadores estão listados segundo sua precedência, de baixa para alta. Operadores na mesma linha têm a mesma precedência:

````
-any -all
-or
-and
-not
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
````

Todos os operadores podem ser usados com ou sem o prefixo de hífen. Parênteses são necessários somente quando a precedência não atender às suas necessidades.
Por exemplo: 

```
   user.department –eq "Marketing" –and user.country –eq "US"
```

é equivalente a:

```
   (user.department –eq "Marketing") –and (user.country –eq "US")
```

## <a name="using-the--in-and--notin-operators"></a>Usando os operadores -In e -notIn

Se quiser comparar o valor de um atributo de usuário com um número de valores diferentes, você pode usar os operadores -In ou -notIn. Veja um exemplo de uso do operador -In:
```
   user.department -In ["50001","50002","50003",“50005”,“50006”,“50007”,“50008”,“50016”,“50020”,“50024”,“50038”,“50039”,“51100”]
```
Observe o uso de "[" e "]" no início e no final da lista de valores. Essa condição é avaliada como verdadeira se o valor de user.department for igual a um dos valores na lista.


## <a name="query-error-remediation"></a>Correção do erro de consulta

A seguinte tabela relacionará os erros comuns e como corrigi-los

| Erro de análise de consulta | Erros de uso | Uso corrigido |
| --- | --- | --- |
| Erro: O atributo não tem suportado. |(user.invalidProperty -eq "Valor") |(user.department -eq "value") A propriedade <br/><br/>Verifique se o atributo está na [lista de propriedades com suporte](#supported-properties). |
| Erro: Operador não é tem suportada no atributo. |(user.accountEnabled -contains true) |(user.accountEnabled -eq true) A propriedade <br/><br/>Não há suporte para o operador usado para o tipo de propriedade (neste exemplo, -contains não pode ser usado no tipo booliano). Use os operadores corretos para o tipo de propriedade. |
| Erro: Erro de compilação de consulta. |1. (user.department -eq "Sales") (user.department -eq "Marketing")<br/><br/>2. (user.userPrincipalName -match "*@domain.ext") |1. Operador ausente. Use -and ou -or para unir predicados<br/><br/>(user.department -eq "Sales") -or (user.department -eq "Marketing")<br/><br/>2.Erro na expressão regular usada com -match<br/><br/>(user.userPrincipalName -match ".*@domain.ext"), alternatively: (user.userPrincipalName -match "\@domain.ext$")|

## <a name="supported-properties"></a>Propriedades com suporte

Estas são todas as propriedades do usuário que você pode usar na regra avançada:

### <a name="properties-of-type-boolean"></a>Propriedades de tipo booliano

Operadores permitidos

* -eq
* -ne

| propriedades | Valores permitidos | Uso |
| --- | --- | --- |
| accountEnabled |verdadeiro, falso |user.accountEnabled -eq true |
| dirSyncEnabled |verdadeiro, falso |user.dirSyncEnabled -eq true |

### <a name="properties-of-type-string"></a>Propriedades do tipo cadeia de caracteres

Operadores permitidos

* -eq
* -ne
* -notStartsWith
* -startsWith
* -contains
* -notContains
* -match
* -notMatch
* -in
* -notIn

| propriedades | Valores permitidos | Uso |
| --- | --- | --- |
| city |Qualquer valor de cadeia de caracteres ou *null* |(user.city -eq "valor") |
| country |Qualquer valor de cadeia de caracteres ou *null* |(user.country -eq "valor") |
| companyName | Qualquer valor de cadeia de caracteres ou *null* | (user.companyName -eq "value") |
| department |Qualquer valor de cadeia de caracteres ou *null* |(user.department -eq "value") A propriedade  |
| displayName |Um valor de cadeia de caracteres. |(user. DisplayName -eq "valor") |
| employeeId |Um valor de cadeia de caracteres. |(user.employeeId -eq "valor")<br>(user.employeeId -ne *null*) |
| facsimileTelephoneNumber |Qualquer valor de cadeia de caracteres ou *null* |user.facsimileTelephoneNumber -eq ("valor") |
| givenName |Qualquer valor de cadeia de caracteres ou *null* |user.givenName -eq ("valor") |
| jobTitle |Qualquer valor de cadeia de caracteres ou *null* |(user.jobTitle - eq "valor") |
| mail |Qualquer valor de cadeia de caracteres ou *null* (endereço SMTP do usuário) |(user.mail - eq "valor") |
| mailNickName |Qualquer valor de cadeia de caracteres (alias de email do usuário) |(user.mailNickName - eq "valor") |
| Serviço Móvel |Qualquer valor de cadeia de caracteres ou *null* |(user.mobile -eq "valor") |
| ID do objeto |GUID do objeto de usuário |(user.objectId -eq "11111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | SID (ID de segurança) local para usuários que foram sincronizados do local para a nuvem. |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |None DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |Qualquer valor de cadeia de caracteres ou *null* |(user.physicalDeliveryOfficeName -eq "valor") |
| postalCode |Qualquer valor de cadeia de caracteres ou *null* |(user.postalCode - eq "valor") |
| preferredLanguage |ISO 639-1 code |(user.preferredLanguage - eq "en-US") |
| sipProxyAddress |Qualquer valor de cadeia de caracteres ou *null* |(user.sipProxyAddress -eq "valor") |
| state |Qualquer valor de cadeia de caracteres ou *null* |(user.state -eq "valor") |
| streetAddress |Qualquer valor de cadeia de caracteres ou *null* |(user.streetAddress -eq "valor") |
| sobrenome |Qualquer valor de cadeia de caracteres ou *null* |(user.surname -eq "valor") |
| telephoneNumber |Qualquer valor de cadeia de caracteres ou *null* |(user.telephoneNumber -eq "valor") |
| usageLocation |Código do país indicados dois |(user.usageLocation -eq "EUA") |
| userPrincipalName |Um valor de cadeia de caracteres. |(user.userPrincipalName -eq "alias@domain") |
| userType |member guest *null* |(ser.userType -eq "Membro") |

### <a name="properties-of-type-string-collection"></a>Propriedades de coleção de cadeias de caracteres de tipo

Operadores permitidos

* -contains
* -notContains

| propriedades | Valores permitidos | Uso |
| --- | --- | --- |
| otherMails |Um valor de cadeia de caracteres. |(user.otherMails -contains "alias@domain") |
| proxyAddresses |SMTP:alias@domainsmtp:alias@domain |(user.proxyAddresses -contains "SMTP: alias@domain") |

## <a name="multi-value-properties"></a>Propriedades de vários valores

Operadores permitidos

* -any (satisfeita quando pelo menos um item na coleção corresponde à condição)
* -all (satisfeita quando todos os itens na coleção correspondem à condição)

| propriedades | Valores | Uso |
| --- | --- | --- |
| assignedPlans |Cada objeto na coleção expõe as seguintes propriedades de cadeia de caracteres: capabilityStatus, service, servicePlanId |user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled") |
| proxyAddresses| SMTP:alias@domainsmtp:alias@domain | (user.proxyAddresses -qualquer (\_ -contém "contoso")) |

As propriedades de vários valores são coleções de objetos do mesmo tipo. Você pode usar os operadores -any e -all para aplicar uma condição a um ou todos os itens na coleção, respectivamente. Por exemplo: 

assignedPlans é uma propriedade de valores múltiplos que lista todos os planos de serviço atribuídos ao usuário. A expressão abaixo selecionará os usuários que tenham o plano do serviço Exchange Online (Plano 2) e que também estejam no estado Habilitado:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

(O identificador de GUID identifica o plano de serviço do Exchange Online (Plano 2).)

> [!NOTE]
> Isso é útil se você deseja identificar todos os usuários para os quais uma funcionalidade do Office 365 (ou outro serviço da Microsoft Online) tenha sido habilitada, por exemplo, para direcioná-los com um determinado conjunto de políticas.

A expressão a seguir seleciona todos os usuários que possuem algum plano de serviço associado ao serviço do Intune (identificado pelo nome de serviço "SCO"):
```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

### <a name="using-the-underscore--syntax"></a>Usando a sintaxe de sublinhado (\_)

A sintaxe de sublinhado (\_) corresponde a ocorrências de um valor específico em uma das propriedades de coleção de cadeias de valores variados para adicionar usuários ou dispositivos a um grupo dinâmico. Ele é usado com-qualquer ou - todos os operadores.

Aqui está um exemplo do uso do sublinhado (\_) em uma regra para adicionar membros com base em user.proxyAddress (funciona da mesma forma para user.otherMails). Essa regra adiciona qualquer usuário com endereço de proxy que contenha "contoso" ao grupo.

```
(user.proxyAddresses -any (_ -contains "contoso"))
```

## <a name="use-of-null-values"></a>Uso de valores nulos

Para especificar um valor nulo em uma regra, você pode usar o valor *null*. Tenha cuidado para não usar a palavra aspas em *null* -nesse caso, ele será interpretado como um valor de cadeia de caracteres literal. O operador -not não pode ser usado como um operador comparativo para nulo. Se for utilizado, você receberá um erro se usar nulo ou $null. Em vez disso, use -eq ou -ne. A maneira correta para referenciar o valor nulo é da seguinte maneira:
```
   user.mail –ne $null
```

## <a name="extension-attributes-and-custom-attributes"></a>Atributos de extensão e atributos personalizados
Os atributos de extensão e os atributos personalizados têm suporte das regras de associação dinâmica.

Os atributos de extensão são sincronizados no AD do Windows Server local e têm o formato "ExtensionAttributeX", em que X é igual a 1 a 15.
Um exemplo de uma regra que usa um atributo de extensão:

```
(user.extensionAttribute15 -eq "Marketing")
```

Os Atributos Personalizados são sincronizados do AD do Windows Server local ou de um aplicativo de SaaS conectado e o formato "user.extension_[GUID]\__[Attribute]", em que [GUID] é o identificador exclusivo no AAD do aplicativo que criou o atributo no Azure AD e [Attribute] é o nome do atributo como ele foi criado. Um exemplo de uma regra que usa um atributo personalizado:

```
user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  
```

O nome do atributo personalizado pode ser encontrado no diretório por meio da consulta do atributo de um usuário, usando o Graph Explorer e procurando o nome do atributo.

## <a name="direct-reports-rule"></a>Regra de "subordinados diretos"
Você pode criar um grupo contendo todos os subordinados diretos de um gerente. Quando os subordinados diretos do gerente forem alterados no futuro, a associação do grupo será ajustada automaticamente.

> [!NOTE]
> 1. Para que a regra funcione, verifique se a propriedade **ID do Gerenciador** está definida corretamente nos usuários em seu locatário. Você pode verificar o valor atual de um usuário na respectiva **guia Perfil**.
> 2. Essa regra só dá suporte a subordinados **diretos**. Atualmente, não é possível criar um grupo para uma hierarquia aninhada; por exemplo, um grupo que inclui relatórios diretos e os respectivos relatórios.
> 3. Esta regra não pode ser combinada com nenhuma outra regra avançada.

**Para configurar o grupo**

1. Siga as etapas de 1 a 5 da seção [Para criar a regra avançada](#to-create-the-advanced-rule) e selecione o **Tipo de associação** como **Usuário Dinâmico**.
2. Na folha **Regras de associação dinâmica** , insira a regra com a seguinte sintaxe:

    *Relatórios diretos para "{objectID_of_manager}"*

    Um exemplo de uma regra válida:
```
                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```
    where “62e19b97-8b3d-4d4a-a106-4ce66896a863” is the objectID of the manager. The object ID can be found on manager's **Profile tab**.
3. Depois de salvar a regra, todos os usuários com o valor da ID do Gerenciador especificada serão adicionados ao grupo.

## <a name="using-attributes-to-create-rules-for-device-objects"></a>Usando atributos para criar regras para objetos de dispositivo
Você também pode criar uma regra que seleciona objetos de dispositivo para associação em um grupo. Os seguintes atributos de dispositivo podem ser usados.

 Atributo do dispositivo  | Valores | Exemplo
 ----- | ----- | ----------------
 accountEnabled | verdadeiro, falso | (device.accountEnabled -eq true)
 displayName | Um valor de cadeia de caracteres. |(device.displayName -eq "Rob Iphone”)
 deviceOSType | Um valor de cadeia de caracteres. | (device.deviceOSType -eq "iPad") -or (device.deviceOSType -eq "iPhone")
 deviceOSVersion | Um valor de cadeia de caracteres. | (device.OSVersion -eq "9.1")
 deviceCategory | o nome de uma categoria de dispositivo válida | (device.deviceCategory -eq "BYOD")
 deviceManufacturer | Um valor de cadeia de caracteres. | (device.deviceManufacturer -eq "Samsung")
 deviceModel | Um valor de cadeia de caracteres. | (device.deviceModel -eq "iPad Air")
 deviceOwnership | Pessoal, Empresa, Desconhecido | (device.deviceOwnership -eq "Company")
 domainName | Um valor de cadeia de caracteres. | (device.domainName -eq "contoso.com")
 enrollmentProfileName | Nome do perfil de perfil de registro de dispositivo Apple ou Autopilot do Windows | (device.enrollmentProfileName -eq "DEP iPhones")
 isRooted | verdadeiro, falso | (device.isRooted -eq true)
 managementType | MDM (para dispositivos móveis)<br>PC (para computadores gerenciados pelo agente de PC do Intune) | (device.managementType -eq "MDM")
 organizationalUnit | qualquer valor de cadeia de caracteres correspondente ao nome da unidade organizacional definido por um Active Directory local | (device.organizationalUnit -eq "US PCs")
 deviceId | uma ID de dispositivo do Azure AD válida | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 ID do objeto | uma ID de objeto do Azure AD válida |  (device.objectId -eq 76ad43c9-32c5-45e8-a272-7b58b58f596d")



## <a name="changing-dynamic-membership-to-static-and-vice-versa"></a>Alterando a associação dinâmica para estática e vice-versa
É possível alterar como a associação é gerida em um grupo. Isso é útil quando você quer manter o mesmo ID e nome de grupo no sistema, então, todas as referências existentes no grupo ainda serão válidas; criar um novo grupo exigiria atualizar essas referências.

Atualizamos o Centro de administração do Azure AD para adicionar suporte a essa funcionalidade. Agora, os clientes podem converter os grupos existentes de associação dinâmica para associação atribuída, e vice-versa, por meio do Centro de Administração do Microsoft Azure AD ou de cmdlets do PowerShell, conforme mostrado abaixo.

> [!WARNING]
> Ao alterar um grupo estático existente para um grupo dinâmico, todos os membros existentes serão removidos do grupo e, em seguida, a regra de associação será processada para adicionar novos membros. Se o grupo for utilizado para controlar o acesso a aplicativos ou recursos, os membros originais poderão perder o acesso até que a regra de associação seja totalmente processada.
>
> É recomendável testar a nova regra de associação antes para garantir que a nova associação no grupo esteja conforme esperado.

### <a name="using-azure-ad-admin-center-to-change-membership-management-on-a-group"></a>Usar o Centro de Administração do Microsoft Azure AD para alterar o gerenciamento de associação em um grupo 

1. Entre no [Centro de Administração do Microsoft Azure AD](https://aad.portal.azure.com) com uma conta que seja um administrador global ou um administrador de conta de usuário no locatário.
2. Selecione **Grupos**.
3. Na lista **Todos os grupos**, abra o grupo que você deseja alterar.
4. Selecione **Propriedades**.
5. Na página **Propriedades**do grupo, selecione um **Tipo de Associação** entre Atribuído (estático), Usuário Dinâmico ou Dispositivo Dinâmico, dependendo do tipo de associação desejado. Para associação dinâmica, é possível usar o construtor de regras para selecionar opções para uma regra simples ou gravar uma regra avançada. 

As etapas a seguir são um exemplo de alteração de um grupo de associação estática para dinâmica para um grupo de usuários. 

1. Na página **Propriedades** do grupo selecionado, selecione um **Tipo de Associação** do **Usuário Dinâmico** e, em seguida, selecione Sim na caixa de diálogo explicando as alterações na associação do grupo para continuar. 
  
   ![selecionar tipo de associação do usuário dinâmico](./media/groups-dynamic-membership/select-group-to-convert.png)
  
2. Selecione **Adicionar consulta dinâmica** e, em seguida, forneça a regra.
  
   ![inserir a regra](./media/groups-dynamic-membership/enter-rule.png)
  
3. Depois de criar a regra, selecione **Adicionar consulta** na parte inferior da página.
4. Selecione **Salvar** na página **Propriedades** para o grupo salvar suas alterações. O **Tipo de Associação** do grupo é imediatamente atualizado na lista de grupos.

> [!TIP]
> A conversão de grupo poderá falhar se a regra avançada que você inseriu estiver incorreta. Uma notificação é exibida no canto superior direito do portal, explicando porque a regra não pode ser aceita pelo sistema. Leia com atenção para entender como você pode ajustar a regra para torná-la válida.

### <a name="using-powershell-to-change-membership-management-on-a-group"></a>Usar o PowerShell para alterar o gerenciamento de associação em um grupo

> [!NOTE]
> Para alterar as propriedades de grupo dinâmico, você precisará usar os cmdlets **da versão prévia da** [versão 2 do PowerShell do Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0). Você pode instalar a versão prévia da [Galeria do PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

Aqui está um exemplo de funções que alternam o gerenciamento de associação em um grupo existente. Neste exemplo, é preciso ter cuidado para manipular corretamente a propriedade GroupTypes e preservar quaisquer valores não relacionados à associação dinâmica.

```
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
Para tornar um grupo estático:
```
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```
Para tornar um grupo estático:
```
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```
## <a name="next-steps"></a>Próximas etapas
Esses artigos fornecem mais informações sobre grupos no Azure Active Directory.

* [Consultar grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Criar um novo grupo e adicionando membros](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Gerenciar configurações de um grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Gerenciar associações de um grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Gerenciar regras dinâmicas para usuários em um grupo](groups-dynamic-membership.md)
