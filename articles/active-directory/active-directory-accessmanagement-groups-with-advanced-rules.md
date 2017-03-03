---
title: "Usar atributos para criar regras avançadas | Microsoft Docs"
description: "Instruções para criar regras avançadas para um grupo, incluindo suporte para operadores de regra de expressões e parâmetros."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 04813a42-d40a-48d6-ae96-15b7e5025884
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 4bab9f44d1c91f05618ea510b83beb06540429f2
ms.openlocfilehash: 00424292fbc5321a77a4e924530ade97739208d4
ms.lasthandoff: 02/14/2017


---
# <a name="using-attributes-to-create-advanced-rules"></a>Usar atributos para criar regras avançadas
O portal clássico do Azure fornece a capacidade de criar regras avançadas para habilitar associações dinâmicas baseadas em atributos mais complexas de grupos do Azure Active Directory (Azure AD).  

Quando os atributos de um usuário são alterados, o sistema avalia todas as regras de grupo dinâmicas em um diretório para ver se a alteração do atributo do usuário dispararia adições ou remoções de grupo. Se um usuário atender a uma regra em um grupo, ele será adicionado como membro a esse grupo. Se ele não satisfizer mais à regra de um grupo do qual é membro, será removido do grupo.

> [!NOTE]
> Você pode configurar uma regra de associação dinâmica em grupos de segurança ou em grupos do Office 365. Atualmente não há suporte a associações de grupo aninhadas para atribuição com base em grupo para aplicativos.
>
> As associações dinâmicas de grupos exigem que uma licença do Azure AD Premium seja atribuída a
>
> * O administrador que gerencia a regra em um grupo
> * Todos os membros do grupo
>

