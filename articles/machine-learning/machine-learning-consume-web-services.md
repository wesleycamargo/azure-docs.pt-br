---
title: "Consumir um serviço Web do Machine Learning | Microsoft Docs"
description: "Depois que um serviço de machine learning é implantado, o serviço Web RESTFul disponibilizado pode ser consumido como serviço de solicitação-resposta ou como um serviço de execução em lote."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 804f8211-9437-4982-98e9-ca841b7edf56
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 10/04/2016
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 369b7c8775388f641727cb889b406ac60be13d16


---
# <a name="how-to-consume-an-azure-machine-learning-web-service-that-has-been-deployed-from-a-machine-learning-experiment"></a>Como consumir um serviço Web do Azure Machine Learning que foi implantado de um experimento de Machine Learning
## <a name="introduction"></a>Introdução
Quando implantado como um serviço Web, os experimentos do Azure Machine Learning fornecem uma API REST e mensagens formatadas em JSON que podem ser consumidas por uma ampla variedade de dispositivos e plataformas. O portal do Azure Machine Learning fornece código que pode ser usado para chamar o serviço Web em R, C# e Python. 

Os serviços podem ser chamados com qualquer linguagem de programação e de qualquer dispositivo que satisfaça três critérios:

* Tem uma conexão de rede
* Tem recursos SSL para executar solicitações HTTPS
* Pode analisar JSON (manualmente ou em bibliotecas de suporte)

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Um serviço Web do Azure Machine Learning pode ser consumido de duas maneiras, como um serviço de solicitação-resposta ou como um serviço de execução de lote. Em cada cenário, a funcionalidade é fornecida por meio do serviço Web RESTFul que é disponibilizado para consumo após você implantar o experimento.

> [!TIP]
> Para conhecer uma maneira simples de criar um aplicativo Web a fim de acessar seu serviço da Web, confira [Consumir um serviço Web do Azure Machine Learning com um modelo de aplicativo Web](machine-learning-consume-web-service-with-web-app-template.md).
> 
> 

<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning Web service endpoints using the REST API, see **Azure machine learning Web service endpoints**.
-->

Para saber mais sobre como criar e implantar um serviço Web do Azure Machine Learning, confira [Implantar um serviço Web do Azure Machine Learning][publicar]. Para obter uma descrição detalhada de como criar um experimento de Machine Learning e implantá-lo, consulte [Desenvolver uma solução preditiva usando o Azure Machine Learning][passo a passo].

## <a name="request-response-service-rrs"></a>Serviço de solicitação-resposta (RRS)
Um RRS (Serviço de Solicitação/Resposta) é um serviço Web de baixa latência e altamente escalonável usado para fornecer uma interface aos modelos sem estado que foram criados e implantados de um experimento do Azure Machine Learning Studio. Ele possibilita cenários em que o aplicativo de consumo espera uma resposta em tempo real.

O RRS aceita uma única linha, ou várias linhas, dos parâmetros de entrada e pode gerar uma única linha, ou várias linhas, como saída. As linhas de saída podem conter várias colunas.

Um exemplo de RRS é validar a autenticidade de um aplicativo. Centenas de milhões de instalações de um aplicativo podem ser esperadas neste caso. Quando o aplicativo é iniciado, ele chama o serviço RRS com a entrada relevante. O aplicativo recebe uma resposta de validação do serviço que permite ou bloqueia a execução do aplicativo.

## <a name="batch-execution-service-bes"></a>Serviço de execução em lote (BES)
Um BES (Serviço de Execução em Lote) é um serviço que lida com a pontuação assíncrona e de alto volume de um lote de registro de dados. A entrada para o BES contém um lote de registros de uma variedade de fontes, como blobs, tabelas no Azure, SQL Azure, HDInsight (resultados de uma Consulta de Hive, por exemplo) e HTTP. A saída para o BES contém os resultados da pontuação. Resultados são exportados para um arquivo no armazenamento de Blobs do Azure e dados do ponto de extremidade de armazenamento são retornados na resposta.

Um BES seria útil quando as respostas não são necessárias imediatamente, como para pontuação agendada regularmente para indivíduos ou dispositivos da Internet das coisas (IOT).

