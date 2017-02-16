---
title: Importar seus dados para o Analytics no Azure Application Insights | Microsoft Docs
description: "Importe dados estáticos para unir a telemetria de aplicativo, ou importe um fluxo de dados separado para consultar com o Analytics."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 47c3491b067d5e112db589672b68e7cfc7cbe921
ms.openlocfilehash: eb89c6f485f2321f729dcfe650af4355de84a9ac


---
# <a name="import-data-into-analytics"></a>Importar dados para o Analytics

Importe quaisquer dados tabulares para o [Analytics](app-insights-analytics.md), seja para associá-los com a telemetria do [Application Insights](app-insights-overview.md) do seu aplicativo ou para que você possa analisá-los como um fluxo separado. O Analytics é uma poderosa linguagem de consulta ideal para analisar fluxos de telemetria de grande volume com carimbo de data e hora.

Você pode importar dados para o Analytics usando seu próprio esquema. Ele não precisa usar os esquemas padrão do Application Insights, como solicitação ou rastreamento.

No momento, você pode importar arquivos CSV (valores separados por vírgulas) ou formatos semelhantes usando separadores de tabulação ou ponto-e-vírgula.

Há três situações em que a importação para o Analytics é útil:

* **Associar à telemetria de aplicativo.** Por exemplo, você pode importar uma tabela que mapeia as URLs do seu site para títulos de página mais legíveis. No Analytics, é possível criar um relatório de gráfico de painel que mostra as&10; páginas mais populares no seu site. Agora ele pode mostrar os títulos das páginas ao invés das URLs.
* **Correlacione a telemetria de aplicativo** com outras fontes, como tráfego de rede, dados de servidor ou arquivos de log da CDN.
* **Aplique o Analytics a um fluxo de dados separado.** O Analytics do Application Insights é uma ferramenta poderosa, que funciona bem com fluxos esparsos com carimbo de data e hora, muito melhor do que com o SQL em muitos casos. Se você tiver tal fluxo de outra origem, pode analisá-lo com o Analytics.

É fácil enviar dados à fonte de dados. 

1. (Uma vez) Defina o esquema dos dados em uma “fonte de dados”.
2. (Periodicamente) Carregue os dados no armazenamento do Azure e chame a API REST para notificar que novos dados estão aguardando a ingestão. Em poucos minutos, os dados estarão disponíveis para consulta no Analytics.

Você define a frequência do carregamento e a rapidez com que deseja que os dados estejam disponíveis para consultas. É mais eficiente carregar dados em blocos maiores, mas não maiores que 1 GB.

