<properties 
	pageTitle="Como consumir um serviço Web de Aprendizado de Máquina que tenha sido publicado por meio de um experimento de Aprendizado de Máquina | Azure" 
	description="Depois que um serviço de aprendizado de máquina é publicado, o serviço Web RESTFul disponibilizado pode ser consumido como serviço de solicitação-resposta ou como um serviço de execução de lote." 
	services="machine-learning" 
	solutions="big-data" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="tbd" 
	ms.date="02/20/2015" 
	ms.author="bradsev" />


# Como consumir um serviço Web publicado do Aprendizado de Máquina do Azure

## Introdução

Quando publicado como um serviço Web, os experimentos de Aprendizado de Máquina do Azure fornecem uma API REST que pode ser consumida por uma ampla variedade de dispositivos e plataformas.  Isso ocorre porque a API REST simples aceita e responde com mensagens formatadas em JSON.  O portal de Aprendizado de Máquina do Azure fornece código que pode ser usado para chamar o serviço Web em R, C# e Python.  Porém, esses serviços podem ser chamados com qualquer linguagem de programação e de qualquer dispositivo que satisfaça três critérios:

* Tem uma conexão de rede
* Tem recursos SSL para executar solicitações HTTPS
* Tem a capacidade de analisar JSON (manualmente ou em bibliotecas de suporte)

Isso significa que os serviços possam ser consumidos de aplicativos Web, aplicativos móveis, aplicativos da área de trabalho personalizados e até mesmo no Excel!  

Um serviço Web de Aprendizado de Máquina do Azure pode ser consumido de duas maneiras diferentes, como um serviço de solicitação-resposta ou como um serviço de execução de lote.  Em cada cenário, a funcionalidade é fornecida por meio do serviço Web RESTFul que é disponibilizado para consumo uma vez que a experiência foi publicada.  Implantando um serviço Web de Aprendizado de Máquina no Azure com um ponto de extremidade de serviço Web do Azure, em que o serviço é escalado automaticamente com base no uso, você pode evitar custos antecipados e contínuos de recursos de hardware.

<!-- Quando este artigo for publicado, corrija o link e remova os comentários
Para obter mais informações sobre como gerenciar os pontos de extremidade do serviço Web do Aprendizado de Máquina do Azure usando a API REST, consulte **Pontos de extremidade de serviço Web do aprendizado de máquina do Azure**. 
-->

Para obter informações sobre como criar e publicar um serviço Web de Aprendizado de Máquina do Azure, 
consulte [Publicar um serviço Web de Aprendizado de Máquina do Azure][publicar].  Para obter um passo a passo da criação e publicação de um experimento de Aprendizado de Máquina, consulte [Desenvolver uma solução preditiva usando o Aprendizado de Máquina do Azure][passo a passo].

[publicar]: machine-learning-publish-a-machine-learning-web-service.md
[passo a passo]: machine-learning-walkthrough-develop-predictive-solution.md


## Serviço de solicitação-resposta (RRS)

Um RRS (Serviço de solicitação-resposta) é um serviço Web de baixa latência e altamente escalável usado para fornecer uma interface para os modelos sem monitoração de estado que foram criados e publicados em um experimento do Estúdio do Aprendizado de Máquina do Azure.

O RRS aceita uma única linha de parâmetros de entrada e gera uma única linha como saída.  A linha de saída pode conter várias colunas.

Um exemplo de RRS é validar a autenticidade de um aplicativo.  Centenas de milhões de instalações de um aplicativo podem ser esperadas neste caso.  Quando o aplicativo é iniciado, ele faz uma chamada para o RRS com a entrada relevante.  O aplicativo recebe uma resposta de validação do serviço que permite ou bloqueia a execução do aplicativo.


## Serviço de execução em lote (BES)
 
Um BES (Serviço de Execução em Lote) é um serviço que lida com a pontuação assíncrona e de alto volume de um lote de registro de dados.  A entrada para o BES contém um lote de registros de uma variedade de fontes, como blobs, tabelas no Azure, SQL Azure, HDInsight (resultados de uma Consulta de Hive, por exemplo) e HTTP.  A saída para o BES contém os resultados da pontuação.  Resultados são exportados para um arquivo no armazenamento de Blobs do Azure e dados do ponto de extremidade de armazenamento são retornados na resposta.

Um BES seria útil quando as respostas não são necessárias imediatamente, como para pontuação agendada regularmente para indivíduos ou dispositivos da Internet das coisas (IOT).

## Exemplos
Para mostrar como funcionam o RRS e o BES, usamos um exemplo de serviço Web do Azure.  Esse serviço seria usado em um cenário IOT (Internet das coisas).  Para manter a simplicidade, nosso dispositivo envia apenas um valor, `cog_speed`e recebe uma única resposta de volta. 

