---
title: "Controle de acesso baseado em função na Automação do Azure | Microsoft Docs"
description: "O RBAC (controle de acesso baseado em função) permite o gerenciamento de acesso aos recursos do Azure. Esse artigo descreve como configurar o RBAC na Automação do Azure."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
keywords: "rbac de automação, controle de acesso baseado em função, rbac azure"
ms.assetid: 04b5625e-0ee8-4b5b-85cd-7734c1b3d4a3
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/05/2018
ms.author: magoedte;sngun
ms.openlocfilehash: 753c06ec4a20650f779b68e11e1d6d6fd27a0141
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="role-based-access-control-in-azure-automation"></a>Controle de acesso com base em função na Automação do Azure

O RBAC (controle de acesso baseado em função) permite o gerenciamento de acesso aos recursos do Azure. Com o [RBAC](../active-directory/role-based-access-control-configure.md), você pode separar as tarefas dentro de sua equipe e conceder somente a quantidade de acesso que os usuários, os grupos e os aplicativos precisam para realizar seus trabalhos. O acesso baseado em função pode ser concedido aos usuários que usam o Portal do Azure, as ferramentas de Linha de Comando do Azure ou as APIs de Gerenciamento do Azure.

## <a name="roles-in-automation-accounts"></a>Funções em Contas de Automação
Na Automação do Azure, o acesso é concedido atribuindo a função apropriada de RBAC aos usuários, grupos e aplicativos no escopo da Conta de Automação. Veja a seguir as funções internas com suporte de uma Conta de Automação:

| **Função** | **Descrição** |
|:--- |:--- |
| Proprietário |A função Proprietário permite acesso a todos os recursos e ações em uma Conta de Automação, incluindo o fornecimento de acesso a outros usuários, grupos e aplicativos para gerenciar a conta de Automação. |
| Colaborador |A função Colaborador permite gerenciar tudo, exceto a modificação de permissões de acesso de outros usuários para uma conta de Automação. |
| Leitor |A função Leitor permite que você veja todos os recursos em uma conta de Automação, mas não permite realizar alterações. |
| Operador de automação |A função Operador de Automação permite realizar tarefas operacionais como iniciar, parar, suspender, continuar e agendar trabalhos. Essa função é útil se você deseja evitar que seus recursos da Conta de Automação, como runbooks e ativos de credenciais, sejam exibidos ou modificados, mas ainda permitir que os membros de sua organização executem esses runbooks. |
|Operador do Trabalho de Automação|A função de Operador de Trabalho de Automação permite criar e gerenciar trabalhos usando runbooks de automação.|
|Operador de Runbook de Automação|A função de Operador de Runbook de Automação permite que você leia as propriedades do runbook. Também é possível criar trabalhos do runbook.|
| Colaborador do Log Analytics | A função Colaborador de Log Analytics permite que você leia todos os dados de monitoramento e edite as configurações de monitoramento. A edição das configurações de monitoramento inclui a adição da extensão da VM às VMs, leitura das chaves da conta de armazenamento para poder configurar a coleção de logs do Armazenamento do Azure, criação e configuração de contas de Automação, adição de soluções e configuração do diagnóstico do Azure em todos os recursos do Azure.|
| Leitor do Log Analytics | A função de Leitor do Log Analytics permite exibir e pesquisar todos os dados de monitoramento, além de exibir configurações de monitoramento. Isso inclui a exibição das configurações de diagnóstico do Azure em todos os recursos do Azure. |
| Colaborador de monitoramento | A função Colaborador de Monitoramento permite que você leia todos os dados de monitoramento e atualize as configurações de monitoramento.|
| Leitor de monitoramento | A função Leitor de Montioring permite que você leia todos os dados de monitoramento. |
| Administrador de Acesso do Usuário |A função Administrador de Acesso do Usuário permite que você gerencie o acesso dos usuários às Contas de Automação do Azure. |

## <a name="role-permissions"></a>Permissões de função

As tabelas a seguir descrevem as permissões específicas fornecidas a cada função. Isso pode incluir Ações, que concedem permissões, e Não Ações, que as restringem.

### <a name="owner"></a>Proprietário

O Proprietário pode gerenciar tudo, incluindo o acesso. A tabela a seguir mostra as permissões concedidas para a função:

