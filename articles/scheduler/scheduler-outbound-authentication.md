<properties 
 pageTitle="Autenticação de Saída do Agendador" 
 description="" 
 services="scheduler" 
 documentationCenter=".NET" 
 authors="krisragh" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="scheduler" 
 ms.workload="infrastructure-services" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="08/04/2015" 
 ms.author="krisragh"/>
 
# Autenticação de Saída do Agendador

Os trabalhos do Agendador podem precisar chamar serviços que requerem autenticação. Dessa forma, um serviço chamado pode determinar se o trabalho do Agendador poderá acessar seus recursos. Alguns desses serviços incluem outros serviços do Azure, Salesforce.com, Facebook e sites seguros personalizados.

## Adição e Remoção de Autenticação

Adicionar a autenticação para um trabalho do Agendador é simples: adicione um elemento filho JSON `authentication` para o elemento `request` ao criar ou atualizar um trabalho. Os segredos passados para o serviço do Agendador em uma solicitação PUT, PATCH ou POST, como parte do objeto `authentication`, nunca são retornados em respostas. Nas respostas, as informações secretas são definidas como nulo ou podem ter um token público que representa a entidade autenticada.

Para remover a autenticação, faça PUT ou PATCH do trabalho explicitamente, configurando o objeto `authentication` como nulo. Você não verá quaisquer propriedades de autenticação na resposta.

Atualmente, os únicos tipos de autenticação com suporte são o modelo `ClientCertificate` (para uso dos certificados de cliente SSL/TLS), o modelo `Basic` (para autenticação básica) e o modelo `ActiveDirectoryOAuth` (para autenticação OAuth do Active Directory.)

## Corpo da solicitação de autenticação ClientCertificate

Ao adicionar a autenticação usando o modelo `ClientCertificate`, especifique os seguintes elementos adicionais no corpo da solicitação.

|Elemento|Descrição|
|:---|:---|
|_autenticação (elemento pai)_|Objeto de autenticação para usar um certificado de cliente SSL.|
|_tipo_|Obrigatório. Tipo de autenticação. Para certificados de cliente SSL, o valor deve ser `ClientCertificate`.|
|_pfx_|Obrigatório. Conteúdo codificado na Base64 do arquivo PFX.|
|_senha_|Obrigatório. Senha para acessar o arquivo PFX.|


## Corpo da resposta para autenticação ClientCertificate

Quando uma solicitação é enviada com as informações de autenticação, a resposta contém os seguintes elementos de autenticação.

|Elemento |Descrição |
|:--|:--|
|_autenticação (elemento pai)_ |Objeto de autenticação para usar um certificado de cliente SSL.|
|_tipo_ |Tipo de autenticação. Para certificados de cliente SSL, o valor é `ClientCertificate`.|
|_certificateThumbprint_ |A impressão digital do certificado.|
|_certificateSubjectName_ |O nome distinto da entidade do certificado.|
|_certificateExpiration_ |A data de validade do certificado.|

## Exemplo de solicitação e resposta para autenticação ClientCertificate

A solicitação de exemplo a seguir faz uma solicitação PUT que incorpora a autenticação `ClientCertificate`. A solicitação é a seguinte:


	PUT https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/cs-brazilsouth-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/testScheduler 
	x-ms-version: 2013-03-01
	User-Agent: Microsoft.WindowsAzure.Scheduler.SchedulerClient/3.0.0.0 AzurePowershell/v0.8.10
	Content-Type: application/json; charset=utf-8
	Host: management.core.windows.net
	Content-Length: 4013
	Expect: 100-continue

	{
	  "action": {
		"type": "http",
		"request": {
		  "uri": "https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/CS-NorthCentralUS-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		  "authentication": {
			"type": "clientcertificate",
			"password": "test",
			"pfx": "long-pfx-key”
		  }
		}
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  }
	}

