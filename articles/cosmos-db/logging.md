---
title: "Log de diagnósticos do Azure Cosmos DB | Microsoft Docs"
description: "Use este tutorial para ajudá-lo a começar a usar o log do Azure Cosmos DB."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
tags: azure-resource-manager
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.author: mimig
ms.openlocfilehash: 835f6ffce9b2e1bb4b6cfd7476bb3fdb24a4f092
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2018
---
# <a name="azure-cosmos-db-diagnostic-logging"></a>Log de diagnósticos do Azure Cosmos DB

Depois de começar a usar um ou mais bancos de dados do Azure Cosmos DB, talvez você deseje monitorar como e quando os bancos de dados são acessados. O log de diagnósticos no Azure Cosmos DB permite que realizar esse monitoramento. Ao habilitar o log de diagnósticos, você pode enviar logs para o [Armazenamento do Azure](https://azure.microsoft.com/services/storage/), transmiti-los para os [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/) e/ou exportá-los o [Log Analytics](https://azure.microsoft.com/services/log-analytics/) que faz parte do [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite).

![Log de diagnósticos para o Armazenamento, Hubs de Eventos ou Operations Management Suite por meio do Log Analytics](./media/logging/azure-cosmos-db-logging-overview.png)

Use este tutorial para começar a usar o log do Azure Cosmos DB por meio do portal do Azure, da CLI ou do PowerShell.

## <a name="what-is-logged"></a>O que é registrado?

* Todas as solicitações de API SQL REST autenticadas são registradas, o que inclui as solicitações que falharam devido a permissões de acesso, erros do sistema ou solicitações inválidas. O suporte para APIs do MongoDB, do Graph e de Tabela não está disponível no momento.
* Operações no próprio banco de dados, que inclui operações CRUD em todos os documentos, contêineres e bancos de dados.
* Operações em chaves de conta, que incluem a criação, modificação ou exclusão dessas chaves.
* Solicitações não autenticadas que resultam em uma resposta 401. Por exemplo, solicitações que não têm um token de portador, estão malformadas ou expiradas ou têm um token inválido.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, você deve ter os seguintes recursos:

* Uma conta existente, banco de dados e contêiner do Azure Cosmos DB. Para obter instruções sobre como criar esses recursos, consulte [Criar uma conta de banco de dados usando o portal do Azure](create-sql-api-dotnet.md#create-a-database-account), [Amostras da CLI](cli-samples.md) ou [Amostras do PowerShell](powershell-samples.md).

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>Ativar o log no portal do Azure

1. No [portal do Azure](https://portal.azure.com), em sua conta do Azure Cosmos DB, clique em **Logs de diagnóstico** na barra navegação à esquerda e, em seguida, clique **Ativar diagnóstico**.

    ![Ativar o log de diagnósticos do Azure Cosmos DB no portal do Azure](./media/logging/turn-on-portal-logging.png)

2. Na página **Configurações de diagnóstico**, faça o seguinte: 

    * **Nome**. Insira um nome para os logs a serem criados.

    * **Arquivar em uma conta de armazenamento**. Para usar essa opção, você precisa de uma conta de armazenamento existente à qual se conectar. Para criar uma nova conta de armazenamento no portal, consulte [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md) e siga as instruções para criar uma conta de uso geral do Resource Manager. Em seguida, retorne a esta página no portal para selecionar sua conta de armazenamento. Pode levar alguns minutos para que as contas de armazenamento recém-criadas sejam exibidas no menu suspenso.
    * **Transmitir para um hub de eventos**. Para usar essa opção, é necessário ter um namespace existente do Hub de Eventos e um hub de evento ao qual se conectar. Para criar um namespace do Hubs de Eventos, consulte [Criar um namespace dos Hubs de Eventos e um hub de eventos usando o portal do Azure](../event-hubs/event-hubs-create.md). Em seguida, retorne a esta página no portal para selecionar o namespace e o nome da política do Hub de Eventos.
    * **Enviar para o Log Analytics**.     Para usar essa opção, use um espaço de trabalho existente ou crie um novo espaço de trabalho do Log Analytics seguindo as etapas para [criar um novo espaço de trabalho](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace) no portal. Para obter mais informações sobre como exibir os logs no Log Analytics, consulte [Exibir logs no Log Analytics](#view-in-loganalytics).
    * **Registrar DataPlaneRequests**. Selecione esta opção para registrar o log de diagnóstico para contas do SQL, Graph e API de Tabela. Se você estiver arquivando em uma conta de armazenamento, poderá selecionar o período de retenção para os logs de diagnóstico. Os logs são excluídos automaticamente depois que o período de retenção expira.
    * **Registrar em Log MongoRequests**. Selecione essa opção para registrar em log diagnóstico para contas de API do MongoDB. Se você estiver arquivando em uma conta de armazenamento, poderá selecionar o período de retenção para os logs de diagnóstico. Os logs são excluídos automaticamente depois que o período de retenção expira.
    * **Solicitações de Métricas**. Selecione esta opção para armazenar dados detalhados em [Métricas do Azure](../monitoring-and-diagnostics/monitoring-supported-metrics.md). Se você estiver arquivando em uma conta de armazenamento, poderá selecionar o período de retenção para os logs de diagnóstico. Os logs são excluídos automaticamente depois que o período de retenção expira.

3. Clique em **Salvar**.

    Se você receber um erro que diga "Falha ao atualizar o diagnóstico para \<nome do espaço de trabalho>. A assinatura \<id da assinatura> não está registrada para usar o microsoft.insights." siga as instruções para [Solucionar Problemas de Diagnóstico do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) para registrar a conta, então repita este procedimento.

    Se você quiser alterar como os seus logs de diagnóstico são salvos em qualquer ponto no futuro, poderá retornar a esta página a qualquer momento para modificar as configurações de log de diagnóstico para sua conta.

## <a name="turn-on-logging-using-cli"></a>Ativar o log usando a CLI

Para habilitar as métricas e o log de diagnóstico usando o CLI do Azure, use os seguintes comandos:

- Para habilitar o armazenamento dos Logs de Diagnóstico em uma Conta de Armazenamento, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   A `resourceId` é o nome da conta do Azure Cosmos DB. A `storageId` é o nome da conta de armazenamento para a qual você deseja enviar os logs.

- Para habilitar o streaming dos Logs de Diagnóstico para um Hub de Eventos, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   A `resourceId` é o nome da conta do Azure Cosmos DB. A `serviceBusRuleId` é uma cadeia de caracteres com este formato:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Para habilitar o envio dos Logs de Diagnóstico para um espaço de trabalho do Log Analytics, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Você pode combinar esses parâmetros para permitir várias opções de saída.

## <a name="turn-on-logging-using-powershell"></a>Ativar o log usando o PowerShell

Para ativar o log usando o PowerShell, você precisa do Azure PowerShell, com uma versão mínima de 1.0.1.

Para instalar o Azure PowerShell e associá-lo à sua assinatura do Azure, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

Se já tiver instalado o Azure PowerShell e não souber a versão, no console do PowerShell, digite `(Get-Module azure -ListAvailable).Version`.  

### <a id="connect"></a>Conectar-se a suas assinaturas
Inicie uma sessão do PowerShell do Azure e entre em sua conta do Azure com o seguinte comando:  

```powershell
Login-AzureRmAccount
```

Na janela pop-up do navegador, insira o nome de usuário e a senha da sua conta do Azure. O Azure PowerShell obtém todas as assinaturas que estão associadas a essa conta e, por padrão, usa a primeira.

Se você tiver várias assinaturas, talvez tenha que indicar uma assinatura específica que tenha sido usada para criar o Cofre de Chaves do Azure. Digite o seguinte para ver as assinaturas da sua conta:

```powershell
Get-AzureRmSubscription
```

Em seguida, para especificar a assinatura associada à conta do Azure Cosmos DB que está sendo registrada, digite:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Caso tenha várias assinaturas associadas à sua conta, é importante especificar a assinatura.
>
>

Para saber mais sobre a configuração PowerShell do Azure, consulte [Como instalar e configurar o PowerShell do Azure](/powershell/azure/overview).

### <a id="storage"></a>Criar uma nova conta de armazenamento para seus logs
Embora você possa usar uma conta de armazenamento existente para os logs, neste tutorial, criamos uma nova conta de armazenamento dedicada aos logs do Azure Cosmos DB. Para conveniência, estamos armazenando os detalhes da conta de armazenamento em uma variável chamada **sa**.

Para facilidade de gerenciamento adicional, neste tutorial, usamos o mesmo grupo de recursos que contém nosso banco de dados do Azure Cosmos DB. Substitua os valores de ContosoResourceGroup, contosocosmosdblogs e “Centro-Norte dos EUA” por seus próprios valores, conforme aplicável:

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> Se você decidir usar uma conta de armazenamento existente, ela deverá usar a mesma assinatura da assinatura do Azure Cosmos DB e deverá usar o modelo de implantação do Resource Manager, em vez do modelo de implantação Clássico.
>
>

### <a id="identify"></a>Identificar a conta do Azure Cosmos DB para os logs
Defina o nome da conta do Azure Cosmos DB como uma variável chamada **account**, em que ResourceName é o nome da conta do Azure Cosmos DB.

```powershell
$account = Get-AzureRmResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>Habilitar o registro em log
Para habilitar o log no Azure Cosmos DB, use o cmdlet Set-AzureRmDiagnosticSetting, junto com as variáveis da nova conta de armazenamento, a conta do Azure Cosmos DB e a categoria da qual você deseja habilitar os logs. Execute o seguinte comando, definindo o sinalizador **-Enabled** como **$true**:

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

O resultado do comando deve ser semelhante ao seguinte:

```powershell
    StorageAccountId            : /subscriptions/<subscription-ID>/resourceGroups/ContosoResourceGroup/providers`
    /Microsoft.Storage/storageAccounts/contosocosmosdblogs
    ServiceBusRuleId            :
    EventHubAuthorizationRuleId :
    Metrics
        TimeGrain       : PT1M
        Enabled         : False
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    Logs
        Category        : DataPlaneRequests
        Enabled         : True
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    WorkspaceId                 :
    Id                          : /subscriptions/<subscription-ID>/resourcegroups/ContosoResourceGroup/providers`
    /microsoft.documentdb/databaseaccounts/contosocosmosdb/providers/microsoft.insights/diagnosticSettings/service
    Name                        : service
    Type                        :
    Location                    :
    Tags                        :
```

Isso confirma que o log agora está habilitado para o banco de dados, salvando as informações em sua conta de armazenamento.

Opcionalmente, você também pode definir a política de retenção para os logs, de modo que os logs mais antigos sejam automaticamente excluídos. Por exemplo, defina a política de retenção usando o sinalizador **-RetentionEnabled** como **$true** e defina o parâmetro **-RetentionInDays** como **90** para que os logs de mais de 90 dias sejam automaticamente excluídos.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>Acessar seus logs
Os logs do Azure Cosmos DB da categoria **DataPlaneRequests** são armazenados no contêiner **insights-logs-data-plane-requests**, na conta de armazenamento fornecida. 

Primeiro, crie uma variável para o nome do contêiner. Isso será usado no restante do passo a passo.

```powershell
    $container = 'insights-logs-dataplanerequests'
```

Para listar todos os blobs desse contêiner, digite:

```powershell
Get-AzureStorageBlob -Container $container -Context $sa.Context
```

A saída será parecida com esta:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 10510193
ContentType       : application/octet-stream
LastModified      : 9/28/2017 7:49:04 PM +00:00
SnapshotTime      :
ContinuationToken:
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.`
                    LazyAzureStorageContext
Name              : resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS`
/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/CONTOSOCOSMOSDB/y=2017/m=09/d=28/h=19/m=00/PT1H.json
```

Como você pode ver neste resultado, os blobs seguem uma convenção de nomenclatura: `resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Os valores de data e hora usam UTC.

Como a mesma conta de armazenamento pode ser usada para coletar logs de vários recursos, a ID totalmente qualificada do recurso no nome do blob é muito útil para acessar ou baixar apenas os blobs de que você precisa. Mas, antes de fazer isso, primeiro vamos mostrar como baixar todos os blobs.

Primeiro, crie uma pasta para baixar os blobs. Por exemplo: 

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

Em seguida, obtenha uma lista de todos os blobs:  

```powershell
$blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context
```

Redirecione essa lista pelo 'Get-AzureStorageBlobContent' para baixar os blobs em nossa pasta de destino:

```powershell
$blobs | Get-AzureStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

Quando você executa esse segundo comando, o delimitador **/** nos nomes de blob cria uma estrutura de pastas completa na pasta de destino. Essa estrutura de pastas é usada para baixar e armazenar os blobs como arquivos.

Use caracteres curinga para baixar seletivamente os blobs. Por exemplo: 

* Caso você tenha vários bancos de dados e deseje baixar apenas os logs de um banco de dados, chamado CONTOSOCOSMOSDB3:

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* Se você tiver vários grupos de recursos e quiser baixar os logs para apenas um grupo de recursos, use `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

    ```powershell
    Get-AzureStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* Se desejar baixar todos os logs do mês de julho de 2017, use `-Blob '*/year=2017/m=07/*'`:

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

Além disso:

* Para consultar o status das configurações de diagnóstico do recurso de banco de dados: `Get-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
* Para desabilitar o log da categoria **DataPlaneRequests** do recurso de conta do banco de dados: `Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`


Os blobs que são retornados em cada uma dessas consultas são armazenados como texto, formatados como um blob JSON, conforme mostrado no código a seguir. 

```json
{
    "records":
    [
        {
           "time": "Fri, 23 Jun 2017 19:29:50.266 GMT",
           "resourceId": "contosocosmosdb",
           "category": "DataPlaneRequests",
           "operationName": "Query",
           "resourceType": "Database",
           "properties": {"activityId": "05fcf607-6f64-48fe-81a5-f13ac13dd1eb",`
           "userAgent": "documentdb-dotnet-sdk/1.12.0 Host/64-bit MicrosoftWindowsNT/6.2.9200.0 AzureSearchIndexer/1.0.0",`
           "resourceType": "Database","statusCode": "200","documentResourceId": "",`
           "clientIpAddress": "13.92.241.0","requestCharge": "2.260","collectionRid": "",`
           "duration": "9250","requestLength": "72","responseLength": "209", "resourceTokenUserRid": ""}
        }
    ]
}
```

Para saber mais sobre os dados em cada blob JSON, consulte [Interpretar seus logs do Azure Cosmos DB](#interpret).

## <a name="managing-your-logs"></a>Gerenciando os logs

Os logs são disponibilizados em sua conta duas horas após a hora em que a operação do Azure Cosmos DB foi feita. Cabe a você gerenciar os logs em sua conta de armazenamento:

* Use os métodos de controle de acesso padrão do Azure para proteger seus logs ao restringir quem pode acessá-los.
* Exclua os logs que você não deseja manter em sua conta de armazenamento.
* O período de retenção para solicitações de plano de dados arquivadas em uma conta de Armazenamento é configurado no portal quando a opção **Registrar DataPlaneRequests** é selecionada. Para alterar essa configuração, consulte [Ativar o log no portal do Azure](#turn-on-logging-in-the-azure-portal).


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-log-analytics"></a>Exibir logs no Log Analytics

Se você tiver selecionado a opção **Enviar para Log Analytics** ao ativar o registro em log, dados da sua coleção serão encaminhados para o Log Analytics dentro de duas horas. Isso significa que, se você observar o Log Analytics imediatamente depois de ativar o registro em log, não verá todos os dados. Apenas aguarde duas horas e tente novamente. 

Antes de exibir os logs, verifique e veja se o espaço de trabalho do Log Analytics foi atualizado para usar a nova linguagem de consulta do Log Analytics. Para verificar isso, abra o [Portal do Azure](https://portal.azure.com), clique em **Log Analytics** no lado esquerdo e, em seguida, selecione o nome do espaço de trabalho conforme mostrado na imagem a seguir. A página **Espaço de trabalho do OMS** é exibida conforme mostrado na imagem a seguir.

![Log Analytics no portal do Azure](./media/logging/azure-portal.png)

Se você vir a seguinte mensagem na página **Espaço de trabalho do OMS**, isso significa que não foi feito o upgrade do seu espaço para usar a nova linguagem. Para obter mais informações sobre como fazer o upgrade para a nova linguagem de consulta, confira [Atualizar o seu espaço de trabalho do Azure Log Analytics para nova pesquisa de logs](../log-analytics/log-analytics-log-search-upgrade.md). 

![Notificação de atualização do Log Analytics](./media/logging/upgrade-notification.png)

Para exibir seus dados de diagnóstico no Log Analytics, abra a página de Pesquisa de Logs do menu à esquerda ou da área de Gerenciamento da página, conforme mostrado na imagem a seguir.

![Opções de pesquisa de logs no portal do Azure](./media/logging/log-analytics-open-log-search.png)

Agora que você habilitou a coleta de dados, execute o seguinte exemplo de pesquisa de logs usando a nova linguagem de consulta para ver os 10 logs mais recentes `AzureDiagnostics | take 10`.

![Exemplo de 10 pesquisas de logs](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="queries"></a>Consultas

Aqui estão algumas consultas adicionais que você pode inserir na caixa **Pesquisa de logs** para ajudá-lo a monitorar seus contêineres do Azure Cosmos DB. Essas consultas funcionam com a [nova linguagem](../log-analytics/log-analytics-log-search-upgrade.md). 

Para saber mais sobre o significado dos dados retornados pelas pesquisas de logs, consulte [Interpretar seus logs do Azure Cosmos DB](#interpret).

* Todos os logs de diagnóstico do Azure Cosmos DB para o período especificado.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* Dez eventos registrados em log mais recentemente.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* Todas as operações, agrupadas por tipo de operação.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* Todas as operações, agrupadas por Recurso.

    ```
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* Toda a atividade do usuário, agrupada por recurso. Observe que se trata de um log de atividades, não de um log de diagnóstico.

    ```
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* Quais operações demoram mais de 3 milissegundos.

    ```
    AzureDiagnostics | where toint(duration_s) > 30000 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Qual agente está executando as operações.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* Quando foram executadas as operações de longa duração.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , toint(duration_s)/1000 | render timechart
    ```

Para obter informações adicionais sobre como usar a nova linguagem de Pesquisa de Logs, consulte [Entender pesquisa de log no Log Analytics](../log-analytics/log-analytics-log-search-new.md). 

## <a id="interpret"></a>Interpretar seus logs

Dados de diagnóstico armazenados no Armazenamento do Azure e Log Analytics usam um esquema muito semelhante. 

A tabela a seguir descreve o conteúdo de cada entrada de log.

| Propriedade ou campo de Armazenamento do Azure | Propriedade do Log Analytics | DESCRIÇÃO |
| --- | --- | --- |
| tempo real | TimeGenerated | A data e hora (UTC) em que a operação ocorreu. |
| ResourceId | Recurso | A conta do Azure Cosmos DB na qual os logs estão habilitados.|
| categoria | Categoria | Para os logs do Azure Cosmos DB, DataPlaneRequests é o único valor disponível. |
| operationName | OperationName | Nome da operação. Esse valor pode ser uma das seguintes operações: Create, Update, Read, ReadFeed, Delete, Replace, Execute, SqlQuery, Query, JSQuery, Head, HeadFeed ou Upsert.   |
| propriedades | n/d | O conteúdo desse campo é descrito nas linhas a seguir. |
| activityId | activityId_g | O GUID exclusivo da operação registrada. |
| userAgent | userAgent_s | Uma cadeia de caracteres que especifica o agente do usuário cliente que executa a solicitação. O formato é {nome do agente do usuário}/{versão}.|
| resourceType | ResourceType | O tipo do recurso acessado. Esse valor pode ser um dos seguintes tipos: Database, Collection, Document, Attachment, User, Permission, StoredProcedure, Trigger, UserDefinedFunction ou Offer. |
| statusCode |statusCode_s | O status da resposta da operação. |
| requestResourceId | ResourceId | A resourceId pertencente à solicitação pode apontar para databaseRid, collectionRid ou documentRid, dependendo da operação executada.|
| clientIpAddress | clientIpAddress_s | Endereço IP do cliente. |
| requestCharge | requestCharge_s | O número de RUs usadas pela operação |
| collectionRid | collectionId_s | A ID exclusiva da coleção.|
| duration | duration_s | A duração da operação, em tiques. |
| requestLength | requestLength_s | O tamanho da solicitação, em bytes. |
| responseLength | responseLength_s | O tamanho da resposta, em bytes.|
| resourceTokenUserRid | resourceTokenUserRid_s | Isso não é vazio quando [tokens de recurso](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) são usados para autenticação e apontam para a ID de recurso do usuário. |

## <a name="next-steps"></a>Próximas etapas

- Para entender não apenas como habilitar o log, mas também as métricas e as categorias de log com suporte nos vários serviços do Azure, leia os artigos [Visão geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) e [Visão geral dos Logs de Diagnóstico do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).
- Leia estes artigos para saber mais sobre os hubs de eventos:
   - [O que são Hubs de Eventos do Azure?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Introdução aos Hubs de Evento](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Leia [Baixar métricas e logs de diagnósticos do Armazenamento do Azure](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs)
- Leia [Entender pesquisas de log no Log Analytics](../log-analytics/log-analytics-log-search-new.md)
