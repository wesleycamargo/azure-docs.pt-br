---
title: Referência à API de auditoria do Azure Active Directory | Microsoft Docs
description: Como começar a usar a API de auditoria do Azure Active Directory
services: active-directory
documentationcenter: ''
author: dhanyahk
manager: femila
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/24/2016
ms.author: dhanyahk;markvi

---
# <a name="azure-active-directory-audit-api-reference"></a>Referência à API de auditoria do Azure Active Directory
Este tópico faz parte de uma coleção de tópicos sobre a API de relatório do Azure Active Directory.  
Os relatórios do Azure AD fornecem uma API que permite a você acessar dados de auditoria usando código ou ferramentas relacionadas.
O escopo deste tópico é fornecer informações de referência sobre a **API de auditoria**.

Consulte:

* [Logs de auditoria](active-directory-reporting-azure-portal.md#audit-logs) para obter mais informações conceituais
* [Introdução à API de relatório do Azure Active Directory](active-directory-reporting-api-getting-started.md) para saber mais sobre a API de relatório.

Para dúvidas, problemas ou comentários, entre em contato com a [Ajuda de relatório do AAD](mailto:aadreportinghelp@microsoft.com).

## <a name="who-can-access-the-data?"></a>Quem pode acessar os dados?
* Usuários na função de Administrador de segurança ou Leitor de segurança
* Administradores globais
* Qualquer aplicativo que tenha autorização para acessar a API (a autorização do aplicativo pode ser definida somente com base na permissão de Administrador Global)

## <a name="prerequisites"></a>Pré-requisitos
Para acessar esse relatório por meio da API de relatórios, você deve ter:

* Um [Azure Active Directory gratuito ou uma edição melhor](active-directory-editions.md)
* Concluído os [pré-requisitos para acessar a API de relatório do Azure AD](active-directory-reporting-api-prerequisites.md). 

## <a name="accessing-the-api"></a>Como acessar a API
Você pode acessar essa API por meio de [Graph Explorer](https://graphexplorer2.cloudapp.net) ou usando programaticamente, por exemplo, o PowerShell. Para o PowerShell interpretar corretamente a sintaxe de filtro OData usada nas chamadas REST Graph do AAD, você deve usar o caractere de acento grave para "escapar" o caractere $. O caractere de acento grave serve como [caractere de escape do PowerShell](https://technet.microsoft.com/library/hh847755.aspx), permitindo que o PowerShell faça uma interpretação literal do caractere $ e evite confundi-lo como um nome de variável do PowerShell (ou seja: $filter).

O foco deste tópico é no Graph Explorer. Para obter um exemplo do PowerShell, consulte [scripts do PowerShell](active-directory-reporting-api-audit-samples.md#powershell-script).

## <a name="api-endpoint"></a>Ponto de extremidade de API
Você pode acessar essa API usando o seguinte URI:  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta

Não há qualquer limite para o número de registros retornados pela API de auditoria do Azure AD (usando a paginação de OData).
Para conhecer os limites de retenção em dados de relatório, confira [Políticas de retenção de relatório](active-directory-reporting-retention.md).

Essa chamada retorna os dados em lotes. Cada lote tem no máximo 1000 registros.  
Para obter o próximo lote de registros, use o link Próximo. Obtenha as informações de skiptoken do primeiro conjunto de registros retornados. O token skip estará no final do conjunto de resultados.  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta&%24skiptoken=-1339686058




## <a name="supported-filters"></a>Filtros com suporte
Você pode reduzir o número de registros retornados por uma chamada de API na forma de um filtro.  
Para entrar dados de entrada relacionados à API, os filtros a seguir recebem suporte:

* **$top=\<número de registros a serem retornados\>** – para limitar o número de registros retornados. Esta é uma operação cara. Não use esse filtro se você quiser retornar milhares de objetos.     
* **$filter=\<sua instrução de filtro\>** – para especificar, com base nos campos de filtro com suporte, o tipo de registro com o qual você se preocupa

## <a name="supported-filter-fields-and-operators"></a>Campos de filtro e operadores com suporte
Para especificar o tipo de registro com o qual você se preocupa, compile uma instrução de filtro que possa conter um ou uma combinação dos campos de filtro a seguir:

* [activityDate](#activitydate) – define uma data ou intervalo de datas
* [activityType](#activitytype) – define o tipo de uma atividade
* [activity](#activity) – Define a atividade como uma cadeia de caracteres  
* [actor/name](#actorname) – define o ator na forma de nome do ator
* [actor/objectid](#actorobjectid) – Define o ator na forma da ID do ator   
* [ator/upn](#actorupn) – define o ator na forma de nome UPN do ator 
* [target/name](#targetname) – define o destino na forma de nome do ator
* [target/objectid](#targetobjectid) – Define o destino na forma de ID do destino  
* [target/upn](#targetupn) – Define o ator na forma de UPN (nome principal de usuário) do ator   

- - -
### <a name="activitydate"></a>activityDate
**Operadores com suporte**: eq, ge, le, gt, lt

**Exemplo**:

    $filter=tdomain + 'activities/audit?api-version=beta&`$filter=eventTime gt ' + $7daysago    

**Observações**:

datetime deve estar no formato UTC

- - -
### <a name="activitytype"></a>activityType
**Operadores com suporte**: eq

**Exemplo**:

    $filter=activityType eq 'User'  

**Observações**:

diferencia maiúsculas de minúsculas

- - -
### <a name="activity"></a>activity
**Suporte para operadores**: eq, contains, startsWith

**Exemplo**:

    $filter=activity eq 'Add application' or contains(activity, 'Application') or startsWith(activity, 'Add')   

**Observações**:

diferencia maiúsculas de minúsculas

- - -
### <a name="actor/name"></a>actor/name
**Suporte para operadores**: eq, contains, startsWith

**Exemplo**:

    $filter=actor/name eq 'test' or contains(actor/name, 'test') or startswith(actor/name, 'test')  

**Observações**:

não diferenciam maiúsculas de minúsculas

- - -
### <a name="actor/objectid"></a>actor/objectid
**Operadores com suporte**: eq

**Exemplo**:

    $filter=actor/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba'    

- - -
### <a name="target/name"></a>target/name
**Suporte para operadores**: eq, contains, startsWith

**Exemplo**:

    $filter=targets/any(t: t/name eq 'some name')   

**Observações**:

não diferenciam maiúsculas de minúsculas

- - -
### <a name="target/upn"></a>target/upn
**Suporte para operadores**: eq, startsWith

**Exemplo**:

    $filter=targets/any(t: startswith(t/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity/userPrincipalName,'abc')) 

**Observações**:

* Não diferenciam maiúsculas de minúsculas
* Você precisa adicionar o namespace completo ao consultar Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity

- - -
### <a name="target/objectid"></a>target/objectid
**Operadores com suporte**: eq

**Exemplo**:

    $filter=targets/any(t: t/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba')    

- - -
### <a name="actor/upn"></a>ator/upn
**Suporte para operadores**: eq, startsWith

**Exemplo**:

    $filter=startswith(actor/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity/userPrincipalName,'abc')  

**Observações**:

* não diferenciam maiúsculas de minúsculas 
* Você precisa adicionar o namespace completo ao consultar Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity

- - -
## <a name="next-steps"></a>Próximas etapas
* Quer ver exemplos para atividades do sistema filtradas? Confira os [exemplos de API de auditoria do Azure Active Directory](active-directory-reporting-api-audit-samples.md).
* Quer saber mais sobre a API de relatório do Azure AD? Confira [Introdução à API de relatório do Azure Active Directory](active-directory-reporting-api-getting-started.md).

<!--HONumber=Oct16_HO2-->