Depois que essa solicitação é enviada, a resposta é a seguinte:

	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 721
	Content-Type: application/json; charset=utf-8
	Expires: -1
	Server: 1.0.6198.153 (rd_rdfe_stable.141027-2149) Microsoft-HTTPAPI/2.0
	x-ms-servedbyregion: ussouth2
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET
	 

	{
	  "id": "testScheduler",
	  "action": {
		"request": {
		  "uri": "https:\/\/management.core.windows.net\/7e2dffb5-45b5-475a-91be-d3d9973c82d5\/cloudservices\/CS-NorthCentralUS-scheduler\/resources\/scheduler\/~\/JobCollections\/testScheduler\/jobs\/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		  "authentication": {
			"type": "ClientCertificate",
			"certificateThumbprint": "C1645E2AF6317D9FCF9C78FE23F9DE0DAFAD2AB5",
			"certificateExpiration": "2021-01-01T08:00:00Z",
			"certificateSubjectName": "CN=Scheduler Management"
		  }
		},
		"type": "http"
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  },
	  "state": "enabled",
	  "status": {
		"nextExecutionTime": "2014-10-29T21:52:35.2108904Z",
		"executionCount": 0,
		"failureCount": 0,
		"faultedCount": 0
	  }
	}
## Corpo da solicitação de autenticação básica

Ao adicionar a autenticação usando o modelo `Basic`, especifique os seguintes elementos adicionais no corpo da solicitação.

|Elemento|Descrição|
|:--|:--|
|_autenticação (elemento pai)_ |Objeto de autenticação para usar a autenticação básica.|
|_tipo_ |Obrigatório. Tipo de autenticação. Para a autenticação básica, o valor deve ser `Basic`.|
|_nome de usuário_ |Obrigatório. Nome de usuário para autenticação.|
|_senha_ |Obrigatório. Senha para autenticação.|

## Corpo da resposta de autenticação básica

Quando uma solicitação é enviada com as informações de autenticação, a resposta contém os seguintes elementos de autenticação.

|Elemento|Descrição|
|:--|:--|
|_autenticação (elemento pai)_ |Objeto de autenticação para usar a autenticação básica.|
|_tipo_ |Tipo de autenticação. Para a autenticação básica, o valor é `Basic`.|
|_nome de usuário_ |O nome de usuário autenticado.|

## Exemplo de solicitação e resposta para autenticação básica

A solicitação de exemplo a seguir faz uma solicitação PUT que incorpora a autenticação `Basic`. A solicitação é a seguinte:

	PUT https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/cs-brazilsouth-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/testScheduler 
	x-ms-version: 2013-03-01
	User-Agent: Microsoft.WindowsAzure.Scheduler.SchedulerClient/3.0.0.0 AzurePowershell/v0.8.10
	Content-Type: application/json; charset=utf-8
	Host: management.core.windows.net
	Expect: 100-continue

	{
	  "action": {
		"type": "http",
		"request": {
		  "uri": "https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/CS-NorthCentralUS-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		"authentication":{  
		  "username":"user1",
		  "password":"password",
		  "type":"basic"
		  }           
		}
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  }
	}

Depois que essa solicitação é enviada, a resposta é a seguinte:

	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 721
	Content-Type: application/json; charset=utf-8
	Expires: -1
	Server: 1.0.6198.153 (rd_rdfe_stable.141027-2149) Microsoft-HTTPAPI/2.0
	x-ms-servedbyregion: ussouth2
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET

	{
	  "id": "testScheduler",
	  "action": {
		"request": {
		  "uri": "https:\/\/management.core.windows.net\/7e2dffb5-45b5-475a-91be-d3d9973c82d5\/cloudservices\/CS-NorthCentralUS-scheduler\/resources\/scheduler\/~\/JobCollections\/testScheduler\/jobs\/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		  "authentication":{  
			"username":"user1",
			"type":"Basic"
		  }
		},
		"type": "http"
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  },
	  "state": "enabled",
	  "status": {
		"nextExecutionTime": "2014-10-29T21:52:35.2108904Z",
		"executionCount": 0,
		"failureCount": 0,
		"faultedCount": 0
	  }
	}

## Corpo da solicitação de autenticação ActiveDirectoryOAuth

Ao adicionar a autenticação usando o modelo `ActiveDirectoryOAuth`, especifique os seguintes elementos adicionais no corpo da solicitação.

|Elemento |Descrição |
|:--|:--|
|_autenticação (elemento pai)_ |Objeto de autenticação para usar a autenticação ActiveDirectoryOAuth.|
|_tipo_ |Obrigatório. Tipo de autenticação. Para autenticação de ActiveDirectoryOAuth, o valor deve ser `ActiveDirectoryOAuth`.|
|_locatário_ |Obrigatório. O identificador do locatário é uma ID usada para identificar o locatário do AD.|
|_audiência_ |Obrigatório. Isso é definido como https://management.core.windows.net/.|
|_clientId_ |Obrigatório. Forneça o identificador de cliente para o aplicativo do AD do Azure.|
|_segredo_ |Obrigatório. Segredo do cliente que está solicitando o token.|

