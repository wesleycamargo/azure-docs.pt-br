---
title: "Exibindo logs de diagnóstico para o Azure Data Lake Analytics | Microsoft Docs"
description: "Entenda como configurar e acessar os logs de diagnóstico do Azure Data Lake Analytics  "
services: data-lake-analytics
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: cf5633d4-bc43-444e-90fc-f90fbd0b7935
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/01/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 73d3e5577d0702a93b7f4edf3bf4e29f55a053ed
ms.openlocfilehash: 599b0bdaef2d0189bbe03153ff82489fbd32dd1b


---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Acessando os logs de diagnóstico do Azure Data Lake Analytics
Saiba mais sobre como habilitar o log de diagnóstico em sua conta do Data Lake Analytics e como exibir os logs coletados em sua conta.

As organizações podem habilitar o log de diagnóstico para sua conta do Azure Data Lake Analytics coletar trilhas de auditoria de acesso de dados. Esses logs fornecem informações como:

* Uma lista de usuários que acessaram os dados.
* Com que frequência os dados são acessados.
* Quantos dados são armazenados na conta.

## <a name="prerequisites"></a>Pré-requisitos
* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Habilitar sua assinatura do Azure** para a visualização pública do Data Lake Analytics. Veja [instruções](data-lake-analytics-get-started-portal.md).
* **Conta do Azure Data Lake Analytics**. Siga as instruções em [Introdução ao Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-get-started-portal.md).

