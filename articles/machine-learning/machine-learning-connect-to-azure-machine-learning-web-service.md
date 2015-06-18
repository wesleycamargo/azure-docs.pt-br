<properties 
	pageTitle="Conectar a um Serviço Web de Aprendizado de Máquina do Azure | Azure" 
	description="Com C# ou Python, conecte a um serviço Web de Aprendizado de Máquina do Azure usando uma chave de autorização." 
	services="machine-learning" 
	documentationCenter="" 
	authors="derrickv" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="derrickv" />


# Conectar a um Serviço Web de Aprendizado de Máquina do Azure 
A experiência do desenvolvedor de Aprendizado de Máquina do Azure é uma API de serviço Web para fazer previsões de dados de entrada em tempo real ou em modo de lote.  Você pode usar o Estúdio de Aprendizado de Máquina do Azure (Estúdio AM) para criar previsões e publicar um serviço Web do AM do Azure. 

Para obter informações sobre como criar e publicar um serviço Web de Aprendizado de Máquina do Azure usando o Estúdio AM:

- [Visão geral do Processo AM do Azure](../machine-learning-overview-of-azure-ml-process.md)
- [Introdução ao Estúdio AM](http://azure.microsoft.com/documentation/videos/getting-started-with-ml-studio/)
- [Visualização de Aprendizado de Máquina do Azure](https://studio.azureml.net/)
- [Centro de Documentação do Aprendizado de Máquina](http://azure.microsoft.com/documentation/services/machine-learning/)

## Serviço Web de Aprendizado de Máquina do Azure ##

Com o serviço Web de Aprendizado de Máquina do Azure (AM), um aplicativo externo se comunica com um modelo de pontuação do fluxo de trabalho de AM em tempo real.  Uma chamada de serviço Web AM retorna resultados de previsão para um aplicativo externo.  Para fazer uma chamada de serviço Web AM, passe uma chave de API que é criada quando você publica uma previsão.  O serviço Web AM baseia-se em REST, uma opção popular de arquitetura para projetos de programação da Web.

O Aprendizado de Máquina do Azure tem dois tipos de serviços:

- Serviço de Solicitação-Resposta (RR) - Um serviço de baixa latência e altamente escalável que fornece uma interface para os modelos sem monitoração de estado criados e publicados no Estúdio AM.
- Serviço de Execução de Lote (BES) - Um serviço assíncrono que pontua um lote de registros de dados.

Para obter mais informações sobre os serviços Web de Aprendizado de Máquina do Azure, consulte [Visão geral do processo de ML do Azure](../machine-learning-overview-of-azure-ml-process.md).

## Obtenha uma chave de autorização de Aprendizado de Máquina do Azure ##
Você pode obter uma chave de API do serviço Web de um serviço Web AM.  Você pode obtê-la no Estúdio de Aprendizado de Máquina do Microsoft Azure ou no Portal de Gerenciamento do Azure.
### Estúdio de Aprendizado de Máquina do Microsoft Azure ###
1. No Estúdio de Aprendizado de Máquina do Microsoft Azure, clique em **SERVIÇOS WEB** à esquerda.
2. Clique em um serviço Web.  A "chave de API" está na guia **PAINEL**.

### Portal de Gerenciamento do Azure ###

1. Clique em **APRENDIZADO DE MÁQUINA** à esquerda.
2. Clique em um espaço de trabalho.
3. Clique em **SERVIÇOS WEB**.
4. Clique em um serviço Web.
5. Clique em um ponto de extremidade.  A "CHAVE DE API" está mais abaixo na parte inferior direita.

## <a id="connect"></a>Conectar a um serviço Web de Aprendizado de Máquina do Azure

Você pode conectar a um serviço Web de Aprendizado de Máquina do Azure usando qualquer linguagem de programação que dá suporte à resposta e solicitação HTTP.  Você pode exibir exemplos em C#, Python e R de uma página de Ajuda do serviço Web do AM do Azure.

### Para exibir uma página de Ajuda da API do serviço Web do AM do Azure ###
Uma página de Ajuda da API do AM do Azure é criada quando você publica um serviço Web.  Consulte [Passo a passo de Aprendizado de Máquina do Azure - Publicar Serviço Web](machine-learning-walkthrough-5-publish-web-service.md).


**Para exibir uma página de Ajuda da API do AM do Azure**
No Estúdio de Aprendizado de Máquina do Microsoft Azure:

1. Escolha **SERVIÇOS WEB**.
2. Escolha um serviço Web.
3. Escolha a **página de Ajuda da API** - **SOLICITAÇÃO/RESPOSTA** ou **EXECUÇÃO EM LOTES**.


**Página de Ajuda da API do AM do Azure**
A página de Ajuda da API do AM do Azure contém detalhes sobre um serviço Web de previsão que inclui


<table>
	<tr>
		<td>&nbsp;</td>
		<td>Exemplo </td>
	</tr>
	<tr>
		<td>Solicitação POST URI </td>

		<td>https://ussouthcentral.services.azureml.net/workspaces/{WorkspaceId}/services/{ServiceId}/score
		</td>
	</tr>
	<tr>
		<td>Exemplo de Solicitação </td>
		<td>{ <br/> 
			&nbsp;&nbsp; "Id":  "score00001",   <br/>
			&nbsp;&nbsp; "Instance": <br/>
			&nbsp;&nbsp;&nbsp;&nbsp; {  <br/>  
 			&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp; "FeatureVector":  { <br/>
			&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;  "Col1":  "0", <br/>      
			&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;  "Col2":  "0", <br/>      
			&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;  "Col3":  "0", <br/>  
			&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;  ...     },   <br/>
			&nbsp;&nbsp;&nbsp;&nbsp;   "GlobalParameters":  {}   <br/>
			&nbsp;&nbsp;&nbsp;&nbsp; } <br/>
		}</td>
	</tr>
	<tr>
		<td>Corpo da resposta </td>
		<td>
		<table style="width: 100%">

			<tr>
				<td><B>Nome</B></td>
				<td><B>Tipo de Dados</B></td>
			</tr>
	
			<tr>
				<td>Recurso</td>
				<td>Cadeia de caracteres</td>
			</tr>
			<tr>
				<td>Contagem</td>
				<td>Numérico</td>
			</tr>
			<tr>
				<td>Contagem de valores exclusivos </td>
				<td>Numérico </td>
			</tr>
			<tr>
				<td>... </td>
				<td>... </td>
			</tr>
		</table>
		</td>
	</tr>
	<tr>
		<td>Exemplo de Resposta </td>
		<td>[&quot;Col1&quot;,&quot;1&quot;,&quot;1&quot;,...] </td>
	</tr>
	<tr>
		<td>Exemplo de código </td>
		<td>(Exemplo de código em C#, Python e R) </td>
	</tr>
</table>

**OBSERVAÇÃO** Os exemplos são do Exemplo 1:  Baixe o conjunto de dados de UCI:  Parte do conjunto de dados de classe adulto 2 da coleção de exemplos AM do Azure.

### Exemplo de C# ###

Para se conectar a um serviço Web do AM do Azure, use um **HttpClient** passando ScoreData.  ScoreData contém FeatureVector, um  vetor com n dimensões de recursos numéricos que representa o ScoreData.  Para autenticar o serviço do AM do Azure com uma chave de API.

Para se conectar a um serviço Web de AM, o pacote de Nuget **Microsoft.AspNet.WebApi.Client** deve ser instalado.

**Instalar o Nuget Microsoft.AspNet.WebApi.Client no Visual Studio**

1. Publicar o conjunto de dados de Download de UCI:  Serviço Web do conjunto de dados da classe Adulto 2.
2. Clique em **Ferramentas** > **◆Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.
2. Escolha **Install-Package Microsoft.AspNet.WebApi.Client**.

**Para executar o exemplo de código**

1. Publicar "Exemplo 1:  Baixe o conjunto de dados de UCI:  Conjunto de dados de classe adulto 2" um experimento que faz parte da coleção de exemplos do AM do Azure.
2. Atribua apiKey com a chave de um serviço Web.  Veja como obter uma chave de autorização AM do Azure.
3. Atribua serviceUri com o URI de solicitação.  Veja como obter um URI de solicitação.

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
	    public class ScoreData
	    {
	        public Dictionary<string, string> FeatureVector { get; set; }
	        public Dictionary<string, string> GlobalParameters { get; set; }
	    }
	
	    public class ScoreRequest
	    {
	        public string Id { get; set; }
	        public ScoreData Instance { get; set; }
	    }
	
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            InvokeRequestResponseService().Wait();
	
	            Console.ReadLine();
	        }
	
	        static async Task InvokeRequestResponseService()
	        {
	            //Assign apiKey with the key from a web service.
	            const string apiKey = "{ApiKey}";
	
	            //Assign serviceUri with the Request URI. See How to get a Request URI.
	            const string serviceUri = "{ServiceUri}";
	            
	            using (var client = new HttpClient())
	            {
	                ScoreData scoreData = new ScoreData()
	                {
	                    //Input data
	                    FeatureVector = new Dictionary<string, string>() 
	                    {
	                        { "Col1", "0" },
	                        { "Col2", "0" },
	                        { "Col3", "0" },
	                        { "Col4", "0" },
	                        { "Col5", "0" },
	                        { "Col6", "0" },
	                        { "Col7", "0" },
	                        { "Col8", "0" },
	                        { "Col9", "0" },
	                        { "Col10", "0" },
	                        { "Col11", "0" },
	                        { "Col12", "0" },
	                        { "Col13", "0" },
	                        { "Col14", "0" },
	                        { "Col15", "0" },
	                    },
	                    GlobalParameters = 
	                        new Dictionary<string, string>() {}
	                };
	
	                ScoreRequest scoreRequest = new ScoreRequest()
	                {
	                    Id = "score00001",
	                    Instance = scoreData
	                };
	
	                //Set authorization header
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
	             
	                client.BaseAddress = new Uri(serviceUrl);
	
	                //Post HTTP response message
	                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);
	
	                if (response.IsSuccessStatusCode)
	                {
	                    //Read result string
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


### Exemplo de Python ###

Para se conectar a um serviço Web do AM do Azure, use a biblioteca **urllib2** passando ScoreData.  ScoreData contém FeatureVector, um  vetor com n dimensões de recursos numéricos que representa o ScoreData.  Para autenticar o serviço do AM do Azure com uma chave de API.


**Para executar o exemplo de código**

1. Publicar "Exemplo 1:  Baixe o conjunto de dados de UCI:  Conjunto de dados de classe adulto 2" um experimento que faz parte da coleção de exemplos do AM do Azure.
2. Atribua apiKey com a chave de um serviço Web.  Veja como obter uma chave de autorização AM do Azure.
3. Atribua serviceUri com o URI de solicitação.  Veja como obter um URI de solicitação.

		import urllib2
		# If you are using Python 3+, import urllib instead of urllib2
	
		import json 
	
		data =  {
	            "Id": "score00001",
	            "Instance": {
	                "FeatureVector": {
	                    "Col1": "0",
	                    "Col2": "0",
	                    "Col3": "0",
	                    "Col4": "0",
	                    "Col5": "0",
	                    "Col6": "0",
	                    "Col7": "0",
	                    "Col8": "0",
	                    "Col9": "0",
	                    "Col10": "0",
	                    "Col11": "0",
	                    "Col12": "0",
	                    "Col13": "0",
	                    "Col14": "0",
	                    "Col15": "0",
	                },
	                "GlobalParameters": { }
	            }
	        }
	
		body = str.encode(json.dumps(data))
	
		#Assign serviceUrl with the Request URI. See How to get a Request URI.
		uri = '{ServiceUri}'
	
		#Assign apiKey with the key from a web service.
		api_key = '{ApiKey}'
		headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
	
		req = urllib2.Request(uri, body, headers) 
		response = urllib2.urlopen(req)
	
		#If you are using Python 3+, replace urllib2 with urllib.request in the above code:
		#req = urllib.request.Request(uri, body, headers) 
		#response = urllib.request.urlopen(req)
	
		result = response.read()
		print(result) 

<!--HONumber=49--> 