## Corpo da resposta de autenticação ActiveDirectoryOAuth

Quando uma solicitação é enviada com as informações de autenticação, a resposta contém os seguintes elementos de autenticação.

|Elemento |Descrição |
|:--|:--|
|_autenticação (elemento pai)_ |Objeto de autenticação para usar a autenticação ActiveDirectoryOAuth.|
|_tipo_ |Tipo de autenticação. Para autenticação de ActiveDirectoryOAuth, o valor é `ActiveDirectoryOAuth`.|
|_locatário_ |O identificador do locatário usado para identificar o locatário do AD.|
|_audiência_ |Isso é definido como https://management.core.windows.net/.|
|_clientId_ |O identificador de cliente para o aplicativo do AD do Azure.|

## Solicitação de exemplo e resposta para autenticação de ActiveDirectoryOAuth

A solicitação de exemplo a seguir faz uma solicitação PUT que incorpora a autenticação `ActiveDirectoryOAuth`. A solicitação é a seguinte:

	PUT https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/cs-brazilsouth-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/testScheduler 
	x-ms-version: 2013-03-01
	User-Agent: Microsoft.WindowsAzure.Scheduler.SchedulerClient/3.0.0.0 AzurePowershell/v0.8.10
	Content-Type: application/json; charset=utf-8
	Host: management.core.windows.net
	Expect: 100-continue

	{
	  "action": {
		"type": "http",
		"request": {
		  "uri": "https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/CS-NorthCentralUS-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		  "authentication":{  
			"tenant":"contoso.com",
			"audience":"https://management.core.windows.net/",
			"clientId":"8a14db88-4d1a-46c7-8429-20323727dfab",
			"secret": "&lt;secret-key&gt;",
			"type":"ActiveDirectoryOAuth"
		  }                      
		}
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  }
	}

Depois que essa solicitação é enviada, a resposta é a seguinte:

	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 721
	Content-Type: application/json; charset=utf-8
	Expires: -1
	Server: 1.0.6198.153 (rd_rdfe_stable.141027-2149) Microsoft-HTTPAPI/2.0
	x-ms-servedbyregion: ussouth2
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET


	{
	  "id": "testScheduler",
	  "action": {
		"request": {
		  "uri": "https:\/\/management.core.windows.net\/7e2dffb5-45b5-475a-91be-d3d9973c82d5\/cloudservices\/CS-NorthCentralUS-scheduler\/resources\/scheduler\/~\/JobCollections\/testScheduler\/jobs\/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		  "authentication":{  
			"tenant":"contoso.com",
			"audience":"https://management.core.windows.net/",
			"clientId":"8a14db88-4d1a-46c7-8429-20323727dfab",
			"type":"ActiveDirectoryOAuth"
		  }
		},
		"type": "http"
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  },
	  "state": "enabled",
	  "status": {
		"nextExecutionTime": "2014-10-29T21:52:35.2108904Z",
		"executionCount": 0,
		"failureCount": 0,
		"faultedCount": 0
	  }
	}

## Consulte também
 
 [O que é o Agendador?](scheduler-intro.md)
 
 [Conceitos, terminologia e hierarquia de entidades do Agendador](scheduler-concepts-terms.md)
 
 [Introdução à utilização do Agendador no Portal de Gerenciamento](scheduler-get-started-portal.md)
 
 [Planos e cobrança no Agendador do Azure](scheduler-plans-billing.md)
 
 [Como criar agendas complexas e recorrência avançada com o Agendador do Azure](scheduler-advanced-complexity.md)
 
 [Referência da API REST do Agendador](https://msdn.microsoft.com/library/dn528946)
 
 [Referência de cmdlets do PowerShell do Agendador](scheduler-powershell-reference.md)
 
 [Alta disponibilidade e confiabilidade do Agendador](scheduler-high-availability-reliability.md)
 
 [Limites, padrões e códigos de erro do Agendador](scheduler-limits-defaults-errors.md)
 
  

<!---HONumber=August15_HO6-->