|Ações|DESCRIÇÃO|
|---|---|
|Microsoft.Automation/automationAccounts/|Crie e gerencie recursos de todos os tipos.|

### <a name="contributor"></a>Colaborador

Um Colaborador pode gerenciar tudo, exceto o acesso. A tabela a seguir mostra as permissões concedidas e negadas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/|Criar e gerenciar recursos de todos os tipos|
|**Não Ações**||
|Microsoft.Authorization/*/Delete| Exclua funções e atribuições de função.       |
|Microsoft.Authorization/*/Write     |  Crie funções e atribuições de função.       |
|Microsoft.Authorization/elevateAccess/Action    | Nega a capacidade de criar um Administrador de Acesso do Usuário.       |

### <a name="reader"></a>Leitor

Um Leitor pode exibir todos os recursos em uma conta de Automação, mas não pode realizar nenhuma alteração.

|**Ações**  |**Descrição**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/read|Exiba todos os recursos em uma Conta de automação. |

### <a name="automation-job-operator"></a>Operador do Trabalho de Automação

Um Operador de Trabalho de Automação recebe concessão no escopo da conta de Automação. Isso permite da ao operador permissões para gerenciar trabalhos na conta.

|**Ações**  |**Descrição**  |
|---------|---------|
|Microsoft.Authorization/*/read|Autorização de leitura.|
|Microsoft.Automation/automationAccounts/jobs/read|Listar trabalhos do runbook.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Retome um trabalho que está em pausa.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Cancele um trabalho em andamento.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Leia os Fluxos e Saída de Trabalho.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Pause um trabalho em andamento.|
|Microsoft.Automation/automationAccounts/jobs/write|Crie trabalhos.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |  Leia funções e atribuições de função.       |
|Microsoft.Resources/deployments/*      |Crie e gerencie implantações do grupo de recursos.         |
|Microsoft.Insights/alertRules/*      | Crie e gerencie regras de alerta.        |
|Microsoft.Support/* |Crie e gerencie tíquetes de suporte.|

### <a name="automation-runbook-operator"></a>Operador de Runbook de Automação

Uma função Operador de Runbook de Automação é concedida no escopo do Runbook. Uma função Operador de Runbook de Automação pode ver o nome do runbook. Essa permissão combinada com 'Operator de Trabalho de Automação' no escopo da conta de Automação permite que o operador execute as ações de Operador de Automação de um runbook específico. A tabela a seguir mostra as permissões concedidas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/runbooks/read     | Liste os runbooks.        |
|Microsoft.Authorization/*/read      | Autorização de leitura.        |
|Microsoft.Resources/subscriptions/resourceGroups/read      |Leia funções e atribuições de função.         |
|Microsoft.Resources/deployments/*      | Crie e gerencie implantações do grupo de recursos.         |
|Microsoft.Insights/alertRules/*      | Crie e gerencie regras de alerta.        |
|Microsoft.Support/*      | Crie e gerencie tíquetes de suporte.        |

### <a name="automation-operator"></a>Operador de automação

Um Operador de Automação pode iniciar, parar, suspender e continuar trabalhos. A tabela a seguir mostra as permissões concedidas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|Microsoft.Authorization/*/read|Autorização de leitura.|
|Microsoft.Automation/automationAccounts/jobs/read|Listar trabalhos do runbook.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Retome um trabalho que está em pausa.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Cancele um trabalho em andamento.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Leia os Fluxos e Saída de Trabalho.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Pause um trabalho em andamento.|
|Microsoft.Automation/automationAccounts/jobs/write|Crie trabalhos.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |Leia funções e atribuições de função.         |
|Microsoft.Resources/deployments/*      |Crie e gerencie implantações do grupo de recursos.         |
|Microsoft.Insights/alertRules/*      | Crie e gerencie regras de alerta.        |
|Microsoft.Support/* |Crie e gerencie tíquetes de suporte.|

### <a name="log-analytics-contributor"></a>Colaborador do Log Analytics

Uma função Colaborador de Log Analytics pode ler todos os dados de monitoramento e editar as configurações de monitoramento. A edição das configurações de monitoramento inclui a adição da extensão da VM às VMs, leitura das chaves da conta de armazenamento para poder configurar a coleção de logs do Armazenamento do Microsoft Azure, criação e configuração de contas de Automação, adição de soluções e configuração do diagnóstico do Azure em todos os recursos do Azure. A tabela a seguir mostra as permissões concedidas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|*/leitura|Ler recursos de todos os tipos, exceto segredos.|
|Microsoft.Automation/automationAccounts/*|Gerencie as contas de automação.|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|Crie e gerencie extensões de escala da máquina virtual.|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|Liste chaves de conta de armazenamento clássica.|
|Microsoft.Compute/virtualMachines/extensions/*|Crie e gerencie extensões de escala da máquina virtual clássicas.|
|Microsoft.Insights/alertRules/*|Leitura/gravação/exclusão de regras de alerta.|
|Microsoft.Insights/diagnosticSettings/*|Leitura/gravação/exclusão de configurações de diagnóstico.|
|Microsoft.OperationalInsights/*|Gerencie o Log Analytics.|
|Microsoft.OperationsManagement/*|Gerencie soluções em espaços de trabalho.|
|Microsoft.Resources/deployments/*|Crie e gerencie implantações do grupo de recursos.|
|Microsoft.Resources/subscriptions/resourcegroups/deployments/*|Crie e gerencie implantações do grupo de recursos.|
|Microsoft.Storage/storageAccounts/listKeys/action|Liste chaves da conta de armazenamento.|
|Microsoft.Support/*|Crie e gerencie tíquetes de suporte.|


### <a name="log-analytics-reader"></a>Leitor do Log Analytics

Um Leitor do Log Analytics pode exibir e pesquisar todos os dados de monitoramento além de exibir as configurações de monitoramento, incluindo a exibição da configuração do diagnóstico do Azure em todos os recursos do Azure. A tabela a seguir mostra as permissões concedidas ou negadas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|*/leitura|Ler recursos de todos os tipos, exceto segredos.|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|Gerencie consultas no Log Analytics.|
|Microsoft.OperationalInsights/workspaces/search/action|Pesquise dados do Log Analytics.|
|Microsoft.Support/*|Crie e gerencie tíquetes de suporte.|
|**Não Ações**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|Não é possível ler as chaves de acesso compartilhadas.|

### <a name="monitoring-contributor"></a>Colaborador de monitoramento

Uma função Colaborador de Monitoramento pode ler todos os dados de monitoramento e atualizar as configurações de monitoramento. A tabela a seguir mostra as permissões concedidas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|*/leitura|Ler recursos de todos os tipos, exceto segredos.|
|Microsoft.AlertsManagement/alerts/*|Gerencie alertas.|
|Microsoft.AlertsManagement/alertsSummary/*|Gerencie o Painel de alertas.|
|Microsoft.Insights/AlertRules/*|Gerencie as regras de alerta.|
|Microsoft.Insights/components/*|Gerencie os componentes do Application Insights.|
|Microsoft.Insights/DiagnosticSettings/*|Gerencie as configurações de diagnóstico.|
|Microsoft.Insights/eventtypes/*|Listar eventos do Log de atividades (eventos de gerenciamento) em um assinatura. Essa permissão é aplicável ao acesso programático e ao portal para o Log de atividades.|
|Microsoft.Insights/LogDefinitions/*|Essa permissão é necessária para usuários que precisam de acesso aos Logs de atividade por meio do portal. Liste as categorias de log no Log de Atividades.|
|Microsoft.Insights/MetricDefinitions/*|Ler definições de métricas (lista de tipos de métrica disponíveis para um recurso).|
|Microsoft.Insights/Metrics/*|Ler as métricas para um recurso.|
|Microsoft.Insights/Register/Action|Registre o provedor do Microsoft.Insights.|
|Microsoft.Insights/webtests/*|Gerencie os testes da Web do Application Insights.|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|Gerencie os pacotes de soluções do Log Analytics.|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Gerencie pesquisas salvas do Log Analytics.|
|Microsoft.OperationalInsights/workspaces/search/action|Pesquise espaços de trabalho do Log Analytics.|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Listar chaves para um espaço de trabalho do Log Analytics.|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|Gerencie configurações de insights de armazenamento do Log Analytics.|
|Microsoft.Support/*|Crie e gerencie tíquetes de suporte.|
|Microsoft.WorkloadMonitor/workloads/*|Gerencie cargas de trabalho.|

### <a name="monitoring-reader"></a>Leitor de monitoramento

Um Leitor de Monitoramento pode ler todos os dados de monitoramento. A tabela a seguir mostra as permissões concedidas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|*/leitura|Ler recursos de todos os tipos, exceto segredos.|
|Microsoft.OperationalInsights/workspaces/search/action|Pesquise espaços de trabalho do Log Analytics.|
|Microsoft.Support/*|Criar e gerenciar tíquetes de suporte|

### <a name="user-access-administrator"></a>Administrador de Acesso do Usuário

Um Administrador de Acesso do Usuário pode gerenciar o acesso do usuário aos recursos do Azure. A tabela a seguir mostra as permissões concedidas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|*/leitura|Ler todos os recursos|
|Microsoft.Authorization/*|Gerenciar autorização|
|Microsoft.Support/*|Criar e gerenciar tíquetes de suporte|

## <a name="onboarding"></a>Integração

As tabelas a seguir mostram as permissões mínimas necessárias para integração de máquinas virtuais para o controle de alterações ou atualização de soluções de gerenciamento.

### <a name="onboarding-from-a-virtual-machine"></a>Integração de uma máquina virtual

|**Ação**  |**Permissão**  |**Escopo mínimo**  |
|---------|---------|---------|
|Gravar nova implantação      | Microsoft.Resources/deployments/*          |Assinatura          |
|Gravar novo grupo de recursos      | Microsoft.Resources/subscriptions/resourceGroups/write        | Assinatura          |
|Criar novo espaço de trabalho padrão      | Microsoft.OperationalInsights/workspaces/write         | Grupo de recursos         |
|Criar nova conta      |  Microsoft.Automation/automationAccounts/write        |Grupo de recursos         |
|Vincular espaço de trabalho e conta      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft.Automation/automationAccounts/read|Espaço de trabalho</br>Conta de automação
|Criar solução      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write |Grupo de recursos          |
|Criar extensão MMA      | Microsoft.Compute/virtualMachines/write         | Máquina Virtual         |
|Criar pesquisa salva      | Microsoft.OperationalInsights/workspaces/write          | Espaço de trabalho         |
|Criar configuração de escopo      | Microsoft.OperationalInsights/workspaces/write          | Espaço de trabalho         |
|Vincular solução à configuração de escopo      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Solução         |
|Verificação do estado da integração – Ler espaço de trabalho      | Microsoft.OperationalInsights/workspaces/read         | Espaço de trabalho         |
|Verificação do estado da integração – Ler propriedade de conta do espaço de trabalho vinculado     | Microsoft.Automation/automationAccounts/read      | Conta de automação        |
|Verificação do estado da integração – Ler solução      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | Solução         |
|Verificação do estado da integração – Ler VM      | Microsoft.Compute/virtualMachines/read         | Máquina Virtual         |
|Verificação do estado da integração – Ler conta      | Microsoft.Automation/automationAccounts/read  |  Conta de automação   |

### <a name="onboarding-from-automation-account"></a>Integração da conta de automação

|**Ação**  |**Permissão** |**Escopo mínimo**  |
|---------|---------|---------|
|Criar nova implantação     | Microsoft.Resources/deployments/*        | Assinatura         |
|Criar novo grupo de recursos     | Microsoft.Resources/subscriptions/resourceGroups/write         | Assinatura        |
|Folha AutomationOnboarding – Criar novo espaço de trabalho     |Microsoft.OperationalInsights/workspaces/write           | Grupo de recursos        |
|Folha AutomationOnboarding – Ler espaço de trabalho vinculado     | Microsoft.Automation/automationAccounts/read        | Conta de automação       |
|Folha AutomationOnboarding – Ler solução     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | Solução        |
|Folha AutomationOnboarding – Ler espaço de trabalho     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | Espaço de trabalho        |
|Criar link para o espaço de trabalho e conta     | Microsoft.OperationalInsights/workspaces/write        | Espaço de trabalho        |
|Gravar conta para caixa de sapatos      | Microsoft.Automation/automationAccounts/write        | Conta        |
|Criar solução      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write        | Grupo de recursos         |
|Criar/editar pesquisa salva     | Microsoft.OperationalInsights/workspaces/write        | Espaço de trabalho        |
|Criar/editar configuração de escopo     | Microsoft.OperationalInsights/workspaces/write        | Espaço de trabalho        |
|Vincular solução à configuração de escopo      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Solução         |
|**Etapa 2 – Integrar várias VMs**     |         |         |
|Folha de VMOnboarding – Criar extensão MMA     | Microsoft.Compute/virtualMachines/write           | Máquina Virtual        |
|Criar/editar pesquisa salva     | Microsoft.OperationalInsights/workspaces/write           | Espaço de trabalho        |
|Criar/editar configuração de escopo  | Microsoft.OperationalInsights/workspaces/write   | Espaço de trabalho|

## <a name="update-management"></a>Gerenciamento de atualizações

Gerenciamento de atualizações atinge vários serviços para fornecer seu serviço. A tabela a seguir mostra as permissões necessárias para gerenciar implantações de atualização de gerenciamento:

|**Recurso**  |**Função**  |**Escopo**  |
|---------|---------|---------|
|Conta de automação     | Colaborador do Log Analytics       | Conta de automação        |
|Conta de automação    | Colaborador de Máquina Virtual        | Grupo de recursos para a conta        |
|Espaço de trabalho do Log Analytics     | Colaborador do Log Analytics| Espaço de trabalho do Log Analytics        |
|Espaço de trabalho do Log Analytics |Leitor do Log Analytics| Assinatura|
|Solução     |Colaborador do Log Analytics         | Solução|
|Máquina Virtual     | Colaborador de Máquina Virtual        | Máquina Virtual        |

## <a name="configure-rbac-for-your-automation-account-using-azure-portal"></a>Configurar o RBAC para sua Conta de automação usando o Portal do Azure
1. Faça logon no [Portal do Azure](https://portal.azure.com/) e abra sua conta de Automação na página Contas de Automação.  
2. Clique no controle **Controle de acesso (IAM)** no canto superior direito. Isso abre a página **Controle de acesso (IAM)** onde você pode adicionar novos usuários, grupos e aplicativos para gerenciar sua Conta de Automação e exibir as funções existentes que podem ser configuradas para ela.
   
   ![Botão de acesso](media/automation-role-based-access-control/automation-01-access-button.png)  

### <a name="add-a-new-user-and-assign-a-role"></a>Adicionar um novo usuário e atribuir uma função
1. Na página **Controle de acesso (IAM)**, clique em **+ Adicionar** para abrir a página **Adicionar permissões**, na qual você pode adicionar um usuário, grupo ou aplicativo e atribuir uma função a eles.  

2. Selecione uma função na lista de funções disponíveis. É possível escolher qualquer uma das funções internas disponíveis compatíveis com uma Conta de Automação ou qualquer função personalizada que você tenha definido.

3. Digite o nome do usuário para quem você deseja conceder permissões no campo **Selecionar**. Selecione o usuário na lista e clique em **Salvar**.
   
   ![Adicionar usuários](media/automation-role-based-access-control/automation-04-add-users.png)  
   
   Agora você já deve ver o usuário adicionado à página **Usuários** com a função selecionada.  
   
   ![Listar usuários](media/automation-role-based-access-control/automation-05-list-users.png)  
   
   Você também pode atribuir uma função para o usuário na página **Funções** . 
4. Clique em **Funções** na página **Controle de acesso (IAM)** para abrir a página **Funções**. Nessa página, você pode exibir o nome da função além do número de usuários e grupos atribuídos a essa função.
   
    ![Atribuir função na página de usuários](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)  
   
   > [!NOTE]
   > O controle de acesso baseado em função só pode ser definido no escopo da Conta de Automação e não em qualquer recurso abaixo dela.

### <a name="remove-a-user"></a>Remover um usuário
Você pode remover a permissão de acesso de um usuário que não está gerenciando a Conta de Automação ou que não trabalha mais para a organização. Veja abaixo as etapas para remover um usuário: 

1. Na página **Controle de acesso (IAM)**, selecione o usuário que você deseja remover e clique em **Remover**.
2. Clique no botão **Remover** na página de detalhes da atribuição.
3. Clique em **Sim** para confirmar a remoção.

   ![Remover usuários](media/automation-role-based-access-control/automation-08-remove-users.png)

## <a name="role-assigned-user"></a>Usuário atribuído à função

Quando um usuário atribuído a uma função entra no Azure e seleciona sua Conta de automação, ele pode ver a conta do proprietário exibida na lista de **Diretórios**. Para exibir a Conta de Automação à qual ele foi adicionado, é preciso alternar o diretório padrão para o diretório padrão do proprietário.

### <a name="user-experience-for-automation-operator-role"></a>Experiência do usuário para a função de Operador de Automação
Quando um usuário, atribuído à função de Operador de Automação, exibe a Conta de Automação atribuída, ele só consegue exibir a lista de runbooks, trabalhos de runbook e agendamentos criados na Conta de Automação, mas não exibe suas definições. Ele pode iniciar, parar, suspender, continuar ou agendar o trabalho de runbook. O usuário não tem acesso a outros recursos da Automação, como configurações, grupos de trabalho híbridos ou nós DSC.

![Sem acesso aos recursos](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)  

O usuário tem acesso para exibir e criar agendamentos, mas não tem acesso a qualquer outro tipo de ativo.

Esse usuário não tem acesso para exibir os Webhooks associados a um runbook

![Nenhum acesso ao Webhooks](media/automation-role-based-access-control/automation-13-no-access-to-webhooks.png)  

## <a name="configure-rbac-for-your-automation-account-using-azure-powershell"></a>Configurar o RBAC para sua Conta de Automação usando o Azure PowerShell
O acesso baseado em função também pode ser configurado para uma Conta de Automação usando os seguintes [cmdlets do Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md):

• [Get-AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx) lista todas as funções RBAC que estão disponíveis no Azure Active Directory. Você pode usar esse comando juntamente com a propriedade **Nome** para listar todas as ações que podem ser executadas por uma função específica.

```powershell-interactive
Get-AzureRmRoleDefinition -Name 'Automation Operator'
```

A seguir está a saída de exemplo:

```powershell
Name             : Automation Operator
Id               : d3881f73-407a-4167-8283-e981cbba0404
IsCustom         : False
Description      : Automation Operators are able to start, stop, suspend, and resume jobs
Actions          : {Microsoft.Authorization/*/read, Microsoft.Automation/automationAccounts/jobs/read, Microsoft.Automation/automationAccounts/jobs/resume/action, 
                   Microsoft.Automation/automationAccounts/jobs/stop/action...}
