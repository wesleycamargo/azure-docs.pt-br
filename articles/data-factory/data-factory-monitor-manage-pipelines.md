---
title: Monitorar e gerenciar pipelines do Azure Data Factory
description: "Saiba como usar o Portal do Azure e o Azure PowerShell para monitorar e gerenciar as data factories e os pipelines do Azure que você criou."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 9b0fdc59-5bbe-44d1-9ebc-8be14d44def9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 6a816e56400abe281b0422abbcd3415f3762a96e
ms.openlocfilehash: 43b828bad5d21fe0f452aa23bbdf6fff758525f2


---
# <a name="monitor-and-manage-azure-data-factory-pipelines"></a>Monitorar e gerenciar pipelines do Azure Data Factory
> [!div class="op_single_selector"]
> * [Usando o Portal do Azure/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Usando o aplicativo de monitoramento e gerenciamento](data-factory-monitor-manage-app.md)
> 
> 

O serviço de Fábrica de Dados fornece uma exibição confiável e completa de seus serviços de armazenamento, processamento e movimentação de dados. O serviço oferece ajuda do painel de monitoramento que você pode usar para executar estas tarefas: 

* Avaliar rapidamente a integridade de dados de ponta a ponta do pipeline.
* Identificar problemas e tomar uma ação corretiva, se necessário. 
* Acompanhar a linhagem de dados. 
* Acompanhar relações entre seus dados através de qualquer uma das suas fontes.
* Exibir um histórico de estatísticas de execução do trabalho, a integridade do sistema e as dependências.

Este artigo descreve como monitorar, gerenciar e depurar seus pipelines. Ele também fornece informações sobre como criar alertas e ser notificado sobre falhas.

## <a name="understand-pipelines-and-activity-states"></a>Entenda os pipelines e os estados de atividade
Usando o portal do Azure, você pode:

* Exibir sua data factory como um diagrama
* Exibir atividades dentro de um pipeline
* Exibir conjuntos de dados de entrada e saída
* e mais. 

Esta seção também explica como uma fatia faz a transição de um estado para outro estado.   

