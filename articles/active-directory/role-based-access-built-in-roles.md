<properties
	pageTitle="RBAC: funções internas | Microsoft Azure"
	description="Este tópico descreve as funções internas para o controle de acesso baseado em função (RBAC)."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="05/20/2016"
	ms.author="kgremban"/>

#RBAC: funções internas

O RBAC (Controle de Acesso baseado em função do Azure) é fornecido com as seguintes funções internas que podem ser atribuídas a usuários, grupos e serviços. Você não pode modificar as definições de funções internas. No entanto, você pode criar [Funções personalizadas no RBAC do Azure](role-based-access-control-custom-roles.md) para atender às necessidades específicas de sua organização.

## Funções no Azure

A tabela a seguir fornece breves descrições das funções internas. Clique no nome da função para ver sua lista detalhada de **ações** e de **não ações**. A propriedade das **ações** especifica as ações permitidas em recursos do Azure. As cadeias de caracteres da ação podem usar caracteres curingas. A propriedade das **não ações** especifica as ações excluídas das ações permitidas.

>[AZURE.NOTE] As definições de função do Azure estão em constante evolução. Este artigo é mantido tão atualizado quanto possível, mas você sempre poderá encontrar as últimas definições de funções no Azure PowerShell. Usar os cmdlets `(get-azurermroledefinition "<role name>").actions` ou `(get-azurermroledefinition "<role name>").notactions` como aplicável.

