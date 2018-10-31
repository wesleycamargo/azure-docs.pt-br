---
title: Operações do provedor de recursos do Azure Resource Manager | Microsoft Docs
description: Lista as operações disponíveis nos provedores de recursos do Microsoft Azure Resource Manager
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/19/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 7d044559d72b07dc86a5ee1c2269452a9059de61
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49471502"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Operações do provedor de recursos do Azure Resource Manager

Este artigo lista as operações disponíveis para cada provedor de recursos do Azure Resource Manager. Essas operações podem ser usadas em [funções personalizadas](custom-roles.md) para fornecer [RBAC (Controle de acesso baseado em função)](overview.md) granular para recursos no Azure. As cadeias de caracteres de operação têm o seguinte formato: `{Company}.{ProviderName}/{resourceType}/{action}`

As operações do provedor de recursos estão sempre em evolução. Para obter as operações mais recentes, use [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) ou [az provider operation list](/cli/azure/provider/operation#az-provider-operation-list).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.AAD/domainServices/delete | Excluir Serviço de Domínio |
> | Ação | Microsoft.AAD/domainServices/oucontainer/delete | Excluir contêiner de unidade organizacional |
> | Ação | Microsoft.AAD/domainServices/oucontainer/read | Ler contêineres de unidade organizacional |
> | Ação | Microsoft.AAD/domainServices/oucontainer/write | Gravar contêiner de unidade organizacional |
> | Ação | Microsoft.AAD/domainServices/read | Ler serviços de domínio |
> | Ação | Microsoft.AAD/domainServices/write | Gravar Serviço de Domínio |
> | Ação | Microsoft.AAD/locations/operationresults/read |  |
> | Ação | Microsoft.AAD/Operations/read |  |
> | Ação | Microsoft.AAD/register/action | Registrar Serviço de Domínio |
> | Ação | Microsoft.AAD/unregister/action | Cancelar registrar Serviço de Domínio |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | microsoft.aadiam/diagnosticsettings/delete | Exclusão de uma configuração de diagnóstico |
> | Ação | microsoft.aadiam/diagnosticsettings/read | Leitura de uma configuração de diagnóstico |
> | Ação | microsoft.aadiam/diagnosticsettings/write | Gravação de uma configuração de diagnóstico |
> | Ação | microsoft.aadiam/diagnosticsettingscategories/read | Leitura de uma categoria de configuração de diagnóstico |
> | Ação | microsoft.aadiam/tenants/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | microsoft.aadiam/tenants/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | microsoft.aadiam/tenants/providers/Microsoft.Insights/logDefinitions/read | Obter os logs disponíveis para locatários |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Addons/operations/read | Obtém as operações de RP compatíveis. |
> | Ação | Microsoft.Addons/register/action | Registra a assinatura especificada com Microsoft.Addons |
> | Ação | Microsoft.Addons/supportProviders/listsupportplaninfo/action | Lista informações do plano de suporte atual para a assinatura especificada. |
> | Ação | Microsoft.Addons/supportProviders/supportPlanTypes/delete | Remove o plano de suporte da Canonical especificado |
> | Ação | Microsoft.Addons/supportProviders/supportPlanTypes/read | Obtém o estado do plano de suporte da Canonical especificado. |
> | Ação | Microsoft.Addons/supportProviders/supportPlanTypes/write | Adiciona o tipo de plano de suporte da Canonical especificado. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.ADHybridHealthService/addsservices/action | Cria uma nova floresta para o locatário. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | Obtém todos os servidores para o nome do serviço especificado. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/alerts/read | Obtém detalhes de alertas da floresta como alertid, data da geração do alerta, último alerta detectado, descrição do alerta, última atualização, nível do alerta, estado do alerta, links de solução de problemas do alerta etc. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/configuration/read | Obtém a Configuração de Serviço da floresta. Exemplo: Nome da Floresta, Nível Funcional, Função de FSMO mestre de Nomeação de Domínio, Função de FSMO mestre de esquema etc. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/delete | Exclui um serviço e seu servidores junto com os dados de integridade. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/dimensions/read | Obtém os detalhes de sites e domínios da floresta. Exemplo: status da integridade, alertas ativos, alertas resolvidos, propriedades como Nível Funcional do Domínio, Floresta, Mestre de Infraestrutura, Controlador de Domínio Primário, Mestre RID etc.  |
> | Ação | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | Obtém a configuração de preferência do usuário da floresta.<br>Exemplo: MetricCounterName como ldapsuccessfulbinds, ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes, ldapsearches.<br>Configurações para Gráficos da Interface do Usuário etc. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | Obtém o resumo da floresta especificada, como o nome da floresta, o número de domínios sob essa floresta, o número de sites e detalhes do site etc. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | Obtém a lista de métricas compatíveis para um determinado serviço.<br>Por exemplo, Bloqueios de Conta de Extranet, Total de Solicitações com Falha, Solicitações de Token Pendentes (Proxy), Solicitações de Token/s etc. para o serviço ADFS.<br>Autenticações NTLM/s, Associações LDAP com Êxito/s, Tempo de Associação LDAP, Threads LDAP Ativos, Autenticações Kerberos/s, Total de Threads ATQ etc. para ADDomainService.<br>Executar Latência do Perfil, Conexões TCP Estabelecidas, Bytes Particulares do Agente do Insights, Exportar Estatísticas para o Azure AD para o serviço ADSync. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | Dado um serviço, essa API obtém as informações de métricas.<br>Por exemplo, essa API pode ser usada para obter informações relacionadas a: Bloqueios de Conta de Extranet, Total de Solicitações com Falha, Solicitações de Token Pendentes (Proxy), Solicitações de Token/s etc. para o serviço ADFederation.<br>Autenticações NTLM/s, Associações LDAP com Êxito/s, Tempo de Associação LDAP, Threads LDAP Ativos, Autenticações Kerberos/s, Total de Threads ATQ etc. para o Serviço ADDomain.<br>Executar Latência do Perfil, Conexões TCP Estabelecidas, Bytes Particulares do Agente do Insights, Exportar Estatísticas para o Azure AD para o Serviço de Sincronização. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | Essa API obtém a lista de todos os ADDomainServices incorporados para um locatário premium. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/read | Obtém os detalhes do serviço para o nome do serviço especificado. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | Obtém detalhes de replicação de todos os servidores para o nome do serviço especificado. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | Obtém o número de controladores de domínio e seus erros de replicação, se houver. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | Obtém a lista completa de controladores de domínio juntamente com detalhes de replicação da floresta especificada. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | Adiciona uma instância de servidor ao serviço. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | Durante o registro do servidor de ADDomainService, essa API é chamada para obter as credenciais para a integração de novos servidores. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | Exclui um servidor para um determinado serviço e locatário. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/write | Cria ou atualiza a instância de ADDomainService para o locatário. |
> | Ação | Microsoft.ADHybridHealthService/configuration/action | Atualizar a configuração do locatário. |
> | Ação | Microsoft.ADHybridHealthService/configuration/read | Ler a configuração de locatário. |
> | Ação | Microsoft.ADHybridHealthService/configuration/write | Criar uma configuração de locatário. |
> | Ação | Microsoft.ADHybridHealthService/logs/contents/read | Obtém o conteúdo da instalação do agente e os logs de registro armazenados no blob. |
> | Ação | Microsoft.ADHybridHealthService/logs/read | Obtém os logs de registro e instalação do agente para o locatário. |
> | Ação | Microsoft.ADHybridHealthService/operations/read | Obtém a lista de operações compatíveis com o sistema. |
> | Ação | Microsoft.ADHybridHealthService/register/action | Registra o Provedor de Recursos do Serviço de Integridade ADHybrid e habilita a criação do recurso de Serviço de Integridade ADHybrid. |
> | Ação | Microsoft.ADHybridHealthService/reports/availabledeployments/read | Obtém a lista de regiões disponíveis, usada pelo DevOps para dar suporte a incidentes de cliente. |
> | Ação | Microsoft.ADHybridHealthService/reports/badpassword/read | Obtém a lista de tentativas com senhas incorretas de todos os usuários no Serviço de Federação do Active Directory. |
> | Ação | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | Obtém o URI de SAS do Blob que contém o status e o resultado eventual do trabalho de relatório recém-enfileirado para a frequência de Tentativas de Nome de Usuário/Senha incorretas por UserId por IPAddress por dia para um determinado locatário. |
> | Ação | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | Obtém a lista de locatários permitidos do DevOps. Normalmente usado para suporte ao cliente. |
> | Ação | Microsoft.ADHybridHealthService/reports/isdevops/read | Obtém um valor que indica se o locatário é permitido pelo DevOps ou não. |
> | Ação | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | Atualiza o Userid(objectid) para o locatário do DevOps selecionado. |
> | Ação | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | Obtém a implantação selecionada para o locatário determinado. |
> | Ação | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | Dada uma ID de locatário, obtém o local de armazenamento do locatário. |
> | Ação | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | Obtém a localização geográfica da qual os dados serão acessados. |
> | Ação | Microsoft.ADHybridHealthService/services/action | Atualizar uma instância de serviço no locatário. |
> | Ação | Microsoft.ADHybridHealthService/services/alerts/read | Ler os alertas de um serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/alerts/read | Ler os alertas de um serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | Dado um nome de recurso, verifica se um serviço tem tudo o que é necessário para usar tal recurso. |
> | Ação | Microsoft.ADHybridHealthService/services/delete | Excluir uma instância de serviço no locatário. |
> | Ação | Microsoft.ADHybridHealthService/services/exporterrors/read | Obtém os erros de exportação para um determinado serviço de sincronização. |
> | Ação | Microsoft.ADHybridHealthService/services/exportstatus/read | Obtém os status de exportação para um determinado serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | Obtém os comentários de alertas para um determinado serviço e servidor. |
> | Ação | Microsoft.ADHybridHealthService/services/metricmetadata/read | Obtém a lista de métricas compatíveis para um determinado serviço.<br>Por exemplo, Bloqueios de Conta de Extranet, Total de Solicitações com Falha, Solicitações de Token Pendentes (Proxy), Solicitações de Token/s etc. para o serviço ADFS.<br>Autenticações NTLM/s, Associações LDAP com Êxito/s, Tempo de Associação LDAP, Threads LDAP Ativos, Autenticações Kerberos/s, Total de Threads ATQ etc. para ADDomainService.<br>Executar Latência do Perfil, Conexões TCP Estabelecidas, Bytes Particulares do Agente do Insights, Exportar Estatísticas para o Azure AD para o serviço ADSync. |
> | Ação | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | Dado um serviço, essa API obtém a média das métricas para um determinado serviço.<br>Por exemplo, essa API pode ser usada para obter informações relacionadas a: Bloqueios de Conta de Extranet, Total de Solicitações com Falha, Solicitações de Token Pendentes (Proxy), Solicitações de Token/s etc. para o serviço ADFederation.<br>Autenticações NTLM/s, Associações LDAP com Êxito/s, Tempo de Associação LDAP, Threads LDAP Ativos, Autenticações Kerberos/s, Total de Threads ATQ etc. para o Serviço ADDomain.<br>Executar Latência do Perfil, Conexões TCP Estabelecidas, Bytes Particulares do Agente do Insights, Exportar Estatísticas para o Azure AD para o Serviço de Sincronização. |
> | Ação | Microsoft.ADHybridHealthService/services/metrics/groups/read | Dado um serviço, essa API obtém as informações de métricas.<br>Por exemplo, essa API pode ser usada para obter informações relacionadas a: Bloqueios de Conta de Extranet, Total de Solicitações com Falha, Solicitações de Token Pendentes (Proxy), Solicitações de Token/s etc. para o serviço ADFederation.<br>Autenticações NTLM/s, Associações LDAP com Êxito/s, Tempo de Associação LDAP, Threads LDAP Ativos, Autenticações Kerberos/s, Total de Threads ATQ etc. para o Serviço ADDomain.<br>Executar Latência do Perfil, Conexões TCP Estabelecidas, Bytes Particulares do Agente do Insights, Exportar Estatísticas para o Azure AD para o Serviço de Sincronização. |
> | Ação | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | Dado um serviço, essa API obtém a exibição agregada da métrica para um determinado serviço.<br>Por exemplo, essa API pode ser usada para obter informações relacionadas a: Bloqueios de Conta de Extranet, Total de Solicitações com Falha, Solicitações de Token Pendentes (Proxy), Solicitações de Token/s etc. para o serviço ADFederation.<br>Autenticações NTLM/s, Associações LDAP com Êxito/s, Tempo de Associação LDAP, Threads LDAP Ativos, Autenticações Kerberos/s, Total de Threads ATQ etc. para o Serviço ADDomain.<br>Executar Latência do Perfil, Conexões TCP Estabelecidas, Bytes Particulares do Agente do Insights, Exportar Estatísticas para o Azure AD para o Serviço de Sincronização. |
> | Ação | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | Adiciona ou atualiza a configuração de monitoramento de um serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | Obtém as configurações de monitoramento de um determinado serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | Adiciona ou atualiza as configurações de monitoramento de um serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/premiumcheck/read | Essa API obtém a lista de todos os serviços incorporados para um locatário premium. |
> | Ação | Microsoft.ADHybridHealthService/services/read | Ler as instâncias de serviço no locatário. |
> | Ação | Microsoft.ADHybridHealthService/services/reports/details/read | Obtém o relatório dos primeiros 50 usuários com erros de senhas incorretas dos últimos sete dias |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/action | Cria uma instância de servidor no serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | Lê os alertas de um servidor. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | Durante o registro do servidor, essa API é chamada para obter as credenciais para a integração de novos servidores. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | Para um determinado servidor, essa API obtém uma lista dos tipos de dados que estão sendo carregados pelos servidores e a hora mais recente de cada upload. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/delete | Exclui uma instância de servidor no serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | Obtém os detalhes do Erro de Exportação de Sincronização para um determinado Serviço de Sincronização. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | Dado um serviço, essa API obtém as informações de métricas.<br>Por exemplo, essa API pode ser usada para obter informações relacionadas a: Bloqueios de Conta de Extranet, Total de Solicitações com Falha, Solicitações de Token Pendentes (Proxy), Solicitações de Token/s etc. para o serviço ADFederation.<br>Autenticações NTLM/s, Associações LDAP com Êxito/s, Tempo de Associação LDAP, Threads LDAP Ativos, Autenticações Kerberos/s, Total de Threads ATQ etc. para o Serviço ADDomain.<br>Executar Latência do Perfil, Conexões TCP Estabelecidas, Bytes Particulares do Agente do Insights, Exportar Estatísticas para o Azure AD para o Serviço de Sincronização. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/read | Lê a instância de servidor no serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | Obtém a configuração de serviço de um determinado locatário. |
> | Ação | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | Obtém o status de lista de permissões de recurso de um determinado locatário. |
> | Ação | Microsoft.ADHybridHealthService/services/write | Criar uma instância de serviço no locatário. |
> | Ação | Microsoft.ADHybridHealthService/unregister/action | Cancela o registro da assinatura do Provedor de Recursos do Serviço de Integridade ADHybrid. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Advisor/configurations/read | Obter configurações |
> | Ação | Microsoft.Advisor/configurations/write | Criar/atualizar configuração |
> | Ação | Microsoft.Advisor/generateRecommendations/action | Gera recomendações |
> | Ação | Microsoft.Advisor/generateRecommendations/read | Obter o status de gerar recomendações |
> | Ação | Microsoft.Advisor/operations/read | Obter as operações para Assistente do Azure |
> | Ação | Microsoft.Advisor/recommendations/available/action | Nova recomendação está disponível no Microsoft advisor |
> | Ação | Microsoft.Advisor/recommendations/read | Ler recomendações |
> | Ação | Microsoft.Advisor/recommendations/suppressions/delete | Excluir supressão |
> | Ação | Microsoft.Advisor/recommendations/suppressions/read | Obter supressões |
> | Ação | Microsoft.Advisor/recommendations/suppressions/write | Criar/atualizar supressões |
> | Ação | Microsoft.Advisor/register/action | Registra a assinatura do Microsoft Advisor |
> | Ação | Microsoft.Advisor/suppressions/delete | Excluir supressão |
> | Ação | Microsoft.Advisor/suppressions/read | Obter supressões |
> | Ação | Microsoft.Advisor/suppressions/write | Criar/atualizar supressões |
> | Ação | Microsoft.Advisor/unregister/action | Cancelar o registro da assinatura do Assistente do Azure |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.AlertsManagement/alerts/changestate/action | Altera o estado do alerta. |
> | Ação | Microsoft.AlertsManagement/alerts/read | Obter todos os alertas para os filtros de entrada. |
> | Ação | Microsoft.AlertsManagement/alertsSummary/read | Obter o resumo dos alertas |
> | Ação | Microsoft.AlertsManagement/Operations/read | Ler as operações fornecidas |
> | Ação | Microsoft.AlertsManagement/smartGroups/changestate/action | Altera o estado do grupo inteligente |
> | Ação | Microsoft.AlertsManagement/smartGroups/read | Obtém todos os grupos inteligentes para os filtros de entrada |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.AnalysisServices/locations/checkNameAvailability/action | Verificar se determinado nome do Analysis Server é válido e não está em uso. |
> | Ação | Microsoft.AnalysisServices/locations/operationresults/read | Recuperar as informações do resultado da operação especificada. |
> | Ação | Microsoft.AnalysisServices/locations/operationstatuses/read | Recuperar as informações do status da operação especificada. |
> | Ação | Microsoft.AnalysisServices/operations/read | Recuperar as informações das operações |
> | Ação | Microsoft.AnalysisServices/register/action | Registrar o provedor de recursos do Analysis Services. |
> | Ação | Microsoft.AnalysisServices/servers/delete | Excluir o Analysis Server. |
> | Ação | Microsoft.AnalysisServices/servers/listGatewayStatus/action | Listar o status do gateway associado ao servidor. |
> | Ação | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o Analysis Server |
> | Ação | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico do Analysis Server |
> | Ação | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/logDefinitions/read | Obter os logs disponíveis para servidores |
> | Ação | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para o Analysis Server |
> | Ação | Microsoft.AnalysisServices/servers/read | Recupera as informações do Analysis Server especificado. |
> | Ação | Microsoft.AnalysisServices/servers/resume/action | Retoma o Analysis Server. |
> | Ação | Microsoft.AnalysisServices/servers/skus/read | Recuperar informações de SKU disponíveis para o servidor |
> | Ação | Microsoft.AnalysisServices/servers/suspend/action | Suspende o Analysis Server. |
> | Ação | Microsoft.AnalysisServices/servers/write | Criar ou atualizar o Analysis Server especificado. |
> | Ação | Microsoft.AnalysisServices/skus/read | Recuperar as informações de SKUs |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.ApiManagement/checkNameAvailability/read | Verificar se o nome do serviço fornecido está disponível |
> | Ação | Microsoft.ApiManagement/operations/read | Ler todas as operações de API disponíveis para o recurso Microsoft.ApiManagement |
> | Ação | Microsoft.ApiManagement/register/action | Registrar a assinatura para o provedor de recursos Microsoft.ApiManagement |
> | Ação | Microsoft.ApiManagement/reports/read | Obter relatórios agregados por períodos de tempo, região geográfica, desenvolvedores, produtos, APIs, operações, assinatura e byRequest. |
> | Ação | Microsoft.ApiManagement/service/apis/delete | Remover a API existente |
> | Ação | Microsoft.ApiManagement/service/apis/diagnostics/delete | Remover o diagnóstico existente |
> | Ação | Microsoft.ApiManagement/service/apis/diagnostics/read | Obter lista de diagnósticos ou Obter detalhes do diagnóstico |
> | Ação | Microsoft.ApiManagement/service/apis/diagnostics/write | Adicionar novo diagnóstico ou Atualizar detalhes de diagnóstico existente |
> | Ação | Microsoft.ApiManagement/service/apis/issues/attachments/delete | Remove o anexo existente |
> | Ação | Microsoft.ApiManagement/service/apis/issues/attachments/read | Obtém os anexos de problema ou os detalhes do anexo do problema do Gerenciamento de API |
> | Ação | Microsoft.ApiManagement/service/apis/issues/attachments/write | Adiciona o anexo do problema de API |
> | Ação | Microsoft.ApiManagement/service/apis/issues/comments/delete | Remove o comentário existente |
> | Ação | Microsoft.ApiManagement/service/apis/issues/comments/read | Obtém comentários do problema ou detalhes do comentário do problema do Gerenciamento de API |
> | Ação | Microsoft.ApiManagement/service/apis/issues/comments/write | Adiciona o comentário do problema da API |
> | Ação | Microsoft.ApiManagement/service/apis/issues/delete | Remover o problema existente |
> | Ação | Microsoft.ApiManagement/service/apis/issues/read | Obtém os problemas associados à API ou os detalhes do problema do Gerenciamento de API |
> | Ação | Microsoft.ApiManagement/service/apis/issues/write | Adiciona o problema de API ou atualiza o problema de API |
> | Ação | Microsoft.ApiManagement/service/apis/operations/delete | Remover a operação da API existente |
> | Ação | Microsoft.ApiManagement/service/apis/operations/policies/delete | Remover a configuração de política das políticas de Operação de API |
> | Ação | Microsoft.ApiManagement/service/apis/operations/policies/read | Obter políticas para Operação de API ou obter detalhes de configuração de política para Operação de API |
> | Ação | Microsoft.ApiManagement/service/apis/operations/policies/write | Definir detalhes de configuração de política para Operação de API |
> | Ação | Microsoft.ApiManagement/service/apis/operations/policy/delete | Remover configuração de política da operação |
> | Ação | Microsoft.ApiManagement/service/apis/operations/policy/read | Obter detalhes de configuração de política para a operação |
> | Ação | Microsoft.ApiManagement/service/apis/operations/policy/write | Definir detalhes de configuração de política para a operação |
> | Ação | Microsoft.ApiManagement/service/apis/operations/read | Obter lista de operações de API existentes ou obter os detalhes de operação da API |
> | Ação | Microsoft.ApiManagement/service/apis/operations/tags/delete | Excluir associação de Marca existente com a Operação existente |
> | Ação | Microsoft.ApiManagement/service/apis/operations/tags/read | Obter marcas associadas à operação ou Obter detalhes da Marca |
> | Ação | Microsoft.ApiManagement/service/apis/operations/tags/write | Associar Marca existente a uma Operação existente |
> | Ação | Microsoft.ApiManagement/service/apis/operations/write | Criar nova operação de API ou atualizar operação de API existente |
> | Ação | Microsoft.ApiManagement/service/apis/operationsByTags/read | Obter lista de associações de Marca/Operação |
> | Ação | Microsoft.ApiManagement/service/apis/policies/delete | Remover a configuração de política das políticas da API |
> | Ação | Microsoft.ApiManagement/service/apis/policies/read | Obter políticas para API ou Obter detalhes de configuração de política para API |
> | Ação | Microsoft.ApiManagement/service/apis/policies/write | Definir detalhes de configuração de política para API |
> | Ação | Microsoft.ApiManagement/service/apis/policy/delete | Remover configuração de política da API |
> | Ação | Microsoft.ApiManagement/service/apis/policy/read | Obter detalhes de configuração de política para API |
> | Ação | Microsoft.ApiManagement/service/apis/policy/write | Definir detalhes de configuração de política para API |
> | Ação | Microsoft.ApiManagement/service/apis/products/read | Obter todos os produtos que a API faz parte |
> | Ação | Microsoft.ApiManagement/service/apis/read | Obter lista de todas as APIs registradas ou obter detalhes da API |
> | Ação | Microsoft.ApiManagement/service/apis/releases/delete | Remover todas as versões da API ou versão Remover API |
> | Ação | Microsoft.ApiManagement/service/apis/releases/read | Obter versões para uma API ou Obter detalhes da versão da API |
> | Ação | Microsoft.ApiManagement/service/apis/releases/write | Criar nova versão da API ou atualizar a versão da API existente |
> | Ação | Microsoft.ApiManagement/service/apis/revisions/delete | Remover todas as revisões de uma API |
> | Ação | Microsoft.ApiManagement/service/apis/revisions/read | Obter revisões pertencentes a uma API |
> | Ação | Microsoft.ApiManagement/service/apis/schemas/delete | Remover o Esquema existente |
> | Ação | Microsoft.ApiManagement/service/apis/schemas/document/read | Obter o documento que descreve o Esquema |
> | Ação | Microsoft.ApiManagement/service/apis/schemas/document/write | Atualizar o documento que descreve o Esquema |
> | Ação | Microsoft.ApiManagement/service/apis/schemas/read | Obter todos os esquemas para uma determinada API ou Obter os esquemas usados pela API |
> | Ação | Microsoft.ApiManagement/service/apis/schemas/write | Definir os Esquemas usados pela API |
> | Ação | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | Remover descrição de Marca da API |
> | Ação | Microsoft.ApiManagement/service/apis/tagDescriptions/read | Obter descrições de Marcas no escopo da API ou obter uma descrição de Marcas no escopo da API |
> | Ação | Microsoft.ApiManagement/service/apis/tagDescriptions/write | Criar/alterar a descrição da Marca no escopo da API |
> | Ação | Microsoft.ApiManagement/service/apis/tags/delete | Remover associação existente de API/Marca |
> | Ação | Microsoft.ApiManagement/service/apis/tags/read | Obter todas as associações de Marca/API para a API ou Obter detalhes da associação de Marca/API |
> | Ação | Microsoft.ApiManagement/service/apis/tags/write | Adicionar nova associação de Marca/API |
> | Ação | Microsoft.ApiManagement/service/apis/write | Criar nova API ou atualizar os detalhes da API existente |
> | Ação | Microsoft.ApiManagement/service/apisByTags/read | Obter lista de associações de Marca/API |
> | Ação | Microsoft.ApiManagement/service/api-version-sets/delete | Remover VersionSet existente |
> | Ação | Microsoft.ApiManagement/service/api-version-sets/read | Obter lista de entidades do grupo de versão ou Obter detalhes de um VersionSet |
> | Ação | Microsoft.ApiManagement/service/api-version-sets/versions/read | Obter lista de entidades de versão |
> | Ação | Microsoft.ApiManagement/service/api-version-sets/write | Criar novo VersionSet ou Atualizar detalhes do VersionSet existente |
> | Ação | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | Atualiza os recursos Microsoft.ApiManagement em execução na rede virtual para obter configurações de rede atualizadas. |
> | Ação | Microsoft.ApiManagement/service/authorizationServers/delete | Remover servidor de autorização existente |
> | Ação | Microsoft.ApiManagement/service/authorizationServers/read | Obter lista de servidores de autorização ou obter detalhes do servidor de autorização |
> | Ação | Microsoft.ApiManagement/service/authorizationServers/write | Criar um novo servidor de autorização ou atualizar detalhes de um servidor de autorização existente |
> | Ação | Microsoft.ApiManagement/service/backends/delete | Remover o back-end existente |
> | Ação | Microsoft.ApiManagement/service/backends/read | Obter lista de back-ends ou obter os detalhes de back-end |
> | Ação | Microsoft.ApiManagement/service/backends/reconnect/action | Criar uma Solicitação de Reconexão |
> | Ação | Microsoft.ApiManagement/service/backends/write | Adicionar um novo back-end ou atualizar os detalhes do back-end existente |
> | Ação | Microsoft.ApiManagement/service/backup/action | Fazer backup do Serviço de Gerenciamento de API para o contêiner especificado em uma conta de armazenamento fornecida pelo usuário |
> | Ação | Microsoft.ApiManagement/service/certificates/delete | Remover certificado existente |
> | Ação | Microsoft.ApiManagement/service/certificates/read | Obter lista de certificados ou obter os detalhes do certificado |
> | Ação | Microsoft.ApiManagement/service/certificates/write | Adicionar novo certificado |
> | Ação | Microsoft.ApiManagement/service/delete | Excluir uma instância do Serviço de Gerenciamento de API |
> | Ação | Microsoft.ApiManagement/service/diagnostics/delete | Remover o diagnóstico existente |
> | Ação | Microsoft.ApiManagement/service/diagnostics/read | Obter lista de diagnósticos ou Obter detalhes do diagnóstico |
> | Ação | Microsoft.ApiManagement/service/diagnostics/write | Adicionar novo diagnóstico ou Atualizar detalhes de diagnóstico existente |
> | Ação | Microsoft.ApiManagement/service/getssotoken/action | Obter token de SSO que pode ser usado para fazer logon no portal de herdado do Serviço de Gerenciamento de API como administrador |
> | Ação | Microsoft.ApiManagement/service/groups/delete | Remover grupo existente |
> | Ação | Microsoft.ApiManagement/service/groups/read | Obter lista de grupos ou obter detalhes de um grupo |
> | Ação | Microsoft.ApiManagement/service/groups/users/delete | Remover usuário existente do grupo existente |
> | Ação | Microsoft.ApiManagement/service/groups/users/read | Obter lista de usuários do grupo |
> | Ação | Microsoft.ApiManagement/service/groups/users/write | Adicionar usuário existente a grupo existente |
> | Ação | Microsoft.ApiManagement/service/groups/write | Criar novo grupo ou atualizar detalhes do grupo existente |
> | Ação | Microsoft.ApiManagement/service/identityProviders/delete | Remover o provedor de identidade existente |
> | Ação | Microsoft.ApiManagement/service/identityProviders/read | Obter lista de provedores de identidade ou obter detalhes do provedor de identidade |
> | Ação | Microsoft.ApiManagement/service/identityProviders/write | Criar um novo provedor de identidade atualizar detalhes de um provedor de identidade existente |
> | Ação | Microsoft.ApiManagement/service/locations/networkstatus/read | Obter o status de acesso à rede dos recursos em que o serviço depende da localização. |
> | Ação | Microsoft.ApiManagement/service/loggers/delete | Remover agente existente |
> | Ação | Microsoft.ApiManagement/service/loggers/read | Obter lista de agentes ou obter detalhes do agente |
> | Ação | Microsoft.ApiManagement/service/loggers/write | Adicionar novo agente ou atualizar detalhes do agente existente |
> | Ação | Microsoft.ApiManagement/service/managedeployments/action | Alterar SKU/unidades, adicionar/remover implantações regionais do Serviço de Gerenciamento de API |
> | Ação | Microsoft.ApiManagement/service/networkstatus/read | Obter o status de acesso à rede dos recursos dos quais o serviço depende. |
> | Ação | Microsoft.ApiManagement/service/notifications/action | Envia uma notificação para um usuário especificado |
> | Ação | Microsoft.ApiManagement/service/notifications/read | Obter todas as notificações do publicador de Gerenciamento de API ou Obter detalhes da notificação do publicador de Gerenciamento de API |
> | Ação | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | Remover emails existentes associados a uma Notificação |
> | Ação | Microsoft.ApiManagement/service/notifications/recipientEmails/read | Receber destinatários de email associados à notificação do publicador de Gerenciamento de API |
> | Ação | Microsoft.ApiManagement/service/notifications/recipientEmails/write | Criar novo destinatário de email da notificação |
> | Ação | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | Removes usuário associado aos destinatários da notificação |
> | Ação | Microsoft.ApiManagement/service/notifications/recipientUsers/read | Obter usuários de destinatários associados à notificação |
> | Ação | Microsoft.ApiManagement/service/notifications/recipientUsers/write | Adicionar usuário aos destinatários da notificação |
> | Ação | Microsoft.ApiManagement/service/notifications/write | Criar ou atualizar a notificação do publicador de Gerenciamento de API |
> | Ação | Microsoft.ApiManagement/service/openidConnectProviders/delete | Remover o provedor OpenID Connect existente |
> | Ação | Microsoft.ApiManagement/service/openidConnectProviders/read | Obter lista de provedores OpenID Connect ou obter detalhes do provedor OpenID Connect |
> | Ação | Microsoft.ApiManagement/service/openidConnectProviders/write | Criar um novo provedor OpenID Connect ou atualizar detalhes de um provedor OpenID Connect existente |
> | Ação | Microsoft.ApiManagement/service/operationresults/read | Obter o status atual da operação de longa duração |
> | Ação | Microsoft.ApiManagement/service/policies/delete | Remover a configuração de política das políticas do Locatário |
> | Ação | Microsoft.ApiManagement/service/policies/read | Obter políticas para locatário ou Obter detalhes de configuração de política para Locatário |
> | Ação | Microsoft.ApiManagement/service/policies/write | Definir detalhes de configuração de política para Locatário |
> | Ação | Microsoft.ApiManagement/service/policySnippets/read | Obter todos os snippets de política |
> | Ação | Microsoft.ApiManagement/service/portalsettings/read | Obter as configurações de inscrição para o Portal, Obter as configurações de entrada para o Portal ou Obter as configurações de delegação para o Portal |
> | Ação | Microsoft.ApiManagement/service/portalsettings/write | Atualizar configurações de inscrição, Atualizar configurações de entrada ou Atualizar configurações de delegação |
> | Ação | Microsoft.ApiManagement/service/products/apis/delete | Remover API existente do produto existente |
> | Ação | Microsoft.ApiManagement/service/products/apis/read | Obter lista de APIs adicionadas ao produto existente |
> | Ação | Microsoft.ApiManagement/service/products/apis/write | Adicionar API existente a um produto existente |
> | Ação | Microsoft.ApiManagement/service/products/delete | Remover produto existente |
> | Ação | Microsoft.ApiManagement/service/products/groups/delete | Excluir a associação de grupo do desenvolvedor existente ao produto existente |
> | Ação | Microsoft.ApiManagement/service/products/groups/read | Obter lista de grupos de desenvolvedores associados ao produto |
> | Ação | Microsoft.ApiManagement/service/products/groups/write | Associar o grupo do desenvolvedor existente ao produto existente |
> | Ação | Microsoft.ApiManagement/service/products/policies/delete | Remover a configuração de política das políticas de Produtos |
> | Ação | Microsoft.ApiManagement/service/products/policies/read | Obter políticas para Produtos ou Obter detalhes de configuração de políticas para o Produto |
> | Ação | Microsoft.ApiManagement/service/products/policies/write | Definir detalhes de configuração de política para o Produto |
> | Ação | Microsoft.ApiManagement/service/products/policy/delete | Remover configuração de política de produto existente |
> | Ação | Microsoft.ApiManagement/service/products/policy/read | Obter a configuração de política do produto existente |
> | Ação | Microsoft.ApiManagement/service/products/policy/write | Definir a configuração de política para o produto existente |
> | Ação | Microsoft.ApiManagement/service/products/read | Obter lista de produtos ou obter detalhes do produto |
> | Ação | Microsoft.ApiManagement/service/products/subscriptions/read | Obter lista de assinaturas de produto |
> | Ação | Microsoft.ApiManagement/service/products/tags/delete | Excluir associação de marca existente com o produto existente |
> | Ação | Microsoft.ApiManagement/service/products/tags/read | Obter marcas associadas ao Produto ou Obter detalhes da Marca |
> | Ação | Microsoft.ApiManagement/service/products/tags/write | Associar Marca existente a um Produto existente |
> | Ação | Microsoft.ApiManagement/service/products/write | Criar novo produto ou atualizar detalhes do produto existente |
> | Ação | Microsoft.ApiManagement/service/productsByTags/read | Obtém lista de associações de Marca/Produto |
> | Ação | Microsoft.ApiManagement/service/properties/delete | Remover uma propriedade existente |
> | Ação | Microsoft.ApiManagement/service/properties/read | Obter a lista de todas as propriedades ou obtém detalhes de uma propriedade especificada |
> | Ação | Microsoft.ApiManagement/service/properties/write | Criar uma nova propriedade ou atualizar o valor da propriedade especificada |
> | Ação | Microsoft.ApiManagement/service/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o serviço de Gerenciamento de API |
> | Ação | Microsoft.ApiManagement/service/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o serviço de Gerenciamento de API |
> | Ação | Microsoft.ApiManagement/service/providers/Microsoft.Insights/logDefinitions/read | Obter os logs disponíveis para o serviço de Gerenciamento de API |
> | Ação | Microsoft.ApiManagement/service/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para o serviço de Gerenciamento de API |
> | Ação | Microsoft.ApiManagement/service/quotas/periods/read | Obter o valor do contador de cota para o período |
> | Ação | Microsoft.ApiManagement/service/quotas/periods/write | Definir o valor atual do contador de cota |
> | Ação | Microsoft.ApiManagement/service/quotas/read | Obter valores para cota |
> | Ação | Microsoft.ApiManagement/service/quotas/write | Definir o valor atual do contador de cota |
> | Ação | Microsoft.ApiManagement/service/read | Ler metadados de uma instância do Serviço de Gerenciamento de API |
> | Ação | Microsoft.ApiManagement/service/reports/read | Obter relatório agregado por períodos de tempo, Obter relatório agregado por região geográfica ou Obter relatório agregado pelos desenvolvedores.<br>ou Obter relatório agregado por produtos.<br>ou Obter relatório agregado por APIs, Obter relatório agregado por operações ou Obter relatório agregado por assinatura.<br>ou Obter dados de relatórios de solicitações |
> | Ação | Microsoft.ApiManagement/service/restore/action | Restaurar o Serviço de Gerenciamento de API do contêiner especificado em uma conta de armazenamento fornecida pelo usuário |
> | Ação | Microsoft.ApiManagement/service/subscriptions/delete | Excluir assinatura. Essa operação pode ser usada para excluir a assinatura |
> | Ação | Microsoft.ApiManagement/service/subscriptions/read | Obter uma lista de assinaturas de produtos ou obter os detalhes de assinatura do produto |
> | Ação | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | Regenerar a chave primária da assinatura |
> | Ação | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | Regenerar a chave secundária da assinatura |
> | Ação | Microsoft.ApiManagement/service/subscriptions/write | Inscrever um usuário existente para um produto existente ou atualizar os detalhes de uma assinatura existente. Essa operação pode ser usada para renovar a assinatura |
> | Ação | Microsoft.ApiManagement/service/tagResources/read | Obter lista de Marcas com Recursos associados |
> | Ação | Microsoft.ApiManagement/service/tags/delete | Remover Marca existente |
> | Ação | Microsoft.ApiManagement/service/tags/read | Obter lista de Marcas ou Obter detalhes da Marca |
> | Ação | Microsoft.ApiManagement/service/tags/write | Adicionar nova Marca ou Atualizar detalhes da Marca existente |
> | Ação | Microsoft.ApiManagement/service/templates/delete | Redefinir o modelo padrão de email de Gerenciamento de API |
> | Ação | Microsoft.ApiManagement/service/templates/read | Obter todos os modelos de email ou Obter detalhes do modelo de email do Gerenciamento de API |
> | Ação | Microsoft.ApiManagement/service/templates/write | Criar ou atualizar o modelo de email de Gerenciamento de API ou o modelo de email de Gerenciamento de APIs de atualização |
> | Ação | Microsoft.ApiManagement/service/tenant/delete | Remover configuração de política do locatário |
> | Ação | Microsoft.ApiManagement/service/tenant/deploy/action | Executar uma tarefa de implantação para aplicar as alterações da ramificação git especificada para a configuração no banco de dados. |
> | Ação | Microsoft.ApiManagement/service/tenant/operationResults/read | Obter lista de resultados da operação ou obter o resultado de uma operação específica |
> | Ação | Microsoft.ApiManagement/service/tenant/read | Obter a configuração de política para o locatário ou Obter detalhes de informações de acesso do locatário |
> | Ação | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | Regenerar a chave de acesso primária |
> | Ação | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | Regenerar a chave de acesso secundária |
> | Ação | Microsoft.ApiManagement/service/tenant/save/action | Criar confirmação com instantâneo de configuração para uma ramificação específica no repositório |
> | Ação | Microsoft.ApiManagement/service/tenant/syncState/read | Obter status da última sincronização git |
> | Ação | Microsoft.ApiManagement/service/tenant/validate/action | Valida as alterações da ramificação git especificada |
> | Ação | Microsoft.ApiManagement/service/tenant/write | Definir a configuração de política para o locatário ou Atualizar detalhes de informações de acesso do locatário |
> | Ação | Microsoft.ApiManagement/service/updatecertificate/action | Carregar certificado SSL para um Serviço de Gerenciamento de API |
> | Ação | Microsoft.ApiManagement/service/updatehostname/action | Configurar, atualizar ou remover nomes de domínio personalizado para um Serviço de Gerenciamento de API |
> | Ação | Microsoft.ApiManagement/service/users/action | Registrar um novo usuário |
> | Ação | Microsoft.ApiManagement/service/users/applications/attachments/delete | Remover um anexo |
> | Ação | Microsoft.ApiManagement/service/users/applications/attachments/read | Obter anexos de aplicativo ou Obter anexo |
> | Ação | Microsoft.ApiManagement/service/users/applications/attachments/write | Adicionar Anexo ao aplicativo |
> | Ação | Microsoft.ApiManagement/service/users/applications/delete | Remover aplicativo existente |
> | Ação | Microsoft.ApiManagement/service/users/applications/read | Obter lista de todos os aplicativos do usuário ou Obter detalhes do aplicativo de Gerenciamento de API |
> | Ação | Microsoft.ApiManagement/service/users/applications/write | Registrar um aplicativo para Gerenciamento de API ou Atualizar detalhes do aplicativo |
> | Ação | Microsoft.ApiManagement/service/users/delete | Remover conta de usuário |
> | Ação | Microsoft.ApiManagement/service/users/generateSsoUrl/action | Gerar a URL do SSO. A URL pode ser usada para acessar o portal de administração |
> | Ação | Microsoft.ApiManagement/service/users/groups/read | Obter lista de grupos do usuário |
> | Ação | Microsoft.ApiManagement/service/users/keys/read | Obter lista de chaves do usuário |
> | Ação | Microsoft.ApiManagement/service/users/read | Obter uma lista de usuários registrados ou obter detalhes da conta de um usuário |
> | Ação | Microsoft.ApiManagement/service/users/subscriptions/read | Obter lista de assinaturas do usuário |
> | Ação | Microsoft.ApiManagement/service/users/token/action | Obter token de acesso do token para um usuário |
> | Ação | Microsoft.ApiManagement/service/users/write | Registrar um novo usuário ou atualizar detalhes da conta de um usuário existente |
> | Ação | Microsoft.ApiManagement/service/write | Criar uma nova instância do Serviço de Gerenciamento de API |
> | Ação | Microsoft.ApiManagement/unregister/action | Cancelar o registro da assinatura para o provedor de recursos Microsoft.ApiManagement |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Authorization/checkAccess/action | Verificar se o chamador está autorizado a executar uma ação específica |
> | Ação | Microsoft.Authorization/classicAdministrators/delete | Remover o administrador da assinatura. |
> | Ação | Microsoft.Authorization/classicAdministrators/operationstatuses/read | Obter o status de operação do administrador da assinatura. |
> | Ação | Microsoft.Authorization/classicAdministrators/read | Ler os administradores para obter a assinatura. |
> | Ação | Microsoft.Authorization/classicAdministrators/write | Adicionar ou modificar o administrador de uma assinatura. |
> | Ação | Microsoft.Authorization/denyAssignments/delete | Excluir uma atribuição de negação no escopo especificado. |
> | Ação | Microsoft.Authorization/denyAssignments/read | Obter informações sobre uma atribuição de negação. |
> | Ação | Microsoft.Authorization/denyAssignments/write | Criar uma atribuição de negação no escopo especificado. |
> | Ação | Microsoft.Authorization/elevateAccess/action | Concede ao chamador acesso de administrador de acesso do usuário no escopo do locatário |
> | Ação | Microsoft.Authorization/locks/delete | Excluir bloqueios no escopo especificado. |
> | Ação | Microsoft.Authorization/locks/read | Obter bloqueios no escopo especificado. |
> | Ação | Microsoft.Authorization/locks/write | Adicionar bloqueios no escopo especificado. |
> | Ação | Microsoft.Authorization/operations/read | Obtém a lista de operações |
> | Ação | Microsoft.Authorization/permissions/read | Listar todas as permissões que o chamador tem em determinado escopo. |
> | Ação | Microsoft.Authorization/policyAssignments/delete | Excluir uma atribuição de política no escopo especificado. |
> | Ação | Microsoft.Authorization/policyAssignments/read | Obter informações sobre uma atribuição de política. |
> | Ação | Microsoft.Authorization/policyAssignments/write | Criar uma atribuição de política no escopo especificado. |
> | Ação | Microsoft.Authorization/policyDefinitions/delete | Excluir uma definição de política. |
> | Ação | Microsoft.Authorization/policyDefinitions/read | Obter informações sobre uma definição de política. |
> | Ação | Microsoft.Authorization/policyDefinitions/write | Criar uma definição de política personalizada. |
> | Ação | Microsoft.Authorization/policySetDefinitions/delete | Excluir uma definição de conjunto de políticas. |
> | Ação | Microsoft.Authorization/policySetDefinitions/read | Obter informações sobre uma definição de conjunto de políticas. |
> | Ação | Microsoft.Authorization/policySetDefinitions/write | Criar uma definição de conjunto de políticas personalizada. |
> | Ação | Microsoft.Authorization/providerOperations/read | Obter operações para todos os provedores de recursos que podem ser usados em definições de função. |
> | Ação | Microsoft.Authorization/roleAssignments/delete | Exclua uma atribuição de função no escopo especificado. |
> | Ação | Microsoft.Authorization/roleAssignments/read | Obter informações sobre uma atribuição de função. |
> | Ação | Microsoft.Authorization/roleAssignments/write | Criar uma atribuição de função no escopo especificado. |
> | Ação | Microsoft.Authorization/roleDefinitions/delete | Excluir a definição de função personalizada especificada. |
> | Ação | Microsoft.Authorization/roleDefinitions/read | Obter informações sobre uma definição de função. |
> | Ação | Microsoft.Authorization/roleDefinitions/write | Criar ou atualizar uma definição de função personalizada com permissões especificadas e escopos atribuíveis. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | Ler uma informação de registro do DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Gravar uma solicitação para regenerar chaves DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/certificates/delete | Excluir um ativo de certificado da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/certificates/getCount/action | Lê a contagem de certificados |
> | Ação | Microsoft.Automation/automationAccounts/certificates/read | Obter um ativo de certificado da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/certificates/write | Criar ou atualizar um ativo de certificado da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/compilationjobs/read | Ler uma compilação do DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/compilationjobs/read | Ler uma compilação do DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/compilationjobs/write | Gravar uma compilação do DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/compilationjobs/write | Gravar uma compilação do DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/configurations/content/read | Lê o conteúdo de mídia de configuração |
> | Ação | Microsoft.Automation/automationAccounts/configurations/delete | Excluir um conteúdo de DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/configurations/getCount/action | Ler a contagem de um conteúdo do DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/configurations/read | Obter o conteúdo de DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/configurations/write | Gravar um conteúdo de DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/connections/delete | Excluir um ativo de conexão da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/connections/getCount/action | Lê a contagem de conexões |
> | Ação | Microsoft.Automation/automationAccounts/connections/read | Obter um ativo de conexão da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/connections/write | Criar ou atualizar um ativo de conexão da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/connectionTypes/delete | Excluir um ativo de tipo de conexão da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/connectionTypes/read | Obter um ativo de tipo de conexão da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/connectionTypes/write | Criar um ativo de tipo de conexão da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/credentials/delete | Excluir um ativo de credencial da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/credentials/getCount/action | Lê a contagem de credenciais |
> | Ação | Microsoft.Automation/automationAccounts/credentials/read | Obter um ativo de credencial da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/credentials/write | Criar ou atualizar um ativo de credencial da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/delete | Excluir uma conta da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Automation/automationAccounts/diagnosticSettings/write | Definir a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Excluir recursos do Hybrid Runbook Worker |
> | Ação | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Ler recursos do Hybrid Runbook Worker |
> | Ação | Microsoft.Automation/automationAccounts/jobs/output/read | Obter a saída de um trabalho |
> | Ação | Microsoft.Automation/automationAccounts/jobs/read | Obter um trabalho da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobs/resume/action | Reinicia um trabalho da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | Obter o conteúdo do runbook da Automação do Azure no momento da execução do trabalho |
> | Ação | Microsoft.Automation/automationAccounts/jobs/stop/action | Interrompe um trabalho da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobs/streams/read | Obter um fluxo de trabalho da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobs/streams/read | Obter um fluxo de trabalho da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspende um trabalho da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobs/write | Criar um trabalho da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobSchedules/delete | Excluir uma agenda de trabalho da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobSchedules/read | Obter uma agenda de trabalho da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobSchedules/write | Criar uma agenda de trabalho da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Obtém o workspace vinculado à conta de automação |
> | Ação | Microsoft.Automation/automationAccounts/listKeys/action | Lê as chaves para a conta de automação |
> | Ação | Microsoft.Automation/automationAccounts/logDefinitions/read | Obter os logs disponíveis para a conta de automação |
> | Ação | Microsoft.Automation/automationAccounts/modules/activities/read | Obter atividades de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/modules/delete | Excluir um módulo Powershell da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/modules/getCount/action | Obtém a contagem de módulos Powershell dentro da conta de automação |
> | Ação | Microsoft.Automation/automationAccounts/modules/read | Obtém um módulo Powershell da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/modules/write | Cria ou atualiza um módulo Powershell da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | Excluir uma configuração de nó do DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | Ler um conteúdo de configuração de nó do DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodeConfigurations/read | Ler uma configuração de nó do DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodeConfigurations/write | Gravar uma configuração de nó do DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodecounts/read | Lê o resumo da contagem de nós para o tipo especificado |
> | Ação | Microsoft.Automation/automationAccounts/nodes/delete | Excluir nós DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodes/read | Ler os nós DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodes/reports/content/read | Lê os conteúdos do relatório do DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodes/reports/read | Ler relatórios do DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodes/write | Cria ou atualiza nós do DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | Obter TypeFields de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/providers/Microsoft.Insights/metricDefinitions/read | Obter definições de Métrica de Automação |
> | Ação | Microsoft.Automation/automationAccounts/python2Packages/delete | Exclui um pacote Python 2 da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/python2Packages/read | Obtém um pacote Python 2 da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/python2Packages/write | Cria ou atualiza um pacote Python 2 da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/python3Packages/delete | Exclui um pacote Python 3 da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/python3Packages/read | Obtém um pacote Python 3 da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/python3Packages/write | Cria ou atualiza um pacote Python 3 da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/read | Obter uma conta da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/content/read | Obter o conteúdo de um runbook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/delete | Excluir um runbook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | Criar o conteúdo de um rascunho de runbook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | Obtém resultados de operação de rascunho do runbook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/read | Obter um rascunho de runbook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | Obter um trabalho de teste de rascunho do runbook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | Retoma um trabalho de teste de rascunho de runbook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | Interrompe um trabalho de teste de rascunho de runbook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | Suspende um trabalho de teste de rascunho de runbook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Criar um trabalho de teste de rascunho de runbook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | Desfazer edições em um rascunho de runbook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/getCount/action | Obtém a contagem de runbooks da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/publish/action | Publica um rascunho de runbook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/read | Obter um runbook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/write | Criar ou atualizar um runbook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/schedules/delete | Excluir um ativo de agendamento da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/schedules/getCount/action | Obtém a contagem de agendamentos da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/schedules/read | Obter um ativo de agendamento da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/schedules/write | Criar ou atualizar um ativo de agendamento da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Excluir uma configuração de atualização do Software de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Obter uma configuração de atualização do Software de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Criar ou atualizar uma configuração de atualização do Software de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/statistics/read | Obter Estatísticas de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Obtém um computador de implantação de atualização da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | Obtém um trabalho de patch de gerenciamento de atualização da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/usages/read | Obter o uso de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/variables/delete | Excluir um ativo de variável da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/variables/read | Ler um ativo de variável da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/variables/write | Criar ou atualizar um ativo de variável da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/delete | Exclui um trabalho do Observador de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/read | Obtém um trabalho do Observador de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/start/action | Inicia um trabalho do Observador de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/stop/action | Para um trabalho do Observador de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/streams/read | Obter um fluxo de trabalho do observador de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | Exclui ações de trabalho do Observador de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | Obtém ações de trabalho do Observador de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | Cria ações de trabalho do Observador de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/write | Cria um trabalho do Observador de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/webhooks/action | Gera um URI para um webhook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/webhooks/delete | Excluir um webhook da Automação do Azure  |
> | Ação | Microsoft.Automation/automationAccounts/webhooks/read | Ler um webhook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/webhooks/write | Criar ou atualizar um webhook da Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/write | Criar ou atualizar uma conta da Automação do Azure |
> | Ação | Microsoft.Automation/operations/read | Obtém as operações disponíveis para os recursos da Automação do Azure |
> | Ação | Microsoft.Automation/register/action | Registra a assinatura à Automação do Azure |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.AzureActiveDirectory/b2cDirectories/delete | Excluir recurso do Active Directory B2C |
> | Ação | Microsoft.AzureActiveDirectory/b2cDirectories/read | Exibir recurso do Active Directory B2C |
> | Ação | Microsoft.AzureActiveDirectory/b2cDirectories/write | Criar ou atualizar recursos do Active Directory B2C |
> | Ação | Microsoft.AzureActiveDirectory/operations/read | Ler todas as operações de API disponíveis para o provedor de recursos Microsoft.AzureActiveDirectory |
> | Ação | Microsoft.AzureActiveDirectory/register/action | Registrar a assinatura do provedor de recursos Microsoft.AzureActiveDirectory |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.AzureStack/Operations/read | Obter as propriedades de uma operação do provedor de recursos |
> | Ação | Microsoft.AzureStack/register/action | Registrar assinatura com o provedor de recursos Microsoft.AzureStack |
> | Ação | Microsoft.AzureStack/registrations/customerSubscriptions/delete | Excluir uma assinatura do cliente do Microsoft Azure Stack |
> | Ação | Microsoft.AzureStack/registrations/customerSubscriptions/read | Obter as propriedades uma assinatura do cliente do Microsoft Azure Stack |
> | Ação | Microsoft.AzureStack/registrations/customerSubscriptions/write | Criar ou atualizar uma assinatura do cliente do Microsoft Azure Stack |
> | Ação | Microsoft.AzureStack/registrations/delete | Excluir um registro do Microsoft Azure Stack |
> | Ação | Microsoft.AzureStack/registrations/getActivationKey/action | Obter a última chave de ativação do Microsoft Azure Stack |
> | Ação | Microsoft.AzureStack/registrations/products/listDetails/action | Recuperar detalhes estendidos de um produto do Marketplace do Azure Stack |
> | Ação | Microsoft.AzureStack/registrations/products/read | Obter as propriedades de um produto do Marketplace do Azure Stack |
> | Ação | Microsoft.AzureStack/registrations/read | Obter as propriedades de um registro do Microsoft Azure Stack |
> | Ação | Microsoft.AzureStack/registrations/write | Criar ou atualizar um registro do Microsoft Azure Stack |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Batch/batchAccounts/applications/delete | Excluir um aplicativo |
> | Ação | Microsoft.Batch/batchAccounts/applications/read | Listar aplicativos ou obtém as propriedades de um aplicativo |
> | Ação | Microsoft.Batch/batchAccounts/applications/versions/activate/action | Ativa um pacote de aplicativos |
> | Ação | Microsoft.Batch/batchAccounts/applications/versions/delete | Excluir um pacote de aplicativos |
> | Ação | Microsoft.Batch/batchAccounts/applications/versions/read | Obter as propriedades de um pacote de aplicativos |
> | Ação | Microsoft.Batch/batchAccounts/applications/versions/write | Criar um novo pacote de aplicativos ou atualizar um pacote de aplicativos existente |
> | Ação | Microsoft.Batch/batchAccounts/applications/write | Criar um novo aplicativo ou atualizar um aplicativo existente |
> | Ação | Microsoft.Batch/batchAccounts/certificateOperationResults/read | Obter os resultados de uma operação de certificado de execução longa em uma conta do Lote |
> | Ação | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | Cancelar a exclusão de falha de um certificado em uma conta do Lote |
> | Ação | Microsoft.Batch/batchAccounts/certificates/delete | Excluir um certificado de uma conta do Batch |
> | Ação | Microsoft.Batch/batchAccounts/certificates/read | Listar certificados em uma conta do Lote ou obter as propriedades de um certificado |
> | Ação | Microsoft.Batch/batchAccounts/certificates/write | Criar um novo certificado em uma conta do Lote ou atualizar um certificado existente |
> | Ação | Microsoft.Batch/batchAccounts/delete | Excluir uma conta do Lote |
> | Ação | Microsoft.Batch/batchAccounts/listkeys/action | Listar chaves de acesso para uma conta do Lote |
> | Ação | Microsoft.Batch/batchAccounts/operationResults/read | Obter os resultados de uma operação de execução longa da conta do Lote |
> | Ação | Microsoft.Batch/batchAccounts/poolOperationResults/read | Obter os resultados de uma operação de pool de execução longa em uma conta do Lote |
> | Ação | Microsoft.Batch/batchAccounts/pools/delete | Excluir um pool de uma conta do Batch |
> | Ação | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | Desabilitar o escalonamento automático de um pool de conta do Lote |
> | Ação | Microsoft.Batch/batchAccounts/pools/read | Listar pools em uma conta do Batch ou obter as propriedades de um pool |
> | Ação | Microsoft.Batch/batchAccounts/pools/stopResize/action | Interromper uma operação de redimensionamento em andamento em um pool de conta do Lote |
> | Ação | Microsoft.Batch/batchAccounts/pools/upgradeOs/action | Atualizar o sistema operacional de um pool de conta do Lote |
> | Ação | Microsoft.Batch/batchAccounts/pools/write | Criar um novo pool em uma conta do Lote ou atualizar um pool existente |
> | Ação | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/logDefinitions/read | Obter os logs disponíveis para o serviço em Lotes |
> | Ação | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para o serviço em Lotes |
> | Ação | Microsoft.Batch/batchAccounts/read | Listar contas do Lote ou obtém as propriedades de uma conta do Lote |
> | Ação | Microsoft.Batch/batchAccounts/regeneratekeys/action | Regenera chaves de acesso para uma conta do Lote |
> | Ação | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | Sincroniza as chaves de acesso da conta de armazenamento automática configurada para uma conta do Lote |
> | Ação | Microsoft.Batch/batchAccounts/write | Criar uma nova conta do Lote ou atualizar uma conta do Lote existente |
> | Ação | Microsoft.Batch/locations/checkNameAvailability/action | Verificar se o nome da conta é válido e não está em uso. |
> | Ação | Microsoft.Batch/locations/quotas/read | Obter cotas do Lote da assinatura especificada na região do Azure especificada |
> | Ação | Microsoft.Batch/operations/read | Lista as operações disponíveis no provedor de recursos Microsoft.Batch |
> | Ação | Microsoft.Batch/register/action | Registra a assinatura do provedor de recursos de lote e permite a criação de contas do Lote |
> | Ação | Microsoft.Batch/unregister/action | Cancelar o registro da assinatura do Provedor de Recursos do Lote impedindo a criação de contas do Lote |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.BatchAI/clusters/read | Listar clusters do IA do Lote do Azure ou obter as propriedades de um cluster do IA do Lote do Azure |
> | Ação | Microsoft.BatchAI/fileservers/read | Listar servidores de arquivos do IA do Lote do Azure ou obter as propriedades de um servidor de arquivos do IA do Lote do Azure |
> | Ação | Microsoft.BatchAI/locations/operationresults/read | Obtém o resultado da operação assíncrona da IA do Lote na região do Azure especificada |
> | Ação | Microsoft.BatchAI/locations/operationstatuses/read | Obtém o status da operação assíncrona da IA do Lote na região do Azure especificada |
> | Ação | Microsoft.BatchAI/locations/usages/read | Obtém usos de IA do Lote da assinatura especificada na região do Azure especificada |
> | Ação | Microsoft.BatchAI/register/action | Registrar a assinatura do Provedor de Recursos do IA do Lote do Azure e habilitar a criação de recursos do IA do Lote do Azure |
> | Ação | Microsoft.BatchAI/unregister/action | Cancela o registro da assinatura do Provedor de Recursos do IA do Lote e habilita a criação de recursos do IA do Lote |
> | Ação | Microsoft.BatchAI/workspaces/clusters/delete | Excluir um cluster do IA do Lote do Azure |
> | Ação | Microsoft.BatchAI/workspaces/clusters/read | Listar clusters do IA do Lote do Azure ou obter as propriedades de um cluster do IA do Lote do Azure |
> | Ação | Microsoft.BatchAI/workspaces/clusters/remoteLoginInformation/action | Listar informações de logon remoto para um cluster do IA do Lote do Azure |
> | Ação | Microsoft.BatchAI/workspaces/clusters/write | Criar um novo cluster do IA do Lote do Azure ou atualizar um cluster do IA do Lote do Azure existente |
> | Ação | Microsoft.BatchAI/workspaces/delete | Exclui um workspace do IA do Lote |
> | Ação | Microsoft.BatchAI/workspaces/experiments/delete | Exclui um experimento do IA do Lote |
> | Ação | Microsoft.BatchAI/workspaces/experiments/jobs/delete | Excluir um trabalho do IA do Lote do Azure |
> | Ação | Microsoft.BatchAI/workspaces/experiments/jobs/listoutputfiles/action | Lista os arquivos de saída de um trabalho do AI do Lote |
> | Ação | Microsoft.BatchAI/workspaces/experiments/jobs/read | Listar trabalhos do IA do Lote do Azure ou obter as propriedades de um trabalho do IA do Lote do Azure |
> | Ação | Microsoft.BatchAI/workspaces/experiments/jobs/remoteLoginInformation/action | Listar informações de logon remoto para um trabalho do IA do Lote do Azure |
> | Ação | Microsoft.BatchAI/workspaces/experiments/jobs/terminate/action | Termina um trabalho do IA do Lote do Azure |
> | Ação | Microsoft.BatchAI/workspaces/experiments/jobs/write | Criar um novo trabalho do IA do Lote do Azure ou atualizar um trabalho do IA do Lote do Azure existente |
> | Ação | Microsoft.BatchAI/workspaces/experiments/read | Lista experimentos do IA do Lote ou obtém as propriedades de um experimento do IA do Lote |
> | Ação | Microsoft.BatchAI/workspaces/experiments/write | Cria um novo experimento do IA do Lote ou atualiza um experimento do IA do Lote existente |
> | Ação | Microsoft.BatchAI/workspaces/fileservers/delete | Excluir um servidor de arquivos do IA do Lote do Azure |
> | Ação | Microsoft.BatchAI/workspaces/fileservers/read | Listar servidores de arquivos do IA do Lote do Azure ou obter as propriedades de um servidor de arquivos do IA do Lote do Azure |
> | Ação | Microsoft.BatchAI/workspaces/fileservers/write | Criar um novo servidor de arquivos do IA do Lote do Azure ou atualizar um servidor de arquivos do IA do Lote do Azure existente |
> | Ação | Microsoft.BatchAI/workspaces/read | Lista workspaces do IA do Lote ou obtém as propriedades de um workspace do IA do Lote |
> | Ação | Microsoft.BatchAI/workspaces/write | Cria um novo workspace do IA do Lote ou atualiza um workspace do IA do Lote existente |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Billing/billingPeriods/read | Listar os períodos de cobrança disponíveis |
> | Ação | Microsoft.Billing/invoices/read | Listar faturas disponíveis |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.BingMaps/mapApis/Delete | Operação de Exclusão |
> | Ação | Microsoft.BingMaps/mapApis/listSecrets/action | Listar os segredos |
> | Ação | Microsoft.BingMaps/mapApis/listSingleSignOnToken/action | Ler o token de autorização de logon único para o recurso |
> | Ação | Microsoft.BingMaps/mapApis/Read | Operação de Leitura |
> | Ação | Microsoft.BingMaps/mapApis/regenerateKey/action | Regenera a chave |
> | Ação | Microsoft.BingMaps/mapApis/Write | Operação de Gravação |
> | Ação | Microsoft.BingMaps/Operations/read | Descrição da operação. |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Blueprint/blueprintAssignments/delete | Excluir quaisquer artefatos do Blueprint |
> | Ação | Microsoft.Blueprint/blueprintAssignments/read | Ler qualquer Artefato do Blueprint |
> | Ação | Microsoft.Blueprint/blueprintAssignments/write | Criar ou atualizar quaisquer artefatos de blueprint |
> | Ação | Microsoft.Blueprint/blueprints/artifacts/delete | Excluir quaisquer artefatos do Blueprint |
> | Ação | Microsoft.Blueprint/blueprints/artifacts/read | Ler qualquer Artefato do Blueprint |
> | Ação | Microsoft.Blueprint/blueprints/artifacts/write | Criar ou atualizar quaisquer artefatos de blueprint |
> | Ação | Microsoft.Blueprint/blueprints/delete | Excluir qualquer Blueprint |
> | Ação | Microsoft.Blueprint/blueprints/read | Ler quaisquer Blueprints |
> | Ação | Microsoft.Blueprint/blueprints/versions/artifacts/read | Ler qualquer Artefato do Blueprint |
> | Ação | Microsoft.Blueprint/blueprints/versions/delete | Excluir qualquer Blueprint |
> | Ação | Microsoft.Blueprint/blueprints/versions/read | Ler quaisquer Blueprints |
> | Ação | Microsoft.Blueprint/blueprints/versions/write | Criar ou Atualizar quaisquer Blueprints |
> | Ação | Microsoft.Blueprint/blueprints/write | Criar ou Atualizar quaisquer Blueprints |
> | Ação | Microsoft.Blueprint/register/action | Registra o Provedor de Recursos de Blueprint |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.BotService/botServices/channels/delete | Excluir um serviço de Bot |
> | Ação | Microsoft.BotService/botServices/channels/read | Ler um serviço de Bot |
> | Ação | Microsoft.BotService/botServices/channels/write | Gravar um serviço de Bot |
> | Ação | Microsoft.BotService/botServices/connections/delete | Excluir um serviço de Bot |
> | Ação | Microsoft.BotService/botServices/connections/read | Ler um serviço de Bot |
> | Ação | Microsoft.BotService/botServices/connections/write | Gravar um serviço de Bot |
> | Ação | Microsoft.BotService/botServices/delete | Excluir um serviço de Bot |
> | Ação | Microsoft.BotService/botServices/read | Ler um serviço de Bot |
> | Ação | Microsoft.BotService/botServices/write | Gravar um serviço de Bot |
> | Ação | Microsoft.BotService/locations/operationresults/read | Ler o status de uma operação assíncrona |
> | Ação | Microsoft.BotService/Operations/read | Ler as operações para todos os tipos de recursos |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Cache/checknameavailability/action | Verificar se um nome está disponível para uso com um novo Cache Redis |
> | Ação | Microsoft.Cache/locations/operationresults/read | Obter o resultado de uma operação de execução longa para a qual o cabeçalho 'Localização' foi retornado anteriormente para o cliente |
> | Ação | Microsoft.Cache/operations/read | Listar as operações que o provedor 'Microsoft.Cache' fornece suporte. |
> | Ação | Microsoft.Cache/redis/delete | Excluir todo o Cache Redis |
> | Ação | Microsoft.Cache/redis/export/action | Exportar dados do Redis para blobs de armazenamento prefixados no formato especificado |
> | Ação | Microsoft.Cache/redis/firewallRules/delete | Excluir regras de firewall de IP de um Cache Redis |
> | Ação | Microsoft.Cache/redis/firewallRules/read | Obter as regras de firewall de IP de um Cache Redis |
> | Ação | Microsoft.Cache/redis/firewallRules/write | Editar as regras de firewall de IP de um Cache Redis |
> | Ação | Microsoft.Cache/redis/forceReboot/action | Forçar a reinicialização de uma instância de cache, possivelmente com perda de dados. |
> | Ação | Microsoft.Cache/redis/import/action | Importar dados em um formato especificado de vários blobs para o Redis |
> | Ação | Microsoft.Cache/redis/linkedservers/delete | Excluir servidor vinculado de um Cache Redis |
> | Ação | Microsoft.Cache/redis/linkedservers/read | Obter servidores vinculados associados a um Cache Redis. |
> | Ação | Microsoft.Cache/redis/linkedservers/write | Adicionar um servidor vinculado a um Cache Redis |
> | Ação | Microsoft.Cache/redis/listKeys/action | Exibir o valor das chaves de acesso do Cache Redis no portal de gerenciamento |
> | Ação | Microsoft.Cache/redis/listUpgradeNotifications/read | Listar as notificações de atualização mais recentes do locatário de cache. |
> | Ação | Microsoft.Cache/redis/metricDefinitions/read | Obter a métrica disponível para um Cache Redis |
> | Ação | Microsoft.Cache/redis/patchSchedules/delete | Excluir a agenda de patch de um Cache Redis |
> | Ação | Microsoft.Cache/redis/patchSchedules/read | Obter o agendamento de aplicação de patch de um Cache Redis |
> | Ação | Microsoft.Cache/redis/patchSchedules/write | Modificar o agendamento de aplicação de patch de um Cache Redis |
> | Ação | Microsoft.Cache/redis/read | Exibir as configurações do Cache Redis e a configuração no portal de gerenciamento |
> | Ação | Microsoft.Cache/redis/recommendations/read | Ler as recomendações do Cache Redis do Azure |
> | Ação | Microsoft.Cache/redis/regenerateKey/action | Alterar o valor das chaves de acesso do Cache Redis no portal de gerenciamento |
> | Ação | Microsoft.Cache/redis/start/action | Iniciar uma instância de cache. |
> | Ação | Microsoft.Cache/redis/start/action | Iniciar uma instância de cache. |
> | Ação | Microsoft.Cache/redis/stop/action | Interromper uma instância de cache. |
> | Ação | Microsoft.Cache/redis/write | Modificar as configurações do Cache Redis e a configuração no portal de gerenciamento |
> | Ação | Microsoft.Cache/register/action | Registra o provedor de recursos 'Microsoft.Cache' com uma assinatura |
> | Ação | Microsoft.Cache/unregister/action | Cancela o registro do provedor de recursos 'Microsoft.Cache' com uma assinatura |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Capacity/register/action | Registrar o provedor de recursos de Capacidade e permitir a criação de recursos de Capacidade. |
> | Ação | Microsoft.Capacity/reservationorders/action | Atualizar qualquer Reserva |
> | Ação | Microsoft.Capacity/reservationorders/delete | Excluir qualquer Reserva |
> | Ação | Microsoft.Capacity/reservationorders/read | Ler todas as Reservas |
> | Ação | Microsoft.Capacity/reservationorders/reservations/action | Atualizar qualquer Reserva |
> | Ação | Microsoft.Capacity/reservationorders/reservations/delete | Excluir qualquer Reserva |
> | Ação | Microsoft.Capacity/reservationorders/reservations/read | Ler todas as Reservas |
> | Ação | Microsoft.Capacity/reservationorders/reservations/revisions/read | Ler todas as Reservas |
> | Ação | Microsoft.Capacity/reservationorders/reservations/write | Criar qualquer Reserva |
> | Ação | Microsoft.Capacity/reservationorders/write | Criar qualquer Reserva |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Cdn/CheckNameAvailability/action |  |
> | Ação | Microsoft.Cdn/CheckResourceUsage/action |  |
> | Ação | Microsoft.Cdn/edgenodes/delete |  |
> | Ação | Microsoft.Cdn/edgenodes/read |  |
> | Ação | Microsoft.Cdn/edgenodes/write |  |
> | Ação | Microsoft.Cdn/operationresults/delete |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/CheckResourceUsage/action |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/CheckResourceUsage/action |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/Load/action |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/write |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/Purge/action |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/Start/action |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/read |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/write |  |
> | Ação | Microsoft.Cdn/operationresults/read |  |
> | Ação | Microsoft.Cdn/operationresults/write |  |
> | Ação | Microsoft.Cdn/operations/read |  |
> | Ação | Microsoft.Cdn/profiles/CheckResourceUsage/action |  |
> | Ação | Microsoft.Cdn/profiles/delete |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/CheckResourceUsage/action |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/customdomains/delete |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/customdomains/EnableCustomHttps/action |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/customdomains/read |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/customdomains/write |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/delete |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/Load/action |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/origins/delete |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/origins/read |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/origins/write |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/read | Obter as configurações de diagnóstico do recurso |
> | Ação | Microsoft.Cdn/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar as configurações de diagnóstico para o recurso |
> | Ação | Microsoft.Cdn/profiles/endpoints/providers/Microsoft.Insights/logDefinitions/read | Obter os logs disponíveis para o Microsoft.Cdn |
> | Ação | Microsoft.Cdn/profiles/endpoints/Purge/action |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/read |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/Start/action |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/Stop/action |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/write |  |
> | Ação | Microsoft.Cdn/profiles/GenerateSsoUri/action |  |
> | Ação | Microsoft.Cdn/profiles/GetSupportedOptimizationTypes/action |  |
> | Ação | Microsoft.Cdn/profiles/read |  |
> | Ação | Microsoft.Cdn/profiles/write |  |
> | Ação | Microsoft.Cdn/register/action | Registrar a assinatura no provedor de recursos CDN e habilitar a criação de perfis CDN. |
> | Ação | Microsoft.Cdn/ValidateProbe/action |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | Excluir um certificado existente |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | Obter a lista de certificados |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | Adicionar um novo certificado ou atualizar um existente |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/Delete | Excluir um AppServiceCertificate existente |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/Read | Obter a lista de CertificateOrders |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | Emitir novamente um certificateorder existente |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/renew/Action | Renovar um certificateorder existente |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | Reenviar email de certificado |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Reenviar emails de solicitação para outro endereço de email |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Recuperar o selo de site para um certificado do Serviço de Aplicativo emitido |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | Recuperar a lista de ações de certificado |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | Recuperar o histórico de email do certificado |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | Verificar a propriedade de domínio |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/Write | Adicionar uma nova certificateOrder ou atualizar uma existente |
> | Ação | Microsoft.CertificateRegistration/operations/Read | Listar todas as operações do registro de certificado de serviço de aplicativo |
> | Ação | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Provisionar entidade de serviço para entidade de serviço de aplicativo |
> | Ação | Microsoft.CertificateRegistration/register/action | Registrar o provedor de recursos do Microsoft Certificates da assinatura |
> | Ação | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | Validar objeto de compra de certificado sem enviá-lo |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.ClassicCompute/capabilities/read | Mostra as funcionalidades |
> | Ação | Microsoft.ClassicCompute/checkDomainNameAvailability/action | Verificar a disponibilidade de um nome de domínio específico. |
> | Ação | Microsoft.ClassicCompute/checkDomainNameAvailability/read | Obtém a disponibilidade de um determinado nome de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/active/write | Define o nome de domínio ativo. |
> | Ação | Microsoft.ClassicCompute/domainNames/availabilitySets/read | Mostra a conjunto de disponibilidade para o recurso. |
> | Ação | Microsoft.ClassicCompute/domainNames/capabilities/read | Mostra os recursos de nome de domínio |
> | Ação | Microsoft.ClassicCompute/domainNames/delete | Remover os nomes de domínio dos recursos. |
> | Ação | Microsoft.ClassicCompute/domainNames/deploymentslots/read | Mostra os slots de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/read | Obter a função no slot de implantação de nome de domínio |
> | Ação | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/roleinstances/read | Obter a instância de função para a função no slot de implantação de nome de domínio |
> | Ação | Microsoft.ClassicCompute/domainNames/deploymentslots/state/read | Obtenha o estado do slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/deploymentslots/state/write | Adicione o estado do slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/read | Obter domínio de atualização para o slot de implantação no nome de domínio |
> | Ação | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/write | Atualizar domínio de atualização para o slot de implantação no nome de domínio |
> | Ação | Microsoft.ClassicCompute/domainNames/deploymentslots/write | Criar ou atualizar a implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/extensions/delete | Remover as extensões de nome de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | Ler o status da operação das extensões de nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/extensions/read | Retornar extensões de nome de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/extensions/write | Adicionar as extensões de nome de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | Remover um novo balanceamento de carga interno. |
> | Ação | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | Ler o status da operação para os balanceadores de carga internos dos nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | Obter os balanceadores de carga internos. |
> | Ação | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | Criar um novo balanceamento de carga interno. |
> | Ação | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | Ler o status da operação para os conjuntos de pontos de extremidade com balanceamento de carga dos nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | Obtenha os conjuntos de ponto de extremidade com balanceamento de carga. |
> | Ação | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/write | Adicione o conjunto de ponto de extremidade com balanceamento de carga. |
> | Ação | Microsoft.ClassicCompute/domainNames/operationstatuses/read | Obtenha o status da operação do nome de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/operationStatuses/read | Ler o status da operação das extensões de nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/read | Retornar os nomes de domínio dos recursos. |
> | Ação | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | Excluir os certificados de serviço usados. |
> | Ação | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | Ler o status da operação para os certificados de serviço dos nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | Retornar todos os certificados de serviço usados. |
> | Ação | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | Adicionar ou modificar os certificados de serviço usados. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/abortMigration/action | Aborta a migração de um slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/commitMigration/action | Confirma a migração de um slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/delete | Excluir determinado slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | Ler o status da operação dos slots de nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/prepareMigration/action | Prepara a migração de um slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/read | Mostra os slots de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | Remover a referência de extensão para a função do slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | Ler o status da operação de referências de extensão das funções de slot dos nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | Retornar a referência de extensão para a função do slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | Adicionar ou modificar a referência de extensão para a função do slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/metricdefinitions/read | Obtenha a definição de métrica de função para o nome de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/metrics/read | Obtenha a métrica de função para o nome de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/operationstatuses/read | Obtenha o status da operação para a função de slot de nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | Obter as configurações de diagnóstico. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | Adicionar ou modificar configurações de diagnóstico. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | Obter as definições de métrica. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/read | Obter a função do slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/downloadremotedesktopconnectionfile/action | Baixa o arquivo de conexão de área de trabalho remota para a instância de função na função do slot de nome de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | Obtém o status da operação para a instância de função na função do slot de nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | Obter a instância de função. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | Recompilar a instância de função. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | Refaz a imagem da instância de função. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | Reinicia as instâncias de função. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/skus/read | Obtenha o SKU de função para o slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/write | Adicione uma função para o slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/start/action | Inicia um slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/state/start/write | Altera o estado do slot de implantação para interrompido. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | Altera o estado do slot de implantação para iniciado. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/stop/action | Suspende o slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | Atualizar o domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/validateMigration/action | Validar a migração de um slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/write | Criar ou atualizar a implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/swap/action | Alterna o slot de preparo para o slot de produção. |
> | Ação | Microsoft.ClassicCompute/domainNames/write | Adicionar ou modificar os nomes de domínio dos recursos. |
> | Ação | Microsoft.ClassicCompute/moveSubscriptionResources/action | Mover todos os recursos clássicos para uma assinatura diferente. |
> | Ação | Microsoft.ClassicCompute/operatingSystemFamilies/read | Listar as famílias de sistemas operacionais convidados disponíveis no Microsoft Azure e também listar as versões dos sistemas operacionais disponíveis para cada família. |
> | Ação | Microsoft.ClassicCompute/operatingSystems/read | Listar as versões do sistema operacional convidado disponíveis atualmente no Microsoft Azure. |
> | Ação | Microsoft.ClassicCompute/operations/read | Obtém a lista de operações. |
> | Ação | Microsoft.ClassicCompute/operationStatuses/read | Ler o status da operação do recurso. |
> | Ação | Microsoft.ClassicCompute/quotas/read | Obter a cota para a assinatura. |
> | Ação | Microsoft.ClassicCompute/register/action | Registrar para computação clássica |
> | Ação | Microsoft.ClassicCompute/resourceTypes/skus/read | Obter a lista de SKU para tipos de recursos com suporte. |
> | Ação | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | Valide a disponibilidade da assinatura para a operação de movimentação clássica. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | Excluir o grupo de segurança de rede associado à máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | Ler que o status de operação das máquinas virtuais associadas a grupos de segurança de rede. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | Obter o grupo de segurança de rede associado à máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | Adiciona um grupo de segurança de rede associado à máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | Obter as operações assíncronas possíveis |
> | Ação | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | Anexa um disco de dados a uma máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/capture/action | Capture uma máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/delete | Remover as máquinas virtuais. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | Desanexa um disco de dados da máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/diagnosticsettings/read | Obtenha as configurações de diagnóstico de máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/disks/read | Recupera a lista de discos de dados |
> | Ação | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | Baixa o arquivo RDP na máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | Ler o status da operação das extensões das máquinas virtuais. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/extensions/read | Obter a extensão da máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/extensions/write | Coloca a extensão da máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | Obtenha a definição de métrica de máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/metrics/read | Obter a métrica. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | Excluir um grupo de segurança de rede associado ao adaptador de rede. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | Ler que o status de operação das máquinas virtuais associadas a grupos de segurança de rede. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | Obter um grupo de segurança de rede associado ao adaptador de rede. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | Adiciona um grupo de segurança de rede associado ao adaptador de rede. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | Ler o status da operação das máquinas virtuais. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | Executar a manutenção na máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | Obter as configurações de diagnóstico. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | Adicionar ou modificar configurações de diagnóstico. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Obter as definições de métrica. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/read | Retorna a lista de máquinas virtuais. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/redeploy/action | Reimplanta a máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/restart/action | Reinicia as máquinas virtuais. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/shutdown/action | Desliga a máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/start/action | Iniciar a máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/stop/action | Interrompe a máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/write | Adicionar ou modificar máquinas virtuais. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.ClassicNetwork/expressroutecrossconnections/operationstatuses/read | Obtenha um status de operação de conexão cruzada do ExpressRoute. |
> | Ação | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/delete | Exclua o emparelhamento de conexão cruzada do ExpressRoute. |
> | Ação | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/operationstatuses/read | Obtenha um status de operação de emparelhamento de conexão cruzada do ExpressRoute. |
> | Ação | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/read | Obtenha o emparelhamento de conexão cruzada do ExpressRoute. |
> | Ação | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/write | Adicione o emparelhamento de conexão cruzada do ExpressRoute. |
> | Ação | Microsoft.ClassicNetwork/expressroutecrossconnections/read | Obtenha conexões cruzadas do ExpressRoute. |
> | Ação | Microsoft.ClassicNetwork/expressroutecrossconnections/write | Adicione conexões cruzadas do ExpressRoute. |
> | Ação | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | Recupera a lista de dispositivos com suporte. |
> | Ação | Microsoft.ClassicNetwork/networkSecurityGroups/delete | Excluir o grupo de segurança de rede. |
> | Ação | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | Ler o status da operação do grupo de segurança de rede. |
> | Ação | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | Obter as configurações de diagnóstico dos Grupos de Segurança de Rede |
> | Ação | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar as configurações de diagnóstico dos Grupos de Segurança de Rede, essa operação é complementada pelo provedor de recursos do Microsoft Insights. |
> | Ação | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | Obter os eventos para o grupo de segurança de rede |
> | Ação | Microsoft.ClassicNetwork/networkSecurityGroups/read | Obter o grupo de segurança de rede. |
> | Ação | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | Excluir a regra de segurança. |
> | Ação | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | Ler o status da operação das regras de segurança do grupo de segurança de rede. |
> | Ação | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | Obter a regra de segurança. |
> | Ação | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | Adiciona ou atualiza uma regra de segurança. |
> | Ação | Microsoft.ClassicNetwork/networkSecurityGroups/write | Adiciona um novo grupo de segurança de rede. |
> | Ação | Microsoft.ClassicNetwork/operations/read | Obtenha operações de rede clássicas. |
> | Ação | Microsoft.ClassicNetwork/quotas/read | Obter a cota para a assinatura. |
> | Ação | Microsoft.ClassicNetwork/register/action | Registrar-se na rede clássica |
> | Ação | Microsoft.ClassicNetwork/reservedIps/delete | Excluir um IP reservado. |
> | Ação | Microsoft.ClassicNetwork/reservedIps/join/action | Ingressar em um IP reservado |
> | Ação | Microsoft.ClassicNetwork/reservedIps/link/action | Vincular um IP reservado |
> | Ação | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | Ler o status da operação dos IPs reservados. |
> | Ação | Microsoft.ClassicNetwork/reservedIps/read | Obter os IPs reservados |
> | Ação | Microsoft.ClassicNetwork/reservedIps/write | Adicionar um novo IP reservado |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/abortMigration/action | Anula a migração de uma Rede Virtual |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | Mostra as funcionalidades |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | Verificar a disponibilidade de determinado endereço IP em uma rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/commitMigration/action | Confirma a migração de uma Rede Virtual |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/delete | Excluir a rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | Cancela a revogação de um certificado de cliente. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | Ler os certificados de cliente revogados. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | Revoga um certificado de cliente. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | Excluir o certificado de cliente do gateway de rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | Baixa o certificado por impressão digital. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | Listar o pacote de certificado do gateway de rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | Localizar os certificados raiz do cliente. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | Carrega um novo certificado raiz do cliente. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/connect/action | Conecta uma conexão de gateway site a site. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/disconnect/action | Desconecta uma conexão de gateway site a site. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/read | Recupera a lista de conexões. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/test/action | Testa uma conexão de gateway site a site. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/delete | Excluir o gateway de rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/action | Baixa o script de configuração do dispositivo. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/action | Baixa o diagnóstico de gateway. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/action | Recupera a chave de serviço do circuito. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/listPackage/action | Listar o pacote de gateway de rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | Ler o status da operação dos gateways de redes virtuais. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/packages/read | Obter o pacote de gateway de rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/read | Obter os gateways de rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | Inicia o diagnóstico do gateway de rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/action | Interrompe o diagnóstico do gateway de rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/write | Adiciona um gateway de rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/join/action | Ingressar na rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | Ler o status da operação das redes virtuais. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/peer/action | Coloca uma rede virtual com outra rede virtual de mesmo nível. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/prepareMigration/action | Prepara a migração de uma Rede Virtual |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/read | Obter a rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | Exclui o proxy de emparelhamento de rede virtual remota. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | Obtém o proxy de emparelhamento de rede virtual remota. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | Adiciona ou atualiza o proxy de emparelhamento de rede virtual remota. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | Excluir o grupo de segurança de rede associado à sub-rede. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | Ler o status da operação do grupo de segurança associado à sub-rede da rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | Obter um grupo de segurança de rede associado à sub-rede. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | Adiciona um grupo de segurança de rede associado à sub-rede. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/validateMigration/action | Valida a migração de uma Rede Virtual |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | Obtém o emparelhamento de rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/write | Adicionar uma nova rede virtual. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.ClassicStorage/capabilities/read | Mostra as funcionalidades |
> | Ação | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | Verificar a disponibilidade de uma conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/checkStorageAccountAvailability/read | Obtenha a disponibilidade de uma conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/disks/read | Retornar o disco da conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/images/operationstatuses/read | Obtém o Status da Operação de Imagem. |
> | Ação | Microsoft.ClassicStorage/images/read | Retornar a imagem. |
> | Ação | Microsoft.ClassicStorage/operations/read | Obtém as operações de armazenamento clássicas |
> | Ação | Microsoft.ClassicStorage/osImages/read | Retornar a imagem do sistema operacional. |
> | Ação | Microsoft.ClassicStorage/osPlatformImages/read | Obtém a imagem da plataforma do sistema operacional. |
> | Ação | Microsoft.ClassicStorage/publicImages/read | Obter a imagem pública da máquina virtual. |
> | Ação | Microsoft.ClassicStorage/quotas/read | Obter a cota para a assinatura. |
> | Ação | Microsoft.ClassicStorage/register/action | Registrar para armazenamento clássico |
> | Ação | Microsoft.ClassicStorage/storageAccounts/abortMigration/action | Anula a migração de uma conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/commitMigration/action | Confirma a migração de uma conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/delete | Excluir a conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/disks/delete | Excluir um disco de conta de armazenamento específico. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | Ler o status da operação do recurso. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/disks/read | Retornar o disco da conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/disks/write | Adiciona um disco de conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/images/delete | Excluir uma imagem da conta de armazenamento específica. (Preterido. Usar 'Microsoft.ClassicStorage/storageAccounts/vmImages') |
> | Ação | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | Retorna o status da operação da imagem da conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/images/read | Retornar a imagem da conta de armazenamento. (Preterido. Usar 'Microsoft.ClassicStorage/storageAccounts/vmImages') |
> | Ação | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Listar as chaves de acesso das contas de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | Ler o status da operação do recurso. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/osImages/delete | Excluir uma imagem do sistema operacional da conta de armazenamento específica. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/osImages/read | Retornar a imagem do sistema operacional da conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/osImages/write | Adiciona uma determinada imagem do sistema operacional da conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/prepareMigration/action | Prepara a migração de uma conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/read | Retornar a conta de armazenamento com a conta fornecida. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | Regenera as chaves de acesso existentes da conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | Obter as configurações de diagnóstico. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | Adicionar ou modificar configurações de diagnóstico. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | Obter as definições de métrica. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | Obter a métrica. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/services/read | Obter os serviços disponíveis. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/validateMigration/action | Valida a migração de uma conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | Exclui uma imagem de máquina virtual fornecida. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | Obtém o status da operação de imagem de uma determinada máquina virtual. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/vmImages/read | Retorna a imagem da máquina virtual. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/vmImages/write | Adiciona a imagem de uma determinada máquina virtual. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/write | Adiciona uma nova conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/vmImages/read | Lista imagens da máquina virtual. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.CognitiveServices/accounts/delete | Excluir contas de API |
> | Ação | Microsoft.CognitiveServices/accounts/listKeys/action | Listar chaves |
> | Ação | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso. |
> | Ação | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso. |
> | Ação | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/logDefinitions/read | Obtém os logs disponíveis para a conta dos Serviços Cognitivos |
> | Ação | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para os Serviços Cognitivos. |
> | Ação | Microsoft.CognitiveServices/accounts/read | Ler as contas de API. |
> | Ação | Microsoft.CognitiveServices/accounts/regenerateKey/action | Regenerar chave |
> | Ação | Microsoft.CognitiveServices/accounts/skus/read | Ler SKUs disponíveis para um recurso existente. |
> | Ação | Microsoft.CognitiveServices/accounts/usages/read | Obter a utilização de cota de um recurso existente. |
> | Ação | Microsoft.CognitiveServices/accounts/write | Grava as contas de API. |
> | Ação | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Ler as SKUs disponíveis para uma assinatura. |
> | Ação | Microsoft.CognitiveServices/Operations/read | Listar todas as operações disponíveis |
> | Ação | Microsoft.CognitiveServices/register/action | Registrar assinatura para Serviços Cognitivos |
> | Ação | Microsoft.CognitiveServices/skus/read | Ler as SKUs disponíveis para os Serviços Cognitivos. |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Commerce/RateCard/read | Retornar dados da oferta, metadados de recurso/medidor e tarifas da assinatura especificada. |
> | Ação | Microsoft.Commerce/UsageAggregates/read | Recupera o consumo do Microsoft Azure para uma assinatura. O resultado contém dados totais de uso, assinatura e informações relativas a recursos em determinado intervalo de tempo. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Compute/availabilitySets/delete | Excluir o conjunto de disponibilidade |
> | Ação | Microsoft.Compute/availabilitySets/read | Obter as propriedades de um conjunto de disponibilidade |
> | Ação | Microsoft.Compute/availabilitySets/vmSizes/read | Listar os tamanhos disponíveis para criar ou atualizar uma máquina virtual no conjunto de disponibilidade |
> | Ação | Microsoft.Compute/availabilitySets/write | Criar um novo conjunto de disponibilidade ou atualizar um existente |
> | Ação | Microsoft.Compute/disks/beginGetAccess/action | Obter o URI de SAS do disco para acesso de blob |
> | Ação | Microsoft.Compute/disks/delete | Excluir o disco |
> | Ação | Microsoft.Compute/disks/endGetAccess/action | Revogar o URI de SAS do disco |
> | Ação | Microsoft.Compute/disks/read | Obter as propriedades de um disco |
> | Ação | Microsoft.Compute/disks/write | Criar um novo disco ou atualizar um existente |
> | Ação | Microsoft.Compute/galleries/delete | Exclui a Galeria |
> | Ação | Microsoft.Compute/galleries/images/delete | Exclui a Imagem da Galeria |
> | Ação | Microsoft.Compute/galleries/images/read | Obtém as propriedades da Imagem da Galeria |
> | Ação | Microsoft.Compute/galleries/images/versions/delete | Exclui a Versão da Imagem da Galeria |
> | Ação | Microsoft.Compute/galleries/images/versions/delete | Obtém as propriedades da Versão de Imagem da Galeria |
> | Ação | Microsoft.Compute/galleries/images/versions/write | Cria uma nova Versão da Imagem da Galeria ou atualiza uma existente |
> | Ação | Microsoft.Compute/galleries/images/write | Cria uma nova Imagem da Galeria ou atualiza uma existente |
> | Ação | Microsoft.Compute/galleries/read | Obtém as propriedades da Galeria |
> | Ação | Microsoft.Compute/galleries/write | Cria uma nova Galeria ou atualiza uma existente |
> | Ação | Microsoft.Compute/images/delete | Excluir a imagem |
> | Ação | Microsoft.Compute/images/read | Obter as propriedades da imagem |
> | Ação | Microsoft.Compute/images/write | Criar uma nova imagem ou atualizar uma existente |
> | Ação | Microsoft.Compute/locations/capsOperations/read | Obtém o status de uma operação de Limites assíncrona |
> | Ação | Microsoft.Compute/locations/diskOperations/read | Obtém o status de uma operação de Disco assíncrona |
> | Ação | Microsoft.Compute/locations/logAnalytics/getRequestRateByInterval/action | Criar logs para mostrar o total de solicitações por intervalo de tempo para auxiliar no diagnóstico de limitação. |
> | Ação | Microsoft.Compute/locations/logAnalytics/getThrottledRequests/action | Criar logs para mostrar agregações das solicitações limitadas, agrupadas por ResourceName, OperationName ou a política de limitação aplicada. |
> | Ação | Microsoft.Compute/locations/operations/read | Obter o status de uma operação assíncrona |
> | Ação | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | Obter as propriedades de uma Oferta de Imagem de Plataforma |
> | Ação | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | Obter as propriedades de uma SKU de Imagem de Plataforma |
> | Ação | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | Obter as propriedades de uma SKU de Imagem de Plataforma |
> | Ação | Microsoft.Compute/locations/publishers/artifacttypes/types/read | Obter as propriedades de um tipo de VMExtension |
> | Ação | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | Obter as propriedades de uma versão do VMExtension |
> | Ação | Microsoft.Compute/locations/publishers/read | Obter as propriedades de um Publicador |
> | Ação | Microsoft.Compute/locations/runCommands/read | Listar os comandos de execução disponíveis no local |
> | Ação | Microsoft.Compute/locations/usages/read | Obter as quantidades de uso atual e limites de serviço para os recursos de computação da assinatura em determinado local |
> | Ação | Microsoft.Compute/locations/vmSizes/read | Listar os tamanhos de máquina virtual disponível em um local |
> | Ação | Microsoft.Compute/operations/read | Listar as operações disponíveis no provedor de recursos Microsoft.Compute |
> | Ação | Microsoft.Compute/register/action | Registra a assinatura com o provedor de recursos Microsoft.Compute |
> | Ação | Microsoft.Compute/restorePointCollections/delete | Excluir a coleção de pontos de restauração e os pontos contidos nela |
> | Ação | Microsoft.Compute/restorePointCollections/read | Obter as propriedades de uma coleção de pontos de restauração |
> | Ação | Microsoft.Compute/restorePointCollections/restorePoints/delete | Excluir o ponto de restauração |
> | Ação | Microsoft.Compute/restorePointCollections/restorePoints/read | Obter as propriedades de um ponto de restauração |
> | Ação | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | Obter as propriedades de um ponto de restauração com URIs de SAS do blob |
> | Ação | Microsoft.Compute/restorePointCollections/restorePoints/write | Criar um novo ponto de restauração |
> | Ação | Microsoft.Compute/restorePointCollections/write | Criar uma nova coleção de pontos de restauração ou atualizar uma existente |
> | Ação | Microsoft.Compute/sharedVMImages/delete | Exclui o SharedVMImage |
> | Ação | Microsoft.Compute/sharedVMImages/read | Obter as propriedades de um SharedVMImage |
> | Ação | Microsoft.Compute/sharedVMImages/versions/delete | Excluir um SharedVMImageVersion |
> | Ação | Microsoft.Compute/sharedVMImages/versions/read | Obter as propriedades de um SharedVMImageVersion |
> | Ação | Microsoft.Compute/sharedVMImages/versions/replicate/action | Replicar um SharedVMImageVersion para direcionar regiões |
> | Ação | Microsoft.Compute/sharedVMImages/versions/write | Criar um novo SharedVMImageVersion ou atualizar um existente |
> | Ação | Microsoft.Compute/sharedVMImages/write | Criar um novo SharedVMImage ou atualizar um existente |
> | Ação | Microsoft.Compute/skus/read | Obter a lista de SKUs do Microsoft.Compute disponíveis para sua Assinatura |
> | Ação | Microsoft.Compute/snapshots/beginGetAccess/action | Obter o URI de SAS do Instantâneo para acesso de blob |
> | Ação | Microsoft.Compute/snapshots/delete | Excluir um instantâneo |
> | Ação | Microsoft.Compute/snapshots/endGetAccess/action | Revogar o URI de SAS do Instantâneo |
> | Ação | Microsoft.Compute/snapshots/read | Obter as propriedades de um instantâneo |
> | Ação | Microsoft.Compute/snapshots/write | Criar um novo instantâneo ou atualizar um existente |
> | Ação | Microsoft.Compute/virtualMachines/capture/action | Capturar a máquina virtual copiando discos rígidos virtuais e gera um modelo que pode ser usado para criar máquinas virtuais semelhantes |
> | Ação | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | Converter os discos baseados em blob da máquina virtual em discos gerenciados |
> | Ação | Microsoft.Compute/virtualMachines/deallocate/action | Desligar a máquina virtual e liberar os recursos de computação |
> | Ação | Microsoft.Compute/virtualMachines/delete | Excluir a máquina virtual |
> | Ação | Microsoft.Compute/virtualMachines/extensions/delete | Excluir a extensão da máquina virtual |
> | Ação | Microsoft.Compute/virtualMachines/extensions/read | Obter as propriedades de uma extensão da máquina virtual |
> | Ação | Microsoft.Compute/virtualMachines/extensions/write | Criar uma nova extensão da máquina virtual ou atualizar uma existente |
> | Ação | Microsoft.Compute/virtualMachines/generalize/action | Definir o estado da máquina virtual como Generalizada e preparar a máquina virtual para captura |
> | Ação | Microsoft.Compute/virtualMachines/instanceView/read | Obter o status de tempo de execução detalhado da máquina virtual e seus recursos |
> | DataAction | Microsoft.Compute/virtualMachines/login/action | Faça logon em uma máquina virtual como um usuário normal |
> | DataAction | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Faça logon em uma máquina virtual com os privilégios de administrador do Windows ou de usuário raiz do Linux |
> | Ação | Microsoft.Compute/virtualMachines/performMaintenance/action | Executar a Operação de Manutenção na VM. |
> | Ação | Microsoft.Compute/virtualMachines/powerOff/action | Desligar a máquina virtual. Observe que a máquina virtual continuará a ser cobrada. |
> | Ação | Microsoft.Compute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Ler definições de Métrica de Máquina Virtual |
> | Ação | Microsoft.Compute/virtualMachines/read | Obter as propriedades de uma máquina virtual |
> | Ação | Microsoft.Compute/virtualMachines/redeploy/action | Reimplantar a máquina virtual |
> | Ação | Microsoft.Compute/virtualMachines/reimage/action | Refazer a imagem da máquina virtual que está usando o disco diferencial. |
> | Ação | Microsoft.Compute/virtualMachines/restart/action | Reinicia a máquina virtual |
> | Ação | Microsoft.Compute/virtualMachines/runCommand/action | Executar um script predefinido na máquina virtual |
> | Ação | Microsoft.Compute/virtualMachines/start/action | Inicia a máquina virtual |
> | Ação | Microsoft.Compute/virtualMachines/vmSizes/read | Listar os tamanhos disponíveis para os quais a máquina virtual possa ser atualizada |
> | Ação | Microsoft.Compute/virtualMachines/write | Criar uma nova máquina virtual ou atualizar uma máquina virtual existente |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | Desligar e liberar os recursos de computação para as instâncias do Conjunto de Dimensionamento de Máquinas Virtuais  |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/delete | Excluir o Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/delete/action | Excluir as instâncias do Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | Excluir a Extensão de Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/extensions/read | Obter as propriedades de uma Extensão de Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/extensions/write | Criar uma nova Extensão de Conjunto de Dimensionamento de Máquinas Virtuais ou atualizar um existente |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | Percorrer manualmente os domínios de atualização de plataforma de um conjunto de dimensionamento de máquinas virtuais do Service Fabric para concluir uma atualização pendente que está paralisada |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | Obter a exibição de instância do Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | Atualizar manualmente as instâncias para o modelo mais recente do Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | Obter propriedades de todas as interfaces de rede de um Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/osRollingUpgrade/action | Iniciar uma atualização sem interrupção para mover todas as instâncias de Conjunto de Dimensionamento de Máquinas Virtuais para a última versão disponível do sistema operacional de Imagem de Plataforma. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | Obter o histórico de atualizações do SO para um Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | Realizar a manutenção planejada nas instâncias do Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | Desligar a instância do Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a configuração de diagnóstico para o conjunto de dimensionamento de máquinas virtuais. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico para o conjunto de dimensionamento de máquinas virtuais. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/providers/Microsoft.Insights/logDefinitions/read | Obtém os logs disponíveis para conjuntos de dimensionamento de máquinas virtuais. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/providers/Microsoft.Insights/metricDefinitions/read | Ler as Definições de Métricas do Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | Obter propriedades de todos os endereços IP públicos de um Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/read | Obter as propriedades de um Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | Reimplantar as instâncias do Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/reimage/action | Refazer a imagem das instâncias do Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/reimageAll/action | Refaz a imagem de todos os discos (Disco de SO e Discos de Dados) das instâncias de um Conjunto de Dimensionamento de Máquinas Virtuais  |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/restart/action | Reiniciar a instância do Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/cancel/action | Cancelar a atualização sem interrupção de um Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | Obter o status mais recente de Atualização Sem Interrupção para um Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/scale/action | Verificar se um Conjunto de Dimensionamento de Máquinas Virtuais existente pode Reduzir Horizontalmente/Escalar Horizontalmente para a contagem de instâncias especificada |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/skus/read | Listar as SKUs válidas para um Conjunto de Dimensionamento de Máquinas Virtuais existente |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/start/action | Iniciar a instância do Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | Desligar e liberar os recursos de computação para uma máquina virtual em um conjunto de dimensionamento de máquinas virtuais. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | Excluir uma máquina virtual específica em um conjunto de dimensionamento de VM. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | Recuperar a exibição de instância de uma máquina virtual em um conjunto de dimensionamento de máquinas virtuais. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | Obter propriedades do endereço IP público criado usando o Conjunto de Dimensionamento de Máquinas Virtuais. O Conjunto de Dimensionamento de Máquinas Virtuais pode criar no máximo um IP público por ipconfiguration (IP privado) |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | Obter propriedades de uma ou de todas as configurações IP de uma interface de rede criada usando o Conjunto de Dimensionamento de Máquinas Virtuais. Configurações de IP representam IPs privados |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | Obter propriedades de uma ou de todas as interfaces de rede de uma máquina virtual criada usando o Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | Realizar manutenção planejada em uma instância de Máquina Virtual em um Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | Desligar uma instância de máquina virtual em um conjunto de dimensionamento de VM. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Ler Máquina Virtual em Definições de Métricas do Conjunto de Dimensionamento |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | Recuperar as propriedades de uma máquina virtual em um conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | Reimplantar uma instância de Máquina Virtual em um Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | Refazer a imagem de uma instância de Máquina Virtual em um Conjunto de Dimensionamento de Máquinas Virtuais. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimageAll/action | Refaz a imagem de todos os discos (Disco de SO e Discos de Dados) da instância da Máquinas Virtual em um Conjunto de Dimensionamento de Máquinas Virtuais. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | Reiniciar uma instância de máquina virtual em um conjunto de dimensionamento de VM. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommand/action | Executa um script predefinido em uma instância da Máquina Virtual em um Conjunto de Dimensionamento de Máquinas Virtuais. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | Iniciar uma instância de máquina virtual em um conjunto de dimensionamento de VM. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | Atualizar as propriedades de uma Máquina Virtual em um Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/write | Criar um novo Conjunto de Dimensionamento de Máquinas Virtuais ou atualizar um existente |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Consumption/balances/read | Listar o resumo de utilização de um período de cobrança para um grupo de gerenciamento. |
> | Ação | Microsoft.Consumption/budgets/read | Listar os orçamentos por uma assinatura ou um grupo de gerenciamento. |
> | Ação | Microsoft.Consumption/budgets/write | Criar, atualizar e excluir os orçamentos por uma assinatura ou um grupo de gerenciamento. |
> | Ação | Microsoft.Consumption/marketplaces/read | Listar os detalhes de uso do recurso do marketplace para um escopo para assinaturas do EA e WebDirect. |
> | Ação | Microsoft.Consumption/operations/read | Listar todas as operações com suporte pelo provedor de recursos Microsoft.Consumption. |
> | Ação | Microsoft.Consumption/pricesheets/read | Listar os dados de Pricesheets para uma assinatura ou um grupo de gerenciamento. |
> | Ação | Microsoft.Consumption/reservationDetails/read | Listar os detalhes de utilização para instâncias reservadas por ordem de reserva ou grupos de gerenciamento. Os dados detalhados são por instância por nível diário. |
> | Ação | Microsoft.Consumption/reservationRecommendations/read | Lista recomendações únicas ou compartilhadas para Instâncias Reservadas para uma assinatura. |
> | Ação | Microsoft.Consumption/reservationSummaries/read | Listar os detalhes de resumo para instâncias reservadas por ordem de reserva ou grupos de gerenciamento. Os dados de resumo serão no nível mensal ou diário. |
> | Ação | Microsoft.Consumption/reservationTransactions/read | Listar o histórico de transações para instâncias reservadas por grupos de gerenciamento. |
> | Ação | Microsoft.Consumption/tenants/register/action | Registre a ação de escopo do Microsoft.Consumption por um locatário. |
> | Ação | Microsoft.Consumption/terms/read | Listar os termos de uma assinatura ou um grupo de gerenciamento. |
> | Ação | Microsoft.Consumption/usageDetails/read | Listar os detalhes de uso de um escopo para assinaturas do EA e WebDirect. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.ContainerInstance/containerGroups/containers/logs/read | Obter logs para um contêiner específico. |
> | Ação | Microsoft.ContainerInstance/containerGroups/delete | Excluir o grupo de contêineres específico. |
> | Ação | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o grupo de contêineres. |
> | Ação | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o grupo de contêineres. |
> | Ação | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para o grupo de contêineres. |
> | Ação | Microsoft.ContainerInstance/containerGroups/read | Obter todos os grupos de contêineres. |
> | Ação | Microsoft.ContainerInstance/containerGroups/restart/action | Reinicia um grupo de contêineres específico. |
> | Ação | Microsoft.ContainerInstance/containerGroups/stop/action | Interrompe um grupo de contêineres específico. Os recursos de computação serão desalocados e a cobrança será interrompida. |
> | Ação | Microsoft.ContainerInstance/containerGroups/write | Criar ou atualizar um grupo de contêineres específico. |
> | Ação | Microsoft.ContainerInstance/register/action | Registra a assinatura para o provedor de recursos da instância do contêiner e habilita a criação de grupos de contêineres. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.ContainerRegistry/checkNameAvailability/read | Verificar se o nome do registro de contêiner está disponível para uso. |
> | Ação | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | Notifica o Microsoft.ContainerRegistry sobre a exclusão de uma rede virtual ou sub-rede |
> | Ação | Microsoft.ContainerRegistry/locations/operationResults/read | Obter um resultado de operação assíncrona |
> | Ação | Microsoft.ContainerRegistry/operations/read | Listar todas as operações da API REST do Registro de Contêiner do Azure disponíveis |
> | Ação | Microsoft.ContainerRegistry/register/action | Registra a assinatura do provedor de recursos de registro de contêiner e permite a criação de registros de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/builds/cancel/action | Cancelar um build existente. |
> | Ação | Microsoft.ContainerRegistry/registries/builds/getLogLink/action | Obter um link para baixar os logs de build. |
> | Ação | Microsoft.ContainerRegistry/registries/builds/read | Obter as propriedades de build especificado ou lista todos os builds para o registro de contêiner especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/builds/write | Atualizar um build para um registro de contêiner com os parâmetros especificados. |
> | Ação | Microsoft.ContainerRegistry/registries/buildTasks/delete | Excluir uma tarefa de build de um registro de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/buildTasks/listSourceRepositoryProperties/action | Listar as propriedades de controle de origem para uma tarefa de build. |
> | Ação | Microsoft.ContainerRegistry/registries/buildTasks/read | Obter as propriedades da tarefa de build especificada ou lista todas as tarefas de build para o registro de contêiner especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/buildTasks/steps/delete | Excluir uma etapa de build de uma tarefa de build. |
> | Ação | Microsoft.ContainerRegistry/registries/buildTasks/steps/listBuildArguments/action | Listar os argumentos de build para uma etapa de build, incluindo os argumentos secretos. |
> | Ação | Microsoft.ContainerRegistry/registries/buildTasks/steps/read | Obter as propriedades da etapa de build especificada ou listar todas as etapas de build da tarefa de build especificada. |
> | Ação | Microsoft.ContainerRegistry/registries/buildTasks/steps/write | Criar ou atualizar uma etapa de build para uma tarefa de build com os parâmetros especificados. |
> | Ação | Microsoft.ContainerRegistry/registries/buildTasks/write | Criar ou atualizar uma tarefa de build para um registro de contêiner com os parâmetros especificados. |
> | Ação | Microsoft.ContainerRegistry/registries/delete | Excluir um registro de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | Excluir um filtro de grade de eventos de um registro de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/eventGridFilters/read | Obter as propriedades do filtro de grade de eventos especificado ou listar todos os filtros de grade de eventos para o registro de contêiner especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/eventGridFilters/write | Criar ou atualizar um filtro de grade de eventos para um registro de contêiner com os parâmetros especificados. |
> | Ação | Microsoft.ContainerRegistry/registries/getBuildSourceUploadUrl/action | Obter o local de upload para o usuário poder carregar a fonte. |
> | Ação | Microsoft.ContainerRegistry/registries/importImage/action | Importa a imagem para o registro de contêiner com os parâmetros especificados. |
> | Ação | Microsoft.ContainerRegistry/registries/listCredentials/action | Listar as credenciais de logon para o registro de contêiner especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/listPolicies/read | Lista as políticas para o registro de contêiner especificado |
> | Ação | Microsoft.ContainerRegistry/registries/listUsages/read | Listar os usos de cotas para o registro de contêiner especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/operationStatuses/read | Obter um status de operação assíncrona do registro |
> | Ação | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para o Microsoft ContainerRegistry |
> | Ação | Microsoft.ContainerRegistry/registries/pull/read | Efetuar pull ou Obter imagens de um registro de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/push/write | Efetuar push ou Gravar imagens para um registro de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/quarantineRead/read | Efetuar pull ou Obter imagens em quarentena do registro de contêiner |
> | Ação | Microsoft.ContainerRegistry/registries/quarantineWrite/write | Gravar/Modificar o estado de quarentena das imagens em quarentena |
> | Ação | Microsoft.ContainerRegistry/registries/queueBuild/action | Criar um novo build com base nos parâmetros da solicitação e adicionar à fila de build. |
> | Ação | Microsoft.ContainerRegistry/registries/read | Obter as propriedades do registro de contêiner especificado ou listar todos os registros de contêiner sob o grupo de recursos ou assinatura especificada. |
> | Ação | Microsoft.ContainerRegistry/registries/regenerateCredential/action | Regenerar uma das credenciais de logon para o registro de contêiner especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/replications/delete | Excluir uma replicação de um registro de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | Obter um status de operação assíncrona de replicação |
> | Ação | Microsoft.ContainerRegistry/registries/replications/read | Obter as propriedades da replicação especificada ou listar todas as replicações do registro de contêiner especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/replications/write | Criar ou atualizar uma replicação para um registro de contêiner com os parâmetros especificados. |
> | Ação | Microsoft.ContainerRegistry/registries/sign/write | Efetuar push/pull de metadados de conteúdo confiável para um registro de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/updatePolicies/write | Atualiza as políticas para o registro de contêiner especificado |
> | Ação | Microsoft.ContainerRegistry/registries/webhooks/delete | Excluir um webhook de um registro de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | Obter a configuração de URI de serviço e cabeçalhos personalizados para o webhook. |
> | Ação | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | Listar eventos recentes para o webhook especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | Obter um status de operação assíncrona do webhook |
> | Ação | Microsoft.ContainerRegistry/registries/webhooks/ping/action | Disparar um evento de ping para ser enviado ao webhook. |
> | Ação | Microsoft.ContainerRegistry/registries/webhooks/read | Obter as propriedades do webhook especificado ou listar todos os webhooks para o registro de contêiner especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/webhooks/write | Criar ou atualizar um webhook para um registro de contêiner com os parâmetros especificados. |
> | Ação | Microsoft.ContainerRegistry/registries/write | Criar ou atualizar um registro de contêiner com os parâmetros especificados. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.ContainerService/containerServices/delete | Exclui um serviço de contêiner |
> | Ação | Microsoft.ContainerService/containerServices/read | Obtém um serviço de contêiner |
> | Ação | Microsoft.ContainerService/containerServices/write | Cria um novo serviço de contêiner ou atualiza um existente |
> | Ação | Microsoft.ContainerService/locations/operationresults/read | Obtém o status de um resultado de operação assíncrona |
> | Ação | Microsoft.ContainerService/locations/operations/read | Obter o status de uma operação assíncrona |
> | Ação | Microsoft.ContainerService/locations/orchestrators/read | Lista os orquestradores com suporte |
> | Ação | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Obtém um perfil de acesso do cluster gerenciado por nome de função usando a credencial de lista |
> | Ação | Microsoft.ContainerService/managedClusters/accessProfiles/read | Obtém um perfil de acesso do cluster gerenciado por nome de função |
> | Ação | Microsoft.ContainerService/managedClusters/delete | Exclui um cluster gerenciado |
> | Ação | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Listar a credencial clusterAdmin de um cluster gerenciado |
> | Ação | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Listar a credencial clusterUser de um cluster gerenciado |
> | Ação | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a configuração de diagnóstico para um recurso de cluster gerenciado |
> | Ação | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico para um recurso de cluster gerenciado |
> | Ação | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/logDefinitions/read | Obtém os logs disponíveis para o cluster gerenciado |
> | Ação | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para o cluster gerenciado |
> | Ação | Microsoft.ContainerService/managedClusters/read | Obtém um cluster gerenciado |
> | Ação | Microsoft.ContainerService/managedClusters/upgradeprofiles/read | Obtém o perfil de atualização do cluster |
> | Ação | Microsoft.ContainerService/managedClusters/write | Cria um novo cluster gerenciado ou atualiza um existente |
> | Ação | Microsoft.ContainerService/operations/read | Lista as operações disponíveis no provedor de recursos Microsoft.ContainerService |
> | Ação | Microsoft.ContainerService/register/action | Registra a assinatura com o provedor de recursos Microsoft.ContainerService |
> | Ação | Microsoft.ContainerService/unregister/action | Remove o registro da assinatura com o provedor de recursos Microsoft.ContainerService |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.ContentModerator/applications/delete | Operação de Exclusão |
> | Ação | Microsoft.ContentModerator/applications/listSecrets/action | Listar segredos |
> | Ação | Microsoft.ContentModerator/applications/listSingleSignOnToken/action | Ler tokens de logon único |
> | Ação | Microsoft.ContentModerator/applications/read | Operação de Leitura |
> | Ação | Microsoft.ContentModerator/applications/write | Operação de Gravação |
> | Ação | Microsoft.ContentModerator/applications/write | Operação de Gravação |
> | Ação | Microsoft.ContentModerator/listCommunicationPreference/action | Listar preferência de comunicação |
> | Ação | Microsoft.ContentModerator/operations/read | operações de leitura |
> | Ação | Microsoft.ContentModerator/updateCommunicationPreference/action | Atualizar as preferências de comunicação |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.CostManagement/dimensions/read | Listar todas as dimensões com suporte por um escopo. |
> | Ação | Microsoft.CostManagement/query/action | Consultar os dados de uso por um escopo. |
> | Ação | Microsoft.CostManagement/query/read | Consultar os dados de uso por um escopo. |
> | Ação | Microsoft.CostManagement/reports/action | Agendar relatórios sobre dados de uso por um escopo. |
> | Ação | Microsoft.CostManagement/reports/read | Agendar relatórios sobre dados de uso por um escopo. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.CustomerInsights/hubs/adobemetadata/action | Criar ou atualizar qualquer Metadado do Adobe do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/adobemetadata/read | Ler qualquer Metadado do Adobe do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/authorizationPolicies/delete | Excluir política de assinatura de acesso compartilhado do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/authorizationPolicies/read | Ler política de assinatura de acesso compartilhado do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/authorizationPolicies/regeneratePrimaryKey/action | Regenerar a chave primária da política de Assinatura de Acesso Compartilhado do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/authorizationPolicies/regenerateSecondaryKey/action | Regenerar a chave secundária da política de Assinatura de Acesso Compartilhado do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/authorizationPolicies/write | Criar ou atualizar política de assinatura de acesso compartilhado do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/connectors/activate/action | Ativar qualquer Conector do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/connectors/activate/action | Ativar qualquer Conector do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/connectors/delete | Excluir conector do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/connectors/getruntimestatus/action | Obter qualquer status de tempo de execução do Azure Customer Insights Connector |
> | Ação | Microsoft.CustomerInsights/hubs/connectors/mappings/activate/action | Ativar qualquer Mapeamento do Conector do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/connectors/mappings/delete | Excluir mapeamento do conector do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/connectors/mappings/operations/read | Ler qualquer resultado da operação Mapeamento do Conector do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/connectors/mappings/read | Ler mapeamento do conector do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/connectors/mappings/write | Criar ou atualizar mapeamento de conector do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/connectors/operations/read | Ler qualquer resultado de operação do Conector do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/connectors/read | Ler conector do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/connectors/saveauthinfo/action | Criar ou atualizar qualquer informação de autenticação do conector do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/connectors/update/action | Atualizar qualquer conector do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/connectors/write | Criar ou atualizar conector do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/crmmetadata/action | Criar ou atualizar quaisquer metadados do CRM do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/crmmetadata/read | Ler todos os metadados do CRM do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/delete | Excluir Hub de informações do Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/gdpr/delete | Excluir qualquer GDPR do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/gdpr/read | Ler qualquer GDPR do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/gdpr/write | Criar ou atualizar qualquer GDPR do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/getbillingcredits/read | Obter créditos de cobrança do Hub do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/getbillinghistory/read | Obter histórico de cobrança do Hub do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/images/delete | Excluir qualquer imagem do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/images/read | Ler qualquer imagem do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/images/write | Criar ou atualizar qualquer imagem do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/interactions/delete | Excluir quaisquer interações do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/interactions/operations/read | Ler qualquer resultado da operação de interação do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/interactions/read | Ler interação do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/interactions/suggestrelationshiplinks/action | Sugerir links do RelationShip para qualquer interação do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/interactions/write | Criar ou atualizar interação do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/kpi/delete | Excluir indicador principal de desempenho do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/kpi/operations/read | Ler qualquer resultado da operação de Indicadores chave de desempenho do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/kpi/read | Ler indicador principal de desempenho do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/kpi/reprocess/action | Reprocessar quaisquer indicadores chave de desempenho do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/kpi/write | Criar ou atualizar indicador principal de desempenho do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/links/delete | Excluir todos os links do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/links/operations/read | Ler qualquer resultado da operação de links do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/links/read | Ler todos os links do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/links/write | Criar ou atualizar qualquer link do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/msemetadata/action | Criar ou atualizar qualquer metadado MSE do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/msemetadata/read | Ler todos os metadados MSE do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/operationresults/read | Obter resultados da operação do Hub do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/predictions/delete | Excluir quaisquer previsões do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/predictions/operations/read | Ler qualquer resultado da operação Previsões do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/predictions/read | Ler todas as previsões do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/predictions/write | Criar ou atualizar qualquer previsão do Azure Customer |
> | Ação | Microsoft.CustomerInsights/hubs/predictivematchpolicies/delete | Excluir qualquer Política de Correspondência Preditiva do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/predictivematchpolicies/operations/read | Ler qualquer resultado da operação de Política de Correspondência Preditiva do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/predictivematchpolicies/read | Ler todas as Políticas de Correspondência Preditiva do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/predictivematchpolicies/write | Criar ou atualizar qualquer Política de Correspondência Preditiva do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/profiles/delete | Excluir qualquer perfil do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/profiles/operations/read | Ler qualquer resultado da operação do Perfil do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/profiles/read | Ler perfil do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/profiles/write | Gravar qualquer perfil do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/logDefinitions/read | Obter os logs disponíveis para o recurso |
> | Ação | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/metricDefinitions/read | Obter a métrica disponível para o recurso |
> | Ação | Microsoft.CustomerInsights/hubs/read | Ler Hub de informações do Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/relationshiplinks/delete | Excluir todos os links do relacionamento do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/relationshiplinks/operations/read | Ler qualquer resultado da operação Links do Relacionamento do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/relationshiplinks/read | Ler todos os links do relacionamento do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/relationshiplinks/write | Criar ou atualizar qualquer link do relacionamento do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/relationships/delete | Excluir todos os relacionamentos do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/relationships/operations/read | Ler qualquer resultado da operação Relacionamentos do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/relationships/read | Ler todos os relacionamentos do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/relationships/write | Criar ou atualizar qualquer relacionamento do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/roleAssignments/delete | Excluir atribuição RBAC do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/roleAssignments/operations/read | Ler qualquer resultado da operação de Atribuição RBAC do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/roleAssignments/read | Ler atribuição RBAC do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/roleAssignments/write | Criar ou atualizar atribuição RBAC do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/roles/read | Ler todos os papéis RBAC do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/salesforcemetadata/action | Criar ou atualizar quaisquer metadados do Salesforce para o Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/salesforcemetadata/read | Ler todos os metadados do Salesforce do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/segments/delete | Excluir todos os segmentos do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/segments/dynamic/action | Gerenciar qualquer segmento dinâmico do Azure Customer Insight |
> | Ação | Microsoft.CustomerInsights/hubs/segments/read | Ler segmentos do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/segments/static/action | Gerenciar segmentos estáticos do Azure Customer Insight |
> | Ação | Microsoft.CustomerInsights/hubs/segments/write | Criar ou atualizar qualquer segmento do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/delete | Excluir qualquer SqlConnectionStrings do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/read | Ler qualquer SqlConnectionStrings do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/write | Criar ou atualizar qualquer SqlConnectionStrings do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/suggesttypeschema/action | Gerar Esquema de Tipo de Sugestão a partir de dados de exemplo |
> | Ação | Microsoft.CustomerInsights/hubs/tenantmanagement/read | Gerenciar qualquer configuração de hub do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/views/delete | Excluir modo de exibição de aplicativo do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/views/read | Ler modo de exibição de aplicativo do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/views/write | Criar ou atualizar modo de exibição de aplicativo do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/widgettypes/read | Ler todos os tipos de widget do aplicativo do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/write | Criar ou atualizar um Hub de informações do Customer Insights |
> | Ação | Microsoft.CustomerInsights/operations/read | Ler os Metadados da Api do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/register/action | Registrar a assinatura do provedor de recursos do Customer Insights e permitir a criação de recursos do Customer Insights |
> | Ação | Microsoft.CustomerInsights/unregister/action | Cancelar o registro da assinatura do provedor de recursos do Customer Insights |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.DataBox/jobs/bookShipmentPickUp/action | Permite agendar uma retirada para remessas de devolução. |
> | Ação | Microsoft.DataBox/jobs/cancel/action | Cancela um pedido em andamento. |
> | Ação | Microsoft.DataBox/jobs/delete | Excluir as Ordens |
> | Ação | Microsoft.DataBox/jobs/listCredentials/action | Lista as credenciais não criptografadas relacionadas ao pedido. |
> | Ação | Microsoft.DataBox/jobs/read | Liste ou obtenha as Ordens |
> | Ação | Microsoft.DataBox/jobs/write | Cria ou atualiza a Ordens |
> | Ação | Microsoft.DataBox/locations/availableSkus/action | Este método retorna a lista de SKUs disponíveis. |
> | Ação | Microsoft.DataBox/locations/validateAddress/action | Validará o endereço de entrega e fornecerá endereços alternativos, se houver algum. |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | Lista ou obtém os alertas |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | Lista ou obtém os alertas |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/delete | Exclui os cronogramas de largura de banda |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Lista ou obtém os cronogramas de largura de banda |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Lista ou obtém os cronogramas de largura de banda |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/write | Cria ou atualiza os cronogramas de largura de banda |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/delete | Exclui os dispositivos do Data Box Edge |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/action | Baixar atualizações no dispositivo |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/extendedInformation/action | Recupera informações estendidas de recurso |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/extendedInformation/write | Cria ou atualiza as informações estendidas de recurso |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/installUpdates/action | Instalar atualizações no dispositivo |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/jobs/read | Lista ou obtém os trabalhos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/networkSettings/read | Lista ou obtém as configurações de rede do dispositivo |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas de nível de dispositivo do Data Box Edge disponíveis |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Lista ou obtém os dispositivos do Data Box Edge |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Lista ou obtém os dispositivos do Data Box Edge |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Lista ou obtém os dispositivos do Data Box Edge |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/delete | Exclui as ArmApiRes_roles |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Lista ou obtém as ArmApiRes_roles |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Lista ou obtém as ArmApiRes_roles |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write | Cria ou atualiza as ArmApiRes_roles |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/action | Examinar se há atualizações |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | Atualizar as configurações de segurança |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/delete | Exclui os compartilhamentos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Lista ou obtém os compartilhamentos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Lista ou obtém os compartilhamentos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/refresh/action | ArmApiDesc_action_refresh_shares |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/write | Cria ou atualiza os compartilhamentos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/delete | Exclui as credenciais da conta de armazenamento |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Lista ou obtém as credenciais da conta de armazenamento |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Lista ou obtém as credenciais da conta de armazenamento |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/write | Cria ou atualiza as credenciais da conta de armazenamento |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateSummary/read | Lista ou obtém o resumo de atualização |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/action | Fazer upload do certificado para registro do dispositivo |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/delete | Exclui os usuários de compartilhamento |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Lista ou obtém os usuários de compartilhamento |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Lista ou obtém os usuários de compartilhamento |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/write | Cria ou atualiza os usuários de compartilhamento |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Cria ou atualiza os dispositivos do Data Box Edge |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Cria ou atualiza os dispositivos do Data Box Edge |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Databricks/workspaces/delete | Remove um workspace. |
> | Ação | Microsoft.Databricks/workspaces/read | Recupera uma lista de workspaces. |
> | Ação | Microsoft.Databricks/workspaces/write | Cria um workspace. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.DataCatalog/catalogs/delete | Excluir o catálogo. |
> | Ação | Microsoft.DataCatalog/catalogs/read | Obter propriedades dos catálogos na assinatura ou no grupo de recursos. |
> | Ação | Microsoft.DataCatalog/catalogs/write | Criar catálogo ou atualizar as marcações e propriedades do catálogo. |
> | Ação | Microsoft.DataCatalog/checkNameAvailability/action | Verificar a disponibilidade do nome do catálogo para o locatário. |
> | Ação | Microsoft.DataCatalog/operations/read | Listar operações disponíveis no provedor de recursos Microsoft.DataCatalog. |
> | Ação | Microsoft.DataCatalog/register/action | Registrar a assinatura com o provedor de recursos Microsoft.DataCatalog. |
> | Ação | Microsoft.DataCatalog/unregister/action | Cancelar a assinatura do provedor de recursos Microsoft.DataCatalog. |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.DataFactory/datafactories/activitywindows/read | Lê Janelas de Atividade no Data Factory com parâmetros especificados. |
> | Ação | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | Lê Janelas de Atividade para a Atividade do Pipeline com parâmetros especificados. |
> | Ação | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | Lê Janelas de Atividade do Pipeline com parâmetros especificados. |
> | Ação | Microsoft.DataFactory/datafactories/datapipelines/delete | Exclui qualquer Pipeline. |
> | Ação | Microsoft.DataFactory/datafactories/datapipelines/pause/action | Pausa qualquer Pipeline. |
> | Ação | Microsoft.DataFactory/datafactories/datapipelines/read | Lê qualquer Pipeline. |
> | Ação | Microsoft.DataFactory/datafactories/datapipelines/resume/action | Retoma qualquer Pipeline. |
> | Ação | Microsoft.DataFactory/datafactories/datapipelines/update/action | Atualiza qualquer Pipeline. |
> | Ação | Microsoft.DataFactory/datafactories/datapipelines/write | Cria ou atualiza qualquer Pipeline. |
> | Ação | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | Lê Janelas de Atividade para o Conjunto de Dados com parâmetros especificados. |
> | Ação | Microsoft.DataFactory/datafactories/datasets/delete | Exclui qualquer Conjunto de Dados. |
> | Ação | Microsoft.DataFactory/datafactories/datasets/read | Lê qualquer Conjunto de Dados. |
> | Ação | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | Lê a Execução da Fatia de Dados do conjunto de dados especificado com a hora de início especificada. |
> | Ação | Microsoft.DataFactory/datafactories/datasets/slices/read | Obtém as Fatias de Dados no período determinado. |
> | Ação | Microsoft.DataFactory/datafactories/datasets/slices/write | Atualiza o Status da Fatia de Dados. |
> | Ação | Microsoft.DataFactory/datafactories/datasets/write | Cria ou atualiza qualquer Conjunto de Dados. |
> | Ação | Microsoft.DataFactory/datafactories/delete | Exclui o Data Factory. |
> | Ação | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | Lê as Informações de Conexão para qualquer Gateway. |
> | Ação | Microsoft.DataFactory/datafactories/gateways/delete | Exclui qualquer Gateway. |
> | Ação | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | Lista as Chaves de Autenticação para qualquer Gateway. |
> | Ação | Microsoft.DataFactory/datafactories/gateways/read | Lê qualquer Gateway. |
> | Ação | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | Regenera as Chaves de Autenticação para qualquer Gateway. |
> | Ação | Microsoft.DataFactory/datafactories/gateways/write | Cria ou atualiza qualquer Gateway. |
> | Ação | Microsoft.DataFactory/datafactories/linkedServices/delete | Exclui qualquer Serviço Vinculado. |
> | Ação | Microsoft.DataFactory/datafactories/linkedServices/read | Lê qualquer Serviço Vinculado. |
> | Ação | Microsoft.DataFactory/datafactories/linkedServices/write | Cria ou atualiza qualquer Serviço Vinculado. |
> | Ação | Microsoft.DataFactory/datafactories/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.DataFactory/datafactories/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.DataFactory/datafactories/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para os datafactories |
> | Ação | Microsoft.DataFactory/datafactories/read | Lê o Data Factory. |
> | Ação | Microsoft.DataFactory/datafactories/runs/loginfo/read | Lê um URI de SAS para um contêiner de blob que contém os logs. |
> | Ação | Microsoft.DataFactory/datafactories/tables/delete | Exclui qualquer Conjunto de Dados. |
> | Ação | Microsoft.DataFactory/datafactories/tables/read | Lê qualquer Conjunto de Dados. |
> | Ação | Microsoft.DataFactory/datafactories/tables/write | Cria ou atualiza qualquer Conjunto de Dados. |
> | Ação | Microsoft.DataFactory/datafactories/write | Cria ou atualiza o Data Factory. |
> | Ação | Microsoft.DataFactory/factories/cancelpipelinerun/action | Cancela a execução de pipeline especificada pela ID de execução. |
> | Ação | Microsoft.DataFactory/factories/datasets/delete | Exclui qualquer Conjunto de Dados. |
> | Ação | Microsoft.DataFactory/factories/datasets/read | Lê qualquer Conjunto de Dados. |
> | Ação | Microsoft.DataFactory/factories/datasets/write | Cria ou atualiza qualquer Conjunto de Dados. |
> | Ação | Microsoft.DataFactory/factories/delete | Excluir o data factory. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/delete | Exclui qualquer Integration Runtime. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | Lê Informações de Conexão do Integration Runtime. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | Lê o Status do Integration Runtime. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | Lista as Chaves de Autenticação para qualquer Integration Runtime. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | Obtém os Dados de Monitoramento para qualquer Integration Runtime. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | Exclui o nó para o Integration Runtime especificado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | Retorna o endereço IP para o nó especificado do Integration Runtime. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/nodes/read | Lê o Nó para o Integration Runtime especificado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | Atualiza um Nó do Integration Runtime auto-hospedado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/queryactivityruns/action | Consulta execuções de atividade para o Integration Runtime especificado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/read | Lê qualquer Integration Runtime. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | Regenera as Chaves de Autenticação para o Integration Runtime especificado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/removelinks/action | Remove as Referências do Tempo de Execução de Integração do Integration Runtime especificado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/start/action | Inicia a qualquer Integration Runtime. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/stop/action | Interrompe qualquer Integration Runtime. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | Sincroniza as Credenciais para o Integration Runtime. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | Exclui o Integration Runtime especificado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/write | Cria ou atualiza qualquer Integration Runtime. |
> | Ação | Microsoft.DataFactory/factories/linkedServices/delete | Exclui o Serviço Vinculado. |
> | Ação | Microsoft.DataFactory/factories/linkedServices/read | Lê o Serviço Vinculado. |
> | Ação | Microsoft.DataFactory/factories/linkedServices/write | Cria ou atualiza o Serviço Vinculado |
> | Ação | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | Lê as execuções de atividade para a ID de execução de pipeline especificada. |
> | Ação | Microsoft.DataFactory/factories/pipelineruns/cancel/action | Cancela a execução de pipeline especificada pela ID de execução. |
> | Ação | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/action | Consulta as execuções de atividade para a ID de execução de pipeline especificada. |
> | Ação | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/read | Ler o resultado das execuções de atividade de consulta para a ID de execução do pipeline especificado. |
> | Ação | Microsoft.DataFactory/factories/pipelineruns/read | Lê as Execuções de Pipeline. |
> | Ação | Microsoft.DataFactory/factories/pipelines/createrun/action | Cria uma execução para o Pipeline. |
> | Ação | Microsoft.DataFactory/factories/pipelines/delete | Excluir o pipeline. |
> | Ação | Microsoft.DataFactory/factories/pipelines/pipelineruns/activityruns/progress/read | Obter o progresso das execuções de atividade. |
> | Ação | Microsoft.DataFactory/factories/pipelines/pipelineruns/read | Lê as Execuções de Pipeline. |
> | Ação | Microsoft.DataFactory/factories/pipelines/read | Ler pipeline. |
> | Ação | Microsoft.DataFactory/factories/pipelines/write | Criar ou atualizar pipeline |
> | Ação | Microsoft.DataFactory/factories/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.DataFactory/factories/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.DataFactory/factories/providers/Microsoft.Insights/logDefinitions/read | Obter os logs disponíveis para fábricas |
> | Ação | Microsoft.DataFactory/factories/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para fábricas |
> | Ação | Microsoft.DataFactory/factories/querypipelineruns/action | Consulta as Execuções de Pipeline. |
> | Ação | Microsoft.DataFactory/factories/querypipelineruns/read | Ler o resultado das execuções de pipeline de consulta. |
> | Ação | Microsoft.DataFactory/factories/querytriggerruns/action | Consulta as Execuções de Gatilho. |
> | Ação | Microsoft.DataFactory/factories/querytriggerruns/read | Ler o resultado das execuções de gatilho. |
> | Ação | Microsoft.DataFactory/factories/read | Ler Data Factory. |
> | Ação | Microsoft.DataFactory/factories/triggerruns/read | Lê as Execuções de Gatilho. |
> | Ação | Microsoft.DataFactory/factories/triggers/delete | Exclui qualquer Gatilho. |
> | Ação | Microsoft.DataFactory/factories/triggers/read | Lê qualquer Gatilho. |
> | Ação | Microsoft.DataFactory/factories/triggers/start/action | Inicia qualquer Gatilho. |
> | Ação | Microsoft.DataFactory/factories/triggers/stop/action | Interrompe qualquer Gatilho. |
> | Ação | Microsoft.DataFactory/factories/triggers/triggerruns/read | Lê as Execuções de Gatilho. |
> | Ação | Microsoft.DataFactory/factories/triggers/write | Cria ou atualiza qualquer Gatilho. |
> | Ação | Microsoft.DataFactory/factories/write | Criar ou atualizar data factory |
> | Ação | Microsoft.DataFactory/locations/configureFactoryRepo/action | Configura o repositório para a fábrica. |
> | Ação | Microsoft.DataFactory/operations/read | Ler todas as operações no provedor do Microsoft Azure Data Factory. |
> | Ação | Microsoft.DataFactory/register/action | Registra a assinatura para o Provedor de Recursos do Data Factory. |
> | Ação | Microsoft.DataFactory/unregister/action | Cancela o registro da assinatura para o Provedor de Recursos do Data Factory. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | Excluir uma política de computação. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | Obter informações sobre uma política de computação. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | Criar ou atualizar uma política de computação. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | Desvincular uma conta do Data Lake Store de uma conta do Data Lake Analytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | Obter informações sobre uma conta vinculada do Data Lake Store de uma conta do Data Lake Analytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | Criar ou atualizar uma conta DataLakeStore vinculada de uma conta DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/delete | Excluir uma conta DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | Excluir uma regra de firewall. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | Obter informações sobre uma regra de firewall. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | Criar ou atualizar uma regra de firewall. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/operationResults/read | Obter o resultado de uma operação de conta DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/diagnosticSettings/read | Obter as configurações de diagnóstico para a conta DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar as configurações de diagnóstico para a conta DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/logDefinitions/read | Obter os logs disponíveis para a conta DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para a conta DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/read | Obter informações sobre uma conta DataLakeAnalytics existente. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | Listar tokens SAS para contêineres de armazenamento de uma conta de armazenamento vinculada de uma conta DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | Obter contêineres de uma conta de armazenamento vinculada de uma conta DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | Desvincular uma conta de armazenamento de uma conta DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | Obter informações sobre uma conta de armazenamento vinculada de uma conta DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | Criar ou atualizar uma conta de armazenamento vinculada de uma conta DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Conceder permissões para cancelar trabalhos enviados por outros usuários. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/write | Criar ou atualizar uma conta DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/locations/capability/read | Obter informações de capacidade de uma assinatura sobre o uso de DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | Verificar a disponibilidade de um nome de conta DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/locations/operationResults/read | Obter o resultado de uma operação de conta DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/operations/read | Obter operações disponíveis do DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/register/action | Registrar a assinatura no DataLakeAnalytics. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.DataLakeStore/accounts/delete | Excluir uma conta DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/accounts/enableKeyVault/action | Habilitar o Key Vault para uma conta DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | Exclui um filtro EventGrid. |
> | Ação | Microsoft.DataLakeStore/accounts/eventGridFilters/read | Obtém um filtro EventGrid. |
> | Ação | Microsoft.DataLakeStore/accounts/eventGridFilters/write | Cria ou atualiza um Filtro EventGrid. |
> | Ação | Microsoft.DataLakeStore/accounts/firewallRules/delete | Excluir uma regra de firewall. |
> | Ação | Microsoft.DataLakeStore/accounts/firewallRules/read | Obter informações sobre uma regra de firewall. |
> | Ação | Microsoft.DataLakeStore/accounts/firewallRules/write | Criar ou atualizar uma regra de firewall. |
> | Ação | Microsoft.DataLakeStore/accounts/operationResults/read | Obter o resultado de uma operação de conta DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/diagnosticSettings/read | Obter as configurações de diagnóstico para a conta DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar as configurações de diagnóstico para a conta DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/logDefinitions/read | Obter os logs disponíveis para a conta DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para a conta DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/accounts/read | Obter informações sobre uma conta DataLakeStore existente. |
> | Ação | Microsoft.DataLakeStore/accounts/Superuser/action | Conceder Superusuário no Data Lake Store quando concedido com o Microsoft.Authorization/roleAssignments/write. |
> | Ação | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | Excluir um provedor de identidade confiável. |
> | Ação | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | Obter informações sobre um provedor de identidade confiável. |
> | Ação | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | Criar ou atualizar um provedor de identidade confiável. |
> | Ação | Microsoft.DataLakeStore/accounts/write | Criar ou atualizar uma conta DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/locations/capability/read | Obter informações de capacidade de uma assinatura sobre o uso do DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/locations/checkNameAvailability/action | Verificar a disponibilidade de um nome de conta DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/locations/operationResults/read | Obter o resultado de uma operação de conta DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/operations/read | Obter operações disponíveis de DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/register/action | Registrar assinatura para DataLakeStore. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.DataMigration/locations/operationResults/read | Obtém o status de uma operação de execução longa relacionada a uma resposta 202 Aceito |
> | Ação | Microsoft.DataMigration/locations/operationStatuses/read | Obtém o status de uma operação de execução longa relacionada a uma resposta 202 Aceito |
> | Ação | Microsoft.DataMigration/register/action | Registra a assinatura com o provedor do Serviço de Migração de Banco de Dados do Azure |
> | Ação | Microsoft.DataMigration/services/checkStatus/action | Verificar se o serviço está implantado e em execução |
> | Ação | Microsoft.DataMigration/services/delete | Exclui um recurso e todos os seus filhos |
> | Ação | Microsoft.DataMigration/services/projects/accessArtifacts/action | Gere uma URL que possa ser usada para executar GET ou PUT nos artefatos do projeto |
> | Ação | Microsoft.DataMigration/services/projects/delete | Exclui um recurso e todos os seus filhos |
> | Ação | Microsoft.DataMigration/services/projects/files/delete | Exclui um recurso e todos os seus filhos |
> | Ação | Microsoft.DataMigration/services/projects/files/read | Lê informações sobre recursos |
> | Ação | Microsoft.DataMigration/services/projects/files/read/action | Obter uma URL que pode ser usada para ler o conteúdo do arquivo |
> | Ação | Microsoft.DataMigration/services/projects/files/readWrite/action | Obter uma URL que pode ser usada para ler ou gravar o conteúdo do arquivo |
> | Ação | Microsoft.DataMigration/services/projects/files/write | Cria ou atualiza recursos e suas propriedades |
> | Ação | Microsoft.DataMigration/services/projects/read | Lê informações sobre recursos |
> | Ação | Microsoft.DataMigration/services/projects/tasks/cancel/action | Cancela a tarefa se ela estiver em execução no momento |
> | Ação | Microsoft.DataMigration/services/projects/tasks/delete | Exclui um recurso e todos os seus filhos |
> | Ação | Microsoft.DataMigration/services/projects/tasks/read | Lê informações sobre recursos |
> | Ação | Microsoft.DataMigration/services/projects/tasks/write | Executa tarefas do Serviço de Migração de Banco de Dados do Azure |
> | Ação | Microsoft.DataMigration/services/projects/write | Executa tarefas do Serviço de Migração de Banco de Dados do Azure |
> | Ação | Microsoft.DataMigration/services/read | Lê informações sobre recursos |
> | Ação | Microsoft.DataMigration/services/slots/delete | Exclui um recurso e todos os seus filhos |
> | Ação | Microsoft.DataMigration/services/slots/read | Lê informações sobre recursos |
> | Ação | Microsoft.DataMigration/services/slots/write | Cria ou atualiza recursos e suas propriedades |
> | Ação | Microsoft.DataMigration/services/start/action | Inicie o serviço do DMS para permitir que ele processe as migrações novamente |
> | Ação | Microsoft.DataMigration/services/stop/action | Pare o serviço do DMS para minimizar seu custo |
> | Ação | Microsoft.DataMigration/services/write | Cria ou atualiza recursos e suas propriedades |
> | Ação | Microsoft.DataMigration/skus/read | Obtenha uma lista de SKUs compatíveis com os recursos do DMS. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.DBforMariaDB/locations/performanceTiers/read | Retornar a lista de Níveis de Desempenho disponíveis. |
> | Ação | Microsoft.DBforMariaDB/performanceTiers/read | Retornar a lista de Níveis de Desempenho disponíveis. |
> | Ação | Microsoft.DBforMariaDB/servers/configurations/read | Retornar a lista de configurações para um servidor ou obter as propriedades para a regra de firewall especificada. |
> | Ação | Microsoft.DBforMariaDB/servers/configurations/write | Atualize o valor para a configuração especificada |
> | Ação | Microsoft.DBforMariaDB/servers/delete | Excluir um servidor existente. |
> | Ação | Microsoft.DBforMariaDB/servers/firewallRules/delete | Excluir uma regra de firewall existente. |
> | Ação | Microsoft.DBforMariaDB/servers/firewallRules/read | Retornar a lista de regras de firewall para um servidor ou obter as propriedades para a regra de firewall especificada. |
> | Ação | Microsoft.DBforMariaDB/servers/firewallRules/write | Criar uma regra de firewall com os parâmetros especificados ou atualizar uma regra existente. |
> | Ação | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/metricDefinitions/read | Retornar tipos de métricas que estão disponíveis para bancos de dados |
> | Ação | Microsoft.DBforMariaDB/servers/read | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | Ação | Microsoft.DBforMariaDB/servers/recoverableServers/read | Devolver as informações recuperáveis do MariaDB Server |
> | Ação | Microsoft.DBforMariaDB/servers/updateConfigurations/action | Atualizar as configurações para o servidor especificado |
> | Ação | Microsoft.DBforMariaDB/servers/virtualNetworkRules/delete | Excluir uma regra de rede virtual existente |
> | Ação | Microsoft.DBforMariaDB/servers/virtualNetworkRules/read | Retornar a lista de regras de rede virtual ou obter as propriedades da regra de rede virtual especificada. |
> | Ação | Microsoft.DBforMariaDB/servers/virtualNetworkRules/write | Criar uma regra de rede virtual com os parâmetros especificados ou atualizar as propriedades ou marcas para a regra de rede virtual especificada. |
> | Ação | Microsoft.DBforMariaDB/servers/write | Criar um servidor com os parâmetros especificados ou atualizar as propriedades ou marcas para o servidor especificado. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.DBforMySQL/locations/performanceTiers/read | Retornar a lista de Níveis de Desempenho disponíveis. |
> | Ação | Microsoft.DBforMySQL/performanceTiers/read | Retornar a lista de Níveis de Desempenho disponíveis. |
> | Ação | Microsoft.DBforMySQL/servers/configurations/read | Retornar a lista de configurações para um servidor ou obter as propriedades para a regra de firewall especificada. |
> | Ação | Microsoft.DBforMySQL/servers/configurations/write | Atualize o valor para a configuração especificada |
> | Ação | Microsoft.DBforMySQL/servers/delete | Excluir um servidor existente. |
> | Ação | Microsoft.DBforMySQL/servers/firewallRules/delete | Excluir uma regra de firewall existente. |
> | Ação | Microsoft.DBforMySQL/servers/firewallRules/read | Retornar a lista de regras de firewall para um servidor ou obter as propriedades para a regra de firewall especificada. |
> | Ação | Microsoft.DBforMySQL/servers/firewallRules/write | Criar uma regra de firewall com os parâmetros especificados ou atualizar uma regra existente. |
> | Ação | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/logDefinitions/read | Obtém os logs disponíveis para servidores MySQL |
> | Ação | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Retornar tipos de métricas que estão disponíveis para bancos de dados |
> | Ação | Microsoft.DBforMySQL/servers/read | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | Ação | Microsoft.DBforMySQL/servers/recoverableServers/read | Retornar as informações recuperáveis do MySQL Server |
> | Ação | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | Recuperar detalhes da política de detecção de ameaças do servidor configurada em determinado servidor |
> | Ação | Microsoft.DBforMySQL/servers/securityAlertPolicies/write | Alterar a política de detecção de ameaças do servidor para um determinado servidor |
> | Ação | Microsoft.DBforMySQL/servers/updateConfigurations/action | Atualizar as configurações para o servidor especificado |
> | Ação | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | Excluir uma regra de rede virtual existente |
> | Ação | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | Retornar a lista de regras de rede virtual ou obter as propriedades da regra de rede virtual especificada. |
> | Ação | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | Criar uma regra de rede virtual com os parâmetros especificados ou atualizar as propriedades ou marcas para a regra de rede virtual especificada. |
> | Ação | Microsoft.DBforMySQL/servers/write | Criar um servidor com os parâmetros especificados ou atualizar as propriedades ou marcas para o servidor especificado. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | Retornar a lista de Níveis de Desempenho disponíveis. |
> | Ação | Microsoft.DBforPostgreSQL/performanceTiers/read | Retornar a lista de Níveis de Desempenho disponíveis. |
> | Ação | Microsoft.DBforPostgreSQL/servers/advisors/read | Devolver a lista de assistentes |
> | Ação | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActions/read | Devolver a lista de ações recomendadas |
> | Ação | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActionSessions/action | Fazer recomendações |
> | Ação | Microsoft.DBforPostgreSQL/servers/configurations/read | Retornar a lista de configurações para um servidor ou obter as propriedades para a regra de firewall especificada. |
> | Ação | Microsoft.DBforPostgreSQL/servers/configurations/write | Atualize o valor para a configuração especificada |
> | Ação | Microsoft.DBforPostgreSQL/servers/delete | Excluir um servidor existente. |
> | Ação | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | Excluir uma regra de firewall existente. |
> | Ação | Microsoft.DBforPostgreSQL/servers/firewallRules/read | Retornar a lista de regras de firewall para um servidor ou obter as propriedades para a regra de firewall especificada. |
> | Ação | Microsoft.DBforPostgreSQL/servers/firewallRules/write | Criar uma regra de firewall com os parâmetros especificados ou atualizar uma regra existente. |
> | Ação | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | Obtém os logs disponíveis para servidores Postgres |
> | Ação | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Retornar tipos de métricas que estão disponíveis para bancos de dados |
> | Ação | Microsoft.DBforPostgreSQL/servers/queryTexts/action | Retornar o texto de uma consulta |
> | Ação | Microsoft.DBforPostgreSQL/servers/read | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | Ação | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | Retornar as informações recuperáveis do PostgreSQL Server |
> | Ação | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | Recuperar detalhes da política de detecção de ameaças do servidor configurada em determinado servidor |
> | Ação | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | Alterar a política de detecção de ameaças do servidor para um determinado servidor |
> | Ação | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | Retorne a lista de estatísticas de consulta para as principais consultas. |
> | Ação | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | Atualizar as configurações para o servidor especificado |
> | Ação | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | Excluir uma regra de rede virtual existente |
> | Ação | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | Retornar a lista de regras de rede virtual ou obter as propriedades da regra de rede virtual especificada. |
> | Ação | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | Criar uma regra de rede virtual com os parâmetros especificados ou atualizar as propriedades ou marcas para a regra de rede virtual especificada. |
> | Ação | Microsoft.DBforPostgreSQL/servers/waitStatistics/read | Devolver as estatísticas de espera para uma instância |
> | Ação | Microsoft.DBforPostgreSQL/servers/write | Criar um servidor com os parâmetros especificados ou atualizar as propriedades ou marcas para o servidor especificado. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Devices/checkNameAvailability/Action | Verificar se o nome do IotHub está disponível |
> | Ação | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Verificar se o nome do serviço de provisionamento está disponível |
> | Ação | Microsoft.Devices/ElasticPools/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Devices/ElasticPools/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | Exclui o Certificado |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | Gerar código de verificação |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | Obtém o Certificado |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/certificates/verify/Action | Verificar recurso de Certificado |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | Cria ou atualiza o Certificado |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/Delete | Excluir o recurso de locatário IotHub |
> | Ação | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | Excluir grupo de consumidores EventHub |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | Obter grupo de consumidores EventHub |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | Criar grupo de consumidores EventHub |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | Exportar dispositivos |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | Obter o recurso de estatísticas do locatário IotHub |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | Importar dispositivos |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | Obter a chave de locatário IotHub |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | Obter detalhes de trabalhos enviados em determinado IotHub |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | Obter chaves de locatário IotHub |
> | Ação | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | Obter as definições de log disponíveis para o serviço IotHub |
> | Ação | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | Obter as métricas disponíveis para o serviço IotHub |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Obter métrica de cota |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/Read | Obter o recurso de locatário IotHub |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | Testar uma mensagem com todas as rotas existentes |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | Testar uma mensagem em uma rota de teste fornecida |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Obter a integridade de todos os pontos de extremidade de roteamento para um IotHub |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/Write | Criar ou atualizar o recurso de locatário IotHub |
> | Ação | Microsoft.Devices/ElasticPools/metricDefinitions/read | Obter as métricas disponíveis para o serviço IotHub |
> | Ação | Microsoft.Devices/iotHubs/certificates/Delete | Exclui o Certificado |
> | Ação | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | Gerar código de verificação |
> | Ação | Microsoft.Devices/iotHubs/certificates/Read | Obtém o Certificado |
> | Ação | Microsoft.Devices/iotHubs/certificates/verify/Action | Verificar recurso de Certificado |
> | Ação | Microsoft.Devices/iotHubs/certificates/Write | Cria ou atualiza o Certificado |
> | Ação | Microsoft.Devices/iotHubs/Delete | Excluir recurso IotHub |
> | Ação | Microsoft.Devices/IotHubs/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Devices/IotHubs/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Excluir o filtro de Grade de Eventos |
> | Ação | Microsoft.Devices/iotHubs/eventGridFilters/Read | Obter o filtro de Grade de Eventos |
> | Ação | Microsoft.Devices/iotHubs/eventGridFilters/Write | Criar novo ou atualizar filtro de Grade de Eventos existente |
> | Ação | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | Excluir grupo de consumidores EventHub |
> | Ação | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | Obter grupo de consumidores EventHub |
> | Ação | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | Criar grupo de consumidores EventHub |
> | Ação | Microsoft.Devices/iotHubs/exportDevices/Action | Exportar dispositivos |
> | Ação | Microsoft.Devices/iotHubs/importDevices/Action | Importar dispositivos |
> | Ação | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | Obter a chave IotHub para o nome fornecido |
> | Ação | Microsoft.Devices/iotHubs/iotHubStats/Read | Obter estatísticas de IotHub |
> | Ação | Microsoft.Devices/iotHubs/jobs/Read | Obter detalhes de trabalhos enviados em determinado IotHub |
> | Ação | Microsoft.Devices/iotHubs/listkeys/Action | Obter todas as chaves IotHub |
> | Ação | Microsoft.Devices/IotHubs/logDefinitions/read | Obter as definições de log disponíveis para o serviço IotHub |
> | Ação | Microsoft.Devices/IotHubs/metricDefinitions/read | Obter as métricas disponíveis para o serviço IotHub |
> | Ação | Microsoft.Devices/iotHubs/operationresults/Read | Obtém o resultado da operação (API obsoleta) |
> | Ação | Microsoft.Devices/iotHubs/quotaMetrics/Read | Obter métrica de cota |
> | Ação | Microsoft.Devices/iotHubs/Read | Obter os recursos IotHub |
> | Ação | Microsoft.Devices/iotHubs/routing/$testall/Action | Testar uma mensagem com todas as rotas existentes |
> | Ação | Microsoft.Devices/iotHubs/routing/$testnew/Action | Testar uma mensagem em uma rota de teste fornecida |
> | Ação | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | Obter a integridade de todos os pontos de extremidade de roteamento para um IotHub |
> | Ação | Microsoft.Devices/iotHubs/skus/Read | Obter SKUs IotHub válidos |
> | Ação | Microsoft.Devices/iotHubs/Write | Criar ou atualizar recursos IotHub |
> | Ação | Microsoft.Devices/locations/operationresults/Read | Obter resultado da operação baseado na localização |
> | Ação | Microsoft.Devices/operationresults/Read | Obtém o resultado da operação |
> | Ação | Microsoft.Devices/operations/Read | Obter todas as operações de ResourceProvider |
> | Ação | Microsoft.Devices/provisioningServices/certificates/Delete | Exclui o Certificado |
> | Ação | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Gerar código de verificação |
> | Ação | Microsoft.Devices/provisioningServices/certificates/Read | Obtém o Certificado |
> | Ação | Microsoft.Devices/provisioningServices/certificates/verify/Action | Verificar recurso de Certificado |
> | Ação | Microsoft.Devices/provisioningServices/certificates/Write | Cria ou atualiza o Certificado |
> | Ação | Microsoft.Devices/provisioningServices/Delete | Excluir recurso IotDps |
> | Ação | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Devices/provisioningServices/keys/listkeys/Action | Obter Chaves IotDps para nome da chave |
> | Ação | Microsoft.Devices/provisioningServices/listkeys/Action | Obter todas as chaves IotDps |
> | Ação | Microsoft.Devices/provisioningServices/logDefinitions/read | Obter as definições de log disponíveis para o Serviço de provisionamento |
> | Ação | Microsoft.Devices/provisioningServices/metricDefinitions/read | Obter as métricas disponíveis para o serviço de provisionamento |
> | Ação | Microsoft.Devices/provisioningServices/operationresults/Read | Obtém o resultado da operação DPS |
> | Ação | Microsoft.Devices/provisioningServices/Read | Obter recurso IotDps |
> | Ação | Microsoft.Devices/provisioningServices/skus/Read | Obter SKUs IotDps válido |
> | Ação | Microsoft.Devices/provisioningServices/Write | Criar recurso IotDps |
> | Ação | Microsoft.Devices/register/action | Registrar a assinatura do provedor de recursos IotHub e permitir a criação de recursos IotHub |
> | Ação | Microsoft.Devices/register/action | Registrar a assinatura do provedor de recursos IotHub e permitir a criação de recursos IotHub |
> | Ação | Microsoft.Devices/usages/Read | Obter detalhes de uso de assinatura para esse provedor. |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.DevTestLab/labCenters/delete | Excluir centrais do laboratório. |
> | Ação | Microsoft.DevTestLab/labCenters/read | Ler centrais do laboratório. |
> | Ação | Microsoft.DevTestLab/labCenters/write | Adicionar ou modificar as centrais do laboratório. |
> | Ação | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Ler modelos do Azure Resource Manager. |
> | Ação | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | Gera um modelo do ARM para o artefato determinado, carrega os arquivos necessários para uma conta de armazenamento e valida o artefato gerado. |
> | Ação | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | Ler artefatos. |
> | Ação | Microsoft.DevTestLab/labs/artifactSources/delete | Excluir fontes de artefato. |
> | Ação | Microsoft.DevTestLab/labs/artifactSources/read | Ler fontes de artefato. |
> | Ação | Microsoft.DevTestLab/labs/artifactSources/write | Adicionar ou modificar fontes de artefato. |
> | Ação | Microsoft.DevTestLab/labs/ClaimAnyVm/action | Declarar uma máquina virtual aleatória declarável no laboratório. |
> | Ação | Microsoft.DevTestLab/labs/costs/read | Ler custos. |
> | Ação | Microsoft.DevTestLab/labs/costs/write | Adicionar ou modificar custos. |
> | Ação | Microsoft.DevTestLab/labs/CreateEnvironment/action | Criar máquinas virtuais em um laboratório. |
> | Ação | Microsoft.DevTestLab/labs/customImages/delete | Excluir imagens personalizadas. |
> | Ação | Microsoft.DevTestLab/labs/customImages/read | Ler imagens personalizadas. |
> | Ação | Microsoft.DevTestLab/labs/customImages/write | Adicionar ou modificar imagens personalizadas. |
> | Ação | Microsoft.DevTestLab/labs/delete | Excluir laboratórios. |
> | Ação | Microsoft.DevTestLab/labs/ExportResourceUsage/action | Exportar o uso de recursos de laboratório para uma conta de armazenamento |
> | Ação | Microsoft.DevTestLab/labs/formulas/delete | Excluir fórmulas. |
> | Ação | Microsoft.DevTestLab/labs/formulas/read | Ler fórmulas. |
> | Ação | Microsoft.DevTestLab/labs/formulas/write | Adicionar ou modificar fórmulas. |
> | Ação | Microsoft.DevTestLab/labs/galleryImages/read | Ler imagens da galeria. |
> | Ação | Microsoft.DevTestLab/labs/GenerateUploadUri/action | Gerar um URI para carregar imagens de disco personalizadas para um laboratório. |
> | Ação | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | Importar uma máquina virtual para um laboratório diferente. |
> | Ação | Microsoft.DevTestLab/labs/ListVhds/action | Listar imagens de disco disponíveis para a criação de imagens personalizadas. |
> | Ação | Microsoft.DevTestLab/labs/notificationChannels/delete | Excluir notificationchannels. |
> | Ação | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | Enviar notificação ao canal fornecido. |
> | Ação | Microsoft.DevTestLab/labs/notificationChannels/read | Ler notificationchannels. |
> | Ação | Microsoft.DevTestLab/labs/notificationChannels/write | Adicionar ou modificar notificationchannels. |
> | Ação | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | Avaliar a política de laboratório. |
> | Ação | Microsoft.DevTestLab/labs/policySets/policies/delete | Excluir políticas. |
> | Ação | Microsoft.DevTestLab/labs/policySets/policies/read | Ler políticas. |
> | Ação | Microsoft.DevTestLab/labs/policySets/policies/write | Adicionar ou modificar políticas. |
> | Ação | Microsoft.DevTestLab/labs/read | Ler laboratórios. |
> | Ação | Microsoft.DevTestLab/labs/schedules/delete | Excluir agendas. |
> | Ação | Microsoft.DevTestLab/labs/schedules/Execute/action | Executar uma agenda. |
> | Ação | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | Listar todas as agendas aplicáveis |
> | Ação | Microsoft.DevTestLab/labs/schedules/read | Ler agendas. |
> | Ação | Microsoft.DevTestLab/labs/schedules/write | Adicionar ou modificar agendamentos. |
> | Ação | Microsoft.DevTestLab/labs/serviceRunners/delete | Excluir executores de serviço. |
> | Ação | Microsoft.DevTestLab/labs/serviceRunners/read | Ler os executores de serviço. |
> | Ação | Microsoft.DevTestLab/labs/serviceRunners/write | Adicionar ou modificar os executores de serviço. |
> | Ação | Microsoft.DevTestLab/labs/users/delete | Excluir perfis de usuário. |
> | Ação | Microsoft.DevTestLab/labs/users/disks/Attach/action | Anexar e criar a concessão do disco para a máquina virtual. |
> | Ação | Microsoft.DevTestLab/labs/users/disks/delete | Excluir discos. |
> | Ação | Microsoft.DevTestLab/labs/users/disks/Detach/action | Desanexar e interromper a concessão do disco anexado à máquina virtual. |
> | Ação | Microsoft.DevTestLab/labs/users/disks/read | Ler discos. |
> | Ação | Microsoft.DevTestLab/labs/users/disks/write | Adicionar ou modificar discos. |
> | Ação | Microsoft.DevTestLab/labs/users/environments/delete | Excluir ambientes. |
> | Ação | Microsoft.DevTestLab/labs/users/environments/read | Ler ambientes. |
> | Ação | Microsoft.DevTestLab/labs/users/environments/write | Adicionar ou modificar ambientes. |
> | Ação | Microsoft.DevTestLab/labs/users/read | Ler perfis de usuário. |
> | Ação | Microsoft.DevTestLab/labs/users/secrets/delete | Excluir segredos. |
> | Ação | Microsoft.DevTestLab/labs/users/secrets/read | Ler segredos. |
> | Ação | Microsoft.DevTestLab/labs/users/secrets/write | Adicionar ou modificar segredos. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | Excluir malhas de serviço. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | Lista as agendas de iniciar/parar aplicáveis, se houver alguma. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/read | Ler malhas de serviço. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | Excluir agendas. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | Executar uma agenda. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | Ler agendas. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | Adicionar ou modificar agendamentos. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | Iniciar uma malha de serviço. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | Parar uma malha de serviço |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/write | Adicionar ou modificar malhas de serviço. |
> | Ação | Microsoft.DevTestLab/labs/users/write | Adicionar ou modificar perfis de usuário. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | Anexar um disco de dados novo ou existente à máquina virtual. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | Aplicar artefatos à máquina virtual. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | Assumir o controle de uma máquina virtual existente |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/delete | Excluir as máquinas virtuais. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | Desanexar o disco especificado da máquina virtual. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | Obtém uma cadeia de caracteres que representa o conteúdo do arquivo RDP para a máquina virtual |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Lista as agendas de iniciar/parar aplicáveis, se houver alguma. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/read | Ler máquinas virtuais. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | Reimplanta uma máquina virtual |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | Redimensiona a máquina virtual. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | Reiniciar uma máquina virtual. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | Excluir agendas. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | Executar uma agenda. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | Ler agendas. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | Adicionar ou modificar agendamentos. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/Start/action | Iniciar uma máquina virtual. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | Parar uma máquina virtual |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | Transferências de todos os discos de dados anexados à máquina virtual a ser possuída pelo usuário atual. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | Liberar propriedade de uma máquina virtual existente |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/write | Adicionar ou modificar máquinas virtuais. |
> | Ação | Microsoft.DevTestLab/labs/virtualNetworks/delete | Excluir redes virtuais. |
> | Ação | Microsoft.DevTestLab/labs/virtualNetworks/read | Ler redes virtuais. |
> | Ação | Microsoft.DevTestLab/labs/virtualNetworks/write | Adicionar ou modificar redes virtuais. |
> | Ação | Microsoft.DevTestLab/labs/vmPools/delete | Exclui pools de máquinas virtuais. |
> | Ação | Microsoft.DevTestLab/labs/vmPools/read | Lê pools de máquinas virtuais. |
> | Ação | Microsoft.DevTestLab/labs/vmPools/write | Adiciona ou modifica pools de máquinas virtuais. |
> | Ação | Microsoft.DevTestLab/labs/write | Adicionar ou modificar os laboratórios. |
> | Ação | Microsoft.DevTestLab/locations/operations/read | Operações de leitura. |
> | Ação | Microsoft.DevTestLab/register/action | Registra a assinatura |
> | Ação | Microsoft.DevTestLab/schedules/delete | Excluir agendas. |
> | Ação | Microsoft.DevTestLab/schedules/Execute/action | Executar uma agenda. |
> | Ação | Microsoft.DevTestLab/schedules/read | Ler agendas. |
> | Ação | Microsoft.DevTestLab/schedules/Retarget/action | Atualizar ID de recurso de destino de uma agenda. |
> | Ação | Microsoft.DevTestLab/schedules/write | Adicionar ou modificar agendamentos. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.DocumentDB/databaseAccountNames/read | Verificar a disponibilidade do nome. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | Alterar o grupo de recursos de uma conta de banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | Ler a coleção de definições de métrica. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | Ler a métrica da coleção. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | Ler métricas de nível de chave de partição da conta do banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | Ler métricas de nível de partição da conta do banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | Ler as partições da conta do banco de dados em uma coleção |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | Ler usos do nível de partição da conta do banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | Ler os usos da coleção. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | Ler as definições de métrica do banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | Ler a métrica do banco de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | Ler os usos do banco de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/delete | Excluir as contas de banco de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | Alterar as prioridades de failover das regiões de uma conta de banco de dados. Isso é usado para executar a operação de failover manual |
> | Ação | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | Obter as cadeias de caracteres de conexão para uma conta de banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/listKeys/action | Listar chaves de uma conta de banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | Ler o banco de dados de definições de métrica da conta. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/metrics/read | Ler a métrica da conta do banco de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/offlineRegion/action | Torne offline uma região de uma conta de banco de dados.  |
> | Ação | Microsoft.DocumentDB/databaseAccounts/onlineRegion/action | Torne online uma região de uma conta de banco de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/operationResults/read | Ler o status da operação assíncrona |
> | Ação | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | Ler métricas de latência |
> | Ação | Microsoft.DocumentDB/databaseAccounts/percentile/read | Lê percentis de latências de replicação |
> | Ação | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | Ler métricas de latência para uma região específica de origem e destino |
> | Ação | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | Ler métricas de latência para uma região de destino específica |
> | Ação | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/logDefinitions/read | Obter as categorias de log disponíveis para a conta do banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para a conta do banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/read | Ler uma conta de banco de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Ler as chaves somente leitura da conta do banco de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | Ler as chaves somente leitura da conta do banco de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | Girar chaves de uma conta de banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | Ler as métricas de coleção regional. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | Ler as métricas de nível de chave de partição da conta do banco de dados regional |
> | Ação | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | Lear as métricas de nível de partição da conta do banco de dados regional |
> | Ação | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | Lê as partições da conta do banco de dados regional em uma coleção |
> | Ação | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | Ler a região e as métricas da conta do banco de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/usages/read | Ler os usos da conta do banco de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/write | Atualizar uma conta de banco de dados. |
> | Ação | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | Notificar o Microsoft.DocumentDB que a VirtualNetwork ou Sub-rede está sendo excluída |
> | Ação | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | Lê o Status da operação assíncrona deleteVirtualNetworkOrSubnets |
> | Ação | Microsoft.DocumentDB/operationResults/read | Ler o status da operação assíncrona |
> | Ação | Microsoft.DocumentDB/operations/read | Operações de leitura disponíveis para o Microsoft DocumentDB  |
> | Ação | Microsoft.DocumentDB/register/action |  Registrar o provedor de recursos do Microsoft DocumentDB para a assinatura |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.DomainRegistration/checkDomainAvailability/Action | Verificar se um domínio está disponível para compra |
> | Ação | Microsoft.DomainRegistration/domains/Delete | Excluir um modo de exibição existente. |
> | Ação | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | Excluir o identificador de propriedade |
> | Ação | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Listar identificadores de propriedade |
> | Ação | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Obter identificador de propriedade |
> | Ação | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | Criar ou atualizar identificador |
> | Ação | Microsoft.DomainRegistration/domains/operationresults/Read | Obter uma operação de domínio |
> | Ação | Microsoft.DomainRegistration/domains/Read | Obter a lista de domínios |
> | Ação | Microsoft.DomainRegistration/domains/Read | Obter domínio |
> | Ação | Microsoft.DomainRegistration/domains/renew/Action | Renovar um domínio existente. |
> | Ação | Microsoft.DomainRegistration/domains/Write | Adicionar um novo domínio ou atualizar um existente |
> | Ação | Microsoft.DomainRegistration/generateSsoRequest/Action | Gerar uma solicitação para entrar no centro de controle de domínio. |
> | Ação | Microsoft.DomainRegistration/listDomainRecommendations/Action | Recuperar as recomendações de domínio da lista com base em palavras-chave |
> | Ação | Microsoft.DomainRegistration/operations/Read | Listar todas as operações do registro de domínio do serviço de aplicativo |
> | Ação | Microsoft.DomainRegistration/register/action | Registrar o provedor de recursos Microsoft Domains para a assinatura |
> | Ação | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | Listar ação de Contrato |
> | Ação | Microsoft.DomainRegistration/topLevelDomains/Read | Obter domínios toplevel |
> | Ação | Microsoft.DomainRegistration/topLevelDomains/Read | Obter domínio toplevel |
> | Ação | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | Validar o objeto de compra de domínio sem enviá-lo |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.DynamicsLcs/lcsprojects/clouddeployments/read | Exibir as implantações de avaliação do Microsoft Dynamics AX 2012 R3 em um projeto dos Serviços de Ciclo de Vida do Microsoft Dynamics que pertencem a um usuário |
> | Ação | Microsoft.DynamicsLcs/lcsprojects/clouddeployments/write | Criar as implantações de avaliação do Microsoft Dynamics AX 2012 R3 em um projeto dos Serviços de Ciclo de Vida do Microsoft Dynamics que pertencem a um usuário. As implantações podem ser gerenciadas no portal de gerenciamento do Azure |
> | Ação | Microsoft.DynamicsLcs/lcsprojects/connectors/read | Ler os conectores que pertencem a um projeto dos Serviços de Ciclo de Vida do Microsoft Dynamics |
> | Ação | Microsoft.DynamicsLcs/lcsprojects/connectors/write | Criar e atualizar os conectores que pertencem a um projeto dos Serviços de Ciclo de Vida do Microsoft Dynamics |
> | Ação | Microsoft.DynamicsLcs/lcsprojects/delete | Excluir projetos dos Serviços de Ciclo de Vida do Microsoft Dynamics que pertencem ao usuário |
> | Ação | Microsoft.DynamicsLcs/lcsprojects/read | Exibir projetos dos Serviços de Ciclo de Vida do Microsoft Dynamics que pertencem a um usuário |
> | Ação | Microsoft.DynamicsLcs/lcsprojects/write | Criar e atualizar projetos dos Serviços de Ciclo de Vida do Microsoft Dynamics que pertencem ao usuário. Somente as propriedades de nome e a descrição podem ser atualizadas. A assinatura e o local associados ao projeto não podem ser atualizados após a criação |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.EventGrid/domains/delete | Excluir um domínio |
> | Ação | Microsoft.EventGrid/domains/listKeys/action | Listar chaves para um domínio |
> | Ação | Microsoft.EventGrid/domains/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para domínios |
> | Ação | Microsoft.EventGrid/domains/read | Ler um domínio |
> | Ação | Microsoft.EventGrid/domains/regenerateKey/action | Regenerar chave para um domínio |
> | Ação | Microsoft.EventGrid/domains/topics/read | Ler um tópico de domínio |
> | Ação | Microsoft.EventGrid/domains/write | Criar ou atualizar um domínio |
> | Ação | Microsoft.EventGrid/eventSubscriptions/delete | Excluir um eventSubscription |
> | Ação | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | Obter a URL completa para a assinatura de evento |
> | Ação | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para assinaturas de eventos |
> | Ação | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para assinaturas de eventos |
> | Ação | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para eventSubscriptions |
> | Ação | Microsoft.EventGrid/eventSubscriptions/read | Ler um eventSubscription |
> | Ação | Microsoft.EventGrid/eventSubscriptions/write | Criar ou atualizar um eventSubscription |
> | Ação | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para tópicos |
> | Ação | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para tópicos |
> | Ação | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para tópicos |
> | Ação | Microsoft.EventGrid/locations/operationResults/read | Ler o resultado de uma operação de regional |
> | Ação | Microsoft.EventGrid/locations/operationsStatus/read | Ler o status de uma operação de regional |
> | Ação | Microsoft.EventGrid/operationResults/read | Ler o resultado de uma operação |
> | Ação | Microsoft.EventGrid/operationsStatus/read | Leia o status de uma operação de leitura |
> | Ação | Microsoft.EventGrid/register/action | Registra a assinatura para o provedor de recursos do EventGrid. |
> | Ação | Microsoft.EventGrid/topics/delete | Excluir um tópico |
> | Ação | Microsoft.EventGrid/topics/listKeys/action | Listar chaves para um tópico |
> | Ação | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para tópicos |
> | Ação | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para tópicos |
> | Ação | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para tópicos |
> | Ação | Microsoft.EventGrid/topics/read | Ler um tópico |
> | Ação | Microsoft.EventGrid/topics/regenerateKey/action | Regenerar chave para um tópico |
> | Ação | Microsoft.EventGrid/topics/write | Criar ou atualizar um tópico |
> | Ação | Microsoft.EventGrid/topictypes/eventtypes/read | Ler eventTypes compatíveis com um topictype |
> | Ação | Microsoft.EventGrid/topictypes/read | Ler um topictype |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.EventHub/checkNameAvailability/action | Verificar a disponibilidade do namespace em determinada assinatura. |
> | Ação | Microsoft.EventHub/checkNamespaceAvailability/action | Verificar a disponibilidade do namespace em determinada assinatura. Essa API está preterida, use CheckNameAvailabiltiy. |
> | Ação | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | Obtém a lista de Descrições de Recursos de Métrica de Cluster |
> | Ação | Microsoft.EventHub/clusters/read | Obtém a Descrição do Recurso de Cluster |
> | Ação | Microsoft.EventHub/clusters/write | Obtém a Descrição do Recurso de Cluster |
> | Ação | Microsoft.EventHub/namespaces/authorizationRules/action | Atualizar regra de autorização de namespace. Essa API está preterida. Use uma chamada PUT para atualizar a regra de autorização de namespace. Esta operação não tem suporte na versão da API 2017-04-01. |
> | Ação | Microsoft.EventHub/namespaces/authorizationRules/delete | Excluir regra de autorização de namespace. A regra de autorização do namespace padrão não pode ser excluída.  |
> | Ação | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | Obter a cadeia de conexão para o namespace |
> | Ação | Microsoft.EventHub/namespaces/authorizationRules/read | Obter a lista de descrição de regras de autorização de namespaces. |
> | Ação | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | Regenerar a chave primária ou secundária para o recurso |
> | Ação | Microsoft.EventHub/namespaces/authorizationRules/write | Criar regras de autorização no nível do namespace e atualizar suas propriedades. Os direitos de acesso das regras de autorização; as chaves primárias e secundárias podem ser atualizadas. |
> | Ação | Microsoft.EventHub/namespaces/Delete | Excluir o recurso de namespace |
> | Ação | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Obter as chaves de regras de autorização para o namespace primário de Recuperação de Desastre |
> | Ação | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | Obter regras de autorização do namespace primário de recuperação de desastre |
> | Ação | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | Desabilitar a Recuperação de desastre e interromper a replicação de alterações de namespaces primários para secundários. |
> | Ação | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Verificar a disponibilidade de alias de namespace sob determinada assinatura. |
> | Ação | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | Excluir a configuração da recuperação de desastre associada ao namespace. Essa operação somente pode ser invocada por meio do namespace primário. |
> | Ação | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | Invocar um failover de GEO DR e reconfigurar o alias de namespace para apontar para o namespace secundário. |
> | Ação | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | Obter a configuração da Recuperação de Desastre associada ao namespace. |
> | Ação | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | Criar ou atualizar a configuração de Recuperação de Desastre associada ao namespace. |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | Operação para atualizar EventHub. Esta operação não tem suporte na versão da API 2017-04-01. Regras de autorização. Use uma chamada PUT para atualizar a Regra de Autorização. |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | Operação para excluir regras de autorização EventHub |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/listkeys/action | Obter a cadeia de conexão para EventHub |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  Obter a lista de regras de autorização EventHub |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | Regenerar a chave primária ou secundária para o recurso |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | Criar regras de autorização EventHub e atualizar suas propriedades. Os Direitos de Acesso das Regras de Autorização podem ser atualizados. |
> | Ação | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | Operação para excluir o recurso ConsumerGroup |
> | Ação | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | Obter lista de descrições de recursos ConsumerGroup |
> | Ação | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | Criar ou atualizar propriedades ConsumerGroup. |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/Delete | Operação para excluir o recurso EventHub |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/read | Obter lista de descrições de recursos de EventHub |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/write | Criar ou atualizar propriedades EventHub. |
> | Ação | Microsoft.EventHub/namespaces/messagingPlan/read | Obter o Plano de Mensagens para um namespace.<br>Essa API está preterida.<br>As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso Namespace (pai) nas versões posteriores da API.<br>Esta operação não tem suporte na versão da API 2017-04-01. |
> | Ação | Microsoft.EventHub/namespaces/messagingPlan/write | Atualizar o Plano de Mensagens para um namespace.<br>Essa API está preterida.<br>As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso Namespace (pai) nas versões posteriores da API.<br>Esta operação não tem suporte na versão da API 2017-04-01. |
> | Ação | Microsoft.EventHub/namespaces/operationresults/read | Obter o status da operação de Namespace |
> | Ação | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Obter lista de descrições de recurso de configurações de diagnóstico do namespace |
> | Ação | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Obter lista de descrições de recurso de configurações de diagnóstico do namespace |
> | Ação | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | Obter lista de descrições do recurso de log do namespace |
> | Ação | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Obter lista de métrica de descrições de recurso de métrica do namespace |
> | Ação | Microsoft.EventHub/namespaces/read | Obter a lista de descrição do recurso de namespace |
> | Ação | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | Remove o namespace de ACS |
> | Ação | Microsoft.EventHub/namespaces/write | Criar um recurso de namespace e atualizar suas propriedades. Marcas e Capacidade do namespace são as propriedades que podem ser atualizadas. |
> | Ação | Microsoft.EventHub/operations/read | Obter Operações |
> | Ação | Microsoft.EventHub/register/action | Registrar a assinatura do provedor de recursos EventHub e permitir a criação de recursos EventHub |
> | Ação | Microsoft.EventHub/sku/read | Obter lista de Descrições de Recursos de SKU |
> | Ação | Microsoft.EventHub/sku/regions/read | Obter lista de Descrições de Recursos SkuRegions |
> | Ação | Microsoft.EventHub/unregister/action | Registrar o Provedor de Recursos EventHub |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Features/features/read | Obter os recursos de uma assinatura. |
> | Ação | Microsoft.Features/operations/read | Obtém a lista de operações. |
> | Ação | Microsoft.Features/providers/features/read | Obter o recurso de uma assinatura em determinado provedor de recursos. |
> | Ação | Microsoft.Features/providers/features/register/action | Registrar o recurso de uma assinatura em determinado provedor de recursos. |
> | Ação | Microsoft.Features/providers/features/unregister/action | Cancelar o registro do recurso de uma assinatura em um determinado provedor de recursos. |
> | Ação | Microsoft.Features/register/action | Registra o recurso de uma assinatura. |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | Obter atribuição de configuração de convidado. |
> | Ação | Microsoft.GuestConfiguration/guestConfigurationAssignments/write | Criar uma nova atribuição de configuração de convidado. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.HDInsight/clusters/applications/delete | Excluir o aplicativo do cluster do HDInsight |
> | Ação | Microsoft.HDInsight/clusters/applications/read | Obter aplicativo para o cluster do HDInsight |
> | Ação | Microsoft.HDInsight/clusters/applications/write | Criar ou atualizar aplicativo para o cluster do HDInsight |
> | Ação | Microsoft.HDInsight/clusters/changerdpsetting/action | Alterar configuração de RDP para Cluster HDInsight |
> | Ação | Microsoft.HDInsight/clusters/configurations/action | Atualizar a configuração do Cluster HDInsight |
> | Ação | Microsoft.HDInsight/clusters/configurations/read | Obter configurações de Cluster HDInsight |
> | Ação | Microsoft.HDInsight/clusters/delete | Excluir um cluster HDInsight |
> | Ação | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso do Cluster HDInsight |
> | Ação | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso do Cluster HDInsight |
> | Ação | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para o Cluster HDInsight |
> | Ação | Microsoft.HDInsight/clusters/read | Obter detalhes sobre o Cluster HDInsight |
> | Ação | Microsoft.HDInsight/clusters/roles/resize/action | Redimensionar um cluster HDInsight |
> | Ação | Microsoft.HDInsight/clusters/write | Criar ou atualizar o Cluster HDInsight |
> | Ação | Microsoft.HDInsight/locations/capabilities/read | Obter recursos de assinatura |
> | Ação | Microsoft.HDInsight/locations/checkNameAvailability/read | Verificar disponibilidade do nome |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.ImportExport/jobs/delete | Excluir um trabalho existente. |
> | Ação | Microsoft.ImportExport/jobs/listBitLockerKeys/action | Obter as chaves do BitLocker para o trabalho especificado. |
> | Ação | Microsoft.ImportExport/jobs/read | Obter as propriedades para o trabalho especificado ou retornar a lista de trabalhos. |
> | Ação | Microsoft.ImportExport/jobs/write | Criar um trabalho com os parâmetros especificados ou atualizar as propriedades ou marcações do trabalho especificado. |
> | Ação | Microsoft.ImportExport/locations/read | Obter as propriedades do local especificado ou retornar a lista de locais. |
> | Ação | Microsoft.ImportExport/register/action | Registra a assinatura do provedor de recursos importar/exportar e habilita a criação de trabalhos de importação/exportação. |

## <a name="microsoftinsights"></a>Microsoft.insights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Insights/ActionGroups/Delete | Excluir um grupo de ações |
> | Ação | Microsoft.Insights/ActionGroups/Read | Ler um grupo de ações |
> | Ação | Microsoft.Insights/ActionGroups/Write | Criar ou atualizar um grupo de ações |
> | Ação | Microsoft.Insights/ActivityLogAlerts/Activated/Action | Alerta do Log de Atividades ativado |
> | Ação | Microsoft.Insights/ActivityLogAlerts/Delete | Excluir um alerta do log de atividades |
> | Ação | Microsoft.Insights/ActivityLogAlerts/Read | Ler um alerta do log de atividades |
> | Ação | Microsoft.Insights/ActivityLogAlerts/Write | Criar ou atualizar um alerta do log de atividades |
> | Ação | Microsoft.Insights/AlertRules/Activated/Action | Alerta de métrica clássico ativado |
> | Ação | Microsoft.Insights/AlertRules/Delete | Excluir alerta de métrica clássico |
> | Ação | Microsoft.Insights/AlertRules/Incidents/Read | Ler incidente de alerta de métrica clássico |
> | Ação | Microsoft.Insights/AlertRules/Read | Ler alerta de métrica clássico |
> | Ação | Microsoft.Insights/AlertRules/Resolved/Action | Alerta de métrica clássico resolvido |
> | Ação | Microsoft.Insights/AlertRules/Throttled/Action | Regra de alerta de métrica clássico acelerada |
> | Ação | Microsoft.Insights/AlertRules/Write | Criar ou atualizar o alerta de métrica clássico |
> | Ação | Microsoft.Insights/AutoscaleSettings/Delete | Excluir uma configuração de dimensionamento automático |
> | Ação | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Read | Ler uma configuração de diagnóstico de recurso |
> | Ação | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Write | Criar um atualizar uma configuração de diagnóstico de recurso |
> | Ação | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/logDefinitions/Read | Ler definições de log |
> | Ação | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | Ler definições de métrica |
> | Ação | Microsoft.Insights/AutoscaleSettings/Read | Ler uma configuração de dimensionamento automático |
> | Ação | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | Redução do dimensionamento automático iniciada |
> | Ação | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | Redução do dimensionamento automático concluída |
> | Ação | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | Aumento do dimensionamento automático iniciado |
> | Ação | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | Aumento do dimensionamento automático concluído |
> | Ação | Microsoft.Insights/AutoscaleSettings/Write | Criar ou atualizar uma configuração de dimensionamento automático |
> | Ação | Microsoft.Insights/Components/AnalyticsItems/Delete | Excluindo um item de análise do Application Insights |
> | Ação | Microsoft.Insights/Components/AnalyticsItems/Read | Lendo um item de análise do Application Insights |
> | Ação | Microsoft.Insights/Components/AnalyticsItems/Write | Gravando um item de análise do Application Insights |
> | Ação | Microsoft.Insights/Components/AnalyticsTables/Action | Ação da tabela de análise do Application Insights |
> | Ação | Microsoft.Insights/Components/AnalyticsTables/Delete | Excluindo um esquema de tabela de análise do Application Insights |
> | Ação | Microsoft.Insights/Components/AnalyticsTables/Read | Lendo um esquema de tabela de análise do Application Insights |
> | Ação | Microsoft.Insights/Components/AnalyticsTables/Write | Gravando um esquema de tabela de análise do Application Insights |
> | Ação | Microsoft.Insights/Components/Annotations/Delete | Excluindo uma anotação do Application Insights |
> | Ação | Microsoft.Insights/Components/Annotations/Read | Lendo uma anotação do Application Insights |
> | Ação | Microsoft.Insights/Components/Annotations/Write | Gravação de anotação do Application Insights |
> | Ação | Microsoft.Insights/Components/Api/Read | Lendo a API de dados do componente do Application Insights |
> | Ação | Microsoft.Insights/Components/ApiKeys/Action | Gerando uma chave de API do Application Insights |
> | Ação | Microsoft.Insights/Components/ApiKeys/Delete | Excluindo uma chave de API do Application Insights |
> | Ação | Microsoft.Insights/Components/ApiKeys/Read | Lendo uma chave de API do Application Insights |
> | Ação | Microsoft.Insights/Components/BillingPlanForComponent/Read | Lendo um plano de cobrança para componente do Application Insights |
> | Ação | Microsoft.Insights/Components/CurrentBillingFeatures/Read | Lendo os recursos de cobrança atuais para componente do Application Insights |
> | Ação | Microsoft.Insights/Components/CurrentBillingFeatures/Write | Gravando recursos de cobrança atuais para componente do Application Insights |
> | Ação | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | Lendo uma configuração de integração de ALM do Application Insights |
> | Ação | Microsoft.Insights/Components/Delete | Excluindo uma configuração de componente do Application Insights |
> | Ação | Microsoft.Insights/Components/Events/Read | Obtém logs do Application Insights usando o formato de consulta OData |
> | Ação | Microsoft.Insights/Components/ExportConfiguration/Action | Ação de configurações de exportação do Application Insights |
> | Ação | Microsoft.Insights/Components/ExportConfiguration/Delete | Excluindo as configurações de exportação do Application Insights |
> | Ação | Microsoft.Insights/Components/ExportConfiguration/Read | Lendo as configurações de exportação do Application Insights |
> | Ação | Microsoft.Insights/Components/ExportConfiguration/Write | Gravando as configurações de exportação do Application Insights |
> | Ação | Microsoft.Insights/Components/ExtendQueries/Read | Lendo os resultados da consulta estendida do componente do Application Insights |
> | Ação | Microsoft.Insights/Components/Favorites/Delete | Excluindo favorito do Application Insights |
> | Ação | Microsoft.Insights/Components/Favorites/Read | Lendo um favorito do Application Insights |
> | Ação | Microsoft.Insights/Components/Favorites/Write | Gravando um favorito do Application Insights |
> | Ação | Microsoft.Insights/Components/FeatureCapabilities/Read | Lendo as capacidades do recurso do componente do Application Insights |
> | Ação | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | Lendo os recursos de cobrança disponíveis do componente do Application Insights |
> | Ação | Microsoft.Insights/Components/GetToken/Read | Lendo um token do componente do Application Insights |
> | Ação | Microsoft.Insights/Components/MetricDefinitions/Read | Lendo as definições de métrica do componente do Application Insights |
> | Ação | Microsoft.Insights/Components/Metrics/Read | Lendo as métricas do componente do Application Insights |
> | Ação | Microsoft.Insights/Components/Move/Action | Mover um componente do Application Insights para outro grupo de recursos ou assinatura |
> | Ação | Microsoft.Insights/Components/MyAnalyticsItems/Delete | Excluindo um item de análise pessoal do Application Insights |
> | Ação | Microsoft.Insights/Components/MyAnalyticsItems/Read | Lendo um item de análise pessoal do Application Insights |
> | Ação | Microsoft.Insights/Components/MyAnalyticsItems/Write | Gravando um item de análise pessoal do Application Insights |
> | Ação | Microsoft.Insights/Components/MyFavorites/Read | Lendo um favorito pessoal do Application Insights |
> | Ação | Microsoft.Insights/Components/Operations/Read | Obtém o status de operações de longa duração no Application Insights |
> | Ação | Microsoft.Insights/Components/PricingPlans/Read | Lendo um plano de preços do componente do Application Insights |
> | Ação | Microsoft.Insights/Components/PricingPlans/Write | Gravando um plano de preços do componente do Application Insights |
> | Ação | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | Lendo a configuração de detecção proativa do Application Insights |
> | Ação | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | Gravando a configuração de detecção proativa do Application Insights |
> | Ação | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | Ler definições de métrica |
> | Ação | Microsoft.Insights/Components/Purge/Action | Limpe dados do Application Insights |
> | Ação | Microsoft.Insights/Components/Query/Read | Execute consultas em logs do Application Insights |
> | Ação | Microsoft.Insights/Components/QuotaStatus/Read | Lendo o status de cota do componente do Application Insights |
> | Ação | Microsoft.Insights/Components/Read | Lendo uma configuração de componente do Application Insights |
> | Ação | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | Lendo as localizações de teste na Web do Application Insights |
> | Ação | Microsoft.Insights/Components/Webtests/Read | Lendo uma configuração de teste na Web |
> | Ação | Microsoft.Insights/Components/WorkItemConfigs/Delete | Excluindo uma configuração de integração de ALM do Application Insights |
> | Ação | Microsoft.Insights/Components/WorkItemConfigs/Read | Lendo uma configuração de integração de ALM do Application Insights |
> | Ação | Microsoft.Insights/Components/WorkItemConfigs/Write | Gravando uma configuração de integração de ALM do Application Insights |
> | Ação | Microsoft.Insights/Components/Write | Gravando em uma configuração de componente do Application Insights |
> | Ação | Microsoft.Insights/DiagnosticSettings/Delete | Excluir configuração de diagnóstico de recurso |
> | Ação | Microsoft.Insights/DiagnosticSettings/Read | Ler uma configuração de diagnóstico de recurso |
> | Ação | Microsoft.Insights/DiagnosticSettings/Write | Criar um atualizar uma configuração de diagnóstico de recurso |
> | Ação | Microsoft.Insights/EventCategories/Read | Ler categorias de evento do Log de Atividades disponíveis |
> | Ação | Microsoft.Insights/eventtypes/digestevents/Read | Ler resumo do tipo de evento de gerenciamento |
> | Ação | Microsoft.Insights/eventtypes/values/Read | Ler eventos do Log de Atividades |
> | Ação | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | Excluir uma configuração de diagnóstico do log de fluxo de rede |
> | Ação | Microsoft.Insights/ExtendedDiagnosticSettings/Read | Ler uma configuração de diagnóstico do log de fluxo de rede |
> | Ação | Microsoft.Insights/ExtendedDiagnosticSettings/Write | Criar ou atualizar uma configuração de diagnóstico do log de fluxo de rede |
> | Ação | Microsoft.Insights/ListMigrationDate/Action | Recuperar a data de migração da Assinatura |
> | Ação | Microsoft.Insights/ListMigrationDate/Read | Recuperar a data de migração da assinatura |
> | Ação | Microsoft.Insights/LogDefinitions/Read | Ler definições de log |
> | Ação | Microsoft.Insights/LogProfiles/Delete | Excluir um perfil do Log de Atividades |
> | Ação | Microsoft.Insights/LogProfiles/Read | Ler um perfil do Log de Atividades |
> | Ação | Microsoft.Insights/LogProfiles/Write | Criar ou atualizar um perfil do Log de Atividades |
> | Ação | Microsoft.Insights/Logs/ADAssessmentRecommendation/Read | Lê dados da tabela ADAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/ADReplicationResult/Read | Lê dados da tabela ADReplicationResult |
> | Ação | Microsoft.Insights/Logs/ADSecurityAssessmentRecommendation/Read | Lê dados da tabela ADSecurityAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/Alert/Read | Lê dados da tabela de Alertas |
> | Ação | Microsoft.Insights/Logs/AlertHistory/Read | Lê dados da tabela AlertHistory |
> | Ação | Microsoft.Insights/Logs/ApplicationInsights/Read | Lê dados da tabela ApplicationInsights |
> | Ação | Microsoft.Insights/Logs/AzureActivity/Read | Lê dados da tabela AzureActivity |
> | Ação | Microsoft.Insights/Logs/AzureMetrics/Read | Lê dados da tabela AzureMetrics |
> | Ação | Microsoft.Insights/Logs/BoundPort/Read | Lê dados da tabela BoundPort |
> | Ação | Microsoft.Insights/Logs/CommonSecurityLog/Read | Lê dados da tabela CommonSecurityLog |
> | Ação | Microsoft.Insights/Logs/ComputerGroup/Read | Lê dados da tabela ComputerGroup |
> | Ação | Microsoft.Insights/Logs/ConfigurationChange/Read | Lê dados da tabela ConfigurationChange |
> | Ação | Microsoft.Insights/Logs/ConfigurationData/Read | Lê dados da tabela ConfigurationData |
> | Ação | Microsoft.Insights/Logs/ContainerImageInventory/Read | Lê dados da tabela ContainerImageInventory |
> | Ação | Microsoft.Insights/Logs/ContainerInventory/Read | Lê dados da tabela ContainerInventory |
> | Ação | Microsoft.Insights/Logs/ContainerLog/Read | Lê dados da tabela ContainerLog |
> | Ação | Microsoft.Insights/Logs/ContainerServiceLog/Read | Lê dados da tabela ContainerServiceLog |
> | Ação | Microsoft.Insights/Logs/DeviceAppCrash/Read | Lê dados da tabela DeviceAppCrash |
> | Ação | Microsoft.Insights/Logs/DeviceAppLaunch/Read | Lê dados da tabela DeviceAppLaunch |
> | Ação | Microsoft.Insights/Logs/DeviceCalendar/Read | Lê dados da tabela DeviceCalendar |
> | Ação | Microsoft.Insights/Logs/DeviceCleanup/Read | Lê dados da tabela DeviceCleanup |
> | Ação | Microsoft.Insights/Logs/DeviceConnectSession/Read | Lê dados da tabela DeviceConnectSession |
> | Ação | Microsoft.Insights/Logs/DeviceEtw/Read | Lê dados da tabela DeviceEtw |
> | Ação | Microsoft.Insights/Logs/DeviceHardwareHealth/Read | Lê dados da tabela DeviceHardwareHealth |
> | Ação | Microsoft.Insights/Logs/DeviceHealth/Read | Lê dados da tabela DeviceHealth |
> | Ação | Microsoft.Insights/Logs/DeviceHeartbeat/Read | Lê dados da tabela DeviceHeartbeat |
> | Ação | Microsoft.Insights/Logs/DeviceSkypeHeartbeat/Read | Lê dados da tabela DeviceSkypeHeartbeat |
> | Ação | Microsoft.Insights/Logs/DeviceSkypeSignIn/Read | Lê dados da tabela DeviceSkypeSignIn |
> | Ação | Microsoft.Insights/Logs/DeviceSleepState/Read | Lê dados da tabela DeviceSleepState |
> | Ação | Microsoft.Insights/Logs/DHAppFailure/Read | Lê dados da tabela DHAppFailure |
> | Ação | Microsoft.Insights/Logs/DHAppReliability/Read | Lê dados da tabela DHAppReliability |
> | Ação | Microsoft.Insights/Logs/DHDriverReliability/Read | Lê dados da tabela DHDriverReliability |
> | Ação | Microsoft.Insights/Logs/DHLogonFailures/Read | Lê dados da tabela DHLogonFailures |
> | Ação | Microsoft.Insights/Logs/DHLogonMetrics/Read | Lê dados da tabela DHLogonMetrics |
> | Ação | Microsoft.Insights/Logs/DHOSCrashData/Read | Lê dados da tabela DHOSCrashData |
> | Ação | Microsoft.Insights/Logs/DHOSReliability/Read | Lê dados da tabela DHOSReliability |
> | Ação | Microsoft.Insights/Logs/DHWipAppLearning/Read | Lê dados da tabela DHWipAppLearning |
> | Ação | Microsoft.Insights/Logs/DnsEvents/Read | Lê dados da tabela DnsEvents |
> | Ação | Microsoft.Insights/Logs/DnsInventory/Read | Lê dados da tabela DnsInventory |
> | Ação | Microsoft.Insights/Logs/ETWEvent/Read | Lê dados da tabela ETWEvent |
> | Ação | Microsoft.Insights/Logs/Event/Read | Lê dados da tabela Event |
> | Ação | Microsoft.Insights/Logs/ExchangeAssessmentRecommendation/Read | Lê dados da tabela ExchangeAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | Ler os dados da tabela ExchangeOnlineAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/Heartbeat/Read | Lê dados da tabela Heartbeat |
> | Ação | Microsoft.Insights/Logs/IISAssessmentRecommendation/Read | Lê dados da tabela IISAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/InboundConnection/Read | Lê dados da tabela InboundConnection |
> | Ação | Microsoft.Insights/Logs/KubeNodeInventory/Read | Lê dados da tabela KubeNodeInventory |
> | Ação | Microsoft.Insights/Logs/KubePodInventory/Read | Lê dados da tabela KubePodInventory |
> | Ação | Microsoft.Insights/Logs/LinuxAuditLog/Read | Lê dados da tabela LinuxAuditLog |
> | Ação | Microsoft.Insights/Logs/MAApplication/Read | Lê dados da tabela MAApplication |
> | Ação | Microsoft.Insights/Logs/MAApplicationHealth/Read | Lê dados da tabela MAApplicationHealth |
> | Ação | Microsoft.Insights/Logs/MAApplicationHealthAlternativeVersions/Read | Ler dados da tabela MAApplicationHealthAlternativeVersions |
> | Ação | Microsoft.Insights/Logs/MAApplicationHealthIssues/Read | Ler dados da tabela MAApplicationHealthIssues |
> | Ação | Microsoft.Insights/Logs/MAApplicationInstance/Read | Lê dados da tabela MAApplicationInstance |
> | Ação | Microsoft.Insights/Logs/MAApplicationInstanceReadiness/Read | Ler dados da tabela MAApplicationInstanceReadiness |
> | Ação | Microsoft.Insights/Logs/MAApplicationReadiness/Read | Lê dados da tabela MAApplicationReadiness |
> | Ação | Microsoft.Insights/Logs/MADeploymentPlan/Read | Lê dados da tabela MADeploymentPlan |
> | Ação | Microsoft.Insights/Logs/MADevice/Read | Lê dados da tabela MADevice |
> | Ação | Microsoft.Insights/Logs/MADevicePnPHealth/Read | Ler dados da tabela MADevicePnPHealth |
> | Ação | Microsoft.Insights/Logs/MADevicePnPHealthAlternativeVersions/Read | Ler dados da tabela MADevicePnPHealthAlternativeVersions |
> | Ação | Microsoft.Insights/Logs/MADevicePnPHealthIssues/Read | Ler dados da tabela MADevicePnPHealthIssues |
> | Ação | Microsoft.Insights/Logs/MADeviceReadiness/Read | Lê dados da tabela MADeviceReadiness |
> | Ação | Microsoft.Insights/Logs/MADriverInstanceReadiness/Read | Ler dados da tabela MADriverInstanceReadiness |
> | Ação | Microsoft.Insights/Logs/MADriverReadiness/Read | Lê dados da tabela MADriverReadiness |
> | Ação | Microsoft.Insights/Logs/MAOfficeAddin/Read | Lê dados da tabela MAOfficeAddin |
> | Ação | Microsoft.Insights/Logs/MAOfficeAddinHealth/Read | Lê dados da tabela MAOfficeAddinHealth |
> | Ação | Microsoft.Insights/Logs/MAOfficeAddinHealthIssues/Read | Ler dados da tabela MAOfficeAddinHealthIssues |
> | Ação | Microsoft.Insights/Logs/MAOfficeAddinInstance/Read | Lê dados da tabela MAOfficeAddinInstance |
> | Ação | Microsoft.Insights/Logs/MAOfficeAddinInstanceReadiness/Read | Ler dados da tabela MAOfficeAddinInstanceReadiness |
> | Ação | Microsoft.Insights/Logs/MAOfficeAddinReadiness/Read | Lê dados da tabela MAOfficeAddinReadiness |
> | Ação | Microsoft.Insights/Logs/MAOfficeApp/Read | Lê dados da tabela MAOfficeApp |
> | Ação | Microsoft.Insights/Logs/MAOfficeAppHealth/Read | Lê dados da tabela MAOfficeAppHealth |
> | Ação | Microsoft.Insights/Logs/MAOfficeAppInstance/Read | Lê dados da tabela MAOfficeAppInstance |
> | Ação | Microsoft.Insights/Logs/MAOfficeAppReadiness/Read | Lê dados da tabela MAOfficeAppReadiness |
> | Ação | Microsoft.Insights/Logs/MAOfficeBuildInfo/Read | Lê dados da tabela MAOfficeBuildInfo |
> | Ação | Microsoft.Insights/Logs/MAOfficeCurrencyAssessment/Read | Lê dados da tabela MAOfficeCurrencyAssessment |
> | Ação | Microsoft.Insights/Logs/MAOfficeCurrencyAssessmentDailyCounts/Read | Lê dados da tabela MAOfficeCurrencyAssessmentDailyCounts |
> | Ação | Microsoft.Insights/Logs/MAOfficeDeploymentStatus/Read | Lê dados da tabela MAOfficeDeploymentStatus |
> | Ação | Microsoft.Insights/Logs/MAOfficeMacroHealth/Read | Ler dados da tabela MAOfficeMacroHealth |
> | Ação | Microsoft.Insights/Logs/MAOfficeMacroHealthIssues/Read | Ler dados da tabela MAOfficeMacroHealthIssues |
> | Ação | Microsoft.Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | Ler dados da tabela MAOfficeMacroIssueInstanceReadiness |
> | Ação | Microsoft.Insights/Logs/MAOfficeMacroIssueReadiness/Read | Lê dados da tabela MAOfficeMacroIssueReadiness |
> | Ação | Microsoft.Insights/Logs/MAOfficeMacroSummary/Read | Lê dados da tabela MAOfficeMacroSummary |
> | Ação | Microsoft.Insights/Logs/MAOfficeSuite/Read | Lê dados da tabela MAOfficeSuite |
> | Ação | Microsoft.Insights/Logs/MAOfficeSuiteInstance/Read | Lê dados da tabela MAOfficeSuiteInstance |
> | Ação | Microsoft.Insights/Logs/MAProposedPilotDevices/Read | Lê dados da tabela MAProposedPilotDevices |
> | Ação | Microsoft.Insights/Logs/MAWindowsBuildInfo/Read | Lê dados da tabela MAWindowsBuildInfo |
> | Ação | Microsoft.Insights/Logs/MAWindowsCurrencyAssessment/Read | Lê dados da tabela MAWindowsCurrencyAssessment |
> | Ação | Microsoft.Insights/Logs/MAWindowsCurrencyAssessmentDailyCounts/Read | Lê dados da tabela MAWindowsCurrencyAssessmentDailyCounts |
> | Ação | Microsoft.Insights/Logs/MAWindowsDeploymentStatus/Read | Lê dados da tabela MAWindowsDeploymentStatus |
> | Ação | Microsoft.Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | Ler dados da tabela MAWindowsSysReqInstanceReadiness |
> | Ação | Microsoft.Insights/Logs/NetworkMonitoring/Read | Lê dados da tabela NetworkMonitoring |
> | Ação | Microsoft.Insights/Logs/OfficeActivity/Read | Lê dados da tabela OfficeActivity |
> | Ação | Microsoft.Insights/Logs/Operation/Read | Lê dados da tabela Operation |
> | Ação | Microsoft.Insights/Logs/OutboundConnection/Read | Lê dados da tabela OutboundConnection |
> | Ação | Microsoft.Insights/Logs/Perf/Read | Lê dados da tabela Perf |
> | Ação | Microsoft.Insights/Logs/ProtectionStatus/Read | Lê dados da tabela ProtectionStatus |
> | Ação | Microsoft.Insights/Logs/Read | Lendo dados de todos os seus logs |
> | Ação | Microsoft.Insights/Logs/ReservedAzureCommonFields/Read | Lê dados da tabela ReservedAzureCommonFields |
> | Ação | Microsoft.Insights/Logs/ReservedCommonFields/Read | Lê dados da tabela ReservedCommonFields |
> | Ação | Microsoft.Insights/Logs/SCCMAssessmentRecommendation/Read | Lê dados da tabela SCCMAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/SCOMAssessmentRecommendation/Read | Lê dados da tabela SCOMAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/SecurityAlert/Read | Lê dados da tabela SecurityAlert |
> | Ação | Microsoft.Insights/Logs/SecurityBaseline/Read | Lê dados da tabela SecurityBaseline |
> | Ação | Microsoft.Insights/Logs/SecurityBaselineSummary/Read | Lê dados da tabela SecurityBaselineSummary |
> | Ação | Microsoft.Insights/Logs/SecurityDetection/Read | Lê dados da tabela SecurityDetection |
> | Ação | Microsoft.Insights/Logs/SecurityEvent/Read | Lê dados da tabela SecurityEvent |
> | Ação | Microsoft.Insights/Logs/ServiceFabricOperationalEvent/Read | Lê dados da tabela ServiceFabricOperationalEvent |
> | Ação | Microsoft.Insights/Logs/ServiceFabricReliableActorEvent/Read | Lê dados da tabela ServiceFabricReliableActorEvent |
> | Ação | Microsoft.Insights/Logs/ServiceFabricReliableServiceEvent/Read | Lê dados da tabela ServiceFabricReliableServiceEvent |
> | Ação | Microsoft.Insights/Logs/SfBAssessmentRecommendation/Read | Lê dados da tabela SfBAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/SfBOnlineAssessmentRecommendation/Read | Ler os dados da tabela SfBOnlineAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | Ler os dados da tabela SharePointOnlineAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/SPAssessmentRecommendation/Read | Lê dados da tabela SPAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/SQLAssessmentRecommendation/Read | Lê dados da tabela SQLAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/SQLQueryPerformance/Read | Lê dados da tabela SQLQueryPerformance |
> | Ação | Microsoft.Insights/Logs/Syslog/Read | Lê dados da tabela Syslog |
> | Ação | Microsoft.Insights/Logs/SysmonEvent/Read | Lê dados da tabela SysmonEvent |
> | Ação | Microsoft.Insights/Logs/UAApp/Read | Lê dados da tabela UAApp |
> | Ação | Microsoft.Insights/Logs/UAComputer/Read | Lê dados da tabela UAComputer |
> | Ação | Microsoft.Insights/Logs/UAComputerRank/Read | Lê dados da tabela UAComputerRank |
> | Ação | Microsoft.Insights/Logs/UADriver/Read | Lê dados da tabela UADriver |
> | Ação | Microsoft.Insights/Logs/UADriverProblemCodes/Read | Lê dados da tabela UADriverProblemCodes |
> | Ação | Microsoft.Insights/Logs/UAFeedback/Read | Lê dados da tabela UAFeedback |
> | Ação | Microsoft.Insights/Logs/UAHardwareSecurity/Read | Lê dados da tabela UAHardwareSecurity |
> | Ação | Microsoft.Insights/Logs/UAIESiteDiscovery/Read | Lê dados da tabela UAIESiteDiscovery |
> | Ação | Microsoft.Insights/Logs/UAOfficeAddIn/Read | Lê dados da tabela UAOfficeAddIn |
> | Ação | Microsoft.Insights/Logs/UAProposedActionPlan/Read | Lê dados da tabela UAProposedActionPlan |
> | Ação | Microsoft.Insights/Logs/UASysReqIssue/Read | Lê dados da tabela UASysReqIssue |
> | Ação | Microsoft.Insights/Logs/UAUpgradedComputer/Read | Lê dados da tabela UAUpgradedComputer |
> | Ação | Microsoft.Insights/Logs/Update/Read | Lê dados da tabela Update |
> | Ação | Microsoft.Insights/Logs/UpdateRunProgress/Read | Lê dados da tabela UpdateRunProgress |
> | Ação | Microsoft.Insights/Logs/UpdateSummary/Read | Lê dados da tabela UpdateSummary |
> | Ação | Microsoft.Insights/Logs/Usage/Read | Lê dados da tabela Usage |
> | Ação | Microsoft.Insights/Logs/W3CIISLog/Read | Lê dados da tabela W3CIISLog |
> | Ação | Microsoft.Insights/Logs/WaaSDeploymentStatus/Read | Lê dados da tabela WaaSDeploymentStatus |
> | Ação | Microsoft.Insights/Logs/WaaSInsiderStatus/Read | Lê dados da tabela WaaSInsiderStatus |
> | Ação | Microsoft.Insights/Logs/WaaSUpdateStatus/Read | Lê dados da tabela WaaSUpdateStatus |
> | Ação | Microsoft.Insights/Logs/WDAVStatus/Read | Lê dados da tabela WDAVStatus |
> | Ação | Microsoft.Insights/Logs/WDAVThreat/Read | Lê dados da tabela WDAVThreat |
> | Ação | Microsoft.Insights/Logs/WindowsClientAssessmentRecommendation/Read | Lê dados da tabela WindowsClientAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/WindowsFirewall/Read | Lê dados da tabela WindowsFirewall |
> | Ação | Microsoft.Insights/Logs/WindowsServerAssessmentRecommendation/Read | Lê dados da tabela WindowsServerAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/WireData/Read | Lê dados da tabela WireData |
> | Ação | Microsoft.Insights/Logs/WUDOAggregatedStatus/Read | Lê dados da tabela WUDOAggregatedStatus |
> | Ação | Microsoft.Insights/Logs/WUDOStatus/Read | Lê dados da tabela WUDOStatus |
> | Ação | Microsoft.Insights/MetricAlerts/Delete | Excluir um alerta de métrica |
> | Ação | Microsoft.Insights/MetricAlerts/Read | Ler um alerta de métrica |
> | Ação | Microsoft.Insights/MetricAlerts/Status/Read | Ler status de alerta de métrica |
> | Ação | Microsoft.Insights/MetricAlerts/Write | Criar ou atualizar um alerta de métrica |
> | Ação | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | Ler definições de métrica |
> | Ação | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | Ler definições de métrica |
> | Ação | Microsoft.Insights/MetricDefinitions/Read | Ler definições de métrica |
> | Ação | Microsoft.Insights/Metrics/providers/Metrics/Read | Ler métrica |
> | Ação | Microsoft.Insights/Metrics/Read | Ler métrica |
> | DataAction | Microsoft.Insights/Metrics/Write | Gravar métricas |
> | Ação | Microsoft.Insights/MigrateToNewpricingModel/Action | Migrar a assinatura para o novo modelo de preços |
> | Ação | Microsoft.Insights/Operations/Read | Operações de leitura |
> | Ação | Microsoft.Insights/Register/Action | Registrar o provedor do Microsoft Insights |
> | Ação | Microsoft.Insights/RollbackToLegacyPricingModel/Action | Reverter a assinatura para o modelo de preços herdado |
> | Ação | Microsoft.Insights/ScheduledQueryRules/Delete | Excluindo uma regra de consulta agendada |
> | Ação | Microsoft.Insights/ScheduledQueryRules/Read | Lendo uma regra de consulta agendada |
> | Ação | Microsoft.Insights/ScheduledQueryRules/Write | Gravando uma regra de consulta agendada |
> | Ação | Microsoft.Insights/Tenants/Register/Action | Inicializa o provedor do Microsoft Insights |
> | Ação | Microsoft.Insights/Unregister/Action | Registrar o provedor do Microsoft Insights |
> | Ação | Microsoft.Insights/Webtests/Delete | Excluindo uma configuração de teste na Web |
> | Ação | Microsoft.Insights/Webtests/GetToken/Read | Lendo um token de teste na Web |
> | Ação | Microsoft.Insights/Webtests/MetricDefinitions/Read | Lendo as definições de métrica do teste na Web |
> | Ação | Microsoft.Insights/Webtests/Metrics/Read | Lendo as métricas de um teste na Web |
> | Ação | Microsoft.Insights/Webtests/Read | Lendo uma configuração de teste na Web |
> | Ação | Microsoft.Insights/Webtests/Write | Gravando em uma configuração de teste na Web |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.IoTSpaces/Graph/delete | Exclui o recurso Microsoft.IoTSpaces Graph |
> | Ação | Microsoft.IoTSpaces/Graph/providers/Microsoft.Insights/diagnosticSettings/read | Obtém as configurações de diagnóstico para o recurso |
> | Ação | Microsoft.IoTSpaces/Graph/providers/Microsoft.Insights/diagnosticSettings/write | Define as configurações de diagnóstico para o recurso |
> | Ação | Microsoft.IoTSpaces/Graph/providers/Microsoft.Insights/logDefinitions/read | Obter as definições de log disponíveis para o serviço Microsoft.IoTSpaces |
> | Ação | Microsoft.IoTSpaces/Graph/providers/Microsoft.Insights/metricDefinitions/read | Obter as definições de métrica disponíveis para o serviço Microsoft.IoTSpaces |
> | Ação | Microsoft.IoTSpaces/Graph/read | Obtém os recursos Microsoft.IoTSpaces Graph |
> | Ação | Microsoft.IoTSpaces/Graph/write | Cria o recurso Microsoft.IoTSpaces Graph |
> | Ação | Microsoft.IoTSpaces/register/action | Registrar a assinatura para o provedor de recursos do Microsoft.IoTSpaces Graph para habilitar a criação de recursos |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.KeyVault/checkNameAvailability/read | Verificar se um nome de chave do cofre é válido e não está em uso |
> | Ação | Microsoft.KeyVault/deletedVaults/read | Exibir as propriedades de cofres de chaves com exclusão reversível |
> | Ação | Microsoft.KeyVault/hsmPools/delete | Excluir um pool de HSM |
> | Ação | Microsoft.KeyVault/hsmPools/joinVault/action | Ingressar em um cofre de chaves para um pool de HSM |
> | Ação | Microsoft.KeyVault/hsmPools/read | Exibir as propriedades de um pool de HSM |
> | Ação | Microsoft.KeyVault/hsmPools/write | Criar um novo pool de HSM de atualização das propriedades de um pool de HSM existente |
> | Ação | Microsoft.KeyVault/locations/deletedVaults/purge/action | Limpar um cofre de chaves com exclusão reversível |
> | Ação | Microsoft.KeyVault/locations/deletedVaults/read | Exibir as propriedades de um cofre de chaves com exclusão reversível |
> | Ação | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | Notifica o Microsoft.KeyVault que uma rede virtual ou sub-rede está sendo excluída |
> | Ação | Microsoft.KeyVault/locations/operationResults/read | Verificar o resultado de uma operação de longo prazo |
> | Ação | Microsoft.KeyVault/operations/read | Lista as operações disponíveis no provedor de recursos Microsoft.KeyVault |
> | Ação | Microsoft.KeyVault/register/action | Registrar uma assinatura |
> | Ação | Microsoft.KeyVault/unregister/action | Cancela uma assinatura |
> | Ação | Microsoft.KeyVault/vaults/accessPolicies/write | Atualizar uma política de acesso existente por mesclagem ou substituição, ou adicionar uma nova política de acesso a um cofre. |
> | Ação | Microsoft.KeyVault/vaults/delete | Excluir um cofre de chaves |
> | Ação | Microsoft.KeyVault/vaults/deploy/action | Permite acesso aos segredos em um cofre de chaves durante a implantação de recursos do Azure |
> | Ação | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/diagnosticSettings/Read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/diagnosticSettings/Write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/logDefinitions/read | Obtém os logs disponíveis para um cofre de chaves |
> | Ação | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para um cofre de chaves |
> | Ação | Microsoft.KeyVault/vaults/read | Exibir as propriedades de um cofre de chaves |
> | Ação | Microsoft.KeyVault/vaults/secrets/read | Exibir as propriedades de um segredo, mas não seu valor |
> | Ação | Microsoft.KeyVault/vaults/secrets/write | Criar um novo segredo ou atualizar o valor de um segredo existente |
> | Ação | Microsoft.KeyVault/vaults/write | Criar um novo cofre de chaves ou atualizar as propriedades de um cofre de chaves existente |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Kusto/Clusters/Databases/delete | Exclui um recurso de banco de dados. |
> | Ação | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | Exclui um recurso de conexões do hub de eventos. |
> | Ação | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | Lê um recurso de conexões do hub de eventos. |
> | Ação | Microsoft.Kusto/Clusters/Databases/EventHubConnections/write | Grava um recurso de conexões do hub de eventos. |
> | Ação | Microsoft.Kusto/Clusters/Databases/read | Lê um recurso de banco de dados. |
> | Ação | Microsoft.Kusto/Clusters/Databases/write | Grava um recurso de banco de dados. |
> | Ação | Microsoft.Kusto/Clusters/delete | Exclui um recurso de cluster. |
> | Ação | Microsoft.Kusto/Clusters/providers/Microsoft.Insights/diagnosticSettings/read | Obter as configurações de diagnóstico do recurso |
> | Ação | Microsoft.Kusto/Clusters/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Kusto/Clusters/providers/Microsoft.Insights/metricDefinitions/read | Obtém as definições de métricas para um recurso |
> | Ação | Microsoft.Kusto/Clusters/read | Lê um recurso de cluster. |
> | Ação | Microsoft.Kusto/Clusters/write | Grava um recurso de cluster. |
> | Ação | Microsoft.Kusto/Locations/CheckNameAvailability/write | Lê a verificação do recurso de disponibilidade |
> | Ação | Microsoft.Kusto/locations/operationresults/read | Lê os recursos de operações |
> | Ação | Microsoft.Kusto/Operations/read | Lê os recursos de operações |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.LabServices/labAccounts/CreateLab/action | Criar um laboratório em uma conta de laboratório. |
> | Ação | Microsoft.LabServices/labAccounts/delete | Excluir contas de laboratório. |
> | Ação | Microsoft.LabServices/labAccounts/galleryImages/delete | Excluir imagens da galeria. |
> | Ação | Microsoft.LabServices/labAccounts/galleryImages/read | Ler imagens da galeria. |
> | Ação | Microsoft.LabServices/labAccounts/galleryImages/write | Adicionar ou modificar imagens da Galeria. |
> | Ação | Microsoft.LabServices/labAccounts/GetRegionalAvailability/action | Obter informações sobre disponibilidade regional para cada categoria de tamanho configurado em uma conta de laboratório |
> | Ação | Microsoft.LabServices/labAccounts/labs/AddUsers/action | Adicionar usuários a um laboratório |
> | Ação | Microsoft.LabServices/labAccounts/labs/delete | Excluir laboratórios. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/ClaimAny/action | Declara um ambiente aleatório para um usuário nas configurações de um ambiente |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | Excluir configuração de ambiente. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Claim/action | Declara o ambiente e o atribui ao usuário |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | Excluir ambientes. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | Ler ambientes. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | Inicia um ambiente iniciando todos os recursos dentro do ambiente. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | Interrompe um ambiente interrompendo todos os recursos dentro do ambiente |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | Adicionar ou modificar ambientes. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | Provisionar/desprovisionar recursos necessários para uma configuração de ambiente com base no estado atual da configuração de ambiente/laboratório. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | Ler configuração de ambiente. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | Inicia um modelo iniciando todos os recursos dentro do modelo. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Inicia um modelo iniciando todos os recursos dentro do modelo. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | Adicionar ou modificar configuração de ambiente. |
> | Ação | Microsoft.LabServices/labAccounts/labs/read | Ler laboratórios. |
> | Ação | Microsoft.LabServices/labAccounts/labs/Register/action | Registra no laboratório gerenciado. |
> | Ação | Microsoft.LabServices/labAccounts/labs/users/delete | Excluir usuários. |
> | Ação | Microsoft.LabServices/labAccounts/labs/users/read | Ler usuários. |
> | Ação | Microsoft.LabServices/labAccounts/labs/users/write | Adicionar ou modificar usuários. |
> | Ação | Microsoft.LabServices/labAccounts/labs/write | Adicionar ou modificar os laboratórios. |
> | Ação | Microsoft.LabServices/labAccounts/read | Ler contas de laboratório. |
> | Ação | Microsoft.LabServices/labAccounts/write | Adicionar ou modificar contas de laboratório. |
> | Ação | Microsoft.LabServices/locations/operations/read | Operações de leitura. |
> | Ação | Microsoft.LabServices/register/action | Registra a assinatura |
> | Ação | Microsoft.LabServices/users/GetEnvironment/action | Obtém os detalhes da máquina virtual |
> | Ação | Microsoft.LabServices/users/GetOperationBatchStatus/action | Obter o status da operação de imagem |
> | Ação | Microsoft.LabServices/users/GetOperationStatus/action | Obtém o status da operação de execução longa |
> | Ação | Microsoft.LabServices/users/ListEnvironments/action | Lista Ambientes para o usuário |
> | Ação | Microsoft.LabServices/users/ListLabs/action | Lista os laboratórios para o usuário. |
> | Ação | Microsoft.LabServices/users/Register/action | Registra um usuário em um laboratório gerenciado |
> | Ação | Microsoft.LabServices/users/StartEnvironment/action | Inicia um ambiente iniciando todos os recursos dentro do ambiente. |
> | Ação | Microsoft.LabServices/users/StopEnvironment/action | Interrompe um ambiente interrompendo todos os recursos dentro do ambiente |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.LocationBasedServices/accounts/delete | (Preterido: use /providers/Microsoft.Maps) Exclui uma Conta dos Serviços Baseados na Localização. |
> | Ação | Microsoft.LocationBasedServices/accounts/listKeys/action | (Preterido: use /providers/Microsoft.Maps) Lista as chaves da Conta dos Serviços Baseados na Localização |
> | Ação | Microsoft.LocationBasedServices/accounts/providers/Microsoft.Insights/diagnosticSettings/read | (Preterido: use /providers/Microsoft.Maps) Obtém a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.LocationBasedServices/accounts/providers/Microsoft.Insights/diagnosticSettings/write | (Preterido: use /providers/Microsoft.Maps) Cria ou atualiza a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.LocationBasedServices/accounts/providers/Microsoft.Insights/metricDefinitions/read | (Preterido: use /providers/Microsoft.Maps) Obtém as métricas disponíveis para as Contas dos Serviços Baseados na Localização |
> | Ação | Microsoft.LocationBasedServices/accounts/read | (Preterido: use /providers/Microsoft.Maps) Obtém uma Conta dos Serviços Baseados na Localização. |
> | Ação | Microsoft.LocationBasedServices/accounts/regenerateKey/action | (Preterido: use /providers/Microsoft.Maps) Gera novas chaves primária e secundária da Conta dos Serviços Baseados na Localização |
> | Ação | Microsoft.LocationBasedServices/accounts/write | (Preterido: use /providers/Microsoft.Maps) Cria ou atualiza uma Conta dos Serviços Baseados na Localização. |
> | Ação | Microsoft.LocationBasedServices/register/action | (Preterido: use /providers/Microsoft.Maps) Registra o provedor |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.LocationServices/accounts/delete | (Preterido: use /providers/Microsoft.Maps) Excluir uma Conta dos Serviços de Localização. |
> | Ação | Microsoft.LocationServices/accounts/listKeys/action | (Preterido: use /providers/Microsoft.Maps) Lista as chaves da Conta dos Serviços Baseados na Localização |
> | Ação | Microsoft.LocationServices/accounts/providers/Microsoft.Insights/diagnosticSettings/read | (Preterido: use /providers/Microsoft.Maps) Obtém a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.LocationServices/accounts/providers/Microsoft.Insights/diagnosticSettings/write | (Preterido: use /providers/Microsoft.Maps) Cria ou atualiza a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.LocationServices/accounts/providers/Microsoft.Insights/metricDefinitions/read | (Preterido: use /providers/Microsoft.Maps) Obtém as métricas disponíveis para as Contas dos Serviços Baseados na Localização |
> | Ação | Microsoft.LocationServices/accounts/read | (Preterido: use /providers/Microsoft.Maps) Obter uma Conta dos Serviços de Localização. |
> | Ação | Microsoft.LocationServices/accounts/regenerateKey/action | (Preterido: use /providers/Microsoft.Maps) Gera novas chaves primária e secundária da Conta dos Serviços Baseados na Localização |
> | Ação | Microsoft.LocationServices/accounts/write | (Preterido: use /providers/Microsoft.Maps) Cria ou atualiza uma Conta dos Serviços de Localização. |
> | Ação | Microsoft.LocationServices/register/action | (Preterido: use /providers/Microsoft.Maps) Registra o provedor |

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | DataAction | Microsoft.LogAnalytics/logs/ADAssessmentRecommendation/read | Lê dados da tabela ADAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/ADReplicationResult/read | Lê dados da tabela ADReplicationResult |
> | DataAction | Microsoft.LogAnalytics/logs/ADSecurityAssessmentRecommendation/read | Lê dados da tabela ADSecurityAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/Alert/read | Lê dados da tabela de Alertas |
> | DataAction | Microsoft.LogAnalytics/logs/AlertHistory/read | Lê dados da tabela AlertHistory |
> | DataAction | Microsoft.LogAnalytics/logs/ApplicationInsights/read | Lê dados da tabela ApplicationInsights |
> | DataAction | Microsoft.LogAnalytics/logs/AuditLogs/read | Ler dados da tabela AuditLogs |
> | DataAction | Microsoft.LogAnalytics/logs/AzureActivity/read | Lê dados da tabela AzureActivity |
> | DataAction | Microsoft.LogAnalytics/logs/AzureMetrics/read | Lê dados da tabela AzureMetrics |
> | DataAction | Microsoft.LogAnalytics/logs/BoundPort/read | Lê dados da tabela BoundPort |
> | DataAction | Microsoft.LogAnalytics/logs/CommonSecurityLog/read | Lê dados da tabela CommonSecurityLog |
> | DataAction | Microsoft.LogAnalytics/logs/ComputerGroup/read | Lê dados da tabela ComputerGroup |
> | DataAction | Microsoft.LogAnalytics/logs/ConfigurationChange/read | Lê dados da tabela ConfigurationChange |
> | DataAction | Microsoft.LogAnalytics/logs/ConfigurationData/read | Lê dados da tabela ConfigurationData |
> | DataAction | Microsoft.LogAnalytics/logs/ContainerImageInventory/read | Lê dados da tabela ContainerImageInventory |
> | DataAction | Microsoft.LogAnalytics/logs/ContainerInventory/read | Lê dados da tabela ContainerInventory |
> | DataAction | Microsoft.LogAnalytics/logs/ContainerLog/read | Lê dados da tabela ContainerLog |
> | DataAction | Microsoft.LogAnalytics/logs/ContainerServiceLog/read | Lê dados da tabela ContainerServiceLog |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceAppCrash/read | Lê dados da tabela DeviceAppCrash |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceAppLaunch/read | Lê dados da tabela DeviceAppLaunch |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceCalendar/read | Lê dados da tabela DeviceCalendar |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceCleanup/read | Lê dados da tabela DeviceCleanup |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceConnectSession/read | Lê dados da tabela DeviceConnectSession |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceEtw/read | Lê dados da tabela DeviceEtw |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceHardwareHealth/read | Lê dados da tabela DeviceHardwareHealth |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceHealth/read | Lê dados da tabela DeviceHealth |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceHeartbeat/read | Lê dados da tabela DeviceHeartbeat |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceSkypeHeartbeat/read | Lê dados da tabela DeviceSkypeHeartbeat |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceSkypeSignIn/read | Lê dados da tabela DeviceSkypeSignIn |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceSleepState/read | Lê dados da tabela DeviceSleepState |
> | DataAction | Microsoft.LogAnalytics/logs/DHAppFailure/read | Lê dados da tabela DHAppFailure |
> | DataAction | Microsoft.LogAnalytics/logs/DHAppReliability/read | Lê dados da tabela DHAppReliability |
> | DataAction | Microsoft.LogAnalytics/logs/DHDriverReliability/read | Lê dados da tabela DHDriverReliability |
> | DataAction | Microsoft.LogAnalytics/logs/DHLogonFailures/read | Lê dados da tabela DHLogonFailures |
> | DataAction | Microsoft.LogAnalytics/logs/DHLogonMetrics/read | Lê dados da tabela DHLogonMetrics |
> | DataAction | Microsoft.LogAnalytics/logs/DHOSCrashData/read | Lê dados da tabela DHOSCrashData |
> | DataAction | Microsoft.LogAnalytics/logs/DHOSReliability/read | Lê dados da tabela DHOSReliability |
> | DataAction | Microsoft.LogAnalytics/logs/DHWipAppLearning/read | Lê dados da tabela DHWipAppLearning |
> | DataAction | Microsoft.LogAnalytics/logs/DnsEvents/read | Lê dados da tabela DnsEvents |
> | DataAction | Microsoft.LogAnalytics/logs/DnsInventory/read | Lê dados da tabela DnsInventory |
> | DataAction | Microsoft.LogAnalytics/logs/ETWEvent/read | Lê dados da tabela ETWEvent |
> | DataAction | Microsoft.LogAnalytics/logs/Event/read | Lê dados da tabela Event |
> | DataAction | Microsoft.LogAnalytics/logs/ExchangeAssessmentRecommendation/read | Lê dados da tabela ExchangeAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/ExchangeOnlineAssessmentRecommendation/read | Ler os dados da tabela ExchangeOnlineAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/Heartbeat/read | Lê dados da tabela Heartbeat |
> | DataAction | Microsoft.LogAnalytics/logs/IISAssessmentRecommendation/read | Lê dados da tabela IISAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/InboundConnection/read | Lê dados da tabela InboundConnection |
> | DataAction | Microsoft.LogAnalytics/logs/KubeEvents/read | Ler dados da tabela KubeEvents |
> | DataAction | Microsoft.LogAnalytics/logs/KubeNodeInventory/read | Lê dados da tabela KubeNodeInventory |
> | DataAction | Microsoft.LogAnalytics/logs/KubePodInventory/read | Lê dados da tabela KubePodInventory |
> | DataAction | Microsoft.LogAnalytics/logs/KubeServices/read | Ler dados da tabela KubeEvents |
> | DataAction | Microsoft.LogAnalytics/logs/LinuxAuditLog/read | Lê dados da tabela LinuxAuditLog |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplication/read | Lê dados da tabela MAApplication |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationHealth/read | Lê dados da tabela MAApplicationHealth |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationHealthAlternativeVersions/read | Ler dados da tabela MAApplicationHealthAlternativeVersions |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationHealthIssues/read | Ler dados da tabela MAApplicationHealthIssues |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationInstance/read | Lê dados da tabela MAApplicationInstance |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationInstanceReadiness/read | Ler dados da tabela MAApplicationInstanceReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationReadiness/read | Lê dados da tabela MAApplicationReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MADeploymentPlan/read | Lê dados da tabela MADeploymentPlan |
> | DataAction | Microsoft.LogAnalytics/logs/MADevice/read | Lê dados da tabela MADevice |
> | DataAction | Microsoft.LogAnalytics/logs/MADevicePnPHealth/read | Ler dados da tabela MADevicePnPHealth |
> | DataAction | Microsoft.LogAnalytics/logs/MADevicePnPHealthAlternativeVersions/read | Ler dados da tabela MADevicePnPHealthAlternativeVersions |
> | DataAction | Microsoft.LogAnalytics/logs/MADevicePnPHealthIssues/read | Ler dados da tabela MADevicePnPHealthIssues |
> | DataAction | Microsoft.LogAnalytics/logs/MADeviceReadiness/read | Lê dados da tabela MADeviceReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MADriverInstanceReadiness/read | Ler dados da tabela MADriverInstanceReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MADriverReadiness/read | Lê dados da tabela MADriverReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddin/read | Lê dados da tabela MAOfficeAddin |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinHealth/read | Lê dados da tabela MAOfficeAddinHealth |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinHealthIssues/read | Ler dados da tabela MAOfficeAddinHealthIssues |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinInstance/read | Lê dados da tabela MAOfficeAddinInstance |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinInstanceReadiness/read | Ler dados da tabela MAOfficeAddinInstanceReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinReadiness/read | Lê dados da tabela MAOfficeAddinReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeApp/read | Lê dados da tabela MAOfficeApp |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAppHealth/read | Lê dados da tabela MAOfficeAppHealth |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAppInstance/read | Lê dados da tabela MAOfficeAppInstance |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAppReadiness/read | Lê dados da tabela MAOfficeAppReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeBuildInfo/read | Lê dados da tabela MAOfficeBuildInfo |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeCurrencyAssessment/read | Lê dados da tabela MAOfficeCurrencyAssessment |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeCurrencyAssessmentDailyCounts/read | Lê dados da tabela MAOfficeCurrencyAssessmentDailyCounts |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeDeploymentStatus/read | Lê dados da tabela MAOfficeDeploymentStatus |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroHealth/read | Ler dados da tabela MAOfficeMacroHealth |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroHealthIssues/read | Ler dados da tabela MAOfficeMacroHealthIssues |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroIssueInstanceReadiness/read | Ler dados da tabela MAOfficeMacroIssueInstanceReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroIssueReadiness/read | Lê dados da tabela MAOfficeMacroIssueReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroSummary/read | Lê dados da tabela MAOfficeMacroSummary |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeSuite/read | Lê dados da tabela MAOfficeSuite |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeSuiteInstance/read | Lê dados da tabela MAOfficeSuiteInstance |
> | DataAction | Microsoft.LogAnalytics/logs/MAProposedPilotDevices/read | Lê dados da tabela MAProposedPilotDevices |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsBuildInfo/read | Lê dados da tabela MAWindowsBuildInfo |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsCurrencyAssessment/read | Lê dados da tabela MAWindowsCurrencyAssessment |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsCurrencyAssessmentDailyCounts/read | Lê dados da tabela MAWindowsCurrencyAssessmentDailyCounts |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsDeploymentStatus/read | Lê dados da tabela MAWindowsDeploymentStatus |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsSysReqInstanceReadiness/read | Ler dados da tabela MAWindowsSysReqInstanceReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/NetworkMonitoring/read | Lê dados da tabela NetworkMonitoring |
> | DataAction | Microsoft.LogAnalytics/logs/OfficeActivity/read | Lê dados da tabela OfficeActivity |
> | DataAction | Microsoft.LogAnalytics/logs/Operation/read | Lê dados da tabela Operation |
> | DataAction | Microsoft.LogAnalytics/logs/OutboundConnection/read | Lê dados da tabela OutboundConnection |
> | DataAction | Microsoft.LogAnalytics/logs/Perf/read | Lê dados da tabela Perf |
> | DataAction | Microsoft.LogAnalytics/logs/ProtectionStatus/read | Lê dados da tabela ProtectionStatus |
> | Ação | Microsoft.LogAnalytics/logs/read | Lendo dados de todos os seus logs |
> | DataAction | Microsoft.LogAnalytics/logs/ReservedAzureCommonFields/read | Lê dados da tabela ReservedAzureCommonFields |
> | DataAction | Microsoft.LogAnalytics/logs/ReservedCommonFields/read | Lê dados da tabela ReservedCommonFields |
> | DataAction | Microsoft.LogAnalytics/logs/SCCMAssessmentRecommendation/read | Lê dados da tabela SCCMAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/SCOMAssessmentRecommendation/read | Lê dados da tabela SCOMAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityAlert/read | Lê dados da tabela SecurityAlert |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityBaseline/read | Lê dados da tabela SecurityBaseline |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityBaselineSummary/read | Lê dados da tabela SecurityBaselineSummary |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityDetection/read | Lê dados da tabela SecurityDetection |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityEvent/read | Lê dados da tabela SecurityEvent |
> | DataAction | Microsoft.LogAnalytics/logs/ServiceFabricOperationalEvent/read | Lê dados da tabela ServiceFabricOperationalEvent |
> | DataAction | Microsoft.LogAnalytics/logs/ServiceFabricReliableActorEvent/read | Lê dados da tabela ServiceFabricReliableActorEvent |
> | DataAction | Microsoft.LogAnalytics/logs/ServiceFabricReliableServiceEvent/read | Lê dados da tabela ServiceFabricReliableServiceEvent |
> | DataAction | Microsoft.LogAnalytics/logs/SfBAssessmentRecommendation/read | Lê dados da tabela SfBAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/SfBOnlineAssessmentRecommendation/read | Ler os dados da tabela SfBOnlineAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/SharePointOnlineAssessmentRecommendation/read | Ler os dados da tabela SharePointOnlineAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/SigninLogs/read | Ler dados da tabela SigninLogs |
> | DataAction | Microsoft.LogAnalytics/logs/SPAssessmentRecommendation/read | Lê dados da tabela SPAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/SQLAssessmentRecommendation/read | Lê dados da tabela SQLAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/SQLQueryPerformance/read | Lê dados da tabela SQLQueryPerformance |
> | DataAction | Microsoft.LogAnalytics/logs/Syslog/read | Lê dados da tabela Syslog |
> | DataAction | Microsoft.LogAnalytics/logs/SysmonEvent/read | Lê dados da tabela SysmonEvent |
> | DataAction | Microsoft.LogAnalytics/logs/Tables.Custom/read | Lê dados de qualquer log personalizado |
> | DataAction | Microsoft.LogAnalytics/logs/UAApp/read | Lê dados da tabela UAApp |
> | DataAction | Microsoft.LogAnalytics/logs/UAComputer/read | Lê dados da tabela UAComputer |
> | DataAction | Microsoft.LogAnalytics/logs/UAComputerRank/read | Lê dados da tabela UAComputerRank |
> | DataAction | Microsoft.LogAnalytics/logs/UADriver/read | Lê dados da tabela UADriver |
> | DataAction | Microsoft.LogAnalytics/logs/UADriverProblemCodes/read | Lê dados da tabela UADriverProblemCodes |
> | DataAction | Microsoft.LogAnalytics/logs/UAFeedback/read | Lê dados da tabela UAFeedback |
> | DataAction | Microsoft.LogAnalytics/logs/UAHardwareSecurity/read | Lê dados da tabela UAHardwareSecurity |
> | DataAction | Microsoft.LogAnalytics/logs/UAIESiteDiscovery/read | Lê dados da tabela UAIESiteDiscovery |
> | DataAction | Microsoft.LogAnalytics/logs/UAOfficeAddIn/read | Lê dados da tabela UAOfficeAddIn |
> | DataAction | Microsoft.LogAnalytics/logs/UAProposedActionPlan/read | Lê dados da tabela UAProposedActionPlan |
> | DataAction | Microsoft.LogAnalytics/logs/UASysReqIssue/read | Lê dados da tabela UASysReqIssue |
> | DataAction | Microsoft.LogAnalytics/logs/UAUpgradedComputer/read | Lê dados da tabela UAUpgradedComputer |
> | DataAction | Microsoft.LogAnalytics/logs/Update/read | Lê dados da tabela Update |
> | DataAction | Microsoft.LogAnalytics/logs/UpdateRunProgress/read | Lê dados da tabela UpdateRunProgress |
> | DataAction | Microsoft.LogAnalytics/logs/UpdateSummary/read | Lê dados da tabela UpdateSummary |
> | DataAction | Microsoft.LogAnalytics/logs/Usage/read | Lê dados da tabela Usage |
> | DataAction | Microsoft.LogAnalytics/logs/VMBoundPort/read | Lê dados da tabela VMBoundPort |
> | DataAction | Microsoft.LogAnalytics/logs/VMConnection/read | Lê dados da tabela VMConnection |
> | DataAction | Microsoft.LogAnalytics/logs/W3CIISLog/read | Lê dados da tabela W3CIISLog |
> | DataAction | Microsoft.LogAnalytics/logs/WaaSDeploymentStatus/read | Lê dados da tabela WaaSDeploymentStatus |
> | DataAction | Microsoft.LogAnalytics/logs/WaaSInsiderStatus/read | Lê dados da tabela WaaSInsiderStatus |
> | DataAction | Microsoft.LogAnalytics/logs/WaaSUpdateStatus/read | Lê dados da tabela WaaSUpdateStatus |
> | DataAction | Microsoft.LogAnalytics/logs/WDAVStatus/read | Lê dados da tabela WDAVStatus |
> | DataAction | Microsoft.LogAnalytics/logs/WDAVThreat/read | Lê dados da tabela WDAVThreat |
> | DataAction | Microsoft.LogAnalytics/logs/WindowsClientAssessmentRecommendation/read | Lê dados da tabela WindowsClientAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/WindowsEvent/read | Ler dados da tabela WindowsEvent |
> | DataAction | Microsoft.LogAnalytics/logs/WindowsFirewall/read | Lê dados da tabela WindowsFirewall |
> | DataAction | Microsoft.LogAnalytics/logs/WindowsServerAssessmentRecommendation/read | Lê dados da tabela WindowsServerAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/WireData/read | Lê dados da tabela WireData |
> | DataAction | Microsoft.LogAnalytics/logs/WorkloadMonitoringPerf/read | Dados de leitura da tabela WorkloadMonitoringPerf |
> | DataAction | Microsoft.LogAnalytics/logs/WUDOAggregatedStatus/read | Lê dados da tabela WUDOAggregatedStatus |
> | DataAction | Microsoft.LogAnalytics/logs/WUDOStatus/read | Lê dados da tabela WUDOStatus |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Logic/integrationAccounts/agreements/delete | Excluir o contrato na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | Obtém a URL de retorno de chamada para o conteúdo do contrato na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/agreements/read | Ler o contrato na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/agreements/write | Criar ou atualizar o contrato na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/assemblies/delete | Exclui o assembly na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | Obtém a URL de retorno de chamada para o conteúdo do assembly na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/assemblies/read | Ler o assembly na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/assemblies/write | Criar ou atualizar o assembly na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | Excluir a configuração de lote na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/batchConfigurations/read | Ler a configuração de lote na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/batchConfigurations/write | Criar ou atualizar a configuração de lote na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/certificates/delete | Excluir o certificado na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/certificates/read | Ler o certificado na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/certificates/write | Criar ou atualizar o certificado na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/delete | Excluir a conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/join/action | Ingressa na Conta de Integração. |
> | Ação | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | Obtém a URL de retorno de chamada para a conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | Obter as chaves no cofre de chaves. |
> | Ação | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | Registra os eventos de acompanhamento na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/maps/delete | Excluir o mapa na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | Obtém a URL de retorno de chamada para o conteúdo do mapa na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/maps/read | Ler o mapa na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/maps/write | Criar ou atualizar o mapa na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/partners/delete | Excluir o parceiro na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | Obtém a URL de retorno de chamada para o conteúdo do parceiro na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/partners/read | Ler o parceiro na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/partners/write | Criar ou atualizar o parceiro na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | Ler as definições de log da Conta de Integração. |
> | Ação | Microsoft.Logic/integrationAccounts/read | Ler a conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | Regenera os segredos da chave de acesso. |
> | Ação | Microsoft.Logic/integrationAccounts/schemas/delete | Excluir o esquema na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | Obtém a URL de retorno de chamada para o conteúdo do esquema na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/schemas/read | Ler o esquema na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/schemas/write | Criar ou atualizar o esquema na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/sessions/delete | Excluir a sessão na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/sessions/read | Ler a configuração de lote na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/sessions/write | Criar ou atualizar a sessão na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/write | Criar ou atualizar a conta de integração. |
> | Ação | Microsoft.Logic/integrationServiceEnvironments/delete | Exclui o ambiente do serviço de integração. |
> | Ação | Microsoft.Logic/integrationServiceEnvironments/join/action | Une o Ambiente do Serviço de Integração. |
> | Ação | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | Lê a operação de API gerenciada do ambiente do serviço de integração. |
> | Ação | Microsoft.Logic/integrationServiceEnvironments/managedApis/read | Lê a API gerenciada do ambiente de serviço de integração. |
> | Ação | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | Lê as definições de métrica do ambiente de serviço de integração. |
> | Ação | Microsoft.Logic/integrationServiceEnvironments/read | Lê o ambiente do serviço de integração. |
> | Ação | Microsoft.Logic/integrationServiceEnvironments/write | Cria ou atualiza o ambiente do serviço de integração. |
> | Ação | Microsoft.Logic/locations/workflows/recommendOperationGroups/action | Obtém os grupos de operação de recomendação do fluxo de trabalho. |
> | Ação | Microsoft.Logic/locations/workflows/validate/action | Valida o fluxo de trabalho. |
> | Ação | Microsoft.Logic/operations/read | Obter a operação. |
> | Ação | Microsoft.Logic/register/action | Registrar o provedor de recursos Microsoft.Logic de uma determinada assinatura. |
> | Ação | Microsoft.Logic/workflows/accessKeys/delete | Excluir a chave de acesso. |
> | Ação | Microsoft.Logic/workflows/accessKeys/list/action | Listar os segredos de chave de acesso. |
> | Ação | Microsoft.Logic/workflows/accessKeys/read | Ler a chave de acesso. |
> | Ação | Microsoft.Logic/workflows/accessKeys/regenerate/action | Regenera os segredos da chave de acesso. |
> | Ação | Microsoft.Logic/workflows/accessKeys/write | Criar ou atualizar a chave de acesso. |
> | Ação | Microsoft.Logic/workflows/delete | Excluir o fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/disable/action | Desabilitar o fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/enable/action | Permitir o fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/listCallbackUrl/action | Obter a URL de retorno de chamada para o fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/listSwagger/action | Obter as definições do Swagger para o fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/move/action | Mover o fluxo de trabalho de sua id de assinatura, grupo de recursos e nome existente para uma id de assinatura, grupo de recursos e/ou nome diferente. |
> | Ação | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | Ler as configurações de diagnóstico de fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico de fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | Ler as definições de log de fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | Ler as definições de métrica de fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/read | Ler o fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/regenerateAccessKey/action | Regenera os segredos da chave de acesso. |
> | Ação | Microsoft.Logic/workflows/run/action | Iniciar uma execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | Obter os rastreamentos de expressão da ação de execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/actions/read | Ler a ação de execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | Obter os rastreamentos de expressão de repetição da ação de execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/actions/repetitions/read | Ler a repetição da ação de execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/actions/repetitions/requestHistories/read | Lê o histórico de solicitações da ação de repetição de execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/actions/requestHistories/read | Lê o histórico de solicitações da ação de execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | Ler a repetição do escopo da ação de execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/cancel/action | Cancelar a execução de um fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/operations/read | Ler o status da operação de execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/read | Ler a execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/suspend/action | Suspender o fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/triggers/histories/read | Ler os históricos de gatilho. |
> | Ação | Microsoft.Logic/workflows/triggers/histories/resubmit/action | Reenviar o gatilho do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | Obter a URL de retorno de chamada do gatilho. |
> | Ação | Microsoft.Logic/workflows/triggers/read | Ler o gatilho. |
> | Ação | Microsoft.Logic/workflows/triggers/reset/action | Reiniciar o gatilho. |
> | Ação | Microsoft.Logic/workflows/triggers/run/action | Executar o gatilho. |
> | Ação | Microsoft.Logic/workflows/triggers/setState/action | Definir o estado do gatilho. |
> | Ação | Microsoft.Logic/workflows/validate/action | Valida o fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/versions/read | Ler a versão do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | Obter a URL de retorno de chamada do gatilho. |
> | Ação | Microsoft.Logic/workflows/write | Criar ou atualizar o fluxo de trabalho. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | Mover associações de plano de compromisso de Machine Learning |
> | Ação | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | Ler associações de plano de compromisso de Machine Learning |
> | Ação | Microsoft.MachineLearning/commitmentPlans/delete | Excluir plano de compromisso de Machine Learning |
> | Ação | Microsoft.MachineLearning/commitmentPlans/join/action | Ingressar em um plano de compromisso de Machine Learning |
> | Ação | Microsoft.MachineLearning/commitmentPlans/read | Ler um plano de compromisso de Machine Learning |
> | Ação | Microsoft.MachineLearning/commitmentPlans/write | Criar ou atualizar plano de compromisso de Machine Learning |
> | Ação | Microsoft.MachineLearning/locations/operationresults/read | Obter o resultado de uma operação do Machine Learning |
> | Ação | Microsoft.MachineLearning/locations/operationsstatus/read | Obter o status de uma operação do Machine Learning em andamento |
> | Ação | Microsoft.MachineLearning/operations/read | Obter operações do Machine Learning |
> | Ação | Microsoft.MachineLearning/register/action | Registra a assinatura do provedor de recursos do serviço Web do Machine Learning e permite a criação de serviços Web. |
> | Ação | Microsoft.MachineLearning/skus/read | Obter SKUs do plano de compromisso do Machine Learning |
> | Ação | Microsoft.MachineLearning/webServices/action | Criar propriedades do serviço Web regionais para regiões com suporte |
> | Ação | Microsoft.MachineLearning/webServices/delete | Excluir um serviço Web do Machine Learning |
> | Ação | Microsoft.MachineLearning/webServices/listkeys/read | Obtém chaves para um Serviço Web do Machine Learning |
> | Ação | Microsoft.MachineLearning/webServices/read | Ler um serviço Web do Machine Learning |
> | Ação | Microsoft.MachineLearning/webServices/write | Criar ou atualizar serviço Web do Machine Learning |
> | Ação | Microsoft.MachineLearning/Workspaces/delete | Criar um Workspace do Machine Learning |
> | Ação | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | Listar chaves para um Workspace do Machine Learning |
> | Ação | Microsoft.MachineLearning/Workspaces/read | Ler um Workspace do Machine Learning |
> | Ação | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | Ressincronizar as chaves da conta de armazenamento configurada para um Workspace do Machine Learning |
> | Ação | Microsoft.MachineLearning/Workspaces/write | Criar ou atualizar Workspace do Machine Learning |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.MachineLearningCompute/operationalizationClusters/checkUpdate/action | Verificar se há atualizações disponíveis para serviços do sistema para o cluster de operação |
> | Ação | Microsoft.MachineLearningCompute/operationalizationClusters/delete | Excluir qualquer conta de hospedagem |
> | Ação | Microsoft.MachineLearningCompute/operationalizationClusters/listKeys/action | Listar as chaves associadas ao cluster de operação |
> | Ação | Microsoft.MachineLearningCompute/operationalizationClusters/read | Ler qualquer conta de hospedagem |
> | Ação | Microsoft.MachineLearningCompute/operationalizationClusters/updateSystem/action | Atualizar os serviços do sistema em um cluster de operação |
> | Ação | Microsoft.MachineLearningCompute/operationalizationClusters/write | Criar ou atualizar qualquer conta de hospedagem |
> | Ação | Microsoft.MachineLearningCompute/register/action | Registrar a ID de assinatura para o provedor de recursos e habilitar a criação de recursos de computação de aprendizado de máquina |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.MachineLearningModelManagement/accounts/delete | Excluir qualquer conta de hospedagem |
> | Ação | Microsoft.MachineLearningModelManagement/accounts/read | Ler qualquer conta de hospedagem |
> | Ação | Microsoft.MachineLearningModelManagement/accounts/write | Criar ou atualizar qualquer conta de hospedagem |
> | Ação | Microsoft.MachineLearningModelManagement/register/action | Registrar a ID de assinatura no provedor de recursos e habilitar a criação de uma conta de hospedagem |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.MachineLearningServices/register/action | Registra a assinatura para o Provedor de Recursos de Serviços de Machine Learning |
> | Ação | Microsoft.MachineLearningServices/workspaces/computes/delete | Exclui os recursos de computação nos Workspaces dos Serviços de Machine Learning |
> | Ação | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | Lista segredos para recursos de computação no Workspace dos Serviços de Machine Learning |
> | Ação | Microsoft.MachineLearningServices/workspaces/computes/read | Obtém os recursos de computação nos Workspaces dos Serviços de Machine Learning |
> | Ação | Microsoft.MachineLearningServices/workspaces/computes/write | Cria ou atualiza os recursos de computação nos Workspaces dos Serviços de Machine Learning |
> | Ação | Microsoft.MachineLearningServices/workspaces/delete | Exclui os Workspaces dos Serviços de Machine Learning |
> | Ação | Microsoft.MachineLearningServices/workspaces/listKeys/action | Lista segredos para um Workspace dos Serviços de Machine Learning |
> | Ação | Microsoft.MachineLearningServices/workspaces/read | Obtém os Workspaces dos Serviços de Machine Learning |
> | Ação | Microsoft.MachineLearningServices/workspaces/write | Cria ou atualiza Workspaces dos Serviços de Machine Learning |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | Ação RBAC para atribuir uma identidade atribuída ao usuário existente a um recurso |
> | Ação | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Excluir uma identidade atribuída ao usuário existente |
> | Ação | Microsoft.ManagedIdentity/userAssignedIdentities/read | Obter uma identidade atribuída ao usuário existente |
> | Ação | Microsoft.ManagedIdentity/userAssignedIdentities/write | Criar uma nova identidade atribuída ao usuário ou atualizar as marcas associadas a uma identidade atribuída a um usuário existente |

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.ManagedLab/labAccounts/CreateLab/action | Criar um laboratório em uma conta de laboratório. |
> | Ação | Microsoft.ManagedLab/labAccounts/delete | Excluir contas de laboratório. |
> | Ação | Microsoft.ManagedLab/labAccounts/labs/delete | Excluir laboratórios. |
> | Ação | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/delete | Excluir configuração de ambiente. |
> | Ação | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/delete | Excluir ambientes. |
> | Ação | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/read | Ler ambientes. |
> | Ação | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/write | Adicionar ou modificar ambientes. |
> | Ação | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/read | Ler configuração de ambiente. |
> | Ação | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/write | Adicionar ou modificar configuração de ambiente. |
> | Ação | Microsoft.ManagedLab/labAccounts/labs/labVms/delete | Excluir máquinas virtuais do laboratório. |
> | Ação | Microsoft.ManagedLab/labAccounts/labs/labVms/read | Ler máquinas virtuais do laboratório. |
> | Ação | Microsoft.ManagedLab/labAccounts/labs/labVms/write | Adicionar ou modificar máquinas virtuais do laboratório. |
> | Ação | Microsoft.ManagedLab/labAccounts/labs/read | Ler laboratórios. |
> | Ação | Microsoft.ManagedLab/labAccounts/labs/write | Adicionar ou modificar os laboratórios. |
> | Ação | Microsoft.ManagedLab/labAccounts/read | Ler contas de laboratório. |
> | Ação | Microsoft.ManagedLab/labAccounts/write | Adicionar ou modificar contas de laboratório. |
> | Ação | Microsoft.ManagedLab/locations/operations/read | Operações de leitura. |
> | Ação | Microsoft.ManagedLab/register/action | Registra a assinatura |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Management/checkNameAvailability/action | Verificar se o nome do grupo de gerenciamento especificado é válido e exclusivo. |
> | Ação | Microsoft.Management/getEntities/action | Listar todas as entidades (Grupos de Gerenciamento, Assinaturas e etc.) para o usuário autenticado. |
> | Ação | Microsoft.Management/managementGroups/delete | Excluir um grupo de gerenciamento. |
> | Ação | Microsoft.Management/managementGroups/read | Listar grupos de gerenciamento para o usuário autenticado. |
> | Ação | Microsoft.Management/managementGroups/subscriptions/delete | Desassociar a assinatura do grupo de gerenciamento. |
> | Ação | Microsoft.Management/managementGroups/subscriptions/write | Associar a assinatura existente ao grupo de gerenciamento. |
> | Ação | Microsoft.Management/managementGroups/write | Criar ou atualizar um grupo de gerenciamento. |
> | Ação | Microsoft.Management/register/action | Registra a assinatura especificada com Microsoft.Management |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | DataAction | Microsoft.Maps/accounts/data/read | Concede acesso de leitura de dados a uma conta de mapas. |
> | Ação | Microsoft.Maps/accounts/delete | Exclui uma Conta de Mapas. |
> | Ação | Microsoft.Maps/accounts/eventGridFilters/delete | Excluir um filtro de Grade de Eventos |
> | Ação | Microsoft.Maps/accounts/eventGridFilters/read | Obtenha um filtro de Grade de Eventos |
> | Ação | Microsoft.Maps/accounts/eventGridFilters/write | Cria ou atualiza qualquer Filtro de Grade de Eventos |
> | Ação | Microsoft.Maps/accounts/listKeys/action | Lista chaves da Conta de Mapas |
> | Ação | Microsoft.Maps/accounts/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Maps/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Maps/accounts/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para contas de Mapas |
> | Ação | Microsoft.Maps/accounts/read | Obtém uma Conta de Mapas. |
> | Ação | Microsoft.Maps/accounts/regenerateKey/action | Gera uma nova chave primária ou secundária da Conta de Mapas |
> | Ação | Microsoft.Maps/accounts/write | Cria ou atualiza uma Conta de Mapas. |
> | Ação | Microsoft.Maps/register/action | Registrar o provedor |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | Retorna um Contrato. |
> | Ação | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | Aceita um contrato assinado. |
> | Ação | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | Importa uma imagem para o ACR do usuário final. |
> | Ação | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | Retorna uma configuração. |
> | Ação | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | Salva uma configuração. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.MarketplaceApps/ClassicDevServices/delete | Faz uma operação DELETE em um recurso de serviço de desenvolvimento clássico. |
> | Ação | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | Obter as chaves de gerenciamento de recursos de serviço de desenvolvimento clássico. |
> | Ação | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | Obter a URL de Logon Único para um serviço de desenvolvimento clássico. |
> | Ação | Microsoft.MarketplaceApps/ClassicDevServices/read | Faz uma operação GET em um serviço de desenvolvimento clássico. |
> | Ação | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | Gerar chaves clássicas de gerenciamento de recursos do serviço de desenvolvimento. |
> | Ação | Microsoft.MarketplaceApps/Operations/read | Ler as operações para todos os tipos de recursos. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | Cancelar um contrato para um item específico do marketplace |
> | Ação | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | Retornar um contrato para um item específico do marketplace |
> | Ação | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | Assinar um contrato para um item específico do marketplace |
> | Ação | Microsoft.MarketplaceOrdering/agreements/read | Retornar todos os contratos sob determinada assinatura |
> | Ação | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | Obter um contrato para um determinado item de máquina virtual do marketplace |
> | Ação | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | Assinar ou Cancelar um contrato para um determinado item de máquina virtual do marketplace |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Media/checknameavailability/action | Verificar se um nome de conta de Serviços de Mídia está disponível |
> | Ação | Microsoft.Media/locations/checkNameAvailability/action | Verificar se um nome de conta de Serviços de Mídia está disponível |
> | Ação | Microsoft.Media/mediaservices/assets/delete | Excluir qualquer Ativo |
> | Ação | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | Obtém a Chave de Criptografia do Ativo |
> | Ação | Microsoft.Media/mediaservices/assets/listContainerSas/action | Lista URLs da SAS de Contêiner de Ativos |
> | Ação | Microsoft.Media/mediaservices/assets/read | Lê qualquer Ativo |
> | Ação | Microsoft.Media/mediaservices/assets/write | Cria ou atualiza qualquer Ativo |
> | Ação | Microsoft.Media/mediaservices/contentKeyPolicies/delete | Exclui qualquer Política de Chave de Conteúdo |
> | Ação | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | Obtém Propriedades da Política com Segredos |
> | Ação | Microsoft.Media/mediaservices/contentKeyPolicies/read | Lê qualquer Política de Chave de Conteúdo |
> | Ação | Microsoft.Media/mediaservices/contentKeyPolicies/write | Cria ou atualiza qualquer Política de Chave de Conteúdo |
> | Ação | Microsoft.Media/mediaservices/delete | Excluir qualquer Conta de Serviços de Mídia |
> | Ação | Microsoft.Media/mediaservices/eventGridFilters/delete | Exclui qualquer Filtro de Grade de Eventos |
> | Ação | Microsoft.Media/mediaservices/eventGridFilters/read | Lê qualquer Filtro de Grade de Eventos |
> | Ação | Microsoft.Media/mediaservices/eventGridFilters/write | Cria ou atualiza qualquer Filtro de Grade de Eventos |
> | Ação | Microsoft.Media/mediaservices/liveEventOperations/read | Lê qualquer Operação de Evento ao Vivo |
> | Ação | Microsoft.Media/mediaservices/liveEvents/delete | Exclui qualquer Evento ao Vivo |
> | Ação | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | Exclui qualquer Saída em Tempo Real |
> | Ação | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | Lê qualquer Saída em Tempo Real |
> | Ação | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | Cria ou atualiza qualquer Saída em Tempo Real |
> | Ação | Microsoft.Media/mediaservices/liveEvents/read | Lê qualquer Evento ao Vivo |
> | Ação | Microsoft.Media/mediaservices/liveEvents/reset/action | Redefine qualquer Operação de Evento ao Vivo |
> | Ação | Microsoft.Media/mediaservices/liveEvents/start/action | Inicia qualquer Operação de Evento ao Vivo |
> | Ação | Microsoft.Media/mediaservices/liveEvents/stop/action | Interrompe qualquer Operação de Evento ao Vivo |
> | Ação | Microsoft.Media/mediaservices/liveEvents/write | Cria ou atualiza qualquer Evento ao Vivo |
> | Ação | Microsoft.Media/mediaservices/liveOutputOperations/read | Lê qualquer Operação de Saída em Tempo Real |
> | Ação | Microsoft.Media/mediaservices/read | Ler qualquer Conta de Serviços de Mídia |
> | Ação | Microsoft.Media/mediaservices/streamingEndpointOperations/read | Lê qualquer Operação de Ponto de Extremidade de Streaming |
> | Ação | Microsoft.Media/mediaservices/streamingEndpoints/delete | Exclui qualquer Ponto de Extremidade de Streaming |
> | Ação | Microsoft.Media/mediaservices/streamingEndpoints/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso. |
> | Ação | Microsoft.Media/mediaservices/streamingEndpoints/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso. |
> | Ação | Microsoft.Media/mediaservices/streamingEndpoints/providers/Microsoft.Insights/metricDefinitions/read | Obter a lista de definições de métricas do ponto de extremidade de Streaming dos Serviços de Mídia do Microsoft Azure. |
> | Ação | Microsoft.Media/mediaservices/streamingEndpoints/read | Lê qualquer Ponto de Extremidade de Streaming |
> | Ação | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | Dimensiona qualquer Operação de Ponto de Extremidade de Streaming |
> | Ação | Microsoft.Media/mediaservices/streamingEndpoints/start/action | Inicia qualquer Operação de Ponto de Extremidade de Streaming |
> | Ação | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | Interrompe qualquer Operação de Ponto de Extremidade de Streaming |
> | Ação | Microsoft.Media/mediaservices/streamingEndpoints/write | Cria ou atualiza qualquer Ponto de Extremidade de Streaming |
> | Ação | Microsoft.Media/mediaservices/streamingLocators/delete | Exclui qualquer Localizador de Streaming |
> | Ação | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | Lista Chaves de Conteúdo |
> | Ação | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | Lista Caminhos |
> | Ação | Microsoft.Media/mediaservices/streamingLocators/read | Lê qualquer Localizador de Streaming |
> | Ação | Microsoft.Media/mediaservices/streamingLocators/write | Cria ou atualiza qualquer Localizador de Streaming |
> | Ação | Microsoft.Media/mediaservices/streamingPolicies/delete | Exclui qualquer Política de Streaming |
> | Ação | Microsoft.Media/mediaservices/streamingPolicies/read | Lê qualquer Política de Streaming |
> | Ação | Microsoft.Media/mediaservices/streamingPolicies/write | Cria ou atualiza qualquer Política de Streaming |
> | Ação | Microsoft.Media/mediaservices/syncStorageKeys/action | Sincronizar as Chaves de Armazenamento para uma conta de Armazenamento do Microsoft Azure anexada |
> | Ação | Microsoft.Media/mediaservices/transforms/delete | Exclui qualquer Transformação |
> | Ação | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | Cancelar trabalho |
> | Ação | Microsoft.Media/mediaservices/transforms/jobs/delete | Exclui qualquer Trabalho |
> | Ação | Microsoft.Media/mediaservices/transforms/jobs/read | Lê qualquer Trabalho |
> | Ação | Microsoft.Media/mediaservices/transforms/jobs/write | Cria ou atualiza qualquer Trabalho |
> | Ação | Microsoft.Media/mediaservices/transforms/read | Lê qualquer Transformação |
> | Ação | Microsoft.Media/mediaservices/transforms/write | Cria ou atualiza qualquer Transformação |
> | Ação | Microsoft.Media/mediaservices/write | Criar ou atualizar qualquer Conta de Serviços de Mídia |
> | Ação | Microsoft.Media/operations/read | Obter operações disponíveis |
> | Ação | Microsoft.Media/register/action | Registra a assinatura do provedor de recursos dos Serviços de Mídia e habilitar a criação de contas dos Serviços de Mídia |
> | Ação | Microsoft.Media/unregister/action | Cancela o registro da assinatura para o provedor de recursos do Serviços de Mídia |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Migrate/locations/assessmentOptions/read | Obtém as opções de avaliação disponíveis no local fornecido |
> | Ação | Microsoft.Migrate/locations/checknameavailability/action | Verifica a disponibilidade do nome do recurso para a assinatura fornecida no local determinado |
> | Ação | Microsoft.Migrate/Operations/read | Lista as operações disponíveis no provedor de recursos Microsoft.Migrate |
> | Ação | Microsoft.Migrate/projects/assessments/read | Lista avaliações dentro de um projeto |
> | Ação | Microsoft.Migrate/projects/delete | Exclui o projeto |
> | Ação | Microsoft.Migrate/projects/groups/assessments/assessedmachines/read | Obter as propriedades de um computador avaliado |
> | Ação | Microsoft.Migrate/projects/groups/assessments/delete | Exclui uma avaliação |
> | Ação | Microsoft.Migrate/projects/groups/assessments/downloadurl/action | Baixa uma URL do relatório de avaliação |
> | Ação | Microsoft.Migrate/projects/groups/assessments/read | Obtém as propriedades de uma avaliação |
> | Ação | Microsoft.Migrate/projects/groups/assessments/write | Cria uma avaliação ou atualiza uma existente |
> | Ação | Microsoft.Migrate/projects/groups/delete | Exclui um grupo |
> | Ação | Microsoft.Migrate/projects/groups/read | Obter as propriedades de um grupo |
> | Ação | Microsoft.Migrate/projects/groups/write | Cria um novo grupo ou atualiza um grupo existente |
> | Ação | Microsoft.Migrate/projects/keys/action | Obtém as chaves compartilhadas para o projeto |
> | Ação | Microsoft.Migrate/projects/machines/read | Obtém as propriedades de um computador |
> | Ação | Microsoft.Migrate/projects/read | Obtém as propriedades de um projeto |
> | Ação | Microsoft.Migrate/projects/write | Cria um projeto ou atualiza um existente |
> | Ação | Microsoft.Migrate/register/action | Registra a Assinatura com o provedor de recursos Microsoft.Migrate |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.NetApp/locations/operationresults/read | Lê um recurso de resultado da operação. |
> | Ação | Microsoft.NetApp/locations/read | Lê um recurso de verificação de disponibilidade. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/delete | Exclui um recurso de pool. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/providers/Microsoft.Insights/diagnosticSettings/read | Exclui um recurso de pool. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/providers/Microsoft.Insights/diagnosticSettings/write | Exclui um recurso de pool. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/providers/Microsoft.Insights/metricDefinitions/read | Exclui um recurso de pool. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/read | Lê um recurso de pool. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/delete | Exclui um recurso de volume. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/MountTargets/delete | Exclui um recurso de destino de montagem. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/MountTargets/read | Lê um recurso de destino de montagem. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/MountTargets/write | Grava um recurso de destino de montagem. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/providers/Microsoft.Insights/diagnosticSettings/read | Exclui um recurso de volume. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/providers/Microsoft.Insights/diagnosticSettings/write | Exclui um recurso de volume. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/providers/Microsoft.Insights/metricDefinitions/read | Exclui um recurso de volume. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/read | Lê um recurso de volume. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots/delete | Exclui um recurso de instantâneo. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots/read | Lê um recurso de instantâneo. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots/write | Grava um recurso de instantâneo. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/write | Grava um recurso de volume. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/write | Grava um recurso de pool. |
> | Ação | Microsoft.NetApp/netAppAccounts/delete | Exclui um recurso de conta. |
> | Ação | Microsoft.NetApp/netAppAccounts/read | Lê um recurso de conta. |
> | Ação | Microsoft.NetApp/netAppAccounts/write | Grava um recurso de conta. |
> | Ação | Microsoft.NetApp/Operations/read | Lê recursos de operação. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Política predefinida SSL do Gateway de Aplicativo |
> | Ação | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Opções SSL disponíveis do Gateway de Aplicativo |
> | Ação | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | Obter conjuntos de regras WAF disponíveis para o Gateway de Aplicativo |
> | Ação | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Ingressar em um pool de endereços de back-end do gateway de aplicativo |
> | Ação | Microsoft.Network/applicationGateways/backendhealth/action | Obter uma integridade de back-end do Gateway de Aplicativo |
> | Ação | Microsoft.Network/applicationGateways/delete | Excluir um gateway de aplicativo |
> | Ação | Microsoft.Network/applicationGateways/effectiveNetworkSecurityGroups/action | Obter Tabela de Rotas configurada no Gateway de Aplicativo |
> | Ação | Microsoft.Network/applicationGateways/effectiveRouteTable/action | Obter Tabela de Rotas configurada no Gateway de Aplicativo |
> | Ação | Microsoft.Network/applicationGateways/providers/Microsoft.Insights/logDefinitions/read | Obter os eventos para o Gateway de Aplicativo |
> | Ação | Microsoft.Network/applicationGateways/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para o Gateway de Aplicativo |
> | Ação | Microsoft.Network/applicationGateways/read | Obter um gateway de aplicativo |
> | Ação | Microsoft.Network/applicationGateways/setSecurityCenterConfiguration/action | Definir a configuração da Central de Segurança do Gateway de Aplicativo |
> | Ação | Microsoft.Network/applicationGateways/start/action | Inicia um gateway de aplicativo |
> | Ação | Microsoft.Network/applicationGateways/stop/action | Interrompe um gateway de aplicativo |
> | Ação | Microsoft.Network/applicationGateways/write | Criar ou atualizar um gateway de aplicativo |
> | Ação | Microsoft.Network/applicationSecurityGroups/delete | Excluir um grupo de segurança de aplicativo |
> | Ação | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | Adicionar uma configuração IP aos grupos de segurança de aplicativo. |
> | Ação | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Adicionar uma regra de segurança aos grupos de segurança de aplicativo. |
> | Ação | Microsoft.Network/applicationSecurityGroups/read | Obter uma ID do grupo de segurança de aplicativo. |
> | Ação | Microsoft.Network/applicationSecurityGroups/write | Criar um grupo de segurança de aplicativo ou atualizar um grupo de segurança de aplicativo existente. |
> | Ação | Microsoft.Network/azureFirewallFqdnTags/read | Obtém as marcas FQDN do Firewall do Azure |
> | Ação | Microsoft.Network/azurefirewalls/delete | Excluir o Firewall do Azure |
> | Ação | Microsoft.Network/azurefirewalls/providers/Microsoft.Insights/logDefinitions/read | Obtém os eventos para o Firewall do Azure |
> | Ação | Microsoft.Network/azurefirewalls/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para o Firewall do Azure |
> | Ação | Microsoft.Network/azurefirewalls/read | Obter o Firewall do Azure |
> | Ação | Microsoft.Network/azurefirewalls/write | Cria ou atualiza um Firewall do Azure |
> | Ação | Microsoft.Network/bgpServiceCommunities/read | Obter comunidades do serviço BGP |
> | Ação | Microsoft.Network/checkTrafficManagerNameAvailability/action | Verificar a disponibilidade de um nome DNS relativo do Gerenciador de Tráfego. |
> | Ação | Microsoft.Network/connections/delete | Excluir VirtualNetworkGatewayConnection |
> | Ação | Microsoft.Network/connections/providers/Microsoft.Insights/diagnosticSettings/read | Obter configurações de diagnóstico para Conexões |
> | Ação | Microsoft.Network/connections/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza as configurações de diagnóstico para Conexões |
> | Ação | Microsoft.Network/connections/providers/Microsoft.Insights/metricDefinitions/read | Obtém as definições de métricas para Conexões |
> | Ação | Microsoft.Network/connections/read | Obter o VirtualNetworkGatewayConnection |
> | Ação | Microsoft.Network/connections/sharedkey/action | Obter VirtualNetworkGatewayConnection SharedKey |
> | Ação | Microsoft.Network/connections/sharedKey/read | Obter SharedKey da VirtualNetworkGatewayConnection |
> | Ação | Microsoft.Network/connections/sharedKey/write | Criar ou atualizar uma VirtualNetworkGatewayConnection SharedKey existente |
> | Ação | Microsoft.Network/connections/vpndeviceconfigurationscript/action | Obter configuração do dispositivo VPN do VirtualNetworkGatewayConnection |
> | Ação | Microsoft.Network/connections/write | Criar ou atualizar uma VirtualNetworkGatewayConnection existente |
> | Ação | Microsoft.Network/ddosProtectionPlans/ddosProtectionPlanProxies/delete | Excluir um proxy do Plano de Proteção contra DDoS |
> | Ação | Microsoft.Network/ddosProtectionPlans/ddosProtectionPlanProxies/read | Obter uma definição de proxy do Plano de Proteção contra DDoS |
> | Ação | Microsoft.Network/ddosProtectionPlans/ddosProtectionPlanProxies/write | Criar um proxy do Plano de Proteção contra DDoS ou atualizar um proxy do Plano de Proteção contra DDoS existente |
> | Ação | Microsoft.Network/ddosProtectionPlans/delete | Excluir um Plano de Proteção contra DDoS |
> | Ação | Microsoft.Network/ddosProtectionPlans/join/action | Adicionar um Plano de Proteção contra DDoS |
> | Ação | Microsoft.Network/ddosProtectionPlans/read | Obter um Plano de Proteção contra DDoS |
> | Ação | Microsoft.Network/ddosProtectionPlans/write | Criar um Plano de Proteção contra DDoS ou atualizar um Plano de Proteção contra DDoS  |
> | Ação | Microsoft.Network/dnsoperationresults/read | Obter os resultados de uma operação de DNS |
> | Ação | Microsoft.Network/dnsoperationstatuses/read | Obter o status de uma operação de DNS  |
> | Ação | Microsoft.Network/dnszones/A/delete | Remover o conjunto de registros de determinado nome e tipo ‘A’ de uma zona DNS. |
> | Ação | Microsoft.Network/dnszones/A/read | Obter o conjunto de registros do tipo 'A', no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags. |
> | Ação | Microsoft.Network/dnszones/A/write | Criar ou atualizar um conjunto de registros do tipo 'A' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft.Network/dnszones/AAAA/delete | Remover o conjunto de registros de determinado nome e digite ‘AAAA’ de uma zona DNS. |
> | Ação | Microsoft.Network/dnszones/AAAA/read | Obter o conjunto de registros do tipo 'AAAA' no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags. |
> | Ação | Microsoft.Network/dnszones/AAAA/write | Criar ou atualizar um conjunto de registros do tipo 'AAAA' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft.Network/dnszones/all/read | Obter os conjuntos de registros DNS em todos os tipos |
> | Ação | Microsoft.Network/dnszones/CAA/delete | Remover o conjunto de registros de um determinado nome e digitar "CAA" de uma zona DNS. |
> | Ação | Microsoft.Network/dnszones/CAA/read | Obter o conjunto de registros do tipo ‘CAA’ no formato JSON. O conjunto de registros contém TTL, marcações e etag. |
> | Ação | Microsoft.Network/dnszones/CAA/write | Criar ou atualizar um conjunto de registros do tipo ‘CAA’ em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft.Network/dnszones/CNAME/delete | Remover o conjunto de registros de determinado nome e tipo ‘CNAME’ de uma zona DNS. |
> | Ação | Microsoft.Network/dnszones/CNAME/read | Obter o conjunto de registros do tipo 'CNAME' no formato JSON. O conjunto de registros contém TTL, marcações e etag. |
> | Ação | Microsoft.Network/dnszones/CNAME/write | Criar ou atualizar um conjunto de registros do tipo 'CNAME' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft.Network/dnszones/delete | Excluir a zona DNS no formato JSON. As propriedades de zona incluem tags, etag, numberOfRecordSets e maxNumberOfRecordSets. |
> | Ação | Microsoft.Network/dnszones/MX/delete | Remover o conjunto de registros de determinado nome e tipo ‘MX’ de uma zona DNS. |
> | Ação | Microsoft.Network/dnszones/MX/read | Obter o conjunto de registros do tipo 'MX' no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags. |
> | Ação | Microsoft.Network/dnszones/MX/write | Criar ou atualizar um conjunto de registros do tipo 'MX' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft.Network/dnszones/NS/delete | Excluir o conjunto de registros DNS do tipo NS |
> | Ação | Microsoft.Network/dnszones/NS/read | Obter o conjunto de registros DNS do tipo NS |
> | Ação | Microsoft.Network/dnszones/NS/write | Criar ou atualizar o conjunto de registros DNS do tipo NS |
> | Ação | Microsoft.Network/dnszones/providers/Microsoft.Insights/diagnosticSettings/read | Obter as configurações de diagnóstico da zona DNS |
> | Ação | Microsoft.Network/dnszones/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar as configurações de diagnóstico da zona DNS |
> | Ação | Microsoft.Network/dnszones/providers/Microsoft.Insights/metricDefinitions/read | Obter as definições de métrica da zona DNS |
> | Ação | Microsoft.Network/dnszones/PTR/delete | Remover o conjunto de registros de determinado nome e tipo ‘PTR’ de uma zona DNS. |
> | Ação | Microsoft.Network/dnszones/PTR/read | Obter o conjunto de registros do tipo 'PTR' no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags. |
> | Ação | Microsoft.Network/dnszones/PTR/write | Criar ou atualizar um conjunto de registros do tipo 'PTR' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft.Network/dnszones/read | Obter a zona DNS no formato JSON. As propriedades de zona incluem tags, etag, numberOfRecordSets e maxNumberOfRecordSets. Observe que esse comando não recupera conjuntos de registros contidos na zona. |
> | Ação | Microsoft.Network/dnszones/recordsets/read | Obter os conjuntos de registros DNS em todos os tipos |
> | Ação | Microsoft.Network/dnszones/SOA/read | Obter o conjunto de registros DNS do tipo SOA |
> | Ação | Microsoft.Network/dnszones/SOA/write | Criar ou atualizar o conjunto de registros DNS do tipo SOA |
> | Ação | Microsoft.Network/dnszones/SRV/delete | Remover o conjunto de registros de determinado nome e tipo ‘SRV’ de uma zona DNS. |
> | Ação | Microsoft.Network/dnszones/SRV/read | Obter o conjunto de registros do tipo 'SRV' no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags. |
> | Ação | Microsoft.Network/dnszones/SRV/write | Criar ou atualizar o conjunto de registros do tipo SRV |
> | Ação | Microsoft.Network/dnszones/TXT/delete | Remover o conjunto de registros de determinado nome e tipo ‘TXT’ de uma zona DNS. |
> | Ação | Microsoft.Network/dnszones/TXT/read | Obter o conjunto de registros do tipo 'TXT' no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags. |
> | Ação | Microsoft.Network/dnszones/TXT/write | Criar ou atualizar um conjunto de registros do tipo 'TXT' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft.Network/dnszones/write | Criar ou atualizar uma zona DNS em um grupo de recursos.  Usado para atualizar as marcações em um recurso de zona DNS. Observe que esse comando não pode ser usado para criar ou atualizar conjuntos de registros na zona. |
> | Ação | Microsoft.Network/expressRouteCircuits/authorizations/delete | Excluir uma autorização ExpressRouteCircuit |
> | Ação | Microsoft.Network/expressRouteCircuits/authorizations/read | Obter uma autorização ExpressRouteCircuit |
> | Ação | Microsoft.Network/expressRouteCircuits/authorizations/write | Criar ou atualizar uma autorização ExpressRouteCircuit existente |
> | Ação | Microsoft.Network/expressRouteCircuits/delete | Excluir um ExpressRouteCircuit |
> | Ação | Microsoft.Network/expressRouteCircuits/join/action | Ingressa em um Circuito de Rota Expressa |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/arpTables/action | Obter ArpTable de emparelhamento de ExpressRouteCircuit |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | Excluir uma conexão de ExpressRouteCircuit |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/connections/read | Obter uma conexão de circuito de ExpressRoute |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/connections/write | Criar ou atualizar um Recurso de Conexão de ExpressRouteCircuit existente |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/delete | Excluir um emparelhamento de ExpressRouteCircuit |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/providers/Microsoft.Insights/diagnosticSettings/read | Obter configurações de diagnóstico para emparelhamento ExpressRoute Circuit |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza configurações de diagnóstico para circuitos de emparelhamentos ExpressRoute Circuit |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/providers/Microsoft.Insights/metricDefinitions/read | Otém as definições de métrica para emparelhamentos ExpressRoute Circuit |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/read | Obter um emparelhamento de ExpressRouteCircuit |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/routeTables/action | Obter RouteTable de emparelhamento de ExpressRouteCircuit |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/routeTablesSummary/action | Obter um resumo de RouteTable de emparelhamento de ExpressRouteCircuit |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/stats/read | Obter status de emparelhamento de ExpressRouteCircuit |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/write | Criar ou atualizar um emparelhamento de ExpressRouteCircuit existente |
> | Ação | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/read | Obter configurações de diagnóstico para circuitos de ExpressRoute |
> | Ação | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar configurações de diagnóstico para circuitos de ExpressRoute |
> | Ação | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/logDefinitions/read | Obter os eventos para circuitos de ExpressRoute |
> | Ação | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/metricDefinitions/read | Obter as definições de métrica para circuitos de ExpressRoute |
> | Ação | Microsoft.Network/expressRouteCircuits/read | Obter um ExpressRouteCircuit |
> | Ação | Microsoft.Network/expressRouteCircuits/stats/read | Obter status de ExpressRouteCircuit |
> | Ação | Microsoft.Network/expressRouteCircuits/write | Criar ou atualizar um ExpressRouteCircuit existente |
> | Ação | Microsoft.Network/expressRouteCrossConnections/delete | Excluir Conexão Cruzada de Rota Expressa |
> | Ação | Microsoft.Network/expressRouteCrossConnections/join/action | Adicionar uma Conexão Cruzada de Rota Expressa |
> | Ação | Microsoft.Network/expressRouteCrossConnections/peerings/arpTables/action | Obter uma Tabela ARP de Emparelhamento de Conexão Cruzada de Rota Expressa |
> | Ação | Microsoft.Network/expressRouteCrossConnections/peerings/delete | Excluir um Emparelhamento de Conexão Cruzada de Rota Expressa |
> | Ação | Microsoft.Network/expressRouteCrossConnections/peerings/read | Obter um Emparelhamento de Conexão Cruzada de Rota Expressa |
> | Ação | Microsoft.Network/expressRouteCrossConnections/peerings/routeTables/action | Obter uma Tabela de Rotas de Emparelhamento de Conexão Cruzada de Rota Expressa |
> | Ação | Microsoft.Network/expressRouteCrossConnections/peerings/routeTableSummary/action | Obter uma Resumo de Tabela de Rotas de Emparelhamento de Conexão Cruzada de Rota Expressa |
> | Ação | Microsoft.Network/expressRouteCrossConnections/peerings/write | Criar um emparelhamento de conexão cruzada de rota expressa ou atualizar um emparelhamento de conexão cruzada de rota expressa existente |
> | Ação | Microsoft.Network/expressRouteCrossConnections/read | Excluir Conexão Cruzada de Rota Expressa |
> | Ação | Microsoft.Network/expressRouteCrossConnections/write | Criar ou atualizar Conexão Cruzada de Rota Expressa |
> | Ação | Microsoft.Network/expressRouteGateways/delete | Excluir o Gateway do ExpressRoute |
> | Ação | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | Adicionar uma Conexão Cruzada de Rota Expressa |
> | Ação | Microsoft.Network/expressRouteGateways/expressRouteConnections/read | Adicionar uma Conexão de Rota Expressa |
> | Ação | Microsoft.Network/expressRouteGateways/expressRouteConnections/write | Cria uma Conexão de Rota Expressa ou Atualiza uma Conexão de Rota Expressa |
> | Ação | Microsoft.Network/expressRouteGateways/join/action | Ingressa em um gateway de Rota Expressa |
> | Ação | Microsoft.Network/expressRouteGateways/read | Obtém o Gateway de Rota Expressa |
> | Ação | Microsoft.Network/expressRouteGateways/write | Criar ou atualizar Gateway de Rota Expressa |
> | Ação | Microsoft.Network/expressRoutePorts/delete | Exclui ExpressRoutePorts |
> | Ação | Microsoft.Network/expressRoutePorts/join/action | Adiciona ExpressRoutePorts |
> | Ação | Microsoft.Network/expressRoutePorts/links/read | Obtém ExpressRouteLink |
> | Ação | Microsoft.Network/expressRoutePorts/providers/Microsoft.Insights/metricDefinitions/read | Obter as definições de métrica para portas de ExpressRoute |
> | Ação | Microsoft.Network/expressRoutePorts/read | Obtém ExpressRoutePorts |
> | Ação | Microsoft.Network/expressRoutePorts/write | Cria ou atualiza o ExpressRoutePorts |
> | Ação | Microsoft.Network/expressRoutePortsLocations/read | Obter Localizações de Porta do ExpressRoute |
> | Ação | Microsoft.Network/expressRouteServiceProviders/read | Obter os provedores de serviços do ExpressRoute |
> | Ação | Microsoft.Network/frontdoors/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso do Frontdoor |
> | Ação | Microsoft.Network/frontdoors/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico para o recurso do Frontdoor |
> | Ação | Microsoft.Network/frontdoors/providers/Microsoft.Insights/logDefinitions/read | Obter logs disponíveis para recursos do Frontdoor |
> | Ação | Microsoft.Network/frontdoors/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para recursos do Frontdoor |
> | Ação | Microsoft.Network/frontdoors/read | Obter Frontdoor |
> | Ação | Microsoft.Network/getDnsResourceReference/action | Solicitação de dependência de recurso alias do DNS |
> | Ação | Microsoft.Network/interfaceEndpoints/delete | Exclui um recurso de ponto de extremidade de interface. |
> | Ação | Microsoft.Network/interfaceEndpoints/read | Obtém um recurso de ponto de extremidade de interface. |
> | Ação | Microsoft.Network/interfaceEndpoints/write | Cria um novo ponto de extremidade de interface ou atualiza um ponto de extremidade de interface existente. |
> | Ação | Microsoft.Network/internalNotify/action | Notificação do recurso de alias do DNS |
> | Ação | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Ingressar em um pool de endereços de back-end do balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/backendAddressPools/read | Obter uma definição de pool de endereços de back-end do balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/delete | Excluir um balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Ingressar em um pool NAT de entrada do balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/inboundNatPools/read | Obter uma definição de pool NAT de entrada do balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/inboundNatRules/delete | Excluir uma regra NAT de entrada do balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Adicionar uma regra NAT de entrada do balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/inboundNatRules/read | Obter uma definição de regra NAT de entrada do balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/inboundNatRules/write | Criar uma regra NAT de entrada do balanceador de carga ou atualizar uma regra NAT de entrada do balanceador de carga existente |
> | Ação | Microsoft.Network/loadBalancers/loadBalancingRules/read | Obter uma definição regra de balanceamento de carga do balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/networkInterfaces/read | Obter as referências a todos os adaptadores de rede em um balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/outboundRules/read | Obtém uma definição de regra NAT de saída do balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/probes/join/action | Permite usar investigações de um balanceador de carga. Por exemplo, com essa permissão, a propriedade healthProbe do conjunto de dimensionamento de VM pode referenciar a investigação. |
> | Ação | Microsoft.Network/loadBalancers/probes/read | Obter uma investigação do balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/read | Obter as configurações de diagnóstico do balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar as configurações de diagnóstico do balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/logDefinitions/read | Obter os eventos para o balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para o balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/read | Obter uma definição de balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/virtualMachines/read | Obter as referências a todas as máquinas virtuais em um balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/write | Criar um balanceador de carga ou atualizar um balanceador de carga existente |
> | Ação | Microsoft.Network/localnetworkgateways/delete | Excluir LocalNetworkGateway |
> | Ação | Microsoft.Network/localnetworkgateways/read | Obter o LocalNetworkGateway |
> | Ação | Microsoft.Network/localnetworkgateways/write | Criar ou atualizar uma LocalNetworkGateway existente |
> | Ação | Microsoft.Network/locations/availableDelegations/read | Obtém as delegações disponíveis |
> | Ação | Microsoft.Network/locations/bareMetalTenants/action | Aloca ou valida um locatário do Bare Metal |
> | Ação | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | Verifica o suporte à Rede Acelerada |
> | Ação | Microsoft.Network/locations/checkDnsNameAvailability/read | Verificar se o rótulo de DNS está disponível no local especificado |
> | Ação | Microsoft.Network/locations/effectiveResourceOwnership/action | Obtém a propriedade de recursos efetivos |
> | Ação | Microsoft.Network/locations/operationResults/read | Obter o resultado de uma operação POST ou DELETE assíncrona |
> | Ação | Microsoft.Network/locations/operations/read | Obter o recurso de operação que representa o status de uma operação assíncrona |
> | Ação | Microsoft.Network/locations/setResourceOwnership/action | Define a propriedade do recurso |
> | Ação | Microsoft.Network/locations/supportedVirtualMachineSizes/read | Obtém os tamanhos de máquinas virtuais compatíveis |
> | Ação | Microsoft.Network/locations/usages/read | Obter a métrica de uso dos recursos |
> | Ação | Microsoft.Network/locations/validateResourceOwnership/action | Valida a propriedade do recurso |
> | Ação | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | Obter uma lista de serviços de ponto de extremidade de Rede Virtual disponíveis |
> | Ação | Microsoft.Network/networkIntentPolicies/delete | Exclui uma política de intenção de rede |
> | Ação | Microsoft.Network/networkIntentPolicies/read | Obtém uma descrição de intenção de diretiva de rede |
> | Ação | Microsoft.Network/networkIntentPolicies/write | Cria uma interface de rede ou atualiza uma política de intenção de rede existente |
> | Ação | Microsoft.Network/networkInterfaces/delete | Excluir um adaptador de rede |
> | Ação | Microsoft.Network/networkInterfaces/diagnosticIdentity/read | Obter identidade de diagnóstico do recurso |
> | Ação | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | Obter grupos de segurança de configurados no adaptador de rede da máquina virtual |
> | Ação | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | Obter tabela de rota configurada no adaptador de rede da máquina virtual |
> | Ação | Microsoft.Network/networkInterfaces/ipconfigurations/join/action | Une uma configuração IP do adaptador de rede. |
> | Ação | Microsoft.Network/networkInterfaces/ipconfigurations/read | Obter uma definição de configuração de IP do adaptador de rede.  |
> | Ação | Microsoft.Network/networkInterfaces/join/action | Adicionar uma Máquina Virtual a um adaptador de rede |
> | Ação | Microsoft.Network/networkInterfaces/joinViaPrivateIp/acti | Ingressa um recurso em um adaptador de rede por meio de uma Associação de Serviço |
> | Ação | Microsoft.Network/networkInterfaces/loadBalancers/read | Obter todos os balanceadores de carga dos quais o adaptador de rede faz parte |
> | Ação | Microsoft.Network/networkInterfaces/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para o Adaptador de Rede |
> | Ação | Microsoft.Network/networkInterfaces/read | Obter uma definição de adaptador de rede.  |
> | Ação | Microsoft.Network/networkInterfaces/serviceAssociations/delete | Exclui uma Associação de Serviço |
> | Ação | Microsoft.Network/networkInterfaces/serviceAssociations/read | Obtém uma definição de Associação de Serviço |
> | Ação | Microsoft.Network/networkInterfaces/serviceAssociations/validate/action | Valida uma Associação de Serviço |
> | Ação | Microsoft.Network/networkInterfaces/serviceAssociations/write | Cria uma nova Associação de Serviço ou modifica uma existente |
> | Ação | Microsoft.Network/networkInterfaces/tapConfigurations/delete | Exclui uma configuração de toque do adaptador de rede. |
> | Ação | Microsoft.Network/networkInterfaces/tapConfigurations/read | Obtém uma configuração de toque do adaptador de rede. |
> | Ação | Microsoft.Network/networkInterfaces/tapConfigurations/write | Cria uma configuração de toque do adaptador de rede ou atualiza uma configuração de toque do adaptador de rede existente. |
> | Ação | Microsoft.Network/networkInterfaces/write | Criar uma interface de rede ou atualizar uma interface de rede existente.  |
> | Ação | Microsoft.Network/networkProfiles/delete | Excluir um perfil de rede |
> | Ação | Microsoft.Network/networkProfiles/read | Obtém um perfil de rede |
> | Ação | Microsoft.Network/networkProfiles/removeContainers/action | Remove os contêineres |
> | Ação | Microsoft.Network/networkProfiles/setContainers/action | Definir contêineres |
> | Ação | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | Define as Interfaces de rede de contêiner |
> | Ação | Microsoft.Network/networkProfiles/write | Criar ou atualizar um perfil de rede |
> | Ação | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | Obter uma definição padrão da regra de segurança |
> | Ação | Microsoft.Network/networkSecurityGroups/delete | Excluir um grupo de segurança de rede |
> | Ação | Microsoft.Network/networkSecurityGroups/join/action | Ingressar em um grupo de segurança de rede |
> | Ação | Microsoft.Network/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | Obter as configurações de diagnóstico dos Grupos de Segurança de Rede |
> | Ação | Microsoft.Network/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar as configurações de diagnóstico dos Grupos de Segurança de Rede, essa operação é complementada pelo provedor de recursos do Microsoft Insights. |
> | Ação | Microsoft.Network/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | Obter os eventos para o grupo de segurança de rede |
> | Ação | Microsoft.Network/networkSecurityGroups/read | Obter uma definição de um grupo de segurança de rede |
> | Ação | Microsoft.Network/networkSecurityGroups/securityRules/delete | Excluir uma regra de segurança |
> | Ação | Microsoft.Network/networkSecurityGroups/securityRules/read | Obter uma definição da regra de segurança |
> | Ação | Microsoft.Network/networkSecurityGroups/securityRules/write | Criar uma regra de segurança ou atualizar uma regra de segurança existente |
> | Ação | Microsoft.Network/networkSecurityGroups/write | Criar um grupo de segurança de rede ou atualizar um grupo de segurança de rede existente |
> | Ação | Microsoft.Network/networkWatchers/availableProvidersList/action | Retornar todos os provedores de serviços de Internet disponíveis para uma região do Azure especificada. |
> | Ação | Microsoft.Network/networkWatchers/azureReachabilityReport/action | Retornar a pontuação de latência relativa para provedores de serviços de Internet de um local especificado para regiões do Azure. |
> | Ação | Microsoft.Network/networkWatchers/configureFlowLog/action | Configurar o registro de fluxo em log para um recurso de destino. |
> | Ação | Microsoft.Network/networkWatchers/connectionMonitors/delete | Excluir um Monitor de Conexão |
> | Ação | Microsoft.Network/networkWatchers/connectionMonitors/providers/Microsoft.Insights/diagnosticSettings/read | Obter as configurações de diagnóstico do Monitor de Conexão |
> | Ação | Microsoft.Network/networkWatchers/connectionMonitors/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar as configurações de diagnóstico do Monitor de Conexão |
> | Ação | Microsoft.Network/networkWatchers/connectionMonitors/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para o Monitor de Conexão |
> | Ação | Microsoft.Network/networkWatchers/connectionMonitors/query/action | Consultar monitoramento de conectividade entre os pontos de extremidades especificados |
> | Ação | Microsoft.Network/networkWatchers/connectionMonitors/read | Obter detalhes do Monitor de Conexão |
> | Ação | Microsoft.Network/networkWatchers/connectionMonitors/start/action | Iniciar monitoramento de conectividade entre pontos de extremidades especificados |
> | Ação | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | Parar/pausar monitoramento de conectividade entre pontos de extremidades especificados |
> | Ação | Microsoft.Network/networkWatchers/connectionMonitors/write | Criar um Monitor de Conexão |
> | Ação | Microsoft.Network/networkWatchers/connectivityCheck/action | Verificar a possibilidade de estabelecer uma conexão TCP direta de uma máquina virtual com um determinado ponto de extremidade, incluindo outra VM ou um servidor remoto arbitrário. |
> | Ação | Microsoft.Network/networkWatchers/delete | Excluir um observador de rede |
> | Ação | Microsoft.Network/networkWatchers/ipFlowVerify/action | Retornar se o pacote é permitido ou negado em relação a um destino específico. |
> | Ação | Microsoft.Network/networkWatchers/lenses/delete | Excluir uma Lente |
> | Ação | Microsoft.Network/networkWatchers/lenses/query/action | Consultar monitoramento de tráfego em um ponto de extremidade especificado |
> | Ação | Microsoft.Network/networkWatchers/lenses/read | Obter os detalhes de Lente |
> | Ação | Microsoft.Network/networkWatchers/lenses/start/action | Iniciar monitoramento de tráfego em um ponto de extremidade especificado |
> | Ação | Microsoft.Network/networkWatchers/lenses/stop/action | Parar/pausar o monitoramento do tráfego de rede em um terminal especificado |
> | Ação | Microsoft.Network/networkWatchers/lenses/write | Criar uma lente |
> | Ação | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | Diagnóstico de configuração de rede. |
> | Ação | Microsoft.Network/networkWatchers/nextHop/action | Para um destino especificado e endereço IP de destino especificados, retornar o tipo do próximo salto e próximo endereço IP esperado. |
> | Ação | Microsoft.Network/networkWatchers/packetCaptures/delete | Excluir uma captura de pacote |
> | Ação | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Obter informações sobre propriedades e status de um recurso de captura de pacote. |
> | Ação | Microsoft.Network/networkWatchers/packetCaptures/read | Obter a definição de captura de pacote |
> | Ação | Microsoft.Network/networkWatchers/packetCaptures/stop/action | Interromper a sessão de captura de pacote em execução. |
> | Ação | Microsoft.Network/networkWatchers/packetCaptures/write | Criar uma captura de pacote |
> | Ação | Microsoft.Network/networkWatchers/pingMeshes/delete | Exclui um PingMesh |
> | Ação | Microsoft.Network/networkWatchers/pingMeshes/read | Obtenha detalhes de PingMesh |
> | Ação | Microsoft.Network/networkWatchers/pingMeshes/start/action | Iniciar PingMesh entre VMs especificadas |
> | Ação | Microsoft.Network/networkWatchers/pingMeshes/stop/action | Iniciar PingMesh entre VMs especificadas |
> | Ação | Microsoft.Network/networkWatchers/pingMeshes/write | Cria um PingMesh |
> | Ação | Microsoft.Network/networkWatchers/queryConnectionMonitors/action | Consultar em lote o monitoramento de conectividade entre os pontos de extremidades especificados |
> | Ação | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | Obter o status do registro do fluxo em log em um recurso. |
> | Ação | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | Obter o resultado da solução de problemas da operação de solução de problemas executada ou atualmente em execução. |
> | Ação | Microsoft.Network/networkWatchers/read | Obter a definição de observador de rede |
> | Ação | Microsoft.Network/networkWatchers/securityGroupView/action | Exibir as regras de grupo de segurança de rede configuradas e em vigor aplicadas a uma máquina virtual. |
> | Ação | Microsoft.Network/networkWatchers/topology/action | Obter uma exibição dos recursos em nível de rede e de suas relações em um grupo de recursos. |
> | Ação | Microsoft.Network/networkWatchers/troubleshoot/action | Iniciar a solução de problemas em um recurso de rede no Azure. |
> | Ação | Microsoft.Network/networkWatchers/write | Criar um observador de rede ou atualizar um observador de rede existente |
> | Ação | Microsoft.Network/operations/read | Obter operações disponíveis |
> | Ação | Microsoft.Network/p2sVpnGateways/delete | Exclui um P2SVpnGateway. |
> | Ação | Microsoft.Network/p2sVpnGateways/generatevpnprofile/action | Gerar o perfil de Vpn para o P2SVpnGateway |
> | Ação | Microsoft.Network/p2sVpnGateways/read | Obtém um P2SVpnGateway. |
> | Ação | Microsoft.Network/p2sVpnGateways/write | Coloca um P2SVpnGateway. |
> | Ação | Microsoft.Network/privateLinkServices/delete | Exclui um recurso do serviço de link privado. |
> | Ação | Microsoft.Network/privateLinkServices/interfaceEndpointConnections/delete | Exclui uma conexão de ponto de extremidade de interface. |
> | Ação | Microsoft.Network/privateLinkServices/interfaceEndpointConnections/read | Obtém uma definição de conexão de ponto de extremidade de interface. |
> | Ação | Microsoft.Network/privateLinkServices/interfaceEndpointConnections/write | Cria uma nova conexão de ponto de extremidade de interface ou atualiza uma conexão de ponto de extremidade de interface existente. |
> | Ação | Microsoft.Network/privateLinkServices/read | Obtém um recurso do serviço de link privado. |
> | Ação | Microsoft.Network/privateLinkServices/write | Cria um novo serviço de link privado ou atualiza um serviço de link privado existente. |
> | Ação | Microsoft.Network/publicIPAddresses/delete | Excluir um endereço IP público. |
> | Ação | Microsoft.Network/publicIPAddresses/dnsAliases/delete | Exclui um recurso de Alias de Dns do endereço Ip público |
> | Ação | Microsoft.Network/publicIPAddresses/dnsAliases/read | Obtém um recurso de Alias de Dns do endereço Ip público |
> | Ação | Microsoft.Network/publicIPAddresses/dnsAliases/write | Cria um recurso de Alias de Dns do endereço Ip público |
> | Ação | Microsoft.Network/publicIPAddresses/join/action | Ingressar em um endereço IP público |
> | Ação | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/read | Obter as configurações de diagnóstico do endereço IP público |
> | Ação | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar as configurações de diagnóstico do endereço IP público |
> | Ação | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/logDefinitions/read | Obter as definições de log do endereço IP público |
> | Ação | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/metricDefinitions/read | Obter as definições de métrica do endereço IP público |
> | Ação | Microsoft.Network/publicIPAddresses/read | Obter uma definição de endereço IP público. |
> | Ação | Microsoft.Network/publicIPAddresses/write | Criar um endereço IP público ou atualizar um endereço IP público existente.  |
> | Ação | Microsoft.Network/publicIPPrefixes/delete | Exclui um prefixo IP público A |
> | Ação | Microsoft.Network/publicIPPrefixes/join/action | Une um PublicIPPrefix |
> | Ação | Microsoft.Network/publicIPPrefixes/read | Obtém uma definição de prefixo IP público |
> | Ação | Microsoft.Network/publicIPPrefixes/write | Cria um prefixo IP público ou atualiza um prefixo IP público existente |
> | Ação | Microsoft.Network/register/action | Registra a assinatura |
> | Ação | Microsoft.Network/routeFilters/delete | Excluir uma definição de filtro de rota |
> | Ação | Microsoft.Network/routeFilters/join/action | Ingressar em um filtro de rota |
> | Ação | Microsoft.Network/routeFilters/read | Obter uma definição de filtro de rota |
> | Ação | Microsoft.Network/routeFilters/routeFilterRules/delete | Excluir uma definição de regra de filtro de rota |
> | Ação | Microsoft.Network/routeFilters/routeFilterRules/read | Obter uma definição de regra de filtro de rota |
> | Ação | Microsoft.Network/routeFilters/routeFilterRules/write | Criar uma regra de filtro de rota ou atualizar uma regra de filtro de rota existente |
> | Ação | Microsoft.Network/routeFilters/write | Criar um filtro de rota ou atualizar um filtro de rota existente |
> | Ação | Microsoft.Network/routeTables/delete | Excluir uma definição de tabela de rota |
> | Ação | Microsoft.Network/routeTables/join/action | Ingressar em uma tabela de rotas |
> | Ação | Microsoft.Network/routeTables/read | Obter uma definição de tabela de rota |
> | Ação | Microsoft.Network/routeTables/routes/delete | Excluir uma definição de rota |
> | Ação | Microsoft.Network/routeTables/routes/read | Obter uma definição de rota |
> | Ação | Microsoft.Network/routeTables/routes/write | Criar uma rota ou atualizar uma rota existente |
> | Ação | Microsoft.Network/routeTables/write | Criar uma tabela de rotas ou atualizar uma tabela de rotas existente |
> | Ação | Microsoft.Network/securegateways/applicationRuleCollections/delete | Excluir uma Coleção de Regras de Aplicativo para um Gateway de Segurança |
> | Ação | Microsoft.Network/securegateways/applicationRuleCollections/read | Recuperar uma Coleção de Regras de Aplicativo para um determinado Gateway de Segurança |
> | Ação | Microsoft.Network/securegateways/applicationRuleCollections/write | Criar ou atualizar uma Coleção de Regras de Aplicativo para um Gateway de Segurança |
> | Ação | Microsoft.Network/securegateways/delete | Excluir um Gateway de Segurança |
> | Ação | Microsoft.Network/securegateways/networkRuleCollections/delete | Excluir uma Coleção de Regras de Rede para um Gateway de Segurança |
> | Ação | Microsoft.Network/securegateways/networkRuleCollections/read | Recuperar uma Coleção de Regras de Rede para um determinado Gateway de Segurança |
> | Ação | Microsoft.Network/securegateways/networkRuleCollections/write | Criar ou atualizar uma Coleção de Regras de Rede para um Gateway de Segurança |
> | Ação | Microsoft.Network/securegateways/providers/Microsoft.Insights/logDefinitions/read | Obtém os eventos para o Firewall do Azure |
> | Ação | Microsoft.Network/securegateways/read | Obter um Gateway de Segurança |
> | Ação | Microsoft.Network/securegateways/write | Criar ou atualizar um Gateway de Segurança |
> | Ação | Microsoft.Network/serviceEndpointPolicies/delete | Excluir uma política de ponto de extremidade de serviço |
> | Ação | Microsoft.Network/serviceEndpointPolicies/join/action | Adicionar uma política de ponto de extremidade de serviço |
> | Ação | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | Adicionar uma sub-rede para políticas de ponto de extremidade de serviço |
> | Ação | Microsoft.Network/serviceEndpointPolicies/read | Obter uma descrição da política de ponto de extremidade de serviço |
> | Ação | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | Excluir uma descrição da política de ponto de extremidade de serviço |
> | Ação | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | Obter uma definição de política de ponto de extremidade de serviço |
> | Ação | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | Criar uma definição de política de ponto de extremidade de serviço ou atualizar uma definição de política de ponto de extremidade de serviço existente |
> | Ação | Microsoft.Network/serviceEndpointPolicies/write | Criar uma política de ponto de extremidade de serviço ou atualizar uma política de ponto de extremidade de serviço existente |
> | Ação | Microsoft.Network/trafficManagerGeographicHierarchies/read | Obter a hierarquia de geográfica do Gerenciador de Tráfego contendo regiões que podem ser usadas com o método de roteamento de tráfego geográfico |
> | Ação | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | Excluir um Ponto de Extremidade do Azure de um perfil do Gerenciador de Tráfego existente. O Gerenciador de Tráfego interromperá o tráfego de roteamento para o Ponto de Extremidade do Azure excluído. |
> | Ação | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Obter um Ponto de Extremidade do Azure que pertence a um perfil do Gerenciador de Tráfego, incluindo todas as propriedades desse Ponto de Extremidade do Azure. |
> | Ação | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | Adicionar um novo Ponto de Extremidade do Azure em um perfil do Gerenciador de Tráfego existente ou atualizar as propriedades de um Ponto de Extremidade do Azure existente nesse perfil do Gerenciador de Tráfego. |
> | Ação | Microsoft.Network/trafficManagerProfiles/delete | Excluir o perfil do Gerenciador de Tráfego. Todas as configurações associadas ao perfil do Gerenciador de Tráfego serão perdidas e o perfil não poderá ser usado para rotear o tráfego. |
> | Ação | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | Excluir um ponto de extremidade externo de um perfil do Gerenciador de Tráfego existente. O Gerenciador de Tráfego do Microsoft Azure interromperá o tráfego de roteamento para o ponto de extremidade externo excluído. |
> | Ação | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Obter um ponto de extremidade externo que pertence a um perfil do Gerenciador de Tráfego, incluindo todas as propriedades desse ponto de extremidade externo. |
> | Ação | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | Adicionar um novo ponto de extremidade externo em um perfil do Gerenciador de Tráfego existente ou atualizar as propriedades de um ponto de extremidade externo existente nesse perfil do Gerenciador de Tráfego. |
> | Ação | Microsoft.Network/trafficManagerProfiles/heatMaps/read | Obter o mapa de calor do Gerenciador de Tráfego para o perfil do Gerenciador de Tráfego fornecido, que contém contagens de consulta e dados de latência por local e IP de origem. |
> | Ação | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | Excluir um ponto de extremidade aninhado de um perfil do Gerenciador de Tráfego existente. O Gerenciador de Tráfego interromperá o tráfego de roteamento para o ponto de extremidade aninhado excluído. |
> | Ação | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Obtém um ponto de extremidade aninhado que pertence a um perfil do Gerenciador de Tráfego, incluindo todas as propriedades desse ponto de extremidade aninhado. |
> | Ação | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | Adicionar um novo ponto de extremidade aninhado em um perfil do Gerenciador de Tráfego existente ou atualizar as propriedades de um ponto de extremidade aninhado existente nesse perfil do Gerenciador de Tráfego. |
> | Ação | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/read | Obter as configurações de diagnóstico do Gerenciador de Tráfego |
> | Ação | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar as configurações de diagnóstico do Gerenciador de Tráfego, essa operação é complementada pelo provedor de recursos do Microsoft Insights. |
> | Ação | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/logDefinitions/read | Obter os eventos para o Gerenciador de Tráfego |
> | Ação | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para o Gerenciador de Tráfego. |
> | Ação | Microsoft.Network/trafficManagerProfiles/read | Obter a configuração de perfil do Gerenciador de Tráfego. Isso inclui configurações DNS, configurações de roteamento de tráfego, configurações de monitoramento do ponto de extremidade e lista de pontos de extremidade roteados por esse perfil do Gerenciador de Tráfego. |
> | Ação | Microsoft.Network/trafficManagerProfiles/write | Criar um perfil do Gerenciador de Tráfego ou modificar a configuração de um perfil do Gerenciador de Tráfego existente.<br>Isso inclui habilitar ou desabilitar um perfil e modificar as configurações de DNS, configurações de roteamento de tráfego ou configurações de monitoramento do ponto de extremidade.<br>Pontos de extremidade roteados pelo perfil do Gerenciador de Tráfego podem ser adicionados, removidos, habilitados ou desabilitados. |
> | Ação | Microsoft.Network/trafficManagerUserMetricsKeys/delete | Excluir a chave do nível de assinatura usada para a coleção de métricas de usuário em tempo real. |
> | Ação | Microsoft.Network/trafficManagerUserMetricsKeys/read | Obter a chave do nível de assinatura usada para a coleção de métricas de usuário em tempo real. |
> | Ação | Microsoft.Network/trafficManagerUserMetricsKeys/write | Criar uma nova chave do nível de assinatura a ser usada para a coleção de métricas de usuário em tempo real. |
> | Ação | Microsoft.Network/unregister/action | Cancelar o registro da assinatura |
> | Ação | Microsoft.Network/virtualHubs/delete | Excluir um Hub Virtual |
> | Ação | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | Excluir um HubVirtualNetworkConnection |
> | Ação | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | Obter um HubVirtualNetworkConnection |
> | Ação | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | Criar ou atualizar um HubVirtualNetworkConnection |
> | Ação | Microsoft.Network/virtualHubs/read | Obter um Hub Virtual |
> | Ação | Microsoft.Network/virtualHubs/write | Criar ou atualizar um Hub Virtual |
> | Ação | microsoft.network/virtualnetworkgateways/connections/read | Obter um VirtualNetworkGatewayConnection |
> | Ação | Microsoft.Network/virtualNetworkGateways/delete | Excluir um virtualNetworkGateway |
> | Ação | microsoft.network/virtualnetworkgateways/generatevpnclientpackage/action | Gerar pacote de VpnClient para virtualNetworkGateway |
> | Ação | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | Gerar pacote de VpnProfile para VirtualNetworkGateway |
> | Ação | microsoft.network/virtualnetworkgateways/getadvertisedroutes/action | Obter rotas anunciadas do virtualNetworkGateway |
> | Ação | microsoft.network/virtualnetworkgateways/getbgppeerstatus/action | Obter status do Par de BGP do virtualNetworkGateway |
> | Ação | microsoft.network/virtualnetworkgateways/getlearnedroutes/action | Obter rotas aprendidas do virtualnetworkgateway |
> | Ação | microsoft.network/virtualnetworkgateways/getvpnclientconnectionhealth/action | Obter por integridade de conexão de cliente Vpn para VirtualNetworkGateway |
> | Ação | microsoft.network/virtualnetworkgateways/getvpnclientipsecparameters/action | Obter parâmetros IPsec do Vpnclient para cliente P2S do VirtualNetworkGateway. |
> | Ação | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | Obter a URL de um pacote de perfil de cliente VPN gerado previamente |
> | Ação | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/read | Obter as configurações de diagnóstico do Gateway de Rede Virtual |
> | Ação | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar as configurações de diagnóstico do Gateway de Rede Virtual, essa operação é complementada pelo provedor de recursos do Microsoft Insights. |
> | Ação | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/logDefinitions/read | Obter os eventos do Gateway de Rede Virtual |
> | Ação | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para o Gateway de Rede Virtual |
> | Ação | Microsoft.Network/virtualNetworkGateways/read | Obter um VirtualNetworkGateway |
> | Ação | microsoft.network/virtualnetworkgateways/reset/action | Reiniciar um virtualNetworkGateway |
> | Ação | microsoft.network/virtualnetworkgateways/resetvpnclientsharedkey/action | Reiniciar a chave compartilhada Vpnclient para o cliente P2S do VirtualNetworkGateway. |
> | Ação | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | Configurar parâmetros IPsec do Vpnclient para cliente P2S do VirtualNetworkGateway. |
> | Ação | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | Listar dispositivos VPN com suporte |
> | Ação | Microsoft.Network/virtualNetworkGateways/write | Criar ou atualizar um VirtualNetworkGateway |
> | Ação | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | Verificar se o endereço IP está disponível na rede virtual especificada |
> | Ação | Microsoft.Network/virtualNetworks/customViews/get/action | Obter um conteúdo de exibição personalizada da Rede Virtual |
> | Ação | Microsoft.Network/virtualNetworks/customViews/read | Obter a definição de uma exibição personalizada da Rede Virtual |
> | Ação | Microsoft.Network/virtualNetworks/delete | Excluir uma rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/peer/action | Colocar uma rede virtual com outra rede virtual de mesmo nível |
> | Ação | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/read | Obter as configurações de diagnóstico da Rede Virtual |
> | Ação | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar as configurações de diagnóstico da Rede Virtual |
> | Ação | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/logDefinitions/read | Obter as definições de log da Rede Virtual |
> | Ação | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/metricDefinitions/read | Obtem as métricas disponíveis para o PingMesh |
> | Ação | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | Excluir um proxy de emparelhamento de rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | Obter uma definição de proxy de emparelhamento de rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | Criar um proxy de emparelhamento de rede virtual ou atualizar um proxy de emparelhamento de rede virtual existente |
> | Ação | Microsoft.Network/virtualNetworks/subnets/delete | Excluir uma sub-rede de rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/subnets/join/action | Ingressar em uma rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Adicionar recursos como conta de armazenamento ou banco de dados SQL a uma sub-rede. |
> | Ação | Microsoft.Network/virtualNetworks/subnets/read | Obter uma definição de sub-rede da rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/subnets/resourceNavigationLinks/delete | Excluir um link de navegação de recursos |
> | Ação | Microsoft.Network/virtualNetworks/subnets/resourceNavigationLinks/read | Obter a definição de link de navegação de recursos |
> | Ação | Microsoft.Network/virtualNetworks/subnets/resourceNavigationLinks/write | Criar um link de navegação de recursos ou atualizar um link de navegação de recursos existente |
> | Ação | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/delete | Exclui um Link de Associação de Serviço |
> | Ação | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/details/read | Obtém uma definição dos Detalhes do Link de Associação de Serviço |
> | Ação | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/read | Obtém uma definição do Link de Associação de Serviço |
> | Ação | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/validate/action | Valida um Link de Associação de Serviço |
> | Ação | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/write | Cria um Link de Associação de Serviço ou atualiza um existente |
> | Ação | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | Obter referências a todas as máquinas virtuais em uma sub-rede de rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/subnets/write | Criar uma sub-rede de rede virtual ou atualizar uma sub-rede de rede virtual existente |
> | Ação | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/delete | Excluir um consumidor de tráfego marcado |
> | Ação | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/read | Obter a definição de consumidor de tráfego marcado |
> | Ação | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/validate/action | Validar um consumidor de tráfego marcado |
> | Ação | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/write | Criar um consumidor de tráfego marcado ou atualiza um consumidor de tráfego marcado existente |
> | Ação | Microsoft.Network/virtualNetworks/usages/read | Obter os usos de IP para cada sub-rede da rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/virtualMachines/read | Obter referências a todas as máquinas virtuais em uma rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Excluir um emparelhamento de redes virtuais |
> | Ação | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | Obter uma definição de emparelhamento de rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Criar um emparelhamento de rede virtual ou atualizar um emparelhamento de rede virtual existente |
> | Ação | Microsoft.Network/virtualNetworks/write | Criar uma rede virtual ou atualizar uma rede virtual existente |
> | Ação | Microsoft.Network/virtualNetworkTaps/delete | Excluir Toque de Rede Virtual |
> | Ação | Microsoft.Network/virtualNetworkTaps/join/action | Adicionar toque de rede virtual |
> | Ação | Microsoft.Network/virtualNetworkTaps/networkInterfaceTapConfigurationProxies/delete | Exclui um proxy de configuração de toque do adaptador de rede. |
> | Ação | Microsoft.Network/virtualNetworkTaps/networkInterfaceTapConfigurationProxies/read | Obtém um proxy de configuração de toque do adaptador de rede. |
> | Ação | Microsoft.Network/virtualNetworkTaps/networkInterfaceTapConfigurationProxies/write | Cria um proxy de configuração de toque do adaptador de rede ou atualiza um proxy de configuração de toque do adaptador de rede existente. |
> | Ação | Microsoft.Network/virtualNetworkTaps/read | Obter Toque de Rede Virtual |
> | Ação | Microsoft.Network/virtualNetworkTaps/write | Criar ou atualizar Toque de Rede Virtual |
> | Ação | Microsoft.Network/virtualWans/delete | Excluir uma WAN Virtual |
> | Ação | Microsoft.Network/virtualWans/p2sVpnGatewayProxies/delete | Exclui um proxy P2SVpnGateway |
> | Ação | Microsoft.Network/virtualWans/p2sVpnGatewayProxies/read | Obtém uma definição de proxy P2SVpnGateway |
> | Ação | Microsoft.Network/virtualWans/p2sVpnGatewayProxies/write | Cria um proxy P2SVpnGateway ou atualiza um proxy P2SVpnGateway |
> | Ação | Microsoft.Network/virtualWans/p2sVpnServerConfigurations/delete | Exclui um P2SVpnServerConfiguration de WAN virtual |
> | Ação | Microsoft.Network/virtualWans/p2sVpnServerConfigurations/read | Obtém uma definição de P2SVpnServerConfiguration de WAN virtual |
> | Ação | Microsoft.Network/virtualWans/p2sVpnServerConfigurations/write | Cria um P2SVpnServerConfiguration de WAN virtual ou atualiza um P2SVpnServerConfiguration de WAN virtual existente |
> | Ação | Microsoft.Network/virtualWans/read | Obter uma WAN Virtual |
> | Ação | Microsoft.Network/virtualwans/supportedSecurityProviders/read | Obtém os provedores de segurança de VirtualWan com suporte. |
> | Ação | Microsoft.Network/virtualWans/virtualHubProxies/delete | Excluir um proxy de Hub Virtual |
> | Ação | Microsoft.Network/virtualWans/virtualHubProxies/read | Obter uma definição de proxy de Hub Virtual |
> | Ação | Microsoft.Network/virtualWans/virtualHubProxies/write | Criar um proxy de Hub Virtual ou atualizar um proxy de Hub Virtual |
> | Ação | Microsoft.Network/virtualWans/virtualHubs/read | Obtém todos os Hubs Virtuais que fazem referência a uma WAN Virtual. |
> | Ação | Microsoft.Network/virtualwans/vpnconfiguration/action | Obter uma configuração de VPN |
> | Ação | Microsoft.Network/virtualWans/vpnSiteProxies/delete | Excluir um proxy de VPN site |
> | Ação | Microsoft.Network/virtualWans/vpnSiteProxies/read | Obter uma definição de proxy de VPN site |
> | Ação | Microsoft.Network/virtualWans/vpnSiteProxies/write | Criar um proxy de VPN site ou atualizar um proxy de VPN site |
> | Ação | Microsoft.Network/virtualWans/vpnSites/read | Obtém todos os Sites VPN que fazem referência a uma WAN Virtual. |
> | Ação | Microsoft.Network/virtualWans/write | Criar ou atualizar um uma WAN Virtual |
> | Ação | Microsoft.Network/vpnGateways/delete | Exclui um VpnGateway. |
> | Ação | microsoft.network/vpngateways/listvpnconnectionshealth/action | Obtém a integridade da conexão para todas ou um subconjunto das conexões em um VpnGateway |
> | Ação | Microsoft.Network/vpnGateways/read | Obter um VpnGateway. |
> | Ação | microsoft.network/vpngateways/reset/action | Reinicia um VpnGateway |
> | Ação | microsoft.network/vpnGateways/vpnConnections/delete | Exclui um VpnConnection. |
> | Ação | microsoft.network/vpnGateways/vpnConnections/read | Obter um VpnConnection. |
> | Ação | microsoft.network/vpnGateways/vpnConnections/write | Implementar um VpnConnection. |
> | Ação | Microsoft.Network/vpnGateways/write | Implementar um VpnGateway. |
> | Ação | Microsoft.Network/vpnsites/delete | Excluir um recurso de VPN site. |
> | Ação | Microsoft.Network/vpnsites/read | Obter um recurso de VPN site. |
> | Ação | Microsoft.Network/vpnsites/write | Criar ou atualizar um recurso de VPN site. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | Verificar se determinado nome de recurso do Namespace está disponível no serviço NotificationHub. |
> | Ação | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | Obter a lista de descrição de regras de autorização de namespaces. |
> | Ação | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Excluir regra de autorização de namespace. A regra de autorização do namespace padrão não pode ser excluída.  |
> | Ação | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | Obter a cadeia de conexão para o namespace |
> | Ação | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | Obter a lista de descrição de regras de autorização de namespaces. |
> | Ação | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Regra de autorização de namespace regenera chave primária/secundária; especifique a chave que precisa ser regenerada |
> | Ação | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | Criar regras de autorização no nível do namespace e atualizar suas propriedades. Os direitos de acesso das regras de autorização; as chaves primárias e secundárias podem ser atualizadas. |
> | Ação | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | Verificar se determinado nome de NotificationHub está disponível em um Namespace. |
> | Ação | Microsoft.NotificationHubs/Namespaces/Delete | Excluir o recurso de namespace |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Obter a lista de regras de autorização do Hub de notificação |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Excluir regras de autorização do Hub de notificação |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Obter a cadeia de conexão para o Hub de notificação |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Obter a lista de regras de autorização do Hub de notificação |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Regra de autorização de Hub de notificação regenera chave primária/secundária; especifique a chave que precisa ser regenerada |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Criar regras de autorização do Hub de notificação e atualizar suas propriedades. Os direitos de acesso das regras de autorização; as chaves primárias e secundárias podem ser atualizadas. |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | Enviar notificação por push de teste. |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Excluir recurso de Hub de notificação |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | Obter lista de métrica de descrições de recurso de métrica do namespace |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Obter todas as credenciais PNS do Hub de notificação. Isso inclui credenciais WNS, MPNS, APNS, GCM e Baidu |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Obter lista de descrições de recursos do Hub de notificação |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Criar um Hub de notificação e atualizar suas propriedades. Suas propriedades incluem principalmente as credenciais PNS. Tempo de vida útil e regras de autorização |
> | Ação | Microsoft.NotificationHubs/Namespaces/read | Obter a lista de descrição do recurso de namespace |
> | Ação | Microsoft.NotificationHubs/Namespaces/write | Criar um recurso de namespace e atualizar suas propriedades. Marcas e Capacidade do namespace são as propriedades que podem ser atualizadas. |
> | Ação | Microsoft.NotificationHubs/operationResults/read | Retorna os resultados da operação para o provedor de Hubs de Notificação |
> | Ação | Microsoft.NotificationHubs/operations/read | Retorna uma lista de operações com suporte para o provedor de Hubs de Notificação |
> | Ação | Microsoft.NotificationHubs/register/action | Registra a assinatura do provedor de recursos NotifciationHubs e permite a criação de Namespaces e NotificationHubs |
> | Ação | Microsoft.NotificationHubs/unregister/action | Cancela a assinatura do provedor de recursos NotifciationHubs e permite a criação de Namespaces e NotificationHubs |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.OffAzure/HyperVSites/clusters/read | Obtém as propriedades de um cluster Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/clusters/read | Obtém as propriedades de um cluster Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/clusters/write | Cria ou atualiza do cluster Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/clusters/write | Cria ou atualiza do cluster Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/delete | Exclui o site do Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/delete | Exclui o site do Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/hosts/read | Obtém as propriedades de um host Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/hosts/read | Obtém as propriedades de um host Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/hosts/write | Cria ou atualiza o host do Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/hosts/write | Cria ou atualiza o host do Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/jobs/read | Obtém as propriedades de trabalhos Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/jobs/read | Obtém as propriedades de trabalhos Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/machines/read | Obtém as propriedades de máquinas Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/machines/read | Obtém as propriedades de máquinas Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/machines/stop/action | Interrompe as máquinas do Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/machines/stop/action | Interrompe as máquinas do Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Obtém as propriedades de status de operação Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Obtém as propriedades de status de operação Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/read | Obtém as propriedades de site Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/read | Obtém as propriedades de site Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/refresh/action | Atualiza os objetos dentro de um site do Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/refresh/action | Atualiza os objetos dentro de um site do Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Obtém as propriedades de um Hyper-V como contas |
> | Ação | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Obtém as propriedades de um Hyper-V como contas |
> | Ação | Microsoft.OffAzure/HyperVSites/usage/read | Obtém os usos de um site HYPER-V |
> | Ação | Microsoft.OffAzure/HyperVSites/usage/read | Obtém os usos de um site HYPER-V |
> | Ação | Microsoft.OffAzure/HyperVSites/write | Cria ou atualiza o site do Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/write | Cria ou atualiza o site do Hyper-V |
> | Ação | Microsoft.OffAzure/Operations/read | Ler as operações expostas |
> | Ação | Microsoft.OffAzure/VMwareSites/delete | Exclui o site de VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/delete | Exclui o site de VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/jobs/read | Obtém as propriedades de trabalhos VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/jobs/read | Obtém as propriedades de trabalhos VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/machines/read | Obtém as propriedades de máquinas VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/machines/read | Obtém as propriedades de máquinas VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/machines/stop/action | Interrompe as máquinas de VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/machines/stop/action | Interrompe as máquinas de VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/operationsstatus/read | Obtém as propriedades de status de operação VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/operationsstatus/read | Obtém as propriedades de status de operação VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/read | Obtém as propriedades de site VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/read | Obtém as propriedades de site VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/refresh/action | Atualiza os objetos dentro de um site do VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/refresh/action | Atualiza os objetos dentro de um site do VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/runasaccounts/read | Obtém as propriedades de um VMware como contas |
> | Ação | Microsoft.OffAzure/VMwareSites/runasaccounts/read | Obtém as propriedades de um VMware como contas |
> | Ação | Microsoft.OffAzure/VMwareSites/usage/read | Obtém os usos de um site VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/usage/read | Obtém os usos de um site VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/vcenters/read | Obtém as propriedades de um vCenter VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/vcenters/read | Obtém as propriedades de um vCenter VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/vcenters/write | Cria ou atualiza o vCenter VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/vcenters/write | Cria ou atualiza o vCenter VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/write | Cria ou atualiza o site VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/write | Cria ou atualiza o site VMware |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.OperationalInsights/linkTargets/read | Listar as contas existentes que não estão associadas uma assinatura do Azure. Para vincular essa assinatura do Azure a um workspace, use uma ID de cliente retornada pela operação na propriedade customer id da operação Criar workspace. |
> | Ação | microsoft.operationalinsights/operations/read | Lista todas as operações da API de Rest OperationalInsights disponíveis. |
> | Ação | Microsoft.OperationalInsights/register/action | Registrar uma assinatura de um provedor de recursos. |
> | Ação | Microsoft.OperationalInsights/workspaces/analytics/query/action | Pesquisar usando o novo mecanismo. |
> | Ação | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | Obter o esquema de pesquisa V2. |
> | Ação | Microsoft.OperationalInsights/workspaces/api/query/action | Pesquisar usando o novo mecanismo. |
> | Ação | Microsoft.OperationalInsights/workspaces/api/query/schema/read | Obter o esquema de pesquisa V2. |
> | Ação | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | Excluir escopo de configuração |
> | Ação | Microsoft.OperationalInsights/workspaces/configurationScopes/read | Obter escopo de configuração |
> | Ação | Microsoft.OperationalInsights/workspaces/configurationScopes/write | Definir o escopo da configuração |
> | Ação | Microsoft.OperationalInsights/workspaces/datasources/delete | Excluir fontes de dados em um workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/datasources/read | Obter fontes de dados em um workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/datasources/write | Criar/atualizar fontes de dados em um workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/delete | Excluir um workspace. Se o workspace foi vinculado a um workspace existente no momento da criação, o workspace a que ele foi vinculado não será excluído. |
> | Ação | Microsoft.OperationalInsights/workspaces/gateways/delete | Remove um gateway configurado para o espaço de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | Gerar certificado de registro para o workspace. Esse certificado é usado para conectar o Microsoft System Center Operations Manager ao workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | Desabilitar um pacote de inteligência para determinado workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | Habilitar um pacote de inteligência para determinado workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | Lista todos os pacotes de inteligência visíveis para determinado workspace e também indica se o pacote está habilitado ou desabilitado para esse workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/linkedServices/delete | Excluir serviços vinculados em um determinado workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/linkedServices/read | Obter serviços vinculados em um determinado workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/linkedServices/write | Criar/atualizar serviços vinculados em determinado workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/listKeys/action | Recuperar as chaves de lista para o workspace. Essas chaves são usadas para conectar agentes do Insights Operacionais da Microsoft ao workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/listKeys/read | Recuperar as chaves de lista para o workspace. Essas chaves são usadas para conectar agentes do Insights Operacionais da Microsoft ao workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/managementGroups/read | Obter os nomes e os metadados para grupos de gerenciamento do System Center Operations Manager conectados ao workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | Obter definições métricas no workspace |
> | Ação | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | Excluir as configurações de notificação do usuário para o workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/notificationSettings/read | Obter as configurações de notificação do usuário para o workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/notificationSettings/write | Definir as configurações de notificação do usuário para o workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/purge/action | Excluir dados especificados do workspace |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | Lê dados da tabela ADAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | Lê dados da tabela ADReplicationResult |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | Lê dados da tabela ADSecurityAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Alert/read | Lê dados da tabela de Alertas |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AlertHistory/read | Lê dados da tabela AlertHistory |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ApplicationInsights/read | Lê dados da tabela ApplicationInsights |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AuditLogs/read | Ler dados da tabela AuditLogs |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AzureActivity/read | Lê dados da tabela AzureActivity |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | Lê dados da tabela AzureMetrics |
> | Ação | Microsoft.OperationalInsights/workspaces/query/BoundPort/read | Lê dados da tabela BoundPort |
> | Ação | Microsoft.OperationalInsights/workspaces/query/CommonSecurityLog/read | Lê dados da tabela CommonSecurityLog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read | Lê dados da tabela ComputerGroup |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ConfigurationChange/read | Lê dados da tabela ConfigurationChange |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ConfigurationData/read | Lê dados da tabela ConfigurationData |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ContainerImageInventory/read | Lê dados da tabela ContainerImageInventory |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ContainerInventory/read | Lê dados da tabela ContainerInventory |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ContainerLog/read | Lê dados da tabela ContainerLog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ContainerServiceLog/read | Lê dados da tabela ContainerServiceLog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceAppCrash/read | Lê dados da tabela DeviceAppCrash |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceAppLaunch/read | Lê dados da tabela DeviceAppLaunch |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceCalendar/read | Lê dados da tabela DeviceCalendar |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceCleanup/read | Lê dados da tabela DeviceCleanup |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceConnectSession/read | Lê dados da tabela DeviceConnectSession |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceEtw/read | Lê dados da tabela DeviceEtw |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceHardwareHealth/read | Lê dados da tabela DeviceHardwareHealth |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceHealth/read | Lê dados da tabela DeviceHealth |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceHeartbeat/read | Lê dados da tabela DeviceHeartbeat |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeHeartbeat/read | Lê dados da tabela DeviceSkypeHeartbeat |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeSignIn/read | Lê dados da tabela DeviceSkypeSignIn |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceSleepState/read | Lê dados da tabela DeviceSleepState |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DHAppFailure/read | Lê dados da tabela DHAppFailure |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DHAppReliability/read | Lê dados da tabela DHAppReliability |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DHDriverReliability/read | Lê dados da tabela DHDriverReliability |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DHLogonFailures/read | Lê dados da tabela DHLogonFailures |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DHLogonMetrics/read | Lê dados da tabela DHLogonMetrics |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DHOSCrashData/read | Lê dados da tabela DHOSCrashData |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DHOSReliability/read | Lê dados da tabela DHOSReliability |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DHWipAppLearning/read | Lê dados da tabela DHWipAppLearning |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DnsEvents/read | Lê dados da tabela DnsEvents |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DnsInventory/read | Lê dados da tabela DnsInventory |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ETWEvent/read | Lê dados da tabela ETWEvent |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Event/read | Lê dados da tabela Event |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ExchangeAssessmentRecommendation/read | Lê dados da tabela ExchangeAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | Ler os dados da tabela ExchangeOnlineAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Heartbeat/read | Lê dados da tabela Heartbeat |
> | Ação | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | Lê dados da tabela IISAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | Lê dados da tabela InboundConnection |
> | Ação | Microsoft.OperationalInsights/workspaces/query/KubeEvents/read | Ler dados da tabela KubeEvents |
> | Ação | Microsoft.OperationalInsights/workspaces/query/KubeNodeInventory/read | Lê dados da tabela KubeNodeInventory |
> | Ação | Microsoft.OperationalInsights/workspaces/query/KubePodInventory/read | Lê dados da tabela KubePodInventory |
> | Ação | Microsoft.OperationalInsights/workspaces/query/KubeServices/read | Ler dados da tabela KubeEvents |
> | Ação | Microsoft.OperationalInsights/workspaces/query/LinuxAuditLog/read | Lê dados da tabela LinuxAuditLog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAApplication/read | Lê dados da tabela MAApplication |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealth/read | Lê dados da tabela MAApplicationHealth |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthAlternativeVersions/read | Ler dados da tabela MAApplicationHealthAlternativeVersions |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthIssues/read | Ler dados da tabela MAApplicationHealthIssues |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstance/read | Lê dados da tabela MAApplicationInstance |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstanceReadiness/read | Ler dados da tabela MAApplicationInstanceReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAApplicationReadiness/read | Lê dados da tabela MAApplicationReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADeploymentPlan/read | Lê dados da tabela MADeploymentPlan |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADevice/read | Lê dados da tabela MADevice |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealth/read | Ler dados da tabela MADevicePnPHealth |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthAlternativeVersions/read | Ler dados da tabela MADevicePnPHealthAlternativeVersions |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthIssues/read | Ler dados da tabela MADevicePnPHealthIssues |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADeviceReadiness/read | Lê dados da tabela MADeviceReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADriverInstanceReadiness/read | Ler dados da tabela MADriverInstanceReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADriverReadiness/read | Lê dados da tabela MADriverReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddin/read | Lê dados da tabela MAOfficeAddin |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealth/read | Lê dados da tabela MAOfficeAddinHealth |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthIssues/read | Ler dados da tabela MAOfficeAddinHealthIssues |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstance/read | Lê dados da tabela MAOfficeAddinInstance |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstanceReadiness/read | Ler dados da tabela MAOfficeAddinInstanceReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinReadiness/read | Lê dados da tabela MAOfficeAddinReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeApp/read | Lê dados da tabela MAOfficeApp |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppHealth/read | Lê dados da tabela MAOfficeAppHealth |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstance/read | Lê dados da tabela MAOfficeAppInstance |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppReadiness/read | Lê dados da tabela MAOfficeAppReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeBuildInfo/read | Lê dados da tabela MAOfficeBuildInfo |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessment/read | Lê dados da tabela MAOfficeCurrencyAssessment |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessmentDailyCounts/read | Lê dados da tabela MAOfficeCurrencyAssessmentDailyCounts |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatus/read | Lê dados da tabela MAOfficeDeploymentStatus |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealth/read | Ler dados da tabela MAOfficeMacroHealth |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealthIssues/read | Ler dados da tabela MAOfficeMacroHealthIssues |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueInstanceReadiness/read | Ler dados da tabela MAOfficeMacroIssueInstanceReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueReadiness/read | Lê dados da tabela MAOfficeMacroIssueReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroSummary/read | Lê dados da tabela MAOfficeMacroSummary |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuite/read | Lê dados da tabela MAOfficeSuite |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuiteInstance/read | Lê dados da tabela MAOfficeSuiteInstance |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAProposedPilotDevices/read | Lê dados da tabela MAProposedPilotDevices |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAWindowsBuildInfo/read | Lê dados da tabela MAWindowsBuildInfo |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessment/read | Lê dados da tabela MAWindowsCurrencyAssessment |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessmentDailyCounts/read | Lê dados da tabela MAWindowsCurrencyAssessmentDailyCounts |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatus/read | Lê dados da tabela MAWindowsDeploymentStatus |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAWindowsSysReqInstanceReadiness/read | Ler dados da tabela MAWindowsSysReqInstanceReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/NetworkMonitoring/read | Lê dados da tabela NetworkMonitoring |
> | Ação | Microsoft.OperationalInsights/workspaces/query/OfficeActivity/read | Lê dados da tabela OfficeActivity |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Operation/read | Lê dados da tabela Operation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/OutboundConnection/read | Lê dados da tabela OutboundConnection |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Perf/read | Lê dados da tabela Perf |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ProtectionStatus/read | Lê dados da tabela ProtectionStatus |
> | Ação | Microsoft.OperationalInsights/workspaces/query/read | Executar consultas dos dados no workspace |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ReservedAzureCommonFields/read | Lê dados da tabela ReservedAzureCommonFields |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ReservedCommonFields/read | Lê dados da tabela ReservedCommonFields |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SCCMAssessmentRecommendation/read | Lê dados da tabela SCCMAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SCOMAssessmentRecommendation/read | Lê dados da tabela SCOMAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read | Lê dados da tabela SecurityAlert |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read | Lê dados da tabela SecurityBaseline |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SecurityBaselineSummary/read | Lê dados da tabela SecurityBaselineSummary |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SecurityDetection/read | Lê dados da tabela SecurityDetection |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SecurityEvent/read | Lê dados da tabela SecurityEvent |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ServiceFabricOperationalEvent/read | Lê dados da tabela ServiceFabricOperationalEvent |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableActorEvent/read | Lê dados da tabela ServiceFabricReliableActorEvent |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableServiceEvent/read | Lê dados da tabela ServiceFabricReliableServiceEvent |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SfBAssessmentRecommendation/read | Lê dados da tabela SfBAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SfBOnlineAssessmentRecommendation/read | Ler os dados da tabela SfBOnlineAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SharePointOnlineAssessmentRecommendation/read | Ler os dados da tabela SharePointOnlineAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | Ler dados da tabela SigninLogs |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SPAssessmentRecommendation/read | Lê dados da tabela SPAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | Lê dados da tabela SQLAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SQLQueryPerformance/read | Lê dados da tabela SQLQueryPerformance |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Syslog/read | Lê dados da tabela Syslog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SysmonEvent/read | Lê dados da tabela SysmonEvent |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read | Lê dados de qualquer log personalizado |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UAApp/read | Lê dados da tabela UAApp |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UAComputer/read | Lê dados da tabela UAComputer |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UAComputerRank/read | Lê dados da tabela UAComputerRank |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UADriver/read | Lê dados da tabela UADriver |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UADriverProblemCodes/read | Lê dados da tabela UADriverProblemCodes |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UAFeedback/read | Lê dados da tabela UAFeedback |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UAHardwareSecurity/read | Lê dados da tabela UAHardwareSecurity |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UAIESiteDiscovery/read | Lê dados da tabela UAIESiteDiscovery |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UAOfficeAddIn/read | Lê dados da tabela UAOfficeAddIn |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UAProposedActionPlan/read | Lê dados da tabela UAProposedActionPlan |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UASysReqIssue/read | Lê dados da tabela UASysReqIssue |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UAUpgradedComputer/read | Lê dados da tabela UAUpgradedComputer |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Update/read | Lê dados da tabela Update |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read | Lê dados da tabela UpdateRunProgress |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read | Lê dados da tabela UpdateSummary |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Usage/read | Lê dados da tabela Usage |
> | Ação | Microsoft.OperationalInsights/workspaces/query/VMBoundPort/read | Lê dados da tabela VMBoundPort |
> | Ação | Microsoft.OperationalInsights/workspaces/query/VMConnection/read | Lê dados da tabela VMConnection |
> | Ação | Microsoft.OperationalInsights/workspaces/query/W3CIISLog/read | Lê dados da tabela W3CIISLog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WaaSDeploymentStatus/read | Lê dados da tabela WaaSDeploymentStatus |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WaaSInsiderStatus/read | Lê dados da tabela WaaSInsiderStatus |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WaaSUpdateStatus/read | Lê dados da tabela WaaSUpdateStatus |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WDAVStatus/read | Lê dados da tabela WDAVStatus |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WDAVThreat/read | Lê dados da tabela WDAVThreat |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | Lê dados da tabela WindowsClientAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WindowsEvent/read | Ler dados da tabela WindowsEvent |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WindowsFirewall/read | Lê dados da tabela WindowsFirewall |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | Lê dados da tabela WindowsServerAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WireData/read | Lê dados da tabela WireData |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WorkloadMonitoringPerf/read | Dados de leitura da tabela WorkloadMonitoringPerf |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WUDOAggregatedStatus/read | Lê dados da tabela WUDOAggregatedStatus |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WUDOStatus/read | Lê dados da tabela WUDOStatus |
> | Ação | Microsoft.OperationalInsights/workspaces/read | Obter um workspace existente |
> | Ação | Microsoft.OperationalInsights/workspaces/regeneratesharedkey/action | Regenera a chave compartilhada do espaço de trabalho especificado |
> | Ação | Microsoft.OperationalInsights/workspaces/savedSearches/delete | Excluir uma consulta de pesquisa salva |
> | Ação | Microsoft.OperationalInsights/workspaces/savedSearches/read | Obter uma consulta de pesquisa salva |
> | Ação | microsoft.operationalinsights/workspaces/savedsearches/results/read | Obter resultados de pesquisas salvos. Preteridos |
> | Ação | Microsoft.OperationalInsights/workspaces/savedSearches/write | Criar uma consulta de pesquisa salva |
> | Ação | Microsoft.OperationalInsights/workspaces/schema/read | Obter o esquema de pesquisa do workspace.  O esquema de pesquisa inclui os campos expostos e seus tipos. |
> | Ação | Microsoft.OperationalInsights/workspaces/search/action | Executar uma consulta de pesquisa |
> | Ação | microsoft.operationalinsights/workspaces/search/read | Obtenha os resultados da pesquisa. Preterido. |
> | Ação | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Recupera as chaves compartilhadas do workspace. Essas chaves são usadas para conectar agentes do Insights Operacionais da Microsoft ao workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Recupera as chaves compartilhadas do workspace. Essas chaves são usadas para conectar agentes do Insights Operacionais da Microsoft ao workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | Excluir uma configuração de armazenamento. Isso interromperá a leitura dos dados da conta de armazenamento pelo Insights Operacionais. |
> | Ação | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | Obter uma configuração de armazenamento. |
> | Ação | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | Criar uma nova configuração de armazenamento. Essas configurações são usadas para extrair dados de um local em uma conta de armazenamento existente. |
> | Ação | Microsoft.OperationalInsights/workspaces/upgradetranslationfailures/read | Obter o log de falha de tradução de atualização da pesquisa para o espaço de trabalho |
> | Ação | Microsoft.OperationalInsights/workspaces/usages/read | Obter dados de uso de um workspace e a quantidade de dados lidos pelo workspace. |
> | Ação | Microsoft.OperationalInsights/workspaces/write | Criar um novo workspace ou links para um workspace existente fornecendo a ID do cliente do workspace existente. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.OperationsManagement/managementAssociations/delete | Excluir a Associação de Gerenciamento existente |
> | Ação | Microsoft.OperationsManagement/managementAssociations/read | Obter Associação de Gerenciamento existente |
> | Ação | Microsoft.OperationsManagement/managementAssociations/write | Crie uma nova Associação de Gerenciamento |
> | Ação | Microsoft.OperationsManagement/managementConfigurations/delete | Excluir Configuração de Gerenciamento existente |
> | Ação | Microsoft.OperationsManagement/managementConfigurations/read | Obter Configuração de Gerenciamento existente |
> | Ação | Microsoft.OperationsManagement/managementConfigurations/write | Criar uma nova Configuração de Gerenciamento |
> | Ação | Microsoft.OperationsManagement/register/action | Registrar uma assinatura de um provedor de recursos. |
> | Ação | Microsoft.OperationsManagement/solutions/delete | Excluir a solução do OMS existente |
> | Ação | Microsoft.OperationsManagement/solutions/read | Obter solução OMS de saída |
> | Ação | Microsoft.OperationsManagement/solutions/write | Criar nova solução do OMS |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.PolicyInsights/asyncOperationResults/read | Obtém o resultado da operação assíncrona. |
> | Ação | Microsoft.PolicyInsights/policyEvents/queryResults/action | Consulta informações sobre eventos de política. |
> | Ação | Microsoft.PolicyInsights/policyEvents/queryResults/read | Consulta informações sobre eventos de política. |
> | Ação | Microsoft.PolicyInsights/policyStates/queryResults/action | Consulta informações sobre estados de política. |
> | Ação | Microsoft.PolicyInsights/policyStates/queryResults/read | Consulta informações sobre estados de política. |
> | Ação | Microsoft.PolicyInsights/policyStates/summarize/action | Consulta informações de resumo sobre os últimos estados de política. |
> | Ação | Microsoft.PolicyInsights/policyStates/summarize/read | Consulta informações de resumo sobre os últimos estados de política. |
> | Ação | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | Dispara uma nova avaliação de conformidade para o escopo selecionado. |
> | Ação | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | Consultar informações sobre os recursos exigidos por políticas de DeployIfNotExists. |
> | Ação | Microsoft.PolicyInsights/register/action | Registra o provedor de recursos de insights da política e habilita ações nele. |
> | Ação | Microsoft.PolicyInsights/remediations/cancel/action | Cancelar correções da política em andamento. |
> | Ação | Microsoft.PolicyInsights/remediations/delete | Excluir correções da política. |
> | Ação | Microsoft.PolicyInsights/remediations/listDeployments/read | Lista as implantações exigidas por uma correção de política. |
> | Ação | Microsoft.PolicyInsights/remediations/read | Obter correções de política. |
> | Ação | Microsoft.PolicyInsights/remediations/write | Criar ou atualizar correções de política. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Portal/dashboards/delete | Remover o painel da assinatura. |
> | Ação | Microsoft.Portal/dashboards/read | Ler os painéis da assinatura. |
> | Ação | Microsoft.Portal/dashboards/write | Adicionar ou modificar o painel para uma assinatura. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.PowerBIDedicated/capacities/checkNameAvailability/action | Verificar se o nome da capacidade dedicada do Power BI é válido e não está em uso. |
> | Ação | Microsoft.PowerBIDedicated/capacities/delete | Excluir a capacidade dedicada do Power BI. |
> | Ação | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/logDefinitions/read | Obtém os logs disponíveis para Capacidades Dedicadas do Power BI |
> | Ação | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para a capacidade dedicada do Power BI. |
> | Ação | Microsoft.PowerBIDedicated/capacities/read | Recuperar as informações da Capacidade Dedicada especificada do Power BI. |
> | Ação | Microsoft.PowerBIDedicated/capacities/resume/action | Retoma a Capacidade. |
> | Ação | Microsoft.PowerBIDedicated/capacities/skus/read | Recuperar informações de SKU disponíveis para a Capacidade |
> | Ação | Microsoft.PowerBIDedicated/capacities/suspend/action | Suspende a Capacidade. |
> | Ação | Microsoft.PowerBIDedicated/capacities/write | Criar ou atualizar a capacidade dedicada especificada do Power BI. |
> | Ação | Microsoft.PowerBIDedicated/locations/operationresults/read | Recuperar as informações do resultado da operação especificada. |
> | Ação | Microsoft.PowerBIDedicated/locations/operationstatuses/read | Recuperar as informações do status da operação especificada. |
> | Ação | Microsoft.PowerBIDedicated/operations/read | Recuperar as informações das operações |
> | Ação | Microsoft.PowerBIDedicated/register/action | Registra o provedor de recursos dedicado do Power BI. |
> | Ação | Microsoft.PowerBIDedicated/skus/read | Recuperar as informações de SKUs |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp é uma operação interna usada pelo serviço |
> | Ação | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp é uma operação interna usada pelo serviço |
> | Ação | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Ação | Microsoft.RecoveryServices/locations/backupStatus/action | Verificar o status de backup para os Cofres dos Serviços de Recuperação |
> | Ação | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Validar recursos |
> | Ação | Microsoft.RecoveryServices/locations/operationStatus/read | Obtém o Status da operação para uma determinada operação |
> | Ação | Microsoft.RecoveryServices/operations/read | Operação retorna a lista de operações para um provedor de recursos |
> | Ação | Microsoft.RecoveryServices/register/action | Registrar assinatura de um determinado provedor de recursos |
> | Ação | Microsoft.RecoveryServices/Vaults/backupconfig/read | Retornar a configuração para cofre dos Serviços de Recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupconfig/write | Atualizar configuração para o cofre dos Serviços de Recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupEngines/read | Retornar todos os servidores de gerenciamento de backup registrados com cofre. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/delete | Excluir uma Intenção de Proteção de backup |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Obter uma Intenção de Proteção de backup |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Criar uma Intenção de Proteção de backup |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Retornar o status da operação |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Obter todos os contêineres protegidos |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/delete | Exclui o Contêiner registrado |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Consultar cargas de trabalho em um contêiner |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Obter todos os itens em um contêiner |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Obter o resultado da operação executada no contêiner de proteção. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Executar um backup para um item protegido. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Excluir item protegido |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Obter o resultado da operação executada em itens protegidos. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Retornar o status da operação executada em itens protegidos. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Retornar detalhes do objeto do item protegido |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Provisionar recuperação de item instantânea para item protegido |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Obter pontos de recuperação para itens protegidos. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Restaurar pontos de recuperação para itens protegidos. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Revogar a recuperação de item instantânea para item protegido |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Criar um item protegido de backup |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Retornar todos os contêineres registrados |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Criar um contêiner registrado |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Atualizar a lista de contêineres |
> | Ação | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Cancelar o trabalho |
> | Ação | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Retornar o resultado da operação do trabalho. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupJobs/read | Retornar todos os objetos de trabalho |
> | Ação | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportar trabalhos |
> | Ação | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Retornar o resultado da operação do trabalho de exportação. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Retornar metadados de gerenciamento de backup para o cofre dos Serviços de Recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Retornar o resultado da operação de backup para o cofre dos Serviços de Recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupOperations/read | Retornar o status da operação de backup para o cofre dos Serviços de Recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupPolicies/delete | Excluir uma política de proteção |
> | Ação | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Obter os resultados da operação de política. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Obter o status da operação de política. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Retornar todas as políticas de proteção |
> | Ação | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Criar uma política de proteção |
> | Ação | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Retornar a lista de todos os itens que podem ser protegidos. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Retornar a lista de todos os itens protegidos. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Retornar todos os contêineres pertencentes à assinatura |
> | Ação | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Listar todas as Intenções de Proteção de backup |
> | Ação | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/action | Retornar a informação de PIN de segurança para o cofre dos Serviços de Recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Retornar cofre de armazenamento para o cofre dos Serviços de Recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupstorageconfig/write | Atualizar cofre de armazenamento para o cofre dos Serviços de Recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Retornar resumos de itens protegidos e servidores protegidos para os Serviços de Recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Validar operação no Item protegido |
> | Ação | Microsoft.RecoveryServices/Vaults/certificates/write | A operação Atualizar certificado do recurso atualiza o certificado de credencial de cofre/recurso. |
> | Ação | Microsoft.RecoveryServices/Vaults/delete | A operação Excluir cofre exclui o recurso do Azure do tipo 'cofre' especificado |
> | Ação | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
> | Ação | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
> | Ação | Microsoft.RecoveryServices/Vaults/extendedInformation/write | A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
> | Ação | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obter os alertas para o cofre dos Serviços de Recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Resolver o alerta. |
> | Ação | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | Obter a configuração de notificação do cofre dos Serviços de Recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | Configurar notificações por email para o cofre dos Serviços de Recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/diagnosticSettings/read | Diagnóstico do Backup do Azure |
> | Ação | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/diagnosticSettings/write | Diagnóstico do Backup do Azure |
> | Ação | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/logDefinitions/read | Logs do Backup do Azure |
> | Ação | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/metricDefinitions/read | Métricas do Backup do Azure |
> | Ação | Microsoft.RecoveryServices/Vaults/read | A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre' |
> | Ação | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | A operação Cancelar o registro de contêiner pode ser usada para cancelar o registro de um contêiner. |
> | Ação | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A operação Obter resultados da operação pode ser usada para obter o status da operação e o resultado da operação enviada de forma assíncrona |
> | Ação | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A operação Obter contêineres pode ser usada para obter os contêineres registrados para um recurso. |
> | Ação | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | A operação Registrar o contêiner de serviço pode ser usada para registrar um contêiner com o Serviço de Recuperação. |
> | Ação | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Ler configurações de alertas |
> | Ação | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | Criar ou atualizar as configurações de alertas |
> | Ação | Microsoft.RecoveryServices/vaults/replicationEvents/read | Ler quaisquer eventos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Verificar consistência da malha |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | Excluir malhas |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | Implantar imagem do servidor de processo |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | Migrar do Fabric para o AAD |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Ler quaisquer malhas |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Reassociar gateway |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | Remover malha |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Renovar Certificado para malha do Microsoft Azure |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | Ler quaisquer redes lógicas |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Ler quaisquer redes |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | Excluir quaisquer mapeamentos de rede |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Ler quaisquer mapeamentos de rede |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | Criar ou atualizar quaisquer mapeamentos de rede |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | Descobrir item que pode ser protegido |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Ler quaisquer contêineres de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | Remover o contêiner de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Ler quaisquer itens que podem ser protegidos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Aplicar ponto de recuperação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Excluir quaisquer itens protegidos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Confirmação de failover |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Failover planejado |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Ler quaisquer itens protegidos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Ler quaisquer pontos de recuperação de replicação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | Remover item protegido |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Reparar replicação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Proteger item protegido novamente |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | Enviar Comentário |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | Ler qualquer tamanhos de computação de destino |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Failover de Teste |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Limpeza do Failover de teste |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Failover |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Atualizar serviço de mobilidade |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | Criar ou atualizar os itens protegidos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | Excluir quaisquer mapeamentos de contêiner de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Ler quaisquer mapeamentos de contêiner de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | Remover o mapeamento de contêiner de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | Criar ou atualizar os mapeamentos de contêiner de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Alterar contêiner de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | Criar ou atualizar quaisquer contêineres de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Excluir quaisquer provedores dos Serviços de Recuperação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Ler qualquer provedores de Serviços de Recuperação do Microsoft Azure |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Atualizar provedor |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Remover o provedor dos Serviços de Recuperação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Criar ou atualizar Provedores de Serviços de Recuperação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Ler quaisquer classificações de armazenamento |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | Excluir mapeamentos de classificação de armazenamento |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Ler quaisquer mapeamentos de classificação de armazenamento |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | Criar ou atualizar os mapeamentos de classificação de armazenamento |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | Ler quaisquer vCenters |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Ler quaisquer vCenters |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | Cria ou atualiza quaisquer vCenters |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/write | Criar ou atualizar malhas |
> | Ação | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | Cancelar trabalho |
> | Ação | Microsoft.RecoveryServices/vaults/replicationJobs/read | Ler todos os trabalhos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | Reiniciar o trabalho |
> | Ação | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | Retomar o trabalho |
> | Ação | Microsoft.RecoveryServices/vaults/replicationNetworkMappings/read | Ler quaisquer mapeamentos de rede |
> | Ação | Microsoft.RecoveryServices/vaults/replicationNetworks/read | Ler quaisquer redes |
> | Ação | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | Excluir políticas |
> | Ação | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Ler quaisquer políticas |
> | Ação | Microsoft.RecoveryServices/vaults/replicationPolicies/write | Criar ou atualizar as políticas |
> | Ação | Microsoft.RecoveryServices/vaults/replicationProtectedItems/read | Ler quaisquer itens protegidos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationProtectionContainerMappings/read | Ler quaisquer mapeamentos de contêiner de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationProtectionContainers/read | Ler quaisquer contêineres de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | Excluir planos de recuperação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Plano de recuperação de confirmação de failover |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Plano de recuperação de failover planejado |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Ler quaisquer planos de recuperação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Proteja plano de recuperação novamente |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Testar plano de recuperação de failover |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Testar plano de recuperação de limpeza do failover |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plano de recuperação de failover |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | Criar ou atualizar planos de recuperação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryServicesProviders/read | Ler qualquer provedores de Serviços de Recuperação do Microsoft Azure |
> | Ação | Microsoft.RecoveryServices/vaults/replicationStorageClassificationMappings/read | Ler quaisquer mapeamentos de classificação de armazenamento |
> | Ação | Microsoft.RecoveryServices/vaults/replicationStorageClassifications/read | Ler quaisquer classificações de armazenamento |
> | Ação | Microsoft.RecoveryServices/vaults/replicationUsages/read | Ler usos de replicação do cofre |
> | Ação | Microsoft.RecoveryServices/vaults/replicationVaultHealth/read | Ler qualquer integridade de replicação do cofre |
> | Ação | Microsoft.RecoveryServices/vaults/replicationVaultHealth/refresh/action | Atualizar integridade de cofre |
> | Ação | Microsoft.RecoveryServices/vaults/replicationvCenters/read | Ler quaisquer vCenters |
> | Ação | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Retornar a informação de token para o cofre dos Serviços de Recuperação. |
> | Ação | Microsoft.RecoveryServices/vaults/usages/read | Ler usos de cofre |
> | Ação | Microsoft.RecoveryServices/Vaults/usages/read | Retornar os detalhes de uso para um cofre dos Serviços de Recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A operação de token do cofre pode ser usada a fim de obter o token do cofre para operações de back-end no nível do cofre. |
> | Ação | Microsoft.RecoveryServices/Vaults/write | A operação Criar cofre cria um recurso do Azure do tipo 'cofre' |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Relay/checkNameAvailability/action | Verificar a disponibilidade do namespace em determinada assinatura. |
> | Ação | Microsoft.Relay/checkNamespaceAvailability/action | Verificar a disponibilidade do namespace em determinada assinatura. Essa API está preterida, use CheckNameAvailabiltiy. |
> | Ação | Microsoft.Relay/namespaces/authorizationRules/action | Atualizar regra de autorização de namespace. Essa API está preterida. Use uma chamada PUT para atualizar a regra de autorização de namespace. Esta operação não tem suporte na versão da API 2017-04-01. |
> | Ação | Microsoft.Relay/namespaces/authorizationRules/delete | Excluir regra de autorização de namespace. A regra de autorização do namespace padrão não pode ser excluída.  |
> | Ação | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | Obter a cadeia de conexão para o namespace |
> | Ação | Microsoft.Relay/namespaces/authorizationRules/read | Obter a lista de descrição de regras de autorização de namespaces. |
> | Ação | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | Regenerar a chave primária ou secundária para o recurso |
> | Ação | Microsoft.Relay/namespaces/authorizationRules/write | Criar regras de autorização no nível do namespace e atualizar suas propriedades. Os direitos de acesso das regras de autorização; as chaves primárias e secundárias podem ser atualizadas. |
> | Ação | Microsoft.Relay/namespaces/Delete | Excluir o recurso de namespace |
> | Ação | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Obter as chaves de regras de autorização para o namespace primário de Recuperação de Desastre |
> | Ação | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | Obter regras de autorização do namespace primário de recuperação de desastre |
> | Ação | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | Desabilitar a Recuperação de desastre e interromper a replicação de alterações de namespaces primários para secundários. |
> | Ação | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Verificar a disponibilidade de alias de namespace sob determinada assinatura. |
> | Ação | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | Excluir a configuração da recuperação de desastre associada ao namespace. Essa operação somente pode ser invocada por meio do namespace primário. |
> | Ação | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | Invocar um failover de GEO DR e reconfigurar o alias de namespace para apontar para o namespace secundário. |
> | Ação | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | Obter a configuração da Recuperação de Desastre associada ao namespace. |
> | Ação | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | Criar ou atualizar a configuração de Recuperação de Desastre associada ao namespace. |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | Operação para atualizar o HybridConnection. Esta operação não tem suporte na versão da API 2017-04-01. Regras de autorização. Use uma chamada PUT para atualizar a Regra de Autorização. |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | Operação para excluir regras de autorização HybridConnection |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | Obter a cadeia de conexão para HybridConnection |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  Obter a lista das Regras d Autorização do HybridConnection |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | Regenerar a chave primária ou secundária para o recurso |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | Criar regras de autorização HybridConnection e atualizar suas propriedades. Os Direitos de Acesso das Regras de Autorização podem ser atualizados. |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/Delete | Operação para excluir o recurso HybridConnection |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/read | Obter lista de descrições de recursos HybridConnection |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/write | Criar ou atualizar propriedades HybridConnection. |
> | Ação | Microsoft.Relay/namespaces/messagingPlan/read | Obter o Plano de Mensagens para um namespace.<br>Essa API está preterida.<br>As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso Namespace (pai) nas versões posteriores da API.<br>Esta operação não tem suporte na versão da API 2017-04-01. |
> | Ação | Microsoft.Relay/namespaces/messagingPlan/write | Atualizar o Plano de Mensagens para um namespace.<br>Essa API está preterida.<br>As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso Namespace (pai) nas versões posteriores da API.<br>Esta operação não tem suporte na versão da API 2017-04-01. |
> | Ação | Microsoft.Relay/namespaces/operationresults/read | Obter o status da operação de Namespace |
> | Ação | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Obter lista de métrica de descrições de recurso de métrica do namespace |
> | Ação | Microsoft.Relay/namespaces/read | Obter a lista de descrição do recurso de namespace |
> | Ação | Microsoft.Relay/namespaces/removeAcsNamepsace/action | Remove o namespace de ACS |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | Operação para atualizar o WcfRelay. Esta operação não tem suporte na versão da API 2017-04-01. Regras de autorização. Use uma chamada PUT para atualizar a Regra de Autorização. |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | Operação para excluir regras de autorização WcfRelay |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | Obter a cadeia de conexão para WcfRelay |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  Obter a lista de Regras de Autorização do WcfRelay |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | Regenerar a chave primária ou secundária para o recurso |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | Criar regras de autorização WcfRelay e atualizar suas propriedades. Os Direitos de Acesso das Regras de Autorização podem ser atualizados. |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/Delete | Operação para excluir o recurso WcfRelay |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/read | Obter lista de descrições de recursos WcfRelay |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/write | Criar ou atualizar propriedades WcfRelay. |
> | Ação | Microsoft.Relay/namespaces/write | Criar um recurso de namespace e atualizar suas propriedades. Marcas e Capacidade do namespace são as propriedades que podem ser atualizadas. |
> | Ação | Microsoft.Relay/operations/read | Obter Operações |
> | Ação | Microsoft.Relay/register/action | Registrar a assinatura do provedor de recursos Relay e permitir a criação de recursos Relay |
> | Ação | Microsoft.Relay/unregister/action | Registrar a assinatura do provedor de recursos Relay e permitir a criação de recursos Relay |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | Obter o status de disponibilidade do recurso especificado |
> | Ação | Microsoft.ResourceHealth/AvailabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Ação | Microsoft.ResourceHealth/events/read | Obtém Eventos de Integridade do Serviço para uma determinada assinatura |
> | Ação | Microsoft.Resourcehealth/healthevent/action | Indicar a alteração no estado de integridade para o recurso especificado |
> | Ação | Microsoft.Resourcehealth/healthevent/Activated/action | Indicar a alteração no estado de integridade para o recurso especificado |
> | Ação | Microsoft.Resourcehealth/healthevent/InProgress/action | Indicar a alteração no estado de integridade para o recurso especificado |
> | Ação | Microsoft.Resourcehealth/healthevent/Pending/action | Indicar a alteração no estado de integridade para o recurso especificado |
> | Ação | Microsoft.Resourcehealth/healthevent/Resolved/action | Indicar a alteração no estado de integridade para o recurso especificado |
> | Ação | Microsoft.Resourcehealth/healthevent/Updated/action | Indicar a alteração no estado de integridade para o recurso especificado |
> | Ação | Microsoft.ResourceHealth/impactedResources/read | Obtém os Recursos Afetados para uma determinada assinatura |
> | Ação | Microsoft.ResourceHealth/Operations/read | Obter as operações disponíveis para o Microsoft ResourceHealth |
> | Ação | Microsoft.ResourceHealth/register/action | Registrar a assinatura do Microsoft ResourceHealth |
> | Ação | Microsoft.ResourceHealth/unregister/action | Cancela o registro da assinatura para o Microsoft ResourceHealth |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Resources/checkPolicyCompliance/action | Verifica o status de conformidade de um determinado recurso em relação às políticas de recurso. |
> | Ação | Microsoft.Resources/checkResourceName/action | Verificar a validade do nome do recurso. |
> | Ação | Microsoft.Resources/deployments/cancel/action | Cancelar uma implantação. |
> | Ação | Microsoft.Resources/deployments/delete | Excluir uma implantação. |
> | Ação | Microsoft.Resources/deployments/operations/read | Obter ou lista operações de implantação. |
> | Ação | Microsoft.Resources/deployments/read | Obter ou lista implantações. |
> | Ação | Microsoft.Resources/deployments/validate/action | Validar uma implantação. |
> | Ação | Microsoft.Resources/deployments/write | Criar ou atualizar uma implantação. |
> | Ação | Microsoft.Resources/links/delete | Excluir um link de recurso. |
> | Ação | Microsoft.Resources/links/read | Obter ou listar links de recursos. |
> | Ação | Microsoft.Resources/links/write | Criar ou atualizar um link de recurso. |
> | Ação | Microsoft.Resources/marketplace/purchase/action | Comprar um recurso do marketplace. |
> | Ação | Microsoft.Resources/providers/read | Obter a lista de provedores. |
> | Ação | Microsoft.Resources/resources/read | Obter a lista de recursos com base em filtros. |
> | Ação | Microsoft.Resources/subscriptions/locations/read | Obter a lista de locais com suporte. |
> | Ação | Microsoft.Resources/subscriptions/operationresults/read | Obter os resultados da operação da assinatura. |
> | Ação | Microsoft.Resources/subscriptions/providers/read | Obter ou listar provedores de recursos. |
> | Ação | Microsoft.Resources/subscriptions/read | Obter a lista de assinaturas. |
> | Ação | Microsoft.Resources/subscriptions/resourceGroups/delete | Excluir um grupo de recursos e todos os seus recursos. |
> | Ação | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | Obter ou lista operações de implantação. |
> | Ação | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | Obter ou listar o status da operação de implantação. |
> | Ação | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | Obter ou lista implantações. |
> | Ação | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | Criar ou atualizar uma implantação. |
> | Ação | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | Mover recursos de um grupo de recursos para outro. |
> | Ação | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Ação | Microsoft.Resources/subscriptions/resourcegroups/resources/read | Obter os recursos do grupo de recursos. |
> | Ação | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | Validar a movimentação de recursos de um grupo de recursos para outro. |
> | Ação | Microsoft.Resources/subscriptions/resourceGroups/write | Criar ou atualizar um grupo de recursos. |
> | Ação | Microsoft.Resources/subscriptions/resources/read | Obter os recursos de uma assinatura. |
> | Ação | Microsoft.Resources/subscriptions/tagNames/delete | Excluir uma marcação de assinatura. |
> | Ação | Microsoft.Resources/subscriptions/tagNames/read | Obter ou listar marcações de assinatura. |
> | Ação | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | Excluir um valor de marcação de assinatura. |
> | Ação | Microsoft.Resources/subscriptions/tagNames/tagValues/read | Obter ou listar valores de marcação de assinatura. |
> | Ação | Microsoft.Resources/subscriptions/tagNames/tagValues/write | Adicionar um valor de marcação de assinatura. |
> | Ação | Microsoft.Resources/subscriptions/tagNames/write | Adicionar uma marcação de assinatura. |
> | Ação | Microsoft.Resources/tenants/read | Obter a lista de locatários. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Scheduler/jobcollections/delete | Excluir coleção de trabalhos. |
> | Ação | Microsoft.Scheduler/jobcollections/disable/action | Desabilita coleção de trabalhos. |
> | Ação | Microsoft.Scheduler/jobcollections/enable/action | Habilita coleção de trabalhos. |
> | Ação | Microsoft.Scheduler/jobcollections/jobs/delete | Excluir trabalho. |
> | Ação | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | Gerar a definição de aplicativo lógico com base em um trabalho do Agendador. |
> | Ação | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | Obtém o histórico de trabalhos. |
> | Ação | Microsoft.Scheduler/jobcollections/jobs/read | Obter o trabalho. |
> | Ação | Microsoft.Scheduler/jobcollections/jobs/run/action | Executa o trabalho. |
> | Ação | Microsoft.Scheduler/jobcollections/jobs/write | Criar ou atualizar o trabalho. |
> | Ação | Microsoft.Scheduler/jobcollections/read | Obter coleção de trabalhos |
> | Ação | Microsoft.Scheduler/jobcollections/write | Criar ou atualizar a coleção de trabalhos. |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Search/checkNameAvailability/action | Verificar a disponibilidade do nome do serviço. |
> | Ação | Microsoft.Search/operations/read | Lista todas as operações disponíveis do provedor Microsoft.Search. |
> | Ação | Microsoft.Search/register/action | Registrar a assinatura do provedor de recursos de pesquisa e permitir a criação de serviços de pesquisa. |
> | Ação | Microsoft.Search/searchServices/createQueryKey/action | Criar a chave de consulta. |
> | Ação | Microsoft.Search/searchServices/delete | Excluir o serviço de pesquisa. |
> | Ação | Microsoft.Search/searchServices/deleteQueryKey/delete | Excluir a chave de consulta. |
> | Ação | Microsoft.Search/searchServices/diagnosticSettings/read | Obter a leitura da configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Search/searchServices/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Search/searchServices/listAdminKeys/action | Ler as chaves de administração. |
> | Ação | Microsoft.Search/searchServices/listQueryKeys/read | Retorna a lista de chaves de API de consulta para o serviço Azure Search especificado. |
> | Ação | Microsoft.Search/searchServices/logDefinitions/read | Obter os logs disponíveis para o serviço de pesquisa |
> | Ação | Microsoft.Search/searchServices/metricDefinitions/read | Obter as métricas disponíveis para o serviço de pesquisa |
> | Ação | Microsoft.Search/searchServices/read | Ler o serviço de pesquisa. |
> | Ação | Microsoft.Search/searchServices/regenerateAdminKey/action | Regenerar a chave de administração. |
> | Ação | Microsoft.Search/searchServices/start/action | Iniciar o serviço de pesquisa. |
> | Ação | Microsoft.Search/searchServices/stop/action | Interromper o serviço de pesquisa. |
> | Ação | Microsoft.Search/searchServices/write | Criar ou atualizar o serviço de pesquisa. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Security/advancedThreatProtectionSettings/read | Obtém as Configurações de Proteção de Ameaça Avançadas para o recurso |
> | Ação | Microsoft.Security/advancedThreatProtectionSettings/write | Atualiza as Configurações de Proteção de Ameaça Avançadas para o recurso |
> | Ação | Microsoft.Security/alerts/read | Obter todos os alertas de segurança disponíveis |
> | Ação | Microsoft.Security/applicationWhitelistings/read | Obter a lista de liberações do aplicativo |
> | Ação | Microsoft.Security/applicationWhitelistings/write | Criar uma nova lista de exceções do aplicativo ou atualizar uma existente |
> | Ação | Microsoft.Security/complianceResults/read | Obter os resultados de conformidade para o recurso |
> | Ação | Microsoft.Security/locations/alerts/activate/action | Ativar um alerta de segurança |
> | Ação | Microsoft.Security/locations/alerts/dismiss/action | Ignorar um alerta de segurança |
> | Ação | Microsoft.Security/locations/alerts/read | Obter todos os alertas de segurança disponíveis |
> | Ação | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | Exclui a política de acesso de rede just-in-time |
> | Ação | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Inicia uma solicitação de política de acesso de rede just-in-time |
> | Ação | Microsoft.Security/locations/jitNetworkAccessPolicies/read | Obter as políticas de acesso de rede just-in-time |
> | Ação | Microsoft.Security/locations/jitNetworkAccessPolicies/write | Criar uma nova política de acesso de rede just-in-time ou atualizar uma existente |
> | Ação | Microsoft.Security/locations/read | Obter o local dos dados de segurança |
> | Ação | Microsoft.Security/locations/tasks/activate/action | Ativar recomendação de segurança |
> | Ação | Microsoft.Security/locations/tasks/dismiss/action | Ignorar recomendação de segurança |
> | Ação | Microsoft.Security/locations/tasks/read | Obter todas as recomendações de segurança disponíveis |
> | Ação | Microsoft.Security/locations/tasks/resolve/action | Resolver uma recomendação de segurança |
> | Ação | Microsoft.Security/locations/tasks/start/action | Iniciar uma recomendação de segurança |
> | Ação | Microsoft.Security/policies/read | Obter a política de segurança |
> | Ação | Microsoft.Security/policies/write | Atualizar a política de segurança |
> | Ação | Microsoft.Security/pricings/delete | Excluir as configurações de preço para o escopo |
> | Ação | Microsoft.Security/pricings/read | Obter as configurações de preço para o escopo |
> | Ação | Microsoft.Security/pricings/write | Atualizar as configurações de preço para o escopo |
> | Ação | Microsoft.Security/register/action | Registrar a assinatura da Central de Segurança do Azure |
> | Ação | Microsoft.Security/securityContacts/delete | Excluir o contato de segurança |
> | Ação | Microsoft.Security/securityContacts/read | Obter o contato de segurança |
> | Ação | Microsoft.Security/securityContacts/write | Atualizar o contato de segurança |
> | Ação | Microsoft.Security/securitySolutions/delete | Excluir uma solução de segurança |
> | Ação | Microsoft.Security/securitySolutions/read | Obter as soluções de segurança |
> | Ação | Microsoft.Security/securitySolutions/write | Criar uma nova solução de segurança ou atualizar uma existente |
> | Ação | Microsoft.Security/securitySolutionsReferenceData/read | Obter dados de referência das soluções de segurança |
> | Ação | Microsoft.Security/securityStatuses/read | Obter status de integridade da segurança para recursos do Azure |
> | Ação | Microsoft.Security/securityStatusesSummaries/read | Obter os resumos de status de segurança para o escopo |
> | Ação | Microsoft.Security/tasks/read | Obter todas as recomendações de segurança disponíveis |
> | Ação | Microsoft.Security/unregister/action | Cancelar a assinatura da Central de Segurança do Azure |
> | Ação | Microsoft.Security/webApplicationFirewalls/delete | Excluir um firewall do aplicativo Web |
> | Ação | Microsoft.Security/webApplicationFirewalls/read | Obter firewalls do aplicativo Web |
> | Ação | Microsoft.Security/webApplicationFirewalls/write | Criar um novo firewall do aplicativo Web ou atualizar um existente |
> | Ação | Microsoft.Security/workspaceSettings/connect/action | Alterar configurações de reconexão das configurações do workspace |
> | Ação | Microsoft.Security/workspaceSettings/delete | Excluir as configurações do workspace |
> | Ação | Microsoft.Security/workspaceSettings/read | Obter as configurações do workspace |
> | Ação | Microsoft.Security/workspaceSettings/write | Atualizar as configurações do workspace |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.SecurityGraph/diagnosticsettings/delete | Exclusão de uma configuração de diagnóstico |
> | Ação | Microsoft.SecurityGraph/diagnosticsettings/read | Leitura de uma configuração de diagnóstico |
> | Ação | Microsoft.SecurityGraph/diagnosticsettings/write | Gravação de uma configuração de diagnóstico |
> | Ação | Microsoft.SecurityGraph/diagnosticsettingscategories/read | Leitura de uma categoria de configuração de diagnóstico |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.ServiceBus/checkNameAvailability/action | Verificar a disponibilidade do namespace em determinada assinatura. |
> | Ação | Microsoft.ServiceBus/checkNamespaceAvailability/action | Verificar a disponibilidade do namespace em determinada assinatura. Essa API está preterida, use CheckNameAvailabiltiy. |
> | Ação | Microsoft.ServiceBus/namespaces/authorizationRules/action | Atualizar regra de autorização de namespace. Essa API está preterida. Use uma chamada PUT para atualizar a regra de autorização de namespace. Esta operação não tem suporte na versão da API 2017-04-01. |
> | Ação | Microsoft.ServiceBus/namespaces/authorizationRules/delete | Excluir regra de autorização de namespace. A regra de autorização do namespace padrão não pode ser excluída.  |
> | Ação | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | Obter a cadeia de conexão para o namespace |
> | Ação | Microsoft.ServiceBus/namespaces/authorizationRules/read | Obter a lista de descrição de regras de autorização de namespaces. |
> | Ação | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | Regenerar a chave primária ou secundária para o recurso |
> | Ação | Microsoft.ServiceBus/namespaces/authorizationRules/write | Criar regras de autorização no nível do namespace e atualizar suas propriedades. Os direitos de acesso das regras de autorização; as chaves primárias e secundárias podem ser atualizadas. |
> | Ação | Microsoft.ServiceBus/namespaces/Delete | Excluir o recurso de namespace |
> | Ação | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Obter as chaves de regras de autorização para o namespace primário de Recuperação de Desastre |
> | Ação | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | Obter regras de autorização do namespace primário de recuperação de desastre |
> | Ação | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | Desabilitar a Recuperação de desastre e interromper a replicação de alterações de namespaces primários para secundários. |
> | Ação | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Verificar a disponibilidade de alias de namespace sob determinada assinatura. |
> | Ação | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | Excluir a configuração da recuperação de desastre associada ao namespace. Essa operação somente pode ser invocada por meio do namespace primário. |
> | Ação | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | Invocar um failover de GEO DR e reconfigurar o alias de namespace para apontar para o namespace secundário. |
> | Ação | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | Obter a configuração da Recuperação de Desastre associada ao namespace. |
> | Ação | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | Criar ou atualizar a configuração de Recuperação de Desastre associada ao namespace. |
> | Ação | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | Excluir o filtro de Grade de Eventos associado ao namespace. |
> | Ação | Microsoft.ServiceBus/namespaces/eventGridFilters/read | Obter o filtro de Grade de Eventos associado ao namespace. |
> | Ação | Microsoft.ServiceBus/namespaces/eventGridFilters/write | Criar ou atualizar o filtro da Grade de Eventos associado ao namespace. |
> | Ação | Microsoft.ServiceBus/namespaces/eventhubs/read | Obter lista de descrições de recursos de EventHub |
> | Ação | Microsoft.ServiceBus/namespaces/messagingPlan/read | Obter o Plano de Mensagens para um namespace.<br>Essa API está preterida.<br>As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso Namespace (pai) nas versões posteriores da API.<br>Esta operação não tem suporte na versão da API 2017-04-01. |
> | Ação | Microsoft.ServiceBus/namespaces/messagingPlan/write | Atualizar o Plano de Mensagens para um namespace.<br>Essa API está preterida.<br>As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso Namespace (pai) nas versões posteriores da API.<br>Esta operação não tem suporte na versão da API 2017-04-01. |
> | Ação | Microsoft.ServiceBus/namespaces/migrate/action | Migrar operação de namespace |
> | Ação | Microsoft.ServiceBus/namespaces/operationresults/read | Obter o status da operação de Namespace |
> | Ação | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Obter lista de descrições de recurso de configurações de diagnóstico do namespace |
> | Ação | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Obter lista de descrições de recurso de configurações de diagnóstico do namespace |
> | Ação | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | Obter lista de descrições do recurso de log do namespace |
> | Ação | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Obter lista de métrica de descrições de recurso de métrica do namespace |
> | Ação | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | Operação para atualizar a Fila. Esta operação não tem suporte na versão da API 2017-04-01. Regras de autorização. Use uma chamada PUT para atualizar a Regra de Autorização. |
> | Ação | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | Operação para excluir regras de autorização de fila |
> | Ação | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | Obter a cadeia de conexão para fila |
> | Ação | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  Obter a lista de regras de autorização de fila |
> | Ação | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | Regenerar a chave primária ou secundária para o recurso |
> | Ação | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | Criar regras de autorização de fila e atualizar suas propriedades. Os Direitos de Acesso das Regras de Autorização podem ser atualizados. |
> | Ação | Microsoft.ServiceBus/namespaces/queues/Delete | Operação para excluir o recurso Queue |
> | Ação | Microsoft.ServiceBus/namespaces/queues/read | Obter lista de descrições do recurso Queue |
> | Ação | Microsoft.ServiceBus/namespaces/queues/write | Criar ou atualizar propriedades Queue. |
> | Ação | Microsoft.ServiceBus/namespaces/read | Obter a lista de descrição do recurso de namespace |
> | Ação | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | Remove o namespace de ACS |
> | Ação | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | Operação para atualizar o Tópico. Esta operação não tem suporte na versão da API 2017-04-01. Regras de autorização. Use uma chamada PUT para atualizar a Regra de Autorização. |
> | Ação | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | Operação para excluir regras de autorização do tópico |
> | Ação | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | Obter a cadeia de conexão para tópico |
> | Ação | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  Obter a lista de regras de autorização do tópico |
> | Ação | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | Regenerar a chave primária ou secundária para o recurso |
> | Ação | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | Criar regras de autorização do tópico e atualizar suas propriedades. Os Direitos de Acesso das Regras de Autorização podem ser atualizados. |
> | Ação | Microsoft.ServiceBus/namespaces/topics/Delete | Operação para excluir o recurso Topic |
> | Ação | Microsoft.ServiceBus/namespaces/topics/read | Obter lista de descrições de recurso Topic |
> | Ação | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | Operação para excluir o recurso TopicSubscription |
> | Ação | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | Obter lista de descrições do recurso TopicSubscription |
> | Ação | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | Operação para excluir o recurso Rule |
> | Ação | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | Obter lista de descrições do recurso Rule |
> | Ação | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | Criar ou atualizar propriedades Rule. |
> | Ação | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | Criar ou atualizar propriedades TopicSubscription. |
> | Ação | Microsoft.ServiceBus/namespaces/topics/write | Criar ou atualizar propriedades Topic. |
> | Ação | Microsoft.ServiceBus/namespaces/write | Criar um recurso de namespace e atualizar suas propriedades. Marcas e Capacidade do namespace são as propriedades que podem ser atualizadas. |
> | Ação | Microsoft.ServiceBus/operations/read | Obter Operações |
> | Ação | Microsoft.ServiceBus/register/action | Registrar a assinatura do provedor de recursos ServiceBus e permitir a criação de recursos ServiceBus |
> | Ação | Microsoft.ServiceBus/sku/read | Obter lista de Descrições de Recursos de SKU |
> | Ação | Microsoft.ServiceBus/sku/regions/read | Obter lista de Descrições de Recursos SkuRegions |
> | Ação | Microsoft.ServiceBus/unregister/action | Registrar a assinatura do provedor de recursos ServiceBus e permitir a criação de recursos ServiceBus |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.ServiceFabric/clusters/applications/delete | Excluir qualquer Aplicativo |
> | Ação | Microsoft.ServiceFabric/clusters/applications/read | Ler qualquer Aplicativo |
> | Ação | Microsoft.ServiceFabric/clusters/applications/services/delete | Excluir qualquer Serviço |
> | Ação | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | Ler qualquer Partição |
> | Ação | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | Ler qualquer Réplica |
> | Ação | Microsoft.ServiceFabric/clusters/applications/services/read | Ler qualquer Serviço |
> | Ação | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | Ler qualquer Status de Serviço |
> | Ação | Microsoft.ServiceFabric/clusters/applications/services/write | Criar ou atualizar qualquer Serviço |
> | Ação | Microsoft.ServiceFabric/clusters/applications/write | Criar ou atualizar qualquer Aplicativo |
> | Ação | Microsoft.ServiceFabric/clusters/applicationTypes/delete | Excluir qualquer Tipo de Aplicativo |
> | Ação | Microsoft.ServiceFabric/clusters/applicationTypes/read | Ler qualquer Tipo de Aplicativo |
> | Ação | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | Excluir qualquer Versão de Tipo de Aplicativo |
> | Ação | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | Ler qualquer Versão de Tipo de Aplicativo |
> | Ação | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | Criar ou atualizar qualquer Versão de Tipo de Aplicativo |
> | Ação | Microsoft.ServiceFabric/clusters/applicationTypes/write | Criar ou atualizar qualquer Tipo de Aplicativo |
> | Ação | Microsoft.ServiceFabric/clusters/delete | Excluir qualquer Cluster |
> | Ação | Microsoft.ServiceFabric/clusters/nodes/read | Ler qualquer Nó |
> | Ação | Microsoft.ServiceFabric/clusters/read | Ler qualquer Cluster |
> | Ação | Microsoft.ServiceFabric/clusters/statuses/read | Ler qualquer Status do Cluster |
> | Ação | Microsoft.ServiceFabric/clusters/write | Criar ou atualizar qualquer Cluster |
> | Ação | Microsoft.ServiceFabric/locations/clusterVersions/read | Ler qualquer Versão do Cluster |
> | Ação | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | Ler qualquer Versão do Cluster de um ambiente específico |
> | Ação | Microsoft.ServiceFabric/locations/operationresults/read | Ler qualquer Resultado de Operação |
> | Ação | Microsoft.ServiceFabric/locations/operations/read | Ler qualquer Operação por Local |
> | Ação | Microsoft.ServiceFabric/operations/read | Ler qualquer Operação Disponível |
> | Ação | Microsoft.ServiceFabric/register/action | Registrar qualquer Ação |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.SignalRService/checknameavailability/action | Verifica se um nome está disponível para uso com um novo serviço SignalR |
> | Ação | Microsoft.SignalRService/register/action | Registra o provedor de recursos 'Microsoft.SignalRService' com uma assinatura |
> | Ação | Microsoft.SignalRService/SignalR/delete | Excluir todo o SignalR |
> | Ação | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para SignalR |
> | Ação | Microsoft.SignalRService/SignalR/read | Exibe as definições e as configurações do SignalR no portal de gerenciamento ou por meio da API |
> | Ação | Microsoft.SignalRService/SignalR/write | Modifica as definições e as configurações do SignalR no portal de gerenciamento ou por meio da API |
> | Ação | Microsoft.SignalRService/unregister/action | Cancela o registro do provedor de recursos 'Microsoft.SignalRService' com uma assinatura |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Solutions/applicationDefinitions/delete | Remover uma definição de aplicativo. |
> | Ação | Microsoft.Solutions/applicationDefinitions/read | Recuperar uma lista de definições de aplicativos. |
> | Ação | Microsoft.Solutions/applicationDefinitions/write | Adicionar ou modificar uma definição de aplicativo. |
> | Ação | Microsoft.Solutions/applications/delete | Remover um aplicativo. |
> | Ação | Microsoft.Solutions/applications/read | Recuperar uma lista de aplicativos. |
> | Ação | Microsoft.Solutions/applications/write | Criar um aplicativo. |
> | Ação | Microsoft.Solutions/jitRequests/delete | Remover um JitRequest |
> | Ação | Microsoft.Solutions/jitRequests/read | Recupera uma lista de JitRequests |
> | Ação | Microsoft.Solutions/jitRequests/write | Cria um JitRequest |
> | Ação | Microsoft.Solutions/locations/operationStatuses/read | Ler o status da operação do recurso. |
> | Ação | Microsoft.Solutions/register/action | Registrar para Soluções. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Sql/checkNameAvailability/action | Verificar se o nome do servidor fornecido está disponível para provisionamento no mundo inteiro para uma determinada assinatura. |
> | Ação | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | Recuperar resultado da política de auditoria de blob de servidor estendida Definir operação |
> | Ação | Microsoft.Sql/locations/auditingSettingsOperationResults/read | Recuperar o resultado da operação Definir da política de auditoria do blob do servidor |
> | Ação | Microsoft.Sql/locations/capabilities/read | Obter as capacidades para essa assinatura em um determinado local |
> | Ação | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | Obter o status de uma operação de banco de dados. |
> | Ação | Microsoft.Sql/locations/databaseOperationResults/read | Obter o status de uma operação de banco de dados. |
> | Ação | Microsoft.Sql/locations/deletedServerAsyncOperation/read | Obter operações em andamento no servidor excluído |
> | Ação | Microsoft.Sql/locations/deletedServerOperationResults/read | Obter operações em andamento no servidor excluído |
> | Ação | Microsoft.Sql/locations/deletedServers/read | Retornar a lista de servidores excluídos ou obter as propriedades para o servidor excluído especificado. |
> | Ação | Microsoft.Sql/locations/deletedServers/recover/action | Recuperar um servidor excluído |
> | Ação | Microsoft.Sql/locations/deleteVirtualNetworkOrSubnets/action | Excluir regras de rede virtual associadas a uma rede virtual ou sub-rede |
> | Ação | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | Obter a operação assíncrona do Azure para uma operação assíncrona de pool elástico |
> | Ação | Microsoft.Sql/locations/elasticPoolOperationResults/read | Obter o resultado de uma operação de pool elástico. |
> | Ação | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | Recuperar resultado da política de auditoria de blob de servidor estendida Definir operação |
> | Ação | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | Recuperar resultado da política de auditoria de blob de servidor estendida Definir operação |
> | Ação | Microsoft.Sql/locations/instanceFailoverGroups/delete | Exclui um grupo de failover de instância existente. |
> | Ação | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | Executa o failover planejado em um grupo de failover de instância existente. |
> | Ação | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Executa o failover forçado em um grupo de failover de instância existente. |
> | Ação | Microsoft.Sql/locations/instanceFailoverGroups/read | Retorna a lista de grupos de failover de instância ou obtém as propriedades do grupo de failover de instância especificado. |
> | Ação | Microsoft.Sql/locations/instanceFailoverGroups/write | Cria um grupo de failover de instância com os parâmetros especificados ou atualiza as propriedades ou marcas para o grupo de failover de instância especificado. |
> | Ação | Microsoft.Sql/locations/interfaceEndpointProfileAzureAsyncOperation/read | Devolve os detalhes de uma operação assíncrona do Azure de ponto de extremidade de interface específico |
> | Ação | Microsoft.Sql/locations/interfaceEndpointProfileOperationResults/read | Devolve os detalhes da operação de perfil de ponto de extremidade de interface específico |
> | Ação | Microsoft.Sql/locations/jobAgentAzureAsyncOperation/read | Obtém o status de uma operação do agente de trabalho. |
> | Ação | Microsoft.Sql/locations/jobAgentOperationResults/read | Obtém o resultado de uma operação do agente de trabalho. |
> | Ação | Microsoft.Sql/locations/longTermRetentionBackups/read | Lista os backups de retenção de longo prazo para cada banco de dados em todos os servidores de um local |
> | Ação | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | Lista os backups de retenção de longo prazo para cada banco de dados em todos os bancos de dados em um servidor |
> | Ação | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | Exclui um backup de retenção de longo prazo |
> | Ação | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | Lista os backups de retenção de longo prazo para um banco de dados |
> | Ação | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | Concluir a operação de restauração do banco de dados gerenciado |
> | Ação | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | Obter operações em andamento na criptografia de dados transparente do banco de dados gerenciado |
> | Ação | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | Obter operações em andamento na criptografia de dados transparente do banco de dados gerenciado |
> | Ação | Microsoft.Sql/locations/read | Obter os locais disponíveis para uma determinada assinatura |
> | Ação | Microsoft.Sql/locations/syncAgentOperationResults/read | Recuperar resultado da operação do recurso do agente de sincronização |
> | Ação | Microsoft.Sql/locations/syncDatabaseIds/read | Recuperar as IDs do banco de dados de sincronização para uma assinatura e região específica |
> | Ação | Microsoft.Sql/locations/syncGroupOperationResults/read | Recuperar resultado da operação do recurso de grupo de sincronização |
> | Ação | Microsoft.Sql/locations/syncMemberOperationResults/read | Recuperar resultado da operação do recurso de membro de sincronização |
> | Ação | Microsoft.Sql/locations/usages/read | Obter uma coleção de métricas de uso para essa assinatura em um local |
> | Ação | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | Retornar os detalhes da operação assíncrona de regras de rede virtual especificada do Azure  |
> | Ação | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | Retornar os detalhes da operação de regras de rede virtual especificada  |
> | Ação | Microsoft.Sql/managedInstances/administrators/delete | Excluir um administrador existente da instância gerenciada. |
> | Ação | Microsoft.Sql/managedInstances/administrators/read | Obter uma lista de administradores de instância gerenciada. |
> | Ação | Microsoft.Sql/managedInstances/administrators/write | Criar ou atualizar o administrador da instância gerenciada com os parâmetros especificados. |
> | Ação | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | Obtém uma política de retenção de curto prazo para um banco de dados gerenciado |
> | Ação | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | Atualiza uma política de retenção de curto prazo para um banco de dados gerenciado |
> | Ação | Microsoft.Sql/managedInstances/databases/delete | Excluir um banco de dados gerenciado existente |
> | Ação | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/logDefinitions/read | Obtém os logs disponíveis para os bancos de dados de instância gerenciada |
> | Ação | Microsoft.Sql/managedInstances/databases/read | Obter o banco de dados gerenciado existente |
> | Ação | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | Recuperar detalhes da política de detecção de ameaças do banco de dados configurada em um determinado banco de dados gerenciado |
> | Ação | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | Alterar a política de detecção de ameaças do banco de dados para um determinado banco de dados gerenciado |
> | Ação | Microsoft.Sql/managedInstances/databases/securityEvents/read | Recuperar os eventos de segurança do banco de dados gerenciado |
> | Ação | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | Recuperar detalhes do Transparent Data Encryption do banco de dados em um determinado banco de dados gerenciado |
> | Ação | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | Alterar o Transparent Data Encryption do banco de dados para um determinado banco de dados gerenciado |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | Remover a avaliação de vulnerabilidade de um determinado banco de dados |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | Recuperar detalhes da avaliação de vulnerabilidade configurada em um determinado banco de dados |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | Remover a linha de base da regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | Obter a linha de base da regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | Alterar a linha de base da regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | Converter um resultado de exame existente em um formato legível por humanos. Se já existir, nada acontecerá |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | Executar verificação de banco de dados para avaliação de vulnerabilidade. |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | Retornar a lista de registros de exame de avaliação de vulnerabilidade de banco de dados ou obter o registro de exame para a ID de exame especificado. |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | Alterar a avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/managedInstances/databases/write | Cria um novo banco de dados ou atualiza um banco de dados existente. |
> | Ação | Microsoft.Sql/managedInstances/delete | Excluir uma instância gerenciada existente. |
> | Ação | Microsoft.Sql/managedInstances/encryptionProtector/read | Retornar uma lista de protetores de criptografia do servidor ou obter as propriedades do protetor de criptografia do servidor especificado. |
> | Ação | Microsoft.Sql/managedInstances/encryptionProtector/write | Atualizar as propriedades do Protetor de Criptografia do Servidor especificado. |
> | Ação | Microsoft.Sql/managedInstances/keys/delete | Excluir uma chave de Instância Gerenciada do Azure SQL. |
> | Ação | Microsoft.Sql/managedInstances/keys/read | Retornar as chaves da lista de instância gerenciada ou obter as propriedades para a instância gerenciada especificada. |
> | Ação | Microsoft.Sql/managedInstances/keys/write | Criar uma chave com os parâmetros especificados ou atualizar as propriedades ou marcas para a instância gerenciada especificada. |
> | Ação | Microsoft.Sql/managedInstances/metricDefinitions/read | Obter definições de métrica de instância gerenciada |
> | Ação | Microsoft.Sql/managedInstances/metrics/read | Obter métricas de instância gerenciada |
> | Ação | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/logDefinitions/read | Obtém os logs disponíveis para instâncias gerenciadas |
> | Ação | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/metricDefinitions/read | Devolver os tipos de métricas que estão disponíveis para instâncias gerenciadas |
> | Ação | Microsoft.Sql/managedInstances/read | Retornar a lista de instâncias gerenciadas ou obter as propriedades para a instância gerenciada especificada. |
> | Ação | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | Obtém uma política de retenção de curto prazo para um banco de dados gerenciado descartado |
> | Ação | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | Atualiza uma política de retenção de curto prazo para um banco de dados gerenciado descartado |
> | Ação | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | Devolve uma lista de bancos de dados gerenciados soltos restauráveis. |
> | Ação | Microsoft.Sql/managedInstances/securityAlertPolicies/read | Recuperar detalhes da política de detecção de ameaças do servidor gerenciado configurada em um determinado servidor gerenciado |
> | Ação | Microsoft.Sql/managedInstances/securityAlertPolicies/write | Alterar a política de detecção de ameaças do servidor gerenciado para um determinado servidor gerenciado |
> | Ação | Microsoft.Sql/managedInstances/tdeCertificates/action | Criar/atualizar certificado TDE |
> | Ação | Microsoft.Sql/managedInstances/vulnerabilityAssessments/delete | Remover a avaliação de vulnerabilidade para determinada instância gerenciada |
> | Ação | Microsoft.Sql/managedInstances/vulnerabilityAssessments/read | Recuperar detalhes da avaliação de vulnerabilidade configurada em determinada instância gerenciada |
> | Ação | Microsoft.Sql/managedInstances/vulnerabilityAssessments/write | Alterar a avaliação de vulnerabilidade para determinada instância gerenciada |
> | Ação | Microsoft.Sql/managedInstances/write | Criar uma instância gerenciada com os parâmetros especificados ou atualizar as propriedades ou marcas para a instância gerenciada especificada. |
> | Ação | Microsoft.Sql/operations/read | Obter as operações REST disponíveis |
> | Ação | Microsoft.Sql/register/action | Registrar a assinatura do provedor de recursos do Banco de Dados SQL do Microsoft Azure e habilitar a criação do Banco de Dados SQL do Microsoft Azure. |
> | Ação | Microsoft.Sql/servers/administratorOperationResults/read | Obter operações em andamento nos administradores do servidor |
> | Ação | Microsoft.Sql/servers/administrators/delete | Excluir administrador do servidor |
> | Ação | Microsoft.Sql/servers/administrators/read | Recuperar detalhes do administrador do servidor |
> | Ação | Microsoft.Sql/servers/administrators/write | Criar ou atualizar o administrador do servidor |
> | Ação | Microsoft.Sql/servers/advisors/read | Retornar a lista de consultores disponíveis para o servidor |
> | Ação | Microsoft.Sql/servers/advisors/recommendedActions/read | Retornar a lista de ações recomendadas do assistente especificado para o servidor |
> | Ação | Microsoft.Sql/servers/advisors/recommendedActions/write | Aplicar a ação recomendada no servidor |
> | Ação | Microsoft.Sql/servers/advisors/write | Atualizar status de execução automática de um assistente no nível do servidor. |
> | Ação | Microsoft.Sql/servers/auditingPolicies/read | Recuperar detalhes da política de auditoria de tabela do servidor padrão configurada em determinado servidor |
> | Ação | Microsoft.Sql/servers/auditingPolicies/write | Alterar a auditoria da tabela de servidor padrão para determinado servidor |
> | Ação | Microsoft.Sql/servers/auditingSettings/operationResults/read | Recuperar o resultado da operação Definir da política de auditoria do blob do servidor |
> | Ação | Microsoft.Sql/servers/auditingSettings/read | Recuperar detalhes da política de auditoria de blob do servidor configurada em determinado servidor |
> | Ação | Microsoft.Sql/servers/auditingSettings/write | Alterar a auditoria de blob de servidor para determinado servidor |
> | Ação | Microsoft.Sql/servers/automaticTuning/read | Retornar as configurações de ajuste automático do servidor |
> | Ação | Microsoft.Sql/servers/automaticTuning/write | Atualizar as configurações de ajuste automático do servidor e retornar as configurações atualizadas |
> | Ação | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | Excluir um cofre de arquivos de backup existente. |
> | Ação | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | Essa operação é usada para obter um cofre de retenção de backup de longo prazo. Ela retorna informações sobre o cofre registrado para esse servidor |
> | Ação | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | Esta operação é utilizada para registrar uma retenção de backup em longo prazo em um servidor |
> | Ação | Microsoft.Sql/servers/communicationLinks/delete | Excluir um link de comunicação com o servidor existente. |
> | Ação | Microsoft.Sql/servers/communicationLinks/read | Retornar a lista de links de comunicação de um servidor especificado. |
> | Ação | Microsoft.Sql/servers/communicationLinks/write | Criar ou atualizar um link de comunicação com o servidor. |
> | Ação | Microsoft.Sql/servers/connectionPolicies/read | Retornar a lista de política de conexão do servidor de um servidor especificado. |
> | Ação | Microsoft.Sql/servers/connectionPolicies/write | Criar ou atualizar uma política de conexão do servidor. |
> | Ação | Microsoft.Sql/servers/databases/advisors/read | Retornar a lista de consultores disponíveis para o banco de dados |
> | Ação | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | Retornar a lista de ações recomendadas do assistente especificado para o banco de dados |
> | Ação | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | Aplicar a ação recomendada no banco de dados |
> | Ação | Microsoft.Sql/servers/databases/advisors/write | Atualizar status de um assistente no nível do banco de dados. |
> | Ação | Microsoft.Sql/servers/databases/auditingPolicies/read | Recuperar detalhes da política de auditoria de tabela configurada em determinado servidor |
> | Ação | Microsoft.Sql/servers/databases/auditingPolicies/write | Alterar a política de auditoria de tabela para determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/auditingSettings/read | Recuperar detalhes da política de auditoria de blob configurada em determinado servidor |
> | Ação | Microsoft.Sql/servers/databases/auditingSettings/write | Alterar a política de auditoria de blob para determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/auditRecords/read | Recuperar os registros de auditoria do blob do banco de dados |
> | Ação | Microsoft.Sql/servers/databases/automaticTuning/read | Retornar as configurações de ajuste automático de um banco de dados |
> | Ação | Microsoft.Sql/servers/databases/automaticTuning/write | Atualizar as configurações de ajuste automático de um banco de dados e retornar as configurações atualizadas |
> | Ação | Microsoft.Sql/servers/databases/azureAsyncOperation/read | Obter o status de uma operação de banco de dados. |
> | Ação | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | Retornar a lista de políticas de arquivamento de backup de um banco de dados especificado. |
> | Ação | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | Criar ou atualizar uma política de arquivamento de backup de banco de dados. |
> | Ação | Microsoft.Sql/servers/databases/connectionPolicies/read | Recuperar detalhes da política de conexão configurada em determinado servidor |
> | Ação | Microsoft.Sql/servers/databases/connectionPolicies/write | Alterar política de conexão para determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | Retornar a lista de políticas de máscara de dados do banco de dados. |
> | Ação | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | Excluir regra de política de máscara de dados para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | Recuperar detalhes da regra de política de mascaramento de dados configurada em determinado servidor |
> | Ação | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | Alterar regra de política de mascaramento de dados para determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | Alterar política de mascaramento de dados para determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | Retornar as informações de etapa de consulta distribuída da consulta de data warehouse para ID da etapa selecionada |
> | Ação | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | Retornar as informações de consulta de distribuição do data warehouse para a ID de consulta selecionada |
> | Ação | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | Recuperar as atividades do usuário de uma instância do SQL Data Warehouse que inclui consultas suspensas e em execução |
> | Ação | Microsoft.Sql/servers/databases/delete | Excluir um banco de dados existente. |
> | Ação | Microsoft.Sql/servers/databases/export/action | Exportar Banco de Dados SQL do Azure |
> | Ação | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Recuperar detalhes da política de auditoria de blob estendida configurada em um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | Alterar a política de auditoria de blob estendida para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/extensions/read | Obter uma coleção de extensões para o banco de dados. |
> | Ação | Microsoft.Sql/servers/databases/extensions/write | Alterar a extensão de um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/geoBackupPolicies/read | Recuperar políticas do backup geográfico para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/geoBackupPolicies/write | Criar ou atualizar uma política do backup geográfico de banco de dados |
> | Ação | Microsoft.Sql/servers/databases/importExportOperationResults/read | Obter operações de importação/exportação em andamento |
> | Ação | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | Obtém uma lista de janelas de manutenção disponíveis para um banco de dados selecionado. |
> | Ação | Microsoft.Sql/servers/databases/maintenanceWindows/read | Obtém a manutenção de configurações do windows para um banco de dados selecionado. |
> | Ação | Microsoft.Sql/servers/databases/maintenanceWindows/write | Define a manutenção de configurações do windows para um banco de dados selecionado. |
> | Ação | Microsoft.Sql/servers/databases/metricDefinitions/read | Retornar tipos de métricas que estão disponíveis para bancos de dados |
> | Ação | Microsoft.Sql/servers/databases/metrics/read | Métricas de retorno para bancos de dados |
> | Ação | Microsoft.Sql/servers/databases/move/action | Renomear Banco de Dados SQL do Azure |
> | Ação | Microsoft.Sql/servers/databases/operationResults/read | Obter o status de uma operação de banco de dados. |
> | Ação | Microsoft.Sql/servers/databases/operations/cancel/action | Cancelar a operação assíncrona pendente do Banco de Dados SQL do Azure que ainda não está concluída. |
> | Ação | Microsoft.Sql/servers/databases/operations/read | Retornar a lista de operações realizadas no banco de dados |
> | Ação | Microsoft.Sql/servers/databases/pause/action | Pausar Banco de Dados do DataWarehouse do Azure SQL |
> | Ação | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | Obter os logs disponíveis para bancos de dados |
> | Ação | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | Retornar tipos de métricas que estão disponíveis para bancos de dados |
> | Ação | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | Retornar a coleção de textos de consulta que correspondem aos parâmetros especificados. |
> | Ação | Microsoft.Sql/servers/databases/queryStore/read | Retornar os valores atuais das configurações do Repositório de Consultas do banco de dados. |
> | Ação | Microsoft.Sql/servers/databases/queryStore/write | Atualizar a configuração do repositório de consultas do banco de dados |
> | Ação | Microsoft.Sql/servers/databases/read | Retornar a lista de bancos de dados ou obter as propriedades para o banco de dados especificado. |
> | Ação | Microsoft.Sql/servers/databases/replicationLinks/delete | Encerrar a relação de replicação de modo forçado e com potencial perda de dados |
> | Ação | Microsoft.Sql/servers/databases/replicationLinks/failover/action | Failover após a sincronização de todas as alterações do primário, tornando esse banco de dados na relação de replicação\u0027s primária e tornando o primário remoto em um secundário |
> | Ação | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | Failover imediatamente com possibilidade de perda de dados, tornando esse banco de dados o principal da relação de replicação\u0027s e tornando o primário remoto um secundário |
> | Ação | Microsoft.Sql/servers/databases/replicationLinks/read | Retorne a lista de links de replicação ou obtém as propriedades para os links de replicação especificado. |
> | Ação | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | Encerrar a relação de replicação de modo forçado ou após a sincronização com o parceiro |
> | Ação | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | Atualizar o modo de replicação para o vincular ao modo síncrono ou assíncrono |
> | Ação | Microsoft.Sql/servers/databases/restorePoints/action | Criar um novo ponto de restauração |
> | Ação | Microsoft.Sql/servers/databases/restorePoints/delete | Exclui um ponto de restauração do banco de dados. |
> | Ação | Microsoft.Sql/servers/databases/restorePoints/read | Retornar os pontos de restauração do banco de dados. |
> | Ação | Microsoft.Sql/servers/databases/resume/action | Retomar o Banco de Dados do Datawarehouse do Azure SQL |
> | Ação | Microsoft.Sql/servers/databases/schemas/read | Recuperar lista de esquemas de um banco de dados |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Recuperar a lista de colunas de uma tabela |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | Excluir o rótulo de confidencialidade de uma determinada coluna |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | Obter o rótulo de confidencialidade de uma determinada coluna |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | Criar ou atualizar o rótulo de confidencialidade de uma determinada coluna |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/read | Recuperar lista de tabelas de um banco de dados |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | Recuperar a lista de recomendações de índice em um banco de dados |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | Aplicar a recomendação de índice |
> | Ação | Microsoft.Sql/servers/databases/securityAlertPolicies/read | Recuperar detalhes da política de detecção de ameaças configurada em determinado servidor |
> | Ação | Microsoft.Sql/servers/databases/securityAlertPolicies/write | Alterar política de detecção de ameaças para determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/securityMetrics/read | Obter uma coleção de métricas de segurança de banco de dados |
> | Ação | Microsoft.Sql/servers/databases/sensitivityLabels/read | Listar rótulos de confidencialidade de um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | Retornar sugestão sobre escalar ou reduzir o banco de dados verticalmente com base nas estatísticas de execução de consulta para melhorar o desempenho ou reduzir os custos |
> | Ação | Microsoft.Sql/servers/databases/skus/read | Obtém uma coleção de SKUs disponíveis para um banco de dados |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | Cancelar a sincronização do grupo de sincronização |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/delete | Excluir um grupo de sincronização existente. |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | Retornar a lista de esquemas do banco de dados do hub de sincronização |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/logs/read | Retornar a lista de logs do grupo de sincronização |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/read | Retornar a lista de grupos de sincronização ou obter as propriedades do grupo de sincronização especificado. |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | Atualizar o esquema do banco de dados do hub de sincronização |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | Recuperar resultado da operação de atualização do esquema do hub de sincronização |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | Excluir um membro de sincronização existente. |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | Retornar a lista de membros de sincronização ou obter as propriedades para o membro de sincronização especificado. |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | Atualizar o esquema do membro de sincronização |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | Recuperar resultado da operação de atualização do esquema do membro de sincronização |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | Retornar a lista de esquemas do banco de dados de membros de sincronização |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | Criar um membro de sincronização com os parâmetros especificados ou atualiza as propriedades do membro de sincronização especificado. |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | Disparar a sincronização do grupo de sincronização |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/write | Criar um grupo de sincronização com os parâmetros especificados ou atualizar as propriedades do grupo de sincronização especificado. |
> | Ação | Microsoft.Sql/servers/databases/topQueries/queryText/action | Retornar o texto Transact-SQL para a ID de consulta selecionada |
> | Ação | Microsoft.Sql/servers/databases/topQueries/read | Retorna estatísticas de tempo de execução agregadas para a consulta selecionada no período de tempo selecionado |
> | Ação | Microsoft.Sql/servers/databases/topQueries/statistics/read | Retorna estatísticas de tempo de execução agregadas para a consulta selecionada no período de tempo selecionado |
> | Ação | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Obter operações em andamento na criptografia de dados transparente |
> | Ação | Microsoft.Sql/servers/databases/transparentDataEncryption/read | Recuperar o status e os detalhes do recurso de segurança Transparent Data Encryption para determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/transparentDataEncryption/write | Alterar o estado de criptografia de dados transparente |
> | Ação | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | Atualizar o Banco de Dados do Datawarehouse do Azure SQL |
> | Ação | Microsoft.Sql/servers/databases/usages/read | Obter as informações de uso do Banco de Dados SQL do Azure |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | Remover a avaliação de vulnerabilidade de um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | Recuperar detalhes da avaliação de vulnerabilidade configurada em um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | Remover a linha de base da regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | Obter a linha de base da regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | Alterar a linha de base da regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | Converter um resultado de exame existente em um formato legível por humanos. Se já existir, nada acontecerá |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | Executar verificação de banco de dados para avaliação de vulnerabilidade. |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | Retornar a lista de registros de exame de avaliação de vulnerabilidade de banco de dados ou obter o registro de exame para a ID de exame especificado. |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | Alterar a avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | Executar verificação de banco de dados para avaliação de vulnerabilidade. |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | Recuperar o resultado de exame de avaliação de vulnerabilidade do banco de dados Executar operação |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | Recuperar detalhes da avaliação de vulnerabilidade configurada em um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | Alterar a avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/write | Criar um banco de dados com os parâmetros especificados ou atualizar as propriedades ou marcas para o banco de dados especificado. |
> | Ação | Microsoft.Sql/servers/delete | Excluir um servidor existente. |
> | Ação | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | Excluir as configurações de recuperação de desastre existentes para um determinado servidor |
> | Ação | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | Failover de um DisasterRecoveryConfiguration |
> | Ação | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | Forçar failover de um DisasterRecoveryConfiguration |
> | Ação | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | Obter uma coleção de configurações de recuperação de desastre que incluem esse servidor |
> | Ação | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | Alterar configuração de recuperação de desastre do servidor |
> | Ação | Microsoft.Sql/servers/elasticPoolEstimates/read | Retornar a lista de estimativas de pool elástico já criado para o servidor |
> | Ação | Microsoft.Sql/servers/elasticPoolEstimates/write | Criar nova estimativa de pool elástico para a lista de bancos de dados fornecida |
> | Ação | Microsoft.Sql/servers/elasticPools/advisors/read | Retornar a lista de consultores disponíveis para o pool elástico |
> | Ação | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | Retornar a lista de ações recomendadas do assistente especificado para o pool elástico |
> | Ação | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | Aplicar a ação recomendada no pool elástico |
> | Ação | Microsoft.Sql/servers/elasticPools/advisors/write | Atualizar status de execução automática de um assistente no nível do pool elástico. |
> | Ação | Microsoft.Sql/servers/elasticPools/databases/read | Obter uma lista de bancos de dados para um pool elástico |
> | Ação | Microsoft.Sql/servers/elasticPools/delete | Excluir pool elástico existente |
> | Ação | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | Recuperar atividades e detalhes em um pool de banco de dados elástico |
> | Ação | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | Recuperar atividades e detalhes em um banco de dados que faz parte do pool de banco de dados elástico |
> | Ação | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | Retornar tipos de métricas que estão disponíveis para pools de bancos de dados elásticos |
> | Ação | Microsoft.Sql/servers/elasticPools/metrics/read | Métricas de retorno para conjuntos de bancos de dados elásticos |
> | Ação | Microsoft.Sql/servers/elasticPools/operations/cancel/action | Cancela o pool elástico do Azure SQL pendente de operação assíncrona que ainda não está concluída. |
> | Ação | Microsoft.Sql/servers/elasticPools/operations/read | Retornar a lista de operações realizadas no pool elástico |
> | Ação | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | Retornar tipos de métricas que estão disponíveis para pools de bancos de dados elásticos |
> | Ação | Microsoft.Sql/servers/elasticPools/read | Recuperar detalhes do pool elástico em um determinado servidor |
> | Ação | Microsoft.Sql/servers/elasticPools/skus/read | Obtém uma coleção de SKUs disponíveis para um pool elástico |
> | Ação | Microsoft.Sql/servers/elasticPools/write | Criar um novo ou alterar propriedades do pool elástico existente |
> | Ação | Microsoft.Sql/servers/encryptionProtector/read | Retornar uma lista de protetores de criptografia do servidor ou obter as propriedades do protetor de criptografia do servidor especificado. |
> | Ação | Microsoft.Sql/servers/encryptionProtector/write | Atualizar as propriedades do Protetor de Criptografia do Servidor especificado. |
> | Ação | Microsoft.Sql/servers/extendedAuditingSettings/read | Recuperar detalhes da política de auditoria de blob de servidor estendida configurada em um determinado servidor |
> | Ação | Microsoft.Sql/servers/extendedAuditingSettings/write | Alterar a auditoria de blob de servidor estendida para um determinado servidor |
> | Ação | Microsoft.Sql/servers/failoverGroups/delete | Excluir um grupo de failover existente. |
> | Ação | Microsoft.Sql/servers/failoverGroups/failover/action | Executar o failover planejado em um grupo de failover existente. |
> | Ação | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | Executar o failover forçado em um grupo de failover existente. |
> | Ação | Microsoft.Sql/servers/failoverGroups/read | Retornar a lista de grupos de failover ou obter as propriedades do grupo de failover especificado. |
> | Ação | Microsoft.Sql/servers/failoverGroups/write | Criar um grupo de failover com os parâmetros especificados ou atualizar as propriedades ou marcas para o grupo de failover especificado. |
> | Ação | Microsoft.Sql/servers/firewallRules/delete | Excluir uma regra de firewall de servidor existente. |
> | Ação | Microsoft.Sql/servers/firewallRules/read | Retornar a lista de regras de firewall do servidor ou obter as propriedades da regra de firewall do servidor especificado. |
> | Ação | Microsoft.Sql/servers/firewallRules/write | Criar uma regra de firewall do servidor com os parâmetros especificados, atualizar as propriedades da regra especificada ou substituir todas as regras existentes pelas novas regras de firewall do servidor. |
> | Ação | Microsoft.Sql/servers/import/action | Criar um novo banco de dados no servidor e implantar o esquema e os dados de um pacote DacPac |
> | Ação | Microsoft.Sql/servers/importExportOperationResults/read | Obter operações de importação/exportação em andamento |
> | Ação | Microsoft.Sql/servers/interfaceEndpointProfiles/delete | Exclui o perfil de ponto de extremidade de interface especificado |
> | Ação | Microsoft.Sql/servers/interfaceEndpointProfiles/read | Devolve as propriedades para o perfil de ponto de extremidade de interface especificado |
> | Ação | Microsoft.Sql/servers/interfaceEndpointProfiles/write | Cria um perfil de ponto de extremidade de interface com os parâmetros especificados ou atualiza as propriedades ou marcas para o ponto de extremidade de interface especificado |
> | Ação | Microsoft.Sql/servers/jobAgents/delete | Exclui um agente de trabalho de BD SQL do Azure |
> | Ação | Microsoft.Sql/servers/jobAgents/read | Obtém um agente de trabalho de BD SQL do Azure |
> | Ação | Microsoft.Sql/servers/jobAgents/write | Cria ou atualiza um agente de trabalho de BD SQL do Azure |
> | Ação | Microsoft.Sql/servers/keys/delete | Excluir uma chave do servidor existente. |
> | Ação | Microsoft.Sql/servers/keys/read | Retornar a lista de chaves do servidor ou obter as propriedades para a chave de servidor especificada. |
> | Ação | Microsoft.Sql/servers/keys/write | Criar uma chave com os parâmetros especificados ou atualizar as propriedades ou marcas para a chave do servidor especificada. |
> | Ação | Microsoft.Sql/servers/operationResults/read | Obter operações do servidor em andamento |
> | Ação | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | Retornar tipos de métricas disponíveis para servidores |
> | Ação | Microsoft.Sql/servers/read | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | Ação | Microsoft.Sql/servers/recommendedElasticPools/databases/read | Recuperar a métrica de pools de banco de dados elástico recomendados para determinado servidor |
> | Ação | Microsoft.Sql/servers/recommendedElasticPools/read | Recuperar recomendação para pools de banco de dados elástico a fim de reduzir custos ou melhorar o desempenho com base no histórico de utilização de recursos |
> | Ação | Microsoft.Sql/servers/recoverableDatabases/read | Essa operação é usada para a recuperação de desastres do banco de dados dinâmico a fim de restaurar o banco de dados no último ponto de backup bom conhecido. Ela retorna informações sobre o último backup, mas não\u0027t restaura o banco de dados efetivamente. |
> | Ação | Microsoft.Sql/servers/replicationLinks/read | Retorne a lista de links de replicação ou obtém as propriedades para os links de replicação especificado. |
> | Ação | Microsoft.Sql/servers/restorableDroppedDatabases/read | Obter uma lista de bancos de dados que foram removidos em determinado servidor ainda na política de retenção. |
> | Ação | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | Recuperar resultados da operação de gravação da política de detecção de ameaças do servidor |
> | Ação | Microsoft.Sql/servers/securityAlertPolicies/read | Recuperar detalhes da política de detecção de ameaças do servidor configurada em determinado servidor |
> | Ação | Microsoft.Sql/servers/securityAlertPolicies/write | Alterar a política de detecção de ameaças do servidor para um determinado servidor |
> | Ação | Microsoft.Sql/servers/serviceObjectives/read | Recuperar a lista de objetivos de nível de serviço (também conhecido como níveis de desempenho) disponíveis em determinado servidor |
> | Ação | Microsoft.Sql/servers/syncAgents/delete | Excluir um agente de sincronização existente. |
> | Ação | Microsoft.Sql/servers/syncAgents/generateKey/action | Gerar chave de registro do agente de sincronização |
> | Ação | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | Retornar a lista de bancos de dados vinculados do agente de sincronização |
> | Ação | Microsoft.Sql/servers/syncAgents/read | Retornar a lista de agentes de sincronização ou obter as propriedades para o agente de sincronização especificado. |
> | Ação | Microsoft.Sql/servers/syncAgents/write | Criar um agente de sincronização com os parâmetros especificados ou atualizar as propriedades do agente de sincronização especificado. |
> | Ação | Microsoft.Sql/servers/tdeCertificates/action | Criar/atualizar certificado TDE |
> | Ação | Microsoft.Sql/servers/usages/read | Retornar cota de DTU de servidor e DTU de consumo atual por todos os bancos de dados no servidor |
> | Ação | Microsoft.Sql/servers/virtualNetworkRules/delete | Excluir uma regra de rede virtual existente |
> | Ação | Microsoft.Sql/servers/virtualNetworkRules/read | Retornar a lista de regras de rede virtual ou obter as propriedades da regra de rede virtual especificada. |
> | Ação | Microsoft.Sql/servers/virtualNetworkRules/write | Criar uma regra de rede virtual com os parâmetros especificados ou atualizar as propriedades ou marcas para a regra de rede virtual especificada. |
> | Ação | Microsoft.Sql/servers/vulnerabilityAssessments/delete | Remover a avaliação de vulnerabilidade de determinado servidor |
> | Ação | Microsoft.Sql/servers/vulnerabilityAssessments/read | Recuperar detalhes da avaliação de vulnerabilidade configurada em determinado servidor |
> | Ação | Microsoft.Sql/servers/vulnerabilityAssessments/write | Alterar a avaliação de vulnerabilidade de determinado servidor |
> | Ação | Microsoft.Sql/servers/write | Criar um servidor com os parâmetros especificados ou atualizar as propriedades ou marcas para o servidor especificado. |
> | Ação | Microsoft.Sql/unregister/action | Cancelar a assinatura do provedor de recursos do Banco de Dados SQL do Microsoft Azure e habilitar a criação do Banco de Dados SQL do Microsoft Azure. |
> | Ação | Microsoft.Sql/virtualClusters/read | Retornar a lista de clusters virtuais ou obter as propriedades para o cluster virtual especificado. |
> | Ação | Microsoft.Sql/virtualClusters/write | Atualiza marcas de cluster virtual. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Storage/checknameavailability/read | Verificar se o nome dessa conta é válido e não está em uso. |
> | Ação | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | Notificar o Microsoft.Storage sobre a exclusão de uma rede virtual ou sub-rede |
> | Ação | Microsoft.Storage/locations/usages/read | Retornar o limite e a contagem atual de uso de recursos na assinatura especificada |
> | Ação | Microsoft.Storage/operations/read | Monitorar o status de uma operação assíncrona. |
> | Ação | Microsoft.Storage/register/action | Registra a assinatura do provedor de recursos de armazenamento e permite a criação de contas de armazenamento. |
> | Ação | Microsoft.Storage/skus/read | Listar os SKUs com suporte pelo Microsoft.Storage. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | Retorna o resultado da adição de conteúdo do blob |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Retorna o resultado da exclusão de um blob |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteAutomaticSnapshot/action | Devolve o resultado da exclusão de um instantâneo automático |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Retorna um blob ou uma lista de blobs |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Retorna o resultado da gravação de um blob |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | Limpar a retenção legal do contêiner de blob |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Retornar o resultado da exclusão de um contêiner |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | Excluir a política de imutabilidade do contêiner de blob |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | Estender a política de imutabilidade do contêiner de blob |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | Bloquear a política de imutabilidade do contêiner de blob |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | Obter a política de imutabilidade do contêiner de blob |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | Inserir a política de imutabilidade do contêiner de blob |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/read | Retornar um contêiner ou uma lista de contêineres |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | Definir a retenção legal do contêiner de blob |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/write | Retornar o resultado de um contêiner de blobs de concessão ou colocação |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Retorna uma chave de delegação de usuário para o serviço Blob |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso. |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso. |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/logDefinitions/read | Obtém a definição de log para Blob |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/metricDefinitions/read | Obter lista de definições de Métricas de Armazenamento da Microsoft. |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/read | Retornar propriedades ou estatísticas do serviço blob |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/write | Retornar o resultado da inserção de propriedades do serviço blob |
> | Ação | Microsoft.Storage/storageAccounts/delete | Excluir uma conta de armazenamento existente. |
> | Ação | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso. |
> | Ação | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso. |
> | Ação | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/logDefinitions/read | Obtém a definição de log para Arquivo |
> | Ação | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/metricDefinitions/read | Obter lista de definições de Métricas de Armazenamento da Microsoft. |
> | Ação | Microsoft.Storage/storageAccounts/lastsynctime/read | Devolve o horário da última sincronização da conta de armazenamento |
> | Ação | Microsoft.Storage/storageAccounts/listAccountSas/action | Retornar o token SAS da conta para a conta de armazenamento especificada. |
> | Ação | Microsoft.Storage/storageAccounts/listkeys/action | Retornar as chaves de acesso da conta de armazenamento especificada. |
> | Ação | Microsoft.Storage/storageAccounts/listServiceSas/action | Retornar o token SAS de serviço para a conta de armazenamento especificada. |
> | Ação | Microsoft.Storage/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso. |
> | Ação | Microsoft.Storage/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso. |
> | Ação | Microsoft.Storage/storageAccounts/providers/Microsoft.Insights/metricDefinitions/read | Obter lista de definições de Métricas de Armazenamento da Microsoft. |
> | Ação | Microsoft.Storage/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso. |
> | Ação | Microsoft.Storage/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso. |
> | Ação | Microsoft.Storage/storageAccounts/queueServices/providers/Microsoft.Insights/logDefinitions/read | Obtém a definição de log para Fila |
> | Ação | Microsoft.Storage/storageAccounts/queueServices/providers/Microsoft.Insights/metricDefinitions/read | Obter lista de definições de Métricas de Armazenamento da Microsoft. |
> | Ação | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Retornar o resultado da exclusão de uma fila |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Retorna o resultado da adição de uma mensagem |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Retorna o resultado da exclusão de uma mensagem |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Retorna o resultado do processamento de uma mensagem |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Retorna uma mensagem |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Retorna o resultado da gravação de uma mensagem |
> | Ação | Microsoft.Storage/storageAccounts/queueServices/queues/read | Retornar uma fila ou uma lista de filas. |
> | Ação | Microsoft.Storage/storageAccounts/queueServices/queues/write | Retornar o resultado da gravação de uma fila |
> | Ação | Microsoft.Storage/storageAccounts/queueServices/read | Retornar propriedades ou estatísticas do serviço de fila. |
> | Ação | Microsoft.Storage/storageAccounts/queueServices/write | Retornar o resultado da configuração das propriedades do serviço de fila |
> | Ação | Microsoft.Storage/storageAccounts/read | Retornar a lista de contas de armazenamento ou obter as propriedades da conta de armazenamento especificada. |
> | Ação | Microsoft.Storage/storageAccounts/regeneratekey/action | Regenerar as chaves de acesso da conta de armazenamento especificada. |
> | Ação | Microsoft.Storage/storageAccounts/revokeUserDelegationKeys/action | Revoga todas as chaves de delegação do usuário para a conta de armazenamento especificada. |
> | Ação | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | Criar/atualizar definições de diagnóstico da conta de armazenamento. |
> | Ação | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso. |
> | Ação | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso. |
> | Ação | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/logDefinitions/read | Obtém a definição de log para Tabela |
> | Ação | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/metricDefinitions/read | Obter lista de definições de Métricas de Armazenamento da Microsoft. |
> | Ação | Microsoft.Storage/storageAccounts/write | Criar uma conta de armazenamento com os parâmetros especificados, atualizar as propriedades ou marcas ou adicionar um domínio personalizado à conta de armazenamento especificada. |
> | Ação | Microsoft.Storage/usages/read | Retornar o limite e a contagem atual de uso de recursos na assinatura especificada |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | microsoft.storagesync/locations/checkNameAvailability/action | Verifica se o nome do serviço de sincronização de armazenamento é válido e não está em uso. |
> | Ação | microsoft.storagesync/locations/workflows/operations/read | Obter o status de uma operação assíncrona |
> | Ação | microsoft.storagesync/storageSyncServices/delete | Excluir os Serviços de Sincronização de Armazenamento |
> | Ação | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para os Serviços de Sincronização de Armazenamento |
> | Ação | microsoft.storagesync/storageSyncServices/read | Ler os Serviços de Sincronização de Armazenamento |
> | Ação | microsoft.storagesync/storageSyncServices/registeredServers/delete | Excluir um servidor registrado |
> | Ação | microsoft.storagesync/storageSyncServices/registeredServers/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para o Servidor Registrado |
> | Ação | microsoft.storagesync/storageSyncServices/registeredServers/read | Ler um servidor registrado |
> | Ação | microsoft.storagesync/storageSyncServices/registeredServers/write | Criar ou atualizar um servidor registrado |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Excluir Pontos de Extremidade de Nuvem |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | Obtém o status de uma operação de backup/restauração assíncrona. |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | Chamar essa ação após o backup |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | Chamar essa ação após restaurar |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | Chamar essa ação antes do backup |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | Chamar essa ação antes de restaurar |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | Ler Pontos de Extremidade de Nuvem |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | Restaurar pulsação |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | Criar ou atualizar Pontos de Extremidade de Nuvem |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/delete | Excluir quaisquer Grupos de Sincronização |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para Grupos de Sincronização |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/read | Lera quaisquer Grupos de Sincronização |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | Excluir qualquer ponto de extremidade do servidor |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para Pontos de Extremidade do Servidor |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | Ler qualquer ponto de extremidade do servidor |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | Chamar essa ação para recuperar arquivos para um servidor |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | Criar ou atualizar qualquer ponto de extremidade de servidor |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/write | Criar ou atualizar Grupos de Sincronização |
> | Ação | microsoft.storagesync/storageSyncServices/workflows/operationresults/read | Obter o status de uma operação assíncrona |
> | Ação | microsoft.storagesync/storageSyncServices/workflows/operations/read | Obter o status de uma operação assíncrona |
> | Ação | microsoft.storagesync/storageSyncServices/workflows/read | Ler fluxos de trabalho |
> | Ação | microsoft.storagesync/storageSyncServices/write | Criar ou atualizar Serviços de Sincronização de Armazenamento |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.StorSimple/managers/accessControlRecords/delete | Excluir os registros de controle de acesso |
> | Ação | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/accessControlRecords/read | Listar ou obter os registros de controle de acesso |
> | Ação | Microsoft.StorSimple/managers/accessControlRecords/write | Criar ou atualizar registros de controle de acesso |
> | Ação | Microsoft.StorSimple/managers/alerts/read | Listar ou obter os alertas |
> | Ação | Microsoft.StorSimple/managers/backups/read | Listar ou obter o conjunto de backup |
> | Ação | Microsoft.StorSimple/managers/bandwidthSettings/delete | Excluir uma configuração de largura de banda existente (somente 8000 Series) |
> | Ação | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | Lista os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/bandwidthSettings/read | Listar as configurações de largura de banda (somente 8000 Series) |
> | Ação | Microsoft.StorSimple/managers/bandwidthSettings/write | Criar novas configurações de largura de banda ou atualizá-las (somente 8000 Series) |
> | Ação | Microsoft.StorSimple/managers/certificates/write | Criar ou atualizar os certificados |
> | Ação | Microsoft.StorSimple/Managers/certificates/write | A operação Atualizar certificado do recurso atualiza o certificado de credencial de cofre/recurso. |
> | Ação | Microsoft.StorSimple/managers/clearAlerts/action | Limpar todos os alertas associados ao Gerenciador de Dispositivos. |
> | Ação | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | Listar configurações com suporte pelo dispositivo de nuvem |
> | Ação | Microsoft.StorSimple/managers/configureDevice/action | Configurar um dispositivo |
> | Ação | Microsoft.StorSimple/managers/delete | Excluir os Gerenciadores de Dispositivo |
> | Ação | Microsoft.StorSimple/Managers/delete | A operação Excluir cofre exclui o recurso do Azure do tipo 'cofre' especificado |
> | Ação | Microsoft.StorSimple/managers/devices/alertSettings/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/alertSettings/read | Listar ou obter as configurações de alerta |
> | Ação | Microsoft.StorSimple/managers/devices/alertSettings/write | Criar ou atualizar as configurações de alerta |
> | Ação | Microsoft.StorSimple/managers/devices/authorizeForServiceEncryptionKeyRollover/action | Autorizar para sobreposição da chave de criptografia de serviço de dispositivos |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | Fazer um backup manual para criar um backup sob demanda de todos os volumes protegidos pela política. |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/delete | Excluir políticas de backup existentes (somente 8000 Series) |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/operationResults/read | Lista os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/read | Listar as políticas de backup (somente 8000 Series) |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | Excluir agendas existentes |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | Lista os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | Listar as agendas |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | Criar ou atualizar agendas |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/write | Criar novas ou atualizar as políticas de Backup (somente 8000 Series) |
> | Ação | Microsoft.StorSimple/managers/devices/backups/delete | Excluir o conjunto de backup |
> | Ação | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | Clonar um compartilhamento ou volume usando um elemento de backup. |
> | Ação | Microsoft.StorSimple/managers/devices/backups/elements/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/backups/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/backups/read | Listar ou obter o conjunto de backup |
> | Ação | Microsoft.StorSimple/managers/devices/backups/restore/action | Restaurar todos os volumes de um conjunto de backup. |
> | Ação | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | Excluir os grupos de agendamento de backup |
> | Ação | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | Listar ou obter os grupos de agendamento de backup |
> | Ação | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | Criar ou atualizar os grupos de agendamento de backup |
> | Ação | Microsoft.StorSimple/managers/devices/chapSettings/delete | Excluir as configurações de Chap |
> | Ação | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/chapSettings/read | Listar ou obter as configurações de Chap |
> | Ação | Microsoft.StorSimple/managers/devices/chapSettings/write | Criar ou atualizar as configurações de Chap |
> | Ação | Microsoft.StorSimple/managers/devices/deactivate/action | Desativar um dispositivo. |
> | Ação | Microsoft.StorSimple/managers/devices/delete | Excluir os dispositivos |
> | Ação | Microsoft.StorSimple/managers/devices/disks/read | Listar ou obter os discos |
> | Ação | Microsoft.StorSimple/managers/devices/download/action | Baixar atualizações para um dispositivo. |
> | Ação | Microsoft.StorSimple/managers/devices/failover/action | Failover do dispositivo. |
> | Ação | Microsoft.StorSimple/managers/devices/failover/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/failoverTargets/read | Lista ou obtém os destinos de failover dos dispositivos |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/backup/action | Fazer backup de um servidor de arquivos. |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/delete | Excluir servidores de arquivos |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | Listar ou obter a métrica |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | Listar ou obter as definições de métrica |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/read | Listar ou obter os servidores de arquivos |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | Excluir os compartilhamentos |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | Listar ou obter a métrica |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | Listar ou obter as definições de métrica |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/shares/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/shares/read | Listar ou obter os compartilhamentos |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/shares/write | Criar ou atualizar os compartilhamentos |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/write | Criar ou atualizar os servidores de arquivos |
> | Ação | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | Alterar o estado de energia do controlador dos grupos de componentes de hardware |
> | Ação | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | Lista os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | Listar os grupos de componentes de hardware |
> | Ação | Microsoft.StorSimple/managers/devices/install/action | Instalar atualizações em um dispositivo. |
> | Ação | Microsoft.StorSimple/managers/devices/installUpdates/action | Instala as atualizações nos dispositivos (apenas série 8000). |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | Fazer backup de um servidor iSCSI. |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/delete | Excluir servidores iSCSI |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | Excluir os discos |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | Listar ou obter a métrica |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | Listar ou obter as definições de métrica |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | Listar ou obter os discos |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | Criar ou atualizar os discos |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | Listar ou obter a métrica |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | Listar ou obter as definições de métrica |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/read | Listar ou obter os servidores iSCSI |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/write | Criar ou atualizar servidores iSCSI |
> | Ação | Microsoft.StorSimple/managers/devices/jobs/cancel/action | Cancelar um trabalho em execução |
> | Ação | Microsoft.StorSimple/managers/devices/jobs/operationResults/read | Lista os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/jobs/read | Listar ou obter os trabalhos |
> | Ação | Microsoft.StorSimple/managers/devices/listFailoverSets/action | Listar os conjuntos de failover para um dispositivo existente (apenas série 8000). |
> | Ação | Microsoft.StorSimple/managers/devices/listFailoverTargets/action | Listar destinos de failover dos dispositivos (apenas Série 8000). |
> | Ação | Microsoft.StorSimple/managers/devices/metrics/read | Listar ou obter a métrica |
> | Ação | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | Listar ou obter as definições de métrica |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | Confirmar uma migração bem-sucedida. |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigrationStatus/read | Listar o status de miração de confirmação |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | Buscar o status de confirmação da migração. |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | Buscar o status do trabalho de estimativa de migração. |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | Buscar o status para a migração. |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | Importar configurações de origem para migração |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationEstimate/read | Listar a estimativa de migração |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationStatus/read | Listar o status de migração |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/operationResults/read | Lista os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | Iniciar migração usando as configurações de origem |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | Iniciar um trabalho para estimar a duração do processo de migração. |
> | Ação | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | Lista os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/networkSettings/read | Listar ou obter as configurações de rede |
> | Ação | Microsoft.StorSimple/managers/devices/networkSettings/write | Criar novas ou atualizar as configurações de rede |
> | Ação | Microsoft.StorSimple/managers/devices/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | Listar chave de criptografia pública do Gerenciador de Dispositivos |
> | Ação | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | Publicar o pacote de suporte de um dispositivo para solução de problemas pelo Suporte da Microsoft. |
> | Ação | Microsoft.StorSimple/managers/devices/read | Listar ou obter os dispositivos |
> | Ação | Microsoft.StorSimple/managers/devices/scanForUpdates/action | Verificar se há atualizações em um dispositivo. |
> | Ação | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/securitySettings/read | Listar as configurações de segurança |
> | Ação | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | Sincronizar o certificado de gerenciamento remoto de um dispositivo. |
> | Ação | Microsoft.StorSimple/managers/devices/securitySettings/update/action | Atualizar as configurações de segurança. |
> | Ação | Microsoft.StorSimple/managers/devices/securitySettings/write | Criar ou atualizar as configurações de segurança |
> | Ação | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | Enviar email de alerta de teste para destinatários de email configurados. |
> | Ação | Microsoft.StorSimple/managers/devices/shares/read | Listar ou obter os compartilhamentos |
> | Ação | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | Lista os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/timeSettings/read | Listar ou obter as configurações de tempo |
> | Ação | Microsoft.StorSimple/managers/devices/timeSettings/write | Criar ou atualizar as configurações de tempo |
> | Ação | Microsoft.StorSimple/managers/devices/updates/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/updateSummary/read | Listar ou obter o resumo de atualização |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/delete | Excluir contêineres de Volume existentes (somente 8000 Series) |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | Listar a métrica |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | Lista as definições de métrica |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | Lista os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/read | Listar os contêineres de volume (somente 8000 Series) |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | Excluir um volume existente |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | Listar a métrica |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | Lista as definições de métrica |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | Lista os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | Listar os volumes |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | Criar ou atualizar volumes |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/write | Criar novos ou atualizar os contêineres de volume (somente 8000 Series) |
> | Ação | Microsoft.StorSimple/managers/devices/volumes/read | Listar os volumes |
> | Ação | Microsoft.StorSimple/managers/devices/write | Criar ou atualizar os dispositivos |
> | Ação | Microsoft.StorSimple/managers/encryptionSettings/read | Listar ou obter as configurações de criptografia |
> | Ação | Microsoft.StorSimple/managers/extendedInformation/delete | Exclui as informações estendidas do cofre |
> | Ação | Microsoft.StorSimple/Managers/extendedInformation/delete | A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
> | Ação | Microsoft.StorSimple/managers/extendedInformation/read | Lista ou obtém as informações estendidas do cofre |
> | Ação | Microsoft.StorSimple/Managers/extendedInformation/read | A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
> | Ação | Microsoft.StorSimple/managers/extendedInformation/write | Criar ou atualizar as informações estendidas do cofre |
> | Ação | Microsoft.StorSimple/Managers/extendedInformation/write | A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
> | Ação | Microsoft.StorSimple/managers/features/read | Listar os recursos |
> | Ação | Microsoft.StorSimple/managers/fileservers/read | Listar ou obter os servidores de arquivos |
> | Ação | Microsoft.StorSimple/managers/getActivationKey/action | Obter a chave de ativação para o gerenciador de dispositivos. |
> | Ação | Microsoft.StorSimple/managers/getEncryptionKey/action | Obter a chave de criptografia para o gerenciador de dispositivos. |
> | Ação | Microsoft.StorSimple/managers/iscsiservers/read | Listar ou obter os servidores iSCSI |
> | Ação | Microsoft.StorSimple/managers/jobs/read | Listar ou obter os trabalhos |
> | Ação | Microsoft.StorSimple/managers/listActivationKey/action | Obter a chave de ativação do Gerenciador de Dispositivo StorSimple. |
> | Ação | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | Listar chaves de criptografia públicas de um Gerenciador de Dispositivo StorSimple. |
> | Ação | Microsoft.StorSimple/managers/metrics/read | Listar ou obter a métrica |
> | Ação | Microsoft.StorSimple/managers/metricsDefinitions/read | Listar ou obter as definições de métrica |
> | Ação | Microsoft.StorSimple/managers/migrateClassicToResourceManager/action | Migrar do Clássico para o Gerenciador de Recursos dos Gerenciadores |
> | Ação | Microsoft.StorSimple/managers/migrationSourceConfigurations/read | Listar as configurações de origem de migração (somente série 8000) |
> | Ação | Microsoft.StorSimple/managers/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/provisionCloudAppliance/action | Criar um novo dispositivo de nuvem. |
> | Ação | Microsoft.StorSimple/managers/read | Listar ou obter os Gerenciadores de Dispositivo |
> | Ação | Microsoft.StorSimple/Managers/read | A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre' |
> | Ação | Microsoft.StorSimple/managers/regenarateRegistationCertificate/action | Regenerar o certificado de registro para os gerenciadores de dispositivo. |
> | Ação | Microsoft.StorSimple/managers/regenerateActivationKey/action | Regenerar a chave de ativação para o gerenciador de dispositivos. |
> | Ação | Microsoft.StorSimple/managers/storageAccountCredentials/delete | Excluir as credenciais da conta de armazenamento |
> | Ação | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/storageAccountCredentials/read | Lista ou obtém as credenciais da conta de armazenamento |
> | Ação | Microsoft.StorSimple/managers/storageAccountCredentials/write | Criar ou atualizar as credenciais da conta de armazenamento |
> | Ação | Microsoft.StorSimple/managers/storageDomains/delete | Excluir os domínios de armazenamento |
> | Ação | Microsoft.StorSimple/managers/storageDomains/operationResults/read | Lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/storageDomains/read | Listar ou obter os domínios de armazenamento |
> | Ação | Microsoft.StorSimple/managers/storageDomains/write | Criar ou atualizar os domínios de armazenamento |
> | Ação | Microsoft.StorSimple/managers/write | Criar ou atualizar os Gerenciadores de Dispositivo |
> | Ação | Microsoft.StorSimple/Managers/write | A operação Criar cofre cria um recurso do Azure do tipo 'cofre' |
> | Ação | Microsoft.StorSimple/operations/read | Lista ou obtém as operações |
> | Ação | Microsoft.StorSimple/register/action | Registrar o provedor Microsoft.StorSimple |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.StreamAnalytics/locations/quotas/Read | Ler Cota de assinatura do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/operations/Read | Ler Operações do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/Register/action | Registrar assinatura com provedor de recursos do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/Delete | Excluir trabalho de Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | Excluir função de trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | Ler os resultados da operação para Função de Trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/functions/Read | Ler Função de Trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | Recuperar a definição padrão de uma Função de Trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | Testar Função de Trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/functions/Write | Gravar Função de Trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | Excluir entrada de trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | Ler os resultados da operação para Entrada de Trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | Ler entrada do trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | Exemplo de Entrada de Trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | Testar Entrada de Trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | Gravar entrada de trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | Ler Definições de Métrica |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | Ler resultados da operação para Trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | Excluir saída de trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | Ler resultados da operação para Saída do Trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | Ler saída do trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | Testar Saída do Trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | Gravar saída de trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | Ler configuração de diagnóstico. |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | Gravar configuração de diagnóstico. |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | Obter os logs disponíveis para streamingjobs |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | Obter a métrica disponível para streamingjobs |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/Read | Ler trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/Start/action | Iniciar trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/Stop/action | Interromper trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | Excluir transformação do trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | Ler transformação do trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | Gravar transformação do trabalho do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/Write | Gravar trabalho de Stream Analytics |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Subscription/CreateSubscription/action | Cria uma assinatura do Azure |
> | Ação | Microsoft.Subscription/SubscriptionDefinitions/read | Obter uma definição de assinatura do Azure em um grupo de gerenciamento. |
> | Ação | Microsoft.Subscription/SubscriptionDefinitions/write | Criar uma definição de assinatura do Azure |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.Support/register/action | Registrar para dar suporte ao provedor de recursos |
> | Ação | Microsoft.Support/supportTickets/read | Obter detalhes de tíquete de suporte (incluindo status, severidade, detalhes de contato e comunicações) ou obtém a lista de tíquetes de suporte entre assinaturas. |
> | Ação | Microsoft.Support/supportTickets/write | Criar ou atualizar um tíquete de suporte. Você pode criar um tíquete de suporte para problemas técnicos, de cotas, de cobrança ou de gerenciamento de assinaturas. Você pode atualizar severidade, detalhes de contato e comunicações de tíquetes de suporte existentes. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | Excluir a política de acesso. |
> | Ação | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | Obter as propriedades de uma política de acesso. |
> | Ação | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | Criar uma nova política de acesso para um ambiente ou atualizar uma política de acesso existente. |
> | Ação | Microsoft.TimeSeriesInsights/environments/delete | Excluir o ambiente. |
> | Ação | Microsoft.TimeSeriesInsights/environments/eventsources/delete | Excluir a origem do evento. |
> | Ação | Microsoft.TimeSeriesInsights/environments/eventsources/eventsources/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.TimeSeriesInsights/environments/eventsources/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.TimeSeriesInsights/environments/eventsources/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para eventsources |
> | Ação | Microsoft.TimeSeriesInsights/environments/eventsources/read | Obter as propriedades de uma origem do evento. |
> | Ação | Microsoft.TimeSeriesInsights/environments/eventsources/write | Criar uma nova origem do evento para um ambiente ou atualizar uma origem do evento existente. |
> | Ação | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para ambientes |
> | Ação | Microsoft.TimeSeriesInsights/environments/read | Obter as propriedades de um ambiente. |
> | Ação | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | Excluir o conjunto de dados de referência. |
> | Ação | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | Obter as propriedades de um conjunto de dados de referência. |
> | Ação | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | Criar um novo conjunto de dados de referência para um ambiente ou atualizar um conjunto de dados de referência existente. |
> | Ação | Microsoft.TimeSeriesInsights/environments/status/read | Obter o status do ambiente, estado das operações associadas, como ingresso. |
> | Ação | Microsoft.TimeSeriesInsights/environments/write | Criar um novo ambiente ou atualizar um ambiente existente. |
> | Ação | Microsoft.TimeSeriesInsights/register/action | Registrar a assinatura do provedor de recursos do Time Series Insights e habilitar a criação de ambientes do Time Series Insights. |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.VisualStudio/Account/Delete | Exclui a Conta |
> | Ação | Microsoft.VisualStudio/Account/Extension/Read | Conta de Leitura/Extensão |
> | Ação | Microsoft.VisualStudio/Account/Project/Read | Ler conta/projeto |
> | Ação | Microsoft.VisualStudio/Account/Project/Write | Definir conta/projeto |
> | Ação | Microsoft.VisualStudio/Account/Read | Lê a Conta |
> | Ação | Microsoft.VisualStudio/Account/Write | Define a Conta |
> | Ação | Microsoft.VisualStudio/Extension/Delete | Exclui a Extensão |
> | Ação | Microsoft.VisualStudio/Extension/Read | Lê a Extensão |
> | Ação | Microsoft.VisualStudio/Extension/Write | Define a Extensão |
> | Ação | Microsoft.VisualStudio/Project/Delete | Exclui o Projeto |
> | Ação | Microsoft.VisualStudio/Project/Read | Lê o Projeto |
> | Ação | Microsoft.VisualStudio/Project/Write | Define o Projeto |
> | Ação | Microsoft.VisualStudio/Register/Action | Registrar a assinatura do Azure com o provedor de Microsoft.VisualStudio |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | microsoft.web/apimanagementaccounts/apiacls/read | Obter Apiacls de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/apiacls/delete | Excluir Apiacls de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/apiacls/read | Obter Apiacls de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/apiacls/write | Atualizar Apiacls de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connectionacls/read | Obter Connectionacls de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/confirmconsentcode/action | Confirmar código de consentimento de conexões de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | Excluir Connectionacls de Conexões de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/read | Obter Connectionacls de Conexões de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/write | Atualizar Connectionacls de Conexões de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/delete | Excluir Conexões de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/getconsentlinks/action | Obter links de consentimento para conexões de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/listconnectionkeys/action | Listar chaves de conexão de conexões de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/listsecrets/action | Listar Segredos de conexões de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/read | Obter conexões de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/write | Atualizar conexões de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/delete | Excluir APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | Excluir definições localizadas de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | Obter definições localizadas de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | Atualizar definições localizadas de APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/read | Obter APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/write | Atualizar APIs de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/connectionacls/read | Obter Connectionacls de Contas de Gerenciamento de API. |
> | Ação | microsoft.web/availablestacks/read | Obter pilhas disponíveis. |
> | Ação | microsoft.web/billingmeters/read | Obter lista de medidores de cobrança. |
> | Ação | Microsoft.Web/certificates/Delete | Excluir um certificado existente. |
> | Ação | Microsoft.Web/certificates/Read | Obter a lista de certificados. |
> | Ação | Microsoft.Web/certificates/Write | Adicionar um novo certificado ou atualizar um existente. |
> | Ação | microsoft.web/checknameavailability/read | Verificar se o nome do recurso está disponível. |
> | Ação | microsoft.web/classicmobileservices/read | Obter Serviços Móveis clássicos. |
> | Ação | Microsoft.Web/connectionGateways/Delete | Excluir um Gateway de Conexão. |
> | Ação | Microsoft.Web/connectionGateways/Join/Action | Adicionar um Gateway de Conexão. |
> | Ação | Microsoft.Web/connectionGateways/ListStatus/Action | Listar status de um gateway de conexão. |
> | Ação | Microsoft.Web/connectionGateways/Move/Action | Mover um gateway de conexão. |
> | Ação | Microsoft.Web/connectionGateways/Read | Obter a lista de gateways de conexão. |
> | Ação | Microsoft.Web/connectionGateways/Write | Criar ou atualizar um Gateway de Conexão. |
> | Ação | microsoft.web/connections/confirmconsentcode/action | Confirmar o código de autorização de conexões. |
> | Ação | Microsoft.Web/connections/Delete | Excluir uma conexão. |
> | Ação | Microsoft.Web/connections/Join/Action | Ingressar em uma conexão. |
> | Ação | microsoft.web/connections/listconsentlinks/action | Listar os links de autorização para conexões. |
> | Ação | Microsoft.Web/connections/Move/Action | Mover uma conexão. |
> | Ação | Microsoft.Web/connections/Read | Obter a lista de conexões. |
> | Ação | Microsoft.Web/connections/Write | Criar ou atualizar uma conexão. |
> | Ação | Microsoft.Web/customApis/Delete | Excluir uma API personalizada. |
> | Ação | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | Extrair a definição de API de uma WSDL. |
> | Ação | Microsoft.Web/customApis/Join/Action | Adicionar uma API personalizada. |
> | Ação | Microsoft.Web/customApis/listWsdlInterfaces/Action | Listar interfaces WSDL para uma API personalizada. |
> | Ação | Microsoft.Web/customApis/Move/Action | Mover uma API personalizada. |
> | Ação | Microsoft.Web/customApis/Read | Obter a lista de API personalizada. |
> | Ação | Microsoft.Web/customApis/Write | Criar ou atualizar uma API personalizada. |
> | Ação | Microsoft.Web/deletedSites/Read | Obtém as propriedades de um aplicativo Web excluído |
> | Ação | microsoft.web/deploymentlocations/read | Obter locais de implantação. |
> | Ação | Microsoft.Web/geoRegions/Read | Obter a lista de regiões geográficas. |
> | Ação | microsoft.web/hostingenvironments/capacities/read | Obter as capacidades dos ambientes de hospedagem. |
> | Ação | Microsoft.Web/hostingEnvironments/Delete | Excluir ambiente do Serviço de Aplicativo |
> | Ação | microsoft.web/hostingenvironments/detectors/read | Obtém detectores de ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/diagnostics/read | Obter diagnóstico dos ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | Obter os pontos de extremidade de rede de todas as dependências de entrada. |
> | Ação | microsoft.web/hostingenvironments/metricdefinitions/read | Obter definições de métrica de ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/multirolepools/metricdefinitions/read | Obter definições de métrica de pools multifunção dos ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/multirolepools/metrics/read | Obter a métrica de pools multifunção em Ambientes de Hospedagem. |
> | Ação | Microsoft.Web/hostingEnvironments/multiRolePools/providers/Microsoft.Insights/metricDefinitions/Read | Obter as métricas disponíveis para o MultiRole do Ambiente do Serviço de Aplicativo |
> | Ação | Microsoft.Web/hostingEnvironments/multiRolePools/Read | Obter as propriedades de um pool de front-end em um Ambiente do Serviço de Aplicativo |
> | Ação | microsoft.web/hostingenvironments/multirolepools/skus/read | Obter SKUs de pools multifunção dos ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/multirolepools/usages/read | Obter usos dos pools multifunção dos ambientes de hospedagem. |
> | Ação | Microsoft.Web/hostingEnvironments/multiRolePools/Write | Criar um novo pool de front-end em um Ambiente do Serviço de Aplicativo ou atualizar um existente |
> | Ação | microsoft.web/hostingenvironments/operations/read | Obter operações dos ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | Obter os pontos de extremidade de rede de todas as dependências de saída. |
> | Ação | microsoft.web/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | microsoft.web/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Web/hostingEnvironments/Read | Obter as propriedades de um Ambiente do Serviço de Aplicativo |
> | Ação | Microsoft.Web/hostingEnvironments/reboot/Action | Reinicializar todas as máquinas em um Ambiente do Serviço de Aplicativo |
> | Ação | microsoft.web/hostingenvironments/resume/action | Retomar ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/serverfarms/read | Obter Planos do Serviço de Aplicativo dos Ambientes de Hospedagem. |
> | Ação | microsoft.web/hostingenvironments/sites/read | Obter aplicativos Web dos ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/suspend/action | Suspender ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/usages/read | Obter usos dos ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | Obter definições de métrica de pools de trabalho dos ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/workerpools/metrics/read | Obter a métrica de pools de trabalho de Ambientes de Hospedagem. |
> | Ação | Microsoft.Web/hostingEnvironments/workerPools/providers/Microsoft.Insights/metricDefinitions/Read | Obter as métricas disponíveis para o WorkerPool do Ambiente do Serviço de Aplicativo |
> | Ação | Microsoft.Web/hostingEnvironments/workerPools/Read | Obter as propriedades de um pool de trabalho em um Ambiente do Serviço de Aplicativo |
> | Ação | microsoft.web/hostingenvironments/workerpools/skus/read | Obter SKUs de pools de trabalho dos ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/workerpools/usages/read | Obter uso dos pools de trabalho dos ambientes de hospedagem. |
> | Ação | Microsoft.Web/hostingEnvironments/workerPools/Write | Criar um novo pool de trabalho em um Ambiente do Serviço de Aplicativo ou atualizar um existente |
> | Ação | Microsoft.Web/hostingEnvironments/Write | Criar um novo Ambiente do Serviço de Aplicativo ou atualizar um existente |
> | Ação | microsoft.web/ishostingenvironmentnameavailable/read | Obter confirmação se o nome do ambiente de hospedagem está disponível. |
> | Ação | microsoft.web/ishostnameavailable/read | Verificar se o nome do host está disponível. |
> | Ação | microsoft.web/isusernameavailable/read | Verificar se o nome de usuário está disponível. |
> | Ação | Microsoft.Web/listSitesAssignedToHostName/Read | Obter nomes dos sites atribuídos ao nome de host. |
> | Ação | microsoft.web/locations/apioperations/read | Obter operações API dos locais. |
> | Ação | microsoft.web/locations/connectiongatewayinstallations/read | Obter as instalações de gateway de conexão locais. |
> | Ação | microsoft.web/locations/extractapidefinitionfromwsdl/action | Extrair a definição de API da WSDL para locais. |
> | Ação | microsoft.web/locations/listwsdlinterfaces/action | Listar interfaces WSDL para locais. |
> | Ação | microsoft.web/locations/managedapis/apioperations/read | Obter operações de API gerenciadas dos locais. |
> | Ação | Microsoft.Web/locations/managedapis/Join/Action | Adicionar uma API gerenciada. |
> | Ação | microsoft.web/locations/managedapis/read | Obter as APIs gerenciadas dos locais. |
> | Ação | microsoft.web/operations/read | Obter Operações. |
> | Ação | microsoft.web/publishingusers/read | Obter usuários de publicação. |
> | Ação | microsoft.web/publishingusers/write | Atualizar usuários de publicação. |
> | Ação | Microsoft.Web/recommendations/Read | Obter a lista de recomendações para assinaturas. |
> | Ação | microsoft.web/register/action | Registrar o provedor de recursos Microsoft.Web para a assinatura. |
> | Ação | microsoft.web/resourcehealthmetadata/read | Obter metadados do Resource Health. |
> | Ação | microsoft.web/serverfarms/capabilities/read | Obter recursos do Planos do Serviço de Aplicativo. |
> | Ação | Microsoft.Web/serverfarms/Delete | Excluir um Plano do Serviço de Aplicativo existente |
> | Ação | microsoft.web/serverfarms/firstpartyapps/settings/delete | Excluir configurações de aplicativos próprios dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/firstpartyapps/settings/read | Obter configurações de aplicativos próprios dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/firstpartyapps/settings/write | Atualizar configurações de aplicativos próprios dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/delete | Excluir retransmissões de namespaces de conexão híbrida dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/read | Obter retransmissões de namespaces de conexão híbrida dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/sites/read | Obter aplicativos Web das retransmissões dos namespaces da conexão híbrida dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/hybridconnectionplanlimits/read | Obter limites do plano de conexão híbrida dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/hybridconnectionrelays/read | Obter transmissões de conexão híbrida dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/metricdefinitions/read | Obter definições de métrica dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/metrics/read | Obter métrica dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/operationresults/read | Obter resultados de operação dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | microsoft.web/serverfarms/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Web/serverfarms/providers/Microsoft.Insights/metricDefinitions/Read | Obter as métricas disponíveis para o Plano do Serviço de Aplicativo |
> | Ação | Microsoft.Web/serverfarms/Read | Obter as propriedades em um Plano do Serviço de Aplicativo |
> | Ação | Microsoft.Web/serverfarms/restartSites/Action | Reiniciar todos os aplicativos Web em um Plano do Serviço de Aplicativo |
> | Ação | microsoft.web/serverfarms/sites/read | Obter aplicativos Web dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/skus/read | Obter as SKUs dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/usages/read | Obter usos dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | Atualizar gateways de conexões de rede virtual dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/virtualnetworkconnections/read | Obter as conexões de rede virtual dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | Excluir rotas de conexões de rede virtual dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | Obter a rotas de conexões de rede virtual dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | Atualizar rotas de conexões de rede virtual dos Planos do Serviço de Aplicativo. |
> | Ação | microsoft.web/serverfarms/workers/reboot/action | Reinicializar trabalhos dos Planos do Serviço de Aplicativo. |
> | Ação | Microsoft.Web/serverfarms/Write | Criar um novo Plano do Serviço de Aplicativo ou atualizar um plano existente |
> | Ação | microsoft.web/sites/analyzecustomhostname/read | Analisar nome de host personalizado. |
> | Ação | Microsoft.Web/sites/applySlotConfig/Action | Aplicar a configuração de slot de aplicativo Web do slot de destino ao aplicativo Web atual |
> | Ação | Microsoft.Web/sites/backup/Action | Criar um novo backup do aplicativo Web |
> | Ação | microsoft.web/sites/backup/read | Obter o backup de aplicativos Web. |
> | Ação | microsoft.web/sites/backup/write | Atualizar o backup de aplicativos Web. |
> | Ação | microsoft.web/sites/backups/action | Descobre um backup existente de aplicativo que pode ser restaurado de um blob no Armazenamento do Azure. |
> | Ação | microsoft.web/sites/backups/delete | Excluir backups de aplicativos Web. |
> | Ação | microsoft.web/sites/backups/list/action | Listar backups de aplicativos de Web. |
> | Ação | Microsoft.Web/sites/backups/Read | Obter as propriedades do backup de um aplicativo Web |
> | Ação | microsoft.web/sites/backups/restore/action | Restaurar backups de aplicativos Web. |
> | Ação | microsoft.web/sites/backups/write | Atualiza os backup de aplicativos Web. |
> | Ação | microsoft.web/sites/config/delete | Excluir configuração de aplicativos Web. |
> | Ação | Microsoft.Web/sites/config/list/Action | Listar as configurações confidenciais de segurança do aplicativo Web, como credenciais de publicação, configurações do aplicativo e cadeias de conexão |
> | Ação | Microsoft.Web/sites/config/Read | Obter definições de configuração do aplicativo Web |
> | Ação | microsoft.web/sites/config/snapshots/read | Obter instantâneos de configuração de aplicativos Web. |
> | Ação | Microsoft.Web/sites/config/Write | Atualizar definições de configuração do aplicativo Web |
> | Ação | microsoft.web/sites/containerlogs/action | Obtém Logs de contêiner compactados para o aplicativo Web. |
> | Ação | microsoft.web/sites/continuouswebjobs/delete | Excluir trabalhos da Web contínuos de aplicativos Web. |
> | Ação | microsoft.web/sites/continuouswebjobs/read | Obter trabalhos de Web contínuos de aplicativos Web. |
> | Ação | microsoft.web/sites/continuouswebjobs/start/action | Iniciar trabalhos de Web contínuos de aplicativos Web. |
> | Ação | microsoft.web/sites/continuouswebjobs/stop/action | Interromper trabalhos de Web contínuos de aplicativos Web. |
> | Ação | Microsoft.Web/sites/Delete | Excluir um aplicativo Web existente |
> | Ação | microsoft.web/sites/deployments/delete | Excluir as implantações de aplicativos Web. |
> | Ação | microsoft.web/sites/deployments/log/read | Obter o log de implantações de aplicativos Web. |
> | Ação | microsoft.web/sites/deployments/read | Obter as implantações de aplicativos Web. |
> | Ação | microsoft.web/sites/deployments/write | Atualizar as implantações de aplicativos Web. |
> | Ação | microsoft.web/sites/detectors/read | Obtém os detectores de aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/analyses/execute/Action | Executar Análise de Diagnóstico de Aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/analyses/read | Obter Análise de Diagnóstico de Aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/aspnetcore/read | Obter Diagnóstico de Aplicativos Web para aplicativo ASP.NET Core. |
> | Ação | microsoft.web/sites/diagnostics/autoheal/read | Obter Diagnóstico de Aplicativos Web para Autoheal. |
> | Ação | microsoft.web/sites/diagnostics/deployment/read | Obter a implantação de Diagnóstico de Aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/deployments/read | Obter implantações de diagnóstico de Aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/detectors/execute/Action | Executar o Detector de Diagnóstico de Aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/detectors/read | Obter o Detector de Diagnóstico de Aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/failedrequestsperuri/read | Obter solicitações de falha de diagnóstico de Aplicativos Web por URI. |
> | Ação | microsoft.web/sites/diagnostics/frebanalysis/read | Obter a análise FREB do diagnóstico de aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/loganalyzer/read | Obter analisador de log de diagnóstico de Aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/read | Obter categorias de diagnóstico de Aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/runtimeavailability/read | Obter a disponibilidade de tempo de execução do diagnóstico de aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/servicehealth/read | Obter integridade do serviço de diagnóstico de aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/sitecpuanalysis/read | Obter análise de CPU do site de diagnóstico de Aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/sitecrashes/read | Obter falhas do Site de Diagnósticos de Aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/sitelatency/read | Obter a latência do Site de Diagnósticos de Aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/sitememoryanalysis/read | Obter análise de memória do site de diagnósticos de Aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/siterestartsettingupdate/read | Obter atualização de configuração de reinicialização do site de diagnóstico de Aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/siterestartuserinitiated/read | Obter reinicialização do site de diagnóstico de Aplicativos Web iniciado pelo usuário. |
> | Ação | microsoft.web/sites/diagnostics/siteswap/read | Obter troca de site de diagnóstico de Aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/threadcount/read | Obter contagem de threads de diagnóstico de Aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/workeravailability/read | Obter Workeravailability de diagnóstico de aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/workerprocessrecycle/read | Obter a reciclagem do processo de trabalho do diagnóstico de aplicativos Web. |
> | Ação | microsoft.web/sites/domainownershipidentifiers/read | Obter identificadores de propriedade de domínio de aplicativos Web. |
> | Ação | microsoft.web/sites/domainownershipidentifiers/write | Atualizar de identificadores de propriedade de domínio de aplicativos Web. |
> | Ação | microsoft.web/sites/functions/action | Aplicativos Web do Functions. |
> | Ação | microsoft.web/sites/functions/delete | Excluir funções de aplicativos Web. |
> | Ação | microsoft.web/sites/functions/listsecrets/action | Listar segredos de funções de aplicativos Web. |
> | Ação | microsoft.web/sites/functions/masterkey/read | Obter a chave mestra de funções de Aplicativos Web. |
> | Ação | microsoft.web/sites/functions/read | Obter funções de aplicativos Web. |
> | Ação | microsoft.web/sites/functions/token/read | Obter token de funções de Aplicativos Web. |
> | Ação | microsoft.web/sites/functions/write | Atualizar funções de aplicativos Web. |
> | Ação | microsoft.web/sites/hostnamebindings/delete | Excluir associações de nome de host de aplicativos Web. |
> | Ação | microsoft.web/sites/hostnamebindings/read | Obter associações de nome de host de aplicativos Web. |
> | Ação | microsoft.web/sites/hostnamebindings/write | Atualizar associações de nome de host de aplicativos Web. |
> | Ação | Microsoft.Web/sites/hostruntime/host/_master/read | Obter chave mestra do Aplicativo de funções para operações de administrador |
> | Ação | Microsoft.Web/sites/hostruntime/host/action | Executar a ação de tempo de execução do Aplicativo de funções como gatilhos de sincronização, adicionar funções, invocar funções, excluir funções, etc. |
> | Ação | microsoft.web/sites/hybridconnection/delete | Excluir a conexão híbrida de aplicativos Web. |
> | Ação | microsoft.web/sites/hybridconnection/read | Obter a conexão híbrida de aplicativos Web. |
> | Ação | microsoft.web/sites/hybridconnection/write | Atualizar a conexão híbrida de aplicativos Web. |
> | Ação | microsoft.web/sites/hybridconnectionnamespaces/relays/delete | Excluir retransmissões de namespaces de conexão híbrida de Aplicativos Web. |
> | Ação | microsoft.web/sites/hybridconnectionnamespaces/relays/listkeys/action | Listar chaves de retransmissões de namespaces de conexão híbrida de Aplicativos Web. |
> | Ação | microsoft.web/sites/hybridconnectionnamespaces/relays/read | Obter retransmissões de namespaces de conexão híbrida de Aplicativos Web. |
> | Ação | microsoft.web/sites/hybridconnectionnamespaces/relays/write | Atualizar retransmissões de namespaces de conexão híbrida de Aplicativos Web. |
> | Ação | microsoft.web/sites/hybridconnectionrelays/read | Obter transmissões de conexão híbrida de aplicativos Web. |
> | Ação | microsoft.web/sites/instances/deployments/delete | Excluir implantações de instâncias de Aplicativos Web. |
> | Ação | microsoft.web/sites/instances/deployments/read | Obter as implantações de instâncias de aplicativos Web. |
> | Ação | microsoft.web/sites/instances/extensions/log/read | Obter log de extensões de instâncias de Aplicativos Web. |
> | Ação | microsoft.web/sites/instances/extensions/read | Obter extensões de instâncias de Aplicativos Web. |
> | Ação | microsoft.web/sites/instances/processes/delete | Excluir processos de instâncias de aplicativos Web. |
> | Ação | microsoft.web/sites/instances/processes/read | Obter os processos de instâncias de aplicativos Web. |
> | Ação | microsoft.web/sites/instances/processes/threads/read | Obter threads de processos de instâncias de aplicativos Web. |
> | Ação | microsoft.web/sites/instances/read | Obter instâncias de aplicativos Web. |
> | Ação | microsoft.web/sites/listsyncfunctiontriggerstatus/action | Listar Aplicativos Web de status do gatilho da função de sincronização. |
> | Ação | microsoft.web/sites/metricdefinitions/read | Obter definições de métrica de aplicativos Web. |
> | Ação | microsoft.web/sites/metrics/read | Métrica de aplicativos Web. |
> | Ação | microsoft.web/sites/metricsdefinitions/read | Obter definições de métricas de Aplicativos Web. |
> | Ação | microsoft.web/sites/migratemysql/action | Migrar Aplicativos Web do MySql. |
> | Ação | microsoft.web/sites/migratemysql/read | Obter migração do MySql de Aplicativos Web. |
> | Ação | microsoft.web/sites/networktrace/action | Aplicativos Web de rastreamento de rede. |
> | Ação | microsoft.web/sites/newpassword/action | Aplicativos Web Newpassword. |
> | Ação | microsoft.web/sites/operationresults/read | Obter resultados de operação de aplicativos Web. |
> | Ação | microsoft.web/sites/operations/read | Obter operações de Aplicativos Web. |
> | Ação | microsoft.web/sites/perfcounters/read | Obter contadores de desempenho de aplicativos Web. |
> | Ação | microsoft.web/sites/premieraddons/delete | Excluir complementos Premier de aplicativos Web. |
> | Ação | microsoft.web/sites/premieraddons/read | Obter complementos Premier de aplicativos Web. |
> | Ação | microsoft.web/sites/premieraddons/write | Atualizar complementos Premier de aplicativos Web. |
> | Ação | microsoft.web/sites/privateaccess/read | Obter dados sobre a habilitação de acesso a sites privados e Redes Virtuais autorizadas que podem acessar o site. |
> | Ação | microsoft.web/sites/processes/read | Obter processos de Aplicativos Web. |
> | Ação | microsoft.web/sites/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | microsoft.web/sites/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | microsoft.web/sites/providers/Microsoft.Insights/logDefinitions/read | Obtém os logs disponíveis para o aplicativo Web |
> | Ação | Microsoft.Web/sites/providers/Microsoft.Insights/metricDefinitions/Read | Obter as métricas disponíveis para Aplicativo Web |
> | Ação | microsoft.web/sites/publiccertificates/delete | Excluir certificados públicos de Aplicativos Web. |
> | Ação | microsoft.web/sites/publiccertificates/read | Obter certificados públicos de Aplicativos Web. |
> | Ação | microsoft.web/sites/publiccertificates/write | Atualizar certificados públicos de Aplicativos Web. |
> | Ação | Microsoft.Web/sites/publish/Action | Publicar um aplicativo Web |
> | Ação | Microsoft.Web/sites/publishxml/Action | Obter XML do perfil de publicação para um aplicativo Web |
> | Ação | microsoft.web/sites/publishxml/read | Obter XML de publicação de aplicativos Web. |
> | Ação | Microsoft.Web/sites/Read | Obter as propriedades de um aplicativo Web |
> | Ação | microsoft.web/sites/recommendationhistory/read | Obter o histórico de recomendação de aplicativos Web. |
> | Ação | microsoft.web/sites/recommendations/disable/action | Desabilitar as recomendações de aplicativos Web. |
> | Ação | Microsoft.Web/sites/recommendations/Read | Obter a lista de recomendações para o aplicativo Web. |
> | Ação | microsoft.web/sites/recover/action | Recuperar Aplicativos Web. |
> | Ação | Microsoft.Web/sites/resetSlotConfig/Action | Redefinir a configuração de aplicativo Web |
> | Ação | microsoft.web/sites/resourcehealthmetadata/read | Obter metadados do Resource Health de Aplicativos Web. |
> | Ação | Microsoft.Web/sites/restart/Action | Reiniciar um aplicativo Web |
> | Ação | microsoft.web/sites/restore/read | Obter a restauração de aplicativos Web. |
> | Ação | microsoft.web/sites/restore/write | Restaurar Aplicativos Web. |
> | Ação | microsoft.web/sites/restorefrombackupblob/action | Restaurar o aplicativo Web a partir do blob de backup. |
> | Ação | microsoft.web/sites/restorefromdeletedwebapp/action | Restaura os aplicativos Web do aplicativo excluído. |
> | Ação | microsoft.web/sites/restoresnapshot/action | Restaura os Instantâneos de Aplicativos Web. |
> | Ação | microsoft.web/sites/siteextensions/delete | Excluir extensões de site de Aplicativos Web. |
> | Ação | microsoft.web/sites/siteextensions/read | Obter extensões de site de Aplicativos Web. |
> | Ação | microsoft.web/sites/siteextensions/write | Atualizar extensões de site de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/analyzecustomhostname/read | Obter slots de aplicativos Web Analisar nome de host personalizado. |
> | Ação | Microsoft.Web/sites/slots/applySlotConfig/Action | Aplicar a configuração de slot de aplicativo Web do slot de destino ao slot atual. |
> | Ação | Microsoft.Web/sites/slots/backup/Action | Criar o novo backup do slot do aplicativo Web. |
> | Ação | microsoft.web/sites/slots/backup/read | Obter backup de Slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/backup/write | Atualizar o backup de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/backups/action | Descobrir backups de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/backups/delete | Excluir backups de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/backups/list/action | Listar backups de slots de aplicativos de Web. |
> | Ação | Microsoft.Web/sites/slots/backups/Read | Obter as propriedades do backup dos slots de um aplicativo Web |
> | Ação | microsoft.web/sites/slots/backups/restore/action | Restaurar backups de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/config/delete | Excluir configuração de slots de aplicativos Web. |
> | Ação | Microsoft.Web/sites/slots/config/list/Action | Listar as configurações confidenciais de segurança do slot do aplicativo Web, como credenciais de publicação, configurações do aplicativo e cadeias de conexão |
> | Ação | Microsoft.Web/sites/slots/config/Read | Obter definições de configuração do slot do aplicativo Web |
> | Ação | Microsoft.Web/sites/slots/config/Write | Atualizar definições de configuração do slot do aplicativo Web |
> | Ação | microsoft.web/sites/slots/containerlogs/action | Obter logs de contêiner compactados para slot do aplicativo Web. |
> | Ação | microsoft.web/sites/slots/continuouswebjobs/delete | Excluir trabalhos da Web contínuos de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/continuouswebjobs/read | Obter trabalhos da Web contínuos de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/continuouswebjobs/start/action | Iniciar trabalhos da Web contínuos de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/continuouswebjobs/stop/action | Interromper trabalhos da Web contínuos de slots de aplicativos Web. |
> | Ação | Microsoft.Web/sites/slots/Delete | Excluir um slot de aplicativo Web existente |
> | Ação | microsoft.web/sites/slots/deployments/delete | Excluir as implantações de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/deployments/log/read | Obter o log de implantações dos slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/deployments/read | Obter as implantações dos slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/deployments/write | Atualizar as implantações de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/detectors/read | Obter detectores de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | Executar análise de diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/analyses/read | Obter análise de diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/aspnetcore/read | Obter o diagnóstico de slots de Aplicativos Web para o aplicativo ASP.NET Core. |
> | Ação | microsoft.web/sites/slots/diagnostics/autoheal/read | Obter o diagnóstico de slots de Aplicativos Web para Autoheal. |
> | Ação | microsoft.web/sites/slots/diagnostics/deployment/read | Obter implantação de diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/deployments/read | Obter implantações de diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | Executar o detector de diagnóstico de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/detectors/read | Obter detector de diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/frebanalysis/read | Obter análise FREB de diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/loganalyzer/read | Obter analisador de log de diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/read | Obter diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/runtimeavailability/read | Obter disponibilidade de tempo de execução de diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/servicehealth/read | Obter Integridade do Serviço do Azure do diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/sitecpuanalysis/read | Obter análise de CPU de site de diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/sitecrashes/read | Obter falhas nos sites de diagnóstico dos slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/sitelatency/read | Obter latência de sites de diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/sitememoryanalysis/read | Obter análise de memória do site de diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/siterestartsettingupdate/read | Obter atualização de configuração de reinicialização do site de diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/siterestartuserinitiated/read | Obter diagnóstico de slots de Aplicativos Web iniciado pelo usuário. |
> | Ação | microsoft.web/sites/slots/diagnostics/siteswap/read | Obter troca de sites de diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/threadcount/read | Obter contagem de threads de diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/workeravailability/read | Obter disponibilidade do trabalho de diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/workerprocessrecycle/read | Obter reciclagem de processo de trabalho de diagnóstico de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/domainownershipidentifiers/read | Obter identificadores de propriedade de domínio de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/functions/read | Obter funções de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/hostnamebindings/delete | Excluir vínculos de nome de host de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/hostnamebindings/read | Obter vínculos de nome de host de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/hostnamebindings/write | Atualizar vínculos de nome de host de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/hybridconnection/delete | Excluir a conexão híbrida de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/hybridconnection/read | Obter a conexão híbrida de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/hybridconnection/write | Atualizar a conexão híbrida de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/delete | Excluir retransmissões de namespaces de conexão híbrida de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/write | Atualizar retransmissões de namespaces de conexão híbrida de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/hybridconnectionrelays/read | Obter retransmissões de conexão híbrida de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/instances/deployments/read | Obter as implantações de instâncias dos slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/instances/processes/delete | Excluir processos de instâncias de slots de plicativos Web. |
> | Ação | microsoft.web/sites/slots/instances/processes/read | Obter os processos de instâncias dos slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/instances/read | Obter instâncias dos slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/metricdefinitions/read | Obter definições de métrica de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/metrics/read | Obter métrica dos slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/migratemysql/read | Obter migração do MySql de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/networktrace/action | Slots de Aplicativos Web de rastreamento de rede. |
> | Ação | microsoft.web/sites/slots/newpassword/action | Slots de aplicativos Web Newpassword. |
> | Ação | microsoft.web/sites/slots/operationresults/read | Obter resultados de operação de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/operations/read | Obter operações de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/perfcounters/read | Obter contadores de desempenho de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/phplogging/read | Obter Phplogging dos slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/premieraddons/delete | Excluir complementos de Premier de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/premieraddons/read | Obter complementos Premier de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/premieraddons/write | Atualizar complementos Premier de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Ação | microsoft.web/sites/slots/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso |
> | Ação | microsoft.web/sites/slots/providers/Microsoft.Insights/logDefinitions/read | Obtém os logs disponíveis para os slots do aplicativo Web |
> | Ação | Microsoft.Web/sites/slots/providers/Microsoft.Insights/metricDefinitions/Read | Obter as métricas disponíveis para slot de Aplicativo Web |
> | Ação | microsoft.web/sites/slots/publiccertificates/delete | Excluir certificados públicos de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/publiccertificates/read | Obter certificados públicos de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/publiccertificates/write | Criar ou atualizar certificados públicos de slots de Aplicativos Web. |
> | Ação | Microsoft.Web/sites/slots/publish/Action | Publicar um slot do aplicativo Web |
> | Ação | Microsoft.Web/sites/slots/publishxml/Action | Obter XML do perfil de publicação para um slot de aplicativo Web |
> | Ação | Microsoft.Web/sites/slots/Read | Obter as propriedades de um slot de implantação de aplicativo Web |
> | Ação | microsoft.web/sites/slots/recover/action | Recupera Slots de Aplicativos Web. |
> | Ação | Microsoft.Web/sites/slots/resetSlotConfig/Action | Redefinir a configuração de slot do aplicativo Web |
> | Ação | microsoft.web/sites/slots/resourcehealthmetadata/read | Obter metadados do Resource Health de Aplicativos Web. |
> | Ação | Microsoft.Web/sites/slots/restart/Action | Reiniciar um slot de aplicativo Web |
> | Ação | microsoft.web/sites/slots/restore/read | Obter a restauração dos slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/restore/write | Restaurar slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/restorefrombackupblob/action | Restaurar o slot de aplicativos Web a partir do blob de backup. |
> | Ação | microsoft.web/sites/slots/restorefromdeletedwebapp/action | Restaura os slots de aplicativo Web do aplicativo excluído. |
> | Ação | microsoft.web/sites/slots/restoresnapshot/action | Restaura os Instantâneos de Slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/siteextensions/delete | Excluir extensões de site de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/siteextensions/read | Obter extensões de site de slots de Aplicativos Web. |
> | Ação | microsoft.web/sites/slots/siteextensions/write | Atualizar extensões de site de slots de Aplicativos Web. |
> | Ação | Microsoft.Web/sites/slots/slotsdiffs/Action | Obter as diferenças de configuração entre aplicativo web e slots |
> | Ação | Microsoft.Web/sites/slots/slotsswap/Action | Trocar slots de implantação de aplicativo Web |
> | Ação | microsoft.web/sites/slots/snapshots/read | Obter instantâneos de slots de Aplicativos Web. |
> | Ação | Microsoft.Web/sites/slots/sourcecontrols/Delete | Excluir definições de configuração de controle de origem do slot do aplicativo Web |
> | Ação | Microsoft.Web/sites/slots/sourcecontrols/Read | Obter definições de configuração de controle de origem do slot do aplicativo Web |
> | Ação | Microsoft.Web/sites/slots/sourcecontrols/Write | Atualizar definições de configuração de controle de origem do slot do aplicativo Web |
> | Ação | Microsoft.Web/sites/slots/start/Action | Iniciar um slot de aplicativo Web |
> | Ação | Microsoft.Web/sites/slots/stop/Action | Interromper um slot de aplicativo Web |
> | Ação | microsoft.web/sites/slots/sync/action | Sincronizar slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/triggeredwebjobs/delete | Excluir trabalhos da Web disparados de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/triggeredwebjobs/read | Obter trabalhos da Web disparados de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/triggeredwebjobs/run/action | Executar trabalhos da Web dos slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/usages/read | Obter usos dos slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/virtualnetworkconnections/delete | Excluir conexões de rede virtual de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/virtualnetworkconnections/gateways/write | Atualizar gateways de conexões de rede virtual de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/virtualnetworkconnections/read | Obter conexões de rede virtual dos slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/virtualnetworkconnections/write | Atualizar conexões de rede virtual de slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/webjobs/read | Obter trabalhos da Web dos slots de aplicativos Web. |
> | Ação | Microsoft.Web/sites/slots/Write | Criar um novo slot do aplicativo Web ou atualizar um existente |
> | Ação | Microsoft.Web/sites/slotsdiffs/Action | Obter as diferenças de configuração entre aplicativo web e slots |
> | Ação | Microsoft.Web/sites/slotsswap/Action | Trocar slots de implantação de aplicativo Web |
> | Ação | microsoft.web/sites/snapshots/read | Obter instantâneos de aplicativos Web. |
> | Ação | Microsoft.Web/sites/sourcecontrols/Delete | Excluir definições de configuração de controle de origem do aplicativo Web |
> | Ação | Microsoft.Web/sites/sourcecontrols/Read | Obter configurações de controle de origem do aplicativo Web |
> | Ação | Microsoft.Web/sites/sourcecontrols/Write | Atualizar definições de configuração de controle de origem do aplicativo Web |
> | Ação | Microsoft.Web/sites/start/Action | Iniciar um aplicativo Web |
> | Ação | Microsoft.Web/sites/stop/Action | Interromper um aplicativo Web |
> | Ação | microsoft.web/sites/sync/action | Sincronizar pplicativos Web. |
> | Ação | microsoft.web/sites/syncfunctiontriggers/action | Gatilhos de função de sincronização para Aplicativos Web. |
> | Ação | microsoft.web/sites/triggeredwebjobs/delete | Excluir trabalhos da Web disparados para aplicativos Web. |
> | Ação | microsoft.web/sites/triggeredwebjobs/history/read | Obter o histórico de WebJobs disparados de Aplicativos Web. |
> | Ação | microsoft.web/sites/triggeredwebjobs/read | Obter trabalhos da Web disparados de aplicativos Web. |
> | Ação | microsoft.web/sites/triggeredwebjobs/run/action | Executar trabalhos da Web disparados de aplicativos Web. |
> | Ação | microsoft.web/sites/usages/read | Obter usos de aplicativos Web. |
> | Ação | microsoft.web/sites/virtualnetworkconnections/delete | Excluir as conexões de rede virtual de aplicativos Web. |
> | Ação | microsoft.web/sites/virtualnetworkconnections/gateways/read | Obter gateways de conexões de rede virtual de aplicativos Web. |
> | Ação | microsoft.web/sites/virtualnetworkconnections/gateways/write | Atualizar gateways de conexões de rede virtual de aplicativos Web. |
> | Ação | microsoft.web/sites/virtualnetworkconnections/read | Obter as conexões de rede virtual de aplicativos Web. |
> | Ação | microsoft.web/sites/virtualnetworkconnections/write | Atualizar as conexões de rede virtual de aplicativos Web. |
> | Ação | microsoft.web/sites/webjobs/read | Obter trabalhos da Web de aplicativos Web. |
> | Ação | Microsoft.Web/sites/Write | Criar um novo aplicativo Web ou atualizar um existente |
> | Ação | microsoft.web/skus/read | Obter SKUs. |
> | Ação | microsoft.web/sourcecontrols/read | Obter os controles de origem. |
> | Ação | microsoft.web/sourcecontrols/write | Atualizar controles de origem. |
> | Ação | microsoft.web/unregister/action | Cancelar o registro do provedor de recursos Microsoft.Web para a assinatura. |
> | Ação | microsoft.web/validate/action | Validar. |
> | Ação | microsoft.web/verifyhostingenvironmentvnet/action | Verificar VNET no ambiente de hospedagem. |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | DESCRIÇÃO |
> | --- | --- | --- |
> | Ação | Microsoft.WorkloadMonitor/components/read | Obter componentes para o recurso |
> | Ação | Microsoft.WorkloadMonitor/componentsSummary/read | Obter resumo dos componentes |
> | Ação | Microsoft.WorkloadMonitor/monitorInstances/read | Obter instâncias de monitores para o recurso |
> | Ação | Microsoft.WorkloadMonitor/monitorInstancesSummary/read | Obter resumo das instâncias do monitor |
> | Ação | Microsoft.WorkloadMonitor/monitors/read | Obter monitores para o recurso |
> | Ação | Microsoft.WorkloadMonitor/monitors/write | Configurar monitor para o recurso |
> | Ação | Microsoft.WorkloadMonitor/notificationSettings/read | Obter as configurações de notificação para o recurso |
> | Ação | Microsoft.WorkloadMonitor/notificationSettings/write | Configurar configurações de notificação para o recurso |
> | Ação | Microsoft.WorkloadMonitor/operations/read | Obter as operações com suporte |

## <a name="next-steps"></a>Próximas etapas

- [Funções personalizadas](custom-roles.md)
- [Funções internas](built-in-roles.md)
