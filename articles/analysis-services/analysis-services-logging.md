---
title: "Log de diagnósticos para o Azure Analysis Services | Microsoft Docs"
description: "Saiba mais sobre como configurar o log de diagnósticos do Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: 
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/14/2018
ms.author: owend
ms.openlocfilehash: cadd47d2e5f490f82846ea562803fcd60f5405a7
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="setup-diagnostic-logging"></a>Configurar o log de diagnósticos

Uma parte importante de qualquer solução do Analysis Services é o monitoramento do desempenho de seus servidores. Com os [logs de diagnósticos de recurso do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md), você pode monitorar e enviar logs para o [Armazenamento do Azure](https://azure.microsoft.com/services/storage/), transmiti-los para os [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/) e exportá-los para o [Log Analytics](https://azure.microsoft.com/services/log-analytics/), que faz parte do [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite). 

![Log de diagnósticos para o Armazenamento, Hubs de Eventos ou Operations Management Suite por meio do Log Analytics](./media/analysis-services-logging/aas-logging-overview.png)


## <a name="whats-logged"></a>O que é registrado em log?

Você pode selecionar as categorias **Mecanismo**, **Serviço** e **Métricas**.

### <a name="engine"></a>Motor

Selecionar **Mecanismo** registra todos [xEvents](https://docs.microsoft.com/sql/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). Não é possível selecionar eventos individuais. 

|Categorias de XEvent |Nome do evento  |
|---------|---------|
|Auditoria de Segurança    |   Auditar logon      |
|Auditoria de Segurança    |   Auditar logoff      |
|Auditoria de Segurança    |   Auditar início e parada de servidor      |
|Relatórios de andamento     |   Início de relatório de andamento      |
|Relatórios de andamento     |   Fim de relatório de andamento      |
|Relatórios de andamento     |   Relatório de andamento atual      |
|Consultas     |  Início de consulta       |
|Consultas     |   Término de consulta      |
|Comandos     |  Início de comando       |
|Comandos     |  Término de comando       |
|Erros e avisos     |   Erro      |
|Descobrir     |   Término de descoberta      |
|Notificação     |    Notificação     |
|Session     |  Inicialização de sessão       |
|Bloqueios    |  Deadlock       |
|Processamento de consulta     |   Início da consulta VertiPaq SE      |
|Processamento de consulta     |   Final da consulta VertiPaq SE      |
|Processamento de consulta     |   Correspondência de Cache de consulta VertiPaq SE      |
|Processamento de consulta     |   Início de consulta direta      |
|Processamento de consulta     |  Término de consulta direta       |

### <a name="service"></a>Serviço

|Nome da operação  |Ocorre quando  |
|---------|---------|
|CreateGateway     |   Usuário configura um gateway no servidor      |
|ResumeServer     |    Retomar um servidor     |
|SuspendServer    |   Pausar um servidor      |
|DeleteServer     |    Excluir um servidor     |
|RestartServer    |     Usuário reinicia um servidor por meio do SSMS ou PowerShell    |
|GetServerLogFiles    |    Usuário exporta log de servidor por meio do PowerShell     |
|ExportModel     |   Usuário exporta um modelo no portal usando Abrir no Visual Studio     |

### <a name="all-metrics"></a>Todas as métricas

A categoria Métricas registra as mesmas [Métricas de servidor](analysis-services-monitor.md#server-metrics) exibidas em Métricas.

## <a name="setup-diagnostics-logging"></a>Configurar o log de diagnósticos

### <a name="azure-portal"></a>Portal do Azure

1. Em [Portal do Azure](https://portal.azure.com) > Servidor, clique em **Logs de diagnósticos** na navegação à esquerda e, em seguida, clique em **Ativar diagnóstico**.

    ![Ativar o log de diagnósticos do Azure Cosmos DB no portal do Azure](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. Em **Configurações de diagnóstico**, especifique as opções a seguir: 

    * **Nome**. Insira um nome para os logs a serem criados.

    * **Arquivar em uma conta de armazenamento**. Para usar essa opção, você precisa de uma conta de armazenamento existente à qual se conectar. Consulte [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md). Siga as instruções para criar um Gerenciador de Recursos, uma conta de finalidade geral, em seguida, selecione sua conta de armazenamento retornando para esta página no portal. Pode levar alguns minutos para que as contas de armazenamento recém-criadas sejam exibidas no menu suspenso.
    * **Transmitir para um hub de eventos**. Para usar essa opção, é necessário ter um namespace existente do Hub de Eventos e um hub de evento ao qual se conectar. Para saber mais, consulte [Criar um namespace de Hubs de Eventos e um hub de eventos usando o Portal do Azure](../event-hubs/event-hubs-create.md). Em seguida, retorne a esta página no portal para selecionar o namespace e o nome da política do Hub de Eventos.
    * **Enviar para o Log Analytics**. Para usar essa opção, use um espaço de trabalho existente ou crie um novo espaço de trabalho do Log Analytics seguindo as etapas para [criar um novo espaço de trabalho](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace) no portal. Para obter mais informações sobre como exibir os logs no Log Analytics, consulte [Exibir logs no Log Analytics](#view-in-loganalytics).

    * **Mecanismo**. Selecione esta opção para registrar xEvents. Se você estiver arquivando em uma conta de armazenamento, poderá selecionar o período de retenção para os logs de diagnóstico. Os logs são excluídos automaticamente depois que o período de retenção expira.
    * **Serviço**. Selecione esta opção para registrar eventos de nível de serviço. Se você estiver arquivando em uma conta de armazenamento, poderá selecionar o período de retenção para os logs de diagnóstico. Os logs são excluídos automaticamente depois que o período de retenção expira.
    * **Métricas**. Selecione esta opção para armazenar dados detalhados em [Métricas](analysis-services-monitor.md#server-metrics). Se você estiver arquivando em uma conta de armazenamento, poderá selecionar o período de retenção para os logs de diagnóstico. Os logs são excluídos automaticamente depois que o período de retenção expira.

3. Clique em **Salvar**.

    Se você receber um erro que diga "Falha ao atualizar o diagnóstico para \<nome do espaço de trabalho>. A assinatura \<id da assinatura> não está registrada para usar o microsoft.insights." siga as instruções para [Solucionar Problemas de Diagnóstico do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) para registrar a conta, então repita este procedimento.

    Sempre que você quiser alterar como os seus logs de diagnóstico são salvos, retorne a esta página para modificar as configurações.

### <a name="powershell"></a>PowerShell

Aqui estão os comandos básicos para você começar. Se você desejar obter ajuda passo a passo sobre como configurar o registro em log para uma conta de armazenamento usando o PowerShell, consulte o tutorial mais adiante neste artigo.

Para habilitar as métricas e o log de diagnósticos usando o PowerShell, use os seguintes comandos:

- Para habilitar o armazenamento do log de diagnóstico em uma conta de armazenamento, use este comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   A ID da conta de armazenamento é a ID de recurso da conta de armazenamento para a qual os logs serão enviados.

- Para habilitar o streaming dos logs de diagnóstico para um hub de eventos, use este comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   A ID da regra do Barramento de Serviço do Azure é uma cadeia de caracteres com este formato:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Para habilitar o envio de logs de diagnóstico para um espaço de trabalho do Log Analytics, use este comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- É possível obter a ID de recurso do espaço de trabalho do Log Analytics usando o seguinte comando:

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

Você pode combinar esses parâmetros para permitir várias opções de saída.

### <a name="rest-api"></a>API REST

Saiba como [alterar as configurações de diagnóstico usando a API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931931.aspx). 

### <a name="resource-manager-template"></a>Modelo do Resource Manager

Saiba como [habilitar as configurações de diagnóstico na criação de recursos usando um modelo do Resource Manager](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md). 

## <a name="manage-your-logs"></a>Gerenciar seus logs

Os logs estão geralmente disponíveis em algumas horas após a configuração do registro em log. Cabe a você gerenciar os logs em sua conta de armazenamento:

* Use os métodos de controle de acesso padrão do Azure para proteger seus logs ao restringir quem pode acessá-los.
* Exclua os logs que você não deseja manter em sua conta de armazenamento.
* Certifique-se de definir um período de retenção para que logs antigos sejam excluídos da sua conta de armazenamento.

## <a name="view-logs-in-log-analytics"></a>Exibir logs no Log Analytics

Os eventos de métricas e de servidor são integrados com o xEvents no Log Analytics para uma análise lado a lado. O Log Analytics também pode ser configurado para receber eventos de outros serviços do Azure, fornecendo uma exibição holística dos dados de log de diagnósticos em sua arquitetura.

Para exibir seus dados de diagnóstico no Log Analytics, abra a página de Pesquisa de Logs no menu à esquerda ou na área de Gerenciamento, conforme mostrado abaixo.

![Opções de pesquisa de logs no portal do Azure](./media/analysis-services-logging/aas-logging-open-log-search.png)

Agora que você habilitou a coleta de dados, em **Pesquisa de Logs**, clique em **Todos os dados coletados**.

Em **Tipo**, clique em **AzureDiagnostics** e, em seguida, clique em **Aplicar**. O AzureDiagnostics inclui eventos de Mecanismo e Serviço. Veja que uma consulta do Log Analytics é imediatamente criada. O campo EventClass\_s contém nomes de xEvents, que podem parecer familiares se você já usou xEvents para registro em log local.

Clique em **EventClass\_s** ou em um dos nomes de eventos e o Log Analytics continuará a criar uma consulta. Certifique-se de salvar as consultas para reutilizar posteriormente.

Não se esqueça de consultar o Operations Management Suite, que oferece um site com recursos de consulta, criação de painéis e de alertas aprimorados nos dados do Log Analytics.

### <a name="queries"></a>Consultas

Há centenas de consultas que você pode usar. Veja algumas para começar.
Para saber mais sobre como usar a nova linguagem de consulta da Pesquisa de Logs, consulte [Entender as pesquisas de log no Log Analytics](../log-analytics/log-analytics-log-search-new.md). 

* Consulta que retorna as consultas enviadas ao Azure Analysis Services que demoraram mais de cinco minutos (300.000 milissegundos) para serem concluídas.

    ```
    search * | where ( Type == "AzureDiagnostics" ) | where ( EventClass_s == "QUERY_END" ) | where toint(Duration_s) > 300000
    ```

* Identificar réplicas de expansão.

    ```
    search * | summarize count() by ServerName_s
    ```
    Ao expandir, você pode identificar réplicas somente leitura porque os valores de campo ServerName\_s têm o número da instância de réplica anexado ao nome. O campo de recurso contém o nome de recurso do Azure, que corresponde ao nome do servidor que os usuários veem. O campo IsQueryScaleoutReadonlyInstance_s é igual a true para réplicas.



> [!TIP]
> Você tem uma ótima consulta do Log Analytics e deseja compartilhar? Se você tem uma conta do GitHub, pode adicioná-la a este artigo. Basta clicar em **Editar** na parte superior direita desta página.


## <a name="tutorial---turn-on-logging-by-using-powershell"></a>Tutorial – ativar o registro em log usando o PowerShell
Neste tutorial rápido, você cria uma conta de armazenamento na mesma assinatura e mesmo grupo de recursos que o servidor do Analysis Services. Então, você usa Set-AzureRmDiagnosticSetting para ativar o log de diagnósticos, enviando a saída para a nova conta de armazenamento.

### <a name="prerequisites"></a>pré-requisitos
Para concluir este tutorial, você deve ter os seguintes recursos:

* Um servidor existente do Azure Analysis Services. Para obter instruções de como criar um recurso de servidor, consulte [Criar um servidor no Portal do Azure](analysis-services-create-server.md) ou [Criar um servidor do Azure Analysis Services usando o PowerShell](analysis-services-create-powershell.md).

### <a name="aconnect-to-your-subscriptions"></a></a>Conectar-se às suas assinaturas

Inicie uma sessão do PowerShell do Azure e entre em sua conta do Azure com o seguinte comando:  

```powershell
Login-AzureRmAccount
```

Na janela pop-up do navegador, insira o nome de usuário e a senha da sua conta do Azure. O Azure PowerShell obtém todas as assinaturas que estão associadas a essa conta e, por padrão, usa a primeira.

Se você tiver várias assinaturas, talvez tenha que indicar uma assinatura específica que tenha sido usada para criar o Cofre de Chaves do Azure. Digite o seguinte para ver as assinaturas da sua conta:

```powershell
Get-AzureRmSubscription
```

Em seguida, para especificar a assinatura associada à conta do Azure Analysis Services que está sendo registrada, digite:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Caso tenha várias assinaturas associadas à sua conta, é importante especificar a assinatura.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Criar uma nova conta de armazenamento para seus logs

Você pode usar uma conta de armazenamento existente para seus logs, desde que ela esteja na mesma assinatura que o servidor. Neste tutorial você cria uma nova conta de armazenamento dedicada aos logs do Analysis Services. Para facilitar, você armazenará os detalhes da conta de armazenamento em uma variável chamada **sa**.

Você também usa o mesmo grupo de recursos que aquele que contém o servidor do Analysis Services. Substitua os valores de `awsales_resgroup`, `awsaleslogs` e `West Central US` pelos seus próprios valores:

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Identificar a conta de servidor para seus logs

Defina o nome da conta do como uma variável chamada **account**, em que ResourceName é o nome da conta.

```powershell
$account = Get-AzureRmResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Habilitar o registro em log

Para habilitar o registro em log, use o cmdlet Set-AzureRmDiagnosticSetting junto com as variáveis para a nova conta de armazenamento, a conta do servidor e a categoria. Execute o seguinte comando, definindo o sinalizador **-Enabled** como **$true**:

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

A saída deve ser semelhante a esta:

```powershell
StorageAccountId            : 
/subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourceGroups/awsales_resgroup/providers/Microsoft.Storage/storageAccounts/awsaleslogs
ServiceBusRuleId            :
EventHubAuthorizationRuleId :
Metrics                    
    TimeGrain       : PT1M
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


Logs                       
    Category        : Engine
    Enabled         : True
    RetentionPolicy
    Enabled : False
    Days    : 0


    Category        : Service
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


WorkspaceId                 :
Id                          : /subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourcegroups/awsales_resgroup/providers/microsoft.analysisservic
es/servers/awsales/providers/microsoft.insights/diagnosticSettings/service
Name                        : service
Type                        :
Location                    :
Tags                        :
```

Isso confirma que o registro em log agora está habilitado para o servidor, salvando as informações na conta de armazenamento.

Você também pode definir a política de retenção para os logs, para que logs mais antigos sejam excluídos automaticamente. Por exemplo, defina a política de retenção usando o sinalizador **-RetentionEnabled** como **$true** e defina o parâmetro **-RetentionInDays** como **90**. Os logs com mais de 90 dias serão automaticamente excluídos.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [o registro em log de diagnóstico de recurso do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

Consulte [Set-AzureRmDiagnosticSetting](https://docs.microsoft.com/powershell/module/azurerm.insights/Set-AzureRmDiagnosticSetting) na ajuda do PowerShell.