## <a name="to-create-the-advanced-rule"></a>Para criar a regra avançada
1. No [portal clássico do Azure](https://manage.windowsazure.com), selecione **Active Directory**e abra o diretório da sua organização.
2. Selecione a guia **Grupos** e, em seguida, abra o grupo que deseja editar.
3. Selecione a guia **Configurar**, selecione a opção **Regra avançada** e, em seguida, digite a regra avançada na caixa de texto.

## <a name="constructing-the-body-of-an-advanced-rule"></a>Construção do corpo de uma regra avançada
A regra avançada que você pode criar para os membros dinâmicos para grupos é essencialmente uma expressão binária que consiste em três partes e resulta em um resultado verdadeiro ou falso. As três partes são:

* Parâmetro da esquerda
* Operador binário
* Constante à direita

Uma regra avançada completa é semelhante a esta: (leftParameter binaryOperator "RightConstant"), em que o parêntese de abertura e fechamento são necessários para toda a expressão binária, aspas duplas são necessárias para a constante à direita e a sintaxe do parâmetro esquerdo é user.property. Uma regra avançada pode consistir em mais de uma expressão binária separada pelos operadores lógicos -and, -or e -not.
A seguir é exemplos de uma regra avançada construída de maneira adequada:

* (user.department -eq "Sales") -or (user.department -eq "Marketing")
* (user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")

Para a lista completa de parâmetros com suporte e operadores de regra de expressão, consulte as seções a seguir.

Observe que a propriedade deve ser prefixada com o tipo de objeto correto: usuário ou dispositivo.
A regra a seguir falhará na validação: mail –ne null

A regra correta seria:

user.mail –ne null

O comprimento total do corpo da sua regra avançada não pode exceder 2048 caracteres.

> [!NOTE]
> Operações de cadeia de caracteres e regex diferenciam maiúsculas de minúsculas.
> As cadeias de caracteres que contém aspas " devem ser ignoradas usando caracteres ', por exemplo, user.department -eq \`"Sales".
> Use as aspas somente para valores do tipo string e use apenas as aspas inglesas.
>
>

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

## <a name="operator-precedence"></a>Precedência do operador

Todos os operadores são listados abaixo da menor precedência para a maior, os operadores na mesma linha têm a mesma precedência. -any -all -or -and -not -eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch

Todos os operadores podem ser usados com ou sem o prefixo de hífen.

Observe que os parênteses nem sempre serão necessários, você só precisará adicionar os parênteses quando a precedência não atender às suas necessidades. Por exemplo:

   user.department –eq "Marketing" –and user.country –eq "US"

é equivalente a:

   (user.department –eq "Marketing") –and (user.country –eq "US")

## <a name="query-error-remediation"></a>Correção do erro de consulta
A seguinte tabela relacionará os possíveis erros e como corrigi-los, se ocorrerem

| Erro de análise de consulta | Erros de uso | Uso corrigido |
| --- | --- | --- |
| Erro: O atributo não tem suportado. |(user.invalidProperty -eq "Valor") |(user.department -eq "value") A propriedade <br/>deve corresponder a uma na [lista de propriedades com suporte](#supported-properties). |
| Erro: Operador não é tem suportada no atributo. |(user.accountEnabled -contains true) |(user.accountEnabled -eq true) A propriedade <br/>é do tipo booliano. Use os operadores com suporte (-eq or -ne) em um tipo booleano da lista acima. |
| Erro: Erro de compilação de consulta. |(user.department -eq "Sales") -and (user.department -eq "Marketing")(user.userPrincipalName -match "*@domain.ext") |(user.department -eq "Sales") -and (user.department -eq "Marketing")<br/>O operador lógico deve corresponder a uma das propriedades com suporte listadas acima. (user.userPrincipalName -match ".*@domain.ext")or(user.userPrincipalName -match "@domain.ext$")Error na expressão regular. |
| Erro: Expressão binária não está no formato correto. |user.department – eq ("Vendas") user.department - eq ("Vendas") user.department-eq ("Vendas") |(user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>A consulta tem vários erros. Parênteses não no lugar certo. |
| Erro: Ocorreu um erro desconhecido durante a configuração de membros dinâmicos. |(user.accountEnabled -eq "True" AND user.userPrincipalName -contains "alias@domain") |(user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>A consulta tem vários erros. Parênteses não no lugar certo. |

## <a name="supported-properties"></a>Propriedades com suporte
Estas são todas as propriedades do usuário que você pode usar na regra avançada:

### <a name="properties-of-type-boolean"></a>Propriedades de tipo booleano
Operadores permitidos

* -eq
* -ne

| Propriedades | Valores permitidos | Uso |
| --- | --- | --- |
| accountEnabled |verdadeiro, falso |user.accountEnabled -eq true) |
| dirSyncEnabled |true false null |(user.dirSyncEnabled -eq true) |

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

| Propriedades | Valores permitidos | Uso |
| --- | --- | --- |
| city |Qualquer valor de cadeia de caracteres ou $null |(user.city -eq "valor") |
| country |Qualquer valor de cadeia de caracteres ou $null |(user.country -eq "valor") |
| department |Qualquer valor de cadeia de caracteres ou $null |(user.department -eq "value") A propriedade  |
| displayName |Um valor de cadeia de caracteres. |(user. DisplayName -eq "valor") |
| facsimileTelephoneNumber |Qualquer valor de cadeia de caracteres ou $null |user.facsimileTelephoneNumber -eq ("valor") |
| givenName |Qualquer valor de cadeia de caracteres ou $null |user.givenName -eq ("valor") |
| jobTitle |Qualquer valor de cadeia de caracteres ou $null |(user.jobTitle - eq "valor") |
| mail |Qualquer valor de cadeia de caracteres ou $null (endereço SMTP do usuário) |(user.mail - eq "valor") |
| mailNickName |Qualquer valor de cadeia de caracteres (alias de email do usuário) |(user.mailNickName - eq "valor") |
| Serviço Móvel |Qualquer valor de cadeia de caracteres ou $null |(user.mobile -eq "valor") |
| ID do objeto |GUID do objeto de usuário |(user.objectId - eq "1111111-1111-1111-1111-111111111111") |
| passwordPolicies |None DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |Qualquer valor de cadeia de caracteres ou $null |(user.physicalDeliveryOfficeName -eq "valor") |
| postalCode |Qualquer valor de cadeia de caracteres ou $null |(user.postalCode - eq "valor") |
| preferredLanguage |ISO 639-1 code |(user.preferredLanguage - eq "en-US") |
| sipProxyAddress |Qualquer valor de cadeia de caracteres ou $null |(user.sipProxyAddress -eq "valor") |
| state |Qualquer valor de cadeia de caracteres ou $null |(user.state -eq "valor") |
| streetAddress |Qualquer valor de cadeia de caracteres ou $null |(user.streetAddress -eq "valor") |
| sobrenome |Qualquer valor de cadeia de caracteres ou $null |(user.surname -eq "valor") |
| telephoneNumber |Qualquer valor de cadeia de caracteres ou $null |(user.telephoneNumber -eq "valor") |
| usageLocation |Código do país indicados dois |(user.usageLocation -eq "EUA") |
| userPrincipalName |Um valor de cadeia de caracteres. |(user.userPrincipalName -eq "alias@domain") |
| userType |member guest $null |(ser.userType -eq "Membro") |

### <a name="properties-of-type-string-collection"></a>Propriedades de coleção de cadeias de caracteres de tipo
Operadores permitidos

* -contains
* -notContains

| Properties | Valores permitidos | Uso |
| --- | --- | --- |
| otherMails |Um valor de cadeia de caracteres. |(user.otherMails -contains "alias@domain") |
| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses -contains "SMTP: alias@domain") |

## <a name="use-of-null-values"></a>Uso de valores nulos

Para especificar um valor nulo em uma regra, você pode usar "null" ou $null. Exemplo:

   user.mail –ne null é equivalente a user.mail –ne $null

## <a name="extension-attributes-and-custom-attributes"></a>Atributos de extensão e atributos personalizados
Os atributos de extensão e os atributos personalizados têm suporte das regras de associação dinâmica.

Os atributos de extensão são sincronizados a partir do AD do Windows Server local e têm o formato "ExtensionAttributeX", onde X é igual a 1 a 15.
Um exemplo de uma regra que usa um atributo de extensão:

(user.extensionAttribute15 -eq "Marketing")

Os Atributos Personalizados são sincronizados do Windows Server AD local ou de um aplicativo SaaS conectado e têm formato "user.extension[GUID]\__[Atributo]", onde [GUID] é o identificador exclusivo no AAD para o aplicativo que criou o atributo no AAD e [Atributo] é o nome do atributo como ele foi criado.
Um exemplo de uma regra que usa um atributo personalizado:

user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  

O nome do atributo personalizado pode ser encontrado no diretório por meio da consulta do atributo de um usuário, usando o Graph Explorer e procurando o nome do atributo.

## <a name="support-for-multi-value-properties"></a>Suporte para propriedades com vários valores

Para incluir uma propriedade com vários valores em uma regra, use o operador "-any", como em

  user.assignedPlans -any assignedPlan.service -startsWith "SCO"

## <a name="direct-reports-rule"></a>Regra de relatórios diretos
Você pode preencher os membros de um grupo com base no atributo gerenciador de um usuário.

**Para configurar um grupo como "Gerenciador"**

1. No portal clássico do Azure, clique em **Active Directory**e no nome do diretório de sua organização.
2. Selecione a guia **Grupos** e, em seguida, abra o grupo que deseja editar.
3. Selecione a guia **Configurar** e selecione **REGRA AVANÇADA**.
4. Digite a regra com a seguinte sintaxe:

    Relatórios diretos para *Relatórios diretos para {obectID_of_manager}*. Um exemplo de regra válida para Funcionários Subordinados:

                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863”

    onde “62e19b97-8b3d-4d4a-a106-4ce66896a863” é a objectID do gerente. A ID de objeto pode ser encontrada no Azure AD na **guia Perfil** da página de usuário para o usuário que for o gerente.
5. Ao salvar essa regra, todos os usuários que atendem à regra serão adicionados como membros do grupo. Pode levar alguns minutos para o preenchimento inicial do grupo.

## <a name="using-attributes-to-create-rules-for-device-objects"></a>Usando atributos para criar regras para objetos de dispositivo
Você também pode criar uma regra que seleciona objetos de dispositivo para associação em um grupo. Os seguintes atributos de dispositivo podem ser usados:

| Propriedades | Valores permitidos | Uso |
| --- | --- | --- |
| displayName |Um valor de cadeia de caracteres. |(device.displayName -eq "Rob Iphone”) |
| deviceOSType |Um valor de cadeia de caracteres. |(device.deviceOSType -eq "IOS") |
| deviceOSVersion |Um valor de cadeia de caracteres. |(device.OSVersion -eq "9.1") |
| isDirSynced |true false null |(device.isDirSynced -eq true) |
| isManaged |true false null |(device.isManaged -eq false) |
| isCompliant |true false null |(device.isCompliant -eq true) |
| deviceCategory |Um valor de cadeia de caracteres. |(device.deviceCategory -eq "") |
| deviceManufacturer |Um valor de cadeia de caracteres. |(device.deviceManufacturer -eq "Microsoft") |
| deviceModel |Um valor de cadeia de caracteres. |(device.deviceModel -eq "IPhone 7+") |
| deviceOwnership |Um valor de cadeia de caracteres. |(device.deviceOwnership -eq "") |
| domainName |Um valor de cadeia de caracteres. |(device.domainName -eq "contoso.com") |
| enrollmentProfileName |Um valor de cadeia de caracteres. |(device.enrollmentProfileName -eq "") |
| isRooted |true false null |(device.isRooted -eq true) |
| managementType |Um valor de cadeia de caracteres. |(device.managementType -eq "") |
| organizationalUnit |Um valor de cadeia de caracteres. |(device.organizationalUnit -eq "") |
| deviceId |um deviceId válido |(device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d" |

> [!NOTE]
> Essas regras de dispositivo não podem ser criadas usando o menu suspenso "regra simples" no portal clássico do Azure.
>
>

## <a name="next-steps"></a>Próximas etapas
Esses artigos fornecem mais informações sobre o Active Directory do Azure.

* [Solucionando problemas de associações dinâmicas a grupos](active-directory-accessmanagement-troubleshooting.md)
* [Gerenciamento de acesso a recursos com grupos do Active Directory do Azure](active-directory-manage-groups.md)
* [Cmdlets do Azure Active Directory para definir configurações de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

