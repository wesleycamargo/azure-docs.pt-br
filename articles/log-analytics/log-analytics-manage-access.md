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
ms.devlang: na
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: d291a46eaac0f5e4023d343f700448d029765098
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52634873"
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
* Escopo para a configuração de configurações, como a limitação de dados e retenção

Do ponto de vista do consumo, recomendamos que você crie o menor número de workspaces possível. Isso torna a administração e a consulta mais fácil e rápida. Porém, com base nas características anteriores, pode ser útil criar vários workspaces se:

* Você é uma empresa global e precisa de dados armazenados em regiões específicas por motivos de soberania de dados ou conformidade.
* Você usa o Azure e deseja evitar encargos de transferência de dados de saída com um workspace na mesma região que os recursos do Azure que ele gerencia.
* Você deseja alocar cobranças a diferentes departamentos ou grupos de negócios com base em seu uso, criando um workspace para cada departamento ou grupo de negócios em sua própria assinatura do Azure.
* Você é um provedor de serviço gerenciado e precisa para manter os dados do Log Analytics para cada cliente que você gerencia isolados dos de outros clientes.
* Você gerencia vários clientes e deseja que cada cliente / departamento / grupo de negócios veja seus próprios dados, mas não os dados de outras pessoas.

Ao usar agentes do Windows para coletar dados, você pode [configurar cada agente para relatar para um ou mais workspaces](../azure-monitor/platform/agent-windows.md).

Se você estiver usando o System Center Operations Manager, cada grupo de gerenciamento do Operations Manager poderá ser conectado a apenas um workspace. Você pode instalar o Microsoft Monitoring Agent em computadores gerenciados pelo Operations Manager e fazer o agente relatar ao Operations Manager e a um workspace do Log Analytics diferente.

## <a name="workspace-information"></a>Informações do workspace

Você pode exibir detalhes sobre o workspace no portal do Azure. 