Há quatro tipos de informações que são necessárias para chamar o serviço de RRS ou BES.  Essas informações estão prontamente disponíveis nas páginas do serviço em [Páginas de serviço do Aprendizado de Máquina do Azure](https://studio.azureml.net) depois que o experimento foi publicado.  Clique no link SERVIÇOS WEB à esquerda da tela e você verá os serviços publicados.  Para obter informações sobre um serviço específico, há links da página de ajuda da API para RRS e BES.

1.	A **chave de API do serviço**, disponível na página principal de serviços
2.	O **URI do serviço**, disponível na página de Ajuda da API do serviço escolhido
3.	O **corpo da solicitação da API** esperado, disponível na página de Ajuda da API do serviço escolhido
4.	O **corpo da resposta API** esperado, disponível na página de Ajuda da API do serviço escolhido

Nos dois exemplos abaixo, a linguagem C# é usada para ilustrar o código necessário e a plataforma de destino é uma área de trabalho do Windows 8. 

### Exemplo de RRS
Na página de ajuda da API, além do URI, você pode encontrar definições e exemplos de código de entrada e saída.  A entrada da API é chamada, especificamente para este serviço, e é a carga da chamada à API. 

**Exemplo de Solicitação**

	{
	  "Inputs": {
	    "input1": {
	      "ColumnNames": [
	        "cog_speed"
	      ],
	      "Values": [
	        [
	          "0"
	        ],
	        [
	          "1"
	        ]
	      ]
	    }
	  },
	  "GlobalParameters": {}
	}


Da mesma forma, a resposta da API também é chamada novamente especificamente para esse serviço.

**Exemplo de Resposta**

	{
	  "Results": {
	    "output1": {
	      "type": "DataTable",
	      "value": {
	        "ColumnNames": [
	          "cog_speed"
	        ],
	        "ColumnTypes": [
	          "Numeric"
	        ].
	      "Values": [
	        [
	          "0"
	        ],
	        [
	          "1"
	        ]
	      ]
	    }
	  },
	  "GlobalParameters": {}
	}

Na parte inferior da página, você encontrará exemplos de código.  Abaixo está o exemplo de código para a implementação do C# 
                   
**Exemplo de código**
	using System;
	using System.Collections.Generic;
	using System.IO;
	using System.Net.Http;
	using System.Net.Http.Formatting;
	using System.Net.Http.Headers;
	using System.Text;
	using System.Threading.Tasks;
	
	namespace CallRequestResponseService
	{
	    public class StringTable
	    {
	        public string[] ColumnNames { get; set; }
	        public string[,] Values { get; set; }
	    }
	
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            InvokeRequestResponseService().Wait();
	        }
	
	        static async Task InvokeRequestResponseService()
	        {
	            using (var client = new HttpClient())
	            {
	                var scoreRequest = new
	                {
	                    Inputs = new Dictionary<string, StringTable> () { 
	                        { 
	                            "input1", 
	                            new StringTable() 
	                            {
	                                ColumnNames = new string[] {"cog_speed"},
	                                Values = new string[,] {  { "0"},  { "1"}  }
	                            }
	                        },
	                    GlobalParameters = new Dictionary<string, string>() { }
	                };
	                
	                const string apiKey = "abc123"; // Replace this with the API key for the web service
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
	
	                client.BaseAddress = new Uri("https://ussouthcentral.services.azureml.net/workspaces/<workspace id>/services/<service id>/execute?api-version=2.0&details=true");
	                
	                // WARNING: The 'await' statement below can result in a deadlock if you are calling this code from the UI thread of an ASP.Net application.
	                // One way to address this would be to call ConfigureAwait(false) so that the execution does not attempt to resume on the original context.
	                // For instance, replace code such as:
	                //      result = await DoSomeTask()
	                // with the following:
	                //      result = await DoSomeTask().ConfigureAwait(false)

	                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);
	
	                if (response.IsSuccessStatusCode)
	                {
	                    string result = await response.Content.ReadAsStringAsync();
	                    Console.WriteLine("Result: {0}", result);
	                }
	                else
	                {
	                    Console.WriteLine("Failed with status code: {0}", response.StatusCode);
	                }
	            }
	        }
	    }
	}

### Exemplo de BES
Na página de ajuda da API, além de URI, você encontrará informações sobre várias chamadas que estão disponíveis.  Ao contrário do serviço RRS, o serviço BES é assíncrono.  Isso significa que a API BES simplesmente coloca na fila um trabalho a ser executado.  Ele não é realmente executado antes de receber a resposta da API.  Há três coisas que um desenvolvedor pode fazer com o serviço BES, que são descritas abaixo.