### <a name="navigate-to-your-data-factory"></a>Navegue até sua data factory
1. Entre no [Portal do Azure](https://portal.azure.com).
2. Clique em **Data factories** no menu à esquerda. Se você não o vir, clique em **Mais serviços >**e clique em **Data factories** na categoria **INTELIGÊNCIA + ANÁLISE**. 
   
   ![Procurar tudo -> fata Factories](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
   
   Você deve ver todas as data factories na folha **Data factories** . 
3. Na folha Data factories, selecione o data factory em que você está interessado.
   
    ![selecionar data factory](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)  
4. e você deverá ver a home page (folha **Data factory**) do data factory.
   
   ![Folha Data factory](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Modo de exibição de diagrama de uma data factory
O Modo de exibição de diagrama de uma data factory fornece um único painel onde você pode monitorar e gerenciar a data factory e seus ativos.

Para exibir o diagrama de seu data factory, clique em **Diagrama** na home page da data factory.

![Exibição de diagrama](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Você pode ampliar, reduzir, ajustar nível de zoom, aplicar zoom 100%, bloquear o layout do diagrama e posicionar automaticamente pipelines e tabelas. Você também pode ver as informações de linhagem de dados (mostrar itens upstream e downstream dos itens selecionados).

### <a name="activities-inside-a-pipeline"></a>Atividades dentro de um pipeline
1. Clique com o botão direito do mouse no pipeline e clique em **Abrir pipeline** para ver todas as atividades no pipeline junto com conjuntos de dados de entrada e saída para as atividades. Esse recurso é útil quando o pipeline é composto de mais de uma atividade e você deseja compreender a linhagem operacional de um único pipeline.
   
    ![Menu do pipeline aberto](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. No exemplo a seguir, você vê duas atividades no pipeline com suas entradas e saídas. A atividade denominada **JoinData**, do tipo atividade de Hive do HDInsight, e **EgressDataAzure**, do tipo atividade de cópia, estão neste pipeline de exemplo. 
   
    ![Atividades dentro de um pipeline](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png) 
3. Você pode navegar de volta para a home page da Data Factory clicando no link da Data factory trilha no canto superior esquerdo.
   
    ![Navegue de volta para a data factory](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-state-of-each-activity-inside-a-pipeline"></a>Exibir o estado de cada atividade dentro de um pipeline
Você pode exibir o estado atual de uma atividade exibindo o status de qualquer um dos conjuntos de dados produzidos pela atividade. 

Por exemplo: no exemplo a seguir, **BlobPartitionHiveActivity** foi executado com êxito e produziu um conjunto de dados chamado **PartitionedProductsUsageTable**, que está no estado **Pronto**.

![Estado do pipeline](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

Clicar duas vezes em **PartitionedProductsUsageTable** no diagrama exibirá todas as fatias produzidas por diferentes execuções de atividade dentro de um pipeline. Você pode ver que **BlobPartitionHiveActivity** foi executado com êxito todos os meses nos últimos oito meses e produziu as fatias com estado **Pronto**.

As fatias de conjunto de dados na data factory podem ter um dos seguintes status:

<table>
<tr>
    <th align="left">Estado</th><th align="left">Subestado</th><th align="left">Descrição</th>
</tr>
<tr>
    <td rowspan="8">Aguardando</td><td>ScheduleTime</td><td>Não é o momento de a fatia executar.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>As dependências de upstream não estão prontas.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Os recursos de computação não estão disponíveis.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Todas as instâncias de atividade estão ocupadas executando outras fatias.</td>
</tr>
<tr>
<td>ActivityResume</td><td>A atividade é pausada e não é possível executar as fatias até que seja retomada.</td>
</tr>
<tr>
<td>Retry</td><td>A execução da atividade é repetida.</td>
</tr>
<tr>
<td>Validação</td><td>A validação ainda não foi iniciada.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Aguardando a validação ser repetida.</td>
</tr>
<tr>
<tr
<td rowspan="2">InProgress</td><td>Validando</td><td>Validação em andamento.</td>
</tr>
<td>-</td>
<td>A fatia está sendo processada.</td>
</tr>
<tr>
<td rowspan="4">Falha</td><td>TimedOut</td><td>A execução demorou mais do que o permitido pela atividade.</td>
</tr>
<tr>
<td>Cancelado</td><td>Cancelado por ação do usuário.</td>
</tr>
<tr>
<td>Validação</td><td>A validação falhou.</td>
</tr>
<tr>
<td>-</td><td>Falha ao gerar e/ou validar a fatia.</td>
</tr>
<td>Ready</td><td>-</td><td>A fatia está pronta para consumo.</td>
</tr>
<tr>
<td>Ignorado</td><td>Nenhum</td><td>A fatia não é processada.</td>
</tr>
<tr>
<td>Nenhum</td><td>-</td><td>Uma fatia que costumava existir com um status diferente, mas foi redefinida.</td>
</tr>
</table>



Você pode exibir os detalhes de uma fatia clicando em uma entrada de fatia na folha **Fatias Atualizadas Recentemente** .

![Detalhes da fatia](./media/data-factory-monitor-manage-pipelines/slice-details.png)

Se a fatia tiver sido executada várias vezes, você verá várias linhas na lista **Execuções de atividade** . Você pode exibir detalhes sobre uma execução de atividade clicando na entrada da execução na lista **Execuções de atividade** . A lista exibirá todos os arquivos de log junto com uma mensagem de erro, se houver. Esse recurso é útil para exibir e depurar logs sem precisar sair de sua data factory.

![Detalhes da execução da atividade](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Quando a fatia não está no estado **Pronto**, você pode ver as fatias upstream que não estão Prontas e estão impedindo a execução da fatia atual na lista **Fatias upstream que não estão prontas**. Esse recurso é útil quando a fatia está no estado **Aguardando** e você quer entender as dependências de upstream em que a fatia está aguardando.

![As fatias upstream não estão prontas](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>Diagrama de estado do conjunto de dados
Quando você implanta uma data factory e os pipelines têm um período de atividade válido, as fatias do conjunto de dados fatias fazem a transição de um estado para outro. Atualmente, o status da fatia segue o seguinte diagrama de estado:

![Diagrama de estado](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

O fluxo de transição de estado do conjunto de dados na data factory: Waiting-> In-Progress/In-Progress (Validating) -> Ready/Failed

A fatias começam com um estado **Aguardando** para que as pré-condições sejam atendidas antes da execução. Depois disso, a atividade começa a ser executada e a fatia passa para o estado **Em Andamento** . A execução da atividade pode ser bem-sucedida ou falhar. A fatia é marcada como **Pronta** ou **Falha** com base no resultado da execução. 

Você pode redefinir a fatia para voltar do estado **Pronto** ou **Falha** para o estado **Aguardando**. Você também pode marcar o estado da fatia como **Ignorar**, o que impedirá a execução da atividade e não processará a fatia.

## <a name="manage-pipelines"></a>Gerenciar pipelines
Você pode gerenciar seus pipelines usando o Azure PowerShell. Por exemplo, você pode pausar e retomar pipelines executando cmdlets do Azure PowerShell. 

### <a name="pause-and-resume-pipelines"></a>Pausar e retomar pipelines
Você pode pausar/suspender pipelines usando o cmdlet **Suspend-AzureDataFactoryPipeline** do Powershell. Esse cmdlet é útil quando você não quiser executar o pipeline até que um problema seja corrigido.

Por exemplo: na captura de tela abaixo, foi identificado um problema com **PartitionProductsUsagePipeline** no data factory **productrecgamalbox1dev** e queremos suspender o pipeline.

![Pipeline a ser suspenso](./media/data-factory-monitor-manage-pipelines/pipeline-to-be-suspended.png)

Para suspender um pipeline, execute este comando do PowerShell:

```powershell
Suspend-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Por exemplo:

```powershell
Suspend-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline 
```

Depois que o problema com **PartitionProductsUsagePipeline**for corrigido, o pipeline suspenso poderá ser retomado executando este comando do PowerShell:

```powershell
Resume-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Por exemplo:

```powershell
Resume-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline 
```
## <a name="debug-pipelines"></a>Depurar pipelines
O Azure Data Factory fornece recursos avançados por meio do Portal do Azure e do Azure PowerShell para depurar e solucionar problemas com pipelines.

### <a name="find-errors-in-a-pipeline"></a>Localizar erros em um pipeline
Se a execução da atividade falhar em um pipeline, o conjunto de dados produzido pelo pipeline ficará em um estado de erro devido à falha. Você pode depurar e solucionar e erros no Azure Data Factory usando os seguintes mecanismos.

#### <a name="use-azure-portal-to-debug-an-error"></a>Use o Portal do Azure para depurar um erro:
1. Na folha **TABELA**, clique na fatia de problema com o **STATUS** definido como **Falha**.
   
   ![Folha de tabela com fatia com problema](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. Na folha **FATIA DE DADOS,** clique na execução de atividade com falha.
   
   ![Fatia de dados com erro](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. Na folha **DETALHES DE EXECUÇÃO DA ATIVIDADE** , você pode baixar os arquivos associados ao processamento do HDInsight. Clique em Download para que Status/stderr baixe o arquivo de log de erros que contém detalhes sobre o erro.
   
   ![Folha de detalhes da execução da atividade com erro](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-the-powershell-to-debug-an-error"></a>Usar o PowerShell para depurar um erro
1. Inicie o **PowerShell do Azure**.
2. Execute o comando **Get-AzureDataFactorySlice** para ver as fatias e seus status. Você deve ver uma fatia com o status: **Falha**.        

    ```powershell   
    Get-AzureRmDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   Por exemplo:

    ```powershell   
    Get-AzureRmDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```
   
   Substitua **StartDateTime** pelo valor StartDateTime especificado para Set-AzureRmDataFactoryPipelineActivePeriod.
3. Agora, execute o cmdlet **Get-AzureRmDataFactoryRun** para obter detalhes sobre a execução da atividade para a fatia.

    ```powershell   
    Get-AzureRmDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] 
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```
  
    Por exemplo:

    ```powershell   
    Get-AzureRmDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```
   
    O valor de StartDateTime é a hora de início do erro/fatia com problema observada na etapa anterior. A data e hora devem ser colocadas entre aspas duplas.
4. Você deve ver a saída com detalhes sobre o erro (semelhante ao seguinte):

    ```   
    Id                      : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
    ResourceGroupName       : ADF
    DataFactoryName         : LogProcessingFactory3
    DatasetName               : EnrichedGameEventsTable
    ProcessingStartTime     : 10/10/2014 3:04:52 AM
    ProcessingEndTime       : 10/10/2014 3:06:49 AM
    PercentComplete         : 0
    DataSliceStart          : 5/5/2014 12:00:00 AM
    DataSliceEnd            : 5/6/2014 12:00:00 AM
    Status                  : FailedExecution
    Timestamp               : 10/10/2014 3:04:52 AM
    RetryAttempt            : 0
    Properties              : {}
    ErrorMessage            : Pig script failed with exit code '5'. See wasb://        adfjobs@spestore.blob.core.windows.net/PigQuery
                                    Jobs/841b77c9-d56c-48d1-99a3-
                8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for
                more details.
    ActivityName            : PigEnrichLogs
    PipelineName            : EnrichGameLogsPipeline
    Type                    :
    ```
5. Você pode executar o cmdlet **Save-AzureRmDataFactoryLog** com o valor de Id que você vê na saída e baixar os arquivos de log usando a opção **-DownloadLogsoption** para o cmdlet.
   
    ```powershell
    Save-AzureRmDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>Executar novamente as falhas em um pipeline
### <a name="using-azure-portal"></a>Usando o Portal do Azure
Depois de solucionar problemas e depurar falhas em um pipeline, você pode executar as falhas novamente navegando até a fatia com erro e clicando no botão **Executar** na barra de comandos.

![Executar novamente uma fatia com falha](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

Em caso de falha na validação da fatia devido a uma falha de política (por exemplo, dados não disponíveis), é possível corrigir a falha e validar novamente clicando no botão **Validar** na barra de comandos.
![Corrigir os erros e validar](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="using-azure-powershell"></a>Usando o PowerShell do Azure
Você pode executar novamente as falhas usando o cmdlet Set-AzureRmDataFactorySliceStatus. Confira o tópico [Set-AzureRmDataFactorySliceStatus](https://msdn.microsoft.com/library/mt603522.aspx) para obter a sintaxe e outros detalhes sobre o cmdlet. 

**Exemplo:** o exemplo a seguir define o status de todas as fatias da tabela "DAWikiAggregatedData" como "Aguardando" no Azure Data Factory "WikiADF".

UpdateType é definido como UpstreamInPipeline, o que significa que o status de cada fatia da tabela e todas as tabelas dependentes (upstream) é definido como "Aguardando". Outro valor possível para esse parâmetro é "Individual".

```powershell
Set-AzureRmDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```

## <a name="create-alerts"></a>Criar alertas
O Azure registra eventos do usuário quando um recurso do Azure (por exemplo, Data Factory) é criado, atualizado ou excluído. Você pode criar alertas para esses eventos. O Data Factory permite que você capture várias métricas e crie alertas para as métricas. Recomendamos que você use os eventos para monitoramento em tempo real e métricas para fins de histórico. 

### <a name="alerts-on-events"></a>Alertas de eventos
Eventos do Azure fornecem percepções úteis sobre o que está acontecendo em seus recursos do Azure. O Azure registra eventos do usuário quando um recurso do Azure (por exemplo, Data Factory) é criado, atualizado ou excluído. Ao usar o Azure Data Factory, os eventos são gerados quando:

* O Azure Data Factory é criado/atualizado/excluído.
* O processamento de dados (chamado de Execuções) foi iniciado/concluído.
* Um cluster de HDInsight sob demanda é criado e removido.

Você pode criar alertas para esses eventos de usuário e configurá-los para enviar notificações por email para o administrador e os coadministradores da assinatura. Além disso, você pode especificar endereços de email adicionais de usuários que precisem receber notificações por email quando as condições forem atendidas. Esse recurso é útil quando você deseja ser notificado sobre falhas e não quer monitorar continuamente sua data factory.

> [!NOTE]
> Atualmente, o portal não mostra alertas em eventos. Use o [Aplicativo de Monitoramento e Gerenciamento](data-factory-monitor-manage-app.md) para ver todos os alertas.
> 
> 

#### <a name="specifying-an-alert-definition"></a>Especificando uma definição de alerta:
Para especificar uma definição de alerta, você deve criar um arquivo JSON que descreva as operações sobre as quais você deseja ser alertado. No exemplo a seguir, o alerta enviará uma notificação por email para a operação RunFinished. Para ser específico, uma notificação por email será enviado quando uma execução no Data Factory for concluída e essa execução falhar (Status = FailedExecution).

```JSON
{
    "contentVersion": "1.0.0.0",
     "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "parameters": {},
    "resources": 
    [
        {
            "name": "ADFAlertsSlice",
            "type": "microsoft.insights/alertrules",
            "apiVersion": "2014-04-01",
            "location": "East US",
            "properties": 
            {
                "name": "ADFAlertsSlice",
                "description": "One or more of the data slices for the Azure Data Factory has failed processing.",
                "isEnabled": true,
                "condition": 
                {
                    "odata.type": "Microsoft.Azure.Management.Insights.Models.ManagementEventRuleCondition",
                    "dataSource": 
                    {
                        "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleManagementEventDataSource",
                        "operationName": "RunFinished",
                        "status": "Failed",
                        "subStatus": "FailedExecution"   
                    }
                },
                "action": 
                {
                    "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
                    "customEmails": [ "<your alias>@contoso.com" ]
                }
            }
        }
    ]
}
```

Da definição do JSON acima, **subStatus** poderá ser removido se você não desejar ser alertado sobre uma falha específica.

Esse exemplo configura o alerta para todas as fábricas de dados em sua assinatura. Se você quiser que o alerta seja configurado para um data factory específico, será possível especificar o **resourceUri** do data factory no bloco **dataSource**:

```JSON
"resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>"
```

A tabela a seguir fornece a lista de operações e status (e substatus) disponíveis.

| Nome da operação | Status | Substatus |
| --- | --- | --- |
| RunStarted |Iniciado |Iniciando |
| RunFinished |Falhou / Bem-sucedido |FailedResourceAllocation<br/><br/>Bem-sucedido<br/><br/>FailedExecution<br/><br/>TimedOut<br/><br/><Cancelado<br/><br/>FailedValidation<br/><br/>Abandoned |
| OnDemandClusterCreateStarted |Iniciado | |
| OnDemandClusterCreateSuccessful |Bem-sucedido | |
| OnDemandClusterDeleted |Bem-sucedido | |

Veja [Criar Regra de Alerta](https://msdn.microsoft.com/library/azure/dn510366.aspx) para obter detalhes sobre os elementos JSON usados no exemplo. 

#### <a name="deploying-the-alert"></a>Implantando o Alerta
Para implantar o alerta, use o cmdlet do Azure PowerShell: **New-AzureRmResourceGroupDeployment**, conforme mostrado no exemplo a seguir:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\ADFAlertFailedSlice.json  
```

Depois que a implantação do grupo de recursos for concluída com êxito, você verá as seguintes mensagens:

```
VERBOSE: 7:00:48 PM - Template is valid.
WARNING: 7:00:48 PM - The StorageAccountName parameter is no longer used and will be removed in a future release.
Please update scripts to remove this parameter.
VERBOSE: 7:00:49 PM - Create template deployment 'ADFAlertFailedSlice'.
VERBOSE: 7:00:57 PM - Resource microsoft.insights/alertrules 'ADFAlertsSlice' provisioning status is succeeded

DeploymentName    : ADFAlertFailedSlice
ResourceGroupName : adf
ProvisioningState : Succeeded
Timestamp         : 10/11/2014 2:01:00 AM
Mode              : Incremental
TemplateLink      :
Parameters        :
Outputs           :
```

> [!NOTE]
> Você pode usar a API REST [Criar Regra de Alerta](https://msdn.microsoft.com/library/azure/dn510366.aspx) para criar uma regra de alerta. O conteúdo JSON é semelhante ao exemplo JSON.  
> 
> 

#### <a name="retrieving-the-list-of-azure-resource-group-deployments"></a>Recuperando a lista de implantações de grupo de recursos do Azure
Para recuperar a lista de implantações do Grupo de Recursos do Azure já implantadas, use o cmdlet **Get-AzureRmResourceGroupDeployment**, conforme mostrado no exemplo a seguir:

```powershell
Get-AzureRmResourceGroupDeployment -ResourceGroupName adf
```

```
DeploymentName    : ADFAlertFailedSlice
ResourceGroupName : adf
ProvisioningState : Succeeded
Timestamp         : 10/11/2014 2:01:00 AM
Mode              : Incremental
TemplateLink      :
Parameters        :
Outputs           :
```

#### <a name="troubleshooting-user-events"></a>Solucionando problemas de eventos de usuário
1. Você pode ver todos os eventos gerados depois de clicar no bloco **Métricas e operações**.
   
    ![Bloco Métricas e operações](./media/data-factory-monitor-manage-pipelines/metrics-and-operations-tile.png)
2. Clique no bloco **Eventos** para ver os eventos. 
   
    ![Bloco de eventos](./media/data-factory-monitor-manage-pipelines/events-tile.png)
3. Na folha **Eventos**, você pode ver detalhes sobre eventos, filtrar eventos e assim por diante. 
   
    ![Folha Eventos](./media/data-factory-monitor-manage-pipelines/events-blade.png)
4. Clique em uma **operação** na lista de operações que causa um erro.
   
    ![Selecionar uma operação](./media/data-factory-monitor-manage-pipelines/select-operation.png) 
5. Clique em um evento de **erro** para ver os detalhes sobre o erro.
   
    ![Erro de evento](./media/data-factory-monitor-manage-pipelines/operation-error-event.png)

Veja o artigo [Cmdlets do Azure Insight](https://msdn.microsoft.com/library/mt282452.aspx) sobre os cmdlets do PowerShell que você pode usar para adicionar/obter/remover alertas. Aqui estão alguns exemplos de como usar o cmdlet **Get-AlertRule** : 

```powershell
get-alertrule -res $resourceGroup -n ADFAlertsSlice -det
```

```
Properties :
Action      : Microsoft.Azure.Management.Insights.Models.RuleEmailAction
Condition   :
DataSource :
EventName             :
Category              :
Level                 :
OperationName         : RunFinished
ResourceGroupName     :
ResourceProviderName  :
ResourceId            :
Status                : Failed
SubStatus             : FailedExecution
Claims                : Microsoft.Azure.Management.Insights.Models.RuleManagementEventClaimsDataSource
Condition      :
Description : One or more of the data slices for the Azure Data Factory has failed processing.
Status      : Enabled
Name:       : ADFAlertsSlice
Tags       :
$type          : Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/ADFAlertsSlice
Location   : West US
Name       : ADFAlertsSlice
```

```powershell
Get-AlertRule -res $resourceGroup
```
```
Properties : Microsoft.Azure.Management.Insights.Models.Rule
Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
Location   : West US
Name       : FailedExecutionRunsWest0

Properties : Microsoft.Azure.Management.Insights.Models.Rule
Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest3
Location   : West US
Name       : FailedExecutionRunsWest3
```

```powershell
Get-AlertRule -res $resourceGroup -Name FailedExecutionRunsWest0
```

```
Properties : Microsoft.Azure.Management.Insights.Models.Rule
Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
Location   : West US
Name       : FailedExecutionRunsWest0
```

Execute os seguintes comandos get-help para ver detalhes e exemplos para o cmdlet Get-AlertRule. 

```powershell
get-help Get-AlertRule -detailed
```

```powershell 
get-help Get-AlertRule -examples
```


* Se você vir os eventos de geração de alerta na folha do portal, mas não receber notificações por email, verifique se o endereço de email especificado está configurado para receber emails de remetentes externos. Os emails de alerta podem ter sido bloqueados por suas configurações de email.

### <a name="alerts-on-metrics"></a>Alertas de métricas
O Data Factory permite que você capture várias métricas e crie alertas para as métricas. Você pode monitorar e criar alertas nas seguintes métricas para as fatias em sua data factory.

* Execuções com falha
* Execuções com êxito

Essas métricas são úteis e permitem que os usuários tenham uma visão geral das execuções com falha e êxito em sua fábrica de dados. Métricas são emitidas sempre que há uma fatia de execução. Ao final de uma hora, essas métricas são agregadas e enviadas para sua conta de armazenamento. Portanto, para habilitar as métricas, configure uma conta de armazenamento.

#### <a name="enabling-metrics"></a>Habilitando métricas:
Para habilitar as métricas, clique no seguinte na folha do Data Factory:

**Monitoramento** -> **Métrica** -> **Configurações de diagnóstico** -> **Diagnóstico**

![Link de diagnóstico](./media/data-factory-monitor-manage-pipelines/diagnostics-link.png)

Na folha **Diagnóstico**, clique em **Ativar**, selecione a conta de armazenamento e salve.

![Folha de diagnósticos](./media/data-factory-monitor-manage-pipelines/diagnostics-blade.png)

Uma vez salvas, pode levar uma hora para que as métricas fiquem visíveis na folha de monitoramento, porque a agregação de métricas acontece de hora em hora.

### <a name="setting-up-alert-on-metrics"></a>Configurando alertas no Metrics:
Clique na folha **Métricas de Data Factory**: 

![Bloco Métricas de data factory](./media/data-factory-monitor-manage-pipelines/data-factory-metrics-tile.png)

Na folha **Métrica**, clique em **+ Adicionar alerta** na barra de ferramentas. 
![Folha Métricas de data factory - adicionar alerta](./media/data-factory-monitor-manage-pipelines/add-alert.png)

Na página **Adicionar uma regra de alerta**, siga as etapas a seguir e clique em **OK**.

* Insira um nome para o alerta (exemplo: alerta de falha).
* Insira uma descrição do alerta (exemplo: enviar um email quando ocorre uma falha).
* Selecione uma métrica (execuções com falha versus execuções com êxito).
* Especifique uma condição e um valor de limite.   
* Especifique o período. 
* Especifique se um email deve ser enviado para proprietários, colaboradores e leitores.
* e mais. 

![Folha Métrica do data factory - adicionar alerta](./media/data-factory-monitor-manage-pipelines/add-an-alert-rule.png)

Depois que a regra de alerta for adicionada com êxito, a folha será fechada e você verá o novo alerta na página **Métrica**. 

![Folha Métrica do data factory - adicionar alerta](./media/data-factory-monitor-manage-pipelines/failed-alert-in-metric-blade.png)

Você também deve ver o número de alertas no bloco **Alertas**. Clique no bloco **Alertas**.

![Folha Métrica de data factory - Regras de alerta](./media/data-factory-monitor-manage-pipelines/alert-rules-tile-rules.png)

Na folha **Alertas**, você vê todos os alertas existentes. Para adicionar um alerta, clique em **Adicionar alerta** na barra de ferramentas.

![Folha Regras de alerta](./media/data-factory-monitor-manage-pipelines/alert-rules-blade.png)

### <a name="alert-notifications"></a>Notificações de alerta:
Quando a regra de alerta corresponder à condição, você deverá receber um email de alerta ativo. Depois que o problema for resolvido e a condição de alerta deixar de corresponder, você receberá um email de alerta resolvido.

Esse comportamento é diferente dos eventos, em que uma notificação será enviada em cada caso de falha para o qual a regra de alerta de qualificar.

### <a name="deploying-alerts-using-powershell"></a>Implantando alertas usando o PowerShell
Você pode implantar alertas para métricas da mesma maneira como faz para eventos. 

**Definição do alerta:**

```JSON
{
    "contentVersion" : "1.0.0.0",
    "$schema" : "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "parameters" : {},
    "resources" : [
    {
            "name" : "FailedRunsGreaterThan5",
            "type" : "microsoft.insights/alertrules",
            "apiVersion" : "2014-04-01",
            "location" : "East US",
            "properties" : {
                "name" : "FailedRunsGreaterThan5",
                "description" : "Failed Runs greater than 5",
                "isEnabled" : true,
                "condition" : {
                    "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
                    "odata.type" : "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
                    "dataSource" : {
                        "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
                        "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
                        "resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName
>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>",
                        "metricName" : "FailedRuns"
                    },
                    "threshold" : 5.0,
                    "windowSize" : "PT3H",
                    "timeAggregation" : "Total"
                },
                "action" : {
                    "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
                    "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
                    "customEmails" : ["abhinav.gpt@live.com"]
                }
            }
        }
    ]
}
```

Substitua subscriptionId, resourceGroupName e dataFactoryName no exemplo pelos valores adequados.

*metricName* a partir de agora dá suporte a dois valores:

* FailedRuns
* SuccessfulRuns

**Implantando o alerta:**

Para implantar o alerta, use o cmdlet do Azure PowerShell: **New-AzureRmResourceGroupDeployment**, conforme mostrado no exemplo a seguir:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\FailedRunsGreaterThan5.json
```

Você verá a seguinte mensagem após a implantação bem-sucedida:

```
VERBOSE: 12:52:47 PM - Template is valid.
VERBOSE: 12:52:48 PM - Create template deployment 'FailedRunsGreaterThan5'.
VERBOSE: 12:52:55 PM - Resource microsoft.insights/alertrules 'FailedRunsGreaterThan5' provisioning status is succeeded


DeploymentName    : FailedRunsGreaterThan5
ResourceGroupName : adf
ProvisioningState : Succeeded
Timestamp         : 7/27/2015 7:52:56 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
Outputs           
```

Você também usar o cmdlet **Add-AlertRule** para implantar uma regra de alerta. Consulte o tópico sobre [Add-AlertRule](https://msdn.microsoft.com/library/mt282468.aspx) para obter detalhes e exemplos.  

## <a name="move-data-factory-to-a-different-resource-group-or-subscription"></a>Mover o data factory para outro grupo de recursos ou assinatura
Você pode mover um data factory para outro grupo de recursos ou assinatura diferente usando o botão **Mover** da barra de comandos na home page do seu data factory. 

![Mover o Data Factory](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

Você também pode mover todos os recursos relacionados (como alertas associados ao data factory) juntamente com ele.

![Mover a caixa de diálogo Recursos](./media/data-factory-monitor-manage-pipelines/MoveResources.png)



<!--HONumber=Nov16_HO3-->


