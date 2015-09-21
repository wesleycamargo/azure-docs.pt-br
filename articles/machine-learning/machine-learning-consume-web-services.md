<properties
	pageTitle="Consumir um serviço Web de Aprendizado de Máquina | Microsoft Azure"
	description="Depois que um serviço de aprendizado de máquina é implantado, o serviço Web RESTFul disponibilizado pode ser consumido como serviço de solicitação-resposta ou como um serviço de execução em lote."
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
	ms.date="09/09/2015" 
	ms.author="bradsev" />


# Como consumir um serviço Web de Aprendizado de Máquina do Azure que foi implantado por meio de um teste de Aprendizado de Máquina

## Introdução

Quando implantado como um serviço Web, os testes de Aprendizado de Máquina do Azure fornecem uma API REST que pode ser consumida por uma ampla variedade de dispositivos e plataformas. Isso ocorre porque a API REST simples aceita e responde com mensagens formatadas em JSON. O portal de Aprendizado de Máquina do Azure fornece código que pode ser usado para chamar o serviço Web em R, C# e Python. Porém, esses serviços podem ser chamados com qualquer linguagem de programação e de qualquer dispositivo que satisfaça três critérios:

* Tem uma conexão de rede
* Tem recursos SSL para executar solicitações HTTPS
* Tem a capacidade de analisar JSON (manualmente ou em bibliotecas de suporte)

Isso significa que os serviços podem ser consumidos em aplicativos Web, aplicativos móveis, aplicativos da área de trabalho personalizados e até mesmo no Excel.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Um serviço Web de Aprendizado de Máquina do Azure pode ser consumido de duas maneiras diferentes, como um serviço de solicitação-resposta ou como um serviço de execução de lote. Em cada cenário, a funcionalidade é fornecida por meio do serviço Web RESTFul que é disponibilizado para consumo depois que o teste tiver sido implantado. Implantando um serviço Web de Aprendizado de Máquina no Azure com um ponto de extremidade de serviço Web do Azure, em que o serviço é escalado automaticamente com base no uso, você pode evitar custos antecipados e contínuos de recursos de hardware.

<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning web service endpoints using the REST API, see **Azure machine learning web service endpoints**.
-->

Para saber mais sobre como criar e implantar um serviço Web de Aprendizado de Máquina do Azure, consulte [Implantar um serviço Web de Aprendizado de Máquina do Azure][publish]. Para obter uma descrição detalhada de como criar um teste de Aprendizado de Máquina e implantá-lo, consulte [Desenvolver uma solução preditiva usando o Aprendizado de Máquina do Azure][walkthrough].

