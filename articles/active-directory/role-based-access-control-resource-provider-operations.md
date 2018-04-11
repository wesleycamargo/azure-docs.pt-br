---
title: Operações do provedor do Azure Resource Manager | Microsoft Docs
description: Fornece detalhes sobre as operações disponíveis nos provedores de recursos do Microsoft Azure Resource Manager
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/06/2018
ms.author: rolyon
ms.openlocfilehash: 0b8c8823c6d21df96dcfd926db1855169f1570e4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Operações do provedor de recursos do Azure Resource Manager

Este documento lista as operações disponíveis para cada provedor de recursos do Microsoft Azure Resource Manager. Elas podem ser usadas em funções personalizadas para fornecer permissões de RBAC (Controle de acesso baseado em função) completas para recursos no Azure. Observe que esta não é uma lista abrangente e outras operações podem ser adicionadas ou removidas conforme a atualização de cada provedor. Cadeias de caracteres de operação seguem o formato de `Microsoft.<ProviderName>/<ChildResourceType>/<action>`. 

> [!NOTE]
> Para obter uma lista abrangente e atual, use `Get-AzureRmProviderOperation` (no PowerShell) ou `az provider operation list` (na CLI v2 do Azure) para listar as operações dos provedores de recursos do Azure.

## <a name="microsoftaad"></a>Microsoft.AAD

| Operação | DESCRIÇÃO |
|---|---|
|/domainServices/delete|Exclui Serviços de Domínio.|
|/domainServices/read|Lê Serviços de Domínio.|
|/domainServices/write|Grava Serviços de Domínio|
|/locations/operationresults/read|Ler o status de uma operação assíncrona.|
|/Operations/read|A descrição amigável localizada para a operação, como deve ser mostrada ao usuário.|

## <a name="microsoftaadiam"></a>microsoft.aadiam

| Operação | DESCRIÇÃO |
|---|---|
|/diagnosticsettings/delete|Exclusão de uma configuração de diagnóstico|
|/diagnosticsettings/read|Leitura de uma configuração de diagnóstico|
|/diagnosticsettings/write|Gravação de uma configuração de diagnóstico|
|/diagnosticsettingscategories/read|Leitura de uma categoria de configuração de diagnóstico|
|/tenants/providers/Microsoft.Insights/diagnosticSettings/read|Obter a configuração de diagnóstico para o recurso|
|/tenants/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o recurso|
|/tenants/providers/Microsoft.Insights/logDefinitions/read|Obter os logs disponíveis para locatários|

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

| Operação | DESCRIÇÃO |
|---|---|
|/configuration/action|Atualizar a configuração do locatário.|
|/configuration/read|Ler a configuração de locatário.|
|/configuration/write|Criar uma configuração de locatário.|
|/services/action|Atualizar uma instância de serviço no locatário.|
|/services/alerts/read|Ler os alertas de um serviço.|
|/services/alerts/read|Ler os alertas de um serviço.|
|/services/delete|Excluir uma instância de serviço no locatário.|
|/services/read|Ler as instâncias de serviço no locatário.|
|/services/servicemembers/action|Criar uma instância de membro de serviço no serviço.|
|/services/servicemembers/alerts/read|Ler os alertas de um membro de serviço.|
|/services/servicemembers/delete|Excluir uma instância de membro de serviço no serviço.|
|/services/servicemembers/read|Ler a instância do membro de serviço no serviço.|
|/services/write|Criar uma instância de serviço no locatário.|

## <a name="microsoftadvisor"></a>Microsoft.Advisor

| Operação | DESCRIÇÃO |
|---|---|
|/configurations/read|Obter configurações|
|/configurations/write|Criar/atualizar configuração|
|/generateRecommendations/action|Gera recomendações|
|/generateRecommendations/read|Obter o status de gerar recomendações|
|/operations/read|Obter as operações para Assistente do Azure|
|/recommendations/read|Ler recomendações|
|/recommendations/suppressions/delete|Excluir supressão|
|/recommendations/suppressions/read|Obter supressões|
|/recommendations/suppressions/write|Criar/atualizar supressões|
|/register/action|Registra a assinatura do Microsoft Advisor|
|/suppressions/delete|Excluir supressão|
|/suppressions/read|Obter supressões|
|/suppressions/write|Criar/atualizar supressões|
|/unregister/action|Cancelar o registro da assinatura do Assistente do Azure|

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

| Operação | DESCRIÇÃO |
|---|---|
|/alerts/read|Obter todos os alertas para os filtros de entrada.|
|/alerts/resolve/action|Alterar o estado do alerta para 'Resolve'|
|/alertsSummary/read|Obter o resumo dos alertas|
|/Operations/read|Ler as operações fornecidas|

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

| Operação | DESCRIÇÃO |
|---|---|
|/locations/checkNameAvailability/action|Verificar se determinado nome do Analysis Server é válido e não está em uso.|
|/locations/operationresults/read|Recuperar as informações do resultado da operação especificada.|
|/locations/operationstatuses/read|Recuperar as informações do status da operação especificada.|
|/operations/read|Recuperar as informações das operações|
|/register/action|Registrar o provedor de recursos do Analysis Services.|
|/servers/delete|Excluir o Analysis Server.|
|/servers/listGatewayStatus/action|Listar o status do gateway associado ao servidor.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|Obter a configuração de diagnóstico para o Analysis Server|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico do Analysis Server|
|/servers/providers/Microsoft.Insights/logDefinitions/read|Obter os logs disponíveis para servidores|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Obter as métricas disponíveis para o Analysis Server|
|/servers/read|Recupera as informações do Analysis Server especificado.|
|/servers/resume/action|Retoma o Analysis Server.|
|/servers/skus/read|Recuperar informações de SKU disponíveis para o servidor|
|/servers/suspend/action|Suspende o Analysis Server.|
|/servers/write|Criar ou atualizar o Analysis Server especificado.|
|/skus/read|Recuperar as informações de SKUs|

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

| Operação | DESCRIÇÃO |
|---|---|
|/checkNameAvailability/read|Verificar se o nome do serviço fornecido está disponível|
|/operations/read|Ler todas as operações de API disponíveis para o recurso Microsoft.ApiManagement|
|/register/action|Registrar a assinatura para o provedor de recursos Microsoft.ApiManagement|
|/reports/read|Obter relatórios agregados por períodos de tempo, região geográfica, desenvolvedores, produtos, APIs, operações, assinatura e byRequest.|
|/service/apis/delete|Remover a API existente|
|/service/apis/diagnostics/delete|Remover o diagnóstico existente|
|/service/apis/diagnostics/loggers/delete|Remover o mapeamento de um agente com uma configuração de diagnóstico|
|/service/apis/diagnostics/loggers/read|Obter lista de agentes de diagnósticos existentes|
|/service/apis/diagnostics/loggers/write|Mapear assistente para uma configuração de diagnóstico|
|/service/apis/diagnostics/read|Obter lista de diagnósticos ou Obter detalhes do diagnóstico|
|/service/apis/diagnostics/write|Adicionar novo diagnóstico ou Atualizar detalhes de diagnóstico existente|
|/service/apis/operations/delete|Remover a operação da API existente|
|/service/apis/operations/policies/delete|Remover a configuração de política das políticas de Operação de API|
|/service/apis/operations/policies/read|Obter políticas para Operação de API ou obter detalhes de configuração de política para Operação de API|
|/service/apis/operations/policies/write|Definir detalhes de configuração de política para Operação de API|
|/service/apis/operations/policy/delete|Remover configuração de política da operação|
|/service/apis/operations/policy/read|Obter detalhes de configuração de política para a operação|
|/service/apis/operations/policy/write|Definir detalhes de configuração de política para a operação|
|/service/apis/operations/read|Obter lista de operações de API existentes ou obter os detalhes de operação da API|
|/service/apis/operations/tags/delete|Excluir associação de Marca existente com a Operação existente|
|/service/apis/operations/tags/read|Obter marcas associadas à operação ou Obter detalhes da Marca|
|/service/apis/operations/tags/write|Associar Marca existente a uma Operação existente|
|/service/apis/operations/write|Criar nova operação de API ou atualizar operação de API existente|
|/service/apis/operationsByTags/read|Obter lista de associações de Marca/Operação|
|/service/apis/policies/delete|Remover a configuração de política das políticas da API|
|/service/apis/policies/read|Obter políticas para API ou Obter detalhes de configuração de política para API|
|/service/apis/policies/write|Definir detalhes de configuração de política para API|
|/service/apis/policy/delete|Remover configuração de política da API|
|/service/apis/policy/read|Obter detalhes de configuração de política para API|
|/service/apis/policy/write|Definir detalhes de configuração de política para API|
|/service/apis/products/read|Obter todos os produtos que a API faz parte|
|/service/apis/read|Obter lista de todas as APIs registradas ou obter detalhes da API|
|/service/apis/releases/delete|Remover todas as versões da API ou versão Remover API|
|/service/apis/releases/read|Obter versões para uma API ou Obter detalhes da versão da API|
|/service/apis/releases/write|Criar nova versão da API ou atualizar a versão da API existente|
|/service/apis/revisions/delete|Remover todas as revisões de uma API|
|/service/apis/revisions/read|Obter revisões pertencentes a uma API|
|/service/apis/schemas/delete|Remover o Esquema existente|
|/service/apis/schemas/document/read|Obter o documento que descreve o Esquema|
|/service/apis/schemas/document/write|Atualizar o documento que descreve o Esquema|
|/service/apis/schemas/read|Obter todos os esquemas para uma determinada API ou Obter os esquemas usados pela API|
|/service/apis/schemas/write|Definir os Esquemas usados pela API|
|/service/apis/tagDescriptions/delete|Remover descrição de Marca da API|
|/service/apis/tagDescriptions/read|Obter descrições de Marcas no escopo da API ou obter uma descrição de Marcas no escopo da API|
|/service/apis/tagDescriptions/write|Criar/alterar a descrição da Marca no escopo da API|
|/service/apis/tags/delete|Remover associação existente de API/Marca|
|/service/apis/tags/read|Obter todas as associações de Marca/API para a API ou Obter detalhes da associação de Marca/API|
|/service/apis/tags/write|Adicionar nova associação de Marca/API|
|/service/apis/write|Criar nova API ou atualizar os detalhes da API existente|
|/service/apisByTags/read|Obter lista de associações de Marca/API|
|/service/api-version-sets/delete|Remover VersionSet existente|
|/service/api-version-sets/read|Obter lista de entidades do grupo de versão ou Obter detalhes de um VersionSet|
|/service/api-version-sets/versions/read|Obter lista de entidades de versão|
|/service/api-version-sets/write|Criar novo VersionSet ou Atualizar detalhes do VersionSet existente|
|/service/applynetworkconfigurationupdates/action|Atualiza os recursos Microsoft.ApiManagement em execução na rede virtual para obter configurações de rede atualizadas.|
|/service/authorizationServers/delete|Remover servidor de autorização existente|
|/service/authorizationServers/read|Obter lista de servidores de autorização ou obter detalhes do servidor de autorização|
|/service/authorizationServers/write|Criar um novo servidor de autorização ou atualizar detalhes de um servidor de autorização existente|
|/service/backends/delete|Remover o back-end existente|
|/service/backends/read|Obter lista de back-ends ou obter os detalhes de back-end|
|/service/backends/reconnect/action|Criar uma Solicitação de Reconexão|
|/service/backends/write|Adicionar um novo back-end ou atualizar os detalhes do back-end existente|
|/service/backup/action|Fazer backup do Serviço de Gerenciamento de API para o contêiner especificado em uma conta de armazenamento fornecida pelo usuário|
|/service/certificates/delete|Remover certificado existente|
|/service/certificates/read|Obter lista de certificados ou obter os detalhes do certificado|
|/service/certificates/write|Adicionar novo certificado|
|/service/delete|Excluir uma instância do Serviço de Gerenciamento de API|
|/service/diagnostics/delete|Remover o diagnóstico existente|
|/service/diagnostics/loggers/delete|Remover o mapeamento de um agente com uma configuração de diagnóstico|
|/service/diagnostics/loggers/read|Obter lista de agentes de diagnósticos existentes|
|/service/diagnostics/loggers/write|Mapear assistente para uma configuração de diagnóstico|
|/service/diagnostics/read|Obter lista de diagnósticos ou Obter detalhes do diagnóstico|
|/service/diagnostics/write|Adicionar novo diagnóstico ou Atualizar detalhes de diagnóstico existente|
|/service/getssotoken/action|Obter token de SSO que pode ser usado para fazer logon no portal de herdado do Serviço de Gerenciamento de API como administrador|
|/service/groups/delete|Remover grupo existente|
|/service/groups/read|Obter lista de grupos ou obter detalhes de um grupo|
|/service/groups/users/delete|Remover usuário existente do grupo existente|
|/service/groups/users/read|Obter lista de usuários do grupo|
|/service/groups/users/write|Adicionar usuário existente a grupo existente|
|/service/groups/write|Criar novo grupo ou atualizar detalhes do grupo existente|
|/service/identityProviders/delete|Remover o provedor de identidade existente|
|/service/identityProviders/read|Obter lista de provedores de identidade ou obter detalhes do provedor de identidade|
|/service/identityProviders/write|Criar um novo provedor de identidade atualizar detalhes de um provedor de identidade existente|
|/service/locations/networkstatus/read|Obter o status de acesso à rede dos recursos em que o serviço depende da localização.|
|/service/loggers/delete|Remover agente existente|
|/service/loggers/read|Obter lista de agentes ou obter detalhes do agente|
|/service/loggers/write|Adicionar novo agente ou atualizar detalhes do agente existente|
|/service/managedeployments/action|Alterar SKU/unidades, adicionar/remover implantações regionais do Serviço de Gerenciamento de API|
|/service/networkstatus/read|Obter o status de acesso à rede dos recursos dos quais o serviço depende.|
|/service/notifications/action|Envia uma notificação para um usuário especificado|
|/service/notifications/read|Obter todas as notificações do publicador de Gerenciamento de API ou Obter detalhes da notificação do publicador de Gerenciamento de API|
|/service/notifications/recipientEmails/delete|Remover emails existentes associados a uma Notificação|
|/service/notifications/recipientEmails/read|Receber destinatários de email associados à notificação do publicador de Gerenciamento de API|
|/service/notifications/recipientEmails/write|Criar novo destinatário de email da notificação|
|/service/notifications/recipientUsers/delete|Removes usuário associado aos destinatários da notificação|
|/service/notifications/recipientUsers/read|Obter usuários de destinatários associados à notificação|
|/service/notifications/recipientUsers/write|Adicionar usuário aos destinatários da notificação|
|/service/notifications/write|Criar ou atualizar a notificação do publicador de Gerenciamento de API|
|/service/openidConnectProviders/delete|Remover o provedor OpenID Connect existente|
|/service/openidConnectProviders/read|Obter lista de provedores OpenID Connect ou obter detalhes do provedor OpenID Connect|
|/service/openidConnectProviders/write|Criar um novo provedor OpenID Connect ou atualizar detalhes de um provedor OpenID Connect existente|
|/service/operationresults/read|Obter o status atual da operação de longa duração|
|/service/policies/delete|Remover a configuração de política das políticas do Locatário|
|/service/policies/read|Obter políticas para locatário ou Obter detalhes de configuração de política para Locatário|
|/service/policies/write|Definir detalhes de configuração de política para Locatário|
|/service/policySnippets/read|Obter todos os trechos de política|
|/service/portalsettings/read|Obter as configurações de inscrição para o Portal, Obter as configurações de entrada para o Portal ou Obter as configurações de delegação para o Portal|
|/service/portalsettings/write|Atualizar configurações de inscrição, Atualizar configurações de entrada ou Atualizar configurações de delegação|
|/service/products/apis/delete|Remover API existente do produto existente|
|/service/products/apis/read|Obter lista de APIs adicionadas ao produto existente|
|/service/products/apis/write|Adicionar API existente a um produto existente|
|/service/products/delete|Remover produto existente|
|/service/products/groups/delete|Excluir a associação de grupo do desenvolvedor existente ao produto existente|
|/service/products/groups/read|Obter lista de grupos de desenvolvedores associados ao produto|
|/service/products/groups/write|Associar o grupo do desenvolvedor existente ao produto existente|
|/service/products/policies/delete|Remover a configuração de política das políticas de Produtos|
|/service/products/policies/read|Obter políticas para Produtos ou Obter detalhes de configuração de políticas para o Produto|
|/service/products/policies/write|Definir detalhes de configuração de política para o Produto|
|/service/products/policy/delete|Remover configuração de política de produto existente|
|/service/products/policy/read|Obter a configuração de política do produto existente|
|/service/products/policy/write|Definir a configuração de política para o produto existente|
|/service/products/read|Obter lista de produtos ou obter detalhes do produto|
|/service/products/subscriptions/read|Obter lista de assinaturas de produto|
|/service/products/tags/delete|Excluir associação de marca existente com o produto existente|
|/service/products/tags/read|Obter marcas associadas ao Produto ou Obter detalhes da Marca|
|/service/products/tags/write|Associar Marca existente a um Produto existente|
|/service/products/write|Criar novo produto ou atualizar detalhes do produto existente|
|/service/properties/delete|Remover uma propriedade existente|
|/service/properties/read|Obter a lista de todas as propriedades ou obtém detalhes de uma propriedade especificada|
|/service/properties/write|Criar uma nova propriedade ou atualizar o valor da propriedade especificada|
|/service/providers/Microsoft.Insights/diagnosticSettings/read|Obter a configuração de diagnóstico para o serviço de Gerenciamento de API|
|/service/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o serviço de Gerenciamento de API|
|/service/providers/Microsoft.Insights/logDefinitions/read|Obter os logs disponíveis para o serviço de Gerenciamento de API|
|/service/providers/Microsoft.Insights/metricDefinitions/read|Obter as métricas disponíveis para o serviço de Gerenciamento de API|
|/service/quotas/periods/read|Obter o valor do contador de cota para o período|
|/service/quotas/periods/write|Definir o valor atual do contador de cota|
|/service/quotas/read|Obter valores para cota|
|/service/quotas/write|Definir o valor atual do contador de cota|
|/service/read|Ler metadados de uma instância do Serviço de Gerenciamento de API|
|/service/reports/read|Obter relatório agregado por períodos de tempo, Obter relatório agregado por região geográfica ou Obter relatório agregado pelos desenvolvedores. ou Obter relatório agregado por produtos. ou Obter relatório agregado por APIs, Obter relatório agregado por operações ou Obter relatório agregado por assinatura. ou Obter dados de relatórios de solicitações|
|/service/restore/action|Restaurar o Serviço de Gerenciamento de API do contêiner especificado em uma conta de armazenamento fornecida pelo usuário|
|/service/subscriptions/delete|Excluir assinatura. Essa operação pode ser usada para excluir a assinatura|
|/service/subscriptions/read|Obter uma lista de assinaturas de produtos ou obter os detalhes de assinatura do produto|
|/service/subscriptions/regeneratePrimaryKey/action|Regenerar a chave primária da assinatura|
|/service/subscriptions/regenerateSecondaryKey/action|Regenerar a chave secundária da assinatura|
|/service/subscriptions/write|Inscrever um usuário existente para um produto existente ou atualizar os detalhes de uma assinatura existente. Essa operação pode ser usada para renovar a assinatura|
|/service/tagResources/read|Obter lista de Marcas com Recursos associados|
|/service/tags/delete|Remover Marca existente|
|/service/tags/read|Obter lista de Marcas ou Obter detalhes da Marca|
|/service/tags/write|Adicionar nova Marca ou Atualizar detalhes da Marca existente|
|/service/templates/delete|Redefinir o modelo padrão de email de Gerenciamento de API|
|/service/templates/read|Obter todos os modelos de email ou Obter detalhes do modelo de email do Gerenciamento de API|
|/service/templates/write|Criar ou atualizar o modelo de email de Gerenciamento de API ou o modelo de email de Gerenciamento de APIs de atualização|
|/service/tenant/delete|Remover configuração de política do locatário|
|/service/tenant/deploy/action|Executar uma tarefa de implantação para aplicar as alterações da ramificação git especificada para a configuração no banco de dados.|
|/service/tenant/operationResults/read|Obter lista de resultados da operação ou obter o resultado de uma operação específica|
|/service/tenant/read|Obter a configuração de política para o locatário ou Obter detalhes de informações de acesso do locatário|
|/service/tenant/regeneratePrimaryKey/action|Regenerar a chave de acesso primária|
|/service/tenant/regenerateSecondaryKey/action|Regenerar a chave de acesso secundária|
|/service/tenant/save/action|Criar confirmação com instantâneo de configuração para uma ramificação específica no repositório|
|/service/tenant/syncState/read|Obter status da última sincronização git|
|/service/tenant/validate/action|Valida as alterações da ramificação git especificada|
|/service/tenant/write|Definir a configuração de política para o locatário ou Atualizar detalhes de informações de acesso do locatário|
|/service/updatecertificate/action|Carregar certificado SSL para um Serviço de Gerenciamento de API|
|/service/updatehostname/action|Configurar, atualizar ou remover nomes de domínio personalizado para um Serviço de Gerenciamento de API|
|/service/users/action|Registrar um novo usuário|
|/service/users/applications/attachments/delete|Remover um anexo|
|/service/users/applications/attachments/read|Obter anexos de aplicativo ou Obter anexo|
|/service/users/applications/attachments/write|Adicionar Anexo ao aplicativo|
|/service/users/applications/delete|Remover aplicativo existente|
|/service/users/applications/read|Obter lista de todos os aplicativos do usuário ou Obter detalhes do aplicativo de Gerenciamento de API|
|/service/users/applications/write|Registrar um aplicativo para Gerenciamento de API ou Atualizar detalhes do aplicativo|
|/service/users/delete|Remover conta de usuário|
|/service/users/generateSsoUrl/action|Gerar a URL do SSO. A URL pode ser usada para acessar o portal de administração|
|/service/users/groups/read|Obter lista de grupos do usuário|
|/service/users/keys/read|Obter lista de chaves do usuário|
|/service/users/read|Obter uma lista de usuários registrados ou obter detalhes da conta de um usuário|
|/service/users/subscriptions/read|Obter lista de assinaturas do usuário|
|/service/users/token/action|Obter token de acesso do token para um usuário|
|/service/users/write|Registrar um novo usuário ou atualizar detalhes da conta de um usuário existente|
|/service/write|Criar uma nova instância do Serviço de Gerenciamento de API|
|/unregister/action|Cancelar o registro da assinatura para o provedor de recursos Microsoft.ApiManagement|

## <a name="microsoftauthorization"></a>Microsoft.Authorization

| Operação | DESCRIÇÃO |
|---|---|
|/checkAccess/action|Verificar se o chamador está autorizado a executar uma ação específica|
|/classicAdministrators/delete|Remover o administrador da assinatura.|
|/classicAdministrators/read|Ler os administradores para obter a assinatura.|
|/classicAdministrators/write|Adicionar ou modificar o administrador de uma assinatura.|
|/elevateAccess/action|Concede ao chamador acesso de administrador de acesso do usuário no escopo do locatário|
|/locks/delete|Excluir bloqueios no escopo especificado.|
|/locks/read|Obter bloqueios no escopo especificado.|
|bloqueios/gravação|Adicionar bloqueios no escopo especificado.|
|/permissions/read|Listar todas as permissões que o chamador tem em determinado escopo.|
|/policyAssignments/delete|Excluir uma atribuição de política no escopo especificado.|
|/policyAssignments/read|Obter informações sobre uma atribuição de política.|
|/policyAssignments/write|Criar uma atribuição de política no escopo especificado.|
|/policyDefinitions/delete|Excluir uma definição de política.|
|/policyDefinitions/read|Obter informações sobre uma definição de política.|
|/policyDefinitions/write|Criar uma definição de política personalizada.|
|/policySetDefinitions/delete|Excluir uma definição de conjunto de políticas.|
|/policySetDefinitions/read|Obter informações sobre uma definição de conjunto de políticas.|
|/policySetDefinitions/write|Criar uma definição de conjunto de políticas personalizada.|
|/providerOperations/read|Obter operações para todos os provedores de recursos que podem ser usados em definições de função.|
|/roleAssignments/delete|Exclua uma atribuição de função no escopo especificado.|
|/roleAssignments/read|Obter informações sobre uma atribuição de função.|
|/roleAssignments/write|Criar uma atribuição de função no escopo especificado.|
|/roleDefinitions/delete|Excluir a definição de função personalizada especificada.|
|/roleDefinitions/read|Obter informações sobre uma definição de função.|
|/roleDefinitions/write|Criar ou atualizar uma definição de função personalizada com permissões especificadas e escopos atribuíveis.|

## <a name="microsoftautomation"></a>Microsoft.Automation

| Operação | DESCRIÇÃO |
|---|---|
|/automationAccounts/agentRegistrationInformation/read|Ler uma informação de registro do DSC de Automação do Azure|
|/automationAccounts/agentRegistrationInformation/regenerateKey/action|Gravar uma solicitação para regenerar chaves DSC de Automação do Azure|
|/automationAccounts/certificates/delete|Excluir um ativo de certificado da Automação do Azure|
|/automationAccounts/certificates/read|Obter um ativo de certificado da Automação do Azure|
|/automationAccounts/certificates/write|Criar ou atualizar um ativo de certificado da Automação do Azure|
|/automationAccounts/compilationjobs/read|Ler uma compilação do DSC de Automação do Azure|
|/automationAccounts/compilationjobs/write|Gravar uma compilação do DSC de Automação do Azure|
|/automationAccounts/configurations/delete|Excluir um conteúdo de DSC de Automação do Azure|
|/automationAccounts/configurations/getCount/action|Ler a contagem de um conteúdo do DSC de Automação do Azure|
|/automationAccounts/configurations/read|Obter o conteúdo de DSC de Automação do Azure|
|/automationAccounts/configurations/write|Gravar um conteúdo de DSC de Automação do Azure|
|/automationAccounts/connections/delete|Excluir um ativo de conexão da Automação do Azure|
|/automationAccounts/connections/read|Obter um ativo de conexão da Automação do Azure|
|/automationAccounts/connections/write|Criar ou atualizar um ativo de conexão da Automação do Azure|
|/automationAccounts/connectionTypes/delete|Excluir um ativo de tipo de conexão da Automação do Azure|
|/automationAccounts/connectionTypes/read|Obter um ativo de tipo de conexão da Automação do Azure|
|/automationAccounts/connectionTypes/Write|Criar um ativo de tipo de conexão da Automação do Azure|
|/automationAccounts/credentials/delete|Excluir um ativo de credencial da Automação do Azure|
|/automationAccounts/credentials/read|Obter um ativo de credencial da Automação do Azure|
|/automationAccounts/credentials/write|Criar ou atualizar um ativo de credencial da Automação do Azure|
|/automationAccounts/delete|Excluir uma conta da Automação do Azure|
|/automationAccounts/diagnosticSettings/read|Obter a configuração de diagnóstico para o recurso|
|/automationAccounts/diagnosticSettings/write|Definir a configuração de diagnóstico para o recurso|
|/automationAccounts/hybridRunbookWorkerGroups/delete|Excluir recursos do Hybrid Runbook Worker|
|/automationAccounts/hybridRunbookWorkerGroups/read|Ler recursos do Hybrid Runbook Worker|
|/automationAccounts/jobs/output/action|Obter a saída de um trabalho|
|/automationAccounts/jobs/output/action|Obter a saída de um trabalho|
|/automationAccounts/jobs/read|Obter um trabalho da Automação do Azure|
|/automationAccounts/jobs/read|Obter um trabalho da Automação do Azure|
|/automationAccounts/jobs/resume/action|Reinicia um trabalho da Automação do Azure|
|/automationAccounts/jobs/resume/action|Reinicia um trabalho da Automação do Azure|
|/automationAccounts/jobs/runbookContent/action|Obter o conteúdo do runbook da Automação do Azure no momento da execução do trabalho|
|/automationAccounts/jobs/runbookContent/action|Obter o conteúdo do runbook da Automação do Azure no momento da execução do trabalho|
|/automationAccounts/jobs/stop/action|Interrompe um trabalho da Automação do Azure|
|/automationAccounts/jobs/stop/action|Interrompe um trabalho da Automação do Azure|
|/automationAccounts/jobs/streams/read|Obter um fluxo de trabalho da Automação do Azure|
|/automationAccounts/jobs/streams/read|Obter um fluxo de trabalho da Automação do Azure|
|/automationAccounts/jobs/suspend/action|Suspende um trabalho da Automação do Azure|
|/automationAccounts/jobs/suspend/action|Suspende um trabalho da Automação do Azure|
|/automationAccounts/jobs/write|Criar um trabalho da Automação do Azure|
|/automationAccounts/jobs/write|Criar um trabalho da Automação do Azure|
|/automationAccounts/jobSchedules/delete|Excluir uma agenda de trabalho da Automação do Azure|
|/automationAccounts/jobSchedules/read|Obter uma agenda de trabalho da Automação do Azure|
|/automationAccounts/jobSchedules/write|Criar uma agenda de trabalho da Automação do Azure|
|/automationAccounts/linkedWorkspace/read|Obter o espaço de trabalho vinculado à conta de automação|
|/automationAccounts/logDefinitions/read|Obter os logs disponíveis para a conta de automação|
|/automationAccounts/modules/activities/read|Obter atividades de Automação do Azure|
|/automationAccounts/modules/delete|Excluir um módulo da Automação do Azure|
|/automationAccounts/modules/read|Obter um módulo da Automação do Azure|
|/automationAccounts/modules/write|Criar ou atualizar um módulo da Automação do Azure|
|/automationAccounts/nodeConfigurations/delete|Excluir uma configuração de nó do DSC de Automação do Azure|
|/automationAccounts/nodeConfigurations/read|Ler uma configuração de nó do DSC de Automação do Azure|
|/automationAccounts/nodeConfigurations/readContent/action|Ler um conteúdo de configuração de nó do DSC de Automação do Azure|
|/automationAccounts/nodeConfigurations/write|Gravar uma configuração de nó do DSC de Automação do Azure|
|/automationAccounts/nodes/delete|Excluir nós DSC de Automação do Azure|
|/automationAccounts/nodes/read|Ler os nós DSC de Automação do Azure|
|/automationAccounts/nodes/reports/read|Ler o conteúdo do relatório do DSC de Automação do Azure|
|/automationAccounts/nodes/reports/read|Ler relatórios do DSC de Automação do Azure|
|/automationAccounts/objectDataTypes/fields/read|Obter TypeFields de Automação do Azure|
|/automationAccounts/providers/Microsoft.Insights/metricDefinitions/read|Obter definições de Métrica de Automação|
|/automationAccounts/read|Obter uma conta da Automação do Azure|
|/automationAccounts/runbooks/delete|Excluir um runbook da Automação do Azure|
|/automationAccounts/runbooks/draft/publish/action|Publica um rascunho de runbook da Automação do Azure|
|/automationAccounts/runbooks/draft/read|Obter um rascunho de runbook da Automação do Azure|
|/automationAccounts/runbooks/draft/readContent/action|Obter o conteúdo de um rascunho de runbook da Automação do Azure|
|/automationAccounts/runbooks/draft/testJob/read|Obter um trabalho de teste de rascunho do runbook da Automação do Azure|
|/automationAccounts/runbooks/draft/testJob/resume/action|Retoma um trabalho de teste de rascunho de runbook da Automação do Azure|
|/automationAccounts/runbooks/draft/testJob/stop/action|Interrompe um trabalho de teste de rascunho de runbook da Automação do Azure|
|/automationAccounts/runbooks/draft/testJob/suspend/action|Suspende um trabalho de teste de rascunho de runbook da Automação do Azure|
|/automationAccounts/runbooks/draft/testJob/write|Criar um trabalho de teste de rascunho de runbook da Automação do Azure|
|/automationAccounts/runbooks/draft/undoEdit/action|Desfazer edições em um rascunho de runbook da Automação do Azure|
|/automationAccounts/runbooks/draft/writeContent/action|Criar o conteúdo de um rascunho de runbook da Automação do Azure|
|/automationAccounts/runbooks/read|Obter um runbook da Automação do Azure|
|/automationAccounts/runbooks/readContent/action|Obter o conteúdo de um runbook da Automação do Azure|
|/automationAccounts/runbooks/write|Criar ou atualizar um runbook da Automação do Azure|
|/automationAccounts/schedules/delete|Excluir um ativo de agendamento da Automação do Azure|
|/automationAccounts/schedules/read|Obter um ativo de agendamento da Automação do Azure|
|/automationAccounts/schedules/write|Criar ou atualizar um ativo de agendamento da Automação do Azure|
|/automationAccounts/statistics/read|Obter Estatísticas de Automação do Azure|
|/automationAccounts/usages/read|Obter o uso de Automação do Azure|
|/automationAccounts/variables/delete|Excluir um ativo de variável da Automação do Azure|
|/automationAccounts/variables/read|Ler um ativo de variável da Automação do Azure|
|/automationAccounts/variables/write|Criar ou atualizar um ativo de variável da Automação do Azure|
|/automationAccounts/watchers/streams/read|Obter um fluxo de trabalho do observador de Automação do Azure|
|/automationAccounts/webhooks/delete|Excluir um webhook da Automação do Azure |
|/automationAccounts/webhooks/generateUri/action|Gera um URI para um webhook da Automação do Azure|
|/automationAccounts/webhooks/read|Ler um webhook da Automação do Azure|
|/automationAccounts/webhooks/write|Criar ou atualizar um webhook da Automação do Azure|
|/automationAccounts/write|Criar ou atualizar uma conta da Automação do Azure|
|/automationAccounts/write|Criar ou atualizar uma conta da Automação do Azure|

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

| Operação | DESCRIÇÃO |
|---|---|
|/b2cDirectories/delete|Excluir recurso do Active Directory B2C|
|/b2cDirectories/read|Exibir recurso do Active Directory B2C|
|/b2cDirectories/write|Criar ou atualizar recursos do Active Directory B2C|
|/operations/read|Ler todas as operações de API disponíveis para o provedor de recursos Microsoft.AzureActiveDirectory|
|/register/action|Registrar a assinatura do provedor de recursos Microsoft.AzureActiveDirectory|

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

| Operação | DESCRIÇÃO |
|---|---|
|/Operations/read|Obter as propriedades de uma operação do provedor de recursos|
|/register/action|Registrar assinatura com o provedor de recursos Microsoft.AzureStack|
|/registrations/customerSubscriptions/delete|Excluir uma assinatura do cliente do Microsoft Azure Stack|
|/registrations/customerSubscriptions/read|Obter as propriedades uma assinatura do cliente do Microsoft Azure Stack|
|/registrations/customerSubscriptions/write|Criar ou atualizar uma assinatura do cliente do Microsoft Azure Stack|
|/registrations/delete|Excluir um registro do Microsoft Azure Stack|
|/registrations/getActivationKey/action|Obter a última chave de ativação do Microsoft Azure Stack|
|/registrations/products/listDetails/action|Recuperar detalhes estendidos de um produto do Marketplace do Azure Stack|
|/registrations/products/read|Obter as propriedades de um produto do Marketplace do Azure Stack|
|/registrations/read|Obter as propriedades de um registro do Microsoft Azure Stack|
|/registrations/write|Criar ou atualizar um registro do Microsoft Azure Stack|

## <a name="microsoftbatch"></a>Microsoft.Batch

| Operação | DESCRIÇÃO |
|---|---|
|/batchAccounts/applications/delete|Excluir um aplicativo|
|/batchAccounts/applications/read|Listar aplicativos ou obtém as propriedades de um aplicativo|
|/batchAccounts/applications/versions/activate/action|Ativa um pacote de aplicativos|
|/batchAccounts/applications/versions/delete|Excluir um pacote de aplicativos|
|/batchAccounts/applications/versions/read|Obter as propriedades de um pacote de aplicativos|
|/batchAccounts/applications/versions/write|Criar um novo pacote de aplicativos ou atualizar um pacote de aplicativos existente|
|/batchAccounts/applications/write|Criar um novo aplicativo ou atualizar um aplicativo existente|
|/batchAccounts/certificateOperationResults/read|Obter os resultados de uma operação de certificado de execução longa em uma conta do Lote|
|/batchAccounts/certificates/cancelDelete/action|Cancelar a exclusão de falha de um certificado em uma conta do Lote|
|/batchAccounts/certificates/delete|Excluir um certificado de uma conta do Batch|
|/batchAccounts/certificates/read|Listar certificados em uma conta do Lote ou obter as propriedades de um certificado|
|/batchAccounts/certificates/write|Criar um novo certificado em uma conta do Lote ou atualizar um certificado existente|
|/batchAccounts/delete|Excluir uma conta do Lote|
|/batchAccounts/listkeys/action|Listar chaves de acesso para uma conta do Lote|
|/batchAccounts/operationResults/read|Obter os resultados de uma operação de execução longa da conta do Lote|
|/batchAccounts/poolOperationResults/read|Obter os resultados de uma operação de pool de execução longa em uma conta do Lote|
|/batchAccounts/pools/delete|Excluir um pool de uma conta do Batch|
|/batchAccounts/pools/disableAutoscale/action|Desabilitar o escalonamento automático de um pool de conta do Lote|
|/batchAccounts/pools/read|Listar pools em uma conta do Batch ou obter as propriedades de um pool|
|/batchAccounts/pools/stopResize/action|Interromper uma operação de redimensionamento em andamento em um pool de conta do Lote|
|/batchAccounts/pools/upgradeOs/action|Atualizar o sistema operacional de um pool de conta do Lote|
|/batchAccounts/pools/write|Criar um novo pool em uma conta do Lote ou atualizar um pool existente|
|/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/read|Obter a configuração de diagnóstico para o recurso|
|/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o recurso|
|/batchAccounts/providers/Microsoft.Insights/logDefinitions/read|Obter os logs disponíveis para o serviço em Lotes|
|/batchAccounts/providers/Microsoft.Insights/metricDefinitions/read|Obtém as métricas disponíveis para o serviço em Lotes|
|/batchAccounts/read|Listar contas do Lote ou obtém as propriedades de uma conta do Lote|
|/batchAccounts/regeneratekeys/action|Regenera chaves de acesso para uma conta do Lote|
|/batchAccounts/syncAutoStorageKeys/action|Sincroniza as chaves de acesso da conta de armazenamento automática configurada para uma conta do Lote|
|/batchAccounts/write|Criar uma nova conta do Lote ou atualizar uma conta do Lote existente|
|/locations/checkNameAvailability/action|Verificar se o nome da conta é válido e não está em uso.|
|/locations/quotas/read|Obter cotas do Lote da assinatura especificada na região do Azure especificada|
|/register/action|Registra a assinatura do provedor de recursos de lote e permite a criação de contas do Lote|
|/unregister/action|Cancelar o registro da assinatura do Provedor de Recursos do Lote impedindo a criação de contas do Lote|

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

| Operação | DESCRIÇÃO |
|---|---|
|/clusters/delete|Excluir um cluster do IA do Lote do Azure|
|/clusters/read|Listar clusters do IA do Lote do Azure ou obter as propriedades de um cluster do IA do Lote do Azure|
|/clusters/remoteLoginInformation/action|Listar informações de logon remoto para um cluster do IA do Lote do Azure|
|/clusters/write|Criar um novo cluster do IA do Lote do Azure ou atualizar um cluster do IA do Lote do Azure existente|
|/fileservers/delete|Excluir um servidor de arquivos do IA do Lote do Azure|
|/fileservers/read|Listar servidores de arquivos do IA do Lote do Azure ou obter as propriedades de um servidor de arquivos do IA do Lote do Azure|
|/fileservers/resume/action|Retomar um servidor de arquivos do IA do Lote do Azure|
|/fileservers/suspend/action|Suspender um servidor de arquivos do IA do Lote do Azure|
|/fileservers/write|Criar um novo servidor de arquivos do IA do Lote do Azure ou atualizar um servidor de arquivos do IA do Lote do Azure existente|
|/jobs/delete|Excluir um trabalho do IA do Lote do Azure|
|/jobs/read|Listar trabalhos do IA do Lote do Azure ou obter as propriedades de um trabalho do IA do Lote do Azure|
|/jobs/remoteLoginInformation/action|Listar informações de logon remoto para um trabalho do IA do Lote do Azure|
|/jobs/terminate/action|Termina um trabalho do IA do Lote do Azure|
|/jobs/write|Criar um novo trabalho do IA do Lote do Azure ou atualizar um trabalho do IA do Lote do Azure existente|
|/register/action|Registrar a assinatura do Provedor de Recursos do IA do Lote do Azure e habilitar a criação de recursos do IA do Lote do Azure|

## <a name="microsoftbilling"></a>Microsoft.Billing

| Operação | DESCRIÇÃO |
|---|---|
|/billingPeriods/read|Listar os períodos de cobrança disponíveis|
|/invoices/read|Listar faturas disponíveis|

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

| Operação | DESCRIÇÃO |
|---|---|
|/mapApis/Delete|Operação de Exclusão|
|/mapApis/listSecrets/action|Listar os segredos|
|/mapApis/listSingleSignOnToken/action|Ler o token de autorização de logon único para o recurso|
|/mapApis/Read|Operação de Leitura|
|/mapApis/regenerateKey/action|Regenera a chave|
|/mapApis/Write|Operação de Gravação|
|/Operations/read|Descrição da operação.|

## <a name="microsoftcache"></a>Microsoft.Cache

| Operação | DESCRIÇÃO |
|---|---|
|/checknameavailability/action|Verificar se um nome está disponível para uso com um novo Cache Redis|
|/operations/read|Listar as operações que o provedor 'Microsoft.Cache' fornece suporte.|
|/redis/delete|Excluir todo o Cache Redis|
|/redis/export/action|Exportar dados do Redis para blobs de armazenamento prefixados no formato especificado|
|/redis/firewallRules/delete|Excluir regras de firewall de IP de um Cache Redis|
|/redis/firewallRules/read|Obter as regras de firewall de IP de um Cache Redis|
|/redis/firewallRules/write|Editar as regras de firewall de IP de um Cache Redis|
|/redis/forceReboot/action|Forçar a reinicialização de uma instância de cache, possivelmente com perda de dados.|
|/redis/import/action|Importar dados em um formato especificado de vários blobs para o Redis|
|/redis/linkedservers/delete|Excluir servidor vinculado de um Cache Redis|
|/redis/linkedservers/read|Obter servidores vinculados associados a um Cache Redis.|
|/redis/linkedservers/write|Adicionar um servidor vinculado a um Cache Redis|
|/redis/listKeys/action|Exibir o valor das chaves de acesso do Cache Redis no portal de gerenciamento|
|/redis/listUpgradeNotifications/read|Listar as notificações de atualização mais recentes do locatário de cache.|
|/redis/locations/operationresults/read|Obter o resultado de uma operação de execução longa para a qual o cabeçalho 'Localização' foi retornado anteriormente para o cliente|
|/redis/metricDefinitions/read|Obter a métrica disponível para um Cache Redis|
|/redis/patchSchedules/delete|Excluir a agenda de patch de um Cache Redis|
|/redis/patchSchedules/read|Obter o agendamento de aplicação de patch de um Cache Redis|
|/redis/patchSchedules/write|Modificar o agendamento de aplicação de patch de um Cache Redis|
|/redis/read|Exibir as configurações do Cache Redis e a configuração no portal de gerenciamento|
|/redis/regenerateKey/action|Alterar o valor das chaves de acesso do Cache Redis no portal de gerenciamento|
|/redis/start/action|Iniciar uma instância de cache.|
|/redis/stop/action|Interromper uma instância de cache.|
|/redis/write|Modificar as configurações do Cache Redis e a configuração no portal de gerenciamento|
|/register/action|Registra o provedor de recursos 'Microsoft.Cache' com uma assinatura|
|/unregister/action|Cancela o registro do provedor de recursos 'Microsoft.Cache' com uma assinatura|

## <a name="microsoftcapacity"></a>Microsoft.Capacity

| Operação | DESCRIÇÃO |
|---|---|
|/register/action|Registrar o provedor de recursos de Capacidade e permitir a criação de recursos de Capacidade.|
|/reservationorders/action|Atualizar qualquer Reserva|
|/reservationorders/delete|Excluir qualquer Reserva|
|/reservationorders/read|Ler todas as Reservas|
|/reservationorders/reservations/action|Atualizar qualquer Reserva|
|/reservationorders/reservations/delete|Excluir qualquer Reserva|
|/reservationorders/reservations/read|Ler todas as Reservas|
|/reservationorders/reservations/revisions/read|Ler todas as Reservas|
|/reservationorders/reservations/write|Criar qualquer Reserva|
|/reservationorders/write|Criar qualquer Reserva|

## <a name="microsoftcdn"></a>Microsoft.Cdn

| Operação | DESCRIÇÃO |
|---|---|
|/CheckNameAvailability/action||
|/CheckResourceUsage/action||
|/edgenodes/delete||
|/edgenodes/read||
|/edgenodes/write||
|/operationresults/delete||
|/operationresults/profileresults/CheckResourceUsage/action||
|/operationresults/profileresults/delete||
|/operationresults/profileresults/endpointresults/CheckResourceUsage/action||
|/operationresults/profileresults/endpointresults/customdomainresults/delete||
|/operationresults/profileresults/endpointresults/customdomainresults/ DisableCustomHttps/action||
|/operationresults/profileresults/endpointresults/customdomainresults/ EnableCustomHttps/action||
|/operationresults/profileresults/endpointresults/customdomainresults/read||
|/operationresults/profileresults/endpointresults/customdomainresults/write||
|/operationresults/profileresults/endpointresults/delete||
|/operationresults/profileresults/endpointresults/Load/action||
|/operationresults/profileresults/endpointresults/originresults/delete||
|/operationresults/profileresults/endpointresults/originresults/read||
|/operationresults/profileresults/endpointresults/originresults/write||
|/operationresults/profileresults/endpointresults/Purge/action||
|/operationresults/profileresults/endpointresults/read||
|/operationresults/profileresults/endpointresults/Start/action||
|/operationresults/profileresults/endpointresults/Stop/action||
|/operationresults/profileresults/endpointresults/ValidateCustomDomain/action||
|/operationresults/profileresults/endpointresults/write||
|/operationresults/profileresults/GenerateSsoUri/action||
|/operationresults/profileresults/GetSupportedOptimizationTypes/action||
|/operationresults/profileresults/read||
|/operationresults/profileresults/write||
|/operationresults/read||
|/operationresults/write||
|/operations/read||
|/profiles/CheckResourceUsage/action||
|/profiles/delete||
|/profiles/endpoints/CheckResourceUsage/action||
|/profiles/endpoints/customdomains/delete||
|/profiles/endpoints/customdomains/DisableCustomHttps/action||
|/profiles/endpoints/customdomains/EnableCustomHttps/action||
|/profiles/endpoints/customdomains/read||
|/profiles/endpoints/customdomains/write||
|/profiles/endpoints/delete||
|/profiles/endpoints/Load/action||
|/profiles/endpoints/origins/delete||
|/profiles/endpoints/origins/read||
|/profiles/endpoints/origins/write||
|/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/read|Obter as configurações de diagnóstico do recurso|
|/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar as configurações de diagnóstico para o recurso|
|/profiles/endpoints/providers/Microsoft.Insights/logDefinitions/read|Obter os logs disponíveis para o Microsoft.Cdn|
|/profiles/endpoints/Purge/action||
|/profiles/endpoints/read||
|/profiles/endpoints/Start/action||
|/profiles/endpoints/Stop/action||
|/profiles/endpoints/ValidateCustomDomain/action||
|/profiles/endpoints/write||
|/profiles/GenerateSsoUri/action||
|/profiles/GetSupportedOptimizationTypes/action||
|/profiles/read||
|/profiles/write||
|/register/action|Registrar a assinatura no provedor de recursos CDN e habilitar a criação de perfis CDN.|
|/ValidateProbe/action||

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

| Operação | DESCRIÇÃO |
|---|---|
|/certificateOrders/certificates/Delete|Excluir um certificado existente|
|/certificateOrders/certificates/Read|Obter a lista de certificados|
|/certificateOrders/certificates/Write|Adicionar um novo certificado ou atualizar um existente|
|/certificateOrders/Delete|Excluir um AppServiceCertificate existente|
|/certificateOrders/operations/Read|Listar todas as operações do registro de certificado de serviço de aplicativo|
|/certificateOrders/Read|Obter a lista de CertificateOrders|
|/certificateOrders/reissue/Action|Emitir novamente um certificateorder existente|
|/certificateOrders/renew/Action|Renovar um certificateorder existente|
|/certificateOrders/resendEmail/Action|Reenviar email de certificado|
|/certificateOrders/resendRequestEmails/Action|Reenviar emails de solicitação para outro endereço de email|
|/certificateOrders/resendRequestEmails/Action|Recuperar o selo de site para um certificado do Serviço de Aplicativo emitido|
|/certificateOrders/retrieveCertificateActions/Action|Recuperar a lista de ações de certificado|
|/certificateOrders/retrieveEmailHistory/Action|Recuperar o histórico de email do certificado|
|/certificateOrders/verifyDomainOwnership/Action|Verificar a propriedade de domínio|
|/certificateOrders/Write|Adicionar uma nova certificateOrder ou atualizar uma existente|
|/provisionGlobalAppServicePrincipalInUserTenant/Action|Provisionar entidade de serviço para entidade de serviço de aplicativo|
|/register/action|Registrar o provedor de recursos do Microsoft Certificates da assinatura|
|/validateCertificateRegistrationInformation/Action|Validar objeto de compra de certificado sem enviá-lo|

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

| Operação | DESCRIÇÃO |
|---|---|
|/capabilities/read|Mostra as funcionalidades|
|/checkDomainNameAvailability/action|Verificar a disponibilidade de um nome de domínio específico.|
|/domainNames/active/write|Define o nome de domínio ativo.|
|/domainNames/availabilitySets/read|Mostra a conjunto de disponibilidade para o recurso.|
|/domainNames/capabilities/read|Mostra os recursos de nome de domínio|
|/domainNames/delete|Remover os nomes de domínio dos recursos.|
|/domainNames/extensions/delete|Remover as extensões de nome de domínio.|
|/domainNames/extensions/operationStatuses/read|Ler o status da operação das extensões de nomes de domínio.|
|/domainNames/extensions/read|Retornar extensões de nome de domínio.|
|/domainNames/extensions/write|Adicionar as extensões de nome de domínio.|
|/domainNames/internalLoadBalancers/delete|Remover um novo balanceamento de carga interno.|
|/domainNames/internalLoadBalancers/operationStatuses/read|Ler o status da operação para os balanceadores de carga internos dos nomes de domínio.|
|/domainNames/internalLoadBalancers/read|Obter os balanceadores de carga internos.|
|/domainNames/internalLoadBalancers/write|Criar um novo balanceamento de carga interno.|
|/domainNames/loadBalancedEndpointSets/operationStatuses/read|Ler o status da operação para os conjuntos de pontos de extremidade com balanceamento de carga dos nomes de domínio.|
|/domainNames/loadBalancedEndpointSets/read|Mostra os conjuntos de ponto de extremidade com balanceamento de carga|
|/domainNames/read|Retornar os nomes de domínio dos recursos.|
|/domainNames/serviceCertificates/delete|Excluir os certificados de serviço usados.|
|/domainNames/serviceCertificates/operationStatuses/read|Ler o status da operação para os certificados de serviço dos nomes de domínio.|
|/domainNames/serviceCertificates/read|Retornar todos os certificados de serviço usados.|
|/domainNames/serviceCertificates/write|Adicionar ou modificar os certificados de serviço usados.|
|/domainNames/slots/delete|Excluir determinado slot de implantação.|
|/domainNames/slots/operationStatuses/read|Ler o status da operação dos slots de nomes de domínio.|
|/domainNames/slots/read|Mostra os slots de implantação.|
|/domainNames/slots/roles/extensionReferences/delete|Remover a referência de extensão para a função do slot de implantação.|
|/domainNames/slots/roles/extensionReferences/operationStatuses/read|Ler o status da operação de referências de extensão das funções de slot dos nomes de domínio.|
|/domainNames/slots/roles/extensionReferences/read|Retornar a referência de extensão para a função do slot de implantação.|
|/domainNames/slots/roles/extensionReferences/write|Adicionar ou modificar a referência de extensão para a função do slot de implantação.|
|/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read|Obter as configurações de diagnóstico.|
|/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write|Adicionar ou modificar configurações de diagnóstico.|
|/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read|Obter as definições de métrica.|
|/domainNames/slots/roles/read|Obter a função do slot de implantação.|
|/domainNames/slots/roles/roleInstances/operationStatuses/read|Ler o status da operação de instâncias de função das funções de slot dos nomes de domínio.|
|/domainNames/slots/roles/roleInstances/read|Obter a instância de função.|
|/domainNames/slots/roles/roleInstances/rebuild/action|Recompilar a instância de função.|
|/domainNames/slots/roles/roleInstances/reimage/action|Refaz a imagem da instância de função.|
|/domainNames/slots/roles/roleInstances/restart/action|Reinicia as instâncias de função.|
|/domainNames/slots/start/action|Inicia um slot de implantação.|
|/domainNames/slots/state/start/write|Altera o estado do slot de implantação para interrompido.|
|/domainNames/slots/state/stop/write|Altera o estado do slot de implantação para iniciado.|
|/domainNames/slots/stop/action|Suspende o slot de implantação.|
|/domainNames/slots/upgradeDomain/write|Atualizar o domínio.|
|/domainNames/slots/write|Criar ou atualizar a implantação.|
|/domainNames/swap/action|Alterna o slot de preparo para o slot de produção.|
|/domainNames/write|Adicionar ou modificar os nomes de domínio dos recursos.|
|/moveSubscriptionResources/action|Mover todos os recursos clássicos para uma assinatura diferente.|
|/operatingSystemFamilies/read|Listar as famílias de sistemas operacionais convidados disponíveis no Microsoft Azure e também listar as versões dos sistemas operacionais disponíveis para cada família.|
|/operatingSystems/read|Listar as versões do sistema operacional convidado disponíveis atualmente no Microsoft Azure.|
|/quotas/read|Obter a cota para a assinatura.|
|/register/action|Registrar para computação clássica|
|/resourceTypes/skus/read|Obter a lista de SKU para tipos de recursos com suporte.|
|/validateSubscriptionMoveAvailability/action|Valide a disponibilidade da assinatura para a operação de movimentação clássica.|
|/virtualMachines/associatedNetworkSecurityGroups/delete|Excluir o grupo de segurança de rede associado à máquina virtual.|
|/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read|Ler que o status de operação das máquinas virtuais associadas a grupos de segurança de rede.|
|/virtualMachines/associatedNetworkSecurityGroups/read|Obter o grupo de segurança de rede associado à máquina virtual.|
|/virtualMachines/associatedNetworkSecurityGroups/write|Adiciona um grupo de segurança de rede associado à máquina virtual.|
|/virtualMachines/asyncOperations/read|Obter as operações assíncronas possíveis|
|/virtualMachines/attachDisk/action|Anexa um disco de dados a uma máquina virtual.|
|/virtualMachines/delete|Remover as máquinas virtuais.|
|/virtualMachines/detachDisk/action|Desanexa um disco de dados da máquina virtual.|
|/virtualMachines/disks/read|Recupera a lista de discos de dados|
|/virtualMachines/downloadRemoteDesktopConnectionFile/action|Baixa o arquivo RDP na máquina virtual.|
|/virtualMachines/extensions/operationStatuses/read|Ler o status da operação das extensões das máquinas virtuais.|
|/virtualMachines/extensions/read|Obter a extensão da máquina virtual.|
|/virtualMachines/extensions/write|Coloca a extensão da máquina virtual.|
|/virtualMachines/metrics/read|Obter a métrica.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete|Excluir um grupo de segurança de rede associado ao adaptador de rede.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/ operationStatuses/read|Ler que o status de operação das máquinas virtuais associadas a grupos de segurança de rede.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read|Obter um grupo de segurança de rede associado ao adaptador de rede.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write|Adiciona um grupo de segurança de rede associado ao adaptador de rede.|
|/virtualMachines/operationStatuses/read|Ler o status da operação das máquinas virtuais.|
|/virtualMachines/performMaintenance/action|Executar a manutenção na máquina virtual.|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read|Obter as configurações de diagnóstico.|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write|Adicionar ou modificar configurações de diagnóstico.|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|Obter as definições de métrica.|
|/virtualMachines/read|Retorna a lista de máquinas virtuais.|
|/virtualMachines/redeploy/action|Reimplanta a máquina virtual.|
|/virtualMachines/restart/action|Reinicia as máquinas virtuais.|
|/virtualMachines/shutdown/action|Desliga a máquina virtual.|
|/virtualMachines/start/action|Iniciar a máquina virtual.|
|/virtualMachines/stop/action|Interrompe a máquina virtual.|
|/virtualMachines/write|Adicionar ou modificar máquinas virtuais.|

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

| Operação | DESCRIÇÃO |
|---|---|
|/gatewaySupportedDevices/Read|Recupera a lista de dispositivos com suporte.|
|/networkSecurityGroups/delete|Excluir o grupo de segurança de rede.|
|/networkSecurityGroups/operationStatuses/read|Ler o status da operação do grupo de segurança de rede.|
|/networkSecurityGroups/read|Obter o grupo de segurança de rede.|
|/networkSecurityGroups/securityRules/delete|Excluir a regra de segurança.|
|/networkSecurityGroups/securityRules/operationStatuses/read|Ler o status da operação das regras de segurança do grupo de segurança de rede.|
|/networkSecurityGroups/securityRules/read|Obter a regra de segurança.|
|/networkSecurityGroups/securityRules/write|Adiciona ou atualiza uma regra de segurança.|
|/networkSecurityGroups/write|Adiciona um novo grupo de segurança de rede.|
|/quotas/read|Obter a cota para a assinatura.|
|/register/action|Registrar-se na rede clássica|
|/reservedIps/delete|Excluir um IP reservado.|
|/reservedIps/join/action|Ingressar em um IP reservado|
|/reservedIps/link/action|Vincular um IP reservado|
|/reservedIps/operationStatuses/read|Ler o status da operação dos IPs reservados.|
|/reservedIps/read|Obter os IPs reservados|
|/reservedIps/write|Adicionar um novo IP reservado|
|/virtualNetworks/capabilities/read|Mostra as funcionalidades|
|/virtualNetworks/checkIPAddressAvailability/action|Verificar a disponibilidade de determinado endereço IP em uma rede virtual.|
|/virtualNetworks/delete|Excluir a rede virtual.|
|/virtualNetworks/gateways/clientRevokedCertificates/delete|Cancela a revogação de um certificado de cliente.|
|/virtualNetworks/gateways/clientRevokedCertificates/read|Ler os certificados de cliente revogados.|
|/virtualNetworks/gateways/clientRevokedCertificates/write|Revoga um certificado de cliente.|
|/virtualNetworks/gateways/clientRootCertificates/delete|Excluir o certificado de cliente do gateway de rede virtual.|
|/virtualNetworks/gateways/clientRootCertificates/download/action|Baixa o certificado por impressão digital.|
|/virtualNetworks/gateways/clientRootCertificates/listPackage/action|Listar o pacote de certificado do gateway de rede virtual.|
|/virtualNetworks/gateways/clientRootCertificates/read|Localizar os certificados raiz do cliente.|
|/virtualNetworks/gateways/clientRootCertificates/write|Carrega um novo certificado raiz do cliente.|
|/virtualNetworks/gateways/connections/connect/action|Conecta uma conexão de gateway site a site.|
|/virtualNetworks/gateways/connections/disconnect/action|Desconecta uma conexão de gateway site a site.|
|/virtualNetworks/gateways/connections/read|Recupera a lista de conexões.|
|/virtualNetworks/gateways/connections/test/action|Testa uma conexão de gateway site a site.|
|/virtualNetworks/gateways/delete|Excluir o gateway de rede virtual.|
|/virtualNetworks/gateways/downloadDeviceConfigurationScript/action|Baixa o script de configuração do dispositivo.|
|/virtualNetworks/gateways/downloadDiagnostics/action|Baixa o diagnóstico de gateway.|
|/virtualNetworks/gateways/listCircuitServiceKey/action|Recupera a chave de serviço do circuito.|
|/virtualNetworks/gateways/listPackage/action|Listar o pacote de gateway de rede virtual.|
|/virtualNetworks/gateways/operationStatuses/read|Ler o status da operação dos gateways de redes virtuais.|
|/virtualNetworks/gateways/packages/read|Obter o pacote de gateway de rede virtual.|
|/virtualNetworks/gateways/read|Obter os gateways de rede virtual.|
|/virtualNetworks/gateways/startDiagnostics/action|Inicia o diagnóstico do gateway de rede virtual.|
|/virtualNetworks/gateways/stopDiagnostics/action|Interrompe o diagnóstico do gateway de rede virtual.|
|/virtualNetworks/gateways/write|Adiciona um gateway de rede virtual.|
|/virtualNetworks/join/action|Ingressar na rede virtual.|
|/virtualNetworks/operationStatuses/read|Ler o status da operação das redes virtuais.|
|/virtualNetworks/peer/action|Coloca uma rede virtual com outra rede virtual de mesmo nível.|
|/virtualNetworks/read|Obter a rede virtual.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete|Excluir o grupo de segurança de rede associado à sub-rede.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read|Ler o status da operação do grupo de segurança associado à sub-rede da rede virtual.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/read|Obter um grupo de segurança de rede associado à sub-rede.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/write|Adiciona um grupo de segurança de rede associado à sub-rede.|
|/virtualNetworks/write|Adicionar uma nova rede virtual.|

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

| Operação | DESCRIÇÃO |
|---|---|
|/capabilities/read|Mostra as funcionalidades|
|/checkStorageAccountAvailability/action|Verificar a disponibilidade de uma conta de armazenamento.|
|/disks/read|Retornar o disco da conta de armazenamento.|
|/images/read|Retornar a imagem.|
|/osImages/read|Retornar a imagem do sistema operacional.|
|/publicImages/read|Obter a imagem pública da máquina virtual.|
|/quotas/read|Obter a cota para a assinatura.|
|/register/action|Registrar para armazenamento clássico|
|/storageAccounts/delete|Excluir a conta de armazenamento.|
|/storageAccounts/disks/delete|Excluir um disco de conta de armazenamento específico.|
|/storageAccounts/disks/operationStatuses/read|Ler o status da operação do recurso.|
|/storageAccounts/disks/read|Retornar o disco da conta de armazenamento.|
|/storageAccounts/disks/write|Adiciona um disco de conta de armazenamento.|
|/storageAccounts/images/delete|Excluir uma imagem da conta de armazenamento específica.|
|/storageAccounts/images/read|Retornar a imagem da conta de armazenamento.|
|/storageAccounts/listKeys/action|Listar as chaves de acesso das contas de armazenamento.|
|/storageAccounts/operationStatuses/read|Ler o status da operação do recurso.|
|/storageAccounts/osImages/delete|Excluir uma imagem do sistema operacional da conta de armazenamento específica.|
|/storageAccounts/osImages/read|Retornar a imagem do sistema operacional da conta de armazenamento.|
|/storageAccounts/read|Retornar a conta de armazenamento com a conta fornecida.|
|/storageAccounts/regenerateKey/action|Regenera as chaves de acesso existentes da conta de armazenamento.|
|/storageAccounts/services/diagnosticSettings/read|Obter as configurações de diagnóstico.|
|/storageAccounts/services/diagnosticSettings/write|Adicionar ou modificar configurações de diagnóstico.|
|/storageAccounts/services/metricDefinitions/read|Obter as definições de métrica.|
|/storageAccounts/services/metrics/read|Obter a métrica.|
|/storageAccounts/services/read|Obter os serviços disponíveis.|
|/storageAccounts/write|Adiciona uma nova conta de armazenamento.|

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

| Operação | DESCRIÇÃO |
|---|---|
|/accounts/delete|Excluir contas de API|
|/accounts/listKeys/action|Listar chaves|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Obter a configuração de diagnóstico para o recurso.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o recurso.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Obter as métricas disponíveis para os Serviços Cognitivos.|
|/accounts/read|Ler as contas de API.|
|/accounts/regenerateKey/action|Regenerar chave|
|/accounts/skus/read|Ler SKUs disponíveis para um recurso existente.|
|/accounts/usages/read|Obter a utilização de cota de um recurso existente.|
|/accounts/write|Grava as contas de API.|
|/locations/checkSkuAvailability/action|Ler as SKUs disponíveis para uma assinatura.|
|/Operations/read|Listar todas as operações disponíveis|
|/register/action|Registrar assinatura para Serviços Cognitivos|
|/skus/read|Ler as SKUs disponíveis para os Serviços Cognitivos.|

## <a name="microsoftcommerce"></a>Microsoft.Commerce

| Operação | DESCRIÇÃO |
|---|---|
|/RateCard/read|Retornar dados da oferta, metadados de recurso/medidor e tarifas da assinatura especificada.|
|/UsageAggregates/read|Recupera o consumo do Microsoft Azure para uma assinatura. O resultado contém dados totais de uso, assinatura e informações relativas a recursos em determinado intervalo de tempo.|

## <a name="microsoftcompute"></a>Microsoft.Compute

| Operação | DESCRIÇÃO |
|---|---|
|/availabilitySets/delete|Excluir o conjunto de disponibilidade|
|/availabilitySets/read|Obter as propriedades de um conjunto de disponibilidade|
|/availabilitySets/vmSizes/read|Listar os tamanhos disponíveis para criar ou atualizar uma máquina virtual no conjunto de disponibilidade|
|/availabilitySets/write|Criar um novo conjunto de disponibilidade ou atualizar um existente|
|/disks/beginGetAccess/action|Obter o URI de SAS do disco para acesso de blob|
|/disks/delete|Excluir o disco|
|/disks/endGetAccess/action|Revogar o URI de SAS do disco|
|/disks/read|Obter as propriedades de um disco|
|/disks/write|Criar um novo disco ou atualizar um existente|
|/images/delete|Excluir a imagem|
|/images/read|Obter as propriedades da imagem|
|/images/write|Criar uma nova imagem ou atualizar uma existente|
|/locations/capsOperations/read|Obtém o status de uma operação de Limites assíncrona|
|/locations/diskOperations/read|Obtém o status de uma operação de Disco assíncrona|
|/locations/operations/read|Obter o status de uma operação assíncrona|
|/locations/publishers/artifacttypes/offers/read|Obter as propriedades de uma Oferta de Imagem de Plataforma|
|/locations/publishers/artifacttypes/offers/skus/read|Obter as propriedades de uma SKU de Imagem de Plataforma|
|/locations/publishers/artifacttypes/offers/skus/versions/read|Obter as propriedades de uma SKU de Imagem de Plataforma|
|/locations/publishers/artifacttypes/types/read|Obter as propriedades de um tipo de VMExtension|
|/locations/publishers/artifacttypes/types/versions/read|Obter as propriedades de uma versão do VMExtension|
|/locations/publishers/read|Obter as propriedades de um Publicador|
|/locations/runCommands/read|Listar os comandos de execução disponíveis no local|
|/locations/usages/read|Obter as quantidades de uso atual e limites de serviço para os recursos de computação da assinatura em determinado local|
|/locations/vmSizes/read|Listar os tamanhos de máquina virtual disponível em um local|
|/operations/read|Listar as operações disponíveis no provedor de recursos Microsoft.Compute|
|/register/action|Registra a assinatura com o provedor de recursos Microsoft.Compute|
|/restorePointCollections/delete|Excluir a coleção de pontos de restauração e os pontos contidos nela|
|/restorePointCollections/read|Obter as propriedades de uma coleção de pontos de restauração|
|/restorePointCollections/restorePoints/delete|Excluir o ponto de restauração|
|/restorePointCollections/restorePoints/read|Obter as propriedades de um ponto de restauração|
|/restorePointCollections/restorePoints/retrieveSasUris/action|Obter as propriedades de um ponto de restauração com URIs de SAS do blob|
|/restorePointCollections/restorePoints/write|Criar um novo ponto de restauração|
|/restorePointCollections/write|Criar uma nova coleção de pontos de restauração ou atualizar uma existente|
|/sharedVMImages/delete|Exclui o SharedVMImage|
|/sharedVMImages/read|Obter as propriedades de um SharedVMImage|
|/sharedVMImages/versions/delete|Excluir um SharedVMImageVersion|
|/sharedVMImages/versions/read|Obter as propriedades de um SharedVMImageVersion|
|/sharedVMImages/versions/replicate/action|Replicar um SharedVMImageVersion para direcionar regiões|
|/sharedVMImages/versions/write|Criar um novo SharedVMImageVersion ou atualizar um existente|
|/sharedVMImages/write|Criar um novo SharedVMImage ou atualizar um existente|
|/skus/read|Obter a lista de SKUs do Microsoft.Compute disponíveis para sua Assinatura|
|/snapshots/beginGetAccess/action|Obter o URI de SAS do Instantâneo para acesso de blob|
|/snapshots/delete|Excluir um instantâneo|
|/snapshots/endGetAccess/action|Revogar o URI de SAS do Instantâneo|
|/snapshots/read|Obter as propriedades de um instantâneo|
|/snapshots/write|Criar um novo instantâneo ou atualizar um existente|
|/virtualMachines/capture/action|Capturar a máquina virtual copiando discos rígidos virtuais e gera um modelo que pode ser usado para criar máquinas virtuais semelhantes|
|/virtualMachines/convertToManagedDisks/action|Converter os discos baseados em blob da máquina virtual em discos gerenciados|
|/virtualMachines/deallocate/action|Desligar a máquina virtual e liberar os recursos de computação|
|/virtualMachines/delete|Excluir a máquina virtual|
|/virtualMachines/extensions/delete|Excluir a extensão da máquina virtual|
|/virtualMachines/extensions/read|Obter as propriedades de uma extensão da máquina virtual|
|/virtualMachines/extensions/write|Criar uma nova extensão da máquina virtual ou atualizar uma existente|
|/virtualMachines/generalize/action|Definir o estado da máquina virtual como Generalizada e preparar a máquina virtual para captura|
|/virtualMachines/instanceView/read|Obter o status de tempo de execução detalhado da máquina virtual e seus recursos|
|/virtualMachines/performMaintenance/action|Executar a Operação de Manutenção na VM.|
|/virtualMachines/powerOff/action|Desligar a máquina virtual. Observe que a máquina virtual continuará a ser cobrada.|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|Ler definições de Métrica de Máquina Virtual|
|/virtualMachines/read|Obter as propriedades de uma máquina virtual|
|/virtualMachines/redeploy/action|Reimplantar a máquina virtual|
|/virtualMachines/reimage/action|Refazer a imagem da máquina virtual que está usando o disco diferencial.|
|/virtualMachines/restart/action|Reinicia a máquina virtual|
|/virtualMachines/runCommand/action|Executar um script predefinido na máquina virtual|
|/virtualMachines/start/action|Inicia a máquina virtual|
|/virtualMachines/vmSizes/read|Listar os tamanhos disponíveis para os quais a máquina virtual possa ser atualizada|
|/virtualMachines/write|Criar uma nova máquina virtual ou atualizar uma máquina virtual existente|
|/virtualMachineScaleSets/deallocate/action|Desligar e liberar os recursos de computação para as instâncias do Conjunto de Dimensionamento de Máquinas Virtuais |
|/virtualMachineScaleSets/delete|Excluir o Conjunto de Dimensionamento de Máquinas Virtuais|
|/virtualMachineScaleSets/delete/action|Excluir as instâncias do Conjunto de Dimensionamento de Máquinas Virtuais|
|/virtualMachineScaleSets/extensions/delete|Excluir a Extensão de Conjunto de Dimensionamento de Máquinas Virtuais|
|/virtualMachineScaleSets/extensions/read|Obter as propriedades de uma Extensão de Conjunto de Dimensionamento de Máquinas Virtuais|
|/virtualMachineScaleSets/extensions/write|Criar uma nova Extensão de Conjunto de Dimensionamento de Máquinas Virtuais ou atualizar um existente|
|/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action|Percorrer manualmente os domínios de atualização de plataforma de um conjunto de dimensionamento de máquinas virtuais do Service Fabric para concluir uma atualização pendente que está paralisada|
|/virtualMachineScaleSets/instanceView/read|Obter a exibição de instância do Conjunto de Dimensionamento de Máquinas Virtuais|
|/virtualMachineScaleSets/manualUpgrade/action|Atualizar manualmente as instâncias para o modelo mais recente do Conjunto de Dimensionamento de Máquinas Virtuais|
|/virtualMachineScaleSets/networkInterfaces/read|Obter propriedades de todas as interfaces de rede de um Conjunto de Dimensionamento de Máquinas Virtuais|
|/virtualMachineScaleSets/osUpgradeHistory/read|Obter o histórico de atualizações do SO para um Conjunto de Dimensionamento de Máquinas Virtuais|
|/virtualMachineScaleSets/performMaintenance/action|Realizar a manutenção planejada nas instâncias do Conjunto de Dimensionamento de Máquinas Virtuais|
|/virtualMachineScaleSets/powerOff/action|Desligar a instância do Conjunto de Dimensionamento de Máquinas Virtuais|
|/virtualMachineScaleSets/providers/Microsoft.Insights/metricDefinitions/read|Ler as Definições de Métricas do Conjunto de Dimensionamento de Máquinas Virtuais|
|/virtualMachineScaleSets/publicIPAddresses/read|Obter propriedades de todos os endereços IP públicos de um Conjunto de Dimensionamento de Máquinas Virtuais|
|/virtualMachineScaleSets/read|Obter as propriedades de um Conjunto de Dimensionamento de Máquinas Virtuais|
|/virtualMachineScaleSets/redeploy/action|Reimplantar as instâncias do Conjunto de Dimensionamento de Máquinas Virtuais|
|/virtualMachineScaleSets/reimage/action|Refazer a imagem das instâncias do Conjunto de Dimensionamento de Máquinas Virtuais|
|/virtualMachineScaleSets/restart/action|Reiniciar a instância do Conjunto de Dimensionamento de Máquinas Virtuais|
|/virtualMachineScaleSets/rollingUpgrades/cancel/action|Cancelar a atualização sem interrupção de um Conjunto de Dimensionamento de Máquinas Virtuais|
|/virtualMachineScaleSets/rollingUpgrades/read|Obter o status mais recente de Atualização Sem Interrupção para um Conjunto de Dimensionamento de Máquinas Virtuais|
|/virtualMachineScaleSets/scale/action|Verificar se um Conjunto de Dimensionamento de Máquinas Virtuais existente pode Reduzir Horizontalmente/Escalar Horizontalmente para a contagem de instâncias especificada|
|/virtualMachineScaleSets/skus/read|Listar as SKUs válidas para um Conjunto de Dimensionamento de Máquinas Virtuais existente|
|/virtualMachineScaleSets/start/action|Iniciar a instância do Conjunto de Dimensionamento de Máquinas Virtuais|
|/virtualMachineScaleSets/virtualMachines/deallocate/action|Desligar e liberar os recursos de computação para uma máquina virtual em um conjunto de dimensionamento de máquinas virtuais.|
|/virtualMachineScaleSets/virtualMachines/delete|Excluir uma máquina virtual específica em um conjunto de dimensionamento de VM.|
|/virtualMachineScaleSets/virtualMachines/instanceView/read|Recuperar a exibição de instância de uma máquina virtual em um conjunto de dimensionamento de máquinas virtuais.|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/ ipConfigurations/publicIPAddresses/read|Obter propriedades do endereço IP público criado usando o Conjunto de Dimensionamento de Máquinas Virtuais. O Conjunto de Dimensionamento de Máquinas Virtuais pode criar no máximo um IP público por ipconfiguration (IP privado)|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read|Obter propriedades de uma ou de todas as configurações IP de uma interface de rede criada usando o Conjunto de Dimensionamento de Máquinas Virtuais. Configurações de IP representam IPs privados|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/read|Obter propriedades de uma ou de todas as interfaces de rede de uma máquina virtual criada usando o Conjunto de Dimensionamento de Máquinas Virtuais|
|/virtualMachineScaleSets/virtualMachines/performMaintenance/action|Realizar manutenção planejada em uma instância de Máquina Virtual em um Conjunto de Dimensionamento de Máquinas Virtuais|
|/virtualMachineScaleSets/virtualMachines/powerOff/action|Desligar uma instância de máquina virtual em um conjunto de dimensionamento de VM.|
|/virtualMachineScaleSets/virtualMachines/providers/ Microsoft.Insights/metricDefinitions/read|Ler Máquina Virtual em Definições de Métricas do Conjunto de Dimensionamento|
|/virtualMachineScaleSets/virtualMachines/read|Recuperar as propriedades de uma máquina virtual em um conjunto de dimensionamento de máquinas virtuais|
|/virtualMachineScaleSets/virtualMachines/redeploy/action|Reimplantar uma instância de Máquina Virtual em um Conjunto de Dimensionamento de Máquinas Virtuais|
|/virtualMachineScaleSets/virtualMachines/reimage/action|Refazer a imagem de uma instância de Máquina Virtual em um Conjunto de Dimensionamento de Máquinas Virtuais.|
|/virtualMachineScaleSets/virtualMachines/restart/action|Reiniciar uma instância de máquina virtual em um conjunto de dimensionamento de VM.|
|/virtualMachineScaleSets/virtualMachines/start/action|Iniciar uma instância de máquina virtual em um conjunto de dimensionamento de VM.|
|/virtualMachineScaleSets/virtualMachines/write|Atualizar as propriedades de uma Máquina Virtual em um Conjunto de Dimensionamento de Máquinas Virtuais|
|/virtualMachineScaleSets/write|Criar um novo Conjunto de Dimensionamento de Máquinas Virtuais ou atualizar um existente|

## <a name="microsoftconsumption"></a>Microsoft.Consumption

| Operação | DESCRIÇÃO |
|---|---|
|/balances/read|Listar o resumo de utilização de um período de cobrança para um grupo de gerenciamento.|
|/budgets/read|Listar os orçamentos por uma assinatura ou um grupo de gerenciamento.|
|/budgets/write|Criar, atualizar e excluir os orçamentos por uma assinatura ou um grupo de gerenciamento.|
|/marketplaces/read|Listar os detalhes de uso do recurso do marketplace para um escopo para assinaturas do EA e WebDirect.|
|/operations/read|Listar todas as operações com suporte pelo provedor de recursos Microsoft.Consumption.|
|/pricesheets/read|Listar os dados de Pricesheets para uma assinatura ou um grupo de gerenciamento.|
|/reservationDetails/read|Listar os detalhes de utilização para instâncias reservadas por ordem de reserva ou grupos de gerenciamento. Os dados detalhados são por instância por nível diário.|
|/reservationSummaries/read|Listar os detalhes de resumo para instâncias reservadas por ordem de reserva ou grupos de gerenciamento. Os dados de resumo serão no nível mensal ou diário.|
|/reservationTransactions/read|Listar o histórico de transações para instâncias reservadas por grupos de gerenciamento.|
|/terms/read|Listar os termos de uma assinatura ou um grupo de gerenciamento.|
|/usageDetails/read|Listar os detalhes de uso de um escopo para assinaturas do EA e WebDirect.|

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

| Operação | DESCRIÇÃO |
|---|---|
|/containerGroups/containers/logs/read|Obter logs para um contêiner específico.|
|/containerGroups/delete|Excluir o grupo de contêineres específico.|
|/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read|Obter a configuração de diagnóstico para o grupo de contêineres.|
|/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o grupo de contêineres.|
|/containerGroups/providers/Microsoft.Insights/metricDefinitions/read|Obter as métricas disponíveis para o grupo de contêineres.|
|/containerGroups/read|Obter todos os grupos de contêineres.|
|/containerGroups/write|Criar ou atualizar um grupo de contêineres específico.|

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

| Operação | DESCRIÇÃO |
|---|---|
|/checkNameAvailability/read|Verificar se o nome do registro de contêiner está disponível para uso.|
|/locations/operationResults/read|Obter um resultado de operação assíncrona|
|/operations/read|Listar todas as operações da API REST do Registro de Contêiner do Azure disponíveis|
|/register/action|Registra a assinatura do provedor de recursos de registro de contêiner e permite a criação de registros de contêiner.|
|/registries/delete|Excluir um registro de contêiner.|
|/registries/eventGridFilters/delete|Excluir um filtro de grade de eventos de um registro de contêiner.|
|/registries/eventGridFilters/read|Obter as propriedades do filtro de grade de eventos especificado ou listar todos os filtros de grade de eventos para o registro de contêiner especificado.|
|/registries/eventGridFilters/write|Criar ou atualizar um filtro de grade de eventos para um registro de contêiner com os parâmetros especificados.|
|/registries/listCredentials/action|Listar as credenciais de logon para o registro de contêiner especificado.|
|/registries/listUsages/read|Listar os usos de cotas para o registro de contêiner especificado.|
|/registries/operationStatuses/read|Obter um status de operação assíncrona do registro|
|/registries/read|Obter as propriedades do registro de contêiner especificado ou listar todos os registros de contêiner sob o grupo de recursos ou assinatura especificada.|
|/registries/regenerateCredential/action|Regenerar uma das credenciais de logon para o registro de contêiner especificado.|
|/registries/replications/delete|Excluir uma replicação de um registro de contêiner.|
|/registries/replications/operationStatuses/read|Obter um status de operação assíncrona de replicação|
|/registries/replications/read|Obter as propriedades da replicação especificada ou listar todas as replicações do registro de contêiner especificado.|
|/registries/replications/write|Criar ou atualizar uma replicação para um registro de contêiner com os parâmetros especificados.|
|/registries/webhooks/delete|Excluir um webhook de um registro de contêiner.|
|/registries/webhooks/getCallbackConfig/action|Obter a configuração de URI de serviço e cabeçalhos personalizados para o webhook.|
|/registries/webhooks/listEvents/action|Listar eventos recentes para o webhook especificado.|
|/registries/webhooks/operationStatuses/read|Obter um status de operação assíncrona do webhook|
|/registries/webhooks/ping/action|Disparar um evento de ping para ser enviado ao webhook.|
|/registries/webhooks/read|Obter as propriedades do webhook especificado ou listar todos os webhooks para o registro de contêiner especificado.|
|/registries/webhooks/write|Criar ou atualizar um webhook para um registro de contêiner com os parâmetros especificados.|
|/registries/write|Criar ou atualizar um registro de contêiner com os parâmetros especificados.|

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

| Operação | DESCRIÇÃO |
|---|---|
|/containerServices/delete|Excluir o serviço de contêiner especificado|
|/containerServices/read|Obter o Serviço de Contêiner especificado|
|/containerServices/write|Inserir ou atualizar o Serviço de Contêiner especificado|

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

| Operação | DESCRIÇÃO |
|---|---|
|/applications/delete|Operação de Exclusão|
|/applications/listSecrets/action|Listar segredos|
|/applications/listSingleSignOnToken/action|Ler tokens de logon único|
|/applications/read|Operação de Leitura|
|/applications/write|Operação de Gravação|
|/applications/write|Operação de Gravação|
|/listCommunicationPreference/action|Listar preferência de comunicação|
|/operations/read|operações de leitura|
|/updateCommunicationPreference/action|Atualizar as preferências de comunicação|

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

| Operação | DESCRIÇÃO |
|---|---|
|/hubs/adobemetadata/action|Criar ou atualizar qualquer Metadado do Adobe do Azure Customer Insights|
|/hubs/adobemetadata/read|Ler qualquer Metadado do Adobe do Azure Customer Insights|
|/hubs/authorizationPolicies/delete|Excluir política de assinatura de acesso compartilhado do Azure Customer Insights|
|/hubs/authorizationPolicies/read|Ler política de assinatura de acesso compartilhado do Azure Customer Insights|
|/hubs/authorizationPolicies/regeneratePrimaryKey/action|Regenerar a chave primária da política de Assinatura de Acesso Compartilhado do Azure Customer Insights|
|/hubs/authorizationPolicies/regenerateSecondaryKey/action|Regenerar a chave secundária da política de Assinatura de Acesso Compartilhado do Azure Customer Insights|
|/hubs/authorizationPolicies/write|Criar ou atualizar política de assinatura de acesso compartilhado do Azure Customer Insights|
|/hubs/connectors/activate/action|Ativar qualquer Conector do Azure Customer Insights|
|/hubs/connectors/activate/action|Ativar qualquer Conector do Azure Customer Insights|
|/hubs/connectors/delete|Excluir conector do Azure Customer Insights|
|/hubs/connectors/getruntimestatus/action|Obter qualquer status de tempo de execução do Azure Customer Insights Connector|
|/hubs/connectors/mappings/activate/action|Ativar qualquer Mapeamento do Conector do Azure Customer Insights|
|/hubs/connectors/mappings/delete|Excluir mapeamento do conector do Azure Customer Insights|
|/hubs/connectors/mappings/operations/read|Ler qualquer resultado da operação Mapeamento do Conector do Azure Customer Insights|
|/hubs/connectors/mappings/read|Ler mapeamento do conector do Azure Customer Insights|
|/hubs/connectors/mappings/write|Criar ou atualizar mapeamento de conector do Azure Customer Insights|
|/hubs/connectors/operations/read|Ler qualquer resultado de operação do Conector do Azure Customer Insights|
|/hubs/connectors/read|Ler conector do Azure Customer Insights|
|/hubs/connectors/saveauthinfo/action|Criar ou atualizar qualquer informação de autenticação do conector do Azure Customer Insights|
|/hubs/connectors/update/action|Atualizar qualquer conector do Azure Customer Insights|
|/hubs/connectors/write|Criar ou atualizar conector do Azure Customer Insights|
|/hubs/crmmetadata/action|Criar ou atualizar quaisquer metadados do CRM do Azure Customer Insights|
|/hubs/crmmetadata/read|Ler todos os metadados do CRM do Azure Customer Insights|
|/hubs/delete|Excluir Hub de informações do Customer Insights|
|/hubs/gdpr/delete|Excluir qualquer GDPR do Azure Customer Insights|
|/hubs/gdpr/read|Ler qualquer GDPR do Azure Customer Insights|
|/hubs/gdpr/write|Criar ou atualizar qualquer GDPR do Azure Customer Insights|
|/hubs/getbillingcredits/read|Obter créditos de cobrança do Hub do Azure Customer Insights|
|/hubs/getbillinghistory/read|Obter histórico de cobrança do Hub do Azure Customer Insights|
|/hubs/images/delete|Excluir qualquer imagem do Azure Customer Insights|
|/hubs/images/read|Ler qualquer imagem do Azure Customer Insights|
|/hubs/images/write|Criar ou atualizar qualquer imagem do Azure Customer Insights|
|/hubs/interactions/delete|Excluir quaisquer interações do Azure Customer Insights|
|/hubs/interactions/operations/read|Ler qualquer resultado da operação de interação do Azure Customer Insights|
|/hubs/interactions/read|Ler interação do Azure Customer Insights|
|/hubs/interactions/suggestrelationshiplinks/action|Sugerir links do RelationShip para qualquer interação do Azure Customer Insights|
|/hubs/interactions/write|Criar ou atualizar interação do Azure Customer Insights|
|/hubs/kpi/delete|Excluir indicador principal de desempenho do Azure Customer Insights|
|/hubs/kpi/operations/read|Ler qualquer resultado da operação de Indicadores chave de desempenho do Azure Customer Insights|
|/hubs/kpi/read|Ler indicador principal de desempenho do Azure Customer Insights|
|/hubs/kpi/reprocess/action|Reprocessar quaisquer indicadores chave de desempenho do Azure Customer Insights|
|/hubs/kpi/write|Criar ou atualizar indicador principal de desempenho do Azure Customer Insights|
|/hubs/links/delete|Excluir todos os links do Azure Customer Insights|
|/hubs/links/operations/read|Ler qualquer resultado da operação de links do Azure Customer Insights|
|/hubs/links/read|Ler todos os links do Azure Customer Insights|
|/hubs/links/write|Criar ou atualizar qualquer link do Azure Customer Insights|
|/hubs/msemetadata/action|Criar ou atualizar qualquer metadado MSE do Azure Customer Insights|
|/hubs/msemetadata/read|Ler todos os metadados MSE do Azure Customer Insights|
|/hubs/operationresults/read|Obter resultados da operação do Hub do Azure Customer Insights|
|/hubs/predictions/delete|Excluir quaisquer previsões do Azure Customer Insights|
|/hubs/predictions/operations/read|Ler qualquer resultado da operação Previsões do Azure Customer Insights|
|/hubs/predictions/read|Ler todas as previsões do Azure Customer Insights|
|/hubs/predictions/write|Criar ou atualizar qualquer previsão do Azure Customer|
|/hubs/predictivematchpolicies/delete|Excluir qualquer Política de Correspondência Preditiva do Azure Customer Insights|
|/hubs/predictivematchpolicies/operations/read|Ler qualquer resultado da operação de Política de Correspondência Preditiva do Azure Customer Insights|
|/hubs/predictivematchpolicies/read|Ler todas as Políticas de Correspondência Preditiva do Azure Customer Insights|
|/hubs/predictivematchpolicies/write|Criar ou atualizar qualquer Política de Correspondência Preditiva do Azure Customer Insights|
|/hubs/profiles/delete|Excluir qualquer perfil do Azure Customer Insights|
|/hubs/profiles/operations/read|Ler qualquer resultado da operação do Perfil do Azure Customer Insights|
|/hubs/profiles/read|Ler perfil do Azure Customer Insights|
|/hubs/profiles/write|Gravar qualquer perfil do Azure Customer Insights|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/read|Obter a configuração de diagnóstico para o recurso|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o recurso|
|/hubs/providers/Microsoft.Insights/logDefinitions/read|Obter os logs disponíveis para o recurso|
|/hubs/providers/Microsoft.Insights/metricDefinitions/read|Obter a métrica disponível para o recurso|
|/hubs/read|Ler Hub de informações do Customer Insights|
|/hubs/relationshiplinks/delete|Excluir todos os links do relacionamento do Azure Customer Insights|
|/hubs/relationshiplinks/operations/read|Ler qualquer resultado da operação Links do Relacionamento do Azure Customer Insights|
|/hubs/relationshiplinks/read|Ler todos os links do relacionamento do Azure Customer Insights|
|/hubs/relationshiplinks/write|Criar ou atualizar qualquer link do relacionamento do Azure Customer Insights|
|/hubs/relationships/delete|Excluir todos os relacionamentos do Azure Customer Insights|
|/hubs/relationships/operations/read|Ler qualquer resultado da operação Relacionamentos do Azure Customer Insights|
|/hubs/relationships/read|Ler todos os relacionamentos do Azure Customer Insights|
|/hubs/relationships/write|Criar ou atualizar qualquer relacionamento do Azure Customer Insights|
|/hubs/roleAssignments/delete|Excluir atribuição RBAC do Azure Customer Insights|
|/hubs/roleAssignments/operations/read|Ler qualquer resultado da operação de Atribuição RBAC do Azure Customer Insights|
|/hubs/roleAssignments/read|Ler atribuição RBAC do Azure Customer Insights|
|/hubs/roleAssignments/write|Criar ou atualizar atribuição RBAC do Azure Customer Insights|
|/hubs/roles/read|Ler todos os papéis RBAC do Azure Customer Insights|
|/hubs/salesforcemetadata/action|Criar ou atualizar quaisquer metadados do Salesforce para o Azure Customer Insights|
|/hubs/salesforcemetadata/read|Ler todos os metadados do Salesforce do Azure Customer Insights|
|/hubs/segments/delete|Excluir todos os segmentos do Azure Customer Insights|
|/hubs/segments/dynamic/action|Gerenciar qualquer segmento dinâmico do Azure Customer Insight|
|/hubs/segments/read|Ler segmentos do Azure Customer Insights|
|/hubs/segments/static/action|Gerenciar segmentos estáticos do Azure Customer Insight|
|/hubs/segments/write|Criar ou atualizar qualquer segmento do Azure Customer Insights|
|/hubs/sqlconnectionstrings/delete|Excluir qualquer SqlConnectionStrings do Azure Customer Insights|
|/hubs/sqlconnectionstrings/read|Ler qualquer SqlConnectionStrings do Azure Customer Insights|
|/hubs/sqlconnectionstrings/write|Criar ou atualizar qualquer SqlConnectionStrings do Azure Customer Insights|
|/hubs/suggesttypeschema/action|Gerar Esquema de Tipo de Sugestão a partir de dados de exemplo|
|/hubs/tenantmanagement/read|Gerenciar qualquer configuração de hub do Azure Customer Insights|
|/hubs/views/delete|Excluir modo de exibição de aplicativo do Azure Customer Insights|
|/hubs/views/read|Ler modo de exibição de aplicativo do Azure Customer Insights|
|/hubs/views/write|Criar ou atualizar modo de exibição de aplicativo do Azure Customer Insights|
|/hubs/widgettypes/read|Ler todos os tipos de widget do aplicativo do Azure Customer Insights|
|/hubs/write|Criar ou atualizar um Hub de informações do Customer Insights|
|/operations/read|Ler os Metadados da Api do Azure Customer Insights|
|/register/action|Registrar a assinatura do provedor de recursos do Customer Insights e permitir a criação de recursos do Customer Insights|
|/unregister/action|Cancelar o registro da assinatura do provedor de recursos do Customer Insights|

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

| Operação | DESCRIÇÃO |
|---|---|
|/catalogs/delete|Excluir o catálogo.|
|/catalogs/read|Obter propriedades dos catálogos na assinatura ou no grupo de recursos.|
|/catalogs/write|Criar catálogo ou atualizar as marcações e propriedades do catálogo.|
|/checkNameAvailability/action|Verificar a disponibilidade do nome do catálogo para o locatário.|
|/operations/read|Listar operações disponíveis no provedor de recursos Microsoft.DataCatalog.|
|/register/action|Registrar a assinatura com o provedor de recursos Microsoft.DataCatalog.|
|/unregister/action|Cancelar a assinatura do provedor de recursos Microsoft.DataCatalog.|

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

| Operação | DESCRIÇÃO |
|---|---|
|/datafactories/providers/Microsoft.Insights/diagnosticSettings/read|Obter a configuração de diagnóstico para o recurso|
|/datafactories/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o recurso|
|/datafactories/providers/Microsoft.Insights/metricDefinitions/read|Obter as métricas disponíveis para os datafactories|
|/factories/providers/Microsoft.Insights/diagnosticSettings/read|Obter a configuração de diagnóstico para o recurso|
|/factories/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o recurso|
|/factories/providers/Microsoft.Insights/logDefinitions/read|Obter os logs disponíveis para fábricas|
|/factories/providers/Microsoft.Insights/metricDefinitions/read|Obter as métricas disponíveis para fábricas|

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

| Operação | DESCRIÇÃO |
|---|---|
|/accounts/computePolicies/delete|Excluir uma política de computação.|
|/accounts/computePolicies/read|Obter informações sobre uma política de computação.|
|/accounts/computePolicies/write|Criar ou atualizar uma política de computação.|
|/accounts/dataLakeStoreAccounts/delete|Desvincular uma conta do Data Lake Store de uma conta do Data Lake Analytics.|
|/accounts/dataLakeStoreAccounts/read|Obter informações sobre uma conta vinculada do Data Lake Store de uma conta do Data Lake Analytics.|
|/accounts/dataLakeStoreAccounts/write|Criar ou atualizar uma conta DataLakeStore vinculada de uma conta DataLakeAnalytics.|
|/accounts/delete|Excluir uma conta DataLakeAnalytics.|
|/accounts/firewallRules/delete|Excluir uma regra de firewall.|
|/accounts/firewallRules/read|Obter informações sobre uma regra de firewall.|
|/accounts/firewallRules/write|Criar ou atualizar uma regra de firewall.|
|/accounts/operationResults/read|Obter o resultado de uma operação de conta DataLakeAnalytics.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Obter as configurações de diagnóstico para a conta DataLakeAnalytics.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar as configurações de diagnóstico para a conta DataLakeAnalytics.|
|/accounts/providers/Microsoft.Insights/logDefinitions/read|Obter os logs disponíveis para a conta DataLakeAnalytics.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Obter as métricas disponíveis para a conta DataLakeAnalytics.|
|/accounts/read|Obter informações sobre uma conta DataLakeAnalytics existente.|
|/accounts/storageAccounts/Containers/listSasTokens/action|Listar tokens SAS para contêineres de armazenamento de uma conta de armazenamento vinculada de uma conta DataLakeAnalytics.|
|/accounts/storageAccounts/Containers/read|Obter contêineres de uma conta de armazenamento vinculada de uma conta DataLakeAnalytics.|
|/accounts/storageAccounts/delete|Desvincular uma conta de armazenamento de uma conta DataLakeAnalytics.|
|/accounts/storageAccounts/read|Obter informações sobre uma conta de armazenamento vinculada de uma conta DataLakeAnalytics.|
|/accounts/storageAccounts/write|Criar ou atualizar uma conta de armazenamento vinculada de uma conta DataLakeAnalytics.|
|/accounts/TakeOwnership/action|Conceder permissões para cancelar trabalhos enviados por outros usuários.|
|/accounts/write|Criar ou atualizar uma conta DataLakeAnalytics.|
|/locations/capability/read|Obter informações de capacidade de uma assinatura sobre o uso de DataLakeAnalytics.|
|/locations/checkNameAvailability/action|Verificar a disponibilidade de um nome de conta DataLakeAnalytics.|
|/locations/operationResults/read|Obter o resultado de uma operação de conta DataLakeAnalytics.|
|/operations/read|Obter operações disponíveis do DataLakeAnalytics.|
|/register/action|Registrar a assinatura no DataLakeAnalytics.|

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

| Operação | DESCRIÇÃO |
|---|---|
|/accounts/delete|Excluir uma conta DataLakeStore.|
|/accounts/enableKeyVault/action|Habilitar o Key Vault para uma conta DataLakeStore.|
|/accounts/firewallRules/delete|Excluir uma regra de firewall.|
|/accounts/firewallRules/read|Obter informações sobre uma regra de firewall.|
|/accounts/firewallRules/write|Criar ou atualizar uma regra de firewall.|
|/accounts/operationResults/read|Obter o resultado de uma operação de conta DataLakeStore.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Obter as configurações de diagnóstico para a conta DataLakeStore.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar as configurações de diagnóstico para a conta DataLakeStore.|
|/accounts/providers/Microsoft.Insights/logDefinitions/read|Obter os logs disponíveis para a conta DataLakeStore.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Obter as métricas disponíveis para a conta DataLakeStore.|
|/accounts/read|Obter informações sobre uma conta DataLakeStore existente.|
|/accounts/Superuser/action|Conceder Superusuário no Data Lake Store quando concedido com o Microsoft.Authorization/roleAssignments/write.|
|/accounts/trustedIdProviders/delete|Excluir um provedor de identidade confiável.|
|/accounts/trustedIdProviders/read|Obter informações sobre um provedor de identidade confiável.|
|/accounts/trustedIdProviders/write|Criar ou atualizar um provedor de identidade confiável.|
|/accounts/write|Criar ou atualizar uma conta DataLakeStore.|
|/locations/capability/read|Obter informações de capacidade de uma assinatura sobre o uso do DataLakeStore.|
|/locations/checkNameAvailability/action|Verificar a disponibilidade de um nome de conta DataLakeStore.|
|/locations/operationResults/read|Obter o resultado de uma operação de conta DataLakeStore.|
|/operations/read|Obter operações disponíveis de DataLakeStore.|
|/register/action|Registrar assinatura para DataLakeStore.|

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

| Operação | DESCRIÇÃO |
|---|---|
|/locations/performanceTiers/read|Retornar a lista de Níveis de Desempenho disponíveis.|
|/performanceTiers/read|Retornar a lista de Níveis de Desempenho disponíveis.|
|/servers/delete|Excluir um servidor existente.|
|/servers/firewallRules/delete|Excluir uma regra de firewall existente.|
|/servers/firewallRules/read|Retornar a lista de regras de firewall para um servidor ou obter as propriedades para a regra de firewall especificada.|
|/servers/firewallRules/write|Criar uma regra de firewall com os parâmetros especificados ou atualizar uma regra existente.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|Obter a configuração de diagnóstico para o recurso|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o recurso|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Retornar tipos de métricas que estão disponíveis para bancos de dados|
|/servers/read|Retornar a lista de servidores ou obter as propriedades para o servidor especificado.|
|/servers/recoverableServers/read|Retornar as informações recuperáveis do MySQL Server|
|/servers/virtualNetworkRules/delete|Excluir uma regra de rede virtual existente|
|/servers/virtualNetworkRules/read|Retornar a lista de regras de rede virtual ou obter as propriedades da regra de rede virtual especificada.|
|/servers/virtualNetworkRules/write|Criar uma regra de rede virtual com os parâmetros especificados ou atualizar as propriedades ou marcas para a regra de rede virtual especificada.|
|/servers/write|Criar um servidor com os parâmetros especificados ou atualizar as propriedades ou marcas para o servidor especificado.|

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

| Operação | DESCRIÇÃO |
|---|---|
|/locations/performanceTiers/read|Retornar a lista de Níveis de Desempenho disponíveis.|
|/performanceTiers/read|Retornar a lista de Níveis de Desempenho disponíveis.|
|/servers/delete|Excluir um servidor existente.|
|/servers/firewallRules/delete|Excluir uma regra de firewall existente.|
|/servers/firewallRules/read|Retornar a lista de regras de firewall para um servidor ou obter as propriedades para a regra de firewall especificada.|
|/servers/firewallRules/write|Criar uma regra de firewall com os parâmetros especificados ou atualizar uma regra existente.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|Obter a configuração de diagnóstico para o recurso|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o recurso|
|/servers/providers/Microsoft.Insights/logDefinitions/read|Retornar tipos de logs que estão disponíveis para bancos de dados|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Retornar tipos de métricas que estão disponíveis para bancos de dados|
|/servers/read|Retornar a lista de servidores ou obter as propriedades para o servidor especificado.|
|/servers/recoverableServers/read|Retornar as informações recuperáveis do PostgreSQL Server|
|/servers/virtualNetworkRules/delete|Excluir uma regra de rede virtual existente|
|/servers/virtualNetworkRules/read|Retornar a lista de regras de rede virtual ou obter as propriedades da regra de rede virtual especificada.|
|/servers/virtualNetworkRules/write|Criar uma regra de rede virtual com os parâmetros especificados ou atualizar as propriedades ou marcas para a regra de rede virtual especificada.|
|/servers/write|Criar um servidor com os parâmetros especificados ou atualizar as propriedades ou marcas para o servidor especificado.|

## <a name="microsoftdevices"></a>Microsoft.Devices

| Operação | DESCRIÇÃO |
|---|---|
|/checkNameAvailability/Action|Verificar se o nome do IotHub está disponível|
|/checkProvisioningServiceNameAvailability/Action|Verificar se o nome do IotHub está disponível|
|/ElasticPools/diagnosticSettings/read|Obter a configuração de diagnóstico para o recurso|
|/ElasticPools/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o recurso|
|/elasticPools/iotHubTenants/Delete|Excluir o recurso de locatário IotHub|
|/ElasticPools/IotHubTenants/diagnosticSettings/read|Obter a configuração de diagnóstico para o recurso|
|/ElasticPools/IotHubTenants/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o recurso|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete|Excluir grupo de consumidores EventHub|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read|Obter grupo de consumidores EventHub|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write|Criar grupo de consumidores EventHub|
|/elasticPools/iotHubTenants/exportDevices/Action|Exportar dispositivos|
|/elasticPools/iotHubTenants/getStats/Read|Obter o recurso de estatísticas do locatário IotHub|
|/elasticPools/iotHubTenants/importDevices/Action|Importar dispositivos|
|/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action|Obter a chave de locatário IotHub|
|/elasticPools/iotHubTenants/jobs/Read|Obter detalhes de trabalhos enviados em determinado IotHub|
|/elasticPools/iotHubTenants/listKeys/Action|Obter chaves de locatário IotHub|
|/ElasticPools/IotHubTenants/logDefinitions/read|Obter as definições de log disponíveis para o serviço IotHub|
|/ElasticPools/IotHubTenants/metricDefinitions/read|Obter as métricas disponíveis para o serviço IotHub|
|/elasticPools/iotHubTenants/quotaMetrics/Read|Obter métrica de cota|
|/elasticPools/iotHubTenants/Read|Obter o recurso de locatário IotHub|
|/elasticPools/iotHubTenants/routing/routes/$testall/Action|Testar uma mensagem com todas as rotas existentes|
|/elasticPools/iotHubTenants/routing/routes/$testnew/Action|Testar uma mensagem em uma rota de teste fornecida|
|/elasticPools/iotHubTenants/routingEndpointsHealth/Read|Obter a integridade de todos os pontos de extremidade de roteamento para um IotHub|
|/elasticPools/iotHubTenants/Write|Criar ou atualizar o recurso de locatário IotHub|
|/ElasticPools/metricDefinitions/read|Obter as métricas disponíveis para o serviço IotHub|
|/iotHubs/certificates/generateVerificationCode/Action|Gerar código de verificação|
|/iotHubs/certificates/verify/Action|Verificar recurso de Certificado|
|/iotHubs/Delete|Excluir recurso IotHub|
|/IotHubs/diagnosticSettings/read|Obter a configuração de diagnóstico para o recurso|
|/IotHubs/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o recurso|
|/iotHubs/eventGridFilters/Delete|Excluir o filtro de Grade de Eventos|
|/iotHubs/eventGridFilters/Read|Obter o filtro de Grade de Eventos|
|/iotHubs/eventGridFilters/Write|Criar novo ou atualizar filtro de Grade de Eventos existente|
|/iotHubs/eventHubEndpoints/consumerGroups/Delete|Excluir grupo de consumidores EventHub|
|/iotHubs/eventHubEndpoints/consumerGroups/Read|Obter grupo de consumidores EventHub|
|/iotHubs/eventHubEndpoints/consumerGroups/Write|Criar grupo de consumidores EventHub|
|/iotHubs/exportDevices/Action|Exportar dispositivos|
|/iotHubs/importDevices/Action|Importar dispositivos|
|/iotHubs/iotHubKeys/listkeys/Action|Obter a chave IotHub para o nome fornecido|
|/iotHubs/iotHubStats/Read|Obter estatísticas de IotHub|
|/iotHubs/jobs/Read|Obter detalhes de trabalhos enviados em determinado IotHub|
|/iotHubs/listkeys/Action|Obter todas as chaves IotHub|
|/IotHubs/logDefinitions/read|Obter as definições de log disponíveis para o serviço IotHub|
|/IotHubs/metricDefinitions/read|Obter as métricas disponíveis para o serviço IotHub|
|/iotHubs/quotaMetrics/Read|Obter métrica de cota|
|/iotHubs/Read|Obter os recursos IotHub|
|/iotHubs/routing/$testall/Action|Testar uma mensagem com todas as rotas existentes|
|/iotHubs/routing/$testnew/Action|Testar uma mensagem em uma rota de teste fornecida|
|/iotHubs/routingEndpointsHealth/Read|Obter a integridade de todos os pontos de extremidade de roteamento para um IotHub|
|/iotHubs/skus/Read|Obter SKUs IotHub válidos|
|/iotHubs/Write|Criar ou atualizar recursos IotHub|
|/operations/Read|Obter todas as operações de ResourceProvider|
|/provisioningServices/certificates/generateVerificationCode/Action|Gerar código de verificação|
|/provisioningServices/certificates/verify/Action|Verificar recurso de Certificado|
|/provisioningServices/Delete|Excluir recurso IotDps|
|/provisioningServices/diagnosticSettings/read|Obter a configuração de diagnóstico para o recurso|
|/provisioningServices/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o recurso|
|/provisioningServices/listkeys/Action|Obter todas as chaves IotDps|
|/provisioningServices/logDefinitions/read|Obter as definições de log disponíveis para o Serviço de provisionamento|
|/provisioningServices/metricDefinitions/read|Obter as métricas disponíveis para o serviço de provisionamento|
|/provisioningServices/ProvisioningServiceKeys/listkeys/Action|Obter Chaves IotDps para nome da chave|
|/provisioningServices/Read|Obter recurso IotDps|
|/provisioningServices/skus/Read|Obter SKUs IotDps válido|
|/provisioningServices/Write|Criar recurso IotDps|
|/register/action|Registrar a assinatura do provedor de recursos IotHub e permitir a criação de recursos IotHub|
|/register/action|Registrar a assinatura do provedor de recursos IotHub e permitir a criação de recursos IotHub|
|/usages/Read|Obter detalhes de uso de assinatura para esse provedor.|

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

| Operação | DESCRIÇÃO |
|---|---|
|/labCenters/delete|Excluir centrais do laboratório.|
|/labCenters/read|Ler centrais do laboratório.|
|/labCenters/write|Adicionar ou modificar as centrais do laboratório.|
|/labs/artifactSources/armTemplates/read|Ler modelos do Azure Resource Manager.|
|/labs/artifactSources/artifacts/GenerateArmTemplate/action|Gera um modelo do ARM para o artefato determinado, carrega os arquivos necessários para uma conta de armazenamento e valida o artefato gerado.|
|/labs/artifactSources/artifacts/read|Ler artefatos.|
|/labs/artifactSources/delete|Excluir fontes de artefato.|
|/labs/artifactSources/read|Ler fontes de artefato.|
|/labs/artifactSources/write|Adicionar ou modificar fontes de artefato.|
|/labs/ClaimAnyVm/action|Declarar uma máquina virtual aleatória declarável no laboratório.|
|/labs/costs/read|Ler custos.|
|/labs/costs/write|Adicionar ou modificar custos.|
|/labs/CreateEnvironment/action|Criar máquinas virtuais em um laboratório.|
|/labs/customImages/delete|Excluir imagens personalizadas.|
|/labs/customImages/read|Ler imagens personalizadas.|
|/labs/customImages/write|Adicionar ou modificar imagens personalizadas.|
|/labs/delete|Excluir laboratórios.|
|/labs/ExportResourceUsage/action|Exportar o uso de recursos de laboratório para uma conta de armazenamento|
|/labs/formulas/delete|Excluir fórmulas.|
|/labs/formulas/read|Ler fórmulas.|
|/labs/formulas/write|Adicionar ou modificar fórmulas.|
|/labs/galleryImages/read|Ler imagens da galeria.|
|/labs/GenerateUploadUri/action|Gerar um URI para carregar imagens de disco personalizadas para um laboratório.|
|/labs/ImportVirtualMachine/action|Importar uma máquina virtual para um laboratório diferente.|
|/labs/ListVhds/action|Listar imagens de disco disponíveis para a criação de imagens personalizadas.|
|/labs/notificationChannels/delete|Excluir notificationchannels.|
|/labs/notificationChannels/Notify/action|Enviar notificação ao canal fornecido.|
|/labs/notificationChannels/read|Ler notificationchannels.|
|/labs/notificationChannels/write|Adicionar ou modificar notificationchannels.|
|/labs/policySets/EvaluatePolicies/action|Avaliar a política de laboratório.|
|/labs/policySets/policies/delete|Excluir políticas.|
|/labs/policySets/policies/read|Ler políticas.|
|/labs/policySets/policies/write|Adicionar ou modificar políticas.|
|/labs/read|Ler laboratórios.|
|/labs/schedules/delete|Excluir agendas.|
|/labs/schedules/Execute/action|Executar uma agenda.|
|/labs/schedules/ListApplicable/action|Listar todas as agendas aplicáveis|
|/labs/schedules/read|Ler agendas.|
|/labs/schedules/write|Adicionar ou modificar agendamentos.|
|/labs/serviceRunners/delete|Excluir executores de serviço.|
|/labs/serviceRunners/read|Ler os executores de serviço.|
|/labs/serviceRunners/write|Adicionar ou modificar os executores de serviço.|
|/labs/users/delete|Excluir perfis de usuário.|
|/labs/users/disks/Attach/action|Anexar e criar a concessão do disco para a máquina virtual.|
|/labs/users/disks/delete|Excluir discos.|
|/labs/users/disks/Detach/action|Desanexar e interromper a concessão do disco anexado à máquina virtual.|
|/labs/users/disks/read|Ler discos.|
|/labs/users/disks/write|Adicionar ou modificar discos.|
|/labs/users/environments/delete|Excluir ambientes.|
|/labs/users/environments/read|Ler ambientes.|
|/labs/users/environments/write|Adicionar ou modificar ambientes.|
|/labs/users/read|Ler perfis de usuário.|
|/labs/users/secrets/delete|Excluir segredos.|
|/labs/users/secrets/read|Ler segredos.|
|/labs/users/secrets/write|Adicionar ou modificar segredos.|
|/labs/users/serviceFabrics/delete|Excluir malhas de serviço.|
|/labs/users/serviceFabrics/ListApplicableSchedules/action|Listar todas as agendas aplicáveis|
|/labs/users/serviceFabrics/read|Ler malhas de serviço.|
|/labs/users/serviceFabrics/schedules/delete|Excluir agendas.|
|/labs/users/serviceFabrics/schedules/Execute/action|Executar uma agenda.|
|/labs/users/serviceFabrics/schedules/read|Ler agendas.|
|/labs/users/serviceFabrics/schedules/write|Adicionar ou modificar agendamentos.|
|/labs/users/serviceFabrics/Start/action|Iniciar uma malha de serviço.|
|/labs/users/serviceFabrics/Stop/action|Parar uma malha de serviço|
|/labs/users/serviceFabrics/write|Adicionar ou modificar malhas de serviço.|
|/labs/users/write|Adicionar ou modificar perfis de usuário.|
|/labs/virtualMachines/AddDataDisk/action|Anexar um disco de dados novo ou existente à máquina virtual.|
|/labs/virtualMachines/ApplyArtifacts/action|Aplicar artefatos à máquina virtual.|
|/labs/virtualMachines/Claim/action|Assumir o controle de uma máquina virtual existente|
|/labs/virtualMachines/delete|Excluir as máquinas virtuais.|
|/labs/virtualMachines/DetachDataDisk/action|Desanexar o disco especificado da máquina virtual.|
|/labs/virtualMachines/ListApplicableSchedules/action|Listar todas as agendas aplicáveis|
|/labs/virtualMachines/read|Ler máquinas virtuais.|
|/labs/virtualMachines/Restart/action|Reiniciar uma máquina virtual.|
|/labs/virtualMachines/schedules/delete|Excluir agendas.|
|/labs/virtualMachines/schedules/Execute/action|Executar uma agenda.|
|/labs/virtualMachines/schedules/read|Ler agendas.|
|/labs/virtualMachines/schedules/write|Adicionar ou modificar agendamentos.|
|/labs/virtualMachines/Start/action|Iniciar uma máquina virtual.|
|/labs/virtualMachines/Stop/action|Parar uma máquina virtual|
|/labs/virtualMachines/TransferDisks/action|Transferir propriedade de discos de dados de máquina virtual para si mesmo|
|/labs/virtualMachines/UnClaim/action|Liberar propriedade de uma máquina virtual existente|
|/labs/virtualMachines/write|Adicionar ou modificar máquinas virtuais.|
|/labs/virtualNetworks/delete|Excluir redes virtuais.|
|/labs/virtualNetworks/read|Ler redes virtuais.|
|/labs/virtualNetworks/write|Adicionar ou modificar redes virtuais.|
|/labs/write|Adicionar ou modificar os laboratórios.|
|/locations/operations/read|Operações de leitura.|
|/register/action|Registra a assinatura|
|/schedules/delete|Excluir agendas.|
|/schedules/Execute/action|Executar uma agenda.|
|/schedules/read|Ler agendas.|
|/schedules/Retarget/action|Atualizar ID de recurso de destino de uma agenda.|
|/schedules/write|Adicionar ou modificar agendamentos.|

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

| Operação | DESCRIÇÃO |
|---|---|
|/databaseAccountNames/read|Verificar a disponibilidade do nome.|
|/databaseAccounts/changeResourceGroup/action|Alterar o grupo de recursos de uma conta de banco de dados|
|/databaseAccounts/databases/collections/metricDefinitions/read|Ler a coleção de definições de métrica.|
|/databaseAccounts/databases/collections/metrics/read|Ler a métrica da coleção.|
|/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read|Ler métricas de nível de chave de partição da conta do banco de dados|
|/databaseAccounts/databases/collections/partitions/metrics/read|Ler métricas de nível de partição da conta do banco de dados|
|/databaseAccounts/databases/collections/partitions/usages/read|Ler usos do nível de partição da conta do banco de dados|
|/databaseAccounts/databases/collections/usages/read|Ler os usos da coleção.|
|/databaseAccounts/databases/metricDefinitions/read|Ler as definições de métrica do banco de dados|
|/databaseAccounts/databases/metrics/read|Ler a métrica do banco de dados.|
|/databaseAccounts/databases/usages/read|Ler os usos do banco de dados.|
|/databaseAccounts/delete|Excluir as contas de banco de dados.|
|/databaseAccounts/failoverPriorityChange/action|Alterar as prioridades de failover das regiões de uma conta de banco de dados. Isso é usado para executar a operação de failover manual|
|/databaseAccounts/listConnectionStrings/action|Obter as cadeias de caracteres de conexão para uma conta de banco de dados|
|/databaseAccounts/listKeys/action|Listar chaves de uma conta de banco de dados|
|/databaseAccounts/metricDefinitions/read|Ler o banco de dados de definições de métrica da conta.|
|/databaseAccounts/metrics/read|Ler a métrica da conta do banco de dados.|
|/databaseAccounts/operationResults/read|Ler o status da operação assíncrona|
|/databaseAccounts/percentile/metrics/read|Ler métricas de latência|
|/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read|Ler métricas de latência para uma região específica de origem e destino|
|/databaseAccounts/percentile/targetRegion/metrics/read|Ler métricas de latência para uma região de destino específica|
|/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/read|Obter a configuração de diagnóstico para o recurso|
|/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o recurso|
|/databaseAccounts/providers/Microsoft.Insights/logDefinitions/read|Obter as categorias de log disponíveis para a conta do banco de dados|
|/databaseAccounts/providers/Microsoft.Insights/metricDefinitions/read|Obter as métricas disponíveis para a conta do banco de dados|
|/databaseAccounts/read|Ler uma conta de banco de dados.|
|/databaseAccounts/readonlykeys/action|Ler as chaves somente leitura da conta do banco de dados.|
|/databaseAccounts/readonlykeys/read|Ler as chaves somente leitura da conta do banco de dados.|
|/databaseAccounts/regenerateKey/action|Girar chaves de uma conta de banco de dados|
|/databaseAccounts/region/databases/collections/metrics/read|Ler as métricas de coleção regional.|
|/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read|Ler as métricas de nível de chave de partição da conta do banco de dados regional|
|/databaseAccounts/region/databases/collections/partitions/metrics/read|Lear as métricas de nível de partição da conta do banco de dados regional|
|/databaseAccounts/region/databases/collections/partitions/read|Ler as partições da conta do banco de dados em uma coleção|
|/databaseAccounts/region/metrics/read|Ler a região e as métricas da conta do banco de dados.|
|/databaseAccounts/usages/read|Ler os usos da conta do banco de dados.|
|/databaseAccounts/write|Atualizar uma conta de banco de dados.|
|/locations/deleteVirtualNetworkOrSubnets/action|Notificar o Microsoft.DocumentDB que a VirtualNetwork ou Sub-rede está sendo excluída|
|/operationResults/read|Ler o status da operação assíncrona|
|/operations/read|Operações de leitura disponíveis para o Microsoft DocumentDB |
|/register/action| Registrar o provedor de recursos do Microsoft DocumentDB para a assinatura|

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

| Operação | DESCRIÇÃO |
|---|---|
|/checkDomainAvailability/Action|Verificar se um domínio está disponível para compra|
|/domains/Delete|Excluir um modo de exibição existente.|
|/domains/domainownershipidentifiers/Delete|Excluir o identificador de propriedade|
|/domains/domainownershipidentifiers/Read|Listar identificadores de propriedade|
|/domains/domainownershipidentifiers/Read|Obter identificador de propriedade|
|/domains/domainownershipidentifiers/Write|Criar ou atualizar identificador|
|/domains/operationresults/Read|Obter uma operação de domínio|
|/domains/operations/Read|Listar todas as operações do registro de domínio do serviço de aplicativo|
|/domains/Read|Obter a lista de domínios|
|/domains/Read|Obter domínio|
|/domains/renew/Action|Renovar um domínio existente.|
|/domains/Write|Adicionar um novo domínio ou atualizar um existente|
|/generateSsoRequest/Action|Gerar uma solicitação para entrar no centro de controle de domínio.|
|/listDomainRecommendations/Action|Recuperar as recomendações de domínio da lista com base em palavras-chave|
|/register/action|Registrar o provedor de recursos Microsoft Domains para a assinatura|
|/topLevelDomains/listAgreements/Action|Listar ação de Contrato|
|/topLevelDomains/Read|Obter domínios toplevel|
|/topLevelDomains/Read|Obter domínio toplevel|
|/validateDomainRegistrationInformation/Action|Validar o objeto de compra de domínio sem enviá-lo|

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

| Operação | DESCRIÇÃO |
|---|---|
|/lcsprojects/clouddeployments/read|Exibir as implantações de avaliação do Microsoft Dynamics AX 2012 R3 em um projeto dos Serviços de Ciclo de Vida do Microsoft Dynamics que pertencem a um usuário|
|/lcsprojects/clouddeployments/write|Criar as implantações de avaliação do Microsoft Dynamics AX 2012 R3 em um projeto dos Serviços de Ciclo de Vida do Microsoft Dynamics que pertencem a um usuário. As implantações podem ser gerenciadas no portal de gerenciamento do Azure|
|/lcsprojects/connectors/read|Ler os conectores que pertencem a um projeto dos Serviços de Ciclo de Vida do Microsoft Dynamics|
|/lcsprojects/connectors/write|Criar e atualizar os conectores que pertencem a um projeto dos Serviços de Ciclo de Vida do Microsoft Dynamics|
|/lcsprojects/delete|Excluir projetos dos Serviços de Ciclo de Vida do Microsoft Dynamics que pertencem ao usuário|
|/lcsprojects/read|Exibir projetos dos Serviços de Ciclo de Vida do Microsoft Dynamics que pertencem a um usuário|
|/lcsprojects/write|Criar e atualizar projetos dos Serviços de Ciclo de Vida do Microsoft Dynamics que pertencem ao usuário. Somente as propriedades de nome e a descrição podem ser atualizadas. A assinatura e o local associados ao projeto não podem ser atualizados após a criação|

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

| Operação | DESCRIÇÃO |
|---|---|
|/eventSubscriptions/delete|Excluir um eventSubscription|
|/eventSubscriptions/getFullUrl/action|Obter a URL completa para a assinatura de evento|
|/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read|Obter a configuração de diagnóstico para assinaturas de eventos|
|/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para assinaturas de eventos|
|/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read|Obtém as métricas disponíveis para eventSubscriptions|
|/eventSubscriptions/read|Ler um eventSubscription|
|/eventSubscriptions/write|Criar ou atualizar um eventSubscription|
|/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read|Obter a configuração de diagnóstico para tópicos|
|/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para tópicos|
|/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read|Obter as métricas disponíveis para tópicos|
|/register/action|Registrar o eventSubscription para o provedor de recursos do EventGrid e permitir a criação de assinaturas da Grade de Eventos.|
|/topics/delete|Excluir um tópico|
|/topics/listKeys/action|Listar chaves para o tópico|
|/topics/providers/Microsoft.Insights/diagnosticSettings/read|Obter a configuração de diagnóstico para tópicos|
|/topics/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para tópicos|
|/topics/providers/Microsoft.Insights/metricDefinitions/read|Obter as métricas disponíveis para tópicos|
|/topics/read|Ler um tópico|
|/topics/regenerateKey/action|Regenerar chave para o tópico|
|/topics/write|Criar ou atualizar um tópico|

## <a name="microsofteventhub"></a>Microsoft.EventHub

| Operação | DESCRIÇÃO |
|---|---|
|/checkNameAvailability/action|Verificar a disponibilidade do namespace em determinada assinatura.|
|/checkNamespaceAvailability/action|Verificar a disponibilidade do namespace em determinada assinatura. Essa API está preterida, use CheckNameAvailabiltiy.|
|/namespaces/authorizationRules/action|Atualizar regra de autorização de namespace. Essa API está preterida. Use uma chamada PUT para atualizar a regra de autorização de namespace. Esta operação não tem suporte na versão da API 2017-04-01.|
|/namespaces/authorizationRules/delete|Excluir regra de autorização de namespace. A regra de autorização do namespace padrão não pode ser excluída. |
|/namespaces/authorizationRules/listkeys/action|Obter a cadeia de conexão para o namespace|
|/namespaces/authorizationRules/read|Obter a lista de descrição de regras de autorização de namespaces.|
|/namespaces/authorizationRules/regenerateKeys/action|Regenerar a chave primária ou secundária para o recurso|
|/namespaces/authorizationRules/write|Criar regras de autorização no nível do namespace e atualizar suas propriedades. Os direitos de acesso das regras de autorização; as chaves primárias e secundárias podem ser atualizadas.|
|/namespaces/Delete|Excluir o recurso de namespace|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|Obter as chaves de regras de autorização para o namespace primário de Recuperação de Desastre|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|Obter regras de autorização do namespace primário de recuperação de desastre|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|Desabilitar a Recuperação de desastre e interromper a replicação de alterações de namespaces primários para secundários.|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|Verificar a disponibilidade de alias de namespace sob determinada assinatura.|
|/namespaces/disasterRecoveryConfigs/delete|Excluir a configuração da recuperação de desastre associada ao namespace. Essa operação somente pode ser invocada por meio do namespace primário.|
|/namespaces/disasterRecoveryConfigs/failover/action|Invocar um failover de GEO DR e reconfigurar o alias de namespace para apontar para o namespace secundário.|
|/namespaces/disasterRecoveryConfigs/read|Obter a configuração da Recuperação de Desastre associada ao namespace.|
|/namespaces/disasterRecoveryConfigs/write|Criar ou atualizar a configuração de Recuperação de Desastre associada ao namespace.|
|/namespaces/eventhubs/authorizationRules/action|Operação para atualizar EventHub. Esta operação não tem suporte na versão da API 2017-04-01. Regras de autorização. Use uma chamada PUT para atualizar a Regra de Autorização.|
|/namespaces/eventhubs/authorizationRules/delete|Operação para excluir regras de autorização EventHub|
|/namespaces/eventhubs/authorizationRules/listkeys/action|Obter a cadeia de conexão para EventHub|
|/namespaces/eventhubs/authorizationRules/read| Obter a lista de regras de autorização EventHub|
|/namespaces/eventhubs/authorizationRules/regenerateKeys/action|Regenerar a chave primária ou secundária para o recurso|
|/namespaces/eventhubs/authorizationRules/write|Criar regras de autorização EventHub e atualizar suas propriedades. Os Direitos de Acesso das Regras de Autorização podem ser atualizados.|
|/namespaces/eventHubs/consumergroups/Delete|Operação para excluir o recurso ConsumerGroup|
|/namespaces/eventHubs/consumergroups/read|Obter lista de descrições de recursos ConsumerGroup|
|/namespaces/eventHubs/consumergroups/write|Criar ou atualizar propriedades ConsumerGroup.|
|/namespaces/eventhubs/Delete|Operação para excluir o recurso EventHub|
|/namespaces/eventhubs/read|Obter lista de descrições de recursos de EventHub|
|/namespaces/eventhubs/write|Criar ou atualizar propriedades EventHub.|
|/namespaces/messagingPlan/read|Obter o Plano de Mensagens para um namespace. Essa API está preterida. As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso Namespace (pai) nas versões posteriores da API. Esta operação não tem suporte na versão da API 2017-04-01.|
|/namespaces/messagingPlan/write|Atualizar o Plano de Mensagens para um namespace. Essa API está preterida. As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso Namespace (pai) nas versões posteriores da API. Esta operação não tem suporte na versão da API 2017-04-01.|
|/namespaces/operationresults/read|Obter o status da operação de Namespace|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/read|Obter lista de descrições de recurso de configurações de diagnóstico do namespace|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/write|Obter lista de descrições de recurso de configurações de diagnóstico do namespace|
|/namespaces/providers/Microsoft.Insights/logDefinitions/read|Obter lista de descrições do recurso de log do namespace|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|Obter lista de métrica de descrições de recurso de métrica do namespace|
|/namespaces/read|Obter a lista de descrição do recurso de namespace|
|/namespaces/write|Criar um recurso de namespace e atualizar suas propriedades. Marcas e Capacidade do namespace são as propriedades que podem ser atualizadas.|
|/operations/read|Obter Operações|
|/register/action|Registrar a assinatura do provedor de recursos EventHub e permitir a criação de recursos EventHub|
|/sku/read|Obter lista de Descrições de Recursos de SKU|
|/sku/regions/read|Obter lista de Descrições de Recursos SkuRegions|
|/unregister/action|Registrar o Provedor de Recursos EventHub|

## <a name="microsoftfeatures"></a>Microsoft.Features

| Operação | DESCRIÇÃO |
|---|---|
|/features/read|Obter os recursos de uma assinatura.|
|/providers/features/read|Obter o recurso de uma assinatura em determinado provedor de recursos.|
|/providers/features/register/action|Registrar o recurso de uma assinatura em determinado provedor de recursos.|
|/providers/features/unregister/action|Cancelar o registro do recurso de uma assinatura em um determinado provedor de recursos.|

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

| Operação | DESCRIÇÃO |
|---|---|
|/clusters/changerdpsetting/action|Alterar configuração de RDP para Cluster HDInsight|
|/clusters/configurations/action|Atualizar a configuração do Cluster HDInsight|
|/clusters/configurations/read|Obter configurações de Cluster HDInsight|
|/clusters/delete|Excluir um cluster HDInsight|
|/clusters/providers/Microsoft.Insights/diagnosticSettings/read|Obter a configuração de diagnóstico para o recurso do Cluster HDInsight|
|/clusters/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o recurso do Cluster HDInsight|
|/clusters/providers/Microsoft.Insights/metricDefinitions/read|Obter as métricas disponíveis para o Cluster HDInsight|
|/clusters/read|Obter detalhes sobre o Cluster HDInsight|
|/clusters/roles/resize/action|Redimensionar um cluster HDInsight|
|/clusters/write|Criar ou atualizar o Cluster HDInsight|
|/locations/capabilities/read|Obter recursos de assinatura|
|/locations/checkNameAvailability/read|Verificar disponibilidade do nome|

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

| Operação | DESCRIÇÃO |
|---|---|
|/jobs/delete|Excluir um trabalho existente.|
|/jobs/listBitLockerKeys/action|Obter as chaves do BitLocker para o trabalho especificado.|
|/jobs/read|Obter as propriedades para o trabalho especificado ou retornar a lista de trabalhos.|
|/jobs/write|Criar um trabalho com os parâmetros especificados ou atualizar as propriedades ou marcações do trabalho especificado.|
|/locations/read|Obter as propriedades do local especificado ou retornar a lista de locais.|
|/register/action|Registra a assinatura do provedor de recursos importar/exportar e habilita a criação de trabalhos de importação/exportação.|

## <a name="microsoftinsights"></a>Microsoft.insights

| Operação | DESCRIÇÃO |
|---|---|
|/ActionGroups/Delete|Excluindo um grupo de ação|
|/ActionGroups/Read|Lendo um grupo de ação|
|/ActionGroups/Write|Gravando um grupo de ação|
|/ActivityLogAlerts/Activated/Action|Disparou o alerta de log de atividade|
|/ActivityLogAlerts/Delete|Excluindo um alerta do log de atividades|
|/ActivityLogAlerts/Read|Lendo um alerta do log de atividades|
|/ActivityLogAlerts/Write|Lendo um alerta do log de atividades|
|/AlertRules/Activated/Action|Regra de alerta ativada|
|/AlertRules/Delete|Excluir uma configuração de regra de alerta|
|/AlertRules/Incidents/Read|Ler uma configuração incidente da regra de alerta|
|/AlertRules/Read|Ler uma configuração de regra de alerta|
|/AlertRules/Resolved/Action|Regra de alerta resolvida|
|/AlertRules/Throttled/Action|A regra de alerta está limitada|
|/AlertRules/Write|Gravar em uma configuração de regra de alerta|
|/AutoscaleSettings/Delete|Excluir uma configuração de dimensionamento automático|
|/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read|Ler definições de métrica|
|/AutoscaleSettings/Read|Ler uma configuração de dimensionamento automático|
|/AutoscaleSettings/Scaledown/Action|Dimensionar operação de redução vertical automaticamente|
|/AutoscaleSettings/Scaleup/Action|Dimensionar operação de escalada vertical automaticamente|
|/AutoscaleSettings/Write|Gravar em uma configuração de dimensionamento automático|
|/Components/AnalyticsItems/Delete|Excluindo um item de análise do Application Insights|
|/Components/AnalyticsItems/Read|Lendo um item de análise do Application Insights|
|/Components/AnalyticsItems/Write|Gravando um item de análise do Application Insights|
|/Components/AnalyticsTables/Action|Ação da tabela de análise do Application Insights|
|/Components/AnalyticsTables/Delete|Excluindo um esquema de tabela de análise do Application Insights|
|/Components/AnalyticsTables/Read|Lendo um esquema de tabela de análise do Application Insights|
|/Components/AnalyticsTables/Write|Gravando um esquema de tabela de análise do Application Insights|
|/Components/Annotations/Delete|Excluindo uma anotação do Application Insights|
|/Components/Annotations/Read|Lendo uma anotação do Application Insights|
|/Components/Annotations/Write|Gravação de anotação do Application Insights|
|/Components/Api/Read|Lendo a API de dados do componente do Application Insights|
|/Components/ApiKeys/Action|Gerando uma chave de API do Application Insights|
|/Components/ApiKeys/Delete|Excluindo uma chave de API do Application Insights|
|/Components/ApiKeys/Read|Lendo uma chave de API do Application Insights|
|/Components/BillingPlanForComponent/Read|Lendo um plano de cobrança para componente do Application Insights|
|/Components/CurrentBillingFeatures/Read|Lendo os recursos de cobrança atuais para componente do Application Insights|
|/Components/CurrentBillingFeatures/Write|Gravando recursos de cobrança atuais para componente do Application Insights|
|/Components/DefaultWorkItemConfig/Read|Lendo uma configuração de integração de ALM do Application Insights|
|/Components/Delete|Excluindo uma configuração de componente do Application Insights|
|/Components/ExportConfiguration/Action|Ação de configurações de exportação do Application Insights|
|/Components/ExportConfiguration/Delete|Excluindo as configurações de exportação do Application Insights|
|/Components/ExportConfiguration/Read|Lendo as configurações de exportação do Application Insights|
|/Components/ExportConfiguration/Write|Gravando as configurações de exportação do Application Insights|
|/Components/ExtendQueries/Read|Lendo os resultados da consulta estendida do componente do Application Insights|
|/Components/Favorites/Delete|Excluindo favorito do Application Insights|
|/Components/Favorites/Read|Lendo um favorito do Application Insights|
|/Components/Favorites/Write|Gravando um favorito do Application Insights|
|/Components/FeatureCapabilities/Read|Lendo as capacidades do recurso do componente do Application Insights|
|/Components/GetAvailableBillingFeatures/Read|Lendo os recursos de cobrança disponíveis do componente do Application Insights|
|/Components/GetToken/Read|Lendo um token do componente do Application Insights|
|/Components/ListMigrationDate/Action|Recuperar a data de migração da Assinatura|
|/Components/ListMigrationDate/Read|Recuperar a data de migração da assinatura|
|/Components/MetricDefinitions/Read|Lendo as definições de métrica do componente do Application Insights|
|/Components/Metrics/Read|Lendo as métricas do componente do Application Insights|
|/Components/MigrateToNewpricingModel/Action|Migrar a assinatura para o novo modelo de preços|
|/Components/Move/Action|Mover um componente do Application Insights para outro grupo de recursos ou assinatura|
|/Components/MyAnalyticsItems/Delete|Excluindo um item de análise pessoal do Application Insights|
|/Components/MyAnalyticsItems/Read|Lendo um item de análise pessoal do Application Insights|
|/Components/MyAnalyticsItems/Write|Gravando um item de análise pessoal do Application Insights|
|/Components/MyFavorites/Read|Lendo um favorito pessoal do Application Insights|
|/Components/PricingPlans/Read|Lendo um plano de preços do componente do Application Insights|
|/Components/PricingPlans/Write|Gravando um plano de preços do componente do Application Insights|
|/Components/ProactiveDetectionConfigs/Read|Lendo a configuração de detecção proativa do Application Insights|
|/Components/ProactiveDetectionConfigs/Write|Gravando a configuração de detecção proativa do Application Insights|
|/Components/providers/Microsoft.Insights/MetricDefinitions/Read|Ler definições de métrica|
|/Components/QuotaStatus/Read|Lendo o status de cota do componente do Application Insights|
|/Components/Read|Lendo uma configuração de componente do Application Insights|
|/Components/RollbackToLegacyPricingModel/Action|Reverter a assinatura para o modelo de preços herdado|
|/Components/SyntheticMonitorLocations/Read|Lendo as localizações de teste na Web do Application Insights|
|/Components/WorkItemConfigs/Delete|Excluindo uma configuração de integração de ALM do Application Insights|
|/Components/WorkItemConfigs/Read|Lendo uma configuração de integração de ALM do Application Insights|
|/Components/WorkItemConfigs/Write|Gravando uma configuração de integração de ALM do Application Insights|
|/Components/Write|Gravando em uma configuração de componente do Application Insights|
|/DiagnosticSettings/Delete|Excluir configuração de diagnóstico|
|/DiagnosticSettings/Read|Ler uma configuração de diagnóstico|
|/DiagnosticSettings/Write|Gravar na configuração de diagnóstico|
|/EventCategories/Read|Lendo uma categoria de eventos|
|/eventtypes/digestevents/Read|Ler resumo do tipo de evento de gerenciamento|
|/eventtypes/values/Read|Ler valores do tipo de evento de gerenciamento|
|/ExtendedDiagnosticSettings/Delete|Excluir configuração de diagnóstico estendido|
|/ExtendedDiagnosticSettings/Read|Ler uma configuração de diagnóstico estendido|
|/ExtendedDiagnosticSettings/Write|Gravar em uma configuração de diagnóstico estendido|
|/LogDefinitions/Read|Ler definições de log|
|/LogProfiles/Delete|Excluir configuração de perfis de log|
|/LogProfiles/Read|Ler perfis de log|
|/LogProfiles/Write|Gravar em uma configuração de perfil de log|
|/MetricAlerts/Delete|Excluindo um alerta de métrica|
|/MetricAlerts/Read|Lendo um alerta de métrica|
|/MetricAlerts/Write|Gravando um alerta de métrica|
|/MetricDefinitions/Microsoft.Insights/Read|Ler definições de métrica|
|/MetricDefinitions/providers/Microsoft.Insights/Read|Ler definições de métrica|
|/MetricDefinitions/Read|Ler definições de métrica|
|/Metrics/providers/Metrics/Read|Ler métrica|
|/Metrics/Read|Ler métrica|
|/Metrics/Write|Gravar métricas|
|/Operations/Read|Lendo operações|
|/Register/Action|Registrar o provedor do Microsoft Insights|
|/Tenants/Register/Action|Inicializa o provedor do Microsoft Insights|
|/Unregister/Action|Registrar o provedor do Microsoft Insights|
|/Webtests/Delete|Excluindo uma configuração de teste na Web|
|/Webtests/GetToken/Read|Lendo um token de teste na Web|
|/Webtests/MetricDefinitions/Read|Lendo as definições de métrica do teste na Web|
|/Webtests/Metrics/Read|Lendo as métricas de um teste na Web|
|/Webtests/Read|Lendo uma configuração de teste na Web|
|/Webtests/Write|Gravando em uma configuração de teste na Web|

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

| Operação | DESCRIÇÃO |
|---|---|
|/checkNameAvailability/read|Verificar se um nome de chave do cofre é válido e não está em uso|
|/deletedVaults/read|Exibir as propriedades de cofres de chaves com exclusão reversível|
|/hsmPools/delete|Excluir um pool de HSM|
|/hsmPools/joinVault/action|Ingressar em um cofre de chaves para um pool de HSM|
|/hsmPools/read|Exibir as propriedades de um pool de HSM|
|/hsmPools/write|Criar um novo pool de HSM de atualização das propriedades de um pool de HSM existente|
|/locations/deletedVaults/purge/action|Limpar um cofre de chaves com exclusão reversível|
|/locations/deletedVaults/read|Exibir as propriedades de um cofre de chaves com exclusão reversível|
|/locations/deleteVirtualNetworkOrSubnets/action|Notifica o Microsoft.KeyVault que uma rede virtual ou sub-rede está sendo excluída|
|/locations/operationResults/read|Verificar o resultado de uma operação de longo prazo|
|/operations/read|Lista as operações disponíveis no provedor de recursos Microsoft.KeyVault|
|/register/action|Registrar uma assinatura|
|/unregister/action|Cancela uma assinatura|
|/vaults/accessPolicies/write|Atualizar uma política de acesso existente por mesclagem ou substituição, ou adicionar uma nova política de acesso a um cofre.|
|/vaults/delete|Excluir um cofre de chaves|
|/vaults/deploy/action|Permite acesso aos segredos em um cofre de chaves durante a implantação de recursos do Azure|
|/vaults/providers/Microsoft.Insights/diagnosticSettings/Read|Obter a configuração de diagnóstico para o recurso|
|/vaults/providers/Microsoft.Insights/diagnosticSettings/Write|Criar ou atualizar a configuração de diagnóstico para o recurso|
|/vaults/providers/Microsoft.Insights/logDefinitions/read|Obtém os logs disponíveis para um cofre de chaves|
|/vaults/providers/Microsoft.Insights/metricDefinitions/read|Obtém as métricas disponíveis para um cofre de chaves|
|/vaults/read|Exibir as propriedades de um cofre de chaves|
|/vaults/secrets/read|Exibir as propriedades de um segredo, mas não seu valor|
|/vaults/secrets/write|Criar um novo segredo ou atualizar o valor de um segredo existente|
|/vaults/write|Criar um novo cofre de chaves ou atualizar as propriedades de um cofre de chaves existente|

## <a name="microsoftlabservices"></a>Microsoft.LabServices

| Operação | DESCRIÇÃO |
|---|---|
|/labAccounts/CreateLab/action|Criar um laboratório em uma conta de laboratório.|
|/labAccounts/delete|Excluir contas de laboratório.|
|/labAccounts/labs/delete|Excluir laboratórios.|
|/labAccounts/labs/environmentSettings/delete|Excluir configuração de ambiente.|
|/labAccounts/labs/environmentSettings/environments/delete|Excluir ambientes.|
|/labAccounts/labs/environmentSettings/environments/read|Ler ambientes.|
|/labAccounts/labs/environmentSettings/environments/write|Adicionar ou modificar ambientes.|
|/labAccounts/labs/environmentSettings/Publish/action|Provisionar/desprovisionar recursos necessários para uma configuração de ambiente com base no estado atual da configuração de ambiente/laboratório.|
|/labAccounts/labs/environmentSettings/read|Ler configuração de ambiente.|
|/labAccounts/labs/environmentSettings/write|Adicionar ou modificar configuração de ambiente.|
|/labAccounts/labs/read|Ler laboratórios.|
|/labAccounts/labs/users/delete|Excluir usuários.|
|/labAccounts/labs/users/read|Ler usuários.|
|/labAccounts/labs/users/write|Adicionar ou modificar usuários.|
|/labAccounts/labs/write|Adicionar ou modificar os laboratórios.|
|/labAccounts/read|Ler contas de laboratório.|
|/labAccounts/write|Adicionar ou modificar contas de laboratório.|
|/locations/operations/read|Operações de leitura.|
|/register/action|Registra a assinatura|

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

| Operação | DESCRIÇÃO |
|---|---|
|/accounts/delete|Excluir Conta de Serviços Baseados na Localização.|
|/accounts/listKeys/action|Listar chaves de conta de serviços baseados em localização|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Obter a configuração de diagnóstico para o recurso|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o recurso|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Obter as métricas disponíveis para contas de serviços baseados na localização|
|/accounts/read|Obter conta de serviços baseados na localização.|
|/accounts/regenerateKey/action|Gerar nova chave primária ou secundária da conta de serviços baseados na localização|
|/accounts/write|Criar ou atualizar uma conta de serviços baseados na localização.|
|/register/action|Registrar o provedor|

## <a name="microsoftlogic"></a>Microsoft.Logic

| Operação | DESCRIÇÃO |
|---|---|
|/integrationAccounts/agreements/delete|Excluir o contrato na conta de integração.|
|/integrationAccounts/agreements/listContentCallbackUrl/action|Obtém a URL de retorno de chamada para o conteúdo do contrato na conta de integração.|
|/integrationAccounts/agreements/read|Ler o contrato na conta de integração.|
|/integrationAccounts/agreements/write|Criar ou atualizar o contrato na conta de integração.|
|/integrationAccounts/assemblies/delete|Exclui o assembly na conta de integração.|
|/integrationAccounts/assemblies/listContentCallbackUrl/action|Obtém a URL de retorno de chamada para o conteúdo do assembly na conta de integração.|
|/integrationAccounts/assemblies/read|Ler o assembly na conta de integração.|
|/integrationAccounts/assemblies/write|Criar ou atualizar o assembly na conta de integração.|
|/integrationAccounts/batchConfigurations/delete|Excluir a configuração de lote na conta de integração.|
|/integrationAccounts/batchConfigurations/read|Ler a configuração de lote na conta de integração.|
|/integrationAccounts/batchConfigurations/write|Criar ou atualizar a configuração de lote na conta de integração.|
|/integrationAccounts/certificates/delete|Excluir o certificado na conta de integração.|
|/integrationAccounts/certificates/read|Ler o certificado na conta de integração.|
|/integrationAccounts/certificates/write|Criar ou atualizar o certificado na conta de integração.|
|/integrationAccounts/delete|Excluir a conta de integração.|
|/integrationAccounts/listCallbackUrl/action|Obtém a URL de retorno de chamada para a conta de integração.|
|/integrationAccounts/listKeyVaultKeys/action|Obter as chaves no cofre de chaves.|
|/integrationAccounts/logTrackingEvents/action|Registra os eventos de acompanhamento na conta de integração.|
|/integrationAccounts/maps/delete|Excluir o mapa na conta de integração.|
|/integrationAccounts/maps/listContentCallbackUrl/action|Obtém a URL de retorno de chamada para o conteúdo do mapa na conta de integração.|
|/integrationAccounts/maps/read|Ler o mapa na conta de integração.|
|/integrationAccounts/maps/write|Criar ou atualizar o mapa na conta de integração.|
|/integrationAccounts/partners/delete|Excluir o parceiro na conta de integração.|
|/integrationAccounts/partners/listContentCallbackUrl/action|Obtém a URL de retorno de chamada para o conteúdo do parceiro na conta de integração.|
|/integrationAccounts/partners/read|Ler o parceiro na conta de integração.|
|/integrationAccounts/partners/write|Criar ou atualizar o parceiro na conta de integração.|
|/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read|Ler as definições de log da Conta de Integração.|
|/integrationAccounts/read|Ler a conta de integração.|
|/integrationAccounts/regenerateAccessKey/action|Regenera os segredos da chave de acesso.|
|/integrationAccounts/schemas/delete|Excluir o esquema na conta de integração.|
|/integrationAccounts/schemas/listContentCallbackUrl/action|Obtém a URL de retorno de chamada para o conteúdo do esquema na conta de integração.|
|/integrationAccounts/schemas/read|Ler o esquema na conta de integração.|
|/integrationAccounts/schemas/write|Criar ou atualizar o esquema na conta de integração.|
|/integrationAccounts/sessions/delete|Excluir a sessão na conta de integração.|
|/integrationAccounts/sessions/read|Ler a configuração de lote na conta de integração.|
|/integrationAccounts/sessions/write|Criar ou atualizar a sessão na conta de integração.|
|/integrationAccounts/write|Criar ou atualizar a conta de integração.|
|/locations/workflows/validate/action|Valida o fluxo de trabalho.|
|/operations/read|Obter a operação.|
|/register/action|Registrar o provedor de recursos Microsoft.Logic de uma determinada assinatura.|
|/workflows/accessKeys/delete|Excluir a chave de acesso.|
|/workflows/accessKeys/list/action|Listar os segredos de chave de acesso.|
|/workflows/accessKeys/read|Ler a chave de acesso.|
|/workflows/accessKeys/regenerate/action|Regenera os segredos da chave de acesso.|
|/workflows/accessKeys/write|Criar ou atualizar a chave de acesso.|
|/workflows/delete|Excluir o fluxo de trabalho.|
|/workflows/disable/action|Desabilitar o fluxo de trabalho.|
|/workflows/enable/action|Permitir o fluxo de trabalho.|
|/workflows/listCallbackUrl/action|Obter a URL de retorno de chamada para o fluxo de trabalho.|
|/workflows/listSwagger/action|Obter as definições do Swagger para o fluxo de trabalho.|
|/workflows/move/action|Mover o fluxo de trabalho de sua id de assinatura, grupo de recursos e nome existente para uma id de assinatura, grupo de recursos e/ou nome diferente.|
|/workflows/providers/Microsoft.Insights/diagnosticSettings/read|Ler as configurações de diagnóstico de fluxo de trabalho.|
|/workflows/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico de fluxo de trabalho.|
|/workflows/providers/Microsoft.Insights/logDefinitions/read|Ler as definições de log de fluxo de trabalho.|
|/workflows/providers/Microsoft.Insights/metricDefinitions/read|Ler as definições de métrica de fluxo de trabalho.|
|/workflows/read|Ler o fluxo de trabalho.|
|/workflows/regenerateAccessKey/action|Regenera os segredos da chave de acesso.|
|/workflows/run/action|Iniciar uma execução do fluxo de trabalho.|
|/workflows/runs/actions/listExpressionTraces/action|Obter os rastreamentos de expressão da ação de execução do fluxo de trabalho.|
|/workflows/runs/actions/read|Ler a ação de execução do fluxo de trabalho.|
|/workflows/runs/actions/repetitions/listExpressionTraces/action|Obter os rastreamentos de expressão de repetição da ação de execução do fluxo de trabalho.|
|/workflows/runs/actions/repetitions/read|Ler a repetição da ação de execução do fluxo de trabalho.|
|/workflows/runs/actions/scoperepetitions/read|Ler a repetição do escopo da ação de execução do fluxo de trabalho.|
|/workflows/runs/cancel/action|Cancelar a execução de um fluxo de trabalho.|
|/workflows/runs/operations/read|Ler o status da operação de execução do fluxo de trabalho.|
|/workflows/runs/read|Ler a execução do fluxo de trabalho.|
|/workflows/suspend/action|Suspender o fluxo de trabalho.|
|/workflows/triggers/histories/read|Ler os históricos de gatilho.|
|/workflows/triggers/histories/resubmit/action|Reenviar o gatilho do fluxo de trabalho.|
|/workflows/triggers/listCallbackUrl/action|Obter a URL de retorno de chamada do gatilho.|
|/workflows/triggers/read|Ler o gatilho.|
|/workflows/triggers/reset/action|Reiniciar o gatilho.|
|/workflows/triggers/run/action|Executar o gatilho.|
|/workflows/triggers/setState/action|Definir o estado do gatilho.|
|/workflows/validate/action|Valida o fluxo de trabalho.|
|/workflows/versions/read|Ler a versão do fluxo de trabalho.|
|/workflows/versions/triggers/listCallbackUrl/action|Obter a URL de retorno de chamada do gatilho.|
|/workflows/write|Criar ou atualizar o fluxo de trabalho.|

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

| Operação | DESCRIÇÃO |
|---|---|
|/commitmentPlans/commitmentAssociations/move/action|Mover associações de plano de compromisso de Machine Learning|
|/commitmentPlans/commitmentAssociations/read|Ler associações de plano de compromisso de Machine Learning|
|/commitmentPlans/delete|Excluir plano de compromisso de Machine Learning|
|/commitmentPlans/join/action|Ingressar em um plano de compromisso de Machine Learning|
|/commitmentPlans/read|Ler um plano de compromisso de Machine Learning|
|/commitmentPlans/write|Criar ou atualizar plano de compromisso de Machine Learning|
|/locations/operationresults/read|Obter o resultado de uma operação do Machine Learning|
|/locations/operationsstatus/read|Obter o status de uma operação do Machine Learning em andamento|
|/operations/read|Obter operações do Machine Learning|
|/register/action|Registra a assinatura do provedor de recursos do serviço Web do Machine Learning e permite a criação de serviços Web.|
|/skus/read|Obter SKUs do plano de compromisso do Machine Learning|
|/webServices/action|Criar propriedades do serviço Web regionais para regiões com suporte|
|/webServices/delete|Excluir um serviço Web do Machine Learning|
|/webServices/read|Ler um serviço Web do Machine Learning|
|/webServices/write|Criar ou atualizar serviço Web do Machine Learning|
|/Workspaces/delete|Criar um Espaço de Trabalho do Machine Learning|
|/Workspaces/listworkspacekeys/action|Listar chaves para um Espaço de Trabalho do Machine Learning|
|/Workspaces/read|Ler um Espaço de Trabalho do Machine Learning|
|/Workspaces/resyncstoragekeys/action|Ressincronizar as chaves da conta de armazenamento configurada para um Espaço de Trabalho do Machine Learning|
|/Workspaces/write|Criar ou atualizar Espaço de Trabalho do Machine Learning|

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

| Operação | DESCRIÇÃO |
|---|---|
|/operationalizationClusters/checkUpdate/action|Verificar se há atualizações disponíveis para serviços do sistema para o cluster de operação|
|/operationalizationClusters/delete|Excluir qualquer conta de hospedagem|
|/operationalizationClusters/listKeys/action|Listar as chaves associadas ao cluster de operação|
|/operationalizationClusters/read|Ler qualquer conta de hospedagem|
|/operationalizationClusters/updateSystem/action|Atualizar os serviços do sistema em um cluster de operação|
|/operationalizationClusters/write|Criar ou atualizar qualquer conta de hospedagem|
|/register/action|Registrar a ID de assinatura para o provedor de recursos e habilitar a criação de recursos de computação de aprendizado de máquina|

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

| Operação | DESCRIÇÃO |
|---|---|
|/accounts/delete|Excluir qualquer conta de hospedagem|
|/accounts/read|Ler qualquer conta de hospedagem|
|/accounts/write|Criar ou atualizar qualquer conta de hospedagem|
|/register/action|Registrar a ID de assinatura no provedor de recursos e habilitar a criação de uma conta de hospedagem|

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

| Operação | DESCRIÇÃO |
|---|---|
|/userAssignedIdentities/assign/action|Ação RBAC para atribuir uma identidade atribuída ao usuário existente a um recurso|
|/userAssignedIdentities/delete|Excluir uma identidade atribuída ao usuário existente|
|/userAssignedIdentities/read|Obter uma identidade atribuída ao usuário existente|
|/userAssignedIdentities/write|Criar uma nova identidade atribuída ao usuário ou atualizar as marcas associadas a uma identidade atribuída a um usuário existente|

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

| Operação | DESCRIÇÃO |
|---|---|
|/labAccounts/CreateLab/action|Criar um laboratório em uma conta de laboratório.|
|/labAccounts/delete|Excluir contas de laboratório.|
|/labAccounts/labs/delete|Excluir laboratórios.|
|/labAccounts/labs/environmentSettings/delete|Excluir configuração de ambiente.|
|/labAccounts/labs/environmentSettings/environments/delete|Excluir ambientes.|
|/labAccounts/labs/environmentSettings/environments/read|Ler ambientes.|
|/labAccounts/labs/environmentSettings/environments/write|Adicionar ou modificar ambientes.|
|/labAccounts/labs/environmentSettings/read|Ler configuração de ambiente.|
|/labAccounts/labs/environmentSettings/write|Adicionar ou modificar configuração de ambiente.|
|/labAccounts/labs/labVms/delete|Excluir máquinas virtuais do laboratório.|
|/labAccounts/labs/labVms/read|Ler máquinas virtuais do laboratório.|
|/labAccounts/labs/labVms/write|Adicionar ou modificar máquinas virtuais do laboratório.|
|/labAccounts/labs/read|Ler laboratórios.|
|/labAccounts/labs/write|Adicionar ou modificar os laboratórios.|
|/labAccounts/read|Ler contas de laboratório.|
|/labAccounts/write|Adicionar ou modificar contas de laboratório.|
|/locations/operations/read|Operações de leitura.|
|/register/action|Registra a assinatura|

## <a name="microsoftmanagement"></a>Microsoft.Management

| Operação | DESCRIÇÃO |
|---|---|
|/checkNameAvailability/action|Verificar se o nome do grupo de gerenciamento especificado é válido e exclusivo.|
|/getEntities/action|Listar todas as entidades (Grupos de Gerenciamento, Assinaturas e etc.) para o usuário autenticado.|
|/managementGroups/delete|Excluir um grupo de gerenciamento.|
|/managementGroups/read|Listar grupos de gerenciamento para o usuário autenticado.|
|/managementGroups/subscriptions/delete|Desassociar a assinatura do grupo de gerenciamento.|
|/managementGroups/subscriptions/write|Associar a assinatura existente ao grupo de gerenciamento.|
|/managementGroups/write|Criar ou atualizar um grupo de gerenciamento.|

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

| Operação | DESCRIÇÃO |
|---|---|
|/ClassicDevServices/delete|Faz uma operação DELETE em um recurso de serviço de desenvolvimento clássico.|
|/ClassicDevServices/listSecrets/action|Obter as chaves de gerenciamento de recursos de serviço de desenvolvimento clássico.|
|/ClassicDevServices/listSingleSignOnToken/action|Obter a URL de Logon Único para um serviço de desenvolvimento clássico.|
|/ClassicDevServices/read|Faz uma operação GET em um serviço de desenvolvimento clássico.|
|/ClassicDevServices/regenerateKey/action|Gerar chaves clássicas de gerenciamento de recursos do serviço de desenvolvimento.|
|/Operations/read|Ler as operações para todos os tipos de recursos.|

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

| Operação | DESCRIÇÃO |
|---|---|
|/agreements/offers/plans/cancel/action|Cancelar um contrato para um item específico do marketplace|
|/agreements/offers/plans/read|Retornar um contrato para um item específico do marketplace|
|/agreements/offers/plans/sign/action|Assinar um contrato para um item específico do marketplace|
|/agreements/read|Retornar todos os contratos sob determinada assinatura|
|/offertypes/publishers/offers/plans/agreements/read|Obter um contrato para um determinado item de máquina virtual do marketplace|
|/offertypes/publishers/offers/plans/agreements/write|Assinar ou Cancelar um contrato para um determinado item de máquina virtual do marketplace|

## <a name="microsoftmedia"></a>Microsoft.Media

| Operação | DESCRIÇÃO |
|---|---|
|/checknameavailability/action|Verificar se um nome de conta de Serviços de Mídia está disponível|
|/mediaservices/delete|Excluir qualquer Conta de Serviços de Mídia|
|/mediaservices/listKeys/action|Listar as chaves do ACS para a conta de Serviços de Mídia|
|/mediaservices/read|Ler qualquer Conta de Serviços de Mídia|
|/mediaservices/regenerateKey/action|Regenerar uma chave do ACS dos Serviços de Mídia|
|/mediaservices/syncStorageKeys/action|Sincronizar as Chaves de Armazenamento para uma conta de Armazenamento do Microsoft Azure anexada|
|/mediaservices/write|Criar ou atualizar qualquer Conta de Serviços de Mídia|
|/operations/read|Ler qualquer Conta de Serviços de Mídia|
|/register/action|Registra a assinatura do provedor de recursos dos Serviços de Mídia e habilitar a criação de contas dos Serviços de Mídia|

## <a name="microsoftmigrate"></a>Microsoft.Migrate

| Operação | DESCRIÇÃO |
|---|---|
|/Operations/read|Ler as operações expostas|

## <a name="microsoftnetwork"></a>Microsoft.Network

| Operação | DESCRIÇÃO |
|---|---|
|/applicationGatewayAvailableSslOptions/predefinedPolicies/read|Política predefinida SSL do Gateway de Aplicativo|
|/applicationGatewayAvailableSslOptions/read|Opções SSL disponíveis do Gateway de Aplicativo|
|/applicationGatewayAvailableWafRuleSets/read|Obter conjuntos de regras WAF disponíveis para o Gateway de Aplicativo|
|/applicationGateways/backendAddressPools/join/action|Ingressar em um pool de endereços de back-end do gateway de aplicativo|
|/applicationGateways/backendhealth/action|Obter uma integridade de back-end do Gateway de Aplicativo|
|/applicationGateways/delete|Excluir um gateway de aplicativo|
|/applicationGateways/effectiveNetworkSecurityGroups/action|Obter Tabela de Rotas configurada no Gateway de Aplicativo|
|/applicationGateways/effectiveRouteTable/action|Obter Tabela de Rotas configurada no Gateway de Aplicativo|
|/applicationGateways/providers/Microsoft.Insights/logDefinitions/read|Obter os eventos para o Gateway de Aplicativo|
|/applicationGateways/providers/Microsoft.Insights/metricDefinitions/read|Obter as métricas disponíveis para o Gateway de Aplicativo|
|/applicationGateways/read|Obter um gateway de aplicativo|
|/applicationGateways/setSecurityCenterConfiguration/action|Definir a configuração da Central de Segurança do Gateway de Aplicativo|
|/applicationGateways/start/action|Inicia um gateway de aplicativo|
|/applicationGateways/stop/action|Interrompe um gateway de aplicativo|
|/applicationGateways/write|Criar ou atualizar um gateway de aplicativo|
|/applicationSecurityGroups/delete|Excluir um grupo de segurança de aplicativo|
|/applicationSecurityGroups/joinIpConfiguration/action|Adicionar uma configuração IP aos grupos de segurança de aplicativo.|
|/applicationSecurityGroups/joinNetworkSecurityRule/action|Adicionar uma regra de segurança aos grupos de segurança de aplicativo.|
|/applicationSecurityGroups/read|Obter uma ID do grupo de segurança de aplicativo.|
|/applicationSecurityGroups/write|Criar um grupo de segurança de aplicativo ou atualizar um grupo de segurança de aplicativo existente.|
|/bgpServiceCommunities/read|Obter comunidades do serviço BGP|
|/checkTrafficManagerNameAvailability/action|Verificar a disponibilidade de um nome DNS relativo do Gerenciador de Tráfego.|
|/connections/delete|Excluir VirtualNetworkGatewayConnection|
|/connections/read|Obter o VirtualNetworkGatewayConnection|
|/connections/sharedkey/action|Obter VirtualNetworkGatewayConnection SharedKey|
|/connections/sharedKey/read|Obter SharedKey da VirtualNetworkGatewayConnection|
|/connections/sharedKey/write|Criar ou atualizar uma VirtualNetworkGatewayConnection SharedKey existente|
|/connections/vpndeviceconfigurationscript/read|Obter configuração do dispositivo VPN do VirtualNetworkGatewayConnection|
|/connections/write|Criar ou atualizar uma VirtualNetworkGatewayConnection existente|
|/ddosProtectionPlans/ddosProtectionPlanProxies/delete|Excluir um proxy do Plano de Proteção contra DDoS|
|/ddosProtectionPlans/ddosProtectionPlanProxies/read|Obter uma definição de proxy do Plano de Proteção contra DDoS|
|/ddosProtectionPlans/ddosProtectionPlanProxies/write|Criar um proxy do Plano de Proteção contra DDoS ou atualizar um proxy do Plano de Proteção contra DDoS existente|
|/ddosProtectionPlans/delete|Excluir um Plano de Proteção contra DDoS|
|/ddosProtectionPlans/join/action|Adicionar um Plano de Proteção contra DDoS|
|/ddosProtectionPlans/read|Obter um Plano de Proteção contra DDoS|
|/ddosProtectionPlans/write|Criar um Plano de Proteção contra DDoS ou atualizar um Plano de Proteção contra DDoS |
|/dnsoperationresults/read|Obter os resultados de uma operação de DNS|
|/dnsoperationstatuses/read|Obter o status de uma operação de DNS |
|/dnszones/A/delete|Remover o conjunto de registros de determinado nome e tipo ‘A’ de uma zona DNS.|
|/dnszones/A/read|Obter o conjunto de registros do tipo 'A', no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags.|
|/dnszones/A/write|Criar ou atualizar um conjunto de registros do tipo 'A' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros.|
|/dnszones/AAAA/delete|Remover o conjunto de registros de determinado nome e digite ‘AAAA’ de uma zona DNS.|
|/dnszones/AAAA/read|Obter o conjunto de registros do tipo 'AAAA' no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags.|
|/dnszones/AAAA/write|Criar ou atualizar um conjunto de registros do tipo 'AAAA' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros.|
|/dnszones/all/read|Obter os conjuntos de registros DNS em todos os tipos|
|/dnszones/CAA/Delete|Remover o conjunto de registros de um determinado nome e digitar "CAA" de uma zona DNS.|
|/dnszones/CAA/read|Obter o conjunto de registros do tipo ‘CAA’ no formato JSON. O conjunto de registros contém TTL, marcações e etag.|
|/dnszones/CAA/write|Criar ou atualizar um conjunto de registros do tipo ‘CAA’ em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros.|
|/dnszones/CNAME/delete|Remover o conjunto de registros de determinado nome e tipo ‘CNAME’ de uma zona DNS.|
|/dnszones/CNAME/read|Obter o conjunto de registros do tipo 'CNAME' no formato JSON. O conjunto de registros contém TTL, marcações e etag.|
|/dnszones/CNAME/write|Criar ou atualizar um conjunto de registros do tipo 'CNAME' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros.|
|/dnszones/delete|Excluir a zona DNS no formato JSON. As propriedades de zona incluem tags, etag, numberOfRecordSets e maxNumberOfRecordSets.|
|/dnszones/MX/delete|Remover o conjunto de registros de determinado nome e tipo ‘MX’ de uma zona DNS.|
|/dnszones/MX/read|Obter o conjunto de registros do tipo 'MX' no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags.|
|/dnszones/MX/write|Criar ou atualizar um conjunto de registros do tipo 'MX' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros.|
|/dnszones/NS/delete|Excluir o conjunto de registros DNS do tipo NS|
|/dnszones/NS/read|Obter o conjunto de registros DNS do tipo NS|
|/dnszones/NS/write|Criar ou atualizar o conjunto de registros DNS do tipo NS|
|/dnszones/providers/Microsoft.Insights/diagnosticSettings/read|Obter as configurações de diagnóstico da zona DNS|
|/dnszones/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar as configurações de diagnóstico da zona DNS|
|/dnszones/providers/Microsoft.Insights/metricDefinitions/read|Obter as definições de métrica da zona DNS|
|/dnszones/PTR/delete|Remover o conjunto de registros de determinado nome e tipo ‘PTR’ de uma zona DNS.|
|/dnszones/PTR/read|Obter o conjunto de registros do tipo 'PTR' no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags.|
|/dnszones/PTR/write|Criar ou atualizar um conjunto de registros do tipo 'PTR' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros.|
|/dnszones/read|Obter a zona DNS no formato JSON. As propriedades de zona incluem tags, etag, numberOfRecordSets e maxNumberOfRecordSets. Observe que esse comando não recupera conjuntos de registros contidos na zona.|
|/dnszones/recordsets/read|Obter os conjuntos de registros DNS em todos os tipos|
|/dnszones/SOA/read|Obter o conjunto de registros DNS do tipo SOA|
|/dnszones/SOA/write|Criar ou atualizar o conjunto de registros DNS do tipo SOA|
|/dnszones/SRV/delete|Remover o conjunto de registros de determinado nome e tipo ‘SRV’ de uma zona DNS.|
|/dnszones/SRV/read|Obter o conjunto de registros do tipo 'SRV' no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags.|
|/dnszones/SRV/write|Criar ou atualizar o conjunto de registros do tipo SRV|
|/dnszones/TXT/delete|Remover o conjunto de registros de determinado nome e tipo ‘TXT’ de uma zona DNS.|
|/dnszones/TXT/read|Obter o conjunto de registros do tipo 'TXT' no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags.|
|/dnszones/TXT/write|Criar ou atualizar um conjunto de registros do tipo 'TXT' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros.|
|/dnszones/write|Criar ou atualizar uma zona DNS em um grupo de recursos.  Usado para atualizar as marcações em um recurso de zona DNS. Observe que esse comando não pode ser usado para criar ou atualizar conjuntos de registros na zona.|
|/expressRouteCircuits/authorizations/delete|Excluir uma autorização ExpressRouteCircuit|
|/expressRouteCircuits/authorizations/read|Obter uma autorização ExpressRouteCircuit|
|/expressRouteCircuits/authorizations/write|Criar ou atualizar uma autorização ExpressRouteCircuit existente|
|/expressRouteCircuits/delete|Excluir um ExpressRouteCircuit|
|/expressRouteCircuits/peerings/arpTables/action|Obter ArpTable de emparelhamento de ExpressRouteCircuit|
|/expressRouteCircuits/peerings/connections/delete|Excluir uma conexão de ExpressRouteCircuit|
|/expressRouteCircuits/peerings/connections/read|Obter uma conexão de circuito de ExpressRoute|
|/expressRouteCircuits/peerings/connections/write|Criar ou atualizar um Recurso de Conexão de ExpressRouteCircuit existente|
|/expressRouteCircuits/peerings/delete|Excluir um emparelhamento de ExpressRouteCircuit|
|/expressRouteCircuits/peerings/read|Obter um emparelhamento de ExpressRouteCircuit|
|/expressRouteCircuits/peerings/routeTables/action|Obter RouteTable de emparelhamento de ExpressRouteCircuit|
|/expressRouteCircuits/peerings/routeTablesSummary/action|Obter um resumo de RouteTable de emparelhamento de ExpressRouteCircuit|
|/expressRouteCircuits/peerings/stats/read|Obter status de emparelhamento de ExpressRouteCircuit|
|/expressRouteCircuits/peerings/write|Criar ou atualizar um emparelhamento de ExpressRouteCircuit existente|
|/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/read|Obter configurações de diagnóstico para circuitos de ExpressRoute|
|/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar configurações de diagnóstico para circuitos de ExpressRoute|
|/expressRouteCircuits/providers/Microsoft.Insights/logDefinitions/read|Obter os eventos para circuitos de ExpressRoute|
|/expressRouteCircuits/providers/Microsoft.Insights/metricDefinitions/read|Obter as definições de métrica para circuitos de ExpressRoute|
|/expressRouteCircuits/read|Obter um ExpressRouteCircuit|
|/expressRouteCircuits/stats/read|Obter status de ExpressRouteCircuit|
|/expressRouteCircuits/write|Criar ou atualizar um ExpressRouteCircuit existente|
|/expressRouteCrossConnections/delete|Excluir Conexão Cruzada de Rota Expressa|
|/expressRouteCrossConnections/join/action|Adicionar uma Conexão Cruzada de Rota Expressa|
|/expressRouteCrossConnections/peerings/arpTables/action|Obter uma Tabela ARP de Emparelhamento de Conexão Cruzada de Rota Expressa|
|/expressRouteCrossConnections/peerings/delete|Excluir um Emparelhamento de Conexão Cruzada de Rota Expressa|
|/expressRouteCrossConnections/peerings/read|Obter um Emparelhamento de Conexão Cruzada de Rota Expressa|
|/expressRouteCrossConnections/peerings/routeTables/action|Obter uma Tabela de Rotas de Emparelhamento de Conexão Cruzada de Rota Expressa|
|/expressRouteCrossConnections/peerings/routeTableSummary/action|Obter uma Resumo de Tabela de Rotas de Emparelhamento de Conexão Cruzada de Rota Expressa|
|/expressRouteCrossConnections/peerings/stats/read|Obter um STAT de Emparelhamento de Conexão Cruzada de Rota Expressa|
|/expressRouteCrossConnections/peerings/write|Criar um emparelhamento de conexão cruzada de rota expressa ou atualizar um emparelhamento de conexão cruzada de rota expressa existente|
|/expressRouteCrossConnections/read|Excluir Conexão Cruzada de Rota Expressa|
|/expressRouteCrossConnections/write|Criar ou atualizar Conexão Cruzada de Rota Expressa|
|/expressRouteServiceProviders/read|Obter os provedores de serviços do ExpressRoute|
|/loadBalancers/backendAddressPools/join/action|Ingressar em um pool de endereços de back-end do balanceador de carga|
|/loadBalancers/backendAddressPools/read|Obter uma definição de pool de endereços de back-end do balanceador de carga|
|/loadBalancers/delete|Excluir um balanceador de carga|
|/loadBalancers/frontendIPConfigurations/read|Obter uma definição de configuração de IP de front-end do balanceador de carga|
|/loadBalancers/inboundNatPools/join/action|Ingressar em um pool NAT de entrada do balanceador de carga|
|/loadBalancers/inboundNatPools/read|Obter uma definição de pool NAT de entrada do balanceador de carga|
|/loadBalancers/inboundNatRules/delete|Excluir uma regra NAT de entrada do balanceador de carga|
|/loadBalancers/inboundNatRules/join/action|Adicionar uma regra NAT de entrada do balanceador de carga|
|/loadBalancers/inboundNatRules/read|Obter uma definição de regra NAT de entrada do balanceador de carga|
|/loadBalancers/inboundNatRules/write|Criar uma regra NAT de entrada do balanceador de carga ou atualizar uma regra NAT de entrada do balanceador de carga existente|
|/loadBalancers/loadBalancingRules/read|Obter uma definição regra de balanceamento de carga do balanceador de carga|
|/loadBalancers/networkInterfaces/read|Obter as referências a todos os adaptadores de rede em um balanceador de carga|
|/loadBalancers/outboundNatRules/read|Obter uma definição de regra NAT de saída do balanceador de carga|
|/loadBalancers/probes/join/action|Permitir o uso de investigações de um balanceador de carga. Por exemplo, com essa permissão, a propriedade healthProbe do conjunto de dimensionamento de VM pode referenciar a investigação.|
|/loadBalancers/probes/read|Obter uma investigação do balanceador de carga|
|/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/read|Obter as configurações de diagnóstico do balanceador de carga|
|/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar as configurações de diagnóstico do balanceador de carga|
|/loadBalancers/providers/Microsoft.Insights/logDefinitions/read|Obter os eventos para o balanceador de carga|
|/loadBalancers/providers/Microsoft.Insights/metricDefinitions/read|Obter as métricas disponíveis para o balanceador de carga|
|/loadBalancers/read|Obter uma definição de balanceador de carga|
|/loadBalancers/virtualMachines/read|Obter as referências a todas as máquinas virtuais em um balanceador de carga|
|/loadBalancers/write|Criar um balanceador de carga ou atualizar um balanceador de carga existente|
|/localnetworkgateways/delete|Excluir LocalNetworkGateway|
|/localnetworkgateways/read|Obter o LocalNetworkGateway|
|/localnetworkgateways/write|Criar ou atualizar uma LocalNetworkGateway existente|
|/locations/checkDnsNameAvailability/read|Verificar se o rótulo de DNS está disponível no local especificado|
|/locations/operationResults/read|Obter o resultado de uma operação POST ou DELETE assíncrona|
|/locations/operations/read|Obter o recurso de operação que representa o status de uma operação assíncrona|
|/locations/usages/read|Obter a métrica de uso dos recursos|
|/locations/virtualNetworkAvailableEndpointServices/read|Obter uma lista de serviços de ponto de extremidade de Rede Virtual disponíveis|
|/networkInterfaces/delete|Excluir um adaptador de rede|
|/networkInterfaces/diagnosticIdentity/read|Obter identidade de diagnóstico do recurso|
|/networkInterfaces/effectiveNetworkSecurityGroups/action|Obter grupos de segurança de configurados no adaptador de rede da máquina virtual|
|/networkInterfaces/effectiveRouteTable/action|Obter tabela de rota configurada no adaptador de rede da máquina virtual|
|/networkInterfaces/ipconfigurations/read|Obter uma definição de configuração de IP do adaptador de rede. |
|/networkInterfaces/join/action|Adicionar uma Máquina Virtual a um adaptador de rede|
|/networkInterfaces/loadBalancers/read|Obter todos os balanceadores de carga dos quais o adaptador de rede faz parte|
|/networkInterfaces/providers/Microsoft.Insights/metricDefinitions/read|Obter as métricas disponíveis para o Adaptador de Rede|
|/networkInterfaces/read|Obter uma definição de adaptador de rede. |
|/networkInterfaces/write|Criar uma interface de rede ou atualizar uma interface de rede existente. |
|/networkSecurityGroups/defaultSecurityRules/read|Obter uma definição padrão da regra de segurança|
|/networkSecurityGroups/delete|Excluir um grupo de segurança de rede|
|/networkSecurityGroups/join/action|Ingressar em um grupo de segurança de rede|
|/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read|Obter as configurações de diagnóstico dos Grupos de Segurança de Rede|
|/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar as configurações de diagnóstico dos Grupos de Segurança de Rede, essa operação é complementada pelo provedor de recursos do Microsoft Insights.|
|/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read|Obter os eventos para o grupo de segurança de rede|
|/networkSecurityGroups/read|Obter uma definição de um grupo de segurança de rede|
|/networkSecurityGroups/securityRules/delete|Excluir uma regra de segurança|
|/networkSecurityGroups/securityRules/read|Obter uma definição da regra de segurança|
|/networkSecurityGroups/securityRules/write|Criar uma regra de segurança ou atualizar uma regra de segurança existente|
|/networkSecurityGroups/write|Criar um grupo de segurança de rede ou atualizar um grupo de segurança de rede existente|
|/networkWatchers/availableProvidersList/action|Retornar todos os provedores de serviços de Internet disponíveis para uma região do Azure especificada.|
|/networkWatchers/azureReachabilityReport/action|Retornar a pontuação de latência relativa para provedores de serviços de Internet de um local especificado para regiões do Azure.|
|/networkWatchers/configureFlowLog/action|Configurar o registro de fluxo em log para um recurso de destino.|
|/networkWatchers/connectionMonitors/delete|Excluir um Monitor de Conexão|
|/networkWatchers/connectionMonitors/providers/Microsoft.Insights/ diagnosticSettings/read|Obter as configurações de diagnóstico do Monitor de Conexão|
|/networkWatchers/connectionMonitors/providers/Microsoft.Insights/ diagnosticSettings/write|Criar ou atualizar as configurações de diagnóstico do Monitor de Conexão|
|/networkWatchers/connectionMonitors/providers/Microsoft.Insights/ metricDefinitions/read|Obter as métricas disponíveis para o Monitor de Conexão|
|/networkWatchers/connectionMonitors/query/action|Consultar monitoramento de conectividade entre os pontos de extremidades especificados|
|/networkWatchers/connectionMonitors/read|Obter detalhes do Monitor de Conexão|
|/networkWatchers/connectionMonitors/start/action|Iniciar monitoramento de conectividade entre pontos de extremidades especificados|
|/networkWatchers/connectionMonitors/stop/action|Parar/pausar monitoramento de conectividade entre pontos de extremidades especificados|
|/networkWatchers/connectionMonitors/write|Criar um Monitor de Conexão|
|/networkWatchers/connectivityCheck/action|Verificar a possibilidade de estabelecer uma conexão TCP direta de uma máquina virtual com um determinado ponto de extremidade, incluindo outra VM ou um servidor remoto arbitrário.|
|/networkWatchers/delete|Excluir um observador de rede|
|/networkWatchers/ipFlowVerify/action|Retornar se o pacote é permitido ou negado em relação a um destino específico.|
|/networkWatchers/lenses/delete|Excluir uma Lente|
|/networkWatchers/lenses/query/action|Consultar monitoramento de tráfego em um ponto de extremidade especificado|
|/networkWatchers/lenses/read|Obter os detalhes de Lente|
|/networkWatchers/lenses/start/action|Iniciar monitoramento de tráfego em um ponto de extremidade especificado|
|/networkWatchers/lenses/stop/action|Parar/pausar o monitoramento do tráfego de rede em um terminal especificado|
|/networkWatchers/lenses/write|Criar uma lente|
|/networkWatchers/nextHop/action|Para um destino especificado e endereço IP de destino especificados, retornar o tipo do próximo salto e próximo endereço IP esperado.|
|/networkWatchers/packetCaptures/delete|Excluir uma captura de pacote|
|/networkWatchers/packetCaptures/queryStatus/action|Obter informações sobre propriedades e status de um recurso de captura de pacote.|
|/networkWatchers/packetCaptures/read|Obter a definição de captura de pacote|
|/networkWatchers/packetCaptures/stop/action|Interromper a sessão de captura de pacote em execução.|
|/networkWatchers/packetCaptures/write|Criar uma captura de pacote|
|/networkWatchers/queryFlowLogStatus/action|Obter o status do registro do fluxo em log em um recurso.|
|/networkWatchers/queryTroubleshootResult/action|Obter o resultado da solução de problemas da operação de solução de problemas executada ou atualmente em execução.|
|/networkWatchers/read|Obter a definição de observador de rede|
|/networkWatchers/securityGroupView/action|Exibir as regras de grupo de segurança de rede configuradas e em vigor aplicadas a uma máquina virtual.|
|/networkWatchers/topology/action|Obter uma exibição dos recursos em nível de rede e de suas relações em um grupo de recursos.|
|/networkWatchers/troubleshoot/action|Iniciar a solução de problemas em um recurso de rede no Azure.|
|/networkWatchers/write|Criar um observador de rede ou atualizar um observador de rede existente|
|/operations/read|Obter operações disponíveis|
|/publicIPAddresses/delete|Excluir um endereço IP público.|
|/publicIPAddresses/join/action|Ingressar em um endereço IP público|
|/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/read|Obter as configurações de diagnóstico do endereço IP público|
|/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar as configurações de diagnóstico do endereço IP público|
|/publicIPAddresses/providers/Microsoft.Insights/logDefinitions/read|Obter as definições de log do endereço IP público|
|/publicIPAddresses/providers/Microsoft.Insights/metricDefinitions/read|Obter as definições de métrica do endereço IP público|
|/publicIPAddresses/read|Obter uma definição de endereço IP público.|
|/publicIPAddresses/write|Criar um endereço IP público ou atualizar um endereço IP público existente. |
|/register/action|Registra a assinatura|
|/routeFilters/delete|Excluir uma definição de filtro de rota|
|/routeFilters/join/action|Ingressar em um filtro de rota|
|/routeFilters/read|Obter uma definição de filtro de rota|
|/routeFilters/routeFilterRules/delete|Excluir uma definição de regra de filtro de rota|
|/routeFilters/routeFilterRules/read|Obter uma definição de regra de filtro de rota|
|/routeFilters/routeFilterRules/write|Criar uma regra de filtro de rota ou atualizar uma regra de filtro de rota existente|
|/routeFilters/write|Criar um filtro de rota ou atualizar um filtro de rota existente|
|/routeTables/delete|Excluir uma definição de tabela de rota|
|/routeTables/join/action|Ingressar em uma tabela de rotas|
|/routeTables/read|Obter uma definição de tabela de rota|
|/routeTables/routes/delete|Excluir uma definição de rota|
|/routeTables/routes/read|Obter uma definição de rota|
|/routeTables/routes/write|Criar uma rota ou atualizar uma rota existente|
|/routeTables/write|Criar uma tabela de rotas ou atualizar uma tabela de rotas existente|
|/securegateways/applicationRuleCollections/delete|Excluir uma Coleção de Regras de Aplicativo para um Gateway de Segurança|
|/securegateways/applicationRuleCollections/read|Recuperar uma Coleção de Regras de Aplicativo para um determinado Gateway de Segurança|
|/securegateways/applicationRuleCollections/write|Criar ou atualizar uma Coleção de Regras de Aplicativo para um Gateway de Segurança|
|/securegateways/delete|Excluir um Gateway de Segurança|
|/securegateways/networkRuleCollections/delete|Excluir uma Coleção de Regras de Rede para um Gateway de Segurança|
|/securegateways/networkRuleCollections/read|Recuperar uma Coleção de Regras de Rede para um determinado Gateway de Segurança|
|/securegateways/networkRuleCollections/write|Criar ou atualizar uma Coleção de Regras de Rede para um Gateway de Segurança|
|/securegateways/read|Obter um Gateway de Segurança|
|/securegateways/write|Criar ou atualizar um Gateway de Segurança|
|/serviceEndpointPolicies/delete|Excluir uma política de ponto de extremidade de serviço|
|/serviceEndpointPolicies/join/action|Adicionar uma política de ponto de extremidade de serviço|
|/serviceEndpointPolicies/joinSubnet/action|Adicionar uma sub-rede para políticas de ponto de extremidade de serviço|
|/serviceEndpointPolicies/read|Obter uma descrição da política de ponto de extremidade de serviço|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete|Excluir uma descrição da política de ponto de extremidade de serviço|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read|Obter uma definição de política de ponto de extremidade de serviço|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write|Criar uma definição de política de ponto de extremidade de serviço ou atualizar uma definição de política de ponto de extremidade de serviço existente|
|/serviceEndpointPolicies/write|Criar uma política de ponto de extremidade de serviço ou atualizar uma política de ponto de extremidade de serviço existente|
|/trafficManagerGeographicHierarchies/read|Obter a hierarquia de geográfica do Gerenciador de Tráfego contendo regiões que podem ser usadas com o método de roteamento de tráfego geográfico|
|/trafficManagerProfiles/azureEndpoints/delete|Excluir um Ponto de Extremidade do Azure de um perfil do Gerenciador de Tráfego existente. O Gerenciador de Tráfego interromperá o tráfego de roteamento para o Ponto de Extremidade do Azure excluído.|
|/trafficManagerProfiles/azureEndpoints/read|Obter um Ponto de Extremidade do Azure que pertence a um perfil do Gerenciador de Tráfego, incluindo todas as propriedades desse Ponto de Extremidade do Azure.|
|/trafficManagerProfiles/azureEndpoints/write|Adicionar um novo Ponto de Extremidade do Azure em um perfil do Gerenciador de Tráfego existente ou atualizar as propriedades de um Ponto de Extremidade do Azure existente nesse perfil do Gerenciador de Tráfego.|
|/trafficManagerProfiles/delete|Excluir o perfil do Gerenciador de Tráfego. Todas as configurações associadas ao perfil do Gerenciador de Tráfego serão perdidas e o perfil não poderá ser usado para rotear o tráfego.|
|/trafficManagerProfiles/externalEndpoints/delete|Excluir um ponto de extremidade externo de um perfil do Gerenciador de Tráfego existente. O Gerenciador de Tráfego do Microsoft Azure interromperá o tráfego de roteamento para o ponto de extremidade externo excluído.|
|/trafficManagerProfiles/externalEndpoints/read|Obter um ponto de extremidade externo que pertence a um perfil do Gerenciador de Tráfego, incluindo todas as propriedades desse ponto de extremidade externo.|
|/trafficManagerProfiles/externalEndpoints/write|Adicionar um novo ponto de extremidade externo em um perfil do Gerenciador de Tráfego existente ou atualizar as propriedades de um ponto de extremidade externo existente nesse perfil do Gerenciador de Tráfego.|
|/trafficManagerProfiles/heatMaps/read|Obter o mapa de calor do Gerenciador de Tráfego para o perfil do Gerenciador de Tráfego fornecido, que contém contagens de consulta e dados de latência por local e IP de origem.|
|/trafficManagerProfiles/nestedEndpoints/delete|Excluir um ponto de extremidade aninhado de um perfil do Gerenciador de Tráfego existente. O Gerenciador de Tráfego interromperá o tráfego de roteamento para o ponto de extremidade aninhado excluído.|
|/trafficManagerProfiles/nestedEndpoints/read|Obtém um ponto de extremidade aninhado que pertence a um perfil do Gerenciador de Tráfego, incluindo todas as propriedades desse ponto de extremidade aninhado.|
|/trafficManagerProfiles/nestedEndpoints/write|Adicionar um novo ponto de extremidade aninhado em um perfil do Gerenciador de Tráfego existente ou atualizar as propriedades de um ponto de extremidade aninhado existente nesse perfil do Gerenciador de Tráfego.|
|/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/read|Obter as configurações de diagnóstico do Gerenciador de Tráfego|
|/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar as configurações de diagnóstico do Gerenciador de Tráfego, essa operação é complementada pelo provedor de recursos do Microsoft Insights.|
|/trafficManagerProfiles/providers/Microsoft.Insights/logDefinitions/read|Obter os eventos para o Gerenciador de Tráfego|
|/trafficManagerProfiles/providers/Microsoft.Insights/metricDefinitions/read|Obtém as métricas disponíveis para o Gerenciador de Tráfego.|
|/trafficManagerProfiles/read|Obter a configuração de perfil do Gerenciador de Tráfego. Isso inclui configurações DNS, configurações de roteamento de tráfego, configurações de monitoramento do ponto de extremidade e lista de pontos de extremidade roteados por esse perfil do Gerenciador de Tráfego.|
|/trafficManagerProfiles/write|Criar um perfil do Gerenciador de Tráfego ou modificar a configuração de um perfil do Gerenciador de Tráfego existente. Isso inclui habilitar ou desabilitar um perfil e modificar as configurações de DNS, configurações de roteamento de tráfego ou configurações de monitoramento do ponto de extremidade. Pontos de extremidade roteados pelo perfil do Gerenciador de Tráfego podem ser adicionados, removidos, habilitados ou desabilitados.|
|/trafficManagerUserMetricsKeys/delete|Excluir a chave do nível de assinatura usada para a coleção de métricas de usuário em tempo real.|
|/trafficManagerUserMetricsKeys/read|Obter a chave do nível de assinatura usada para a coleção de métricas de usuário em tempo real.|
|/trafficManagerUserMetricsKeys/write|Criar uma nova chave do nível de assinatura a ser usada para a coleção de métricas de usuário em tempo real.|
|/unregister/action|Cancelar o registro da assinatura|
|/virtualHubs/delete|Excluir um Hub Virtual|
|/virtualHubs/hubVirtualNetworkConnections/delete|Excluir um HubVirtualNetworkConnection|
|/virtualHubs/hubVirtualNetworkConnections/read|Obter um HubVirtualNetworkConnection|
|/virtualHubs/hubVirtualNetworkConnections/write|Criar ou atualizar um HubVirtualNetworkConnection|
|/virtualHubs/read|Obter um Hub Virtual|
|/virtualHubs/write|Criar ou atualizar um Hub Virtual|
|/virtualnetworkgateways/connections/read|Obter um VirtualNetworkGatewayConnection|
|/virtualNetworkGateways/delete|Excluir um virtualNetworkGateway|
|/virtualnetworkgateways/generatevpnclientpackage/action|Gerar pacote de VpnClient para virtualNetworkGateway|
|/virtualnetworkgateways/generatevpnprofile/action|Gerar pacote de VpnProfile para VirtualNetworkGateway|
|/virtualnetworkgateways/getadvertisedroutes/action|Obter rotas anunciadas do virtualNetworkGateway|
|/virtualnetworkgateways/getbgppeerstatus/action|Obter status do Par de BGP do virtualNetworkGateway|
|/virtualnetworkgateways/getlearnedroutes/action|Obter rotas aprendidas do virtualnetworkgateway|
|/virtualnetworkgateways/getvpnclientipsecparameters/action|Obter parâmetros IPsec do Vpnclient para cliente P2S do VirtualNetworkGateway.|
|/virtualnetworkgateways/getvpnprofilepackageurl/action|Obter a URL de um pacote de perfil de cliente VPN gerado previamente|
|/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/read|Obter as configurações de diagnóstico do Gateway de Rede Virtual|
|/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar as configurações de diagnóstico do Gateway de Rede Virtual, essa operação é complementada pelo provedor de recursos do Microsoft Insights.|
|/virtualNetworkGateways/providers/Microsoft.Insights/logDefinitions/read|Obter os eventos do Gateway de Rede Virtual|
|/virtualNetworkGateways/providers/Microsoft.Insights/metricDefinitions/read|Obter as métricas disponíveis para o Gateway de Rede Virtual|
|/virtualNetworkGateways/read|Obter um VirtualNetworkGateway|
|/virtualnetworkgateways/reset/action|Reiniciar um virtualNetworkGateway|
|/virtualnetworkgateways/setvpnclientipsecparameters/action|Configurar parâmetros IPsec do Vpnclient para cliente P2S do VirtualNetworkGateway.|
|/virtualnetworkgateways/supportedvpndevices/action|Listar dispositivos VPN com suporte|
|/virtualNetworkGateways/write|Criar ou atualizar um VirtualNetworkGateway|
|/virtualNetworks/checkIpAddressAvailability/read|Verificar se o endereço IP está disponível na rede virtual especificada|
|/virtualNetworks/customViews/get/action|Obter um conteúdo de exibição personalizada da Rede Virtual|
|/virtualNetworks/customViews/read|Obter a definição de uma exibição personalizada da Rede Virtual|
|/virtualNetworks/delete|Excluir uma rede virtual|
|/virtualNetworks/peer/action|Colocar uma rede virtual com outra rede virtual de mesmo nível|
|/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/read|Obter as configurações de diagnóstico da Rede Virtual|
|/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar as configurações de diagnóstico da Rede Virtual|
|/virtualNetworks/providers/Microsoft.Insights/logDefinitions/read|Obter as definições de log da Rede Virtual|
|/virtualNetworks/providers/Microsoft.Insights/metricDefinitions/read|Obter as definições métrica de Rede Virtual|
|/virtualNetworks/read|Obter a definição de rede virtual|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete|Excluir um proxy de emparelhamento de rede virtual|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/read|Obter uma definição de proxy de emparelhamento de rede virtual|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/write|Criar um proxy de emparelhamento de rede virtual ou atualizar um proxy de emparelhamento de rede virtual existente|
|/virtualNetworks/subnets/delete|Excluir uma sub-rede de rede virtual|
|/virtualNetworks/subnets/join/action|Ingressar em uma rede virtual|
|/virtualNetworks/subnets/joinViaServiceEndpoint/action|Adicionar recursos como conta de armazenamento ou banco de dados SQL a uma sub-rede.|
|/virtualNetworks/subnets/read|Obter uma definição de sub-rede da rede virtual|
|/virtualNetworks/subnets/resourceNavigationLinks/delete|Excluir um link de navegação de recursos|
|/virtualNetworks/subnets/resourceNavigationLinks/read|Obter a definição de link de navegação de recursos|
|/virtualNetworks/subnets/resourceNavigationLinks/write|Criar um link de navegação de recursos ou atualizar um link de navegação de recursos existente|
|/virtualNetworks/subnets/virtualMachines/read|Obter referências a todas as máquinas virtuais em uma sub-rede de rede virtual|
|/virtualNetworks/subnets/write|Criar uma sub-rede de rede virtual ou atualizar uma sub-rede de rede virtual existente|
|/virtualNetworks/taggedTrafficConsumers/delete|Excluir um consumidor de tráfego marcado|
|/virtualNetworks/taggedTrafficConsumers/read|Obter a definição de consumidor de tráfego marcado|
|/virtualNetworks/taggedTrafficConsumers/validate/action|Validar um consumidor de tráfego marcado|
|/virtualNetworks/taggedTrafficConsumers/write|Criar um consumidor de tráfego marcado ou atualiza um consumidor de tráfego marcado existente|
|/virtualNetworks/usages/read|Obter os usos de IP para cada sub-rede da rede virtual|
|/virtualNetworks/virtualMachines/read|Obter referências a todas as máquinas virtuais em uma rede virtual|
|/virtualNetworks/virtualNetworkPeerings/delete|Excluir um emparelhamento de redes virtuais|
|/virtualNetworks/virtualNetworkPeerings/read|Obter uma definição de emparelhamento de rede virtual|
|/virtualNetworks/virtualNetworkPeerings/write|Criar um emparelhamento de rede virtual ou atualizar um emparelhamento de rede virtual existente|
|/virtualNetworks/write|Criar uma rede virtual ou atualizar uma rede virtual existente|
|/virtualNetworkTaps/delete|Excluir Toque de Rede Virtual|
|/virtualNetworkTaps/join/action|Adicionar toque de rede virtual|
|/virtualNetworkTaps/read|Obter Toque de Rede Virtual|
|/virtualNetworkTaps/write|Criar ou atualizar Toque de Rede Virtual|
|/virtualwans/delete|Excluir uma WAN Virtual|
|/virtualwans/read|Obter uma WAN Virtual|
|/virtualWans/virtualHubProxies/delete|Excluir um proxy de Hub Virtual|
|/virtualWans/virtualHubProxies/read|Obter uma definição de proxy de Hub Virtual|
|/virtualWans/virtualHubProxies/write|Criar um proxy de Hub Virtual ou atualizar um proxy de Hub Virtual|
|/virtualwans/virtualHubs/read|Obter todos os Hubs Virtuais associados a uma WAN Virtual.|
|/virtualwans/vpnconfiguration/read|Obter uma configuração de VPN|
|/virtualWans/vpnSiteProxies/delete|Excluir um proxy de VPN site|
|/virtualWans/vpnSiteProxies/read|Obter uma definição de proxy de VPN site|
|/virtualWans/vpnSiteProxies/write|Criar um proxy de VPN site ou atualizar um proxy de VPN site|
|/virtualwans/vpnSites/read|Obter todos os Sites VPN associados a uma WAN Virtual.|
|/virtualwans/write|Criar ou atualizar um uma WAN Virtual|
|/vpnGateways/read|Obter um VpnGateway.|
|/vpnGateways/vpnConnections/read|Obter um VpnConnection.|
|/vpnGateways/vpnConnections/write|Implementar um VpnConnection.|
|/vpnGateways/write|Implementar um VpnGateway.|
|/vpnsites/delete|Excluir um recurso de VPN site.|
|/vpnsites/read|Obter um recurso de VPN site.|
|/vpnsites/write|Criar ou atualizar um recurso de VPN site.|

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

| Operação | DESCRIÇÃO |
|---|---|
|/CheckNamespaceAvailability/action|Verificar se determinado nome de recurso do Namespace está disponível no serviço NotificationHub.|
|/Namespaces/authorizationRules/action|Obter a lista de descrição de regras de autorização de namespaces.|
|/Namespaces/authorizationRules/delete|Excluir regra de autorização de namespace. A regra de autorização do namespace padrão não pode ser excluída. |
|/Namespaces/authorizationRules/listkeys/action|Obter a cadeia de conexão para o namespace|
|/Namespaces/authorizationRules/read|Obter a lista de descrição de regras de autorização de namespaces.|
|/Namespaces/authorizationRules/regenerateKeys/action|Regra de autorização de namespace regenera chave primária/secundária; especifique a chave que precisa ser regenerada|
|/Namespaces/authorizationRules/write|Criar regras de autorização no nível do namespace e atualizar suas propriedades. Os direitos de acesso das regras de autorização; as chaves primárias e secundárias podem ser atualizadas.|
|/Namespaces/CheckNotificationHubAvailability/action|Verificar se determinado nome de NotificationHub está disponível em um Namespace.|
|/Namespaces/Delete|Excluir o recurso de namespace|
|/Namespaces/NotificationHubs/authorizationRules/action|Obter a lista de regras de autorização do Hub de notificação|
|/Namespaces/NotificationHubs/authorizationRules/delete|Excluir regras de autorização do Hub de notificação|
|/Namespaces/NotificationHubs/authorizationRules/listkeys/action|Obter a cadeia de conexão para o Hub de notificação|
|/Namespaces/NotificationHubs/authorizationRules/read|Obter a lista de regras de autorização do Hub de notificação|
|/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action|Regra de autorização de Hub de notificação regenera chave primária/secundária; especifique a chave que precisa ser regenerada|
|/Namespaces/NotificationHubs/authorizationRules/write|Criar regras de autorização do Hub de notificação e atualizar suas propriedades. Os direitos de acesso das regras de autorização; as chaves primárias e secundárias podem ser atualizadas.|
|/Namespaces/NotificationHubs/debugSend/action|Enviar notificação por push de teste.|
|/Namespaces/NotificationHubs/Delete|Excluir recurso de Hub de notificação|
|/Namespaces/NotificationHubs/metricDefinitions/read|Obter lista de métrica de descrições de recurso de métrica do namespace|
|/Namespaces/NotificationHubs/pnsCredentials/action|Obter todas as credenciais PNS do Hub de notificação. Isso inclui credenciais WNS, MPNS, APNS, GCM e Baidu|
|/Namespaces/NotificationHubs/read|Obter lista de descrições de recursos do Hub de notificação|
|/Namespaces/NotificationHubs/write|Criar um Hub de notificação e atualizar suas propriedades. Suas propriedades incluem principalmente as credenciais PNS. Tempo de vida útil e regras de autorização|
|/Namespaces/read|Obter a lista de descrição do recurso de namespace|
|/Namespaces/write|Criar um recurso de namespace e atualizar suas propriedades. Marcas e Capacidade do namespace são as propriedades que podem ser atualizadas.|
|/register/action|Registra a assinatura do provedor de recursos NotifciationHubs e permite a criação de Namespaces e NotificationHubs|

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

| Operação | DESCRIÇÃO |
|---|---|
|/linkTargets/read|Listar as contas existentes que não estão associadas uma assinatura do Azure. Para vincular essa assinatura do Azure a um espaço de trabalho, use uma ID de cliente retornada pela operação na propriedade customer id da operação Criar espaço de trabalho.|
|/register/action|Registrar uma assinatura de um provedor de recursos.|
|/workspaces/analytics/query/action|Pesquisar usando o novo mecanismo.|
|/workspaces/analytics/query/schema/read|Obter o esquema de pesquisa V2.|
|/workspaces/api/query/action|Pesquisar usando o novo mecanismo.|
|/workspaces/api/query/schema/read|Obter o esquema de pesquisa V2.|
|/workspaces/configurationScopes/delete|Excluir escopo de configuração|
|/workspaces/configurationScopes/read|Obter escopo de configuração|
|/workspaces/configurationScopes/write|Definir o escopo da configuração|
|/workspaces/datasources/delete|Excluir fontes de dados em um espaço de trabalho.|
|/workspaces/datasources/read|Obter fontes de dados em um espaço de trabalho.|
|/workspaces/datasources/write|Criar/atualizar fontes de dados em um espaço de trabalho.|
|/workspaces/delete|Excluir um espaço de trabalho. Se o espaço de trabalho foi vinculado a um espaço de trabalho existente no momento da criação, o espaço de trabalho a que ele foi vinculado não será excluído.|
|/workspaces/generateregistrationcertificate/action|Gerar certificado de registro para o espaço de trabalho. Esse certificado é usado para conectar o Microsoft System Center Operations Manager ao espaço de trabalho.|
|/workspaces/intelligencepacks/disable/action|Desabilitar um pacote de inteligência para determinado espaço de trabalho.|
|/workspaces/intelligencepacks/enable/action|Habilitar um pacote de inteligência para determinado espaço de trabalho.|
|/workspaces/intelligencepacks/read|Lista todos os pacotes de inteligência visíveis para determinado espaço de trabalho e também indica se o pacote está habilitado ou desabilitado para esse espaço de trabalho.|
|/workspaces/linkedServices/delete|Excluir serviços vinculados em um determinado espaço de trabalho.|
|/workspaces/linkedServices/read|Obter serviços vinculados em um determinado espaço de trabalho.|
|/workspaces/linkedServices/write|Criar/atualizar serviços vinculados em determinado espaço de trabalho.|
|/workspaces/listKeys/action|Recuperar as chaves de lista para o espaço de trabalho. Essas chaves são usadas para conectar agentes do Insights Operacionais da Microsoft ao espaço de trabalho.|
|/workspaces/listKeys/read|Recuperar as chaves de lista para o espaço de trabalho. Essas chaves são usadas para conectar agentes do Insights Operacionais da Microsoft ao espaço de trabalho.|
|/workspaces/managementGroups/read|Obter os nomes e os metadados para grupos de gerenciamento do System Center Operations Manager conectados ao espaço de trabalho.|
|/workspaces/metricDefinitions/read|Obter definições métricas no espaço de trabalho|
|/workspaces/notificationSettings/delete|Excluir as configurações de notificação do usuário para o espaço de trabalho.|
|/workspaces/notificationSettings/read|Obter as configurações de notificação do usuário para o espaço de trabalho.|
|/workspaces/notificationSettings/write|Definir as configurações de notificação do usuário para o espaço de trabalho.|
|/workspaces/purge/action|Excluir dados especificados do espaço de trabalho|
|/workspaces/read|Obter um espaço de trabalho existente|
|/workspaces/savedSearches/delete|Excluir uma consulta de pesquisa salva|
|/workspaces/savedSearches/read|Obter uma consulta de pesquisa salva|
|/workspaces/savedSearches/write|Criar uma consulta de pesquisa salva|
|/workspaces/schema/read|Obter o esquema de pesquisa do espaço de trabalho.  O esquema de pesquisa inclui os campos expostos e seus tipos.|
|/workspaces/search/action|Executar uma consulta de pesquisa|
|/workspaces/sharedKeys/action|Recupera as chaves compartilhadas do espaço de trabalho. Essas chaves são usadas para conectar agentes do Insights Operacionais da Microsoft ao espaço de trabalho.|
|/workspaces/sharedKeys/read|Recupera as chaves compartilhadas do espaço de trabalho. Essas chaves são usadas para conectar agentes do Insights Operacionais da Microsoft ao espaço de trabalho.|
|/workspaces/storageinsightconfigs/delete|Excluir uma configuração de armazenamento. Isso interromperá a leitura dos dados da conta de armazenamento pelo Insights Operacionais.|
|/workspaces/storageinsightconfigs/read|Obter uma configuração de armazenamento.|
|/workspaces/storageinsightconfigs/write|Criar uma nova configuração de armazenamento. Essas configurações são usadas para extrair dados de um local em uma conta de armazenamento existente.|
|/workspaces/usages/read|Obter dados de uso de um espaço de trabalho e a quantidade de dados lidos pelo espaço de trabalho.|
|/workspaces/write|Criar um novo espaço de trabalho ou links para um espaço de trabalho existente fornecendo a ID do cliente da área de trabalho existente.|

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

| Operação | DESCRIÇÃO |
|---|---|
|/managementAssociations/delete|Excluir a Associação de Gerenciamento existente|
|/managementAssociations/read|Obter Associação de Gerenciamento existente|
|/managementAssociations/write|Crie uma nova Associação de Gerenciamento|
|/managementConfigurations/delete|Excluir Configuração de Gerenciamento existente|
|/managementConfigurations/read|Obter Configuração de Gerenciamento existente|
|/managementConfigurations/write|Criar uma nova Configuração de Gerenciamento|
|/register/action|Registrar uma assinatura de um provedor de recursos.|
|/solutions/delete|Excluir a solução do OMS existente|
|/solutions/read|Obter solução OMS de saída|
|/solutions/write|Criar nova solução do OMS|

## <a name="microsoftportal"></a>Microsoft.Portal

| Operação | DESCRIÇÃO |
|---|---|
|/dashboards/delete|Remover o painel da assinatura.|
|/dashboards/read|Ler os painéis da assinatura.|
|/dashboards/write|Adicionar ou modificar o painel para uma assinatura.|

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

| Operação | DESCRIÇÃO |
|---|---|
|/capacities/checkNameAvailability/action|Verificar se o nome da capacidade dedicada do Power BI é válido e não está em uso.|
|/capacities/delete|Excluir a capacidade dedicada do Power BI.|
|/capacities/providers/Microsoft.Insights/metricDefinitions/read|Obter as métricas disponíveis para a capacidade dedicada do Power BI.|
|/capacities/read|Recuperar as informações da Capacidade Dedicada especificada do Power BI.|
|/capacities/write|Criar ou atualizar a capacidade dedicada especificada do Power BI.|

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

| Operação | DESCRIÇÃO |
|---|---|
|/locations/allocatedStamp/read|GetAllocatedStamp é uma operação interna usada pelo serviço|
|/locations/allocateStamp/action|AllocateStamp é uma operação interna usada pelo serviço|
|/locations/backupPreValidateProtection/action||
|/locations/backupStatus/action|Verificar o status de backup para os Cofres dos Serviços de Recuperação|
|/locations/backupValidateFeatures/action|Validar recursos|
|/operations/read|Operação retorna a lista de operações para um provedor de recursos|
|/register/action|Registrar assinatura de um determinado provedor de recursos|
|/Vaults/backupconfig/read|Retornar a configuração para cofre dos Serviços de Recuperação.|
|/Vaults/backupconfig/write|Atualizar configuração para o cofre dos Serviços de Recuperação.|
|/Vaults/backupEngines/read|Retornar todos os servidores de gerenciamento de backup registrados com cofre.|
|/Vaults/backupFabrics/{fabricName}/protectionContainers/{containerName}/items/read|Obter todos os itens em um contêiner|
|/Vaults/backupFabrics/backupProtectionIntent/write|Criar uma Intenção de Proteção de backup|
|/Vaults/backupFabrics/operationResults/read|Retornar o status da operação|
|/Vaults/backupFabrics/protectableContainers/read|Obter todos os contêineres protegidos|
|/Vaults/backupFabrics/protectionContainers/inquire/action|Consultar cargas de trabalho em um contêiner|
|/Vaults/backupFabrics/protectionContainers/operationResults/read|Obter o resultado da operação executada no contêiner de proteção.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action|Executar um backup para um item protegido.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/delete|Excluir item protegido|
|/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read|Obter o resultado da operação executada em itens protegidos.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read|Retornar o status da operação executada em itens protegidos.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/read|Retornar detalhes do objeto do item protegido|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/provisionInstantItemRecovery/action|Provisionar recuperação de item instantânea para item protegido|
|/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read|Obter pontos de recuperação para itens protegidos.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/restore/action|Restaurar pontos de recuperação para itens protegidos.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/revokeInstantItemRecovery/action|Revogar a recuperação de item instantânea para item protegido|
|/Vaults/backupFabrics/protectionContainers/protectedItems/write|Criar um item protegido de backup|
|/Vaults/backupFabrics/protectionContainers/read|Retornar todos os contêineres registrados|
|/Vaults/backupFabrics/protectionContainers/write|Criar um contêiner registrado|
|/Vaults/backupFabrics/refreshContainers/action|Atualizar a lista de contêineres|
|/Vaults/backupJobs/cancel/action|Cancelar o trabalho|
|/Vaults/backupJobs/operationResults/read|Retornar o resultado da operação do trabalho.|
|/Vaults/backupJobs/read|Retornar todos os objetos de trabalho|
|/Vaults/backupJobsExport/action|Exportar trabalhos|
|/Vaults/backupJobsExport/operationResults/read|Retornar o resultado da operação do trabalho de exportação.|
|/Vaults/backupManagementMetaData/read|Retornar metadados de gerenciamento de backup para o cofre dos Serviços de Recuperação.|
|/Vaults/backupOperationResults/read|Retornar o resultado da operação de backup para o cofre dos Serviços de Recuperação.|
|/Vaults/backupOperations/read|Retornar o status da operação de backup para o cofre dos Serviços de Recuperação.|
|/Vaults/backupPolicies/delete|Excluir uma política de proteção|
|/Vaults/backupPolicies/operationResults/read|Obter os resultados da operação de política.|
|/Vaults/backupPolicies/operations/read|Obter o status da operação de política.|
|/Vaults/backupPolicies/read|Retornar todas as políticas de proteção|
|/Vaults/backupPolicies/write|Criar uma política de proteção|
|/Vaults/backupProtectableItems/read|Retornar a lista de todos os itens que podem ser protegidos.|
|/Vaults/backupProtectedItems/read|Retornar a lista de todos os itens protegidos.|
|/Vaults/backupProtectionContainers/read|Retornar todos os contêineres pertencentes à assinatura|
|/Vaults/backupSecurityPIN/action|Retornar a informação de PIN de segurança para o cofre dos Serviços de Recuperação.|
|/Vaults/backupstorageconfig/read|Retornar cofre de armazenamento para o cofre dos Serviços de Recuperação.|
|/Vaults/backupstorageconfig/write|Atualizar cofre de armazenamento para o cofre dos Serviços de Recuperação.|
|/Vaults/backupUsageSummaries/read|Retornar resumos de itens protegidos e servidores protegidos para os Serviços de Recuperação.|
|/Vaults/certificates/write|A operação Atualizar certificado do recurso atualiza o certificado de credencial de cofre/recurso.|
|/Vaults/delete|A operação Excluir cofre exclui o recurso do Azure do tipo 'cofre' especificado|
|/Vaults/extendedInformation/delete|A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault?|
|/Vaults/extendedInformation/read|A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault?|
|/Vaults/extendedInformation/write|A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault?|
|/Vaults/monitoringAlerts/read|Obter os alertas para o cofre dos Serviços de Recuperação.|
|/Vaults/monitoringAlerts/write|Resolver o alerta.|
|/Vaults/monitoringConfigurations/read|Obter a configuração de notificação do cofre dos Serviços de Recuperação.|
|/Vaults/monitoringConfigurations/write|Configurar notificações por email para o cofre dos Serviços de Recuperação.|
|/Vaults/providers/Microsoft.Insights/diagnosticSettings/read|Diagnóstico do Backup do Azure|
|/Vaults/providers/Microsoft.Insights/diagnosticSettings/write|Diagnóstico do Backup do Azure|
|/Vaults/providers/Microsoft.Insights/logDefinitions/read|Logs do Backup do Azure|
|/Vaults/providers/Microsoft.Insights/metricDefinitions/read|Métricas do Backup do Azure|
|/Vaults/read|A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre'|
|/Vaults/registeredIdentities/delete|A operação Cancelar o registro de contêiner pode ser usada para cancelar o registro de um contêiner.|
|/Vaults/registeredIdentities/operationResults/read|A operação Obter resultados da operação pode ser usada para obter o status da operação e o resultado da operação enviada de forma assíncrona|
|/Vaults/registeredIdentities/read|A operação Obter contêineres pode ser usada para obter os contêineres registrados para um recurso.|
|/Vaults/registeredIdentities/write|A operação Registrar o contêiner de serviço pode ser usada para registrar um contêiner com o Serviço de Recuperação.|
|/vaults/replicationAlertSettings/read|Ler configurações de alertas|
|/vaults/replicationAlertSettings/write|Criar ou atualizar as configurações de alertas|
|/vaults/replicationEvents/read|Ler eventos|
|/vaults/replicationFabrics/checkConsistency/action|Verificar consistência da malha|
|/vaults/replicationFabrics/delete|Excluir malhas|
|/vaults/replicationFabrics/deployProcessServerImage/action|Implantar imagem do servidor de processo|
|/vaults/replicationFabrics/read|Ler malhas|
|/vaults/replicationFabrics/reassociateGateway/action|Reassociar gateway|
|/vaults/replicationFabrics/remove/action|Remover malha|
|/vaults/replicationFabrics/renewcertificate/action|Renovar Certificado para malha do Microsoft Azure|
|/vaults/replicationFabrics/replicationNetworks/read|Ler redes|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete|Excluir mapeamentos de rede|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read|Ler mapeamentos de rede|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write|Criar ou atualizar os mapeamentos de rede|
|/vaults/replicationFabrics/replicationProtectionContainers/ discoverProtectableItem/action|Descobrir item que pode ser protegido|
|/vaults/replicationFabrics/replicationProtectionContainers/read|Ler contêineres de proteção|
|/vaults/replicationFabrics/replicationProtectionContainers/remove/action|Remover o contêiner de proteção|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectableItems/read|Ler itens que podem ser protegidos|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/applyRecoveryPoint/action|Aplicar ponto de recuperação|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/delete|Excluir itens protegidos|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/failoverCommit/action|Confirmação de failover|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/plannedFailover/action|Failover planejado|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/read|Ler itens protegidos|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/recoveryPoints/read|Ler pontos de recuperação de replicação|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/remove/action|Remover item protegido|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/repairReplication/action|Reparar replicação|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/reProtect/action|Proteger item protegido novamente|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/testFailover/action|Failover de Teste|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/testFailoverCleanup/action|Limpeza do Failover de teste|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/unplannedFailover/action|Failover|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/updateMobilityService/action|Atualizar serviço de mobilidade|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/write|Criar ou atualizar os itens protegidos|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/delete|Excluir mapeamentos de contêiner de proteção|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/read|Ler mapeamentos de contêiner de proteção|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/remove/action|Remover o mapeamento de contêiner de proteção|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/write|Criar ou atualizar os mapeamentos de contêiner de proteção|
|/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action|Alterar contêiner de proteção|
|/vaults/replicationFabrics/replicationProtectionContainers/write|Criar ou atualizar contêineres de proteção|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete|Excluir provedor dos Serviços de Recuperação|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/read|Ler provedores de serviços de recuperação|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/ refreshProvider/action|Atualizar provedor|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action|Remover o provedor dos Serviços de Recuperação|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/write|Criar ou atualizar Provedores de Serviços de Recuperação|
|/vaults/replicationFabrics/replicationStorageClassifications/read|Ler classificações de armazenamento|
|/vaults/replicationFabrics/replicationStorageClassifications/ replicationStorageClassificationMappings/delete|Excluir mapeamentos de classificação de armazenamento|
|/vaults/replicationFabrics/replicationStorageClassifications/ replicationStorageClassificationMappings/read|Ler mapeamentos de classificação de armazenamento|
|/vaults/replicationFabrics/replicationStorageClassifications/ replicationStorageClassificationMappings/write|Criar ou atualizar os mapeamentos de classificação de armazenamento|
|/vaults/replicationFabrics/replicationvCenters/delete|Excluir trabalhos|
|/vaults/replicationFabrics/replicationvCenters/read|Ler todos os trabalhos|
|/vaults/replicationFabrics/replicationvCenters/write|Criar ou atualizar os trabalhos|
|/vaults/replicationFabrics/write|Criar ou atualizar malhas|
|/vaults/replicationJobs/cancel/action|Cancelar trabalho|
|/vaults/replicationJobs/read|Ler todos os trabalhos|
|/vaults/replicationJobs/restart/action|Reiniciar o trabalho|
|/vaults/replicationJobs/resume/action|Retomar o trabalho|
|/vaults/replicationPolicies/delete|Excluir políticas|
|/vaults/replicationPolicies/read|Ler políticas|
|/vaults/replicationPolicies/write|Criar ou atualizar as políticas|
|/vaults/replicationRecoveryPlans/delete|Excluir planos de recuperação|
|/vaults/replicationRecoveryPlans/failoverCommit/action|Plano de recuperação de confirmação de failover|
|/vaults/replicationRecoveryPlans/plannedFailover/action|Plano de recuperação de failover planejado|
|/vaults/replicationRecoveryPlans/read|Ler planos de recuperação|
|/vaults/replicationRecoveryPlans/reProtect/action|Proteja plano de recuperação novamente|
|/vaults/replicationRecoveryPlans/testFailover/action|Testar plano de recuperação de failover|
|/vaults/replicationRecoveryPlans/testFailoverCleanup/action|Testar plano de recuperação de limpeza do failover|
|/vaults/replicationRecoveryPlans/unplannedFailover/action|Plano de recuperação de failover|
|/vaults/replicationRecoveryPlans/write|Criar ou atualizar planos de recuperação|
|/Vaults/tokenInfo/read|Retornar a informação de token para o cofre dos Serviços de Recuperação.|
|/vaults/usages/read|Ler usos de cofre|
|/Vaults/usages/read|Retornar os detalhes de uso para um cofre dos Serviços de Recuperação.|
|/Vaults/vaultTokens/read|A operação de token do cofre pode ser usada a fim de obter o token do cofre para operações de back-end no nível do cofre.|
|/Vaults/write|A operação Criar cofre cria um recurso do Azure do tipo 'cofre'|

## <a name="microsoftrelay"></a>Microsoft.Relay

| Operação | DESCRIÇÃO |
|---|---|
|/checkNameAvailability/action|Verificar a disponibilidade do namespace em determinada assinatura.|
|/checkNamespaceAvailability/action|Verificar a disponibilidade do namespace em determinada assinatura. Essa API está preterida, use CheckNameAvailabiltiy.|
|/namespaces/authorizationRules/action|Atualizar regra de autorização de namespace. Essa API está preterida. Use uma chamada PUT para atualizar a regra de autorização de namespace. Esta operação não tem suporte na versão da API 2017-04-01.|
|/namespaces/authorizationRules/delete|Excluir regra de autorização de namespace. A regra de autorização do namespace padrão não pode ser excluída. |
|/namespaces/authorizationRules/listkeys/action|Obter a cadeia de conexão para o namespace|
|/namespaces/authorizationRules/read|Obter a lista de descrição de regras de autorização de namespaces.|
|/namespaces/authorizationRules/regenerateKeys/action|Regenerar a chave primária ou secundária para o recurso|
|/namespaces/authorizationRules/write|Criar regras de autorização no nível do namespace e atualizar suas propriedades. Os direitos de acesso das regras de autorização; as chaves primárias e secundárias podem ser atualizadas.|
|/namespaces/Delete|Excluir o recurso de namespace|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|Obter as chaves de regras de autorização para o namespace primário de Recuperação de Desastre|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|Obter regras de autorização do namespace primário de recuperação de desastre|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|Desabilitar a Recuperação de desastre e interromper a replicação de alterações de namespaces primários para secundários.|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|Verificar a disponibilidade de alias de namespace sob determinada assinatura.|
|/namespaces/disasterRecoveryConfigs/delete|Excluir a configuração da recuperação de desastre associada ao namespace. Essa operação somente pode ser invocada por meio do namespace primário.|
|/namespaces/disasterRecoveryConfigs/failover/action|Invocar um failover de GEO DR e reconfigurar o alias de namespace para apontar para o namespace secundário.|
|/namespaces/disasterRecoveryConfigs/read|Obter a configuração da Recuperação de Desastre associada ao namespace.|
|/namespaces/disasterRecoveryConfigs/write|Criar ou atualizar a configuração de Recuperação de Desastre associada ao namespace.|
|/namespaces/HybridConnections/authorizationRules/action|Operação para atualizar o HybridConnection. Esta operação não tem suporte na versão da API 2017-04-01. Regras de autorização. Use uma chamada PUT para atualizar a Regra de Autorização.|
|/namespaces/HybridConnections/authorizationRules/delete|Operação para excluir regras de autorização HybridConnection|
|/namespaces/HybridConnections/authorizationRules/listkeys/action|Obter a cadeia de conexão para HybridConnection|
|/namespaces/HybridConnections/authorizationRules/read| Obter a lista das Regras d Autorização do HybridConnection|
|/namespaces/HybridConnections/authorizationRules/regeneratekeys/action|Regenerar a chave primária ou secundária para o recurso|
|/namespaces/HybridConnections/authorizationRules/write|Criar regras de autorização HybridConnection e atualizar suas propriedades. Os Direitos de Acesso das Regras de Autorização podem ser atualizados.|
|/namespaces/HybridConnections/Delete|Operação para excluir o recurso HybridConnection|
|/namespaces/HybridConnections/read|Obter lista de descrições de recursos HybridConnection|
|/namespaces/HybridConnections/write|Criar ou atualizar propriedades HybridConnection.|
|/namespaces/messagingPlan/read|Obter o Plano de Mensagens para um namespace. Essa API está preterida. As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso Namespace (pai) nas versões posteriores da API. Esta operação não tem suporte na versão da API 2017-04-01.|
|/namespaces/messagingPlan/write|Atualizar o Plano de Mensagens para um namespace. Essa API está preterida. As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso Namespace (pai) nas versões posteriores da API. Esta operação não tem suporte na versão da API 2017-04-01.|
|/namespaces/operationresults/read|Obter o status da operação de Namespace|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|Obter lista de métrica de descrições de recurso de métrica do namespace|
|/namespaces/read|Obter a lista de descrição do recurso de namespace|
|/namespaces/WcfRelays/authorizationRules/action|Operação para atualizar o WcfRelay. Esta operação não tem suporte na versão da API 2017-04-01. Regras de autorização. Use uma chamada PUT para atualizar a Regra de Autorização.|
|/namespaces/WcfRelays/authorizationRules/delete|Operação para excluir regras de autorização WcfRelay|
|/namespaces/WcfRelays/authorizationRules/listkeys/action|Obter a cadeia de conexão para WcfRelay|
|/namespaces/WcfRelays/authorizationRules/read| Obter a lista de Regras de Autorização do WcfRelay|
|/namespaces/WcfRelays/authorizationRules/regeneratekeys/action|Regenerar a chave primária ou secundária para o recurso|
|/namespaces/WcfRelays/authorizationRules/write|Criar regras de autorização WcfRelay e atualizar suas propriedades. Os Direitos de Acesso das Regras de Autorização podem ser atualizados.|
|/namespaces/WcfRelays/Delete|Operação para excluir o recurso WcfRelay|
|/namespaces/WcfRelays/read|Obter lista de descrições de recursos WcfRelay|
|/namespaces/WcfRelays/write|Criar ou atualizar propriedades WcfRelay.|
|/namespaces/write|Criar um recurso de namespace e atualizar suas propriedades. Marcas e Capacidade do namespace são as propriedades que podem ser atualizadas.|
|/operations/read|Obter Operações|
|/register/action|Registrar a assinatura do provedor de recursos Relay e permitir a criação de recursos Relay|
|/unregister/action|Registrar a assinatura do provedor de recursos Relay e permitir a criação de recursos Relay|

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

| Operação | DESCRIÇÃO |
|---|---|
|/AvailabilityStatuses/current/read|Obter o status de disponibilidade do recurso especificado|
|/AvailabilityStatuses/read|Obter os status de disponibilidade para todos os recursos no escopo especificado|
|/healthevent/action|Indicar a alteração no estado de integridade para o recurso especificado|
|/healthevent/Activated/action|Indicar a alteração no estado de integridade para o recurso especificado|
|/healthevent/InProgress/action|Indicar a alteração no estado de integridade para o recurso especificado|
|/healthevent/Pending/action|Indicar a alteração no estado de integridade para o recurso especificado|
|/healthevent/Resolved/action|Indicar a alteração no estado de integridade para o recurso especificado|
|/healthevent/Updated/action|Indicar a alteração no estado de integridade para o recurso especificado|
|/register/action|Registrar a assinatura do Microsoft ResourceHealth|

## <a name="microsoftresources"></a>Microsoft.Resources

| Operação | DESCRIÇÃO |
|---|---|
|/checkResourceName/action|Verificar a validade do nome do recurso.|
|/deployments/cancel/action|Cancelar uma implantação.|
|/deployments/delete|Excluir uma implantação.|
|/deployments/operations/read|Obter ou lista operações de implantação.|
|/deployments/read|Obter ou lista implantações.|
|/deployments/validate/action|Validar uma implantação.|
|/deployments/write|Criar ou atualizar uma implantação.|
|/links/delete|Excluir um link de recurso.|
|/links/read|Obter ou listar links de recursos.|
|/links/write|Criar ou atualizar um link de recurso.|
|/marketplace/purchase/action|Comprar um recurso do marketplace.|
|/providers/read|Obter a lista de provedores.|
|/resources/read|Obter a lista de recursos com base em filtros.|
|/subscriptions/locations/read|Obter a lista de locais com suporte.|
|/subscriptions/operationresults/read|Obter os resultados da operação da assinatura.|
|/subscriptions/providers/read|Obter ou listar provedores de recursos.|
|/subscriptions/read|Obter a lista de assinaturas.|
|/subscriptions/resourceGroups/delete|Excluir um grupo de recursos e todos os seus recursos.|
|/subscriptions/resourcegroups/deployments/operations/read|Obter ou lista operações de implantação.|
|/subscriptions/resourcegroups/deployments/operationstatuses/read|Obter ou listar o status da operação de implantação.|
|/subscriptions/resourcegroups/deployments/read|Obter ou lista implantações.|
|/subscriptions/resourcegroups/deployments/write|Criar ou atualizar uma implantação.|
|/subscriptions/resourceGroups/moveResources/action|Mover recursos de um grupo de recursos para outro.|
|/subscriptions/resourceGroups/read|Obter ou listar de grupos de recursos.|
|/subscriptions/resourcegroups/resources/read|Obter os recursos do grupo de recursos.|
|/subscriptions/resourceGroups/validateMoveResources/action|Validar a movimentação de recursos de um grupo de recursos para outro.|
|/subscriptions/resourceGroups/write|Criar ou atualizar um grupo de recursos.|
|/subscriptions/resources/read|Obter os recursos de uma assinatura.|
|/subscriptions/tagNames/delete|Excluir uma marcação de assinatura.|
|/subscriptions/tagNames/read|Obter ou listar marcações de assinatura.|
|/subscriptions/tagNames/tagValues/delete|Excluir um valor de marcação de assinatura.|
|/subscriptions/tagNames/tagValues/read|Obter ou listar valores de marcação de assinatura.|
|/subscriptions/tagNames/tagValues/write|Adicionar um valor de marcação de assinatura.|
|/subscriptions/tagNames/write|Adicionar uma marcação de assinatura.|
|/tenants/read|Obter a lista de locatários.|

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

| Operação | DESCRIÇÃO |
|---|---|
|/jobcollections/delete|Excluir coleção de trabalhos.|
|/jobcollections/disable/action|Desabilita coleção de trabalhos.|
|/jobcollections/enable/action|Habilita coleção de trabalhos.|
|/jobcollections/jobs/delete|Excluir trabalho.|
|/jobcollections/jobs/generateLogicAppDefinition/action|Gerar a definição de aplicativo lógico com base em um trabalho do Agendador.|
|/jobcollections/jobs/jobhistories/read|Obtém o histórico de trabalhos.|
|/jobcollections/jobs/read|Obter o trabalho.|
|/jobcollections/jobs/run/action|Executa o trabalho.|
|/jobcollections/jobs/write|Criar ou atualizar o trabalho.|
|/jobcollections/read|Obter coleção de trabalhos|
|/jobcollections/write|Criar ou atualizar a coleção de trabalhos.|

## <a name="microsoftsearch"></a>Microsoft.Search

| Operação | DESCRIÇÃO |
|---|---|
|/checkNameAvailability/action|Verificar a disponibilidade do nome do serviço.|
|/register/action|Registrar a assinatura do provedor de recursos de pesquisa e permitir a criação de serviços de pesquisa.|
|/searchServices/createQueryKey/action|Criar a chave de consulta.|
|/searchServices/delete|Excluir o serviço de pesquisa.|
|/searchServices/diagnosticSettings/read|Obter a leitura da configuração de diagnóstico para o recurso|
|/searchServices/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o recurso|
|/searchServices/listAdminKeys/action|Ler as chaves de administração.|
|/searchServices/logDefinitions/read|Obter os logs disponíveis para o serviço de pesquisa|
|/searchServices/metricDefinitions/read|Obter as métricas disponíveis para o serviço de pesquisa|
|/searchServices/queryKey/delete|Excluir a chave de consulta.|
|/searchServices/queryKey/read|Ler as chaves de consulta.|
|/searchServices/read|Ler o serviço de pesquisa.|
|/searchServices/regenerateAdminKey/action|Regenerar a chave de administração.|
|/searchServices/start/action|Iniciar o serviço de pesquisa.|
|/searchServices/stop/action|Interromper o serviço de pesquisa.|
|/searchServices/write|Criar ou atualizar o serviço de pesquisa.|

## <a name="microsoftsecurity"></a>Microsoft.Security

| Operação | DESCRIÇÃO |
|---|---|
|/alerts/read|Obter todos os alertas de segurança disponíveis|
|/applicationWhitelistings/read|Obter a lista de liberações do aplicativo|
|/applicationWhitelistings/write|Criar uma nova lista de exceções do aplicativo ou atualizar uma existente|
|/complianceResults/read|Obter os resultados de conformidade para o recurso|
|/locations/alerts/activate/action|Ativar um alerta de segurança|
|/locations/alerts/dismiss/action|Ignorar um alerta de segurança|
|/locations/alerts/read|Obter todos os alertas de segurança disponíveis|
|/locations/jitNetworkAccessPolicies/initiate/action|Iniciar uma política de acesso de rede just-in-time|
|/locations/jitNetworkAccessPolicies/read|Obter as políticas de acesso de rede just-in-time|
|/locations/jitNetworkAccessPolicies/write|Criar uma nova política de acesso de rede just-in-time ou atualizar uma existente|
|/locations/read|Obter o local dos dados de segurança|
|/locations/tasks/activate/action|Ativar recomendação de segurança|
|/locations/tasks/dismiss/action|Ignorar recomendação de segurança|
|/locations/tasks/read|Obter todas as recomendações de segurança disponíveis|
|/locations/tasks/resolve/action|Resolver uma recomendação de segurança|
|/locations/tasks/start/action|Iniciar uma recomendação de segurança|
|/policies/read|Obter a política de segurança|
|/policies/write|Atualizar a política de segurança|
|/pricings/delete|Excluir as configurações de preço para o escopo|
|/pricings/read|Obter as configurações de preço para o escopo|
|/pricings/write|Atualizar as configurações de preço para o escopo|
|/register/action|Registrar a assinatura da Central de Segurança do Azure|
|/securityContacts/delete|Excluir o contato de segurança|
|/securityContacts/read|Obter o contato de segurança|
|/securityContacts/write|Atualizar o contato de segurança|
|/securitySolutions/delete|Excluir uma solução de segurança|
|/securitySolutions/read|Obter as soluções de segurança|
|/securitySolutions/write|Criar uma nova solução de segurança ou atualizar uma existente|
|/securitySolutionsReferenceData/read|Obter dados de referência das soluções de segurança|
|/securityStatuses/read|Obter status de integridade da segurança para recursos do Azure|
|/securityStatusesSummaries/read|Obter os resumos de status de segurança para o escopo|
|/tasks/read|Obter todas as recomendações de segurança disponíveis|
|/webApplicationFirewalls/delete|Excluir um firewall do aplicativo Web|
|/webApplicationFirewalls/read|Obter firewalls do aplicativo Web|
|/webApplicationFirewalls/write|Criar um novo firewall do aplicativo Web ou atualizar um existente|
|/workspaceSettings/connect/action|Alterar configurações de reconexão das configurações do espaço de trabalho|
|/workspaceSettings/delete|Excluir as configurações do espaço de trabalho|
|/workspaceSettings/read|Obter as configurações do espaço de trabalho|
|/workspaceSettings/write|Atualizar as configurações do espaço de trabalho|

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

| Operação | DESCRIÇÃO |
|---|---|
|/checkNameAvailability/action|Verificar a disponibilidade do namespace em determinada assinatura.|
|/checkNamespaceAvailability/action|Verificar a disponibilidade do namespace em determinada assinatura. Essa API está preterida, use CheckNameAvailabiltiy.|
|/namespaces/authorizationRules/action|Atualizar regra de autorização de namespace. Essa API está preterida. Use uma chamada PUT para atualizar a regra de autorização de namespace. Esta operação não tem suporte na versão da API 2017-04-01.|
|/namespaces/authorizationRules/delete|Excluir regra de autorização de namespace. A regra de autorização do namespace padrão não pode ser excluída. |
|/namespaces/authorizationRules/listkeys/action|Obter a cadeia de conexão para o namespace|
|/namespaces/authorizationRules/read|Obter a lista de descrição de regras de autorização de namespaces.|
|/namespaces/authorizationRules/regenerateKeys/action|Regenerar a chave primária ou secundária para o recurso|
|/namespaces/authorizationRules/write|Criar regras de autorização no nível do namespace e atualizar suas propriedades. Os direitos de acesso das regras de autorização; as chaves primárias e secundárias podem ser atualizadas.|
|/namespaces/Delete|Excluir o recurso de namespace|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|Obter as chaves de regras de autorização para o namespace primário de Recuperação de Desastre|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|Obter regras de autorização do namespace primário de recuperação de desastre|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|Desabilitar a Recuperação de desastre e interromper a replicação de alterações de namespaces primários para secundários.|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|Verificar a disponibilidade de alias de namespace sob determinada assinatura.|
|/namespaces/disasterRecoveryConfigs/delete|Excluir a configuração da recuperação de desastre associada ao namespace. Essa operação somente pode ser invocada por meio do namespace primário.|
|/namespaces/disasterRecoveryConfigs/failover/action|Invocar um failover de GEO DR e reconfigurar o alias de namespace para apontar para o namespace secundário.|
|/namespaces/disasterRecoveryConfigs/read|Obter a configuração da Recuperação de Desastre associada ao namespace.|
|/namespaces/disasterRecoveryConfigs/write|Criar ou atualizar a configuração de Recuperação de Desastre associada ao namespace.|
|/namespaces/eventGridFilters/delete|Excluir o filtro de Grade de Eventos associado ao namespace.|
|/namespaces/eventGridFilters/read|Obter o filtro de Grade de Eventos associado ao namespace.|
|/namespaces/eventGridFilters/write|Criar ou atualizar o filtro da Grade de Eventos associado ao namespace.|
|/namespaces/eventhubs/read|Obter lista de descrições de recursos de EventHub|
|/namespaces/messagingPlan/read|Obter o Plano de Mensagens para um namespace. Essa API está preterida. As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso Namespace (pai) nas versões posteriores da API. Esta operação não tem suporte na versão da API 2017-04-01.|
|/namespaces/messagingPlan/write|Atualizar o Plano de Mensagens para um namespace. Essa API está preterida. As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso Namespace (pai) nas versões posteriores da API. Esta operação não tem suporte na versão da API 2017-04-01.|
|/namespaces/migrate/action|Migrar operação de namespace|
|/namespaces/operationresults/read|Obter o status da operação de Namespace|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/read|Obter lista de descrições de recurso de configurações de diagnóstico do namespace|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/write|Obter lista de descrições de recurso de configurações de diagnóstico do namespace|
|/namespaces/providers/Microsoft.Insights/logDefinitions/read|Obter lista de descrições do recurso de log do namespace|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|Obter lista de métrica de descrições de recurso de métrica do namespace|
|/namespaces/queues/authorizationRules/action|Operação para atualizar a Fila. Esta operação não tem suporte na versão da API 2017-04-01. Regras de autorização. Use uma chamada PUT para atualizar a Regra de Autorização.|
|/namespaces/queues/authorizationRules/delete|Operação para excluir regras de autorização de fila|
|/namespaces/queues/authorizationRules/listkeys/action|Obter a cadeia de conexão para fila|
|/namespaces/queues/authorizationRules/read| Obter a lista de regras de autorização de fila|
|/namespaces/queues/authorizationRules/regenerateKeys/action|Regenerar a chave primária ou secundária para o recurso|
|/namespaces/queues/authorizationRules/write|Criar regras de autorização de fila e atualizar suas propriedades. Os Direitos de Acesso das Regras de Autorização podem ser atualizados.|
|/namespaces/queues/Delete|Operação para excluir o recurso Queue|
|/namespaces/queues/read|Obter lista de descrições do recurso Queue|
|/namespaces/queues/write|Criar ou atualizar propriedades Queue.|
|/namespaces/read|Obter a lista de descrição do recurso de namespace|
|/namespaces/topics/authorizationRules/action|Operação para atualizar o Tópico. Esta operação não tem suporte na versão da API 2017-04-01. Regras de autorização. Use uma chamada PUT para atualizar a Regra de Autorização.|
|/namespaces/topics/authorizationRules/delete|Operação para excluir regras de autorização do tópico|
|/namespaces/topics/authorizationRules/listkeys/action|Obter a cadeia de conexão para tópico|
|/namespaces/topics/authorizationRules/read| Obter a lista de regras de autorização do tópico|
|/namespaces/topics/authorizationRules/regenerateKeys/action|Regenerar a chave primária ou secundária para o recurso|
|/namespaces/topics/authorizationRules/write|Criar regras de autorização do tópico e atualizar suas propriedades. Os Direitos de Acesso das Regras de Autorização podem ser atualizados.|
|/namespaces/topics/Delete|Operação para excluir o recurso Topic|
|/namespaces/topics/read|Obter lista de descrições de recurso Topic|
|/namespaces/topics/subscriptions/Delete|Operação para excluir o recurso TopicSubscription|
|/namespaces/topics/subscriptions/read|Obter lista de descrições do recurso TopicSubscription|
|/namespaces/topics/subscriptions/rules/Delete|Operação para excluir o recurso Rule|
|/namespaces/topics/subscriptions/rules/read|Obter lista de descrições do recurso Rule|
|/namespaces/topics/subscriptions/rules/write|Criar ou atualizar propriedades Rule.|
|/namespaces/topics/subscriptions/write|Criar ou atualizar propriedades TopicSubscription.|
|/namespaces/topics/write|Criar ou atualizar propriedades Topic.|
|/namespaces/write|Criar um recurso de namespace e atualizar suas propriedades. Marcas e Capacidade do namespace são as propriedades que podem ser atualizadas.|
|/operations/read|Obter Operações|
|/register/action|Registrar a assinatura do provedor de recursos ServiceBus e permitir a criação de recursos ServiceBus|
|/sku/read|Obter lista de Descrições de Recursos de SKU|
|/sku/regions/read|Obter lista de Descrições de Recursos SkuRegions|
|/unregister/action|Registrar a assinatura do provedor de recursos ServiceBus e permitir a criação de recursos ServiceBus|

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

| Operação | DESCRIÇÃO |
|---|---|
|/clusters/applications/delete|Excluir qualquer Aplicativo|
|/clusters/applications/read|Ler qualquer Aplicativo|
|/clusters/applications/services/delete|Excluir qualquer Serviço|
|/clusters/applications/services/partitions/read|Ler qualquer Partição|
|/clusters/applications/services/partitions/replicas/read|Ler qualquer Réplica|
|/clusters/applications/services/read|Ler qualquer Serviço|
|/clusters/applications/services/statuses/read|Ler qualquer Status de Serviço|
|/clusters/applications/services/write|Criar ou atualizar qualquer Serviço|
|/clusters/applications/write|Criar ou atualizar qualquer Aplicativo|
|/clusters/applicationTypes/delete|Excluir qualquer Tipo de Aplicativo|
|/clusters/applicationTypes/read|Ler qualquer Tipo de Aplicativo|
|/clusters/applicationTypes/versions/delete|Excluir qualquer Versão de Tipo de Aplicativo|
|/clusters/applicationTypes/versions/read|Ler qualquer Versão de Tipo de Aplicativo|
|/clusters/applicationTypes/versions/write|Criar ou atualizar qualquer Versão de Tipo de Aplicativo|
|/clusters/applicationTypes/write|Criar ou atualizar qualquer Tipo de Aplicativo|
|/clusters/delete|Excluir qualquer Cluster|
|/clusters/nodes/read|Ler qualquer Nó|
|/clusters/read|Ler qualquer Cluster|
|/clusters/statuses/read|Ler qualquer Status do Cluster|
|/clusters/write|Criar ou atualizar qualquer Cluster|
|/locations/clusterVersions/read|Ler qualquer Versão do Cluster|
|/locations/environments/clusterVersions/read|Ler qualquer Versão do Cluster de um ambiente específico|
|/locations/operationresults/read|Ler qualquer Resultado de Operação|
|/locations/operations/read|Ler qualquer Operação por Local|
|/operations/read|Ler qualquer Operação Disponível|
|/register/action|Registrar qualquer Ação|

## <a name="microsoftsolutions"></a>Microsoft.Solutions

| Operação | DESCRIÇÃO |
|---|---|
|/applicationDefinitions/delete|Remover uma definição de aplicativo.|
|/applicationDefinitions/read|Recuperar uma lista de definições de aplicativos.|
|/applicationDefinitions/write|Adicionar ou modificar uma definição de aplicativo.|
|/applications/delete|Remover um aplicativo.|
|/applications/read|Recuperar uma lista de aplicativos.|
|/applications/write|Criar um aplicativo.|
|/locations/operationStatuses/read|Ler o status da operação do recurso.|
|/register/action|Registrar para Soluções.|

## <a name="microsoftsql"></a>Microsoft.Sql

| Operação | DESCRIÇÃO |
|---|---|
|/checkNameAvailability/action|Verificar se o nome do servidor fornecido está disponível para provisionamento no mundo inteiro para uma determinada assinatura.|
|/locations/auditingSettingsAzureAsyncOperation/read|Recuperar resultado da política de auditoria de blob de servidor estendida Definir operação|
|/locations/auditingSettingsOperationResults/read|Recuperar o resultado da operação Definir da política de auditoria do blob do servidor|
|/locations/capabilities/read|Obter as capacidades para essa assinatura em um determinado local|
|/locations/databaseAzureAsyncOperation/read|Obter o status de uma operação de banco de dados.|
|/locations/databaseOperationResults/read|Obter o status de uma operação de banco de dados.|
|/locations/deletedServerAsyncOperation/read|Obter operações em andamento no servidor excluído|
|/locations/deletedServerOperationResults/read|Obter operações em andamento no servidor excluído|
|/locations/deletedServers/read|Retornar a lista de servidores excluídos ou obter as propriedades para o servidor excluído especificado.|
|/locations/deletedServers/recover/action|Recuperar um servidor excluído|
|/locations/deleteVirtualNetworkOrSubnets/action|Excluir regras de rede virtual associadas a uma rede virtual ou sub-rede|
|/locations/elasticPoolAzureAsyncOperation/read|Obter a operação assíncrona do Azure para uma operação assíncrona de pool elástico|
|/locations/elasticPoolOperationResults/read|Obter o resultado de uma operação de pool elástico.|
|/locations/extendedAuditingSettingsAzureAsyncOperation/read|Recuperar resultado da política de auditoria de blob de servidor estendida Definir operação|
|/locations/extendedAuditingSettingsOperationResults/read|Recuperar resultado da política de auditoria de blob de servidor estendida Definir operação|
|/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action|Concluir a operação de restauração do banco de dados gerenciado|
|/locations/managedTransparentDataEncryptionAzureAsyncOperation/read|Obter operações em andamento na criptografia de dados transparente do banco de dados gerenciado|
|/locations/managedTransparentDataEncryptionOperationResults/read|Obter operações em andamento na criptografia de dados transparente do banco de dados gerenciado|
|/locations/read|Obter os locais disponíveis para uma determinada assinatura|
|/locations/syncAgentOperationResults/read|Recuperar resultado da operação do recurso do agente de sincronização|
|/locations/syncDatabaseIds/read|Recuperar as IDs do banco de dados de sincronização para uma assinatura e região específica|
|/locations/syncGroupOperationResults/read|Recuperar resultado da operação do recurso de grupo de sincronização|
|/locations/syncMemberOperationResults/read|Recuperar resultado da operação do recurso de membro de sincronização|
|/locations/usages/read|Obter uma coleção de métricas de uso para essa assinatura em um local|
|/locations/virtualNetworkRulesAzureAsyncOperation/read|Retornar os detalhes da operação assíncrona de regras de rede virtual especificada do Azure |
|/locations/virtualNetworkRulesOperationResults/read|Retornar os detalhes da operação de regras de rede virtual especificada |
|/managedInstances/administrators/delete|Excluir um administrador existente da instância gerenciada.|
|/managedInstances/administrators/read|Obter uma lista de administradores de instância gerenciada.|
|/managedInstances/administrators/write|Criar ou atualizar o administrador da instância gerenciada com os parâmetros especificados.|
|/managedInstances/databases/delete|Excluir um banco de dados gerenciado existente|
|/managedInstances/databases/read|Obter o banco de dados gerenciado existente|
|/managedInstances/databases/securityAlertPolicies/read|Recuperar detalhes da política de detecção de ameaças do banco de dados configurada em um determinado banco de dados gerenciado|
|/managedInstances/databases/securityAlertPolicies/write|Alterar a política de detecção de ameaças do banco de dados para um determinado banco de dados gerenciado|
|/managedInstances/databases/securityEvents/read|Recuperar os eventos de segurança do banco de dados gerenciado|
|/managedInstances/databases/transparentDataEncryption/read|Recuperar detalhes do Transparent Data Encryption do banco de dados em um determinado banco de dados gerenciado|
|/managedInstances/databases/transparentDataEncryption/write|Alterar o Transparent Data Encryption do banco de dados para um determinado banco de dados gerenciado|
|/managedInstances/databases/write|Cria um novo banco de dados ou atualiza um banco de dados existente.|
|/managedInstances/delete|Excluir uma instância gerenciada existente.|
|/managedInstances/metricDefinitions/read|Obter definições de métrica de instância gerenciada|
|/managedInstances/metrics/read|Obter métricas de instância gerenciada|
|/managedInstances/read|Retornar a lista de instâncias gerenciadas ou obter as propriedades para a instância gerenciada especificada.|
|/managedInstances/securityAlertPolicies/read|Recuperar detalhes da política de detecção de ameaças do servidor gerenciado configurada em um determinado servidor gerenciado|
|/managedInstances/securityAlertPolicies/write|Alterar a política de detecção de ameaças do servidor gerenciado para um determinado servidor gerenciado|
|/managedInstances/write|Criar uma instância gerenciada com os parâmetros especificados ou atualizar as propriedades ou marcas para a instância gerenciada especificada.|
|/operations/read|Obter as operações REST disponíveis|
|/register/action|Registrar a assinatura do provedor de recursos do Banco de Dados SQL do Microsoft Azure e habilitar a criação do Banco de Dados SQL do Microsoft Azure.|
|/servers/administratorOperationResults/read|Obter operações em andamento nos administradores do servidor|
|/servers/administrators/delete|Excluir administrador do servidor|
|/servers/administrators/read|Recuperar detalhes do administrador do servidor|
|/servers/administrators/write|Criar ou atualizar o administrador do servidor|
|/servers/advisors/read|Retornar a lista de consultores disponíveis para o servidor|
|/servers/advisors/recommendedActions/read|Retornar a lista de ações recomendadas do assistente especificado para o servidor|
|/servers/advisors/recommendedActions/write|Aplicar a ação recomendada no servidor|
|/servers/advisors/write|Atualizar status de execução automática de um assistente no nível do servidor.|
|/servers/auditingPolicies/read|Recuperar detalhes da política de auditoria de tabela do servidor padrão configurada em determinado servidor|
|/servers/auditingPolicies/write|Alterar a auditoria da tabela de servidor padrão para determinado servidor|
|/servers/auditingSettings/operationResults/read|Recuperar o resultado da operação Definir da política de auditoria do blob do servidor|
|/servers/auditingSettings/read|Recuperar detalhes da política de auditoria de blob do servidor configurada em determinado servidor|
|/servers/auditingSettings/write|Alterar a auditoria de blob de servidor para determinado servidor|
|/servers/automaticTuning/read|Retornar as configurações de ajuste automático do servidor|
|/servers/automaticTuning/write|Atualizar as configurações de ajuste automático do servidor e retornar as configurações atualizadas|
|/servers/backupLongTermRetentionVaults/delete|Excluir um cofre de arquivos de backup existente.|
|/servers/backupLongTermRetentionVaults/read|Essa operação é usada para obter um cofre de retenção de backup de longo prazo. Ela retorna informações sobre o cofre registrado para esse servidor|
|/servers/backupLongTermRetentionVaults/write|Esta operação é utilizada para registrar uma retenção de backup em longo prazo em um servidor|
|/servers/communicationLinks/delete|Excluir um link de comunicação com o servidor existente.|
|/servers/communicationLinks/read|Retornar a lista de links de comunicação de um servidor especificado.|
|/servers/communicationLinks/write|Criar ou atualizar um link de comunicação com o servidor.|
|/servers/connectionPolicies/read|Retornar a lista de política de conexão do servidor de um servidor especificado.|
|/servers/connectionPolicies/write|Criar ou atualizar uma política de conexão do servidor.|
|/servers/databases/advisors/read|Retornar a lista de consultores disponíveis para o banco de dados|
|/servers/databases/advisors/recommendedActions/read|Retornar a lista de ações recomendadas do assistente especificado para o banco de dados|
|/servers/databases/advisors/recommendedActions/write|Aplicar a ação recomendada no banco de dados|
|/servers/databases/advisors/write|Atualizar status de um assistente no nível do banco de dados.|
|/servers/databases/auditingPolicies/read|Recuperar detalhes da política de auditoria de tabela configurada em determinado servidor|
|/servers/databases/auditingPolicies/write|Alterar a política de auditoria de tabela para determinado banco de dados|
|/servers/databases/auditingSettings/read|Recuperar detalhes da política de auditoria de blob configurada em determinado servidor|
|/servers/databases/auditingSettings/write|Alterar a política de auditoria de blob para determinado banco de dados|
|/servers/databases/auditRecords/read|Recuperar os registros de auditoria do blob do banco de dados|
|/servers/databases/automaticTuning/read|Retornar as configurações de ajuste automático de um banco de dados|
|/servers/databases/automaticTuning/write|Atualizar as configurações de ajuste automático de um banco de dados e retornar as configurações atualizadas|
|/servers/databases/azureAsyncOperation/read|Obter o status de uma operação de banco de dados.|
|/servers/databases/backupLongTermRetentionPolicies/read|Retornar a lista de políticas de arquivamento de backup de um banco de dados especificado.|
|/servers/databases/backupLongTermRetentionPolicies/write|Criar ou atualizar uma política de arquivamento de backup de banco de dados.|
|/servers/databases/connectionPolicies/read|Recuperar detalhes da política de conexão configurada em determinado servidor|
|/servers/databases/connectionPolicies/write|Alterar política de conexão para determinado banco de dados|
|/servers/databases/dataMaskingPolicies/read|Retornar a lista de políticas de máscara de dados do banco de dados.|
|/servers/databases/dataMaskingPolicies/rules/delete|Excluir regra de política de máscara de dados para um determinado banco de dados|
|/servers/databases/dataMaskingPolicies/rules/read|Recuperar detalhes da regra de política de mascaramento de dados configurada em determinado servidor|
|/servers/databases/dataMaskingPolicies/rules/write|Alterar regra de política de mascaramento de dados para determinado banco de dados|
|/servers/databases/dataMaskingPolicies/write|Alterar política de mascaramento de dados para determinado banco de dados|
|/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read|Retornar as informações de etapa de consulta distribuída da consulta de data warehouse para ID da etapa selecionada|
|/servers/databases/dataWarehouseQueries/read|Retornar as informações de consulta de distribuição do data warehouse para a ID de consulta selecionada|
|/servers/databases/dataWarehouseUserActivities/read|Recuperar as atividades do usuário de uma instância do SQL Data Warehouse que inclui consultas suspensas e em execução|
|/servers/databases/delete|Excluir um banco de dados existente.|
|/servers/databases/export/action|Exportar Banco de Dados SQL do Azure|
|/servers/databases/extendedAuditingSettings/read|Recuperar detalhes da política de auditoria de blob estendida configurada em um determinado banco de dados|
|/servers/databases/extendedAuditingSettings/write|Alterar a política de auditoria de blob estendida para um determinado banco de dados|
|/servers/databases/extensions/read|Obter uma coleção de extensões para o banco de dados.|
|/servers/databases/extensions/write|Alterar a extensão de um determinado banco de dados|
|/servers/databases/geoBackupPolicies/read|Recuperar políticas do backup geográfico para um determinado banco de dados|
|/servers/databases/geoBackupPolicies/write|Criar ou atualizar uma política do backup geográfico de banco de dados|
|/servers/databases/importExportOperationResults/read|Obter operações de importação/exportação em andamento|
|/servers/databases/metricDefinitions/read|Retornar tipos de métricas que estão disponíveis para bancos de dados|
|/servers/databases/metrics/read|Métricas de retorno para bancos de dados|
|/servers/databases/move/action|Renomear Banco de Dados SQL do Azure|
|/servers/databases/operationResults/read|Obter o status de uma operação de banco de dados.|
|/servers/databases/operations/cancel/action|Cancelar a operação assíncrona pendente do Banco de Dados SQL do Azure que ainda não está concluída.|
|/servers/databases/operations/read|Retornar a lista de operações realizadas no banco de dados|
|/servers/databases/pause/action|Pausar Banco de Dados do DataWarehouse do Azure SQL|
|/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read|Obter a configuração de diagnóstico para o recurso|
|/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o recurso|
|/servers/databases/providers/Microsoft.Insights/logDefinitions/read|Obter os logs disponíveis para bancos de dados|
|/servers/databases/providers/Microsoft.Insights/metricDefinitions/read|Retornar tipos de métricas que estão disponíveis para bancos de dados|
|/servers/databases/queryStore/queryTexts/read|Retornar a coleção de textos de consulta que correspondem aos parâmetros especificados.|
|/servers/databases/queryStore/read|Retornar os valores atuais das configurações do Repositório de Consultas do banco de dados.|
|/servers/databases/queryStore/write|Atualizar a configuração do repositório de consultas do banco de dados|
|/servers/databases/read|Retornar a lista de bancos de dados ou obter as propriedades para o banco de dados especificado.|
|/servers/databases/replicationLinks/delete|Encerrar a relação de replicação de modo forçado e com potencial perda de dados|
|/servers/databases/replicationLinks/failover/action|Failover após a sincronização de todas as alterações do primário, tornando esse banco de dados na relação de replicação\u0027s primária e tornando o primário remoto em um secundário|
|/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action|Failover imediatamente com possibilidade de perda de dados, tornando esse banco de dados o principal da relação de replicação\u0027s e tornando o primário remoto um secundário|
|/servers/databases/replicationLinks/read|Retornar detalhes sobre links de replicação estabelecidos para determinado banco de dados|
|/servers/databases/replicationLinks/unlink/action|Encerrar a relação de replicação de modo forçado ou após a sincronização com o parceiro|
|/servers/databases/replicationLinks/updateReplicationMode/action|Atualizar o modo de replicação para o vincular ao modo síncrono ou assíncrono|
|/servers/databases/restorePoints/action|Criar um novo ponto de restauração|
|/servers/databases/restorePoints/read|Retornar os pontos de restauração do banco de dados.|
|/servers/databases/resume/action|Retomar o Banco de Dados do Datawarehouse do Azure SQL|
|/servers/databases/schemas/read|Recuperar lista de esquemas de um banco de dados|
|/servers/databases/schemas/tables/columns/read|Recuperar a lista de colunas de uma tabela|
|/servers/databases/schemas/tables/columns/sensitivityLabels/delete|Excluir o rótulo de confidencialidade de uma determinada coluna|
|/servers/databases/schemas/tables/columns/sensitivityLabels/read|Obter o rótulo de confidencialidade de uma determinada coluna|
|/servers/databases/schemas/tables/columns/sensitivityLabels/write|Criar ou atualizar o rótulo de confidencialidade de uma determinada coluna|
|/servers/databases/schemas/tables/read|Recuperar lista de tabelas de um banco de dados|
|/servers/databases/schemas/tables/recommendedIndexes/read|Recuperar a lista de recomendações de índice em um banco de dados|
|/servers/databases/schemas/tables/recommendedIndexes/write|Aplicar a recomendação de índice|
|/servers/databases/securityAlertPolicies/read|Recuperar detalhes da política de detecção de ameaças configurada em determinado servidor|
|/servers/databases/securityAlertPolicies/write|Alterar política de detecção de ameaças para determinado banco de dados|
|/servers/databases/securityMetrics/read|Obter uma coleção de métricas de segurança de banco de dados|
|/servers/databases/sensitivityLabels/read|Listar rótulos de confidencialidade de um determinado banco de dados|
|/servers/databases/serviceTierAdvisors/read|Retornar sugestão sobre escalar ou reduzir o banco de dados verticalmente com base nas estatísticas de execução de consulta para melhorar o desempenho ou reduzir os custos|
|/servers/databases/syncGroups/cancelSync/action|Cancelar a sincronização do grupo de sincronização|
|/servers/databases/syncGroups/delete|Excluir um grupo de sincronização existente.|
|/servers/databases/syncGroups/hubSchemas/read|Retornar a lista de esquemas do banco de dados do hub de sincronização|
|/servers/databases/syncGroups/logs/read|Retornar a lista de logs do grupo de sincronização|
|/servers/databases/syncGroups/read|Retornar a lista de grupos de sincronização ou obter as propriedades do grupo de sincronização especificado.|
|/servers/databases/syncGroups/refreshHubSchema/action|Atualizar o esquema do banco de dados do hub de sincronização|
|/servers/databases/syncGroups/refreshHubSchemaOperationResults/read|Recuperar resultado da operação de atualização do esquema do hub de sincronização|
|/servers/databases/syncGroups/syncMembers/delete|Excluir um membro de sincronização existente.|
|/servers/databases/syncGroups/syncMembers/read|Retornar a lista de membros de sincronização ou obter as propriedades para o membro de sincronização especificado.|
|/servers/databases/syncGroups/syncMembers/refreshSchema/action|Atualizar o esquema do membro de sincronização|
|/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read|Recuperar resultado da operação de atualização do esquema do membro de sincronização|
|/servers/databases/syncGroups/syncMembers/schemas/read|Retornar a lista de esquemas do banco de dados de membros de sincronização|
|/servers/databases/syncGroups/syncMembers/write|Criar um membro de sincronização com os parâmetros especificados ou atualiza as propriedades do membro de sincronização especificado.|
|/servers/databases/syncGroups/triggerSync/action|Disparar a sincronização do grupo de sincronização|
|/servers/databases/syncGroups/write|Criar um grupo de sincronização com os parâmetros especificados ou atualizar as propriedades do grupo de sincronização especificado.|
|/servers/databases/topQueries/queryText/action|Retornar o texto Transact-SQL para a ID de consulta selecionada|
|/servers/databases/topQueries/read|Retorna estatísticas de tempo de execução agregadas para a consulta selecionada no período de tempo selecionado|
|/servers/databases/topQueries/statistics/read|Retorna estatísticas de tempo de execução agregadas para a consulta selecionada no período de tempo selecionado|
|/servers/databases/transparentDataEncryption/operationResults/read|Obter operações em andamento na criptografia de dados transparente|
|/servers/databases/transparentDataEncryption/read|Recuperar o status e os detalhes do recurso de segurança Transparent Data Encryption para determinado banco de dados|
|/servers/databases/transparentDataEncryption/write|Alterar o estado de criptografia de dados transparente|
|/servers/databases/upgradeDataWarehouse/action|Atualizar o Banco de Dados do Datawarehouse do Azure SQL|
|/servers/databases/usages/read|Obter as informações de uso do Banco de Dados SQL do Azure|
|/servers/databases/vulnerabilityAssessments/delete|Remover a avaliação de vulnerabilidade de um determinado banco de dados|
|/servers/databases/vulnerabilityAssessments/read|Recuperar detalhes da avaliação de vulnerabilidade configurada em um determinado banco de dados|
|/servers/databases/vulnerabilityAssessments/rules/baselines/delete|Remover a linha de base da regra de avaliação de vulnerabilidade para um determinado banco de dados|
|/servers/databases/vulnerabilityAssessments/rules/baselines/read|Obter a linha de base da regra de avaliação de vulnerabilidade para um determinado banco de dados|
|/servers/databases/vulnerabilityAssessments/rules/baselines/write|Alterar a linha de base da regra de avaliação de vulnerabilidade para um determinado banco de dados|
|/servers/databases/vulnerabilityAssessments/scans/action|Executar verificação de banco de dados para avaliação de vulnerabilidade.|
|/servers/databases/vulnerabilityAssessments/scans/export/action|Converter um resultado de exame existente em um formato legível por humanos. Se já existir, nada acontecerá|
|/servers/databases/vulnerabilityAssessments/scans/read|Retornar a lista de registros de exame de avaliação de vulnerabilidade de banco de dados ou obter o registro de exame para a ID de exame especificado.|
|/servers/databases/vulnerabilityAssessments/write|Alterar a avaliação de vulnerabilidade para um determinado banco de dados|
|/servers/databases/vulnerabilityAssessmentScans/action|Executar verificação de banco de dados para avaliação de vulnerabilidade.|
|/servers/databases/vulnerabilityAssessmentScans/operationResults/read|Recuperar o resultado de exame de avaliação de vulnerabilidade do banco de dados Executar operação|
|/servers/databases/vulnerabilityAssessmentSettings/read|Recuperar detalhes da avaliação de vulnerabilidade configurada em um determinado banco de dados|
|/servers/databases/vulnerabilityAssessmentSettings/write|Alterar a avaliação de vulnerabilidade para um determinado banco de dados|
|/servers/databases/write|Criar um banco de dados com os parâmetros especificados ou atualizar as propriedades ou marcas para o banco de dados especificado.|
|/servers/delete|Excluir um servidor existente.|
|/servers/disasterRecoveryConfiguration/delete|Excluir as configurações de recuperação de desastre existentes para um determinado servidor|
|/servers/disasterRecoveryConfiguration/failover/action|Failover de um DisasterRecoveryConfiguration|
|/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action|Forçar failover de um DisasterRecoveryConfiguration|
|/servers/disasterRecoveryConfiguration/read|Obter uma coleção de configurações de recuperação de desastre que incluem esse servidor|
|/servers/disasterRecoveryConfiguration/write|Alterar configuração de recuperação de desastre do servidor|
|/servers/elasticPoolEstimates/read|Retornar a lista de estimativas de pool elástico já criado para o servidor|
|/servers/elasticPoolEstimates/write|Criar nova estimativa de pool elástico para a lista de bancos de dados fornecida|
|/servers/elasticPools/advisors/read|Retornar a lista de consultores disponíveis para o pool elástico|
|/servers/elasticPools/advisors/recommendedActions/read|Retornar a lista de ações recomendadas do assistente especificado para o pool elástico|
|/servers/elasticPools/advisors/recommendedActions/write|Aplicar a ação recomendada no pool elástico|
|/servers/elasticPools/advisors/write|Atualizar status de execução automática de um assistente no nível do pool elástico.|
|/servers/elasticPools/databases/read|Obter uma lista de bancos de dados para um pool elástico|
|/servers/elasticPools/delete|Excluir pool elástico existente|
|/servers/elasticPools/elasticPoolActivity/read|Recuperar atividades e detalhes em um pool de banco de dados elástico|
|/servers/elasticPools/elasticPoolDatabaseActivity/read|Recuperar atividades e detalhes em um banco de dados que faz parte do pool de banco de dados elástico|
|/servers/elasticPools/metricDefinitions/read|Retornar tipos de métricas que estão disponíveis para pools de bancos de dados elásticos|
|/servers/elasticPools/metrics/read|Métricas de retorno para conjuntos de bancos de dados elásticos|
|/servers/elasticPools/operations/cancel/action|Cancela o pool elástico do Azure SQL pendente de operação assíncrona que ainda não está concluída.|
|/servers/elasticPools/operations/read|Retornar a lista de operações realizadas no pool elástico|
|/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read|Obter a configuração de diagnóstico para o recurso|
|/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o recurso|
|/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read|Retornar tipos de métricas que estão disponíveis para pools de bancos de dados elásticos|
|/servers/elasticPools/read|Recuperar detalhes do pool elástico em um determinado servidor|
|/servers/elasticPools/skus/read|Obter uma coleção de SKUs disponíveis para esse pool elástico|
|/servers/elasticPools/write|Criar um novo ou alterar propriedades do pool elástico existente|
|/servers/encryptionProtector/read|Retornar uma lista de protetores de criptografia do servidor ou obter as propriedades do protetor de criptografia do servidor especificado.|
|/servers/encryptionProtector/write|Atualizar as propriedades do Protetor de Criptografia do Servidor especificado.|
|/servers/extendedAuditingSettings/read|Recuperar detalhes da política de auditoria de blob de servidor estendida configurada em um determinado servidor|
|/servers/extendedAuditingSettings/write|Alterar a auditoria de blob de servidor estendida para um determinado servidor|
|/servers/failoverGroups/delete|Excluir um grupo de failover existente.|
|/servers/failoverGroups/failover/action|Executar o failover planejado em um grupo de failover existente.|
|/servers/failoverGroups/forceFailoverAllowDataLoss/action|Executar o failover forçado em um grupo de failover existente.|
|/servers/failoverGroups/read|Retornar a lista de grupos de failover ou obter as propriedades do grupo de failover especificado.|
|/servers/failoverGroups/write|Criar um grupo de failover com os parâmetros especificados ou atualizar as propriedades ou marcas para o grupo de failover especificado.|
|/servers/firewallRules/delete|Excluir uma regra de firewall de servidor existente.|
|/servers/firewallRules/read|Retornar a lista de regras de firewall do servidor ou obter as propriedades da regra de firewall do servidor especificado.|
|/servers/firewallRules/write|Criar uma regra de firewall do servidor com os parâmetros especificados, atualizar as propriedades da regra especificada ou substituir todas as regras existentes pelas novas regras de firewall do servidor.|
|/servers/import/action|Criar um novo banco de dados no servidor e implantar o esquema e os dados de um pacote DacPac|
|/servers/importExportOperationResults/read|Obter operações de importação/exportação em andamento|
|/servers/keys/delete|Excluir uma chave do servidor existente.|
|/servers/keys/read|Retornar a lista de chaves do servidor ou obter as propriedades para a chave de servidor especificada.|
|/servers/keys/write|Criar uma chave com os parâmetros especificados ou atualizar as propriedades ou marcas para a chave do servidor especificada.|
|/servers/operationResults/read|Obter operações do servidor em andamento|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Retornar tipos de métricas disponíveis para servidores|
|/servers/read|Retornar a lista de servidores ou obter as propriedades para o servidor especificado.|
|/servers/recommendedElasticPools/databases/read|Recuperar a métrica de pools de banco de dados elástico recomendados para determinado servidor|
|/servers/recommendedElasticPools/read|Recuperar recomendação para pools de banco de dados elástico a fim de reduzir custos ou melhorar o desempenho com base no histórico de utilização de recursos|
|/servers/recoverableDatabases/read|Essa operação é usada para a recuperação de desastres do banco de dados dinâmico a fim de restaurar o banco de dados no último ponto de backup bom conhecido. Ela retorna informações sobre o último backup, mas não\u0027t restaura o banco de dados efetivamente.|
|/servers/restorableDroppedDatabases/read|Obter uma lista de bancos de dados que foram removidos em determinado servidor ainda na política de retenção.|
|/servers/securityAlertPolicies/operationResults/read|Recuperar resultados da operação de gravação da política de detecção de ameaças do servidor|
|/servers/securityAlertPolicies/read|Recuperar detalhes da política de detecção de ameaças do servidor configurada em determinado servidor|
|/servers/securityAlertPolicies/write|Alterar a política de detecção de ameaças do servidor para um determinado servidor|
|/servers/serviceObjectives/read|Recuperar a lista de objetivos de nível de serviço (também conhecido como níveis de desempenho) disponíveis em determinado servidor|
|/servers/syncAgents/delete|Excluir um agente de sincronização existente.|
|/servers/syncAgents/generateKey/action|Gerar chave de registro do agente de sincronização|
|/servers/syncAgents/linkedDatabases/read|Retornar a lista de bancos de dados vinculados do agente de sincronização|
|/servers/syncAgents/read|Retornar a lista de agentes de sincronização ou obter as propriedades para o agente de sincronização especificado.|
|/servers/syncAgents/write|Criar um agente de sincronização com os parâmetros especificados ou atualizar as propriedades do agente de sincronização especificado.|
|/servers/usages/read|Retornar cota de DTU de servidor e DTU de consumo atual por todos os bancos de dados no servidor|
|/servers/virtualNetworkRules/delete|Excluir uma regra de rede virtual existente|
|/servers/virtualNetworkRules/read|Retornar a lista de regras de rede virtual ou obter as propriedades da regra de rede virtual especificada.|
|/servers/virtualNetworkRules/write|Criar uma regra de rede virtual com os parâmetros especificados ou atualizar as propriedades ou marcas para a regra de rede virtual especificada.|
|/servers/write|Criar um servidor com os parâmetros especificados ou atualizar as propriedades ou marcas para o servidor especificado.|
|/unregister/action|Cancelar a assinatura do provedor de recursos do Banco de Dados SQL do Microsoft Azure e habilitar a criação do Banco de Dados SQL do Microsoft Azure.|
|/virtualClusters/read|Retornar a lista de clusters virtuais ou obter as propriedades para o cluster virtual especificado.|
|/virtualClusters/write|Atualiza marcas de cluster virtual.|

## <a name="microsoftstorage"></a>Microsoft.Storage

| Operação | DESCRIÇÃO |
|---|---|
|/checknameavailability/read|Verificar se o nome dessa conta é válido e não está em uso.|
|/locations/deleteVirtualNetworkOrSubnets/action|Notificar o Microsoft.Storage sobre a exclusão de uma rede virtual ou sub-rede|
|/operations/read|Monitorar o status de uma operação assíncrona.|
|/register/action|Registra a assinatura do provedor de recursos de armazenamento e permite a criação de contas de armazenamento.|
|/skus/read|Listar os SKUs com suporte pelo Microsoft.Storage.|
|/storageAccounts/blobServices/containers/clearLegalHold/action|Limpar a retenção legal do contêiner de blob|
|/storageAccounts/blobServices/containers/delete|Retornar o resultado da exclusão de um contêiner|
|/storageAccounts/blobServices/containers/immutabilityPolicies/delete|Excluir a política de imutabilidade do contêiner de blob|
|/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action|Estender a política de imutabilidade do contêiner de blob|
|/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action|Bloquear a política de imutabilidade do contêiner de blob|
|/storageAccounts/blobServices/containers/immutabilityPolicies/read|Obter a política de imutabilidade do contêiner de blob|
|/storageAccounts/blobServices/containers/immutabilityPolicies/write|Inserir a política de imutabilidade do contêiner de blob|
|/storageAccounts/blobServices/containers/read|Retornar um contêiner ou uma lista de contêineres|
|/storageAccounts/blobServices/containers/setLegalHold/action|Definir a retenção legal do contêiner de blob|
|/storageAccounts/blobServices/containers/write|Retornar o resultado de um contêiner de blobs de concessão ou colocação|
|/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/read|Obter a configuração de diagnóstico para o recurso.|
|/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o recurso.|
|/storageAccounts/blobServices/providers/Microsoft.Insights/metricDefinitions/read|Obter lista de definições de Métricas de Armazenamento da Microsoft.|
|/storageAccounts/blobServices/read|Retornar propriedades ou estatísticas do serviço blob|
|/storageAccounts/blobServices/write|Retornar o resultado da inserção de propriedades do serviço blob|
|/storageAccounts/delete|Excluir uma conta de armazenamento existente.|
|/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/read|Obter a configuração de diagnóstico para o recurso.|
|/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o recurso.|
|/storageAccounts/fileServices/providers/Microsoft.Insights/metricDefinitions/read|Obter lista de definições de Métricas de Armazenamento da Microsoft.|
|/storageAccounts/listAccountSas/action|Retornar o token SAS da conta para a conta de armazenamento especificada.|
|/storageAccounts/listkeys/action|Retornar as chaves de acesso da conta de armazenamento especificada.|
|/storageAccounts/listServiceSas/action|Retornar o token SAS de serviço para a conta de armazenamento especificada.|
|/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/read|Obter a configuração de diagnóstico para o recurso.|
|/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o recurso.|
|/storageAccounts/providers/Microsoft.Insights/metricDefinitions/read|Obter lista de definições de Métricas de Armazenamento da Microsoft.|
|/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o recurso.|
|/storageAccounts/queueServices/providers/Microsoft.Insights/metricDefinitions/read|Obter lista de definições de Métricas de Armazenamento da Microsoft.|
|/storageAccounts/queueServices/queues/delete|Retornar o resultado da exclusão de uma fila|
|/storageAccounts/queueServices/queues/read|Retornar uma fila ou uma lista de filas.|
|/storageAccounts/queueServices/queues/write|Retornar o resultado da gravação de uma fila|
|/storageAccounts/queueServices/read|Retornar propriedades ou estatísticas do serviço de fila.|
|/storageAccounts/queueServices/write|Retornar o resultado da configuração das propriedades do serviço de fila|
|/storageAccounts/read|Retornar a lista de contas de armazenamento ou obter as propriedades da conta de armazenamento especificada.|
|/storageAccounts/regeneratekey/action|Regenerar as chaves de acesso da conta de armazenamento especificada.|
|/storageAccounts/services/diagnosticSettings/write|Criar/atualizar definições de diagnóstico da conta de armazenamento.|
|/storageAccounts/storageAccounts/queueServices/providers/ Microsoft.Insights/diagnosticSettings/read|Obter a configuração de diagnóstico para o recurso.|
|/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/read|Obter a configuração de diagnóstico para o recurso.|
|/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o recurso.|
|/storageAccounts/tableServices/providers/Microsoft.Insights/metricDefinitions/read|Obter lista de definições de Métricas de Armazenamento da Microsoft.|
|/storageAccounts/write|Criar uma conta de armazenamento com os parâmetros especificados, atualizar as propriedades ou marcas ou adicionar um domínio personalizado à conta de armazenamento especificada.|
|/usages/read|Retornar o limite e a contagem atual de uso de recursos na assinatura especificada|

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

| Operação | DESCRIÇÃO |
|---|---|
|/storageSyncServices/delete|Excluir os Serviços de Sincronização de Armazenamento|
|/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read|Obter as métricas disponíveis para os Serviços de Sincronização de Armazenamento|
|/storageSyncServices/read|Ler os Serviços de Sincronização de Armazenamento|
|/storageSyncServices/registeredServers/delete|Excluir um servidor registrado|
|/storageSyncServices/registeredServers/read|Ler um servidor registrado|
|/storageSyncServices/registeredServers/write|Criar ou atualizar um servidor registrado|
|/storageSyncServices/syncGroups/cloudEndpoints/delete|Excluir Pontos de Extremidade de Nuvem|
|/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read|Localização da API para chamadas de backup assíncronas|
|/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action|Chamar essa ação após o backup|
|/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action|Chamar essa ação após restaurar|
|/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action|Chamar essa ação antes do backup|
|/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action|Chamar essa ação antes de restaurar|
|/storageSyncServices/syncGroups/cloudEndpoints/read|Ler Pontos de Extremidade de Nuvem|
|/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action|Restaurar pulsação|
|/storageSyncServices/syncGroups/cloudEndpoints/write|Criar ou atualizar Pontos de Extremidade de Nuvem|
|/storageSyncServices/syncGroups/delete|Excluir quaisquer Grupos de Sincronização|
|/storageSyncServices/syncGroups/read|Lera quaisquer Grupos de Sincronização|
|/storageSyncServices/syncGroups/serverEndpoints/delete|Excluir qualquer ponto de extremidade do servidor|
|/storageSyncServices/syncGroups/serverEndpoints/read|Ler qualquer ponto de extremidade do servidor|
|/storageSyncServices/syncGroups/serverEndpoints/recallAction/action|Chamar essa ação para recuperar arquivos para um servidor|
|/storageSyncServices/syncGroups/serverEndpoints/write|Criar ou atualizar qualquer ponto de extremidade de servidor|
|/storageSyncServices/syncGroups/write|Criar ou atualizar Grupos de Sincronização|
|/storageSyncServices/write|Criar ou atualizar Serviços de Sincronização de Armazenamento|

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

| Operação | DESCRIÇÃO |
|---|---|
|/managers/accessControlRecords/delete|Excluir os registros de controle de acesso|
|/managers/accessControlRecords/read|Listar ou obter os registros de controle de acesso|
|/managers/accessControlRecords/write|Criar ou atualizar registros de controle de acesso|
|/managers/alerts/read|Listar ou obter os alertas|
|/managers/bandwidthSettings/delete|Excluir uma configuração de largura de banda existente (somente 8000 Series)|
|/managers/bandwidthSettings/read|Listar as configurações de largura de banda (somente 8000 Series)|
|/managers/bandwidthSettings/write|Criar novas configurações de largura de banda ou atualizá-las (somente 8000 Series)|
|/Managers/certificates/write|A operação Atualizar certificado do recurso atualiza o certificado de credencial de cofre/recurso.|
|/managers/clearAlerts/action|Limpar todos os alertas associados ao Gerenciador de Dispositivos.|
|/managers/cloudApplianceConfigurations/read|Listar configurações com suporte pelo dispositivo de nuvem|
|/managers/configureDevice/action|Configurar um dispositivo|
|/managers/delete|Excluir os Gerenciadores de Dispositivo|
|/Managers/delete|A operação Excluir cofre exclui o recurso do Azure do tipo 'cofre' especificado|
|/managers/devices/alertSettings/read|Listar ou obter as configurações de alerta|
|/managers/devices/alertSettings/write|Criar ou atualizar as configurações de alerta|
|/managers/devices/backupPolicies/backup/action|Fazer um backup manual para criar um backup sob demanda de todos os volumes protegidos pela política.|
|/managers/devices/backupPolicies/delete|Excluir políticas de backup existentes (somente 8000 Series)|
|/managers/devices/backupPolicies/read|Listar as políticas de backup (somente 8000 Series)|
|/managers/devices/backupPolicies/schedules/delete|Excluir agendas existentes|
|/managers/devices/backupPolicies/schedules/read|Listar as agendas|
|/managers/devices/backupPolicies/schedules/write|Criar ou atualizar agendas|
|/managers/devices/backupPolicies/write|Criar novas ou atualizar as políticas de Backup (somente 8000 Series)|
|/managers/devices/backups/delete|Excluir o conjunto de backup|
|/managers/devices/backups/elements/clone/action|Clonar um compartilhamento ou volume usando um elemento de backup.|
|/managers/devices/backups/read|Listar ou obter o conjunto de backup|
|/managers/devices/backups/restore/action|Restaurar todos os volumes de um conjunto de backup.|
|/managers/devices/backupScheduleGroups/delete|Excluir os grupos de agendamento de backup|
|/managers/devices/backupScheduleGroups/read|Listar ou obter os grupos de agendamento de backup|
|/managers/devices/backupScheduleGroups/write|Criar ou atualizar os grupos de agendamento de backup|
|/managers/devices/chapSettings/delete|Excluir as configurações de Chap|
|/managers/devices/chapSettings/read|Listar ou obter as configurações de Chap|
|/managers/devices/chapSettings/write|Criar ou atualizar as configurações de Chap|
|/managers/devices/deactivate/action|Desativar um dispositivo.|
|/managers/devices/delete|Excluir os dispositivos|
|/managers/devices/download/action|Baixar atualizações para um dispositivo.|
|/managers/devices/failover/action|Failover do dispositivo.|
|/managers/devices/fileservers/backup/action|Fazer backup de um servidor de arquivos.|
|/managers/devices/fileservers/delete|Excluir servidores de arquivos|
|/managers/devices/fileservers/metrics/read|Listar ou obter a métrica|
|/managers/devices/fileservers/metricsDefinitions/read|Listar ou obter as definições de métrica|
|/managers/devices/fileservers/read|Listar ou obter os servidores de arquivos|
|/managers/devices/fileservers/shares/delete|Excluir os compartilhamentos|
|/managers/devices/fileservers/shares/metrics/read|Listar ou obter a métrica|
|/managers/devices/fileservers/shares/metricsDefinitions/read|Listar ou obter as definições de métrica|
|/managers/devices/fileservers/shares/read|Listar ou obter os compartilhamentos|
|/managers/devices/fileservers/shares/write|Criar ou atualizar os compartilhamentos|
|/managers/devices/fileservers/write|Criar ou atualizar os servidores de arquivos|
|/managers/devices/hardwareComponentGroups/changeControllerPowerState/action|Alterar o estado de energia do controlador dos grupos de componentes de hardware|
|/managers/devices/hardwareComponentGroups/read|Listar os grupos de componentes de hardware|
|/managers/devices/install/action|Instalar atualizações em um dispositivo.|
|/managers/devices/installUpdates/action|Instalar as atualizações nos dispositivos|
|/managers/devices/iscsiservers/backup/action|Fazer backup de um servidor iSCSI.|
|/managers/devices/iscsiservers/delete|Excluir servidores iSCSI|
|/managers/devices/iscsiservers/disks/delete|Excluir os discos|
|/managers/devices/iscsiservers/disks/metrics/read|Listar ou obter a métrica|
|/managers/devices/iscsiservers/disks/metricsDefinitions/read|Listar ou obter as definições de métrica|
|/managers/devices/iscsiservers/disks/read|Listar ou obter os discos|
|/managers/devices/iscsiservers/disks/write|Criar ou atualizar os discos|
|/managers/devices/iscsiservers/metrics/read|Listar ou obter a métrica|
|/managers/devices/iscsiservers/metricsDefinitions/read|Listar ou obter as definições de métrica|
|/managers/devices/iscsiservers/read|Listar ou obter os servidores iSCSI|
|/managers/devices/iscsiservers/write|Criar ou atualizar servidores iSCSI|
|/managers/devices/jobs/cancel/action|Cancelar um trabalho em execução|
|/managers/devices/jobs/read|Listar ou obter os trabalhos|
|/managers/devices/listFailoverSets/action|Listar os conjuntos de failover para um dispositivo existente.|
|/managers/devices/listFailoverTargets/action|Listar ou obter de destinos de failover dos dispositivos|
|/managers/devices/metrics/read|Listar ou obter a métrica|
|/managers/devices/metricsDefinitions/read|Listar ou obter as definições de métrica|
|/managers/devices/migrationSourceConfigurations/confirmMigration/action|Confirmar uma migração bem-sucedida.|
|/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action|Buscar o status de confirmação da migração.|
|/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action|Buscar o status do trabalho de estimativa de migração.|
|/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action|Buscar o status para a migração.|
|/managers/devices/migrationSourceConfigurations/import/action|Importar configurações de origem para migração|
|/managers/devices/migrationSourceConfigurations/startMigration/action|Iniciar migração usando as configurações de origem|
|/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action|Iniciar um trabalho para estimar a duração do processo de migração.|
|/managers/devices/networkSettings/read|Listar ou obter as configurações de rede|
|/managers/devices/networkSettings/write|Criar novas ou atualizar as configurações de rede|
|/managers/devices/publicEncryptionKey/action|Listar chave de criptografia pública do Gerenciador de Dispositivos|
|/managers/devices/publishSupportPackage/action|Publicar o pacote de suporte de um dispositivo para solução de problemas pelo Suporte da Microsoft.|
|/managers/devices/read|Listar ou obter os dispositivos|
|/managers/devices/scanForUpdates/action|Verificar se há atualizações em um dispositivo.|
|/managers/devices/securitySettings/read|Listar as configurações de segurança|
|/managers/devices/securitySettings/syncRemoteManagementCertificate/action|Sincronizar o certificado de gerenciamento remoto de um dispositivo.|
|/managers/devices/securitySettings/update/action|Atualizar as configurações de segurança.|
|/managers/devices/securitySettings/write|Criar ou atualizar as configurações de segurança|
|/managers/devices/sendTestAlertEmail/action|Enviar email de alerta de teste para destinatários de email configurados.|
|/managers/devices/timeSettings/read|Listar ou obter as configurações de tempo|
|/managers/devices/timeSettings/write|Criar ou atualizar as configurações de tempo|
|/managers/devices/updateSummary/read|Listar ou obter o resumo de atualização|
|/managers/devices/volumeContainers/delete|Excluir contêineres de Volume existentes (somente 8000 Series)|
|/managers/devices/volumeContainers/listEncryptionKeys/action|Listar chaves de criptografia da lista de contêineres de volume|
|/managers/devices/volumeContainers/metrics/read|Listar a métrica|
|/managers/devices/volumeContainers/metricsDefinitions/read|Lista as definições de métrica|
|/managers/devices/volumeContainers/read|Listar os contêineres de volume (somente 8000 Series)|
|/managers/devices/volumeContainers/rolloverEncryptionKey/action|Chaves de criptografia de substituição de contêineres de volume|
|/managers/devices/volumeContainers/volumes/delete|Excluir um volume existente|
|/managers/devices/volumeContainers/volumes/metrics/read|Listar a métrica|
|/managers/devices/volumeContainers/volumes/metricsDefinitions/read|Lista as definições de métrica|
|/managers/devices/volumeContainers/volumes/read|Listar os volumes|
|/managers/devices/volumeContainers/volumes/write|Criar ou atualizar volumes|
|/managers/devices/volumeContainers/write|Criar novos ou atualizar os contêineres de volume (somente 8000 Series)|
|/managers/devices/write|Criar ou atualizar os dispositivos|
|/managers/encryptionSettings/read|Listar ou obter as configurações de criptografia|
|/Managers/extendedInformation/delete|A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault?|
|/Managers/extendedInformation/read|A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault?|
|/Managers/extendedInformation/write|A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault?|
|/managers/getActivationKey/action|Obter a chave de ativação para o gerenciador de dispositivos.|
|/managers/getEncryptionKey/action|Obter a chave de criptografia para o gerenciador de dispositivos.|
|/managers/listActivationKey/action|Obter a chave de ativação do Gerenciador de Dispositivo StorSimple.|
|/managers/listPrivateEncryptionKey/action|Obter a chave de criptografia particular para um Gerenciador de Dispositivos StorSimple.|
|/managers/listPublicEncryptionKey/action|Listar chaves de criptografia públicas de um Gerenciador de Dispositivo StorSimple.|
|/managers/metrics/read|Listar ou obter a métrica|
|/managers/metricsDefinitions/read|Listar ou obter as definições de métrica|
|/managers/provisionCloudAppliance/action|Criar um novo dispositivo de nuvem.|
|/managers/read|Listar ou obter os Gerenciadores de Dispositivo|
|/Managers/read|A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre'|
|/managers/regenarateRegistationCertificate/action|Regenerar o certificado de registro para os gerenciadores de dispositivo.|
|/managers/regenerateActivationKey/action|Regenerar a chave de ativação para o gerenciador de dispositivos.|
|/managers/storageAccountCredentials/delete|Excluir as credenciais da conta de armazenamento|
|/managers/storageAccountCredentials/listAccessKey/action|Listar chaves de acesso de credenciais da conta de armazenamento|
|/managers/storageAccountCredentials/read|Lista ou obtém as credenciais da conta de armazenamento|
|/managers/storageAccountCredentials/write|Criar ou atualizar as credenciais da conta de armazenamento|
|/managers/storageDomains/delete|Excluir os domínios de armazenamento|
|/managers/storageDomains/read|Listar ou obter os domínios de armazenamento|
|/managers/storageDomains/write|Criar ou atualizar os domínios de armazenamento|
|/managers/write|Criar ou atualizar os Gerenciadores de Dispositivo|
|/Managers/write|A operação Criar cofre cria um recurso do Azure do tipo 'cofre'|

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

| Operação | DESCRIÇÃO |
|---|---|
|/locations/quotas/Read|Ler Cota de assinatura do Stream Analytics|
|/operations/Read|Ler Operações do Stream Analytics|
|/Register/action|Registrar assinatura com provedor de recursos do Stream Analytics|
|/streamingjobs/Delete|Excluir trabalho de Stream Analytics|
|/streamingjobs/functions/Delete|Excluir função de trabalho do Stream Analytics|
|/streamingjobs/functions/operationresults/Read|Ler os resultados da operação para Função de Trabalho do Stream Analytics|
|/streamingjobs/functions/Read|Ler Função de Trabalho do Stream Analytics|
|/streamingjobs/functions/RetrieveDefaultDefinition/action|Recuperar a definição padrão de uma Função de Trabalho do Stream Analytics|
|/streamingjobs/functions/Test/action|Testar Função de Trabalho do Stream Analytics|
|/streamingjobs/functions/Write|Gravar Função de Trabalho do Stream Analytics|
|/streamingjobs/inputs/Delete|Excluir entrada de trabalho do Stream Analytics|
|/streamingjobs/inputs/operationresults/Read|Ler os resultados da operação para Entrada de Trabalho do Stream Analytics|
|/streamingjobs/inputs/Read|Ler entrada do trabalho do Stream Analytics|
|/streamingjobs/inputs/Sample/action|Exemplo de Entrada de Trabalho do Stream Analytics|
|/streamingjobs/inputs/Test/action|Testar Entrada de Trabalho do Stream Analytics|
|/streamingjobs/inputs/Write|Gravar entrada de trabalho do Stream Analytics|
|/streamingjobs/metricdefinitions/Read|Ler Definições de Métrica|
|/streamingjobs/operationresults/Read|Ler resultados da operação para Trabalho do Stream Analytics|
|/streamingjobs/outputs/Delete|Excluir saída de trabalho do Stream Analytics|
|/streamingjobs/outputs/operationresults/Read|Ler resultados da operação para Saída do Trabalho do Stream Analytics|
|/streamingjobs/outputs/Read|Ler saída do trabalho do Stream Analytics|
|/streamingjobs/outputs/Test/action|Testar Saída do Trabalho do Stream Analytics|
|/streamingjobs/outputs/Write|Gravar saída de trabalho do Stream Analytics|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read|Ler configuração de diagnóstico.|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write|Gravar configuração de diagnóstico.|
|/streamingjobs/providers/Microsoft.Insights/logDefinitions/read|Obter os logs disponíveis para streamingjobs|
|/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read|Obter a métrica disponível para streamingjobs|
|/streamingjobs/Read|Ler trabalho do Stream Analytics|
|/streamingjobs/Start/action|Iniciar trabalho do Stream Analytics|
|/streamingjobs/Stop/action|Interromper trabalho do Stream Analytics|
|/streamingjobs/transformations/Delete|Excluir transformação do trabalho do Stream Analytics|
|/streamingjobs/transformations/Read|Ler transformação do trabalho do Stream Analytics|
|/streamingjobs/transformations/Write|Gravar transformação do trabalho do Stream Analytics|
|/streamingjobs/Write|Gravar trabalho de Stream Analytics|

## <a name="microsoftsubscription"></a>Microsoft.Subscription

| Operação | DESCRIÇÃO |
|---|---|
|/SubscriptionDefinitions/read|Obter uma definição de assinatura do Azure em um grupo de gerenciamento.|
|/SubscriptionDefinitions/write|Criar uma definição de assinatura do Azure|

## <a name="microsoftsupport"></a>Microsoft.Support

| Operação | DESCRIÇÃO |
|---|---|
|/register/action|Registrar para dar suporte ao provedor de recursos|
|/supportTickets/read|Obter detalhes de tíquete de suporte (incluindo status, severidade, detalhes de contato e comunicações) ou obtém a lista de tíquetes de suporte entre assinaturas.|
|/supportTickets/write|Criar ou atualizar um tíquete de suporte. Você pode criar um tíquete de suporte para problemas técnicos, de cotas, de cobrança ou de gerenciamento de assinaturas. Você pode atualizar severidade, detalhes de contato e comunicações de tíquetes de suporte existentes.|

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

| Operação | DESCRIÇÃO |
|---|---|
|/environments/accesspolicies/delete|Excluir a política de acesso.|
|/environments/accesspolicies/read|Obter as propriedades de uma política de acesso.|
|/environments/accesspolicies/write|Criar uma nova política de acesso para um ambiente ou atualizar uma política de acesso existente.|
|/environments/delete|Excluir o ambiente.|
|/environments/eventsources/delete|Excluir a origem do evento.|
|/environments/eventsources/eventsources/providers/Microsoft.Insights/ diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o recurso|
|/environments/eventsources/providers/Microsoft.Insights/diagnosticSettings/read|Obter a configuração de diagnóstico para o recurso|
|/environments/eventsources/providers/Microsoft.Insights/metricDefinitions/read|Obtém as métricas disponíveis para eventsources|
|/environments/eventsources/read|Obter as propriedades de uma origem do evento.|
|/environments/eventsources/write|Criar uma nova origem do evento para um ambiente ou atualizar uma origem do evento existente.|
|/environments/providers/Microsoft.Insights/diagnosticSettings/read|Obter a configuração de diagnóstico para o recurso|
|/environments/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o recurso|
|/environments/providers/Microsoft.Insights/metricDefinitions/read|Obter as métricas disponíveis para ambientes|
|/environments/read|Obter as propriedades de um ambiente.|
|/environments/referencedatasets/delete|Excluir o conjunto de dados de referência.|
|/environments/referencedatasets/read|Obter as propriedades de um conjunto de dados de referência.|
|/environments/referencedatasets/write|Criar um novo conjunto de dados de referência para um ambiente ou atualizar um conjunto de dados de referência existente.|
|/environments/status/read|Obter o status do ambiente, estado das operações associadas, como ingresso.|
|/environments/write|Criar um novo ambiente ou atualizar um ambiente existente.|
|/register/action|Registrar a assinatura do provedor de recursos do Time Series Insights e habilitar a criação de ambientes do Time Series Insights.|

## <a name="microsoftweb"></a>microsoft.web

| Operação | DESCRIÇÃO |
|---|---|
|/apimanagementaccounts/apiacls/read|Obter Apiacls de Contas de Gerenciamento de API.|
|/apimanagementaccounts/apis/apiacls/delete|Excluir Apiacls de APIs de Contas de Gerenciamento de API.|
|/apimanagementaccounts/apis/apiacls/read|Obter Apiacls de APIs de Contas de Gerenciamento de API.|
|/apimanagementaccounts/apis/apiacls/write|Atualizar Apiacls de APIs de Contas de Gerenciamento de API.|
|/apimanagementaccounts/apis/connectionacls/read|Obter Connectionacls de APIs de Contas de Gerenciamento de API.|
|/apimanagementaccounts/apis/connections/confirmconsentcode/action|Confirmar código de consentimento de conexões de APIs de Contas de Gerenciamento de API.|
|/apimanagementaccounts/apis/connections/connectionacls/delete|Excluir Connectionacls de Conexões de APIs de Contas de Gerenciamento de API.|
|/apimanagementaccounts/apis/connections/connectionacls/read|Obter Connectionacls de Conexões de APIs de Contas de Gerenciamento de API.|
|/apimanagementaccounts/apis/connections/connectionacls/write|Atualizar Connectionacls de Conexões de APIs de Contas de Gerenciamento de API.|
|/apimanagementaccounts/apis/connections/delete|Excluir Conexões de APIs de Contas de Gerenciamento de API.|
|/apimanagementaccounts/apis/connections/getconsentlinks/action|Obter links de consentimento para conexões de APIs de Contas de Gerenciamento de API.|
|/apimanagementaccounts/apis/connections/listconnectionkeys/action|Listar chaves de conexão de conexões de APIs de Contas de Gerenciamento de API.|
|/apimanagementaccounts/apis/connections/listsecrets/action|Listar Segredos de conexões de APIs de Contas de Gerenciamento de API.|
|/apimanagementaccounts/apis/connections/read|Obter conexões de APIs de Contas de Gerenciamento de API.|
|/apimanagementaccounts/apis/connections/write|Atualizar conexões de APIs de Contas de Gerenciamento de API.|
|/apimanagementaccounts/apis/delete|Excluir APIs de Contas de Gerenciamento de API.|
|/apimanagementaccounts/apis/localizeddefinitions/delete|Excluir definições localizadas de APIs de Contas de Gerenciamento de API.|
|/apimanagementaccounts/apis/localizeddefinitions/read|Obter definições localizadas de APIs de Contas de Gerenciamento de API.|
|/apimanagementaccounts/apis/localizeddefinitions/write|Atualizar definições localizadas de APIs de Contas de Gerenciamento de API.|
|/apimanagementaccounts/apis/read|Obter APIs de Contas de Gerenciamento de API.|
|/apimanagementaccounts/apis/write|Atualizar APIs de Contas de Gerenciamento de API.|
|/apimanagementaccounts/connectionacls/read|Obter Connectionacls de Contas de Gerenciamento de API.|
|/availablestacks/read|Obter pilhas disponíveis.|
|/billingmeters/read|Obter lista de medidores de cobrança.|
|/certificates/Delete|Excluir um certificado existente.|
|/certificates/Read|Obter a lista de certificados.|
|/certificates/Write|Adicionar um novo certificado ou atualizar um existente.|
|/checknameavailability/read|Verificar se o nome do recurso está disponível.|
|/classicmobileservices/read|Obter Serviços Móveis clássicos.|
|/connectionGateways/Delete|Excluir um Gateway de Conexão.|
|/connectionGateways/Join/Action|Adicionar um Gateway de Conexão.|
|/connectiongateways/liststatus/action|Listar status de gateways de conexão.|
|/connectionGateways/ListStatus/Action|Listar status de um gateway de conexão.|
|/connectionGateways/Move/Action|Mover um gateway de conexão.|
|/connectionGateways/Read|Obter a lista de gateways de conexão.|
|/connectionGateways/Write|Criar ou atualizar um Gateway de Conexão.|
|/connections/confirmconsentcode/action|Confirmar o código de autorização de conexões.|
|/connections/Delete|Excluir uma conexão.|
|/connections/Join/Action|Ingressar em uma conexão.|
|/connections/listconsentlinks/action|Listar os links de autorização para conexões.|
|/connections/Move/Action|Mover uma conexão.|
|/connections/Read|Obter a lista de conexões.|
|/connections/Write|Criar ou atualizar uma conexão.|
|/customApis/Delete|Excluir uma API personalizada.|
|/customApis/extractApiDefinitionFromWsdl/Action|Extrair a definição de API de uma WSDL.|
|/customApis/Join/Action|Adicionar uma API personalizada.|
|/customApis/listWsdlInterfaces/Action|Listar interfaces WSDL para uma API personalizada.|
|/customApis/Move/Action|Mover uma API personalizada.|
|/customApis/Read|Obter a lista de API personalizada.|
|/customApis/Write|Criar ou atualizar uma API personalizada.|
|/deploymentlocations/read|Obter locais de implantação.|
|/geoRegions/Read|Obter a lista de regiões geográficas.|
|/hostingenvironments/capacities/read|Obter as capacidades dos ambientes de hospedagem.|
|/hostingEnvironments/Delete|Excluir ambiente do Serviço de Aplicativo|
|/hostingenvironments/diagnostics/read|Obter diagnóstico dos ambientes de hospedagem.|
|/hostingenvironments/inboundnetworkdependenciesendpoints/read|Obter os pontos de extremidade de rede de todas as dependências de entrada.|
|/hostingenvironments/metricdefinitions/read|Obter definições de métrica de ambientes de hospedagem.|
|/hostingenvironments/multirolepools/metricdefinitions/read|Obter definições de métrica de pools multifunção dos ambientes de hospedagem.|
|/hostingenvironments/multirolepools/metrics/read|Obter a métrica de pools multifunção em Ambientes de Hospedagem.|
|/hostingEnvironments/multiRolePools/providers/Microsoft.Insights/ metricDefinitions/Read|Obter as métricas disponíveis para o MultiRole do Ambiente do Serviço de Aplicativo|
|/hostingEnvironments/multiRolePools/Read|Obter as propriedades de um pool de front-end em um Ambiente do Serviço de Aplicativo|
|/hostingenvironments/multirolepools/skus/read|Obter SKUs de pools multifunção dos ambientes de hospedagem.|
|/hostingenvironments/multirolepools/usages/read|Obter usos dos pools multifunção dos ambientes de hospedagem.|
|/hostingEnvironments/multiRolePools/Write|Criar um novo pool de front-end em um Ambiente do Serviço de Aplicativo ou atualizar um existente|
|/hostingenvironments/operations/read|Obter operações dos ambientes de hospedagem.|
|/hostingenvironments/outboundnetworkdependenciesendpoints/read|Obter os pontos de extremidade de rede de todas as dependências de saída.|
|/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/read|Obter a configuração de diagnóstico para o recurso|
|/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o recurso|
|/hostingEnvironments/Read|Obter as propriedades de um Ambiente do Serviço de Aplicativo|
|/hostingEnvironments/reboot/Action|Reinicializar todas as máquinas em um Ambiente do Serviço de Aplicativo|
|/hostingenvironments/resume/action|Retomar ambientes de hospedagem.|
|/hostingenvironments/serverfarms/read|Obter Planos do Serviço de Aplicativo dos Ambientes de Hospedagem.|
|/hostingenvironments/sites/read|Obter aplicativos Web dos ambientes de hospedagem.|
|/hostingenvironments/suspend/action|Suspender ambientes de hospedagem.|
|/hostingenvironments/usages/read|Obter usos dos ambientes de hospedagem.|
|/hostingenvironments/workerpools/metricdefinitions/read|Obter definições de métrica de pools de trabalho dos ambientes de hospedagem.|
|/hostingenvironments/workerpools/metrics/read|Obter a métrica de pools de trabalho de Ambientes de Hospedagem.|
|/hostingEnvironments/workerPools/providers/Microsoft.Insights/metricDefinitions/Read|Obter as métricas disponíveis para o WorkerPool do Ambiente do Serviço de Aplicativo|
|/hostingEnvironments/workerPools/Read|Obter as propriedades de um pool de trabalho em um Ambiente do Serviço de Aplicativo|
|/hostingenvironments/workerpools/skus/read|Obter SKUs de pools de trabalho dos ambientes de hospedagem.|
|/hostingenvironments/workerpools/usages/read|Obter uso dos pools de trabalho dos ambientes de hospedagem.|
|/hostingEnvironments/workerPools/Write|Criar um novo pool de trabalho em um Ambiente do Serviço de Aplicativo ou atualizar um existente|
|/hostingEnvironments/Write|Criar um novo Ambiente do Serviço de Aplicativo ou atualizar um existente|
|/ishostingenvironmentnameavailable/read|Obter confirmação se o nome do ambiente de hospedagem está disponível.|
|/ishostnameavailable/read|Verificar se o nome do host está disponível.|
|/isusernameavailable/read|Verificar se o nome de usuário está disponível.|
|/listSitesAssignedToHostName/Read|Obter nomes dos sites atribuídos ao nome de host.|
|/locations/apioperations/read|Obter operações API dos locais.|
|/locations/connectiongatewayinstallations/read|Obter as instalações de gateway de conexão locais.|
|/locations/extractapidefinitionfromwsdl/action|Extrair a definição de API da WSDL para locais.|
|/locations/listwsdlinterfaces/action|Listar interfaces WSDL para locais.|
|/locations/managedapis/apioperations/read|Obter operações de API gerenciadas dos locais.|
|/locations/managedapis/Join/Action|Adicionar uma API gerenciada.|
|/locations/managedapis/read|Obter as APIs gerenciadas dos locais.|
|/operations/read|Obter Operações.|
|/publishingusers/read|Obter usuários de publicação.|
|/publishingusers/write|Atualizar usuários de publicação.|
|/recommendations/Read|Obter a lista de recomendações para assinaturas.|
|/register/action|Registrar o provedor de recursos Microsoft.Web para a assinatura.|
|/resourcehealthmetadata/read|Obter metadados do Resource Health.|
|/serverfarms/capabilities/read|Obter recursos do Planos do Serviço de Aplicativo.|
|/serverfarms/Delete|Excluir um Plano do Serviço de Aplicativo existente|
|/serverfarms/firstpartyapps/settings/delete|Excluir configurações de aplicativos próprios dos Planos do Serviço de Aplicativo.|
|/serverfarms/firstpartyapps/settings/read|Obter configurações de aplicativos próprios dos Planos do Serviço de Aplicativo.|
|/serverfarms/firstpartyapps/settings/write|Atualizar configurações de aplicativos próprios dos Planos do Serviço de Aplicativo.|
|/serverfarms/hybridconnectionnamespaces/relays/delete|Excluir retransmissões de namespaces de conexão híbrida dos Planos do Serviço de Aplicativo.|
|/serverfarms/hybridconnectionnamespaces/relays/read|Obter retransmissões de namespaces de conexão híbrida dos Planos do Serviço de Aplicativo.|
|/serverfarms/hybridconnectionnamespaces/relays/sites/read|Obter aplicativos Web das retransmissões dos namespaces da conexão híbrida dos Planos do Serviço de Aplicativo.|
|/serverfarms/hybridconnectionplanlimits/read|Obter limites do plano de conexão híbrida dos Planos do Serviço de Aplicativo.|
|/serverfarms/hybridconnectionrelays/read|Obter transmissões de conexão híbrida dos Planos do Serviço de Aplicativo.|
|/serverfarms/metricdefinitions/read|Obter definições de métrica dos Planos do Serviço de Aplicativo.|
|/serverfarms/metrics/read|Obter métrica dos Planos do Serviço de Aplicativo.|
|/serverfarms/operationresults/read|Obter resultados de operação dos Planos do Serviço de Aplicativo.|
|/serverfarms/providers/Microsoft.Insights/diagnosticSettings/read|Obter a configuração de diagnóstico para o recurso|
|/serverfarms/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o recurso|
|/serverfarms/providers/Microsoft.Insights/metricDefinitions/Read|Obter as métricas disponíveis para o Plano do Serviço de Aplicativo|
|/serverfarms/Read|Obter as propriedades em um Plano do Serviço de Aplicativo|
|/serverfarms/restartSites/Action|Reiniciar todos os aplicativos Web em um Plano do Serviço de Aplicativo|
|/serverfarms/sites/read|Obter aplicativos Web dos Planos do Serviço de Aplicativo.|
|/serverfarms/skus/read|Obter as SKUs dos Planos do Serviço de Aplicativo.|
|/serverfarms/usages/read|Obter usos dos Planos do Serviço de Aplicativo.|
|/serverfarms/virtualnetworkconnections/gateways/write|Atualizar gateways de conexões de rede virtual dos Planos do Serviço de Aplicativo.|
|/serverfarms/virtualnetworkconnections/read|Obter as conexões de rede virtual dos Planos do Serviço de Aplicativo.|
|/serverfarms/virtualnetworkconnections/routes/delete|Excluir rotas de conexões de rede virtual dos Planos do Serviço de Aplicativo.|
|/serverfarms/virtualnetworkconnections/routes/read|Obter a rotas de conexões de rede virtual dos Planos do Serviço de Aplicativo.|
|/serverfarms/virtualnetworkconnections/routes/write|Atualizar rotas de conexões de rede virtual dos Planos do Serviço de Aplicativo.|
|/serverfarms/workers/reboot/action|Reinicializar trabalhos dos Planos do Serviço de Aplicativo.|
|/serverfarms/Write|Criar um novo Plano do Serviço de Aplicativo ou atualizar um plano existente|
|/sites/analyzecustomhostname/read|Analisar nome de host personalizado.|
|/sites/applySlotConfig/Action|Aplicar a configuração de slot de aplicativo Web do slot de destino ao aplicativo Web atual|
|/sites/backup/Action|Criar um novo backup do aplicativo Web|
|/sites/backup/read|Obter o backup de aplicativos Web.|
|/sites/backup/write|Atualizar o backup de aplicativos Web.|
|/sites/backups/delete|Excluir backups de aplicativos Web.|
|/sites/backups/list/action|Listar backups de aplicativos de Web.|
|/sites/backups/Read|Obter as propriedades do backup de um aplicativo Web|
|/sites/backups/restore/action|Restaurar backups de aplicativos Web.|
|/sites/config/delete|Excluir configuração de aplicativos Web.|
|/sites/config/list/Action|Listar as configurações confidenciais de segurança do aplicativo Web, como credenciais de publicação, configurações do aplicativo e cadeias de conexão|
|/sites/config/Read|Obter definições de configuração do aplicativo Web|
|/sites/config/Write|Atualizar definições de configuração do aplicativo Web|
|/sites/continuouswebjobs/delete|Excluir trabalhos da Web contínuos de aplicativos Web.|
|/sites/continuouswebjobs/read|Obter trabalhos de Web contínuos de aplicativos Web.|
|/sites/continuouswebjobs/start/action|Iniciar trabalhos de Web contínuos de aplicativos Web.|
|/sites/continuouswebjobs/stop/action|Interromper trabalhos de Web contínuos de aplicativos Web.|
|/sites/Delete|Excluir um aplicativo Web existente|
|/sites/deployments/delete|Excluir as implantações de aplicativos Web.|
|/sites/deployments/log/read|Obter o log de implantações de aplicativos Web.|
|/sites/deployments/read|Obter as implantações de aplicativos Web.|
|/sites/deployments/write|Atualizar as implantações de aplicativos Web.|
|/sites/diagnostics/analyses/execute/Action|Executar Análise de Diagnóstico de Aplicativos Web.|
|/sites/diagnostics/analyses/read|Obter Análise de Diagnóstico de Aplicativos Web.|
|/sites/diagnostics/aspnetcore/read|Obter Diagnóstico de Aplicativos Web para aplicativo ASP.NET Core.|
|/sites/diagnostics/autoheal/read|Obter Diagnóstico de Aplicativos Web para Autoheal.|
|/sites/diagnostics/deployment/read|Obter a implantação de Diagnóstico de Aplicativos Web.|
|/sites/diagnostics/deployments/read|Obter implantações de diagnóstico de Aplicativos Web.|
|/sites/diagnostics/detectors/execute/Action|Executar o Detector de Diagnóstico de Aplicativos Web.|
|/sites/diagnostics/detectors/read|Obter o Detector de Diagnóstico de Aplicativos Web.|
|/sites/diagnostics/failedrequestsperuri/read|Obter solicitações de falha de diagnóstico de Aplicativos Web por URI.|
|/sites/diagnostics/frebanalysis/read|Obter a análise FREB do diagnóstico de aplicativos Web.|
|/sites/diagnostics/loganalyzer/read|Obter analisador de log de diagnóstico de Aplicativos Web.|
|/sites/diagnostics/read|Obter categorias de diagnóstico de Aplicativos Web.|
|/sites/diagnostics/runtimeavailability/read|Obter a disponibilidade de tempo de execução do diagnóstico de aplicativos Web.|
|/sites/diagnostics/servicehealth/read|Obter integridade do serviço de diagnóstico de aplicativos Web.|
|/sites/diagnostics/sitecpuanalysis/read|Obter análise de CPU do site de diagnóstico de Aplicativos Web.|
|/sites/diagnostics/sitecrashes/read|Obter falhas do Site de Diagnósticos de Aplicativos Web.|
|/sites/diagnostics/sitelatency/read|Obter a latência do Site de Diagnósticos de Aplicativos Web.|
|/sites/diagnostics/sitememoryanalysis/read|Obter análise de memória do site de diagnósticos de Aplicativos Web.|
|/sites/diagnostics/siterestartsettingupdate/read|Obter atualização de configuração de reinicialização do site de diagnóstico de Aplicativos Web.|
|/sites/diagnostics/siterestartuserinitiated/read|Obter reinicialização do site de diagnóstico de Aplicativos Web iniciado pelo usuário.|
|/sites/diagnostics/siteswap/read|Obter troca de site de diagnóstico de Aplicativos Web.|
|/sites/diagnostics/threadcount/read|Obter contagem de threads de diagnóstico de Aplicativos Web.|
|/sites/diagnostics/workeravailability/read|Obter Workeravailability de diagnóstico de aplicativos Web.|
|/sites/diagnostics/workerprocessrecycle/read|Obter a reciclagem do processo de trabalho do diagnóstico de aplicativos Web.|
|/sites/domainownershipidentifiers/read|Obter identificadores de propriedade de domínio de aplicativos Web.|
|/sites/domainownershipidentifiers/write|Atualizar de identificadores de propriedade de domínio de aplicativos Web.|
|/sites/functions/action|Aplicativos Web do Functions.|
|/sites/functions/delete|Excluir funções de aplicativos Web.|
|/sites/functions/listsecrets/action|Listar segredos de funções de aplicativos Web.|
|/sites/functions/masterkey/read|Obter a chave mestra de funções de Aplicativos Web.|
|/sites/functions/read|Obter funções de aplicativos Web.|
|/sites/functions/token/read|Obter token de funções de Aplicativos Web.|
|/sites/functions/write|Atualizar funções de aplicativos Web.|
|/sites/hostnamebindings/delete|Excluir associações de nome de host de aplicativos Web.|
|/sites/hostnamebindings/read|Obter associações de nome de host de aplicativos Web.|
|/sites/hostnamebindings/write|Atualizar associações de nome de host de aplicativos Web.|
|/sites/hybridconnection/delete|Excluir a conexão híbrida de aplicativos Web.|
|/sites/hybridconnection/read|Obter a conexão híbrida de aplicativos Web.|
|/sites/hybridconnection/write|Atualizar a conexão híbrida de aplicativos Web.|
|/sites/hybridconnectionnamespaces/relays/delete|Excluir retransmissões de namespaces de conexão híbrida de Aplicativos Web.|
|/sites/hybridconnectionnamespaces/relays/listkeys/action|Listar chaves de retransmissões de namespaces de conexão híbrida de Aplicativos Web.|
|/sites/hybridconnectionnamespaces/relays/read|Obter retransmissões de namespaces de conexão híbrida de Aplicativos Web.|
|/sites/hybridconnectionnamespaces/relays/write|Atualizar retransmissões de namespaces de conexão híbrida de Aplicativos Web.|
|/sites/hybridconnectionrelays/read|Obter transmissões de conexão híbrida de aplicativos Web.|
|/sites/instances/deployments/delete|Excluir implantações de instâncias de Aplicativos Web.|
|/sites/instances/deployments/read|Obter as implantações de instâncias de aplicativos Web.|
|/sites/instances/extensions/log/read|Obter log de extensões de instâncias de Aplicativos Web.|
|/sites/instances/extensions/read|Obter extensões de instâncias de Aplicativos Web.|
|/sites/instances/processes/delete|Excluir processos de instâncias de aplicativos Web.|
|/sites/instances/processes/read|Obter os processos de instâncias de aplicativos Web.|
|/sites/instances/read|Obter instâncias de aplicativos Web.|
|/sites/listsyncfunctiontriggerstatus/action|Listar Aplicativos Web de status do gatilho da função de sincronização.|
|/sites/metricdefinitions/read|Obter definições de métrica de aplicativos Web.|
|/sites/metrics/read|Métrica de aplicativos Web.|
|/sites/metricsdefinitions/read|Obter definições de métricas de Aplicativos Web.|
|/sites/migratemysql/action|Migrar Aplicativos Web do MySql.|
|/sites/migratemysql/read|Obter migração do MySql de Aplicativos Web.|
|/sites/networktrace/action|Aplicativos Web de rastreamento de rede.|
|/sites/newpassword/action|Aplicativos Web Newpassword.|
|/sites/operationresults/read|Obter resultados de operação de aplicativos Web.|
|/sites/operations/read|Obter operações de Aplicativos Web.|
|/sites/perfcounters/read|Obter contadores de desempenho de aplicativos Web.|
|/sites/premieraddons/delete|Excluir complementos Premier de aplicativos Web.|
|/sites/premieraddons/read|Obter complementos Premier de aplicativos Web.|
|/sites/premieraddons/write|Atualizar complementos Premier de aplicativos Web.|
|/sites/processes/read|Obter processos de Aplicativos Web.|
|/sites/providers/Microsoft.Insights/diagnosticSettings/read|Obter a configuração de diagnóstico para o recurso|
|/sites/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o recurso|
|/sites/providers/Microsoft.Insights/metricDefinitions/Read|Obter as métricas disponíveis para Aplicativo Web|
|/sites/publiccertificates/delete|Excluir certificados públicos de Aplicativos Web.|
|/sites/publiccertificates/read|Obter certificados públicos de Aplicativos Web.|
|/sites/publiccertificates/write|Atualizar certificados públicos de Aplicativos Web.|
|/sites/publish/Action|Publicar um aplicativo Web|
|/sites/publishxml/Action|Obter XML do perfil de publicação para um aplicativo Web|
|/sites/publishxml/read|Obter XML de publicação de aplicativos Web.|
|/sites/Read|Obter as propriedades de um aplicativo Web|
|/sites/recommendationhistory/read|Obter o histórico de recomendação de aplicativos Web.|
|/sites/recommendations/disable/action|Desabilitar as recomendações de aplicativos Web.|
|/sites/recommendations/Read|Obter a lista de recomendações para o aplicativo Web.|
|/sites/recover/action|Recuperar Aplicativos Web.|
|/sites/resetSlotConfig/Action|Redefinir a configuração de aplicativo Web|
|/sites/resourcehealthmetadata/read|Obter metadados do Resource Health de Aplicativos Web.|
|/sites/restart/Action|Reiniciar um aplicativo Web|
|/sites/restore/read|Obter a restauração de aplicativos Web.|
|/sites/restore/write|Restaurar Aplicativos Web.|
|/sites/siteextensions/delete|Excluir extensões de site de Aplicativos Web.|
|/sites/siteextensions/read|Obter extensões de site de Aplicativos Web.|
|/sites/siteextensions/write|Atualizar extensões de site de Aplicativos Web.|
|/sites/slots/analyzecustomhostname/read|Obter slots de aplicativos Web Analisar nome de host personalizado.|
|/sites/slots/applySlotConfig/Action|Aplicar a configuração de slot de aplicativo Web do slot de destino ao slot atual.|
|/sites/slots/backup/Action|Criar o novo backup do slot do aplicativo Web.|
|/sites/slots/backup/read|Obter backup de Slots de Aplicativos Web.|
|/sites/slots/backup/write|Atualizar o backup de slots de aplicativos Web.|
|/sites/slots/backups/delete|Excluir backups de slots de Aplicativos Web.|
|/sites/slots/backups/list/action|Listar backups de slots de aplicativos de Web.|
|/sites/slots/backups/Read|Obter as propriedades do backup dos slots de um aplicativo Web|
|/sites/slots/backups/restore/action|Restaurar backups de slots de aplicativos Web.|
|/sites/slots/config/delete|Excluir configuração de slots de aplicativos Web.|
|/sites/slots/config/list/Action|Listar as configurações confidenciais de segurança do slot do aplicativo Web, como credenciais de publicação, configurações do aplicativo e cadeias de conexão|
|/sites/slots/config/Read|Obter definições de configuração do slot do aplicativo Web|
|/sites/slots/config/Write|Atualizar definições de configuração do slot do aplicativo Web|
|/sites/slots/continuouswebjobs/delete|Excluir trabalhos da Web contínuos de slots de aplicativos Web.|
|/sites/slots/continuouswebjobs/read|Obter trabalhos da Web contínuos de slots de aplicativos Web.|
|/sites/slots/continuouswebjobs/start/action|Iniciar trabalhos da Web contínuos de slots de aplicativos Web.|
|/sites/slots/continuouswebjobs/stop/action|Interromper trabalhos da Web contínuos de slots de aplicativos Web.|
|/sites/slots/Delete|Excluir um slot de aplicativo Web existente|
|/sites/slots/deployments/delete|Excluir as implantações de slots de aplicativos Web.|
|/sites/slots/deployments/log/read|Obter o log de implantações dos slots de aplicativos Web.|
|/sites/slots/deployments/read|Obter as implantações dos slots de aplicativos Web.|
|/sites/slots/deployments/write|Atualizar as implantações de slots de aplicativos Web.|
|/sites/slots/diagnostics/analyses/execute/Action|Executar análise de diagnóstico de slots de Aplicativos Web.|
|/sites/slots/diagnostics/analyses/read|Obter análise de diagnóstico de slots de Aplicativos Web.|
|/sites/slots/diagnostics/aspnetcore/read|Obter o diagnóstico de slots de Aplicativos Web para o aplicativo ASP.NET Core.|
|/sites/slots/diagnostics/autoheal/read|Obter o diagnóstico de slots de Aplicativos Web para Autoheal.|
|/sites/slots/diagnostics/deployment/read|Obter implantação de diagnóstico de slots de Aplicativos Web.|
|/sites/slots/diagnostics/deployments/read|Obter implantações de diagnóstico de slots de Aplicativos Web.|
|/sites/slots/diagnostics/detectors/execute/Action|Executar o detector de diagnóstico de slots de aplicativos Web.|
|/sites/slots/diagnostics/detectors/read|Obter detector de diagnóstico de slots de Aplicativos Web.|
|/sites/slots/diagnostics/frebanalysis/read|Obter análise FREB de diagnóstico de slots de Aplicativos Web.|
|/sites/slots/diagnostics/loganalyzer/read|Obter analisador de log de diagnóstico de slots de Aplicativos Web.|
|/sites/slots/diagnostics/read|Obter diagnóstico de slots de Aplicativos Web.|
|/sites/slots/diagnostics/runtimeavailability/read|Obter disponibilidade de tempo de execução de diagnóstico de slots de Aplicativos Web.|
|/sites/slots/diagnostics/servicehealth/read|Obter Integridade do Serviço do Azure do diagnóstico de slots de Aplicativos Web.|
|/sites/slots/diagnostics/sitecpuanalysis/read|Obter análise de CPU de site de diagnóstico de slots de Aplicativos Web.|
|/sites/slots/diagnostics/sitecrashes/read|Obter falhas nos sites de diagnóstico dos slots de Aplicativos Web.|
|/sites/slots/diagnostics/sitelatency/read|Obter latência de sites de diagnóstico de slots de Aplicativos Web.|
|/sites/slots/diagnostics/sitememoryanalysis/read|Obter análise de memória do site de diagnóstico de slots de Aplicativos Web.|
|/sites/slots/diagnostics/siterestartsettingupdate/read|Obter atualização de configuração de reinicialização do site de diagnóstico de slots de Aplicativos Web.|
|/sites/slots/diagnostics/siterestartuserinitiated/read|Obter diagnóstico de slots de Aplicativos Web iniciado pelo usuário.|
|/sites/slots/diagnostics/siteswap/read|Obter troca de sites de diagnóstico de slots de Aplicativos Web.|
|/sites/slots/diagnostics/threadcount/read|Obter contagem de threads de diagnóstico de slots de Aplicativos Web.|
|/sites/slots/diagnostics/workeravailability/read|Obter disponibilidade do trabalho de diagnóstico de slots de Aplicativos Web.|
|/sites/slots/diagnostics/workerprocessrecycle/read|Obter reciclagem de processo de trabalho de diagnóstico de slots de Aplicativos Web.|
|/sites/slots/domainownershipidentifiers/read|Obter identificadores de propriedade de domínio de slots de Aplicativos Web.|
|/sites/slots/hostnamebindings/delete|Excluir vínculos de nome de host de slots de aplicativos Web.|
|/sites/slots/hostnamebindings/read|Obter vínculos de nome de host de slots de aplicativos Web.|
|/sites/slots/hostnamebindings/write|Atualizar vínculos de nome de host de slots de aplicativos Web.|
|/sites/slots/hybridconnection/delete|Excluir a conexão híbrida de slots de aplicativos Web.|
|/sites/slots/hybridconnection/read|Obter a conexão híbrida de slots de aplicativos Web.|
|/sites/slots/hybridconnection/write|Atualizar a conexão híbrida de slots de aplicativos Web.|
|/sites/slots/hybridconnectionnamespaces/relays/delete|Excluir retransmissões de namespaces de conexão híbrida de slots de Aplicativos Web.|
|/sites/slots/hybridconnectionnamespaces/relays/write|Atualizar retransmissões de namespaces de conexão híbrida de slots de Aplicativos Web.|
|/sites/slots/hybridconnectionrelays/read|Obter retransmissões de conexão híbrida de slots de Aplicativos Web.|
|/sites/slots/instances/deployments/read|Obter as implantações de instâncias dos slots de aplicativos Web.|
|/sites/slots/instances/processes/delete|Excluir processos de instâncias de slots de plicativos Web.|
|/sites/slots/instances/processes/read|Obter os processos de instâncias dos slots de aplicativos Web.|
|/sites/slots/instances/read|Obter instâncias dos slots de aplicativos Web.|
|/sites/slots/metricdefinitions/read|Obter definições de métrica de slots de aplicativos Web.|
|/sites/slots/metrics/read|Obter métrica dos slots de aplicativos Web.|
|/sites/slots/migratemysql/read|Obter migração do MySql de slots de Aplicativos Web.|
|/sites/slots/networktrace/action|Slots de Aplicativos Web de rastreamento de rede.|
|/sites/slots/newpassword/action|Slots de aplicativos Web Newpassword.|
|/sites/slots/operationresults/read|Obter resultados de operação de slots de aplicativos Web.|
|/sites/slots/operations/read|Obter operações de slots de Aplicativos Web.|
|/sites/slots/perfcounters/read|Obter contadores de desempenho de slots de Aplicativos Web.|
|/sites/slots/phplogging/read|Obter Phplogging dos slots de aplicativos Web.|
|/sites/slots/premieraddons/delete|Excluir complementos de Premier de slots de aplicativos Web.|
|/sites/slots/premieraddons/read|Obter complementos Premier de slots de aplicativos Web.|
|/sites/slots/premieraddons/write|Atualizar complementos Premier de slots de aplicativos Web.|
|/sites/slots/providers/Microsoft.Insights/diagnosticSettings/read|Obter a configuração de diagnóstico para o recurso|
|/sites/slots/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o recurso|
|/sites/slots/providers/Microsoft.Insights/metricDefinitions/Read|Obter as métricas disponíveis para slot de Aplicativo Web|
|/sites/slots/publiccertificates/read|Obter certificados públicos de slots de Aplicativos Web.|
|/sites/slots/publiccertificates/write|Criar ou atualizar certificados públicos de slots de Aplicativos Web.|
|/sites/slots/publish/Action|Publicar um slot do aplicativo Web|
|/sites/slots/publishxml/Action|Obter XML do perfil de publicação para um slot de aplicativo Web|
|/sites/slots/Read|Obter as propriedades de um slot de implantação de aplicativo Web|
|/sites/slots/resetSlotConfig/Action|Redefinir a configuração de slot do aplicativo Web|
|/sites/slots/resourcehealthmetadata/read|Obter metadados do Resource Health de Aplicativos Web.|
|/sites/slots/restart/Action|Reiniciar um slot de aplicativo Web|
|/sites/slots/restore/read|Obter a restauração dos slots de aplicativos Web.|
|/sites/slots/restore/write|Restaurar slots de Aplicativos Web.|
|/sites/slots/siteextensions/delete|Excluir extensões de site de slots de Aplicativos Web.|
|/sites/slots/siteextensions/read|Obter extensões de site de slots de Aplicativos Web.|
|/sites/slots/siteextensions/write|Atualizar extensões de site de slots de Aplicativos Web.|
|/sites/slots/slotsdiffs/Action|Obter as diferenças de configuração entre aplicativo web e slots|
|/sites/slots/slotsswap/Action|Trocar slots de implantação de aplicativo Web|
|/sites/slots/snapshots/read|Obter instantâneos de slots de Aplicativos Web.|
|/sites/slots/sourcecontrols/Delete|Excluir definições de configuração de controle de origem do slot do aplicativo Web|
|/sites/slots/sourcecontrols/Read|Obter definições de configuração de controle de origem do slot do aplicativo Web|
|/sites/slots/sourcecontrols/Write|Atualizar definições de configuração de controle de origem do slot do aplicativo Web|
|/sites/slots/start/Action|Iniciar um slot de aplicativo Web|
|/sites/slots/stop/Action|Interromper um slot de aplicativo Web|
|/sites/slots/sync/action|Sincronizar slots de aplicativos Web.|
|/sites/slots/triggeredwebjobs/delete|Excluir trabalhos da Web disparados de slots de aplicativos Web.|
|/sites/slots/triggeredwebjobs/read|Obter trabalhos da Web disparados de slots de aplicativos Web.|
|/sites/slots/triggeredwebjobs/run/action|Executar trabalhos da Web dos slots de aplicativos Web.|
|/sites/slots/usages/read|Obter usos dos slots de aplicativos Web.|
|/sites/slots/virtualnetworkconnections/delete|Excluir conexões de rede virtual de slots de aplicativos Web.|
|/sites/slots/virtualnetworkconnections/gateways/write|Atualizar gateways de conexões de rede virtual de slots de aplicativos Web.|
|/sites/slots/virtualnetworkconnections/read|Obter conexões de rede virtual dos slots de aplicativos Web.|
|/sites/slots/virtualnetworkconnections/write|Atualizar conexões de rede virtual de slots de aplicativos Web.|
|/sites/slots/webjobs/read|Obter trabalhos da Web dos slots de aplicativos Web.|
|/sites/slots/Write|Criar um novo slot do aplicativo Web ou atualizar um existente|
|/sites/slotsdiffs/Action|Obter as diferenças de configuração entre aplicativo web e slots|
|/sites/slotsswap/Action|Trocar slots de implantação de aplicativo Web|
|/sites/snapshots/read|Obter instantâneos de aplicativos Web.|
|/sites/sourcecontrols/Delete|Excluir definições de configuração de controle de origem do aplicativo Web|
|/sites/sourcecontrols/Read|Obter configurações de controle de origem do aplicativo Web|
|/sites/sourcecontrols/Write|Atualizar definições de configuração de controle de origem do aplicativo Web|
|/sites/start/Action|Iniciar um aplicativo Web|
|/sites/stop/Action|Interromper um aplicativo Web|
|/sites/sync/action|Sincronizar pplicativos Web.|
|/sites/syncfunctiontriggers/action|Gatilhos de função de sincronização para Aplicativos Web.|
|/sites/triggeredwebjobs/delete|Excluir trabalhos da Web disparados para aplicativos Web.|
|/sites/triggeredwebjobs/history/read|Obter o histórico de WebJobs disparados de Aplicativos Web.|
|/sites/triggeredwebjobs/read|Obter trabalhos da Web disparados de aplicativos Web.|
|/sites/triggeredwebjobs/run/action|Executar trabalhos da Web disparados de aplicativos Web.|
|/sites/usages/read|Obter usos de aplicativos Web.|
|/sites/virtualnetworkconnections/delete|Excluir as conexões de rede virtual de aplicativos Web.|
|/sites/virtualnetworkconnections/gateways/read|Obter gateways de conexões de rede virtual de aplicativos Web.|
|/sites/virtualnetworkconnections/gateways/write|Atualizar gateways de conexões de rede virtual de aplicativos Web.|
|/sites/virtualnetworkconnections/read|Obter as conexões de rede virtual de aplicativos Web.|
|/sites/virtualnetworkconnections/write|Atualizar as conexões de rede virtual de aplicativos Web.|
|/sites/webjobs/read|Obter trabalhos da Web de aplicativos Web.|
|/sites/Write|Criar um novo aplicativo Web ou atualizar um existente|
|/skus/read|Obter SKUs.|
|/sourcecontrols/read|Obter os controles de origem.|
|/sourcecontrols/write|Atualizar controles de origem.|
|/unregister/action|Cancelar o registro do provedor de recursos Microsoft.Web para a assinatura.|
|/validate/action|Validar.|
|/verifyhostingenvironmentvnet/action|Verificar VNET no ambiente de hospedagem.|

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

| Operação | DESCRIÇÃO |
|---|---|
|/components/read|Ler recursos de operações|
|/healthInstances/read|Ler recursos de operações|
|/Operations/read|Ler recursos de operações|
|/workloads/delete|Excluir um recurso de carga de trabalho|
|/workloads/read|Ler um recurso de carga de trabalho|
|/workloads/write|Gravar um recurso de carga de trabalho|

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar uma função personalizada](role-based-access-control-custom-roles.md).
- Examine as [funções RBAC incorporadas](role-based-access-built-in-roles.md).
- Saiba como gerenciar atribuições de acesso [por usuário](role-based-access-control-manage-assignments.md) ou [por recurso](role-based-access-control-configure.md) 
- Aprenda a [Exibir logs de atividade para auditar ações em recursos](~/articles/azure-resource-manager/resource-group-audit.md)