## <a name="enable-logging"></a>Habilitar o registro em log
1. Inscreva-se no novo [portal do Azure](https://portal.azure.com).
2. Abra sua conta do Data Lake Analytics e, da folha da conta do Data Lake Analytics, clique em **Configurações** e, em seguida, em **Configurações de Diagnóstico**.
3. Na folha **Diagnóstico** , faça as seguintes alterações para configurar o log de diagnóstico.

    ![Habilitar registro em log de diagnóstico](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "Enable diagnostic logs")

   * Defina **Status** para **Ativado** para habilitar o log de diagnóstico.
   * Você pode optar por armazenar/processar os dados de duas maneiras diferentes.
     * Selecione **Exportar para o Hub de Eventos** para transmitir os dados de log para um Hub de Eventos do Azure. Use esta opção se tiver um pipeline de processamento de downstream para analisar os logs de entrada em tempo real. Se escolher esta opção, você deverá fornecer os detalhes no Hub de Eventos do Azure que deseja usar.
     * Selecione **Exportar para a Conta de Armazenamento** para armazenar os logs em uma conta de armazenamento do Azure. Use esta opção se quiser arquivar os dados. Se escolher esta opção, você deverá fornecer uma conta de armazenamento do Azure para salvar os logs.
   * Especifique se deseja obter os logs de auditoria, os logs de solicitação ou ambos.
   * Especifique o número de dias que os dados devem ser mantidos.
   * Clique em **Salvar**.

Depois de habilitar as configurações de diagnóstico, você poderá observar os logs na guia **Logs de Diagnóstico** .

## <a name="view-logs"></a>Exibir logs
Há duas maneiras de exibir os dados do log da sua conta no Data Lake Analytics.

* Das configurações da conta do Data Lake Analytics
* Na conta de Armazenamento do Azure onde os dados são armazenados

### <a name="using-the-data-lake-analytics-settings-view"></a>Usando a exibição de configurações do Data Lake Analytics
1. Na folha **Configurações** da sua conta do Data Lake Analytics, clique em **Logs de Diagnóstico**.

    ![Exibir o log de diagnósticos](./media/data-lake-analytics-diagnostic-logs/view-diagnostic-logs.png "View diagnostic logs")
2. Na folha **Logs de Diagnóstico**, você deve ver os logs categorizados por **Logs de Auditoria** e **Logs de Solicitação**.

   * Os Logs de Solicitação capturam todas as solicitações de API feitas na conta do Data Lake Analytics.
   * Os Logs de Auditoria são semelhantes aos Logs de Solicitação, mas fornecem uma análise mais detalhada das operações que estão sendo executadas na conta do Data Lake Analytics. Por exemplo, uma única chamada à API de upload nos logs de solicitação poderá resultar em várias operações do tipo "Anexar" nos logs de auditoria.
3. Clique no link **Download** em cada entrada de log para baixar os logs.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>Na conta de Armazenamento do Azure que contém dados de log
1. Abra a folha Conta de armazenamento do Azure associada ao Data Lake Analytics para registro em log e clique em Blobs. A folha **serviço Blob** lista dois contêineres.

    ![Exibir o log de diagnósticos](./media/data-lake-analytics-diagnostic-logs/view-diagnostic-logs-storage-account.png "View diagnostic logs")

   * O contêiner **insights-logs-audit** contém os logs de auditoria.
   * O contêiner **insights-logs-requests** contém os logs de solicitação.
2. Dentro desses contêineres, os logs são armazenados na estrutura a seguir.

        resourceId=/
          SUBSCRIPTIONS/
            <<SUBSCRIPTION_ID>>/
              RESOURCEGROUPS/
                <<RESOURCE_GRP_NAME>>/
                  PROVIDERS/
                    MICROSOFT.DATALAKEANALYTICS/
                      ACCOUNTS/
                        <DATA_LAKE_ANALYTICS_NAME>>/
                          y=####/
                            m=##/
                              d=##/
                                h=##/
                                  m=00/
                                    PT1H.json

   > [!NOTE]
   > A folha `##` no caminho contêm o ano, o mês, o dia e a hora em que o log foi criado. O Data Lake Analytics cria um arquivo a cada hora e, portanto, o `m=` sempre conterá um valor `00`.
   >
   >

    Como um exemplo, o caminho completo para um log de auditoria poderia ser:

        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json

    De modo semelhante, o caminho completo para um log de solicitação poderia ser:

        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## <a name="log-structure"></a>Estrutura de log
Os logs de auditoria e solicitação estão em formato JSON. Nesta seção, examinaremos a estrutura do JSON nos logs de solicitação e auditoria.

### <a name="request-logs"></a>Logs de Solicitação
Aqui está um exemplo de entrada no log de solicitação formatado em JSON. Cada blob tem um objeto-raiz chamado **registros** que contém uma matriz de objetos do log.

    {
    "records":
      [        
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_analytics_account_name>",
             "category": "Requests",
             "operationName": "GetAggregatedJobHistory",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {
                 "HttpMethod":"POST",
                 "Path":"/JobAggregatedHistory",
                 "RequestContentLength":122,
                 "ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8",
                 "StartTime":"2016-07-07T21:02:52.472Z",
                 "EndTime":"2016-07-07T21:02:53.456Z"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="request-log-schema"></a>Esquema do log de solicitação
| Name | Tipo | Descrição |
| --- | --- | --- |
| tempo real |Cadeia de caracteres |O carimbo de data/hora (em UTC) do log |
| resourceId |Cadeia de caracteres |A ID do recurso em que a operação ocorreu |
| categoria |Cadeia de caracteres |A categoria do log. Por exemplo, **Solicitações**. |
| operationName |Cadeia de caracteres |Nome da operação que está registrada. Por exemplo, GetAggregatedJobHistory. |
| resultType |Cadeia de caracteres |O status da operação, por exemplo, 200. |
| callerIpAddress |Cadeia de caracteres |O endereço IP do cliente que está fazendo a solicitação |
| correlationId |Cadeia de caracteres |A id do log. Esse valor pode ser usado para agrupar um conjunto de entradas de log relacionadas |
| identidade |Objeto |A identidade que gerou o log |
| propriedades |JSON |Veja a próxima seção (Esquema de propriedades do log de solicitação) para obter detalhes |

#### <a name="request-log-properties-schema"></a>Esquema de propriedades do log de solicitação
| Name | Tipo | Descrição |
| --- | --- | --- |
| HttpMethod |Cadeia de caracteres |O método HTTP usado para a operação. Por exemplo, GET. |
| Caminho |Cadeia de caracteres |O caminho em que a operação foi executada |
| RequestContentLength |int |O comprimento do conteúdo da solicitação HTTP |
| ClientRequestId |Cadeia de caracteres |A ID que identifica esta solicitação exclusivamente |
| StartTime |Cadeia de caracteres |A hora em que o servidor recebeu a solicitação |
| EndTime |Cadeia de caracteres |A hora em que o servidor enviou uma resposta |

### <a name="audit-logs"></a>Logs de auditoria
Aqui está um exemplo de entrada no log de auditoria formatado em JSON. Cada blob tem um objeto-raiz chamado **registros** que contém uma matriz de objetos do log

    {
    "records":
      [        
        . . . .
        ,
        {
             "time": "2016-07-28T19:15:16.245Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_ANALYTICS_account_name>",
             "category": "Audit",
             "operationName": "JobSubmitted",
             "identity": "user@somewhere.com",
             "properties": {
                 "JobId":"D74B928F-5194-4E6C-971F-C27026C290E6",
                 "JobName": "New Job",
                 "JobRuntimeName": "default",
                 "SubmitTime": "7/28/2016 7:14:57 PM"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>Esquema do log de auditoria
| Name | Tipo | Descrição |
| --- | --- | --- |
| tempo real |Cadeia de caracteres |O carimbo de data/hora (em UTC) do log |
| resourceId |Cadeia de caracteres |A ID do recurso em que a operação ocorreu |
| categoria |Cadeia de caracteres |A categoria do log. Por exemplo, **Auditoria**. |
| operationName |Cadeia de caracteres |Nome da operação que está registrada. Por exemplo, JobSubmitted. |
| resultType |Cadeia de caracteres |Um substatus para o status do trabalho (operationName). |
| resultSignature |Cadeia de caracteres |Detalhes adicionais sobre o status do trabalho (operationName). |
| identidade |Cadeia de caracteres |O usuário que solicitou a operação. Por exemplo, susan@contoso.com. |
| propriedades |JSON |Veja a próxima seção (Esquema de propriedades do log de auditoria) para obter detalhes |

> [!NOTE]
> **resultType** e **resultSignature** fornecem informações sobre o resultado de uma operação e conter apenas um valor caso uma operação tenha sido concluída. Por exemplo, eles contêm um valor quando **operationName** contém um valor **JobStarted** ou **JobEnded**.
>
>

#### <a name="audit-log-properties-schema"></a>Esquema de propriedades do log de auditoria
| Name | Tipo | Descrição |
| --- | --- | --- |
| JobId |Cadeia de caracteres |A ID atribuída ao trabalho |
| JobName |Cadeia de caracteres |O nome fornecido para o trabalho |
| JobRunTime |Cadeia de caracteres |O tempo de execução usado para processar o trabalho |
| SubmitTime |Cadeia de caracteres |A hora (em UTC) em que o trabalho foi enviado |
| StartTime |Cadeia de caracteres |A hora em que o trabalho começou a ser executado após o envio (em UTC). |
| EndTime |Cadeia de caracteres |A hora em que o trabalho foi concluído. |
| Paralelismo |Cadeia de caracteres |O número de unidades do Data Lake Analytics solicitadas para esse trabalho durante o envio. |

> [!NOTE]
> **SubmitTime**, **StartTime**, **EndTime** e **Paralelism** fornecem informações sobre uma operação e só conterão um valor se uma operação tiver sido iniciada ou concluída. Por exemplo, **SubmitTime** conterá um valor depois que **operationName** indicar **JobSubmitted**.
>
>

## <a name="process-the-log-data"></a>Processar os dados de log
O Azure Data Lake Analytics fornece um exemplo sobre como processar e analisar os dados do log. Você pode encontrar o exemplo em [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample).

## <a name="next-steps"></a>Próximas etapas
* [Visão geral do Azure Data Lake Analytics](data-lake-analytics-overview.md)



<!--HONumber=Nov16_HO3-->


