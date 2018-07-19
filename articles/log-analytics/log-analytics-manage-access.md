---
title: Gerenciar espaços de trabalho no Azure Log Analytics e no portal do OMS | Microsoft Docs
description: Gerencie espaços de trabalho no Azure Log Analytics e no portal do OMS usando várias tarefas administrativas em usuários, contas, espaços de trabalho e contas do Azure.
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
ms.date: 05/17/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 3b4e0f978cc7d23d0157b78fd2dff27096d2768b
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37133185"
---
# <a name="manage-workspaces"></a>Gerenciar espaços de trabalho

Para gerenciar o acesso ao Log Analytics, você executa várias tarefas administrativas relacionadas aos espaços de trabalho. Este artigo fornece conselhos de práticas recomendados e os procedimentos para gerenciar espaços de trabalho. Um espaço de trabalho é essencialmente um contêiner que inclui informações da conta e informações de configuração simples para a conta. Você ou outros membros de sua organização podem usar vários espaços de trabalho para gerenciar diferentes conjuntos de dados que são coletados de todos ou de partes da sua infraestrutura de TI.

Para criar um espaço de trabalho, você precisa:

1. Tenho uma assinatura do Azure.
2. Escolher um nome para o espaço de trabalho.
3. Associe o espaço de trabalho com uma das suas assinaturas e grupos de recursos.
4. Escolher uma localização geográfica.

## <a name="determine-the-number-of-workspaces-you-need"></a>Determinar o número de espaços de trabalho que você precisa
Um espaço de trabalho é um recurso do Azure e é um contêiner no qual os dados são coletados, agregados, analisados e apresentados no portal do Azure.

Você pode ter vários espaços de trabalho por assinatura do Azure e ter acesso a mais de um espaço de trabalho, com a capacidade de realizar uma consulta facilmente. Esta seção descreve quando pode ser útil criar mais de um espaço de trabalho.

Hoje, um espaço de trabalho fornece:

* Uma localização geográfica para o armazenamento dos dados
* Isolamento de dados para definir direitos de acesso de usuário diferente
* Escopo para a configuração de configurações, como a limitação de dados e retenção

Do ponto de vista de consumo, é recomendável criar o menor número possível de espaços de trabalho. Isso torna a administração e consulta experiências mais fácil e mais rápido. Porém, com base nas características anteriores, pode ser útil criar vários espaços de trabalho se:

* Você é uma empresa global e precisa de dados armazenados em regiões específicas por motivos de soberania de dados ou conformidade.
* Você usa o Azure e deseja evitar encargos de transferência de dados de saída com um espaço de trabalho na mesma região que os recursos do Azure que ele gerencia.
* Você deseja alocar os encargos para diferentes departamentos ou grupos de negócios com base no uso. Quando você cria um espaço de trabalho para cada departamento ou grupo de negócios em sua própria assinatura do Azure.
* Você é um provedor de serviço gerenciado e precisa para manter os dados do Log Analytics para cada cliente que você gerencia isolados dos de outros clientes.
* Você gerencia vários clientes e deseja que cada cliente/departamento/grupo de negócios veja seus próprios dados, mas não os dados de outras pessoas.

Ao usar agentes do Windows para coletar dados, você pode [configurar cada agente para relatar para um ou mais espaços de trabalho](log-analytics-windows-agents.md).

Se você estiver usando o System Center Operations Manager, cada grupo de gerenciamento do Operations Manager poderá ser conectado a apenas um espaço de trabalho. Você pode instalar o Microsoft Monitoring Agent em computadores gerenciados pelo Operations Manager e fazer o agente relatar ao Operations Manager e a um espaço de trabalho do Log Analytics diferente.

### <a name="workspace-information"></a>Informações do espaço de trabalho

Você pode exibir detalhes sobre o espaço de trabalho no portal do Azure. 

#### <a name="view-workspace-information-in-the-azure-portal"></a>Exibir informações do espaço de trabalho no portal do Azure