| Nome da função | Descrição |
| --------- | ----------- |
| [Colaborador de serviço de gerenciamento de API](#api-management-service-contributor) | Pode gerenciar os serviços de gerenciamento de API |
| [Colaborador de componente do Application Insights](#application-insights-component-contributor) | Pode gerenciar os componentes do Application Insights |
| [Operador de automação](#automation-operator) | Capaz de iniciar, parar, suspender e reiniciar trabalhos |
| [Colaborador do BizTalk](#biztalk-contributor) | Pode gerenciar os serviços BizTalk |
| [Colaborador do DB MySQL ClearDB](#cleardb-mysql-db-contributor) | Pode gerenciar bancos de dados MySQL ClearDB |
| [Colaborador](#contributor) | Pode gerenciar tudo, exceto o acesso. |
| [Colaborador da fábrica de dados](#data-factory-contributor) | Pode gerenciar fábricas de dados |
| [Usuário do DevTest Labs](#devtest-labs-user) | Pode exibir tudo e se conectar a máquinas virtuais, iniciá-las, reiniciá-las e desligá-las. |
| [Colaborador de Conta do Banco de Dados de Documentos](#documentdb-account-contributor) | Pode gerenciar contas do Banco de Dados de Documentos |
| [Colaborador de conta do sistemas inteligentes](#intelligent-systems-account-contributor) | Pode gerenciar contas do Intelligent Systems |
| [Colaborador de rede](#network-contributor) | Pode gerenciar todos os recursos de rede |
| [Colaborador de Conta APM do New Relic](#new-relic-apm-account-contributor) | Pode gerenciar contas e aplicativos de Gerenciamento de Desempenho do Aplicativo New Relic |
| [Proprietário](#owner) | Pode gerenciar tudo, incluindo o acesso |
| [Leitor](#reader) | Pode ver tudo, mas não pode fazer alterações |
| [Colaborador do Cache Redis](#redis-cache-contributor]) | Pode gerenciar caches Redis |
| [Colaborador de Coleções de Trabalho do Agendador](#scheduler-job-collections-contributor) | Pode gerenciar coleções de trabalhos do agendador |
| [Colaborador do Serviço de Pesquisa](#search-service-contributor) | Pode gerenciar serviços de pesquisa |
| [Gerenciador de Segurança](#security-manager) | Pode gerenciar componentes de segurança, políticas de segurança e máquinas virtuais |
| [Colaborador do banco de dados SQL](#sql-db-contributor) | Pode gerenciar bancos de dados SQL, mas não suas políticas relacionadas a segurança |
| [Gerenciador de Segurança do SQL](#sql-security-manager) | Pode gerenciar políticas relacionadas à segurança de servidores e banco de dados SQL |
| [Colaborador do SQL Server](#sql-server-contributor) | Pode gerenciar bancos de dados e servidores SQL, mas não suas políticas relacionadas à segurança |
| [Colaborador da conta de armazenamento clássica](#classic-storage-account-contributor) | Pode gerenciar contas de armazenamento clássicas |
| [Colaborador da Conta de Armazenamento](#storage-account-contributor) | Pode gerenciar contas de armazenamento |
| [Administrador de Acesso do Usuário](#user-access-administrator) | Pode gerenciar o acesso do usuário aos recursos do Azure |
| [Colaborador de Máquina Virtual Clássica](#classic-virtual-machine-contributor) | Pode gerenciar máquinas virtuais clássicas, mas não a rede virtual ou a conta de armazenamento à qual estão conectadas |
| [Colaborador de Máquina Virtual](#virtual-machine-contributor) | Pode gerenciar máquinas virtuais, mas não a rede virtual ou a conta de armazenamento à qual estão conectadas |
| [Colaborador de rede clássica](#classic-network-contributor) | Pode gerenciar redes virtuais clássicas e IPs reservados |
| [Colaborador do Plano de Web](#web-plan-contributor) | Pode gerenciar planos da Web |
| [Colaborador do Site](#website-contributor) | Pode gerenciar sites, mas não os planos da Web ao quais eles estão conectados |

## Permissões de função
As tabelas a seguir descrevem as permissões específicas fornecidas a cada função. Isso pode incluir **Actions** que concedem permissões e **NotActions** que as restringem.

### Colaborador de serviço de gerenciamento de API
Pode gerenciar os serviços de gerenciamento de API

| **Ações** | |
| ------- | ------ |
| Microsoft.ApiManagement/Service/* | Criar e gerenciar Serviços de Gerenciamento de API |
| Microsoft.Authorization/*/read | Ler autorização |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ler funções e atribuições de função |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### Colaborador de componente do Application Insights
Pode gerenciar os componentes do Application Insights

| **Ações** | |
| ------- | ------ |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.Insights/components/* | Criar e gerenciar componentes do Insights |
| Microsoft.Insights/webtests/* | Criar e gerenciar testes da Web |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ler grupos de recursos |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### Operador de automação
Capaz de iniciar, parar, suspender e reiniciar trabalhos

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Automation/automationAccounts/jobs/read | Ler trabalhos de conta de automação |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Reiniciar um trabalho de conta de automação |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Interromper um trabalho de conta de automação |
| Microsoft.Automation/automationAccounts/jobs/streams/read | Ler fluxos de trabalho de conta de automação |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspender um trabalho de conta de automação |
| Microsoft.Automation/automationAccounts/jobs/write | Gravar trabalhos de conta de automação |
| Microsoft.Automation/automationAccounts/jobSchedules/read | Ler um plano de trabalho de conta de automação |
| Microsoft.Automation/automationAccounts/jobSchedules/write | Ler um plano de trabalho de conta de automação |
| Microsoft.Automation/automationAccounts/read | Ler contas de automação |
| Microsoft.Automation/automationAccounts/runbooks/read | Ler runbooks de automação |
| Microsoft.Automation/automationAccounts/schedules/read | Ler agendamentos de conta de automação |
| Microsoft.Automation/automationAccounts/schedules/write | Gravar agendamentos de conta de automação |
| Microsoft.Insights/components/* | Criar e gerenciar componentes do Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ler grupos de recursos |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### Colaborador do BizTalk
Pode gerenciar os serviços BizTalk

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.BizTalkServices/BizTalk/* | Criar e gerenciar serviços BizTalk |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ler grupos de recursos |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### Colaborador do DB MySQL ClearDB
Pode gerenciar bancos de dados MySQL ClearDB

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ler grupos de recursos |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
| successbricks.cleardb/Databases/* | Criar e gerenciar bancos de dados MySQL ClearDB |

### Colaborador
Pode gerenciar tudo, exceto o acesso.

| **Ações** ||
| ------- | ------ |
| * | Criar e gerenciar recursos de todos os tipos |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Authorization/*/Write | Não é possível criar funções e atribuições de função |
| Microsoft.Authorization/*/Delete | Não é possível excluir funções e atribuições de função |

### Colaborador da fábrica de dados
Pode gerenciar fábricas de dados

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.DataFactory/dataFactories/* | Gerenciar data factories |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ler grupos de recursos |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### Usuário do DevTest Labs
Pode exibir tudo e se conectar a máquinas virtuais, iniciá-las, reiniciá-las e desligá-las.

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Compute/availabilitySets/read | Ler as propriedades de conjuntos de disponibilidade |
| Microsoft.Compute/virtualMachines/*/read | Ler as propriedades de uma máquina virtual (tamanhos de VM, status de tempo de execução, extensões de VM etc.) |
| Microsoft.Compute/virtualMachines/deallocate/action | Desalocar máquinas virtuais |
| Microsoft.Compute/virtualMachines/read | Ler as propriedades de uma máquina virtual |
| Microsoft.Compute/virtualMachines/restart/action | Reiniciar máquinas virtuais |
| Microsoft.Compute/virtualMachines/start/action | Iniciar máquinas virtuais |
| Microsoft.DevTestLab/*/read | Ler as propriedades de um laboratório |
| Microsoft.DevTestLab/labs/createEnvironment/action | Criar um ambiente de laboratório |
| Microsoft.DevTestLab/labs/formulas/delete | Excluir fórmulas |
| Microsoft.DevTestLab/labs/formulas/read | Ler fórmulas |
| Microsoft.DevTestLab/labs/formulas/write | Adicionar ou modificar fórmulas |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Avaliar políticas de laboratório |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Unir um pool de endereços de back-end de balanceador de carga |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Unir uma regra de NAT de entrada de balanceadores de carga |
| Microsoft.Network/networkInterfaces/*/read | Ler as propriedades de uma interface de rede (por exemplo, todos os balanceadores dos quais a interface de rede faz parte) |
| Microsoft.Network/networkInterfaces/join/action | Unir uma Máquina Virtual a uma interface de rede |
| Microsoft.Network/networkInterfaces/read | Ler interfaces de rede |
| Microsoft.Network/networkInterfaces/write | Gravar interfaces de rede |
| Microsoft.Network/publicIPAddresses/*/read | Ler as propriedades de um endereço IP público |
| Microsoft.Network/publicIPAddresses/join/action | Unir um endereço IP público |
| Microsoft.Network/publicIPAddresses/read | Ler endereços IP públicos de rede |
| Microsoft.Network/virtualNetworks/subnets/join/action | Unir uma rede virtual |
| Microsoft.Resources/deployments/operations/read | Ler operações de implantação |
| Microsoft.Resources/deployments/read | Ler implantações |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ler grupos de recursos |
| Microsoft.Storage/storageAccounts/listKeys/action | Listar chaves da conta de armazenamento |

### Colaborador de Conta do Banco de Dados de Documentos
Pode gerenciar contas do Banco de Dados de Documentos

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.DocumentDb/databaseAccounts/* | Criar e gerenciar contas do Banco de Dados de Documentos |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ler grupos de recursos |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### Colaborador de conta do sistemas inteligentes
Pode gerenciar contas do Intelligent Systems

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.IntelligentSystems/accounts/* | Criar e gerenciar contas do Sistemas Inteligentes |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ler grupos de recursos |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### Colaborador de rede
Pode gerenciar todos os recursos de rede

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.Network/* | Criar e gerenciar redes |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ler grupos de recursos |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### Colaborador de Conta APM do New Relic
Pode gerenciar contas e aplicativos de Gerenciamento de Desempenho do Aplicativo New Relic

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ler grupos de recursos |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
| NewRelic.APM/accounts/* | Criar e gerenciar contas de gerenciamento de desempenho do aplicativo New Relic |

### Proprietário
Pode gerenciar tudo, incluindo o acesso

| **Ações** ||
| ------- | ------ |
| * | Criar e gerenciar recursos de todos os tipos |

### Leitor
Pode ver tudo, mas não pode fazer alterações

| **Ações** ||
| ------- | ------ |
| **/read | Ler recursos de todos os tipos, exceto segredos. |

### Colaborador do Cache Redis
Pode gerenciar caches Redis

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Cache/redis/* | Criar e gerenciar caches Redis |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ler grupos de recursos |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### Colaborador de Coleções de Trabalho do Agendador
Pode gerenciar coleções de trabalhos do Agendador

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ler grupos de recursos | Microsoft.Scheduler/jobcollections/* | Criar e gerenciar coleções de trabalhos |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### Colaborador do Serviço de Pesquisa
Pode gerenciar serviços de Pesquisa

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ler grupos de recursos | Microsoft.Search/searchServices/* | Criar e gerenciar serviços de pesquisa |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### Gerenciador de segurança
Pode gerenciar componentes de segurança, políticas de segurança e máquinas virtuais

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.ClassicCompute/*/read | Ler informações de configuração de máquinas virtuais de computação clássica |
| Microsoft.ClassicCompute/virtualMachines/*/write | Gravar configuração para máquinas virtuais |
| Microsoft.ClassicNetwork/*/read | Ler informações de configuração sobre a rede clássica |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ler grupos de recursos | Microsoft.Security/* | Criar e gerenciar políticas e componentes de segurança |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### Colaborador do banco de dados SQL
Pode gerenciar bancos de dados SQL, mas não a segurança das políticas relacionadas

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Ler funções e atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ler grupos de recursos | Microsoft.Sql/servers/databases/* | Criar e gerenciar bancos de dados SQL |
| Microsoft.Sql/servers/read | Leia os SQL Servers |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Não é possível editar políticas de auditoria |
| Microsoft.Sql/servers/databases/auditingSettings/* | Não é possível editar configurações de auditoria |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Não é possível editar políticas de conexão |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Não é possível editar políticas de mascaramento dos dados |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Não é possível editar políticas de alerta de segurança |
| Microsoft.Sql/servers/databases/securityMetrics/* | Não é possível editar métricas de segurança |

### Gerenciador de Segurança do SQL
Pode gerenciar políticas relacionadas à segurança de servidores e banco de dados SQL

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Ler autorização da Microsoft |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ler grupos de recursos | Microsoft.Sql/servers/auditingPolicies/* | Criar e gerenciar políticas de auditoria do servidor SQL |
| Microsoft.Sql/servers/auditingSettings/* | Criar e gerenciar a configuração de auditoria do servidor SQL |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Criar e gerenciar políticas de auditoria de banco de dados do servidor SQL |
| Microsoft.Sql/servers/databases/auditingSettings/* | Criar e gerenciar configurações de auditoria de banco de dados do servidor SQL |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Criar e gerenciar políticas de conexão de banco de dados do servidor SQL |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Criar e gerenciar políticas de mascaramento de dados do banco de dados do servidor SQL |
| Microsoft.Sql/servers/databases/read | Ler bancos de dados SQL |
| Microsoft.Sql/servers/databases/schemas/read | Ler esquemas de banco de dados do SQL Server |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read | Ler colunas de tabela de banco de dados do servidor SQL |
| Microsoft.Sql/servers/databases/schemas/tables/read | Ler tabelas de banco de dados do servidor SQL |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Criar e gerenciar políticas de alerta de segurança do banco de dados do servidor SQL |
| Microsoft.Sql/servers/databases/securityMetrics/* | Criar e gerenciar métricas de segurança do banco de dados do servidor SQL |
| Microsoft.Sql/servers/read | Leia os SQL Servers |
| Microsoft.Sql/servers/securityAlertPolicies/* | Criar e gerenciar políticas de alerta de segurança de servidor SQL |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### Colaborador do SQL Server
Pode gerenciar servidores e bancos de dados SQL, mas não as políticas relacionadas à segurança

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Ler autorização|
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ler grupos de recursos | Microsoft.Sql/servers/* | Criar e gerenciar servidores SQL |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Sql/servers/auditingPolicies/* | Não é possível editar políticas de auditoria do servidor SQL |
| Microsoft.Sql/servers/auditingSettings/* | Não é possível editar configurações de auditoria do servidor SQL |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Não é possível editar políticas de auditoria de banco de dados do servidor SQL |
| Microsoft.Sql/servers/databases/auditingSettings/* | Não é possível editar configurações de auditoria de banco de dados do servidor SQL |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Não é possível editar políticas de conexão de banco de dados do servidor SQL |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Não é possível editar políticas de mascaramento de banco de dados do servidor SQL |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Não é possível editar políticas de alerta de segurança de banco de dados do servidor SQL |
| Microsoft.Sql/servers/databases/securityMetrics/* | Não é possível editar métricas de segurança de banco de dados do servidor SQL |
| Microsoft.Sql/servers/securityAlertPolicies/* | Não é possível editar políticas de alerta de segurança de servidor SQL |

### Colaborador da Conta de Armazenamento Clássica
Pode gerenciar contas de armazenamento clássicas

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Ler autorização |
| Microsoft.ClassicStorage/storageAccounts/* | Criar e gerenciar contas de armazenamento |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ler grupos de recursos | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### Colaborador da Conta de Armazenamento
Pode gerenciar contas de armazenamento, mas não acessá-las.

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Ler todas as autorizações |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.Insights/diagnosticSettings/* | Gerenciar configurações de diagnóstico |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ler grupos de recursos | Microsoft.Storage/storageAccounts/* | Criar e gerenciar contas de armazenamento |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### Administrador de Acesso do Usuário
Pode gerenciar o acesso do usuário aos recursos do Azure

| **Ações** ||
| ------- | ------ |
| */read | Ler recursos de todos os tipos, exceto segredos. | 
| Microsoft.Authorization/* | Gerenciar autorização |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### Colaborador de Máquina Virtual Clássica
Pode gerenciar máquinas virtuais clássicas, mas não a rede virtual ou a conta de armazenamento à qual estão conectadas

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Ler autorização |
| Microsoft.ClassicCompute/domainNames/* | Criar e gerenciar nomes de domínio de computação clássica |
| Microsoft.ClassicCompute/virtualMachines/* | Criar e gerenciar máquinas virtuais |
| Microsoft.ClassicNetwork/networkSecurityGroups/join/action | Unir grupos de segurança de rede |
| Microsoft.ClassicNetwork/reservedIps/link/action | Vincular IPs reservados |
| Microsoft.ClassicNetwork/reservedIps/read | Ler endereços IP reservados |
| Microsoft.ClassicNetwork/virtualNetworks/join/action | Unir redes virtuais |
| Microsoft.ClassicNetwork/virtualNetworks/read | Ler redes virtuais |
| Microsoft.ClassicStorage/storageAccounts/disks/read | Ler discos de contas de armazenamento |
| Microsoft.ClassicStorage/storageAccounts/images/read | Ler imagens de contas de armazenamento |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Listar chaves da conta de armazenamento |
| Microsoft.ClassicStorage/storageAccounts/read | Ler contas de armazenamento clássico |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ler grupos de recursos | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### Colaborador de Máquina Virtual
Pode gerenciar máquinas virtuais, mas não a rede virtual ou a conta de armazenamento à qual estão conectadas

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Ler autorização |
| Microsoft.Compute/availabilitySets/* | Criar e gerenciar conjuntos de disponibilidade de computação |
| Microsoft.Compute/locations/* | Criar e gerenciar locais de computação |
| Microsoft.Compute/virtualMachines/* | Criar e gerenciar máquinas virtuais |
| Microsoft.Compute/virtualMachineScaleSets/* | Criar e gerenciar conjuntos de escala de máquina virtual |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action | Unir pools de endereços back-end do gateway de aplicativo de rede |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Unir pools de endereços de back-end de balanceador de carga |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action | Unir pools NAT de entrada dos balanceadores de carga |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Unir regras de NAT de entrada do balanceador de carga |
| Microsoft.Network/loadBalancers/read | Ler balanceadores de carga |
| Microsoft.Network/locations/* | Criar e gerenciar locais de rede |
| Microsoft.Network/networkInterfaces/* | Criar e gerenciar as interfaces de rede |
| Microsoft.Network/networkSecurityGroups/join/action | Unir grupos de segurança de rede |
| Microsoft.Network/networkSecurityGroups/read | Ler grupos de segurança de rede |
| Microsoft.Network/publicIPAddresses/join/action | Unir endereços IP públicos de rede |
| Microsoft.Network/publicIPAddresses/read | Ler endereços IP públicos de rede |
| Microsoft.Network/virtualNetworks/read | Ler redes virtuais |
| Microsoft.Network/virtualNetworks/subnets/join/action | Unir sub-redes de rede virtual |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ler grupos de recursos | Microsoft.Storage/storageAccounts/listKeys/action | Listar chaves da conta de armazenamento |
| Microsoft.Storage/storageAccounts/read | Ler contas de armazenamento |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### Colaborador de rede clássica
Pode gerenciar redes virtuais clássicas e IPs reservados

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Ler autorização |
| Microsoft.ClassicNetwork/* | Criar e gerenciar redes clássicas |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ler grupos de recursos | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### Colaborador do Plano de Web
Pode gerenciar planos da Web

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Ler autorização |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ler grupos de recursos | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
| Microsoft.Web/serverFarms/* | Criar e gerenciar farms de servidores |

### Colaborador do Site
Pode gerenciar sites, mas não os planos da Web ao quais eles estão conectados

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Ler autorização |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
| Microsoft.Insights/components/* | Criar e gerenciar componentes do Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ler grupos de recursos | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
| Microsoft.Web/certificates/* | Criar e gerenciar certificados de site da Web |
| Microsoft.Web/listSitesAssignedToHostName/read | Ler sites atribuídos a um nome de host |
| Microsoft.Web/serverFarms/join/action | Unir farms de servidores |
| Microsoft.Web/serverFarms/read | Ler farms de servidores |
| Microsoft.Web/sites/* | Criar e gerenciar sites da Web |

## Consulte também
- [Controle de Acesso Baseado em Função](role-based-access-control-configure.md): introdução ao RBAC no portal do Azure.
- [Funções personalizadas no RBAC do Azure](role-based-access-control-custom-roles.md): aprenda a criar funções personalizadas para atender às suas necessidades de acesso.
- [Criar um relatório de histórico de alterações de acesso](role-based-access-control-access-change-history-report.md): mantenha o controle das alterações de atribuições de função no RBAC.
- [Solução de problemas de Controle de Acesso Baseado em Função](role-based-access-control-troubleshooting.md): obtenha sugestões para corrigir problemas comuns.

<!---HONumber=AcomDC_0601_2016-->