## <a name="examples"></a>Exemplos
Para mostrar como funcionam o RRS e o BES, usamos um exemplo de serviço Web do Azure. Esse serviço seria usado em um cenário IOT (Internet das coisas). Para manter a simplicidade, nosso dispositivo envia apenas um valor, `cog_speed`, e recebe uma única resposta de volta.

Após o experimento ter sido implantado, há quatro tipos de informações necessárias para chamar o serviço de RSS ou BES.

* A **Chave de API** ou **Chave primeira** do serviço
* O **URI de solicitação** do serviço
* Os **cabeçalhos de solicitação** e o **corpo** da API esperada
* Os **cabeçalhos de resposta** e o **corpo** da API esperada

A maneira pela qual você pode encontrar essas informações depende o tipo de serviço implantado: um novo serviço Web ou um serviço Web clássico.

### <a name="information-location-in-the-azure-machine-learning-web-services-portal"></a>Localização de informações no portal de serviços Web do Azure Machine Learning
Para localizar as informações necessárias:

1. Entre no portal de [Serviços Web do Azure Machine Learning][webservicesportal].
2. Clique em **Serviços Web** ou **Classic Web Services (Serviços Web clássicos)**.
3. Clique no serviço Web com o qual está trabalhando. 
4. Se você estiver trabalhando com um serviço Web Clássico, clique no ponto de extremidade com o qual está trabalhando.

As informações estão localizadas nestas páginas:

* A **Chave primária** está disponível na página **Consumir**
* O **URI de solicitação** está disponível na página **Consumir** 
* Os **cabeçalhos de solicitação**, **cabeçalhos de resposta** e **corpo** estão disponíveis na página da **Swagger API (API Swagger)**

### <a name="information-locations-in-machine-learning-studio-classic-web-service-only"></a>Locais de informações no Machine Learning Studio (apenas no serviço Web clássico)
Você pode encontrar as informações necessárias em duas localizações: no Machine Learning Studio ou no portal de serviços Web do Azure Machine Learning.

Para localizar as informações necessárias no Machine Learning Studio:

1. Faça logon no [Machine Learning Studio][mlstudio].
2. À esquerda da tela, clique em **SERVIÇOS WEB**.
3. Clique no serviço Web com o qual está trabalhando. 

As informações estão localizadas nestas páginas:

* A **Chave de API** está disponível no **Painel** de serviços 
* O **URI de solicitação** está disponível na página de ajuda da API
* Os **cabeçalhos de solicitação**, **cabeçalhos de resposta** e **corpo** estão disponíveis na página de ajuda da API

Para acessar a página de ajuda da API, clique no link **SOLICITAÇÃO/RESPOSTA** ou **EXECUÇÃO EM LOTES** conforme apropriado para sua tarefa.

Para localizar as informações necessárias no portal de Serviços Web do Azure Machine Learning:

1. Entre no portal de [Serviços Web do Azure Machine Learning][webservicesportal].
2. Clique em **Classic Web Services (Serviços Web clássicos)**.
3. Clique no serviço Web com o qual está trabalhando. 
4. Clique no ponto de extremidade com o qual está trabalhando.

As informações estão localizadas nestas páginas:

* A **Chave primária** está disponível na página **Consumir**
* O **URI de solicitação** está disponível na página **Consumir** 
* Os **cabeçalhos de solicitação**, **cabeçalhos de resposta** e **corpo** estão disponíveis na página da **Swagger API (API Swagger)**

Nos dois exemplos abaixo, a linguagem C# é usada para ilustrar o código necessário.

### <a name="rrs-example"></a>Exemplo de RRS
O exemplo de solicitação a seguir mostra a entrada da API do conteúdo para a chamada à API de nosso serviço de exemplo. Para um serviço Web clássico, você pode encontrar exemplos de conteúdo na **página de ajuda da API** ou na página **Swagger API (API Swagger)** do portal de Serviços Web do Machine Learning. Para um novo serviço Web, você pode encontrar exemplos de conteúdo na página **Swagger API (API Swagger)** do portal de Serviços Web do Machine Learning.

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


Da mesma forma, o exemplo a seguir mostra a resposta da API para o serviço.

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

Abaixo está o exemplo de código para a implementação do C#. Para um serviço Web clássico, você pode encontrar exemplos de código na parte inferior da **página de ajuda da API** ou na parte inferior da página **Consumir**. Para um novo serviço Web, você pode encontrar exemplos de código na parte inferior da página **Consumir**.