1. Se ainda não tiver feito isso, entre no [portal do Azure](https://portal.azure.com) usando a sua assinatura do Azure.
2. No menu **Hub**, clique em **Mais serviços** e, na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Clique em **Log Analytics**.  
    ![Hub do Azure](./media/log-analytics-manage-access/hub.png)  
3. Na folha de assinaturas do Log Analytics, selecione um espaço de trabalho.
4. A folha de espaço de trabalho exibe detalhes sobre o espaço de trabalho e links para obter informações adicionais.  
    ![detalhes do espaço de trabalho](./media/log-analytics-manage-access/workspace-details.png)  


## <a name="manage-accounts-and-users"></a>Gerenciar contas e usuários
Cada espaço de trabalho pode ter várias contas associadas e cada conta pode ter acesso a vários espaços de trabalho. O acesso é gerenciado via [acesso baseado em função do Azure](../active-directory/role-based-access-control-configure.md). Esses direitos de acesso se aplicam no portal do Azure e sobre o acesso de API.


As atividades a seguir também exigem permissões do Azure:

| Ação                                                          | Permissões do Azure necessárias | Observações |
|-----------------------------------------------------------------|--------------------------|-------|
| Adicionar e remover soluções de gerenciamento                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Essas permissões precisam ser concedidas no nível de assinatura ou no grupo de recursos. |
| Alterar o tipo de preço                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Exibir dados nos blocos de solução *Backup* e *Site Recovery* | Administrador/coadministrador | Acessa recursos implantados usando o modelo de implantação clássico |
| Criar um espaço de trabalho no portal do Azure                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


### <a name="managing-access-to-log-analytics-using-azure-permissions"></a>Gerenciar o acesso ao Log Analytics usando permissões do Azure
Para conceder acesso ao espaço de trabalho do Log Analytics usando permissões do Azure, execute as etapas em [Usar atribuições de função para gerenciar o acesso aos recursos de sua assinatura do Azure](../active-directory/role-based-access-control-configure.md).

O Azure tem duas funções de usuário predefinidas para o Log Analytics:
- Leitor do Log Analytics
- Colaborador do Log Analytics

Os membros da função *Leitor do Log Analytics* podem:
- Exibir e pesquisar todos os dados de monitoramento 
- Exiba configurações de monitoramento, incluindo exibir a configuração do diagnóstico do Azure em todos os recursos do Azure.

A função de leitor do Log Analytics inclui as seguintes ações do Azure:

| type    | Permissão | DESCRIÇÃO |
| ------- | ---------- | ----------- |
| Ação | `*/read`   | Capacidade de exibir todos os recursos do Azure e a configuração do recurso. Inclui exibir: <br> Status de extensão da máquina virtual <br> Configuração do diagnóstico do Azure nos recursos <br> Todas as propriedades e configurações de todos os recursos |
| Ação | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Capacidade de execução de consultas de Pesquisa de Logs v2 |
| Ação | `Microsoft.OperationalInsights/workspaces/search/action` | Capacidade de execução de consultas de Pesquisa de Logs v1 |
| Ação | `Microsoft.Support/*` | Capacidade de abrir casos de suporte |
|Nenhuma Ação | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Impede a leitura da chave do espaço de trabalho necessária para usar a API da coleta de dados e instalar os agentes. Isso impede que o usuário adicione os novos recursos para o espaço de trabalho |


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
| `Microsoft.OperationalInsights/*` | Adicionar, atualizar e remover a configuração dos espaços de trabalho do Log Analytics |
| `Microsoft.OperationsManagement/*` | Adicionar e remover soluções de gerenciamento |
| `Microsoft.Resources/deployments/*` | Crie e exclua implantações. Necessário para adicionar e remover soluções, espaços de trabalho e contas de automação |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Crie e exclua implantações. Necessário para adicionar e remover soluções, espaços de trabalho e contas de automação |

Para adicionar e remover usuários de uma função de usuário, é necessário ter as permissões `Microsoft.Authorization/*/Delete` e `Microsoft.Authorization/*/Write`.

Use essas funções para conceder acesso aos usuários em escopos diferentes:
- Assinatura – acesso a todos os espaços de trabalho na assinatura
- Grupo de Recursos - acesso a todo espaço de trabalho no grupo de recursos
- Recurso - acesso somente ao espaço de trabalho especificado

Recomendamos que você execute atribuições no nível do recurso (espaço de trabalho) para garantir o controle de acesso preciso.  Use as [funções personalizadas](../active-directory/role-based-access-control-custom-roles.md) para criar funções com as permissões específicas necessárias.

## <a name="link-an-existing-workspace-to-an-azure-subscription"></a>Vincular um espaço de trabalho existente a uma assinatura do Azure
Todos os espaços de trabalho criados depois de 26 de setembro de 2016 devem ser vinculados a uma assinatura do Azure no momento da criação. Os espaços de trabalho criados antes dessa data devem ser vinculados a um espaço de trabalho quando você entra. Quando você cria o espaço de trabalho no Portal do Azure ou vincula seu espaço de trabalho a uma assinatura do Azure, o Azure Active Directory é vinculado à sua conta organizacional.

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-azure-portal"></a>Para vincular um espaço de trabalho a uma assinatura do Azure no portal do Azure
1. Faça logon no [Portal do Azure](http://portal.azure.com).
2. Procure pelo **Log Analytics** e selecione-o.
3. Você vê sua lista de espaços de trabalho existentes. Clique em **Adicionar**.  
   ![lista de espaços de trabalho](./media/log-analytics-manage-access/manage-access-link-azure01.png)
4. No **Espaço de Trabalho do OMS**, clique em **ou no link existente**.  
   ![link existente](./media/log-analytics-manage-access/manage-access-link-azure02.png)
5. Clique em **Definir configurações obrigatórias**.  
   ![configure required settings](./media/log-analytics-manage-access/manage-access-link-azure03.png)
6. Você vê a lista de espaços de trabalho que ainda não estão vinculados à sua conta do Azure. Selecione um espaço de trabalho.  
   ![selecionar espaços de trabalho](./media/log-analytics-manage-access/manage-access-link-azure04.png)
7. Se necessário, você pode alterar os valores para os seguintes itens:
   * Assinatura
   * Grupo de recursos
   * Local padrão
   * Tipo de preço   
     ![alterar valores](./media/log-analytics-manage-access/manage-access-link-azure05.png)
8. Clique em **OK**. O espaço de trabalho agora está vinculado à sua conta do Azure.

> [!NOTE]
> Caso você não veja o espaço de trabalho que deseja vincular, sua assinatura do Azure não tem acesso ao espaço de trabalho criado usando o portal do OMS.  Para conceder acesso a essa conta no portal do OMS, consulte [adicionar um usuário a um espaço de trabalho](#add-a-user-to-an-existing-workspace).
>
>

## <a name="upgrade-a-workspace-to-a-paid-plan"></a>Atualizar um espaço de trabalho para um plano pago
Há três tipos de plano de espaço de trabalho para OMS: **Gratuito**, **Autônomo** e **OMS**.  Se você estiver usando o plano *Gratuito*, haverá um limite de 500 MB de dados por dia enviados para o Log Analytics.  Se exceder esse valor, você precisará alterar o espaço de trabalho para um plano pago para não coletar dados acima do limite. Você pode alterar seu tipo de plano a qualquer momento.  Para obter mais informações sobre os preços do OMS, consulte [Detalhes do Preço](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-pricing).

### <a name="using-entitlements-from-an-oms-subscription"></a>Usar os direitos de uma assinatura do OMS
Para usar os direitos provenientes da aquisição de OMS E1, OMS E2 OMS ou Complemento do OMS para System Center, escolha o plano *OMS* do Log Analytics do OMS.

Quando você adquire uma assinatura do OMS, os direitos são adicionados ao Enterprise Agreement. Qualquer assinatura do Azure criada seguindo esse contrato pode usar os direitos. Todos os espaços de trabalho nessas assinaturas usam os direitos do OMS.

Para garantir que o uso de um espaço de trabalho seja aplicado aos seus direitos da assinatura do OMS, você precisa:

1. Criar o espaço de trabalho em uma assinatura do Azure que faz parte do Enterprise Agreement que inclui a assinatura do OMS
2. Selecione o plano *OMS* para o espaço de trabalho

> [!NOTE]
> Se o espaço de trabalho tiver sido criado antes de 26 de setembro de 2016 e seu plano de preços do Log Analytics for *Premium*, esse espaço de trabalho usará direitos do Complemento do OMS para o System Center. Você também pode usar seus direitos alterando para o tipo de preços *OMS*.
>
>

Os direitos de assinatura do OMS não são visíveis no portal do Azure ou do OMS. Você pode ver os direitos e o uso no Portal Enterprise.  

Se você precisar alterar a assinatura do Azure vinculada ao seu espaço de trabalho, poderá usar o cmdlet do Azure PowerShell [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx) .

### <a name="using-azure-commitment-from-an-enterprise-agreement"></a>Usar o Azure Commitment de um Enterprise Agreement
Se não tiver uma assinatura do OMS, você pagará separadamente por cada componente do OMS, e o uso será exibido em sua conta do Azure.

Se você tiver um compromisso monetário do Azure no registro corporativo ao qual suas assinaturas do Azure estão vinculadas, o uso do Log Analytics debitará automaticamente qualquer compromisso monetário restante.

Se você precisar alterar a assinatura do Azure vinculada ao seu espaço de trabalho, poderá usar o cmdlet do Azure PowerShell [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx) .  

### <a name="change-a-workspace-to-a-paid-pricing-tier-in-the-azure-portal"></a>Alterar um espaço de trabalho para um tipo de preço pago no Portal do Azure
1. Faça logon no [Portal do Azure](http://portal.azure.com).
2. Procure pelo **Log Analytics** e selecione-o.
3. Você vê sua lista de espaços de trabalho existentes. Selecione um espaço de trabalho.  
4. Na folha do espaço de trabalho, em **Geral**, clique em **Tipo de preço**.  
5. Em **Tipo de preço**, clique em selecionar um tipo de preço e clique em **Selecionar**.  
    ![selecionar plano](./media/log-analytics-manage-access/manage-access-change-plan03.png)
6. Ao atualizar sua exibição do Portal do Azure, você vê o **Tipo de preço** atualizado para o tipo selecionado.  
    ![plano atualizado](./media/log-analytics-manage-access/manage-access-change-plan04.png)

> [!NOTE]
> Se o seu espaço de trabalho está vinculado a uma conta de automação, antes de poder selecionar o tipo de preços *Autônomo (por GB)*, deve excluir quaisquer soluções de **Automação e Controle** e desvincular a conta de Automação. Na folha do espaço de trabalho, em **geral**, clique em **soluções** para ver e excluir soluções. Para desvincular a conta de automação, clique no nome da conta de automação na folha **Tipo de preços**.
>
>

### <a name="change-a-workspace-to-a-paid-pricing-tier-in-the-oms-portal"></a>Alterar um espaço de trabalho para um tipo de preço pago no portal do OMS

Para alterar o tipo de preço usando o portal do OMS, você deve ter uma assinatura do Azure.

1. No portal do OMS, clique no bloco **Configurações**.
2. Clique na guia **Contas**, em seguida, clique na guia **Plano de Assinatura e Dados do Azure**.
3. Clique no tipo de preço que você deseja usar.
4. Clique em **Salvar**.  
   ![planos de assinatura e dados](./media/log-analytics-manage-access/subscription-tab.png)

Seu novo plano de dados é exibido na faixa de opções do portal do OMS, na parte superior da página da Web.

![faixa de opções do OMS](./media/log-analytics-manage-access/data-plan-changed.png)


## <a name="change-how-long-log-analytics-stores-data"></a>Alterar o tempo que o Log Analytics leva para armazenar dados

No tipo de preço Gratuito, o Log Analytics disponibiliza os últimos sete dias de dados.
No tipo de preço Standard, o Log Analytics disponibiliza os últimos 30 dias de dados.
No tipo de preço Premium, o Log Analytics disponibiliza os últimos 365 dias de dados.
Nos tipos de preço Autônomo e OMS, o Log Analytics disponibiliza por padrão os últimos 31 dias de dados.

Quando você usa os tipos de preço OMS e Autônomo, pode manter até dois anos de dados (730 dias). Dados armazenados por mais tempo do que o padrão de 31 dias acarreta um custo por retenção de dados. Para obter mais informações sobre preços, consulte [encargos excedentes](https://azure.microsoft.com/pricing/details/log-analytics/).

Para alterar o período de tempo de retenção de dados, consulte [Gerenciar o custo controlando a retenção e o volume de dados no Log Analytics](log-analytics-manage-cost-storage.md).


## <a name="delete-a-log-analytics-workspace"></a>Excluir um espaço de trabalho do Log Analytics
Quando você exclui um espaço de trabalho do Log Analytics, todos os dados relacionados ao espaço de trabalho são excluídos do serviço Log Analytics em até 30 dias.

Se você for um administrador e houver vários usuários associados ao espaço de trabalho, a associação entre os usuários e o espaço de trabalho será interrompida. Se os usuários estiverem associados a outros espaços de trabalho, eles poderão continuar usando o Log Analytics com esses outros espaços. No entanto, se eles não estiverem associados a outros espaços de trabalho, precisarão criar um espaço de trabalho para usar o serviço. Para excluir um espaço de trabalho, consulte [Excluir um espaço de trabalho do Azure Log Analytics](log-analytics-manage-del-workspace.md)

## <a name="next-steps"></a>Próximas etapas
* Consulte [Coletar dados de computadores em seu ambiente com o Log Analytics](log-analytics-concept-hybrid.md) para coletar dados de computadores em seu data center ou em outro ambiente de nuvem.
* Consulte [Coletar dados sobre as máquinas virtuais do Azure](log-analytics-quick-collect-azurevm.md) para configurar a coleta de dados de máquinas virtuais do Azure.  
* [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md) para adicionar funcionalidades e reunir dados.

