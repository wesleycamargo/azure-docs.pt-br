---
title: Funções internas para RBAC (controle de acesso baseado em função) do Azure | Microsoft Docs
description: Este tópico descreve as funções internas para o RBAC (controle de acesso baseado em função). As funções são adicionadas continuamente, portanto, verifique a atualização da documentação.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 03/06/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: it-pro
ms.openlocfilehash: 4e413d921390cfb87c27c530c6870945c28c0df8
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2018
---
# <a name="built-in-roles-for-azure-role-based-access-control"></a>Funções internas para o controle de acesso baseado em função do Azure
O RBAC (Controle de Acesso baseado em função do Azure) é fornecido com as seguintes funções internas que podem ser atribuídas a usuários, grupos e serviços. Você não pode modificar as definições de funções internas. No entanto, você pode criar [Funções personalizadas no RBAC do Azure](custom-roles.md) para atender às necessidades específicas de sua organização.

## <a name="built-in-role-descriptions"></a>Descrições de função interna
A tabela a seguir fornece breves descrições das funções internas. Clique no nome da função para ver sua lista detalhada de **ações** e de **não ações**. A propriedade das **ações** especifica as ações permitidas em recursos do Azure. As cadeias de caracteres da ação podem usar caracteres curingas. A propriedade das **não ações** especifica as ações excluídas das ações permitidas.

A ação define o tipo das operações que você pode executar em um determinado tipo de recurso. Por exemplo: 
- **Gravar** permite que você execute operações PUT, POST, PATCH e DELETE.
- **Ler** permite que você execute operações GET.

Este artigo aborda apenas as diferentes funções que existem atualmente. Quando atribui uma função a um usuário, no entanto, você pode limitar mais as ações permitidas definindo um escopo. Isso será útil se você quiser tornar alguém um colaborador do site, mas apenas para um grupo de recursos.

> [!NOTE]
> As definições de função do Azure estão em constante evolução. Este artigo é mantido tão atualizado quanto possível, mas você sempre poderá encontrar as últimas definições de funções no Azure PowerShell. Use o cmdlet [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) para listar todas as funções atuais. Você pode se aprofundar em uma função específica usando `(get-azurermroledefinition "<role name>").actions` ou `(get-azurermroledefinition "<role name>").notactions` conforme aplicável. Use [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) para listar operações de provedores de recursos do Azure específicos.


