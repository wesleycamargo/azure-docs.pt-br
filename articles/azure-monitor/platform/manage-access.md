---
title: Gerenciar workspaces no Azure Log Analytics e no portal do OMS | Microsoft Docs
description: Gerencie workspaces no Azure Log Analytics e no portal do OMS usando várias tarefas administrativas em usuários, contas, workspaces e contas do Azure.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: 32a31a87bacbb13cd3b2cb4561ac04e54d51ba46
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55656746"
---
# <a name="manage-workspaces"></a>Gerenciar workspaces

Para gerenciar o acesso ao Log Analytics, você executa várias tarefas administrativas relacionadas aos workspaces. Este artigo fornece conselhos e procedimentos para gerenciar workspaces. Um workspace é essencialmente um contêiner que inclui informações da conta e informações de configuração simples para a conta. Você ou outros membros de sua organização podem usar vários workspaces para gerenciar diferentes conjuntos de dados que são coletados de todos ou de partes da sua infraestrutura de TI.

Para criar um workspace, você precisa:

1. Tenho uma assinatura do Azure.
2. Escolher um nome para o workspace.
3. Associe o workspace com uma das suas assinaturas e grupos de recursos.
4. Escolher uma localização geográfica.

## <a name="determine-the-number-of-workspaces-you-need"></a>Determinar o número de workspaces que você precisa
Um workspace é um recurso do Azure e é um contêiner no qual os dados são coletados, agregados, analisados e apresentados no portal do Azure.

Você pode ter vários workspaces por assinatura do Azure e ter acesso a mais de um workspace, com a capacidade de realizar uma consulta facilmente. Esta seção descreve quando pode ser útil criar mais de um workspace.

Hoje, um workspace fornece:

* Uma localização geográfica para o armazenamento dos dados
* Isolamento de dados para definir direitos de acesso de usuário diferente
* Escopo para a definição de configurações como [tipo de preço](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier), [retenção](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period) e [limitação de dados](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap) 

Do ponto de vista do consumo, recomendamos que você crie o menor número de workspaces possível. Isso torna a administração e a consulta mais fácil e rápida. Porém, com base nas características anteriores, pode ser útil criar vários workspaces se:

* Você é uma empresa global e precisa de dados armazenados em regiões específicas por motivos de soberania de dados ou conformidade.
* Você usa o Azure e deseja evitar encargos de transferência de dados de saída com um workspace na mesma região que os recursos do Azure que ele gerencia.
* Você deseja alocar cobranças a diferentes departamentos ou grupos de negócios com base em seu uso, criando um workspace para cada departamento ou grupo de negócios em sua própria assinatura do Azure.
* Você é um provedor de serviço gerenciado e precisa para manter os dados do Log Analytics para cada cliente que você gerencia isolados dos de outros clientes.
* Você gerencia vários clientes e deseja que cada cliente / departamento / grupo de negócios veja seus próprios dados, mas não os dados de outras pessoas.

Ao usar agentes do Windows para coletar dados, você pode [configurar cada agente para relatar para um ou mais workspaces](../../azure-monitor/platform/agent-windows.md).

Se você estiver usando o System Center Operations Manager, cada grupo de gerenciamento do Operations Manager poderá ser conectado a apenas um workspace. Você pode instalar o Microsoft Monitoring Agent em computadores gerenciados pelo Operations Manager e fazer o agente relatar ao Operations Manager e a um workspace do Log Analytics diferente.

## <a name="workspace-information"></a>Informações do workspace

Você pode exibir detalhes sobre o workspace no portal do Azure. 