1. Se ainda não tiver feito isso, entre no [portal do Azure](https://portal.azure.com).

2. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Log Analytics**.  

    ![Portal do Azure](media/log-analytics-manage-access/azure-portal-01.png)  

3. No painel de inscrições do Log Analytics, selecione um workspace.

4. A página do workspace exibe detalhes sobre os primeiros passos, configurações e links para informações adicionais.  

    ![Detalhes do workspace](./media/log-analytics-manage-access/workspace-overview-page.png)  

## <a name="manage-accounts-and-users"></a>Gerenciar contas e usuários
Cada workspace pode ter várias contas associadas e cada conta pode ter acesso a vários workspaces. O acesso é gerenciado via [acesso baseado em função do Azure](../role-based-access-control/role-assignments-portal.md). Esses direitos de acesso se aplicam no portal do Azure e sobre o acesso de API.


As atividades a seguir também exigem permissões do Azure:

| Ação                                                          | Permissões do Azure necessárias | Observações |
|-----------------------------------------------------------------|--------------------------|-------|
| Adicionar e remover soluções de gerenciamento                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Essas permissões precisam ser concedidas no nível de assinatura ou no grupo de recursos. |
| Alterar o tipo de preço                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Exibir dados nos blocos de solução *Backup* e *Site Recovery* | Administrador/coadministrador | Acessa recursos implantados usando o modelo de implantação clássico |
| Criar um workspace no portal do Azure                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


### <a name="managing-access-to-log-analytics-using-azure-permissions"></a>Gerenciar o acesso ao Log Analytics usando permissões do Azure
Para conceder acesso ao workspace do Log Analytics usando permissões do Azure, execute as etapas em [Usar atribuições de função para gerenciar o acesso aos recursos de sua assinatura do Azure](../role-based-access-control/role-assignments-portal.md).

O Azure tem duas funções de usuário predefinidas para o Log Analytics:
- Leitor do Log Analytics
- Colaborador do Log Analytics

Os membros da função *Leitor do Log Analytics* podem:
- Exibir e pesquisar todos os dados de monitoramento 
- Exiba configurações de monitoramento, incluindo exibir a configuração do diagnóstico do Azure em todos os recursos do Azure.

A função de leitor do Log Analytics inclui as seguintes ações do Azure:

| Tipo    | Permissão | DESCRIÇÃO |
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

Recomendamos que você execute atribuições no nível do recurso (workspace) para garantir o controle de acesso preciso.  Use as [funções personalizadas](../role-based-access-control/custom-roles.md) para criar funções com as permissões específicas necessárias.

## <a name="link-an-existing-workspace-to-an-azure-subscription"></a>Vincular um workspace existente a uma assinatura do Azure
Todos os workspaces criados depois de 26 de setembro de 2016 devem ser vinculados a uma assinatura do Azure no momento da criação. Os workspaces criados antes dessa data devem ser vinculados a um workspace quando você entra. Quando você cria o workspace no Portal do Azure ou vincula seu workspace a uma assinatura do Azure, o Azure Active Directory é vinculado à sua conta organizacional.

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-azure-portal"></a>Para vincular um workspace a uma assinatura do Azure no portal do Azure
1. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Log Analytics**.  

2. No painel de inscrições do Log Analytics, clique em **Adicionar**.  

    ![lista de workspaces](./media/log-analytics-manage-access/workspace-link-existing-01.png)

3. No painel **do workspace de Log Analytics**, clique em **Vincular existente**.  

4. Clique em **Definir configurações obrigatórias**.  

5. Você vê a lista de workspaces que ainda não estão vinculados à sua conta do Azure. Selecione o workspace.  
   
6. Se necessário, você pode alterar os valores para os seguintes itens:
   * Assinatura
   * Grupo de recursos
   * Local padrão
   * Tipo de preço  

7. Clique em **OK**. O workspace agora está vinculado à sua conta do Azure.

> [!NOTE]
> Caso você não veja o workspace que deseja vincular, sua assinatura do Azure não tem acesso ao workspace criado usando o portal do OMS.  Para conceder acesso a essa conta no portal do OMS, consulte [adicionar um usuário a um workspace](#add-a-user-to-an-existing-workspace).
>
>

## <a name="upgrade-a-workspace-to-a-paid-plan"></a>Atualizar um workspace para um plano pago
Há três tipos de plano de workspace para OMS: **Gratuito**, **Autônomo** e **OMS**.  Se você estiver usando o plano *Gratuito*, haverá um limite de 500 MB de dados por dia enviados para o Log Analytics.  Se exceder esse valor, você precisará alterar o workspace para um plano pago para não coletar dados acima do limite. Você pode alterar seu tipo de plano a qualquer momento.  Para obter mais informações sobre os preços do OMS, consulte [Detalhes do Preço](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-pricing).

### <a name="using-entitlements-from-an-oms-subscription"></a>Usar os direitos de uma assinatura do OMS
Para usar os direitos provenientes da aquisição de OMS E1, OMS E2 OMS ou Complemento do OMS para System Center, escolha o plano *OMS* do Log Analytics do OMS.

Quando você adquire uma assinatura do OMS, os direitos são adicionados ao Enterprise Agreement. Qualquer assinatura do Azure criada seguindo esse contrato pode usar os direitos. Todos os workspaces nessas assinaturas usam os direitos do OMS.

Para garantir que o uso de um workspace seja aplicado aos seus direitos da assinatura do OMS, você precisa:

1. Criar o workspace em uma assinatura do Azure que faz parte do Enterprise Agreement que inclui a assinatura do OMS

2. Selecione o plano *OMS* para o workspace

> [!NOTE]
> Se o workspace tiver sido criado antes de 26 de setembro de 2016 e seu plano de preços do Log Analytics for *Premium*, esse workspace usará direitos do Complemento do OMS para o System Center. Você também pode usar seus direitos alterando para o tipo de preços *OMS*.
>
>

Os direitos de assinatura do OMS não são visíveis no portal do Azure. Você pode ver os direitos e o uso no Portal Enterprise.  

Se você precisar alterar a assinatura do Azure vinculada ao seu workspace, poderá usar o cmdlet do Azure PowerShell [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx).

### <a name="using-azure-commitment-from-an-enterprise-agreement"></a>Usar o Azure Commitment de um Enterprise Agreement
Se não tiver uma assinatura do OMS, você pagará separadamente por cada componente do OMS, e o uso será exibido em sua conta do Azure.

Se você tiver um compromisso monetário do Azure no registro corporativo ao qual suas assinaturas do Azure estão vinculadas, o uso do Log Analytics debitará automaticamente qualquer compromisso monetário restante.

Se você precisar alterar a assinatura do Azure vinculada ao seu workspace, poderá usar o cmdlet do Azure PowerShell [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx).  

### <a name="change-a-workspace-to-a-paid-pricing-tier-in-the-azure-portal"></a>Alterar um workspace para um tipo de preço pago no Portal do Azure
1. No portal do Azure, no painel de inscrições do Log Analytics, selecione um workspace.

2. No painel de workspace, sob **gerais**, selecione **tipo de preço**.  

3. Sob **tipo de preço**, selecione um tipo de preço e, em seguida, clique em **selecione**.  
    ![Selecionado o plano de preços](./media/log-analytics-manage-access/workspace-pricing-tier-info.png)

> [!NOTE]
> Se o seu workspace está vinculado a uma conta de automação, antes de poder selecionar o tipo de preços *Autônomo (por GB)*, deve excluir quaisquer soluções de **Automação e Controle** e desvincular a conta de Automação. Na folha do workspace, em **geral**, clique em **soluções** para ver e excluir soluções. Para desvincular a conta de automação, clique no nome da conta de automação na folha **Tipo de preços**.
>
>

### <a name="change-a-workspace-to-a-paid-pricing-tier-in-the-oms-portal"></a>Alterar um workspace para um tipo de preço pago no portal do OMS

Para alterar o tipo de preço usando o portal do OMS, você deve ter uma assinatura do Azure.

1. No portal do OMS, clique no bloco **Configurações**.

2. Clique na guia **Contas**, em seguida, clique na guia **Plano de Assinatura e Dados do Azure**.

3. Clique no tipo de preço que você deseja usar.

4. Clique em **Salvar**.  

    ![planos de assinatura e seus dados](./media/log-analytics-manage-access/subscription-tab.png)

Seu novo plano de dados é exibido na faixa de opções do portal do OMS, na parte superior da página da Web.

![faixa de opções do OMS](./media/log-analytics-manage-access/data-plan-changed.png)

## <a name="next-steps"></a>Próximas etapas
* Consulte a [visão geral do agente do Log Analytics](../azure-monitor/platform/log-analytics-agent.md) para reunir dados de computadores no datacenter ou outro ambiente de nuvem.
* Consulte [Coletar dados sobre as máquinas virtuais do Azure](log-analytics-quick-collect-azurevm.md) para configurar a coleta de dados de máquinas virtuais do Azure.  
* [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](../azure-monitor/insights/solutions.md) para adicionar funcionalidades e reunir dados.