> [!NOTE]
> *Você tem muitas fontes de dados para analisar?* [*Considere usar* logstash *para enviar seus dados para o Application Insights.*](https://github.com/Microsoft/logstash-output-application-insights)
> 

## <a name="before-you-start"></a>Antes de começar

Você precisa de:

1. Um recurso do Application Insights no Microsoft Azure.

 * Se quiser analisar seus dados separadamente de qualquer outra telemetria, [crie um novo recurso do Application Insights](app-insights-create-new-resource.md).
 * Se você estiver unindo ou comparando os dados com a telemetria de um aplicativo que já está configurado com o Application Insights, pode usar o recurso desse aplicativo.
 * Acesso de colaborador ou proprietário para esse recurso.
 
2. Armazenamento do Azure. Carregue no armazenamento do Azure e o Analytics recebe seus dados. 

 * Recomendamos que você crie uma conta de armazenamento dedicada para seus blobs. Se seus blobs estiverem compartilhados com outros processos, pode demorar mais para que nossos processos leiam seus blobs.

2. Enquanto esse recurso estiver no modo de prévia, você deve solicitar acesso.

 * Abra o Analytics do recurso do Application Insights no [Portal do Azure](https://portal.azure.com). 
 * Na parte inferior do painel de esquemas, clique no link ‘Fale conosco’ em **Outras Fontes de Dados.** 
 * Se você vir “Adicionar fonte de dados”, então já tem acesso.


## <a name="define-your-schema"></a>Definir seu esquema

Antes de importar dados, você deve definir uma *fonte de dados*, que especifica o esquema de seus dados.

1. Iniciar o Assistente de fonte de dados

    ![Adicionar uma nova fonte de dados](./media/app-insights-analytics-import/add-new-data-source.png)

2. Siga as instruções para carregar um arquivo de dados de exemplo.

 * A primeira linha do exemplo pode ser de cabeçalhos de coluna. (Você pode alterar os nomes dos campos na próxima etapa.)
 * O exemplo deve incluir pelo menos 10 linhas de dados.

3. Revise o esquema que o assistente inferiu a partir do seu exemplo. Você pode ajustar os tipos deduzidos das colunas, se necessário.

4. Selecione um carimbo de data/hora. Todos os dados no Analytics devem ter um campo de carimbo de data/hora. Ele deve ter o tipo `datetime`, mas não precisa ser chamado de ‘carimbo de data/hora’. Se os dados tiverem uma coluna com data e hora no formato ISO, escolha esta opção como a coluna de carimbo de data/hora. Caso contrário, escolha "conforme os dados chegarem", e o processo de importação adicionará um campo de carimbo de data/hora.

    ![Examinar o esquema](./media/app-insights-analytics-import/data-source-review-schema.png)

5. Crie a fonte de dados.


## <a name="import-data"></a>Importar dados

Para importar dados, carregue-os no armazenamento do Azure, crie uma chave de acesso e, em seguida, faça uma chamada para a API REST.

![Adicionar uma nova fonte de dados](./media/app-insights-analytics-import/analytics-upload-process.png)

Você pode realizar o seguinte processo manualmente ou configurar um sistema automatizado para fazê-lo em intervalos regulares. Você precisa seguir estas etapas para cada bloco de dados que quiser importar.

1. Carregue os dados no [armazenamento de blobs do Azure](../storage/storage-dotnet-how-to-use-blobs.md). 

 * Blobs podem ter um tamanho de até 1 GB descompactados. Os blobs grandes, com centenas de MB, são ideais da perspectiva do desempenho.
 * Você pode compactá-los com Gzip para melhorar o tempo de carregamento e a latência, para que os dados fiquem disponíveis para consulta. Use a extensão de nome de arquivo `.gz`.
 
2. [Crie uma Assinatura de Acesso Compartilhado para o blob](../storage/storage-dotnet-shared-access-signature-part-2.md). A chave deve ter um período de validade de um dia e fornecer acesso de leitura.
3. Faça uma chamada REST para notificar o Application Insights que os dados estão em espera.

 * Ponto de extremidade: `https://dc.services.visualstudio.com/v2/track`
 * Método HTTP: POST
 * Conteúdo:

```JSON

    {
       "data": {
            "baseType":"OpenSchemaData",
            "baseData":{
               "ver":"2",
               "blobSasUri":"<Blob URI with Shared Access Key>",
               "sourceName":"<Schema ID>",
               "sourceVersion":"1.0"
             }
       },
       "ver":1,
       "name":"Microsoft.ApplicationInsights.OpenSchema",
       "time":"<DateTime>",
       "iKey":"<instrumentation key>"
    }
```

Os espaços reservados são:

* `Blob URI with Shared Access Key`: você obtém isso no procedimento para criar uma chave. É específico do blob.
* `Schema ID`: a ID de esquema gerada para seu esquema definido. Os dados desse blob devem obedecer o esquema.
* `DateTime`: a hora em que a solicitação foi enviada, em UTC. Aceitamos os seguintes formatos: ISO8601 (como "2016-01-01 13:45:01"); RFC822 ("Qua, 14 dez 16 14:57:01 +0000"); RFC850 ("Quarta-feira, 14-dez-16 14:57:00 UTC"); RFC1123 ("Qua, 14 dez 2016 14:57:00 +0000").
* `Instrumentation key` de seu recurso do Application Insights.

Os dados ficam disponíveis no Analytics depois de alguns minutos.

## <a name="error-responses"></a>Respostas de erro

* **400 Solicitação incorreta**: indica que o conteúdo de solicitação é inválido. Verificação:
 * chave de instrumentação correta.
 * Valor de tempo válido. Deve ser a hora atual em UTC.
 * Dados em conformidade com o esquema.
* **403 Proibido**: o blob que você enviou não está acessível. Certifique-se de que a chave de acesso compartilhado é válida e não expirou.
* **404 Não Encontrado**:
 * o blob não existe.
 * O nome da fonte de dados está errado.

Informações mais detalhadas estão disponíveis na mensagem de erro de resposta.

## <a name="sample-code"></a>Exemplo de código

Esse código usa o pacote [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/9.0.1) do NuGet.

### <a name="classes"></a>Classes

```C#


namespace IngestionClient 
{ 
    using System; 
    using Newtonsoft.Json; 

    public class AnalyticsDataSourceIngestionRequest 
    { 
        #region Members 
        private const string BaseDataRequiredVersion = "2"; 
        private const string RequestName = "Microsoft.ApplicationInsights.OpenSchema"; 
        #endregion Members 

        public AnalyticsDataSourceIngestionRequest(string ikey, string schemaId, string blobSasUri, int version = 1) 
        { 
            Ver = version; 
            IKey = ikey; 
            Data = new Data 
            { 
                BaseData = new BaseData 
                { 
                    Ver = BaseDataRequiredVersion, 
                    BlobSasUri = blobSasUri, 
                    SourceName = schemaId, 
                    SourceVersion = version.ToString() 
                } 
            }; 
        } 


        [JsonProperty("data")] 
        public Data Data { get; set; } 

        [JsonProperty("ver")] 
        public int Ver { get; set; } 

        [JsonProperty("name")] 
        public string Name { get { return RequestName; } } 

        [JsonProperty("time")] 
        public DateTime Time { get { return DateTime.UtcNow; } } 

        [JsonProperty("iKey")] 
        public string IKey { get; set; } 
    } 

    #region Internal Classes 

    public class Data 
    { 
        private const string DataBaseType = "OpenSchemaData"; 

        [JsonProperty("baseType")] 
        public string BaseType 
        { 
            get { return DataBaseType; } 
        } 

        [JsonProperty("baseData")] 
        public BaseData BaseData { get; set; } 
    } 


    public class BaseData 
    { 
        [JsonProperty("ver")] 
        public string Ver { get; set; } 

        [JsonProperty("blobSasUri")] 
        public string BlobSasUri { get; set; } 

        [JsonProperty("sourceName")] 
        public string SourceName { get; set; } 

        [JsonProperty("sourceVersion")] 
        public string SourceVersion { get; set; } 
    } 

    #endregion Internal Classes 
} 


namespace IngestionClient 
{ 
    using System; 
    using System.IO; 
    using System.Net; 
    using System.Text; 
    using System.Threading.Tasks; 
    using Newtonsoft.Json; 

    public class AnalyticsDataSourceClient 
    { 
        #region Members 
        private readonly Uri endpoint = new Uri("https://dc.services.visualstudio.com/v2/track"); 
        private const string RequestContentType = "application/json; charset=UTF-8"; 
        private const string RequestAccess = "application/json"; 
        #endregion Members 

        #region Public 

        public async Task<bool> RequestBlobIngestion(AnalyticsDataSourceIngestionRequest ingestionRequest) 
        { 
            HttpWebRequest request = WebRequest.CreateHttp(endpoint); 
            request.Method = WebRequestMethods.Http.Post; 
            request.ContentType = RequestContentType; 
            request.Accept = RequestAccess; 

            string notificationJson = Serialize(ingestionRequest); 
            byte[] notificationBytes = GetContentBytes(notificationJson); 
            request.ContentLength = notificationBytes.Length; 

            Stream requestStream = request.GetRequestStream(); 
            requestStream.Write(notificationBytes, 0, notificationBytes.Length); 
            requestStream.Close(); 

            HttpWebResponse response; 
            try 
            { 
                using (var response = (HttpWebResponse)await request.GetResponseAsync())
                {
                    return response.StatusCode == HttpStatusCode.OK;
                }
            } 
            catch (WebException e) 
            { 
                HttpWebResponse httpResponse = e.Response as HttpWebResponse; 
                if (httpResponse != null) 
                { 
                    Console.WriteLine( 
                        "Ingestion request failed with status code: {0}. Error: {1}", 
                        httpResponse.StatusCode, 
                        httpResponse.StatusDescription); 
                    return false; 
                }
                throw; 
            } 
        } 
        #endregion Public 

        #region Private 
        private byte[] GetContentBytes(string content) 
        { 
            return Encoding.UTF8.GetBytes(content);
        } 


        private string Serialize(AnalyticsDataSourceIngestionRequest ingestionRequest) 
        { 
            return JsonConvert.SerializeObject(ingestionRequest); 
        } 
        #endregion Private 
    } 
} 

```

### <a name="ingest-data"></a>Ingestão de dados

Use este código para cada blob. 

```C#


   AnalyticsDataSourceClient client = new AnalyticsDataSourceClient(); 

   var ingestionRequest = new AnalyticsDataSourceIngestionRequest("iKey", "tableId/sourceId", "blobUrlWithSas"); 

   bool success = await client.RequestBlobIngestion(ingestionRequest);

```

## <a name="next-steps"></a>Próximas etapas

* [Tour sobre a linguagem de consulta do Analytics](app-insights-analytics-tour.md)
* [Usar *Logstash* para enviar dados ao Application Insights](https://github.com/Microsoft/logstash-output-application-insights)



<!--HONumber=Jan17_HO3-->


