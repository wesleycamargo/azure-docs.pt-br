<properties 
   pageTitle="Exibindo logs de diagnóstico do Azure Data Lake Store| Microsoft Azure" 
   description="Entenda como configurar e acessar os logs de diagnóstico do Azure Data Lake Store " 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="07/19/2016"
   ms.author="nitinme"/>

# Acessando os logs de diagnóstico do Azure Data Lake Store

Saiba mais sobre como habilitar o log de diagnóstico em sua conta do Data Lake Store e como exibir os logs coletados em sua conta.

As organizações podem habilitar o log de diagnóstico para que suas contas do Azure Data Lake Store coletem trilhas de auditoria de acesso a dados que forneçam informações, como a lista de usuários que acessam os dados, a frequência que os dados são acessados, a quantidade de dados armazenados na conta, etc.

## Pré-requisitos

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Habilite sua assinatura do Azure** para a visualização pública do Repositório Data Lake. Veja [instruções](data-lake-store-get-started-portal.md#signup).
- **Conta do Repositório Azure Data Lake**. Siga as instruções em [Introdução ao Repositório Azure Data Lake usando o Portal do Azure](data-lake-store-get-started-portal.md).

## Habilitar o log de diagnóstico em sua conta do Data Lake Store

1. Entre no novo [Portal do Azure](https://portal.azure.com).

2. Abra sua conta do Data Lake Store e, da folha da conta do Data Lake Store, clique em **Configurações** e, em seguida, em **Configurações de Diagnóstico**.

3. Na folha **Diagnóstico**, faça as seguintes alterações para configurar o log de diagnóstico.

	![Habilitar registro em log de diagnóstico](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Habilitar logs de diagnóstico")

	* Defina o **Status** para **Ativado** para habilitar o log de diagnóstico.
	* Você pode optar por armazenar/processar os dados de duas maneiras diferentes.
		* Selecione a opção **Exportar para o Hub de Eventos** para transmitir os dados de log para um Hub de Eventos do Azure. Provavelmente, você usará esta opção se tiver um pipeline de processamento de downstream para analisar os logs de entrada em tempo real. Se escolher esta opção, você deverá fornecer os detalhes no Hub de Eventos do Azure que deseja usar.
		* Selecione a opção **Exportar para a Conta de Armazenamento** para armazenar os logs em uma conta de armazenamento do Azure. Use esta opção se quiser arquivar os dados que serão processados em lote em uma data posterior. Se escolher esta opção, você deverá fornecer uma conta de armazenamento do Azure para salvar os logs.
	* Especifique se deseja obter os logs de auditoria, os logs de solicitação ou ambos.
	* Especifique o número de dias que os dados devem ser mantidos.
	* Clique em **Salvar**.

Depois de habilitar as configurações de diagnóstico, você poderá observar os logs na guia **Logs de Diagnóstico**.

## Veja os logs de diagnóstico em sua conta do Data Lake Store

Há duas maneiras de exibir os dados do log da sua conta no Data Lake Store.

* Na exibição de configurações da conta do Data Lake Store
* Na conta de Armazenamento do Azure onde os dados são armazenados

### Usando a exibição de configurações do Data Lake Store

1. Na folha **Configurações** da sua conta do Data Lake Store, clique em **Logs de Diagnóstico**.

	![Exibir o log de diagnósticos](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "Exibir os logs de diagnóstico")

2. Na folha **Logs de Diagnóstico**, você deverá ver os logs categorizados por **Logs de Auditoria** e por **Logs de Solicitação**.
	* Os Logs de Solicitação capturam todas as solicitações de API feitas na conta do Data Lake Store.
	* Os Logs de Auditoria são semelhantes aos Logs de Solicitação, mas fornecem uma análise mais detalhada das operações que estão sendo executadas na conta do Data Lake Store. Por exemplo, uma única chamada à API de upload nos logs de solicitação poderá resultar em várias operações do tipo "Anexar" nos logs de auditoria.

3. Clique no link **Download** em cada entrada de log para baixar os logs.

### Na conta de Armazenamento do Azure que contém dados de log

1. Abra a folha Conta de armazenamento do Azure associada ao Data Lake Store para registro em log e clique em Blobs. A folha **serviço Blob** lista dois contêineres.

	![Exibir o log de diagnósticos](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "Exibir os logs de diagnóstico")

	* O contêiner **insights-logs-audit** contém os logs de auditoria.
	* O contêiner **insights-logs-requests** contém os logs de solicitação.

2. Dentro desses contêineres, os logs são armazenados na estrutura a seguir.

	![Exibir o log de diagnósticos](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "Exibir os logs de diagnóstico")

	Por exemplo, o caminho completo para um log de auditoria poderia ser `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=04/m=00/PT1H.json`

	De modo semelhante, o caminho completo para um log de solicitação poderia ser `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## Compreenda a estrutura dos dados de log

Os logs de auditoria e solicitação estão em formato JSON. Nesta seção, examinaremos a estrutura do JSON nos logs de solicitação e auditoria.

### Logs de solicitação

Aqui está um exemplo de entrada no log de solicitação formatado em JSON. Cada blob tem um objeto raiz chamado **records** que contém uma matriz de objetos do log.

	{
	"records": 
	  [		
		. . . .
		,
		{
			 "time": "2016-07-07T21:02:53.456Z",
			 "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
			 "category": "Requests",
			 "operationName": "GETCustomerIngressEgress",
			 "resultType": "200",
			 "callerIpAddress": "::ffff:1.1.1.1",
			 "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
			 "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
			 "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z"}
		}
		,
		. . . .
	  ]
	}

#### Esquema do log de solicitação

| Nome | Tipo | Descrição |
|-----------------|--------|--------------------------------------------------------------------------------|
| tempo real | Cadeia de caracteres | O carimbo de data/hora (em UTC) do log |
| resourceId | Cadeia de caracteres | A ID do recurso em que a operação ocorreu |
| categoria | Cadeia de caracteres | A categoria do log. Por exemplo, **Solicitações**. |
| operationName | Cadeia de caracteres | Nome da operação que está registrada. Por exemplo, getfilestatus. |
| resultType | Cadeia de caracteres | O status da operação, por exemplo, 200. |
| callerIpAddress | Cadeia de caracteres | O endereço IP do cliente que está fazendo a solicitação |
| correlationId | Cadeia de caracteres | A ID do log que pode ser usada para agrupar um conjunto de entradas de log relacionadas |
| identidade | Objeto | A identidade que gerou o log |
| propriedades | JSON | Confira abaixo para obter os detalhes |

#### Esquema de propriedades do log de solicitação

| Nome | Tipo | Descrição |
|----------------------|--------|-----------------------------------------------------------|
| HttpMethod | Cadeia de caracteres | O método HTTP usado para a operação. Por exemplo, GET. |
| Caminho | Cadeia de caracteres | O caminho em que a operação foi executada |
| RequestContentLength | int | O comprimento do conteúdo da solicitação HTTP |
| ClientRequestId | Cadeia de caracteres | A ID que identifica esta solicitação exclusivamente |
| StartTime | Cadeia de caracteres | A hora em que o servidor recebeu a solicitação |
| EndTime | Cadeia de caracteres | A hora em que o servidor enviou uma resposta |

### Logs de auditoria

Aqui está um exemplo de entrada no log de auditoria formatado em JSON. Cada blob tem um objeto-raiz chamado **registros** que contém uma matriz de objetos do log

	{
	"records": 
	  [		
		. . . .
		,
		{
			 "time": "2016-07-08T19:08:59.359Z",
			 "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
			 "category": "Audit",
			 "operationName": "SeOpenStream",
			 "resultType": "0",
			 "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
			 "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
			 "properties": {"StreamName":"adl://<data_lake_store_account_name>.azuredatalakestore.net/logs.csv"}
		}
		,
		. . . .
	  ]
	}

#### Esquema do log de auditoria

| Nome | Tipo | Descrição |
|-----------------|--------|--------------------------------------------------------------------------------|
| tempo real | Cadeia de caracteres | O carimbo de data/hora (em UTC) do log |
| resourceId | Cadeia de caracteres | A ID do recurso em que a operação ocorreu |
| categoria | Cadeia de caracteres | A categoria do log. Por exemplo, **Auditoria**. |
| operationName | Cadeia de caracteres | Nome da operação que está registrada. Por exemplo, getfilestatus. |
| resultType | Cadeia de caracteres | O status da operação, por exemplo, 200. |
| correlationId | Cadeia de caracteres | A ID do log que pode ser usada para agrupar um conjunto de entradas de log relacionadas |
| identidade | Objeto | A identidade que gerou o log |
| propriedades | JSON | Confira abaixo para obter os detalhes |

#### Esquema de propriedades do log de auditoria

| Nome | Tipo | Descrição |
|------------|--------|------------------------------------------|
| StreamName | Cadeia de caracteres | O caminho em que a operação foi executada |


## Exemplos para processar os dados do log

O Azure Data Lake Store fornece um exemplo sobre como processar e analisar os dados do log. Você pode encontrar o exemplo em [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample).


## Consulte também

- [Visão geral do repositório Azure Data Lake](data-lake-store-overview.md)
- [Proteger dados no Repositório Data Lake](data-lake-store-secure-data.md)

<!---HONumber=AcomDC_0720_2016-->