[publish]: machine-learning-publish-a-machine-learning-web-service.md
[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md


## Serviço de solicitação-resposta (RRS)

Um RRS (Serviço de Solicitação/Resposta) é um serviço Web de baixa latência e altamente escalonável usado para fornecer uma interface aos modelos sem estado que foram criados e implantados de um teste do Estúdio de Aprendizado de Máquina do Azure. Ele possibilita cenários em que o aplicativo de consumo espera uma resposta em tempo real.

O RRS aceita uma única linha, ou várias linhas, dos parâmetros de entrada e pode gerar uma única linha, ou várias linhas, como saída. As linhas de saída podem conter várias colunas.

Um exemplo de RRS é validar a autenticidade de um aplicativo. Centenas de milhões de instalações de um aplicativo podem ser esperadas neste caso. Quando o aplicativo é iniciado, ele faz uma chamada para o RRS com a entrada relevante. O aplicativo recebe uma resposta de validação do serviço que permite ou bloqueia a execução do aplicativo.


## Serviço de execução em lote (BES)

Um BES (Serviço de Execução em Lote) é um serviço que lida com a pontuação assíncrona e de alto volume de um lote de registro de dados. A entrada para o BES contém um lote de registros de uma variedade de fontes, como blobs, tabelas no Azure, SQL Azure, HDInsight (resultados de uma Consulta de Hive, por exemplo) e HTTP. A saída para o BES contém os resultados da pontuação. Resultados são exportados para um arquivo no armazenamento de Blobs do Azure e dados do ponto de extremidade de armazenamento são retornados na resposta.

Um BES seria útil quando as respostas não são necessárias imediatamente, como para pontuação agendada regularmente para indivíduos ou dispositivos da Internet das coisas (IOT).

## Exemplos
Para mostrar como funcionam o RRS e o BES, usamos um exemplo de serviço Web do Azure. Esse serviço seria usado em um cenário IOT (Internet das coisas). Para manter a simplicidade, nosso dispositivo envia apenas um valor, `cog_speed`, e recebe uma única resposta de volta.

Há quatro tipos de informações que são necessárias para chamar o serviço de RRS ou BES. Essas informações estarão prontamente disponíveis nas páginas do serviço em [Páginas de serviço do Aprendizado de Máquina do Azure](https://studio.azureml.net) depois que o teste tiver sido implantado. Clique no link SERVIÇOS WEB à esquerda da tela e você verá os serviços implantados. Para obter informações sobre um serviço específico, há links da página de ajuda da API para RRS e BES.

1.	A **chave de API do serviço**, disponível na página principal dos serviços
2.	O **URI do serviço**, disponível na página de Ajuda da API do serviço escolhido
3.	O **corpo da solicitação da API** esperado, disponível na página de Ajuda da API do serviço escolhido
4.	O **corpo da resposta da API** esperado, disponível na página de Ajuda da API do serviço escolhido

Nos dois exemplos abaixo, a linguagem C# é usada para ilustrar o código necessário e a plataforma de destino é uma área de trabalho do Windows 8.

### Exemplo de RRS
Na página de ajuda da API, além do URI, você pode encontrar definições e exemplos de código de entrada e saída. A entrada da API é chamada, especificamente para este serviço, e é a carga da chamada à API.

**Solicitação de exemplo**

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

**Resposta de exemplo**

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

Na parte inferior da página, você encontrará exemplos de código. Abaixo está o exemplo de código para a implementação do C#

**Código de exemplo**

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
Na página de ajuda da API, além de URI, você encontrará informações sobre várias chamadas que estão disponíveis. Ao contrário do serviço RRS, o serviço BES é assíncrono. Isso significa que a API do BES está simplesmente colocando na fila um trabalho a ser executado, e o chamador sonda o status do trabalho para ver quando ele foi concluído. Veja as operações com suporte para trabalhos em lotes no momento:

1. Criar (enviar) um trabalho em lotes
1. Iniciar esse trabalho em lotes
1. Obter o status ou o resultado de um trabalho em lotes
1. Cancelar um trabalho em lotes em execução

**1. Criar um trabalho de execução em lotes**

Ao criar um trabalho em lotes para o ponto de extremidade de serviço de Aprendizado de Máquina do Azure, é possível especificar vários parâmetros que definirão a execução em lotes:

* **Input**: representa uma referência de blob na qual a entrada do trabalho em lotes é armazenada.
* **GlobalParameters**: representa o conjunto de parâmetros globais que é possível definir para o experimento. Um experimento de Aprendizado de Máquina do Azure pode ter parâmetros obrigatórios e opcionais que personalizam a execução do serviço, e o chamador deve fornecer todos os parâmetros obrigatórios se aplicável. Esses parâmetros são especificados como uma coleção de pares chave-valor.
* **Outputs**: se o serviço definiu uma ou mais saídas, permitimos que o chamador redirecione qualquer uma delas para um local de blob do Azure que preferir. Isso permitirá salvar as saídas do serviço em um local preferencial e com um nome previsível, caso contrário, o nome de blob de saída é gerado aleatoriamente. **OBSERVE** que o serviço espera que o conteúdo de saída, de acordo com o tipo, sejam salvos como formatos com suporte:
  - saídas de conjuntos de dados: podem ser salvas como **CSV, TSV, ARFF**
  - saídas de modelos treinados: podem ser salvas como **ILEARNER**

  As substituições de local de saída são especificadas como uma coleção de *<output name  blob reference>* pares, em que o *nome de saída* é o nome definido pelo usuário para um nó de saída específico (também mostrado na página de Ajuda da API do serviço) e a *referência de blob* é uma referência a um local de blob do Azure para o qual a saída dever ser direcionada.

Todos esses parâmetros de criação de trabalho podem ser opcionais, dependendo da natureza do serviço. Por exemplo, os serviços sem nenhum nó de entrada definido não exigem a passagem em um parâmetro *Input* e o recurso de substituição de local de saída é totalmente opcional, caso contrário, as saídas serão armazenadas na conta de armazenamento padrão configurada para o espaço de trabalho do Aprendizado de Máquina do Azure. A seguir, mostramos uma carga de solicitação de exemplo, conforme passada à API REST, de um serviço em que apenas as informações de entrada são passadas:

**Solicitação de exemplo**

	{
	  "Input": {
	    "ConnectionString":     
	    "DefaultEndpointsProtocol=https;AccountName=mystorageacct;AccountKey=mystorageacctKey",
	    "RelativeLocation": "/mycontainer/mydatablob.csv",
	    "BaseLocation": null,
	    "SasBlobToken": null
	  },
	  "Outputs": null,
	  "GlobalParameters": null
	}

A resposta à API de criação de trabalho em lotes é a ID exclusiva do trabalho associada ao seu trabalho. Essa ID é muito importante porque fornece o único meio para fazer referência a esse trabalho no sistema para outras operações.

**Resposta de exemplo**

	"539d0bc2fde945b6ac986b851d0000f0" // The JOB_ID

**2. Iniciar um trabalho de execução em lotes**

A criação de um trabalho em lotes apenas o registra no sistema e o coloca em um estado *não iniciado*. Para realmente agendar o trabalho para execução, você precisará chamar a API **iniciar** descrita na página de Ajuda da API do ponto de extremidade de serviço e fornecer a ID do trabalho obtida quando o trabalho foi criado.

**3. Obter o status de um trabalho de execução em lotes**

Você pode sondar o status do trabalho assíncrono em lotes a qualquer momento ao passar a ID do trabalho para a API GetJobStatus. A resposta da API conterá um indicador do estado atual do trabalho, bem como os resultados reais do trabalho em lotes se concluído com êxito. Em caso de erro, mais informações sobre os motivos reais relacionados à falha são retornados na propriedade *Details*.

**Carga de resposta**

	{
	    "StatusCode": STATUS_CODE,
	    "Results": RESULTS,
	    "Details": DETAILS
	}

*StatusCode* pode ser uma das seguintes opções:

* Não iniciado
* Executando
* Falha
* Cancelado
* Concluído

A propriedade *Results* é preenchida somente se o trabalho foi concluído com êxito (caso contrário é **null**). Após a conclusão do trabalho e se o serviço tiver pelo menos um nó de saída definido, os resultados serão retornados como uma coleção de pares *[nome de saída, referência de blob]*, em que a referência é uma referência SAS somente leitura ao blob que contém o resultado real.

**Resposta de exemplo**

	{
	    "Status Code": "Finished",
	    "Results":
	    {
	        "dataOutput":
	        {              
	            "ConnectionString": null,
	            "RelativeLocation": "outputs/dataOutput.csv",
	            "BaseLocation": "https://mystorageaccount.blob.core.windows.net/",
	            "SasBlobToken": "?sv=2013-08-15&sr=b&sig=ABCD&st=2015-04-04T05%3A39%3A55Z&se=2015-04-05T05%3A44%3A55Z&sp=r"              
	        },
	        "trainedModelOutput":
	        {              
	            "ConnectionString": null,
	            "RelativeLocation": "models/trainedModel.ilearner",
	            "BaseLocation": "https://mystorageaccount.blob.core.windows.net/",
	            "SasBlobToken": "?sv=2013-08-15&sr=b&sig=EFGH%3D&st=2015-04-04T05%3A39%3A55Z&se=2015-04-05T05%3A44%3A55Z&sp=r"              
	        },           
	    },
	    "Details": null
	}

**4. Cancelar um trabalho de execução em lotes**

Um trabalho em lotes em execução pode ser cancelado a qualquer momento ao chamar a API CancelJob e passar ID do trabalho. Isso seria feito por vários motivos, tal como se o trabalho estiver demorando muito para ser concluído.



#### Usando o [SDK do BES](machine-learning-consume-web-services.md#batch-execution-service-sdk)

O [pacote NuGet do SDK do BES](http://www.nuget.org/packages/Microsoft.Azure.MachineLearning/) fornece funções que simplificam a chamada do BES para pontuação no modo em lotes. Para instalar o pacote NuGet no Visual Studio, vá para Ferramentas, selecione Gerenciador de Pacotes NuGet e clique em Console do Gerenciador de Pacotes.

Os testes AzureML implantados como serviços Web podem incluir módulos de entrada de serviço Web, o que significa que eles esperam que a entrada seja fornecida por meio da chamada do serviço Web na forma de uma referência a um local de blob. Também há a opção de não usar um módulo de entrada de serviço Web, mas sim um módulo Leitor em vez disso. Nesse caso, o Leitor normalmente leria um banco de dados SQL usando uma consulta em tempo de execução para obter os dados. Os parâmetros do serviço Web podem ser usados para apontar dinamicamente para outros servidores ou tabelas, etc. O SDK dá suporte a esses padrões.

O exemplo de código abaixo demonstra como você pode enviar e monitorar um trabalho em lotes em relação a um ponto de extremidade de serviço de Aprendizado de Máquina do Azure usando o SDK do BES. Observe os comentários para obter detalhes sobre as configurações e as chamadas.

#### **Código de exemplo**

	// This code requires the Nuget package Microsoft.Azure.MachineLearning to be installed.
	// Instructions for doing this in Visual Studio:
	// Tools -> Nuget Package Manager -> Package Manager Console
	// Install-Package Microsoft.Azure.MachineLearning

	  using System;
	  using System.Collections.Generic;
	  using System.Threading.Tasks;

	  using Microsoft.Azure.MachineLearning;
	  using Microsoft.Azure.MachineLearning.Contracts;
	  using Microsoft.Azure.MachineLearning.Exceptions;

	namespace CallBatchExecutionService
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {	            
	            InvokeBatchExecutionService().Wait();
	        }

	        static async Task InvokeBatchExecutionService()
	        {
	            // First collect and fill in the URI and access key for your web service endpoint.
	            // These are available on your service's API help page.
	            var endpointUri = "https://ussouthcentral.services.azureml.net/workspaces/YOUR_WORKSPACE_ID/services/YOUR_SERVICE_ENDPOINT_ID/";
	            string accessKey = "YOUR_SERVICE_ENDPOINT_ACCESS_KEY";

	            // Create an Azure Machine Learning runtime client for this endpoint
	            var runtimeClient = new RuntimeClient(endpointUri, accessKey);

	            // Define the request information for your batch job. This information can contain:
	            // -- A reference to the AzureBlob containing the input for your job run
	            // -- A set of values for global parameters defined as part of your experiment and service
	            // -- A set of output blob locations that allow you to redirect the job's results

	            // NOTE: This sample is applicable, as is, for a service with explicit input port and
	            // potential global parameters. Also, we choose to also demo how you could override the
	            // location of one of the output blobs that could be generated by your service. You might
	            // need to tweak these features to adjust the sample to your service.
	            //
	            // All of these properties of a BatchJobRequest shown below can be optional, depending on
	            // your service, so it is not required to specify all with any request.  If you do not want to
	            // use any of the parameters, a null value should be passed in its place.

	            // Define the reference to the blob containing your input data. You can refer to this blob by its
                    // connection string / container / blob name values; alternatively, we also support references
                    // based on a blob SAS URI

                    BlobReference inputBlob = BlobReference.CreateFromConnectionStringData(connectionString:                                         "DefaultEndpointsProtocol=https;AccountName=YOUR_ACCOUNT_NAME;AccountKey=YOUR_ACCOUNT_KEY",
                        containerName: "YOUR_CONTAINER_NAME",
                        blobName: "YOUR_INPUT_BLOB_NAME");

                    // If desired, one can override the location where the job outputs are to be stored, by passing in
                    // the storage account details and name of the blob where we want the output to be redirected to.

                    var outputLocations = new Dictionary<string, BlobReference>
                        {
                          {
                           "YOUR_OUTPUT_NODE_NAME",
                           BlobReference.CreateFromConnectionStringData(                                     connectionString: "DefaultEndpointsProtocol=https;AccountName=YOUR_ACCOUNT_NAME;AccountKey=YOUR_ACCOUNT_KEY",
                                containerName: "YOUR_CONTAINER_NAME",
                                blobName: "YOUR_DESIRED_OUTPUT_BLOB_NAME")
                           }
                        };

	            // If applicable, you can also set the global parameters for your service
	            var globalParameters = new Dictionary<string, string>
	            {
	                { "YOUR_GLOBAL_PARAMETER", "PARAMETER_VALUE" }
	            };

	            var jobRequest = new BatchJobRequest
	            {
	                Input = inputBlob,
	                GlobalParameters = globalParameters,
	                Outputs = outputLocations
	            };

	            try
	            {
	                // Register the batch job with the system, which will grant you access to a job object
	                BatchJob job = await runtimeClient.RegisterBatchJobAsync(jobRequest);

	                // Start the job to allow it to be scheduled in the running queue
	                await job.StartAsync();

	                // Wait for the job's completion and handle the output
	                BatchJobStatus jobStatus = await job.WaitForCompletionAsync();
	                if (jobStatus.JobState == JobState.Finished)
	                {
	                    // Process job outputs
	                    Console.WriteLine(@"Job {0} has completed successfully and returned {1} outputs", job.Id, jobStatus.Results.Count);
	                    foreach (var output in jobStatus.Results)
	                    {
	                        Console.WriteLine(@"\t{0}: {1}", output.Key, output.Value.AbsoluteUri);
	                    }
	                }
	                else if (jobStatus.JobState == JobState.Failed)
	                {
	                    // Handle job failure
	                    Console.WriteLine(@"Job {0} has failed with this error: {1}", job.Id, jobStatus.Details);
	                }
	            }
	            catch (ArgumentException aex)
	            {
	                Console.WriteLine("Argument {0} is invalid: {1}", aex.ParamName, aex.Message);
	            }
	            catch (RuntimeException runtimeError)
	            {
	                Console.WriteLine("Runtime error occurred: {0} - {1}", runtimeError.ErrorCode, runtimeError.Message);
	                Console.WriteLine("Error details:");
	                foreach (var errorDetails in runtimeError.Details)
	                {
	                    Console.WriteLine("\t{0} - {1}", errorDetails.Code, errorDetails.Message);
	                }
	            }
	            catch (Exception ex)
	            {
	                Console.WriteLine("Unexpected error occurred: {0} - {1}", ex.GetType().Name, ex.Message);
	            }
	        }
	    }
	}

<!---HONumber=Sept15_HO2-->