**Código de exemplo em C#**

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

                    const string apiKey = "abc123"; // Replace this with the API key for the Web service
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

**Código de exemplo em Java**

O código de exemplo a seguir mostra como construir uma solicitação de API REST em Java. Ele pressupõe que as variáveis (apikey e apiurl) tenham os detalhes de API necessários e que a variável jsonBody tenha um objeto JSON correto como esperado pela API REST. Você pode baixar o código completo do github – [https://github.com/nk773/AzureML_RRSApp](https://github.com/nk773/AzureML_RRSApp). Este exemplo de Java requer a [biblioteca cliente HTTP do apache](https://hc.apache.org/downloads.cgi).

    /**
     * Download full code from github - [https://github.com/nk773/AzureML_RRSApp](https://github.com/nk773/AzureML_RRSApp)
      */
        /**
           * Call REST API for retrieving prediction from Azure ML 
           * @return response from the REST API
           */    
        public static String rrsHttpPost() {

            HttpPost post;
            HttpClient client;
            StringEntity entity;

            try {
                    // create HttpPost and HttpClient object
                    post = new HttpPost(apiurl);
                    client = HttpClientBuilder.create().build();

                    // setup output message by copying JSON body into 
                    // apache StringEntity object along with content type
                    entity = new StringEntity(jsonBody, HTTP.UTF_8);
                    entity.setContentEncoding(HTTP.UTF_8);
                    entity.setContentType("text/json");

                    // add HTTP headers
                    post.setHeader("Accept", "text/json");
                    post.setHeader("Accept-Charset", "UTF-8");

                    // set Authorization header based on the API key
                    post.setHeader("Authorization", ("Bearer "+apikey));
                    post.setEntity(entity);

                    // Call REST API and retrieve response content
                    HttpResponse authResponse = client.execute(post);

                    return EntityUtils.toString(authResponse.getEntity());

            }
            catch (Exception e) {

                    return e.toString();
            }

        }




### <a name="bes-example"></a>Exemplo de BES
Ao contrário do serviço RRS, o serviço BES é assíncrono. Isso significa que a API do BES está simplesmente colocando na fila um trabalho a ser executado, e o chamador sonda o status do trabalho para ver quando ele foi concluído. Veja as operações com suporte para trabalhos em lotes no momento:

1. Criar (enviar) um trabalho em lotes
2. Iniciar esse trabalho em lotes
3. Obter o status ou o resultado de um trabalho em lotes
4. Cancelar um trabalho em lotes em execução

**1. Criar um trabalho de execução em lotes**

Ao criar um trabalho em lotes para seu serviço do Azure Machine Learning, você pode especificar vários parâmetros que definem a execução em lotes:

* **Input**: representa uma referência de blob na qual a entrada do trabalho em lotes é armazenada.
* **GlobalParameters**: representa o conjunto de parâmetros globais que você pode definir para o experimento. Um experimento de Aprendizado de Máquina do Azure pode ter parâmetros obrigatórios e opcionais que personalizam a execução do serviço, e o chamador deve fornecer todos os parâmetros obrigatórios se aplicável. Esses parâmetros são especificados como uma coleção de pares chave-valor.
* **Outputs**: se o serviço tiver definido uma ou mais saídas, o chamador poderá redirecionar qualquer uma delas para uma localização de blob do Azure. Ao definir esse parâmetro, você pode salvar as saídas do serviço em uma localização preferencial e com um nome previsível, caso contrário, o nome do blob de saída é gerado aleatoriamente. 
  
    O serviço espera que o conteúdo de saída, de acordo com o tipo, seja salvos como formatos com suporte:
  
  * saídas de conjuntos de dados: podem ser salvas como **.csv, .tsv, .arff**
  * saídas de modelos treinados: devem ser salvas como **.ilearner**
    
    Você especifica as substituições de localização de saída como uma coleção de pares de referência de blob ou nome de saída. O *nome de saída* é o nome definido pelo usuário para um nó de saída específico e *referência de blob* é uma referência a uma localização de blob do Azure para a qual a saída é redirecionada. O *nome de saída* é mostrado na página de ajuda da API do serviço.

Todos os parâmetros de criação de trabalho são opcionais, dependendo da natureza do serviço. Por exemplo, serviços os sem um nó de entrada definido não exigem a passagem em um parâmetro *Input* . Da mesma forma, o recurso de substituição de localização de saída é opcional, uma vez que, caso contrário, as saídas serão armazenadas na conta de armazenamento padrão configurada para o espaço de trabalho do Azure Machine Learning. O exemplo a seguir solicita conteúdos para um serviço em que apenas as informações de entrada são fornecidas:

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

A resposta à API de criação de trabalho em lotes é a ID exclusiva do trabalho associada ao seu trabalho. Essa ID é importante porque fornece o único meio para fazer referência a esse trabalho no sistema para outras operações.  

**Resposta de exemplo**

    "539d0bc2fde945b6ac986b851d0000f0" // The JOB_ID

**2. Iniciar um trabalho de execução em lotes**

Ao criar um trabalho em lotes, ele é registrado no sistema e o coloca em um estado *Não iniciado* . Para realmente agendar o trabalho para execução, você chama a API **start** descrita na página de ajuda da API do ponto de extremidade de serviço ou na página da API Swagger do serviço Web e fornece a ID do trabalho obtida quando o trabalho foi criado.

**3. Obter o status de um trabalho de execução em lotes**

Você pode sondar o status do trabalho em lotes assíncrono a qualquer momento ao passar a ID do trabalho para a API *GetJobStatus*. A resposta da API contém um indicador do estado atual do trabalho e os resultados do trabalho em lotes indicando se ele foi concluído com sucesso. Se houver um erro, mais informações sobre os motivos reais relacionados à falha serão retornadas na propriedade *Details*, como mostrado aqui:

**Carga de resposta**

    {
        "StatusCode": STATUS_CODE,
        "Results": RESULTS,
        "Details": DETAILS
    }

*StatusCode* pode ser uma das seguintes opções:

* não iniciado
* Executando
* Falha
* Cancelado
* Concluído

A propriedade *Results* é populada quando o trabalho foi concluído com êxito (caso contrário é **null**). Após a conclusão do trabalho e se o serviço tiver pelo menos um nó de saída definido, os resultados são retornados como uma coleção de pares *[nome de saída, referência de blob]*, em que a referência é uma referência SAS somente leitura ao blob que contém o resultado.

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

Você pode cancelar um trabalho em lotes em execução a qualquer momento ao chamar a API *CancelJob* designada e passar ID do trabalho. Você pode cancelar por vários motivos, tal como se o trabalho estiver demorando muito para ser concluído.

#### <a name="using-the-bes-sdk"></a>Usando o SDK do BES
O [pacote Nuget do SDK do BES](http://www.nuget.org/packages/Microsoft.Azure.MachineLearning/) fornece funções que simplificam a chamada do BES para pontuação no modo em lotes. Para instalar o pacote Nuget, no Visual Studio, no menu **Ferramentas**, selecione **Gerenciador de Pacotes Nuget** e clique em **Console do Gerenciador de Pacotes**.

Os experimentos do Azure Machine Learning que são implantados como serviços Web podem incluir módulos de entrada de serviço Web. Isso significa que a entrada para o serviço Web é fornecida por meio da chamada do serviço Web na forma de uma referência para uma localização de blob. Também há a opção de não usar um módulo de entrada de serviço Web, mas sim um módulo **Importar Dados**. Nesse caso, o módulo **Importar Dados** lê uma fonte de dados, como um banco de dados SQL usando uma consulta no tempo de execução. Os parâmetros do serviço Web podem ser usados para apontar dinamicamente para outros servidores ou tabelas, etc. O SDK dá suporte a esses padrões.

O exemplo de código a seguir demonstra como você pode enviar e monitorar um trabalho em lotes em relação a um serviço do Azure Machine Learning usando o SDK do BES. Os comentários contêm detalhes sobre as configurações e chamadas.

#### <a name="sample-code"></a>**Código de exemplo**
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
                // First collect and fill in the URI and access key for your Web service endpoint.
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

#### <a name="sample-code-in-java-for-bes"></a>Código de exemplo em Java para BES
A API REST do serviço de execução do Lote usa o JSON que consiste em uma referência para um csv de exemplo de entrada e em um csv de exemplo de saída, como mostrado no exemplo a seguir, e cria um trabalho no Azure ML para fazer previsões em lotes. Você pode exibir o código completo no [Github](https://github.com/nk773/AzureML_BESApp/tree/master/src/azureml_besapp). Este exemplo de Java requer a [biblioteca cliente HTTP do apache](https://hc.apache.org/downloads.cgi). 

    { "GlobalParameters": {}, 
        "Inputs": { "input1": { "ConnectionString":     "DefaultEndpointsProtocol=https;
            AccountName=myAcctName; AccountKey=Q8kkieg==", 
            "RelativeLocation": "myContainer/sampleinput.csv" } }, 
        "Outputs": { "output1": { "ConnectionString":     "DefaultEndpointsProtocol=https;
            AccountName=myAcctName; AccountKey=kjC12xQ8kkieg==", 
            "RelativeLocation": "myContainer/sampleoutput.csv" } } 
    } 


##### <a name="create-a-bes-job"></a>Criar um trabalho do BES
        /**
         * Call REST API to create a job to Azure ML 
         * for batch predictions
         * @return response from the REST API
         */    
        public static String besCreateJob() {

            HttpPost post;
            HttpClient client;
            StringEntity entity;

            try {
                // create HttpPost and HttpClient object
                post = new HttpPost(apiurl);
                client = HttpClientBuilder.create().build();

                // setup output message by copying JSON body into 
                // apache StringEntity object along with content type
                entity = new StringEntity(jsonBody, HTTP.UTF_8);
                entity.setContentEncoding(HTTP.UTF_8);
                entity.setContentType("text/json");

                // add HTTP headers
                post.setHeader("Accept", "text/json");
                post.setHeader("Accept-Charset", "UTF-8");

                // set Authorization header based on the API key
                // note a space after the word "Bearer " - don't miss that
                post.setHeader("Authorization", ("Bearer "+apikey));
                post.setEntity(entity);

                // Call REST API and retrieve response content
                HttpResponse authResponse = client.execute(post);

                jobId = EntityUtils.toString(authResponse.getEntity()).replaceAll("\"", "");


                return jobId;

            }
            catch (Exception e) {

                return e.toString();
            }

        }

##### <a name="start-a-previously-created-bes-job"></a>Iniciar um trabalho do BES criado anteriormente
        /**
         * Call REST API for starting prediction job previously submitted 
         * 
         * @param job job to be started 
         * @return response from the REST API
         */    
        public static String besStartJob(String job){
            HttpPost post;
            HttpClient client;
            StringEntity entity;

            try {
                // create HttpPost and HttpClient object
                post = new HttpPost(startJobUrl+"/"+job+"/start?api-version=2.0");
                client = HttpClientBuilder.create().build();

                // add HTTP headers
                post.setHeader("Accept", "text/json");
                post.setHeader("Accept-Charset", "UTF-8");

                // set Authorization header based on the API key
                post.setHeader("Authorization", ("Bearer "+apikey));

                // Call REST API and retrieve response content
                HttpResponse authResponse = client.execute(post);

                if (authResponse.getEntity()==null)
                {
                    return authResponse.getStatusLine().toString();
                }

                return EntityUtils.toString(authResponse.getEntity());

            }
            catch (Exception e) {

                return e.toString();
            }
        }
##### <a name="cancel-a-previously-created-bes-job"></a>Cancelar um trabalho do BES criado anteriormente
        /**
         * Call REST API for canceling the batch job 
         * 
         * @param job job to be started 
         * @return response from the REST API
         */    
        public static String besCancelJob(String job) {
            HttpDelete post;
            HttpClient client;
            StringEntity entity;

            try {
                // create HttpPost and HttpClient object
                post = new HttpDelete(startJobUrl+job);
                client = HttpClientBuilder.create().build();

                // add HTTP headers
                post.setHeader("Accept", "text/json");
                post.setHeader("Accept-Charset", "UTF-8");

                // set Authorization header based on the API key
                post.setHeader("Authorization", ("Bearer "+apikey));

                // Call REST API and retrieve response content
                HttpResponse authResponse = client.execute(post);

                if (authResponse.getEntity()==null)
                {
                    return authResponse.getStatusLine().toString();
                }
                return EntityUtils.toString(authResponse.getEntity());

            }
            catch (Exception e) {

                return e.toString();
            }
        }

### <a name="other-programming-environments"></a>Outros ambientes de programação
Você também pode gerar o código em muitas outras linguagens ao seguir as instruções fornecidas no site [swagger.io](http://swagger.io/). Para um serviço Web clássico, você pode obter o documento de swagger:

* Na página de ajuda da API 
* Chamando Obter documento da API para ponto de extremidade, encontrada na página da API do Swagger do portal de serviços Web do Machine Learning. 

Acesse o site [swagger.io](http://swagger.io/swagger-codegen/) e siga as instruções para baixar o código do swagger, o java e o apache mvn. 

A lista a seguir é o resumo de instruções sobre como configurar o swagger para outros ambientes de programação.

* Verifique se o Java 7 ou superior está instalado
* Instale o apache mvn (no ubuntu, você pode usar *apt-get install mvn*)
* Vá para o github a fim de obter o swagger e baixe o projeto do swagger como um arquivo zip
* Descompacte o swagger
* Crie ferramentas do swagger ao executar o *pacote mvn* do diretório de origem do swagger

Agora você pode usar todas as ferramentas do swagger. Aqui estão as instruções para gerar código de cliente Java. 

* Vá para a página de Ajuda da API do AM do Azure (exemplo [aqui](https://studio.azureml.net/apihelp/workspaces/afbd553b9bac4c95be3d040998943a4f/webservices/4dfadc62adcc485eb0cf162397fb5682/endpoints/26a3afce1767461ab6e73d5a206fbd62/jobs))
* Encontre a URL do swagger.json para APIs REST do AM do Azure (penúltimo marcador na parte superior da página da ajuda da API)
* Clique no link de documento do swagger (veja o exemplo [aqui](https://management.azureml.net/workspaces/afbd553b9bac4c95be3d040998943a4f/webservices/4dfadc62adcc485eb0cf162397fb5682/endpoints/26a3afce1767461ab6e73d5a206fbd62/apidocument))
* Use o comando a seguir, como mostrado no [arquivo Leiame do swagger](https://github.com/swagger-api/swagger-codegen/blob/master/README.md) , para gerar o código do cliente

**Exemplo de linha de comando para gerar o código cliente**

    java -jar swagger-codegen-cli.jar generate\
     -i https://ussouthcentral.services.azureml.net:443/workspaces/\
    fb62b56f29fc4ba4b8a8f900c9b89584/services/26a3afce1767461ab6e73d5a206fbd62/swagger.json\
     -l java -o /home/username/sample

* Combine os valores nos campos host, basePath e "/swagger.json" no exemplo de uma [página de Ajuda da API](https://management.azureml.net/workspaces/afbd553b9bac4c95be3d040998943a4f/webservices/4dfadc62adcc485eb0cf162397fb5682/endpoints/26a3afce1767461ab6e73d5a206fbd62/apidocument) do swagger mostrada abaixo para construir a URL do swagger usada na linha de comando anterior.

**Página de Ajuda da API de exemplo**

    {
      "swagger": "2.0",
      "info": {
        "version": "2.0",
        "title": "Sample 5: Binary Classification with Web service: Adult Dataset [Predictive Exp.]",
        "description": "No description provided for this Web service.",
        "x-endpoint-name": "default"
      },
      "host": "ussouthcentral.services.azureml.net:443",
      "basePath": "/workspaces/afbd553b9bac4c95be3d040998943a4f/services/26a3afce1767461ab6e73d5a206fbd62",
      "schemes": [
        "https"
      ],
      "consumes": [
        "application/json"
      ],
      "produces": [
        "application/json"
      ],
      "paths": {
        "/swagger.json": {
          "get": {
            "summary": "Get swagger API document for the Web service",
            "operationId": "getSwaggerDocument",

<!-- Relative Links -->

[publicar]: machine-learning-publish-a-machine-learning-web-service.md
[passo a passo]: machine-learning-walkthrough-develop-predictive-solution.md

<!-- External Links -->
[webservicesportal]: https://services.azureml.net/
[mlstudio]: https://studio.azureml.net



<!--HONumber=Nov16_HO3-->