1. Enviar um trabalho de execução em lotes
1. Obter o status ou o resultado de um trabalho de execução em lotes
1. Excluir um trabalho de execução de lote  

**1.  Enviar um trabalho de execução em lotes**

Você pode enviar um trabalho de execução em lotes, fornecendo informações sobre onde os dados do lote são armazenados.  Para este exemplo, vamos pressupor que os registros que queremos pontuar em lote estão em um arquivo de blob em uma conta de armazenamento.

A resposta para um trabalho em lotes é uma ID de trabalho, novamente, porque o trabalho é executado de forma assíncrona.  Usaremos a ID do trabalho para obter o status do trabalho e os resultados em um momento posterior.

**Exemplo de Solicitação**

	{
	  "Input": {
	    "ConnectionString":     
	    "DefaultEndpointsProtocol=https;AccountName=mystorageacct;AccountKey=mystorageacctKey",
	    "RelativeLocation": "/mycontainer/mydatablob.csv",
	    "BaseLocation": null,
	    "SasBlobToken": null
	  },
	  "Output": null,
	  "GlobalParameters": {}
	}

**Exemplo de Resposta**

	"539d0bc2fde945b6ac986b851d0000f0" // The JOB_ID

**Exemplo de código**

	// This code requires the Nuget package Microsoft.AspNet.WebApi.Client to be installed.
	// Instructions for doing this in Visual Studio:
	// Tools -> Nuget Package Manager -> Package Manager Console
	// Install-Package Microsoft.AspNet.WebApi.Client
	
	using System;
	using System.Collections.Generic;
	using System.Net.Http;
	using System.Threading.Tasks;
	using System.Net.Http.Headers;
	
	namespace CallBatchExecutionService
	{
	    internal class Program
	    {
	        private static void Main(string[] args)
	        {
	            InvokeBatchExecutionService().Wait();
	        }
	
	        private static async Task InvokeBatchExecutionService()
	        {
	            // API Information
	            const string BESUrl = "[BES URI]";
	            const string ApiKey = "abc123"; 
	            // The storage account information
	            const string StorageAccountName = @"mystorageacct"; 
	            const string StorageAccountKey = @"Dx9WbMIThAvXRQWap/aLnxT9LV5txxw==";
	            // Storage file with the batch of records
	            const string StorageInputFile = @"/mycontainermydatablob.csv"; 
	
	
	            String connString = String.Format(
	                "DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
	                StorageAccountName,
	                StorageAccountKey);
	
	            BatchRequest request = new BatchRequest();
	            request.Input.RelativeLocation = StorageInputFile;
	            request.Input.ConnectionString = connString;
	
	            using (var client = new HttpClient())
	            {
	                client.BaseAddress = new Uri(BESUrl);
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", ApiKey);
	
	                HttpResponseMessage response = await client.PostAsJsonAsync("", request);
	                if (response.IsSuccessStatusCode)
	                {
	                    string result = await response.Content.ReadAsStringAsync();
	                    Console.WriteLine("Job ID: {0}", result);
	                }
	                else
	                {
	                    Console.WriteLine("Failed with status code: {0}", response.StatusCode);
	                }
	            }
	        }
	    }
	
	    public class BatchInput
	    {
	        public String ConnectionString { get; set; }
	        public String RelativeLocation { get; set; }
	        public String BaseLocation { get; set; }
	        public String SasBlobToken { get; set; }
	
	        public BatchInput()
	        {
	            ConnectionString = null;
	            RelativeLocation = null;
	            BaseLocation = null;
	            SasBlobToken = null;
	        }
	    }
	
	    public class BatchRequest
	    {
	        public BatchInput Input { get; set; }
	
	        public Object Output { get; set; }
	
	        public Dictionary<string, string> GlobalParameters { get; set; }
	
	        public BatchRequest()
	        {
	            this.GlobalParameters = new Dictionary<string, string>();
	            Input = new BatchInput();
	            Output = null;
	        }
	    }
	}
	
**2.  Obter o status ou o resultado de um trabalho de execução em lotes**

Para obter o resultado de um trabalho, você deve primeiramente ter a ID do trabalho que estava na resposta para o envio do trabalho.  Não há nenhuma entrada real nessa chamada à API.  Ele usa uma pequena alteração do URI do BES o método de solicitação.  Em vez de uma solicitação POST, ela usa a solicitação GET após o URI definido na página de ajuda de API.
 
A resposta, no entanto, é em camadas.

**Carga de resposta**

	{
	    "StatusCode": STATUS_CODE,
	    "Result": RESULT,
	    "Details": DETAILS
	}

`StatusCode` pode ter um valor de 0, 1, 2, 3 ou 4 com a semântica a seguir:

* 0	Não iniciado
* 1	Em execução
* 2	Falha
* 3	Cancelado
* 4	Concluído