1. Se ainda não tiver feito isso, entre no [portal do Azure](https://portal.azure.com).

2. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Log Analytics**.  

    ![Portal do Azure](media/manage-access/azure-portal-01.png)  

3. No painel de inscrições do Log Analytics, selecione um workspace.

4. A página do workspace exibe detalhes sobre os primeiros passos, configurações e links para informações adicionais.  

    ![Detalhes do workspace](./media/manage-access/workspace-overview-page.png)  

## <a name="manage-accounts-and-users"></a>Gerenciar contas e usuários
Cada workspace pode ter várias contas associadas e cada conta pode ter acesso a vários workspaces. O acesso é gerenciado via [acesso baseado em função do Azure](../../role-based-access-control/role-assignments-portal.md). Esses direitos de acesso se aplicam no portal do Azure e sobre o acesso de API.


As atividades a seguir também exigem permissões do Azure:

| Ação                                                          | Permissões do Azure necessárias | Observações |
|-----------------------------------------------------------------|--------------------------|-------|
| Adicionar e remover soluções de gerenciamento                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Essas permissões precisam ser concedidas no nível de assinatura ou no grupo de recursos. |
| Alterar o tipo de preço                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Exibir dados nos blocos de solução *Backup* e *Site Recovery* | Administrador/coadministrador | Acessa recursos implantados usando o modelo de implantação clássico |
| Criar um workspace no portal do Azure                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


### <a name="managing-access-to-log-analytics-using-azure-permissions"></a>Gerenciar o acesso ao Log Analytics usando permissões do Azure
Para conceder acesso ao workspace do Log Analytics usando permissões do Azure, execute as etapas em [Usar atribuições de função para gerenciar o acesso aos recursos de sua assinatura do Azure](../../role-based-access-control/role-assignments-portal.md).

O Azure tem duas funções de usuário predefinidas para o Log Analytics:
- Leitor do Log Analytics
- Colaborador do Log Analytics

Os membros da função *Leitor do Log Analytics* podem:
- Exibir e pesquisar todos os dados de monitoramento 
- Exiba configurações de monitoramento, incluindo exibir a configuração do diagnóstico do Azure em todos os recursos do Azure.

A função de leitor do Log Analytics inclui as seguintes ações do Azure:

| Type    | Permissão | DESCRIÇÃO |
| ------- | ---------- | ----------- |
| Ação | `*/read`   | Capacidade de exibir todos os recursos do Azure e a configuração do recurso. Inclui exibir: <br> Status de extensão da máquina virtual <br> Configuração do diagnóstico do Azure nos recursos <br> Todas as propriedades e configurações de todos os recursos |
| Ação | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Capacidade de execução de consultas de Pesquisa de Logs v2 |
| Ação | `Microsoft.OperationalInsights/workspaces/search/action` | Capacidade de execução de consultas de Pesquisa de Logs v1 |
| Ação | `Microsoft.Support/*` | Capacidade de abrir casos de suporte |
|Nenhuma Ação | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Impede a leitura da chave do workspace necessária para usar a API da coleta de dados e instalar os agentes. Isso impede que o usuário adicione os novos recursos para o workspace |


Os membros da função *Colaborador do Log Analytics* podem:
- Ler todos os dados de monitoramento, como o leitor do Log Analytics pode  
- Criar e configurar Contas de automação  
- Adicionar e remover soluções de gerenciamento    
    > [!NOTE] 
    > Para executar com êxito essas duas ações, essa permissão deve ser concedida no nível do grupo de recursos ou da assinatura.  

- Ler as chaves da conta de armazenamento   
- Configurar a coleta de logs no Armazenamento do Azure  
- Editar configurações de monitoramento dos recursos do Azure, incluindo
  - Adicionar a extensão VM às VMs
  - Configurar o diagnóstico do Azure em todos os recursos do Azure

> [!NOTE] 
> Você pode usar a capacidade de adicionar uma extensão da máquina virtual a uma máquina virtual para ter controle total sobre uma máquina virtual.

A função de Colaborador do Log Analytics inclui as seguintes ações do Azure:

| Permissão | DESCRIÇÃO |
| ---------- | ----------- |
| `*/read`     | Capacidade de exibir todos os recursos e a configuração do recurso. Inclui exibir: <br> Status de extensão da máquina virtual <br> Configuração do diagnóstico do Azure nos recursos <br> Todas as propriedades e configurações de todos os recursos |
| `Microsoft.Automation/automationAccounts/*` | Capacidade de criar e configurar Contas de automação do Azure, incluindo adicionar e editar runbooks |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Adicionar, atualizar e remover as extensões da máquina virtual, incluindo a extensão do Microsoft Monitoring Agent e o Agente do OMS para a extensão do Linux |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Exiba a chave da conta de armazenamento. Necessário para configurar o Log Analytics para ler os logs das contas de armazenamento do Azure |
| `Microsoft.Insights/alertRules/*` | Adicionar, atualizar e remover as regras de alerta |
| `Microsoft.Insights/diagnosticSettings/*` | Adicionar, atualizar e remover as configurações de diagnóstico nos recursos do Azure |
| `Microsoft.OperationalInsights/*` | Adicionar, atualizar e remover a configuração dos workspaces do Log Analytics |
| `Microsoft.OperationsManagement/*` | Adicionar e remover soluções de gerenciamento |
| `Microsoft.Resources/deployments/*` | Crie e exclua implantações. Necessário para adicionar e remover soluções, workspace e contas de automação |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Crie e exclua implantações. Necessário para adicionar e remover soluções, workspace e contas de automação |

Para adicionar e remover usuários de uma função de usuário, é necessário ter as permissões `Microsoft.Authorization/*/Delete` e `Microsoft.Authorization/*/Write`.

Use essas funções para conceder acesso aos usuários em escopos diferentes:
- Assinatura – acesso a todos os workspaces na assinatura
- Grupo de Recursos - acesso a todo workspace no grupo de recursos
- Recurso - acesso somente ao workspace especificado

Recomendamos que você execute atribuições no nível do recurso (workspace) para garantir o controle de acesso preciso.  Use as [funções personalizadas](../../role-based-access-control/custom-roles.md) para criar funções com as permissões específicas necessárias.

## <a name="next-steps"></a>Próximas etapas
* Consulte a [visão geral do agente do Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) para reunir dados de computadores no datacenter ou outro ambiente de nuvem.
* Consulte [Coletar dados sobre as máquinas virtuais do Azure](../../azure-monitor/learn/quick-collect-azurevm.md) para configurar a coleta de dados de máquinas virtuais do Azure.  
* [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](../../azure-monitor/insights/solutions.md) para adicionar funcionalidades e reunir dados.