NotActions       : {}
AssignableScopes : {/}
``` 

• [Get-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx) lista as atribuições de função do RBAC do Azure AD no escopo especificado. Sem parâmetros, esse comando retorna todas as atribuições de função realizadas na assinatura. Use o parâmetro **ExpandPrincipalGroups** para listar as atribuições de acesso para o usuário especificado, bem como para os grupos dos quais o usuário é membro.  
    **Exemplo:** use o comando a seguir para listar todos os usuários e suas funções em uma conta de automação.

```powershell-interactive
Get-AzureRMRoleAssignment -scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

A seguir está a saída de exemplo:

```powershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/cc594d39-ac10-46c4-9505-f182a355c41f
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : 15f26a47-812d-489a-8197-3d4853558347
ObjectType         : User
```

• [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603580.aspx) para atribuir acesso de usuários, grupos e aplicativos a um determinado escopo.  
    **Exemplo:** use o comando a seguir para atribuir a função "Operador de Automação" para um usuário no escopo da Conta de Automação.

```powershell-interactive
New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

A seguir está a saída de exemplo:

```powershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/25377770-561e-4496-8b4f-7cba1d6fa346
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : f5ecbe87-1181-43d2-88d5-a8f5e9d8014e
ObjectType         : User
```

• Use [Remove-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603781.aspx) para remover o acesso de um usuário, grupo ou aplicativo especificado de um determinado escopo.  
    **Exemplo:** use o comando a seguir para remover o usuário da função "Operador de Automação" do escopo da Conta de Automação.

```powershell-interactive
Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Nos exemplos anteriores, substitua a **ID de entrada**, a **ID da assinatura**, o **nome do grupo de recursos** e o **nome da conta de Automação** pelos detalhes de sua conta. Escolha **sim** quando solicitado a confirmar antes de continuar a remover a atribuição de função de usuário.   

## <a name="next-steps"></a>Próximas etapas
* Para obter informações sobre as diversas maneiras de configurar o RBAC para a Automação do Azure, consulte [gerenciar o RBAC com o Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md).
* Para obter detalhes sobre diferentes maneiras de iniciar um runbook, confira [Iniciando um runbook](automation-starting-a-runbook.md)
* Para obter informações sobre os diferentes tipos de runbook, consulte [Tipos de runbook da Automação do Azure](automation-runbook-types.md)