Se o trabalho não for concluído, `Result` é **nulo**.  Se o trabalho for concluído, `Result` deve estar no formato: 

	{
	  "ConnectionString": null,
	  "RelativeLocation": "RELATIVE_LOCATION",
	  "BaseLocation": "BASE_LOCATION",
	  "SasBlobToken": "SAS_BLOB_TOKEN"
	}

Detalhes mostra os detalhes do erro, se houver.

**Exemplo de código**

	// This code requires the Nuget package Microsoft.AspNet.WebApi.Client to be installed.
	// Instructions for doing this in Visual Studio:
	// Tools -> Nuget Package Manager -> Package Manager Console
	// Install-Package Microsoft.AspNet.WebApi.Client
	//
	// Also, add a reference to Microsoft.WindowsAzure.Storage.dll for reading from and writing to the Azure blob storage
	
	using System;
	using System.IO;
	using System.Net.Http;
	using System.Net.Http.Headers;
	using System.Threading.Tasks;
	using Newtonsoft.Json;
	
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;
	
	namespace CallBatchExecutionService
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            String jobId = "123";
	            InvokeBatchExecutionService(jobId).Wait();
	        }
	
	        static async Task InvokeBatchExecutionService(String JobId)
	        {
	            Console.WriteLine(String.Format("Getting job status for job {0}", JobId));
	
	            // BES Information
	            const string BaseUrl = @"[BES Job Id]/{0}";
	            const string ApiKey = "abc123"; 
	            // Replace this with the location you would like to use for your output file
	            const string OutputFileLocation = @"myresults.csv"; 
	
	            using (var client = new HttpClient())
	            {
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", ApiKey);
	
	                HttpResponseMessage response = await client.GetAsync(String.Format(BaseUrl, JobId));
	                if (response.IsSuccessStatusCode)
	                {
	                    string result = await response.Content.ReadAsStringAsync();
	                    BatchResponseStructure responseStruct = JsonConvert.DeserializeObject<BatchResponseStructure>(result);
	
	                    switch (responseStruct.StatusCode)
	                    {
	                        case (int)BatchScoreStatusCode.NotStarted:
	                            Console.WriteLine("Not started...");
	                            break;
	                        case (int)BatchScoreStatusCode.Running:
	                            Console.WriteLine("Running...");
	                            break;
	                        case (int)BatchScoreStatusCode.Failed:
	                            Console.WriteLine("Failed!");
	                            Console.WriteLine(string.Format(@"Error details: {0}", status.Details));
	                            break;
	                        case (int)BatchScoreStatusCode.Cancelled:
	                            Console.WriteLine("Cancelled!");
	                            break;
	                        case (int)BatchScoreStatusCode.Finished:
	                            Console.WriteLine("Finished!");
	                            var credentials = new StorageCredentials(status.Result.SasBlobToken);
	                            var cloudBlob = new CloudBlockBlob(new Uri(new Uri(responseStruct.Result.BaseLocation), 
	                                                                                               responseStruct.Result.RelativeLocation), credentials);
	                            cloudBlob.DownloadToFile(OutputFileLocation, FileMode.Create);
	                            Console.WriteLine(string.Format(@"The results have been written to the file {0}", OutputFileLocation));
	                            break;
	                    }
	                }
	                else
	                {
	                    Console.WriteLine(String.Format("Batch Result : Failed with status code: {0}", response.StatusCode));
	                }
	            }
	        }
	    }
	
	    public enum BatchScoreStatusCode : int
	    {
	        NotStarted = 0,
	        Running = 1,
	        Failed = 2,
	        Cancelled = 3,
	        Finished = 4
	    }
	
	    public class BatchResult
	    {
	        public String ConnectionString { get; set; }
	        public String RelativeLocation { get; set; }
	        public String BaseLocation { get; set; }
	        public String SasBlobToken { get; set; }
	    }
	
	    public class BatchResponseStructure
	    {
	        public int StatusCode { get; set; }
	        public BatchResult Result { get; set; }
	        public String Details { get; set; }
	        public BatchResponseStructure()
	        {
	            this.Result = new BatchResult();
	        }
	    }
	}

**3.  Excluir um trabalho de execução de lote**              
Um trabalho também pode ser excluído depois que for iniciado.  Isso seria feito por vários motivos, tal como se o trabalho estiver demorando muito para ser concluído.  Para excluir um trabalho, você deve primeiramente ter a ID do trabalho que estava contida na resposta para o envio do trabalho.

Não há nenhuma entrada real nessa chamada à API.  Ela usa uma pequena alteração do URI do BES o método de solicitação.  Em vez de uma solicitação POST, ela usa a solicitação DELETE após o URI definido na página de ajuda de API.  O exemplo de código para isso é muito simples.


<!--HONumber=49-->