| Função interna | DESCRIÇÃO |
| --- | --- |
| [Proprietário](#owner) | Permite que você gerencie tudo, incluindo acesso aos recursos. |
| [Colaborador](#contributor) | Permite que você gerencie tudo, exceto o acesso aos recursos. |
| [Leitor](#reader) | Permite que você veja tudo, mas não faça alterações. |
| [Colaborador de serviço de gerenciamento de API](#api-management-service-contributor) | Pode gerenciar o serviço e as APIs |
| [Função do operador de serviço de gerenciamento da API](#api-management-service-operator-role) | Pode gerenciar serviços, mas não as APIs |
| [Função do leitor do serviço de gerenciamento da API](#api-management-service-reader-role) | Acesso somente leitura ao serviço e APIs |
| [Colaborador de componente do Application Insights](#application-insights-component-contributor) | Pode gerenciar os componentes do Application Insights |
| [Depurador de Instantâneos do Application Insights](#application-insights-snapshot-debugger) | Fornece ao usuário a permissão para usar os recursos do Depurador de Instantâneos do Application Insights |
| [Operador do Trabalho de Automação](#automation-job-operator) | Criar e gerenciar trabalhos usando runbooks de Automação. |
| [Operador de automação](#automation-operator) | Os Operadores de Automação podem iniciar, interromper, suspender e retomar trabalhos |
| [Operador de runbook de Automação](#automation-runbook-operator) | Ler propriedades do Runbook - para poder criar Trabalhos do runbook. |
| [Proprietário de registro do Microsoft Azure Stack](#azure-stack-registration-owner) | Permite que você gerencie registros do Microsoft Azure Stack. |
| [Colaborador de Backup](#backup-contributor) | Permite que você gerencie o serviço de backup, mas não pode criar cofres e fornecer acesso a outras pessoas |
| [Operador de Backup](#backup-operator) | Permite que você gerencie serviços de backup, exceto a remoção de backup, a criação de cofres e o fornecimento de acesso a outras pessoas |
| [Leitor de Backup](#backup-reader) | Pode exibir serviços de backup, mas não pode fazer alterações |
| [Leitor de cobrança](#billing-reader) | Permite o acesso de leitura aos dados de cobrança |
| [Colaborador do BizTalk](#biztalk-contributor) | Permite gerenciar serviços do BizTalk, mas não acessá-los. |
| [Colaborador de ponto de extremidade de CDN](#cdn-endpoint-contributor) | Pode gerenciar os pontos de extremidade de CDN, mas não pode conceder acesso a outros usuários. |
| [Leitor de ponto de extremidade de CDN](#cdn-endpoint-reader) | Pode exibir os pontos de extremidade de CDN, mas não fazer alterações. |
| [Colaborador de perfil de CDN](#cdn-profile-contributor) | Pode gerenciar os perfis de CDN e os respectivos pontos de extremidade, mas não pode conceder acesso a outros usuários. |
| [Leitor de perfis de CDN](#cdn-profile-reader) | Pode exibir os perfis de CDN e os respectivos pontos de extremidade, mas não fazer alterações. |
| [Colaborador de rede clássica](#classic-network-contributor) | Permite que você gerencie redes clássicas, mas não acessá-las. |
| [Colaborador da conta de armazenamento clássica](#classic-storage-account-contributor) | Permite que você gerencie contas de armazenamento clássico, mas não acessá-las. |
| [Função do Serviço de Operador da Chave da Conta de Armazenamento Clássica](#classic-storage-account-key-operator-service-role) | Os Operadores da Chave da Conta de Armazenamento Clássica têm permissão para listar e regenerar chaves nas Contas de Armazenamento Clássicas |
| [Colaborador de Máquina Virtual Clássica](#classic-virtual-machine-contributor) | Permite gerenciar máquinas virtuais clássicas, mas não o acesso a elas, nem à rede virtual ou conta de armazenamento à qual estão conectadas. |
| [Colaborador do DB MySQL ClearDB](#cleardb-mysql-db-contributor) | Permite que você gerencie bancos de dados MySQL ClearDB, mas não acessá-los. |
| [Função de leitor de conta do Cosmos DB](#cosmos-db-account-reader-role) | Pode ler dados de contas do Azure Cosmos DB. Consulte [Colaborador de conta do DocumentDB](#documentdb-account-contributor) para gerenciar contas do Azure Cosmos DB. |
| [Colaborador da fábrica de dados](#data-factory-contributor) | Cria e gerencia data factories, assim como os recursos filhos neles. |
| [Desenvolvedor do Data Lake Analytics](#data-lake-analytics-developer) | Permite enviar, monitorar e gerenciar seus próprios trabalhos, mas não criar nem excluir contas do Data Lake Analytics. |
| [Usuário do DevTest Labs](#devtest-labs-user) | Permite conectar, iniciar, reiniciar e encerrar as máquinas virtuais no Azure DevTest Labs. |
| [Colaborador de zona DNS](#dns-zone-contributor) | Permite gerenciar zonas DNS e conjuntos de registros no DNS do Azure, mas não permite controlar quem tem acesso a eles. |
| [Colaborador de Conta do DocumentDB](#documentdb-account-contributor) | Pode gerenciar contas do Azure Cosmos DB. O Azure Cosmos DB era anteriormente conhecido como DocumentDB. |
| [Colaborador de conta do sistemas inteligentes](#intelligent-systems-account-contributor) | Permite gerenciar contas do Intelligent Systems, mas não acessá-las. |
| [Colaborador do Key Vault](#key-vault-contributor) | Permite gerenciar cofres de chaves, mas não acessá-los. |
| [Criador de laboratório](#lab-creator) | Permite a você criar, gerenciar e excluir os laboratórios gerenciados nas contas de laboratório do Azure. |
| [Colaborador do Log Analytics](#log-analytics-contributor) | O Colaborador do Log Analytics pode ler todos os dados de monitoramento e editar as configurações de monitoramento. A edição das configurações de monitoramento inclui a adição da extensão da VM às VMs, leitura das chaves da conta de armazenamento para poder configurar a coleção de logs do Armazenamento do Microsoft Azure, criação e configuração de contas de Automação, adição de soluções e configuração do diagnóstico do Azure em todos os recursos do Azure. |
| [Leitor do Log Analytics](#log-analytics-reader) | Um Leitor do Log Analytics pode exibir e pesquisar todos os dados de monitoramento além de exibir as configurações de monitoramento, incluindo a exibição da configuração do diagnóstico do Azure em todos os recursos do Azure. |
| [Colaborador de Aplicativo Lógico](#logic-app-contributor) | Permite gerenciar o aplicativo lógico, mas não o acesso a ele. |
| [Operador de Aplicativo Lógico](#logic-app-operator) | Permite que você leia, habilite e desabilite o aplicativo lógico. |
| [Colaborador de Identidade Gerenciada](#managed-identity-contributor) | Criar, ler, atualizar e excluir a identidade atribuída pelo usuário |
| [Operador de Identidade Gerenciada](#managed-identity-operator) | Ler e atribuir identidade atribuída pelo usuário |
| [Colaborador de monitoramento](#monitoring-contributor) | Pode ler todos os dados de monitoramento e editar configurações de monitoramento. Consulte também [Introdução às funções, permissões e segurança com o Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
| [Leitor de monitoramento](#monitoring-reader) | Pode ler todos os dados de monitoramento (métricas, logs, etc). Consulte também [Introdução às funções, permissões e segurança com o Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
| [Colaborador de rede](#network-contributor) | Permite gerenciar redes, mas não acessá-las. |
| [Colaborador de Conta APM do New Relic](#new-relic-apm-account-contributor) | Permite que você gerencie contas e aplicativos do Gerenciamento de desempenho de aplicativos da New Relic, mas não tem acesso a eles. |
| [Colaborador do Cache Redis](#redis-cache-contributor) | Permite gerenciar caches Redis, mas não acessá-los. |
| [Colaborador de Coleções de Trabalho do Agendador](#scheduler-job-collections-contributor) | Permite gerenciar as coleções de trabalhos do Agendador, mas não acessá-las. |
| [Colaborador do Serviço de Pesquisa](#search-service-contributor) | Permite gerenciar serviços de pesquisa, mas não acessá-las. |
| [Administrador de Segurança](#security-admin) | Na Central de Segurança somente: Pode visualizar as políticas de segurança, estados de segurança, editar políticas de segurança, visualizar alertas e recomendações, ignorar alertas e recomendações |
| [Gerenciador de Segurança](#security-manager) | Permite gerenciar componentes de segurança, políticas de segurança e máquinas virtuais |
| [Leitor de Segurança](#security-reader) | Na Central de Segurança somente: Pode visualizar recomendações e alertas, visualizar políticas de segurança, visualizar estados de segurança, mas não pode fazer alterações |
| [Colaborador do Site Recovery](#site-recovery-contributor) | Permite gerenciar o serviço do Azure Site Recovery, exceto a criação de cofre e atribuição de função |
| [Operador do Site Recovery](#site-recovery-operator) | Permite failover e failback, mas não executa outras operações de gerenciamento do Azure Site Recovery |
| [Leitor do Site Recovery](#site-recovery-reader) | Permite visualizar o status do Azure Site Recovery, mas não executar outras operações de gerenciamento |
| [Colaborador do banco de dados SQL](#sql-db-contributor) | Permite gerenciar Bancos de Dados SQL, mas não acessá-los. Além disso, não é possível gerenciar as políticas relacionadas à segurança ou respectivos servidores SQL pai. |
| [Gerenciador de Segurança do SQL](#sql-security-manager) | Permite você gerenciar as políticas relacionadas à segurança de servidores e bancos de dados SQL, mas não acessá-los. |
| [Colaborador do SQL Server](#sql-server-contributor) | Permite gerenciar servidores e Bancos de Dados SQL, mas não acessá-los, nem as políticas relacionadas à segurança. |
| [Colaborador da Conta de Armazenamento](#storage-account-contributor) | Permite gerenciar contas de armazenamento, mas não acessá-las. |
| [Função do Serviço de Operador da Chave da Conta de Armazenamento](#storage-account-key-operator-service-role) | Os operadores da Chave da Conta de Armazenamento podem listar e regenerar chaves em contas de armazenamento |
| [Colaborador de solicitação de suporte](#support-request-contributor) | Permite criar e gerenciar Solicitações de Suporte |
| [Colaborador do Gerenciador de Tráfego](#traffic-manager-contributor) | Permite gerenciar perfis do Gerenciador de Tráfego, mas não permite controlar quem tem acesso a eles. |
| [Administrador de Acesso do Usuário](#user-access-administrator) | Permite que você gerencie o acesso do usuário aos recursos do Azure. |
| [Logon de administrador da Máquina Virtual](#virtual-machine-administrator-login) | - Usuários com essa função têm a capacidade de fazer logon em uma máquina virtual com privilégios de administrador do Windows ou de usuário raiz do Linux. |
| [Colaborador de Máquina Virtual](#virtual-machine-contributor) | Permite gerenciar máquinas virtuais, mas não o acesso a elas, nem à rede virtual ou conta de armazenamento à qual estão conectadas. |
| [Logon de usuário da Máquina Virtual](#virtual-machine-user-login) | Usuários com essa função têm a capacidade de fazer logon em uma máquina virtual como um usuário comum. |
| [Colaborador do Plano de Web](#web-plan-contributor) | Permite gerenciar os planos da Web para sites, mas não o acesso a eles. |
| [Colaborador do Site](#website-contributor) | Permite gerenciar sites (não planos da Web), mas não acessá-los. |

As tabelas a seguir descrevem as permissões específicas fornecidas a cada função. Isso pode incluir **Ações** que concedem permissões e **Não Ações** que as restringem.

## <a name="owner"></a>Proprietário
Permite que você gerencie tudo, incluindo acesso aos recursos.

| **Ações** |  |
| --- | --- |
| * | Criar e gerenciar recursos de todos os tipos |

## <a name="contributor"></a>Colaborador
Permite que você gerencie tudo, exceto o acesso aos recursos.

| **Ações** |  |
| --- | --- |
| * | Criar e gerenciar recursos de todos os tipos |

| **NotActions** |  |
| --- | --- |
| Microsoft.Authorization/*/Delete | Não é possível excluir funções e atribuições de função |
| Microsoft.Authorization/*/Write | Não é possível criar funções e atribuições de função |
| Microsoft.Authorization/elevateAccess/Action | Concede ao chamador acesso de administrador de acesso do usuário no escopo do locatário |

## <a name="reader"></a>Leitor
Permite que você veja tudo, mas não faça alterações.

| **Ações** |  |
| --- | --- |
| */leitura | Ler recursos de todos os tipos, exceto segredos. |

## <a name="api-management-service-contributor"></a>Colaborador de serviço de gerenciamento de API
Pode gerenciar o serviço e as APIs

| **Ações** |  |
| --- | --- |
| Microsoft.ApiManagement/service/* | Criar e gerenciar o serviço de gerenciamento da API |
| Microsoft.Authorization/*/read | Ler autorização |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="api-management-service-operator-role"></a>Função do operador de serviço de gerenciamento da API
Pode gerenciar serviços, mas não as APIs

| **Ações** |  |
| --- | --- |
| Microsoft.ApiManagement/service/*/read | Ler instâncias do serviço de gerenciamento da API |
| Microsoft.ApiManagement/service/backup/action | Fazer backup do Serviço de Gerenciamento de API para o contêiner especificado em uma conta de armazenamento fornecida pelo usuário |
| Microsoft.ApiManagement/service/delete | Excluir uma instância do Serviço de Gerenciamento de API |
| Microsoft.ApiManagement/service/managedeployments/action | Alterar SKU/unidades, adicionar/remover implantações regionais do Serviço de Gerenciamento de API |
| Microsoft.ApiManagement/service/read | Ler metadados de uma instância do Serviço de Gerenciamento de API |
| Microsoft.ApiManagement/service/restore/action | Restaurar o Serviço de Gerenciamento de API do contêiner especificado em uma conta de armazenamento fornecida pelo usuário |
| Microsoft.ApiManagement/service/updatecertificate/action | Carregar certificado SSL para um Serviço de Gerenciamento de API |
| Microsoft.ApiManagement/service/updatehostname/action | Configurar, atualizar ou remover nomes de domínio personalizado para um Serviço de Gerenciamento de API |
| Microsoft.ApiManagement/service/write | Criar uma nova instância do Serviço de Gerenciamento de API |
| Microsoft.Authorization/*/read | Ler autorização |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

| **NotActions** |  |
| --- | --- |
| Microsoft.ApiManagement/service/users/keys/read | Obter lista de chaves do usuário |

## <a name="api-management-service-reader-role"></a>Função de leitor do Serviço de Gerenciamento de API
Acesso somente leitura ao serviço e APIs

| **Ações** |  |
| --- | --- |
| Microsoft.ApiManagement/service/*/read | Ler instâncias do serviço de gerenciamento da API |
| Microsoft.ApiManagement/service/read | Ler metadados de uma instância do Serviço de Gerenciamento de API |
| Microsoft.Authorization/*/read | Ler autorização |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

| **NotActions** |  |
| --- | --- |
| Microsoft.ApiManagement/service/users/keys/read | Obter lista de chaves do usuário |

## <a name="application-insights-component-contributor"></a>Colaborador de componente do Application Insights
Pode gerenciar os componentes do Application Insights

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.Insights/components/* | Criar e gerenciar componentes do Insights |
| Microsoft.Insights/webtests/* | Criar e gerenciar testes da Web |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="application-insights-snapshot-debugger"></a>Depurador de Instantâneos do Application Insights
Fornece ao usuário a permissão para usar os recursos do Depurador de Instantâneos do Application Insights

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.Insights/components/*/read |  |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="automation-job-operator"></a>Operador do Trabalho de Automação
Criar e gerenciar trabalhos usando runbooks de Automação.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Automation/automationAccounts/jobs/read | Obter um trabalho da Automação do Azure |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Reinicia um trabalho da Automação do Azure |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Interrompe um trabalho da Automação do Azure |
| Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Ler recursos do Hybrid Runbook Worker |
| Microsoft.Automation/automationAccounts/jobs/streams/read | Obter um fluxo de trabalho da Automação do Azure |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspende um trabalho da Automação do Azure |
| Microsoft.Automation/automationAccounts/jobs/write | Criar um trabalho da Automação do Azure |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="automation-operator"></a>Operador de automação
Os Operadores de Automação podem iniciar, interromper, suspender e retomar trabalhos

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Automation/automationAccounts/jobs/read | Obter um trabalho da Automação do Azure |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Reinicia um trabalho da Automação do Azure |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Interrompe um trabalho da Automação do Azure |
| Microsoft.Automation/automationAccounts/jobs/streams/read | Obter um fluxo de trabalho da Automação do Azure |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspende um trabalho da Automação do Azure |
| Microsoft.Automation/automationAccounts/jobs/write | Criar um trabalho da Automação do Azure |
| Microsoft.Automation/automationAccounts/jobSchedules/read | Obter uma agenda de trabalho da Automação do Azure |
| Microsoft.Automation/automationAccounts/jobSchedules/write | Criar uma agenda de trabalho da Automação do Azure |
| Microsoft.Automation/automationAccounts/linkedWorkspace/read | Obtém o espaço de trabalho vinculado à conta de automação |
| Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Ler recursos do Hybrid Runbook Worker |
| Microsoft.Automation/automationAccounts/read | Obter uma conta da Automação do Azure |
| Microsoft.Automation/automationAccounts/runbooks/read | Obter um runbook da Automação do Azure |
| Microsoft.Automation/automationAccounts/schedules/read | Obter um ativo de agendamento da Automação do Azure |
| Microsoft.Automation/automationAccounts/schedules/write | Criar ou atualizar um ativo de agendamento da Automação do Azure |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="automation-runbook-operator"></a>Operador de Runbook de Automação
Ler propriedades do Runbook - para poder criar Trabalhos do runbook.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Automation/automationAccounts/runbooks/read | Obter um runbook da Automação do Azure |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="azure-stack-registration-owner"></a>Proprietário de registro do Azure Stack
Permite que você gerencie registros do Microsoft Azure Stack.

| **Ações** |  |
| --- | --- |
| Microsoft.AzureStack/registrations/products/listDetails/action | Recupera detalhes estendidos de um produto do Marketplace do Azure Stack |
| Microsoft.AzureStack/registrations/products/read | Obtém as propriedades de um produto do Marketplace do Azure Stack |
| Microsoft.AzureStack/registrations/read | Obter as propriedades de um registro do Microsoft Azure Stack |

## <a name="backup-contributor"></a>Colaborador de Backup
Permite que você gerencie o serviço de backup, mas não pode criar cofres e fornecer acesso a outras pessoas

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Gerenciar os resultados da operação no gerenciamento de backup |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Criar e gerenciar contêineres de backup em malhas de backup do cofre de Serviços de Recuperação |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | Criar e gerenciar trabalhos de backup |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportar trabalhos |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | Criar e gerenciar metadados relacionados ao gerenciamento de backup |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Criar e gerenciar os Resultados das operações de gerenciamento de backup |
| Microsoft.RecoveryServices/Vaults/backupPolicies/* | Criar e gerenciar políticas de backup |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Criar e gerenciar itens para backup |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Criar e gerenciar itens submetidos a backup |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Criar e gerenciar contêineres que armazenam itens de backup |
| Microsoft.RecoveryServices/Vaults/certificates/* | Criar e gerenciar certificados relacionados a backup em um cofre de Serviços de Recuperação |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | Criar e gerenciar informações estendidas relacionadas ao cofre |
| Microsoft.RecoveryServices/Vaults/read | A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre' |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | Gerenciar operações de descoberta para buscar contêineres recém-criados |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Criar e gerenciar identidades registradas |
| Microsoft.RecoveryServices/Vaults/usages/* | Criar e gerenciar o uso do cofre dos Serviços de Recuperação |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Retornar resumos de itens protegidos e servidores protegidos para os Serviços de Recuperação. |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Storage/storageAccounts/read | Retornar a lista de contas de armazenamento ou obter as propriedades da conta de armazenamento especificada. |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp é uma operação interna usada pelo serviço |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obter os alertas para o cofre dos Serviços de Recuperação. |
| Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Retornar o resultado da operação do trabalho de exportação. |
| Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="backup-operator"></a>Operador de Backup
Permite que você gerencie serviços de backup, exceto a remoção de backup, a criação de cofres e o fornecimento de acesso a outras pessoas

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Retornar o status da operação |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ operationResults/read | Obter o resultado da operação executada no contêiner de proteção. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/backup/action | Executar um backup para um item protegido. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationResults/read | Obter o resultado da operação executada em itens protegidos. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationsStatus/read | Retornar o status da operação executada em itens protegidos. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/read | Retornar detalhes do objeto do item protegido |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/read | Obter pontos de recuperação para itens protegidos. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/restore/action | Restaurar pontos de recuperação para itens protegidos. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/write | Criar um item protegido de backup |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Retornar todos os contêineres registrados |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | Criar e gerenciar trabalhos de backup |
| Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Cancelar o trabalho |
| Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Retornar o resultado da operação do trabalho. |
| Microsoft.RecoveryServices/Vaults/backupJobs/read | Retornar todos os objetos de trabalho |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportar trabalhos |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Retornar metadados de gerenciamento de backup para o cofre dos Serviços de Recuperação. |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Criar e gerenciar os Resultados das operações de gerenciamento de backup |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Obter os resultados da operação de política. |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | Retornar todas as políticas de proteção |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Criar e gerenciar itens para backup |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Retornar a lista de todos os itens que podem ser protegidos. |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Retornar a lista de todos os itens protegidos. |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Retornar todos os contêineres pertencentes à assinatura |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Retornar resumos de itens protegidos e servidores protegidos para os Serviços de Recuperação. |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
| Microsoft.RecoveryServices/Vaults/extendedInformation/write | A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
| Microsoft.RecoveryServices/Vaults/read | A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre' |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | Gerenciar operações de descoberta para buscar contêineres recém-criados |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A operação Obter resultados da operação pode ser usada para obter o status da operação e o resultado da operação enviada de forma assíncrona |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A operação Obter contêineres pode ser usada para obter os contêineres registrados para um recurso. |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/write | A operação Registrar o contêiner de serviço pode ser usada para registrar um contêiner com o Serviço de Recuperação. |
| Microsoft.RecoveryServices/Vaults/usages/read | Retornar os detalhes de uso para um cofre dos Serviços de Recuperação. |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Storage/storageAccounts/read | Retornar a lista de contas de armazenamento ou obter as propriedades da conta de armazenamento especificada. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Provisionar recuperação de item instantânea para item protegido |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Revogar a recuperação de item instantânea para item protegido |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp é uma operação interna usada pelo serviço |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obter os alertas para o cofre dos Serviços de Recuperação. |
| Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Retornar o resultado da operação do trabalho de exportação. |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationStatus/read |  |
| Microsoft.RecoveryServices/Vaults/certificates/write | A operação Atualizar certificado do recurso atualiza o certificado de credencial de cofre/recurso. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="backup-reader"></a>Leitor de Backup
Pode exibir serviços de backup, mas não pode fazer alterações

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Retornar o status da operação |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ operationResults/read | Obter o resultado da operação executada no contêiner de proteção. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationResults/read | Obter o resultado da operação executada em itens protegidos. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationsStatus/read | Retornar o status da operação executada em itens protegidos. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/read | Retornar detalhes do objeto do item protegido |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Retornar todos os contêineres registrados |
| Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Retornar o resultado da operação do trabalho. |
| Microsoft.RecoveryServices/Vaults/backupJobs/read | Retornar todos os objetos de trabalho |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportar trabalhos |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Retornar metadados de gerenciamento de backup para o cofre dos Serviços de Recuperação. |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Retornar o resultado da operação de backup para o cofre dos Serviços de Recuperação. |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Obter os resultados da operação de política. |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | Retornar todas as políticas de proteção |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Retornar a lista de todos os itens protegidos. |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Retornar todos os contêineres pertencentes à assinatura |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Retornar resumos de itens protegidos e servidores protegidos para os Serviços de Recuperação. |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
| Microsoft.RecoveryServices/Vaults/read | A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre' |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A operação Obter resultados da operação pode ser usada para obter o status da operação e o resultado da operação enviada de forma assíncrona |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A operação Obter contêineres pode ser usada para obter os contêineres registrados para um recurso. |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp é uma operação interna usada pelo serviço |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obter os alertas para o cofre dos Serviços de Recuperação. |
| Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/read |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/read | Obter pontos de recuperação para itens protegidos. |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Retornar o resultado da operação do trabalho de exportação. |
| Microsoft.RecoveryServices/Vaults/usages/read | Retornar os detalhes de uso para um cofre dos Serviços de Recuperação. |

## <a name="billing-reader"></a>Leitor de cobrança
Permite o acesso de leitura aos dados de cobrança

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Billing/*/read | Ler Informações de cobrança |
| Microsoft.Consumption/*/read |  |
| Microsoft.Commerce/*/read |  |
| Microsoft.Management/managementGroups/read | Listar grupos de gerenciamento para o usuário autenticado. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="biztalk-contributor"></a>Colaborador do BizTalk
Permite gerenciar serviços do BizTalk, mas não acessá-los.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.BizTalkServices/BizTalk/* | Criar e gerenciar serviços BizTalk |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="cdn-endpoint-contributor"></a>Colaborador de ponto de extremidade de CDN
Pode gerenciar os pontos de extremidade de CDN, mas não pode conceder acesso a outros usuários.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/endpoints/* |  |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="cdn-endpoint-reader"></a>Leitor de ponto de extremidade de CDN
Pode exibir os pontos de extremidade de CDN, mas não fazer alterações.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/endpoints/*/read |  |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="cdn-profile-contributor"></a>Colaborador de perfil de CDN
Pode gerenciar os perfis de CDN e os respectivos pontos de extremidade, mas não pode conceder acesso a outros usuários.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/* |  |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="cdn-profile-reader"></a>Leitor de perfil de CDN
Pode exibir os perfis de CDN e os respectivos pontos de extremidade, mas não fazer alterações.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/*/read |  |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="classic-network-contributor"></a>Colaborador de rede clássica
Permite que você gerencie redes clássicas, mas não acessá-las.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler autorização |
| Microsoft.ClassicNetwork/* | Criar e gerenciar redes clássicas |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="classic-storage-account-contributor"></a>Colaborador da conta de armazenamento clássica
Permite que você gerencie contas de armazenamento clássico, mas não acessá-las.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler autorização |
| Microsoft.ClassicStorage/storageAccounts/* | Criar e gerenciar contas de armazenamento |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="classic-storage-account-key-operator-service-role"></a>Função do Serviço de Operador da Chave da Conta de Armazenamento Clássica
Os Operadores da Chave da Conta de Armazenamento Clássica têm permissão para listar e regenerar chaves nas Contas de Armazenamento Clássicas

| **Ações** |  |
| --- | --- |
| Microsoft.ClassicStorage/storageAccounts/listkeys/action | Listar as chaves de acesso das contas de armazenamento. |
| Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Regenera as chaves de acesso existentes da conta de armazenamento. |

## <a name="classic-virtual-machine-contributor"></a>Colaborador de Máquina Virtual Clássica
Permite gerenciar máquinas virtuais clássicas, mas não o acesso a elas, nem à rede virtual ou conta de armazenamento à qual estão conectadas.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler autorização |
| Microsoft.ClassicCompute/domainNames/* | Criar e gerenciar nomes de domínio de computação clássica |
| Microsoft.ClassicCompute/virtualMachines/* | Criar e gerenciar máquinas virtuais |
| Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
| Microsoft.ClassicNetwork/reservedIps/link/action | Vincular um IP reservado |
| Microsoft.ClassicNetwork/reservedIps/read | Obter os IPs reservados |
| Microsoft.ClassicNetwork/virtualNetworks/join/action | Ingressar na rede virtual. |
| Microsoft.ClassicNetwork/virtualNetworks/read | Obter a rede virtual. |
| Microsoft.ClassicStorage/storageAccounts/disks/read | Retornar o disco da conta de armazenamento. |
| Microsoft.ClassicStorage/storageAccounts/images/read | Retornar a imagem da conta de armazenamento. |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Listar as chaves de acesso das contas de armazenamento. |
| Microsoft.ClassicStorage/storageAccounts/read | Retornar a conta de armazenamento com a conta fornecida. |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="cleardb-mysql-db-contributor"></a>Colaborador do DB MySQL ClearDB
Permite que você gerencie bancos de dados MySQL ClearDB, mas não acessá-los.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
| successbricks.cleardb/Databases/* | Criar e gerenciar bancos de dados MySQL ClearDB |

## <a name="cosmos-db-account-reader-role"></a>Função de leitor de conta do Cosmos DB
Pode ler dados de contas do Azure Cosmos DB. Consulte [Colaborador de conta do DocumentDB](#documentdb-account-contributor) para gerenciar contas do Azure Cosmos DB.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Funções de leitor e atribuições de função, pode ler permissões concedidas a cada usuário |
| Microsoft.DocumentDB/*/read | Ler qualquer coleção |
| Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Ler as chaves somente leitura da conta do banco de dados. |
| Microsoft.Insights/MetricDefinitions/read | Ler definições de métrica |
| Microsoft.Insights/Metrics/read | Ler métrica |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="data-factory-contributor"></a>Colaborador da fábrica de dados
Cria e gerencia data factories, assim como os recursos filhos neles.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.DataFactory/factory/* | Criar e gerenciar data factories e recursos filho dentro deles. |
| Microsoft.DataFactory/dataFactories/* | Criar e gerenciar data factories e recursos filho dentro deles. |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="data-lake-analytics-developer"></a>Desenvolvedor do Data Lake Analytics
Permite enviar, monitorar e gerenciar seus próprios trabalhos, mas não criar nem excluir contas do Data Lake Analytics.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.BigAnalytics/accounts/* |  |
| Microsoft.DataLakeAnalytics/accounts/* |  |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

| **NotActions** |  |
| --- | --- |
| Microsoft.BigAnalytics/accounts/Delete |  |
| Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
| Microsoft.BigAnalytics/accounts/Write |  |
| Microsoft.DataLakeAnalytics/accounts/Delete | Excluir uma conta DataLakeAnalytics. |
| Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Conceder permissões para cancelar trabalhos enviados por outros usuários. |
| Microsoft.DataLakeAnalytics/accounts/Write | Criar ou atualizar uma conta DataLakeAnalytics. |
| Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Criar ou atualizar uma conta DataLakeStore vinculada de uma conta DataLakeAnalytics. |
| Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Desvincular uma conta DataLakeStore de uma conta DataLakeAnalytics. |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Criar ou atualizar uma conta de armazenamento vinculada de uma conta DataLakeAnalytics. |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | Desvincular uma conta de armazenamento de uma conta DataLakeAnalytics. |
| Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Criar ou atualizar uma regra de firewall. |
| Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Excluir uma regra de firewall. |
| Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Criar ou atualizar uma política de computação. |
| Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Excluir uma política de computação. |

## <a name="devtest-labs-user"></a>Usuário do DevTest Labs
Permite conectar, iniciar, reiniciar e encerrar as máquinas virtuais no Azure DevTest Labs.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Compute/availabilitySets/read | Obter as propriedades de um conjunto de disponibilidade |
| Microsoft.Compute/virtualMachines/*/read | Ler as propriedades de uma máquina virtual (tamanhos de VM, status de tempo de execução, extensões de VM etc.) |
| Microsoft.Compute/virtualMachines/deallocate/action | Desligar a máquina virtual e liberar os recursos de computação |
| Microsoft.Compute/virtualMachines/read | Obter as propriedades de uma máquina virtual |
| Microsoft.Compute/virtualMachines/restart/action | Reinicia a máquina virtual |
| Microsoft.Compute/virtualMachines/start/action | Inicia a máquina virtual |
| Microsoft.DevTestLab/*/read | Ler as propriedades de um laboratório |
| Microsoft.DevTestLab/labs/createEnvironment/action | Criar máquinas virtuais em um laboratório. |
| Microsoft.DevTestLab/labs/claimAnyVm/action | Declarar uma máquina virtual aleatória declarável no laboratório. |
| Microsoft.DevTestLab/labs/formulas/delete | Excluir fórmulas. |
| Microsoft.DevTestLab/labs/formulas/read | Ler fórmulas. |
| Microsoft.DevTestLab/labs/formulas/write | Adicionar ou modificar fórmulas. |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Avaliar a política de laboratório. |
| Microsoft.DevTestLab/labs/virtualMachines/claim/action | Assumir o controle de uma máquina virtual existente |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Ingressar em um pool de endereços de back-end do balanceador de carga |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Adicionar uma regra NAT de entrada do balanceador de carga |
| Microsoft.Network/networkInterfaces/*/read | Ler as propriedades de uma interface de rede (por exemplo, todos os balanceadores de carga dos quais o adaptador de rede faz parte) |
| Microsoft.Network/networkInterfaces/join/action | Adicionar uma Máquina Virtual a um adaptador de rede |
| Microsoft.Network/networkInterfaces/read | Obter uma definição de adaptador de rede.  |
| Microsoft.Network/networkInterfaces/write | Criar uma interface de rede ou atualizar uma interface de rede existente.  |
| Microsoft.Network/publicIPAddresses/*/read | Ler as propriedades de um endereço IP público |
| Microsoft.Network/publicIPAddresses/join/action | Ingressar em um endereço IP público |
| Microsoft.Network/publicIPAddresses/read | Obter uma definição de endereço IP público. |
| Microsoft.Network/virtualNetworks/subnets/join/action | Ingressar em uma rede virtual |
| Microsoft.Resources/deployments/operations/read | Obter ou lista operações de implantação. |
| Microsoft.Resources/deployments/read | Obter ou lista implantações. |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Storage/storageAccounts/listKeys/action | Retornar as chaves de acesso da conta de armazenamento especificada. |

| **NotActions** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/vmSizes/read | Listar os tamanhos disponíveis para os quais a máquina virtual possa ser atualizada |

## <a name="dns-zone-contributor"></a>Colaborador de zona DNS
Permite gerenciar zonas DNS e conjuntos de registros no DNS do Azure, mas não permite controlar quem tem acesso a eles.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.Network/dnsZones/* | Criar e gerenciar zonas e registros DNS |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="documentdb-account-contributor"></a>Colaborador de Conta do DocumentDB
Pode gerenciar contas do Azure Cosmos DB. O Azure Cosmos DB era anteriormente conhecido como DocumentDB.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.DocumentDb/databaseAccounts/* | Criar e gerenciar contas do Azure Cosmos DB |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="intelligent-systems-account-contributor"></a>Colaborador de conta do sistemas inteligentes
Permite gerenciar contas do Intelligent Systems, mas não acessá-las.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.IntelligentSystems/accounts/* | Criar e gerenciar contas do Sistemas Inteligentes |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="key-vault-contributor"></a>Colaborador do Key Vault
Permite gerenciar cofres de chaves, mas não acessá-los.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.KeyVault/* |  |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

| **NotActions** |  |
| --- | --- |
| Microsoft.KeyVault/locations/deletedVaults/purge/action | Limpar um cofre de chaves com exclusão reversível |
| Microsoft.KeyVault/hsmPools/* |  |

## <a name="lab-creator"></a>Criador de laboratório
Permite a você criar, gerenciar e excluir os laboratórios gerenciados nas contas de laboratório do Azure.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.ManagedLab/labAccounts/createLab/action | Criar um laboratório em uma conta de laboratório. |
| Microsoft.ManagedLab/labAccounts/*/read |  |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="log-analytics-contributor"></a>Colaborador do Log Analytics
O Colaborador do Log Analytics pode ler todos os dados de monitoramento e editar as configurações de monitoramento. A edição das configurações de monitoramento inclui a adição da extensão da VM às VMs, leitura das chaves da conta de armazenamento para poder configurar a coleção de logs do Armazenamento do Microsoft Azure, criação e configuração de contas de Automação, adição de soluções e configuração do diagnóstico do Azure em todos os recursos do Azure.

| **Ações** |  |
| --- | --- |
| */leitura | Ler recursos de todos os tipos, exceto segredos. |
| Microsoft.Automation/automationAccounts/* |  |
| Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Listar as chaves de acesso das contas de armazenamento. |
| Microsoft.Compute/virtualMachines/extensions/* |  |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.Insights/diagnosticSettings/* | Criar, atualizar ou ler a configuração de diagnóstico do Analysis Server |
| Microsoft.OperationalInsights/* |  |
| Microsoft.OperationsManagement/* |  |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
| Microsoft.Storage/storageAccounts/listKeys/action | Retornar as chaves de acesso da conta de armazenamento especificada. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="log-analytics-reader"></a>Leitor do Log Analytics
Um Leitor do Log Analytics pode exibir e pesquisar todos os dados de monitoramento além de exibir as configurações de monitoramento, incluindo a exibição da configuração do diagnóstico do Azure em todos os recursos do Azure.

| **Ações** |  |
| --- | --- |
| */leitura | Ler recursos de todos os tipos, exceto segredos. |
| Microsoft.OperationalInsights/workspaces/analytics/query/action | Pesquisar usando o novo mecanismo. |
| Microsoft.OperationalInsights/workspaces/search/action | Executar uma consulta de pesquisa |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

| **NotActions** |  |
| --- | --- |
| Microsoft.OperationalInsights/workspaces/sharedKeys/read | Recupera as chaves compartilhadas do espaço de trabalho. Essas chaves são usadas para conectar agentes do Insights Operacionais da Microsoft ao espaço de trabalho. |

## <a name="logic-app-contributor"></a>Colaborador de aplicativo lógico
Permite gerenciar o aplicativo lógico, mas não o acesso a ele.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Listar as chaves de acesso das contas de armazenamento. |
| Microsoft.ClassicStorage/storageAccounts/read | Retornar a conta de armazenamento com a conta fornecida. |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.Insights/diagnosticSettings/* | Criar, atualizar ou ler a configuração de diagnóstico do Analysis Server |
| Microsoft.Insights/logdefinitions/* | Essa permissão é necessária para usuários que precisam de acesso aos Logs de atividade por meio do portal. Liste as categorias de log no Log de Atividades. |
| Microsoft.Insights/metricDefinitions/* | Ler definições de métricas (lista de tipos de métrica disponíveis para um recurso). |
| Microsoft.Logic/* | Gerenciar recursos de Aplicativos Lógicos. |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/operationresults/read | Obter os resultados da operação da assinatura. |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Storage/storageAccounts/listkeys/action | Retornar as chaves de acesso da conta de armazenamento especificada. |
| Microsoft.Storage/storageAccounts/read | Retornar a lista de contas de armazenamento ou obter as propriedades da conta de armazenamento especificada. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
| Microsoft.Web/connectionGateways/* | Criar e gerenciar um Gateway de Conexão. |
| Microsoft.Web/connections/* | Criar e gerenciar uma Conexão. |
| Microsoft.Web/customApis/* | Criar e gerenciar uma API Personalizada. |
| Microsoft.Web/serverFarms/join/action |  |
| Microsoft.Web/serverFarms/read | Obter as propriedades em um Plano do Serviço de Aplicativo |
| Microsoft.Web/sites/functions/listSecrets/action | Listar segredos de funções de aplicativos Web. |

## <a name="logic-app-operator"></a>Operador de aplicativo lógico
Permite que você leia, habilite e desabilite o aplicativo lógico.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Insights/alertRules/*/read | Ler regras de alerta do Insights |
| Microsoft.Insights/diagnosticSettings/*/read | Obter configurações de diagnóstico para Aplicativos Lógicos |
| Microsoft.Insights/metricDefinitions/*/read | Obter as métricas disponíveis para Aplicativos Lógicos. |
| Microsoft.Logic/*/read | Ler recursos de Aplicativos Lógicos. |
| Microsoft.Logic/workflows/disable/action | Desabilitar o fluxo de trabalho. |
| Microsoft.Logic/workflows/enable/action | Permitir o fluxo de trabalho. |
| Microsoft.Logic/workflows/validate/action | Valida o fluxo de trabalho. |
| Microsoft.Resources/deployments/operations/read | Obter ou lista operações de implantação. |
| Microsoft.Resources/subscriptions/operationresults/read | Obter os resultados da operação da assinatura. |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
| Microsoft.Web/connectionGateways/*/read | Ler gateways de conexão. |
| Microsoft.Web/connections/*/read | Ler conexões. |
| Microsoft.Web/customApis/*/read | Ler API personalizada. |
| Microsoft.Web/serverFarms/read | Obter as propriedades em um Plano do Serviço de Aplicativo |

## <a name="managed-identity-contributor"></a>Colaborador de Identidade Gerenciada
Criar, ler, atualizar e excluir a identidade atribuída pelo usuário

| **Ações** |  |
| --- | --- |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/write |  |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/delete |  |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="managed-identity-operator"></a>Operador de Identidade Gerenciada
Ler e atribuir identidade atribuída pelo usuário

| **Ações** |  |
| --- | --- |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="monitoring-contributor"></a>Colaborador de monitoramento
Pode ler todos os dados de monitoramento e editar configurações de monitoramento. Consulte também [Introdução às funções, permissões e segurança com o Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles).

| **Ações** |  |
| --- | --- |
| */leitura | Ler recursos de todos os tipos, exceto segredos. |
| Microsoft.AlertsManagement/alerts/* |  |
| Microsoft.AlertsManagement/alertsSummary/* |  |
| Microsoft.Insights/AlertRules/* | Leitura/gravação/exclusão de regras de alerta. |
| Microsoft.Insights/components/* | Leia/grave/exclua componentes do Application Insights. |
| Microsoft.Insights/DiagnosticSettings/* | Leitura/gravação/exclusão de configurações de diagnóstico. |
| Microsoft.Insights/eventtypes/* | Listar eventos do Log de atividades (eventos de gerenciamento) em um assinatura. Essa permissão é aplicável ao acesso programático e ao portal para o Log de atividades. |
| Microsoft.Insights/LogDefinitions/* | Essa permissão é necessária para usuários que precisam de acesso aos Logs de atividade por meio do portal. Liste as categorias de log no Log de Atividades. |
| Microsoft.Insights/MetricDefinitions/* | Ler definições de métricas (lista de tipos de métrica disponíveis para um recurso). |
| Microsoft.Insights/Metrics/* | Ler as métricas para um recurso. |
| Microsoft.Insights/Register/Action | Registrar o provedor do Microsoft Insights |
| Microsoft.Insights/webtests/* | Leia/grave/exclua testes da Web do Application Insights. |
| Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Leia/grave/exclua pacotes de solução do Log Analytics. |
| Microsoft.OperationalInsights/workspaces/savedSearches/* | Leia/grave/exclua pesquisas salvas do Log Analytics. |
| Microsoft.OperationalInsights/workspaces/search/action | Executar uma consulta de pesquisa |
| Microsoft.OperationalInsights/workspaces/sharedKeys/action | Recupera as chaves compartilhadas do espaço de trabalho. Essas chaves são usadas para conectar agentes do Insights Operacionais da Microsoft ao espaço de trabalho. |
| Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Leia/grave/exclua configurações de insights de armazenamento do Log Analytics. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
| Microsoft.WorkloadMonitor/workloads/* |  |

## <a name="monitoring-reader"></a>Leitor de monitoramento
Pode ler todos os dados de monitoramento (métricas, logs, etc). Consulte também [Introdução às funções, permissões e segurança com o Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles).

| **Ações** |  |
| --- | --- |
| */leitura | Ler recursos de todos os tipos, exceto segredos. |
| Microsoft.OperationalInsights/workspaces/search/action | Executar uma consulta de pesquisa |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="network-contributor"></a>Colaborador de rede
Permite gerenciar redes, mas não acessá-las.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.Network/* | Criar e gerenciar redes |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="new-relic-apm-account-contributor"></a>Colaborador de Conta APM do New Relic
Permite que você gerencie contas e aplicativos do Gerenciamento de desempenho de aplicativos da New Relic, mas não tem acesso a eles.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
| NewRelic.APM/accounts/* |  |

## <a name="redis-cache-contributor"></a>Colaborador do Cache Redis
Permite gerenciar caches Redis, mas não acessá-los.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Cache/redis/* | Criar e gerenciar caches Redis |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="scheduler-job-collections-contributor"></a>Colaborador de Coleções de Trabalho do Agendador
Permite gerenciar as coleções de trabalhos do Agendador, mas não acessá-las.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Scheduler/jobcollections/* | Criar e gerenciar coleções de trabalhos |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="search-service-contributor"></a>Colaborador do Serviço de Pesquisa
Permite gerenciar serviços de pesquisa, mas não acessá-las.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Search/searchServices/* | Criar e gerenciar serviços de pesquisa |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="security-admin"></a>Administrador de Segurança
Na Central de Segurança somente: Pode visualizar as políticas de segurança, estados de segurança, editar políticas de segurança, visualizar alertas e recomendações, ignorar alertas e recomendações

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Authorization/policyAssignments/* | Criar e gerenciar atribuições de política |
| Microsoft.Authorization/policyDefinitions/* | Criar e gerenciar definições de política |
| Microsoft.Authorization/policySetDefinitions/* | Criar e gerenciar conjuntos de política |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.operationalInsights/workspaces/*/read | Exibir dados do Log Analytics |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Security/*/read | Ler componentes de segurança e políticas |
| Microsoft.Security/locations/alerts/dismiss/action | Ignorar um alerta de segurança |
| Microsoft.Security/locations/tasks/dismiss/action | Ignorar recomendação de segurança |
| Microsoft.Security/policies/write | Atualizar a política de segurança |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="security-manager"></a>Gerenciador de Segurança
Permite gerenciar componentes de segurança, políticas de segurança e máquinas virtuais

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.ClassicCompute/*/read | Ler informações de configuração de máquinas virtuais clássicas |
| Microsoft.ClassicCompute/virtualMachines/*/write | Escrever configurações para máquinas virtuais clássicas |
| Microsoft.ClassicNetwork/*/read | Ler informações de configuração sobre a rede clássica |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Security/* | Criar e gerenciar políticas e componentes de segurança |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="security-reader"></a>Leitor de segurança
Na Central de Segurança somente: Pode visualizar recomendações e alertas, visualizar políticas de segurança, visualizar estados de segurança, mas não pode fazer alterações

| **Ações** |  |
| --- | --- |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.operationalInsights/workspaces/*/read | Exibir dados do Log Analytics |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Security/*/read | Ler componentes de segurança e políticas |

## <a name="site-recovery-contributor"></a>Colaborador do Site Recovery
Permite gerenciar o serviço do Azure Site Recovery, exceto a criação de cofre e atribuição de função

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp é uma operação interna usada pelo serviço |
| Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp é uma operação interna usada pelo serviço |
| Microsoft.RecoveryServices/Vaults/certificates/write | A operação Atualizar certificado do recurso atualiza o certificado de credencial de cofre/recurso. |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | Criar e gerenciar informações estendidas relacionadas ao cofre |
| Microsoft.RecoveryServices/Vaults/read | A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre' |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Criar e gerenciar identidades registradas |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Criar ou atualizar as configurações de alerta de replicação |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Ler eventos |
| Microsoft.RecoveryServices/vaults/replicationFabrics/* | Criar e gerenciar malhas de replicação |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | Criar e gerenciar trabalhos de replicação |
| Microsoft.RecoveryServices/vaults/replicationPolicies/* | Criar e gerenciar políticas de replicação |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Criar e gerenciar planos de recuperação |
| Microsoft.RecoveryServices/Vaults/storageConfig/* | Criar e gerenciar a configuração de armazenamento do cofre de Serviços de Recuperação |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Retornar a informação de token para o cofre dos Serviços de Recuperação. |
| Microsoft.RecoveryServices/Vaults/usages/read | Retornar os detalhes de uso para um cofre dos Serviços de Recuperação. |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | A operação de token do cofre pode ser usada a fim de obter o token do cofre para operações de back-end no nível do cofre. |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Obter os alertas para o cofre dos Serviços de Recuperação |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read |  |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Storage/storageAccounts/read | Retornar a lista de contas de armazenamento ou obter as propriedades da conta de armazenamento especificada. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="site-recovery-operator"></a>Operador do Site Recovery
Permite failover e failback, mas não executa outras operações de gerenciamento do Azure Site Recovery

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp é uma operação interna usada pelo serviço |
| Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp é uma operação interna usada pelo serviço |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
| Microsoft.RecoveryServices/Vaults/read | A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre' |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A operação Obter resultados da operação pode ser usada para obter o status da operação e o resultado da operação enviada de forma assíncrona |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A operação Obter contêineres pode ser usada para obter os contêineres registrados para um recurso. |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Ler configurações de alertas |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Ler eventos |
| Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Verificar consistência da malha |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read | Ler malhas |
| Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Reassociar gateway |
| Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Renovar Certificado para malha do Microsoft Azure |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Ler redes |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/read | Ler mapeamentos de rede |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/read | Ler contêineres de proteção |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/read | Ler itens que podem ser protegidos |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Aplicar ponto de recuperação |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Confirmação de failover |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Failover planejado |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/read | Ler itens protegidos |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Ler pontos de recuperação de replicação |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Reparar replicação |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/reProtect/action | Proteger item protegido novamente |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/testFailover/action | Failover de Teste |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Limpeza do Failover de teste |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Failover |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Atualizar serviço de mobilidade |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/read | Ler mapeamentos de contêiner de proteção |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/read | Ler provedores de serviços de recuperação |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/refreshProvider/action | Atualizar provedor |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/read | Ler classificações de armazenamento |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/read | Ler mapeamentos de classificação de armazenamento |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Ler todos os trabalhos |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | Criar e gerenciar trabalhos de replicação |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read | Ler políticas |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Plano de recuperação de confirmação de failover |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Plano de recuperação de failover planejado |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Ler planos de recuperação |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Proteja plano de recuperação novamente |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Testar plano de recuperação de failover |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Testar plano de recuperação de limpeza do failover |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plano de recuperação de failover |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Obter os alertas para o cofre dos Serviços de Recuperação |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read |  |
| Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Retornar a informação de token para o cofre dos Serviços de Recuperação. |
| Microsoft.RecoveryServices/Vaults/usages/read | Retornar os detalhes de uso para um cofre dos Serviços de Recuperação. |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | A operação de token do cofre pode ser usada a fim de obter o token do cofre para operações de back-end no nível do cofre. |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Storage/storageAccounts/read | Retornar a lista de contas de armazenamento ou obter as propriedades da conta de armazenamento especificada. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="site-recovery-reader"></a>Leitor do Site Recovery
Permite visualizar o status do Azure Site Recovery, mas não executar outras operações de gerenciamento

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp é uma operação interna usada pelo serviço |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obter os alertas para o cofre dos Serviços de Recuperação. |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read |  |
| Microsoft.RecoveryServices/Vaults/read | A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre' |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A operação Obter resultados da operação pode ser usada para obter o status da operação e o resultado da operação enviada de forma assíncrona |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A operação Obter contêineres pode ser usada para obter os contêineres registrados para um recurso. |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Ler configurações de alertas |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Ler eventos |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read | Ler malhas |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Ler redes |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/read | Ler mapeamentos de rede |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/read | Ler contêineres de proteção |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/read | Ler itens que podem ser protegidos |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/read | Ler itens protegidos |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Ler pontos de recuperação de replicação |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/read | Ler mapeamentos de contêiner de proteção |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/read | Ler provedores de serviços de recuperação |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/read | Ler classificações de armazenamento |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/read | Ler mapeamentos de classificação de armazenamento |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Ler todos os trabalhos |
| Microsoft.RecoveryServices/vaults/replicationJobs/read | Ler todos os trabalhos |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read | Ler políticas |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Ler planos de recuperação |
| Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Retornar a informação de token para o cofre dos Serviços de Recuperação. |
| Microsoft.RecoveryServices/Vaults/usages/read | Retornar os detalhes de uso para um cofre dos Serviços de Recuperação. |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | A operação de token do cofre pode ser usada a fim de obter o token do cofre para operações de back-end no nível do cofre. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="sql-db-contributor"></a>Colaborador do banco de dados SQL
Permite gerenciar Bancos de Dados SQL, mas não acessá-los. Além disso, não é possível gerenciar as políticas relacionadas à segurança ou respectivos servidores SQL pai.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Sql/locations/*/read |  |
| Microsoft.Sql/servers/databases/* | Criar e gerenciar bancos de dados SQL |
| Microsoft.Sql/servers/read | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Não é possível editar políticas de auditoria |
| Microsoft.Sql/servers/databases/auditingSettings/* | Não é possível editar configurações de auditoria |
| Microsoft.Sql/servers/databases/auditRecords/read | Recuperar os registros de auditoria do blob do banco de dados |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Não é possível editar políticas de conexão |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Não é possível editar políticas de mascaramento dos dados |
| Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Não é possível editar políticas de alerta de segurança |
| Microsoft.Sql/servers/databases/securityMetrics/* | Não é possível editar métricas de segurança |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |

## <a name="sql-security-manager"></a>Gerenciador de Segurança do SQL
Permite você gerenciar as políticas relacionadas à segurança de servidores e bancos de dados SQL, mas não acessá-los.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler autorização da Microsoft |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Adicionar recursos como conta de armazenamento ou banco de dados SQL a uma sub-rede. |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Sql/servers/auditingPolicies/* | Criar e gerenciar políticas de auditoria do servidor SQL |
| Microsoft.Sql/servers/auditingSettings/* | Criar e gerenciar a configuração de auditoria do servidor SQL |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Criar e gerenciar políticas de auditoria de banco de dados do servidor SQL |
| Microsoft.Sql/servers/databases/auditingSettings/* | Criar e gerenciar configurações de auditoria de banco de dados do servidor SQL |
| Microsoft.Sql/servers/databases/auditRecords/read | Ler registros de auditoria |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Criar e gerenciar políticas de conexão de banco de dados do servidor SQL |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Criar e gerenciar políticas de mascaramento de dados do banco de dados do servidor SQL |
| Microsoft.Sql/servers/databases/read | Retornar a lista de bancos de dados ou obter as propriedades para o banco de dados especificado. |
| Microsoft.Sql/servers/databases/schemas/read | Recuperar lista de esquemas de um banco de dados |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read | Recuperar a lista de colunas de uma tabela |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/read | Recuperar lista de tabelas de um banco de dados |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Criar e gerenciar políticas de alerta de segurança do banco de dados do servidor SQL |
| Microsoft.Sql/servers/databases/securityMetrics/* | Criar e gerenciar métricas de segurança do banco de dados do servidor SQL |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
| Microsoft.Sql/servers/firewallRules/* |  |
| Microsoft.Sql/servers/read | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
| Microsoft.Sql/servers/securityAlertPolicies/* | Criar e gerenciar políticas de alerta de segurança de servidor SQL |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="sql-server-contributor"></a>Colaborador do SQL Server
Permite gerenciar servidores e Bancos de Dados SQL, mas não acessá-los, nem as políticas relacionadas à segurança.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Sql/locations/*/read |  |
| Microsoft.Sql/servers/* | Criar e gerenciar servidores SQL |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/auditingPolicies/* | Não é possível editar políticas de auditoria do servidor SQL |
| Microsoft.Sql/servers/auditingSettings/* | Não é possível editar configurações de auditoria do servidor SQL |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Não é possível editar políticas de auditoria de banco de dados do servidor SQL |
| Microsoft.Sql/servers/databases/auditingSettings/* | Não é possível editar configurações de auditoria de banco de dados do servidor SQL |
| Microsoft.Sql/servers/databases/auditRecords/read | Não é possível ler registros de auditoria |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Não é possível editar políticas de conexão de banco de dados do servidor SQL |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Não é possível editar políticas de mascaramento de banco de dados do servidor SQL |
| Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Não é possível editar políticas de alerta de segurança de banco de dados do servidor SQL |
| Microsoft.Sql/servers/databases/securityMetrics/* | Não é possível editar métricas de segurança de banco de dados do servidor SQL |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
| Microsoft.Sql/servers/extendedAuditingSettings/* |  |
| Microsoft.Sql/servers/securityAlertPolicies/* | Não é possível editar políticas de alerta de segurança de servidor SQL |

## <a name="storage-account-contributor"></a>Colaborador da Conta de Armazenamento
Permite gerenciar contas de armazenamento, mas não acessá-las.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler todas as autorizações |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.Insights/diagnosticSettings/* | Gerenciar configurações de diagnóstico |
| Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Adicionar recursos como conta de armazenamento ou banco de dados SQL a uma sub-rede. |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Storage/storageAccounts/* | Criar e gerenciar contas de armazenamento |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="storage-account-key-operator-service-role"></a>Função do Serviço de Operador da Chave da Conta de Armazenamento
Os operadores da Chave da Conta de Armazenamento podem listar e regenerar chaves em contas de armazenamento

| **Ações** |  |
| --- | --- |
| Microsoft.Storage/storageAccounts/listkeys/action | Retornar as chaves de acesso da conta de armazenamento especificada. |
| Microsoft.Storage/storageAccounts/regeneratekey/action | Regenerar as chaves de acesso da conta de armazenamento especificada. |

## <a name="support-request-contributor"></a>Colaborador de solicitação de suporte
Permite criar e gerenciar Solicitações de Suporte

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler autorização |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="traffic-manager-contributor"></a>Colaborador do Gerenciador de Tráfego
Permite gerenciar perfis do Gerenciador de Tráfego, mas não permite controlar quem tem acesso a eles.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.Network/trafficManagerProfiles/* |  |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="user-access-administrator"></a>Administrador de Acesso do Usuário
Permite que você gerencie o acesso do usuário aos recursos do Azure.

| **Ações** |  |
| --- | --- |
| */leitura | Ler recursos de todos os tipos, exceto segredos. |
| Microsoft.Authorization/* | Gerenciar autorização |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="virtual-machine-administrator-login"></a>Logon de administrador da Máquina Virtual
-   Usuários com essa função têm a capacidade de fazer logon em uma máquina virtual com privilégios de administrador do Windows ou de usuário raiz do Linux.

| **Ações** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/loginAsAdmin/action |  |
| Microsoft.Compute/virtualMachines/login/action |  |
| Microsoft.Compute/virtualMachine/loginAsAdmin/action |  |
| Microsoft.Compute/virtualMachine/logon/action |  |

## <a name="virtual-machine-contributor"></a>Colaborador de Máquina Virtual
Permite gerenciar máquinas virtuais, mas não o acesso a elas, nem à rede virtual ou conta de armazenamento à qual estão conectadas.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler autorização |
| Microsoft.Compute/availabilitySets/* | Criar e gerenciar conjuntos de disponibilidade de computação |
| Microsoft.Compute/locations/* | Criar e gerenciar locais de computação |
| Microsoft.Compute/virtualMachines/* | Criar e gerenciar máquinas virtuais |
| Microsoft.Compute/virtualMachineScaleSets/* | Criar e gerenciar conjuntos de escala de máquina virtual |
| Microsoft.DevTestLab/schedules/* |  |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action | Ingressar em um pool de endereços de back-end do gateway de aplicativo |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Ingressar em um pool de endereços de back-end do balanceador de carga |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action | Ingressar em um pool NAT de entrada do balanceador de carga |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Adicionar uma regra NAT de entrada do balanceador de carga |
| Microsoft.Network/loadBalancers/probes/join/action | Permite usar investigações de um balanceador de carga. Por exemplo, com essa permissão, a propriedade healthProbe do conjunto de dimensionamento de VM pode referenciar a investigação. |
| Microsoft.Network/loadBalancers/read | Obter uma definição de balanceador de carga |
| Microsoft.Network/locations/* | Criar e gerenciar locais de rede |
| Microsoft.Network/networkInterfaces/* | Criar e gerenciar as interfaces de rede |
| Microsoft.Network/networkSecurityGroups/join/action | Ingressar em um grupo de segurança de rede |
| Microsoft.Network/networkSecurityGroups/read | Obter uma definição de um grupo de segurança de rede |
| Microsoft.Network/publicIPAddresses/join/action | Ingressar em um endereço IP público |
| Microsoft.Network/publicIPAddresses/read | Obter uma definição de endereço IP público. |
| Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
| Microsoft.Network/virtualNetworks/subnets/join/action | Ingressar em uma rede virtual |
| Microsoft.RecoveryServices/locations/* |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/*/read |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/read | Retornar detalhes do objeto do item protegido |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/write | Criar um item protegido de backup |
| Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Criar uma Intenção de Proteção de backup |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | Retornar todas as políticas de proteção |
| Microsoft.RecoveryServices/Vaults/backupPolicies/write | Criar uma política de proteção |
| Microsoft.RecoveryServices/Vaults/read | A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre' |
| Microsoft.RecoveryServices/Vaults/usages/read | Retornar os detalhes de uso para um cofre dos Serviços de Recuperação. |
| Microsoft.RecoveryServices/Vaults/write | A operação Criar cofre cria um recurso do Azure do tipo 'cofre' |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Storage/storageAccounts/listKeys/action | Retornar as chaves de acesso da conta de armazenamento especificada. |
| Microsoft.Storage/storageAccounts/read | Retornar a lista de contas de armazenamento ou obter as propriedades da conta de armazenamento especificada. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

## <a name="virtual-machine-user-login"></a>Logon de usuário da Máquina Virtual
Usuários com essa função têm a capacidade de fazer logon em uma máquina virtual como um usuário comum.

| **Ações** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/login/action |  |
| Microsoft.Compute/virtualMachine/logon/action |  |

## <a name="web-plan-contributor"></a>Colaborador do Plano de Web
Permite gerenciar os planos da Web para sites, mas não o acesso a eles.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler autorização |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
| Microsoft.Web/serverFarms/* | Criar e gerenciar farms de servidores |

## <a name="website-contributor"></a>Colaborador do Site
Permite gerenciar sites (não planos da Web), mas não acessá-los.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Ler autorização |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.Insights/components/* | Criar e gerenciar componentes do Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
| Microsoft.Web/certificates/* | Criar e gerenciar certificados de site da Web |
| Microsoft.Web/listSitesAssignedToHostName/read | Obter nomes dos sites atribuídos ao nome de host. |
| Microsoft.Web/serverFarms/join/action |  |
| Microsoft.Web/serverFarms/read | Obter as propriedades em um Plano do Serviço de Aplicativo |
| Microsoft.Web/sites/* | Criar e gerenciar sites (a criação de sites também requer permissões de gravação para o Plano do Serviço de Aplicativo associado) |

## <a name="see-also"></a>Consulte também
* [Controle de Acesso Baseado em Função](role-assignments-portal.md): introdução ao RBAC no portal do Azure.
* [Funções personalizadas no Azure RBAC](custom-roles.md): aprenda a criar funções personalizadas para atender às suas necessidades de acesso.
* [Criar um relatório de histórico de alterações de acesso](change-history-report.md): mantenha o controle das alterações de atribuições de função no RBAC.
* [Solução de problemas de Controle de Acesso Baseado em Função](troubleshooting.md): obtenha sugestões para corrigir problemas comuns.
* [Permissões na Central de Segurança do Azure](../security-center/security-center-permissions.md)
