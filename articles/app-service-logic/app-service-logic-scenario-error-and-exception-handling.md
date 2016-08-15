<properties 
    pageTitle="Tratamento de logs e de erros nos Aplicativos Lógicos | Microsoft Azure" 
    description="Exibir um caso de uso real de tratamento e registro em log avançados de erros com Aplicativos Lógicos" 
    keywords=""
    services="logic-apps" 
    authors="hedidin" 
    manager="" 
    editor="" 
    documentationCenter=""/>
    
<tags 
    ms.service="logic-apps" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/29/2016" 
    ms.author="b-hoedid"/>
    
# Registro em log e tratamento de erros nos Aplicativos Lógicos 

Este artigo apresenta detalhes sobre como você pode estender um aplicativo lógico para dar um suporte melhor ao tratamento de exceções. É um caso de uso real e nossa resposta à pergunta **Os Aplicativos Lógicos oferecem suporte ao tratamento de exceções e de erros?**

>[AZURE.NOTE] A versão atual de Aplicativos Lógicos fornece um modelo padrão para respostas de ação. Isso inclui validação interna e respostas de erro retornadas de um Aplicativo de API.

## Visão geral do cenário e do caso de uso

A história a seguir é o caso de uso para este artigo.

> Uma organização de saúde bem conhecida solicitou a criação de uma solução do Azure para criar um portal de pacientes usando o CRMOL (Dynamics CRM Online). Naquele momento, eles estavam utilizando o Salesforce e precisávamos enviar os registros de consulta entre o portal de pacientes do CRMOL e o Salesforce. Foi solicitado que usássemos o padrão [HL7 FHIR](http://www.hl7.org/implement/standards/fhir/) para todos os registros de pacientes.

> Havia dois requisitos principais que precisávamos incluir:
> -  Log de registros enviados do Portal do CRMOL
> -  Eles precisavam de uma maneira de exibir os erros ocorridos no fluxo de trabalho.


## Como solucionamos o problema

>[AZURE.TIP] Você pode exibir um vídeo de alto nível no [Grupo de Usuários de Integração](http://www.integrationusergroup.com/do-logic-apps-support-error-handling/ "Grupo de usuários de integração")

Decidimos usar o **[Banco de Dados de Documentos do Azure](https://azure.microsoft.com/services/documentdb/ "Banco de Dados de Documentos do Azure")** como um repositório para os registros de log e de erros. (Banco de Dados de Documentos refere-se aos registros como documentos). Como os Aplicativos Lógicos têm um modelo padrão para todas as respostas, não seria necessário criar um esquema personalizado. Nós poderíamos criar um Aplicativo de API para Inserir e Consultar registros de Erro e de Log. Também poderíamos definir um esquema para cada um deles no Aplicativo de API.

Nós tínhamos outros requisitos para eliminar registros após uma determinada data. O Banco de Dados de Documentos tem uma propriedade [Time-To-Live](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "Vida Útil") (ttl), que nos permitiu definir um valor **Time-to-Live** para cada registro ou em uma coleção inteira. Isso elimina a necessidade de excluir manualmente os registros do Banco de Dados de Documentos.

### Nossa abordagem

A primeira etapa também é criar nosso Aplicativo Lógico e carregá-lo no designer. Neste exemplo, estamos usando Aplicativos Lógicos Pai--Filho. Vamos supor que já tenhamos criado o pai. Vamos criar um aplicativo lógico filho.

Já que vamos registrar em log o registro vindo do CRMOL, começaremos da parte superior. Precisamos usar um Gatilho de Solicitação, já que o aplicativo lógico pai irá disparar esse filho.

> [AZURE.IMPORTANT] Para concluir este tutorial, você precisará criar um Banco de Dados de Documentos e duas Coleções (Registro em Log e de Erros)

### Gatilho de Aplicativo Lógico 

**Estamos usando um Gatilho de Solicitação como mostrado abaixo**

```` json
"triggers": {
        "request": {
          "type": "request",
          "kind": "http",
          "inputs": {
            "schema": {
              "properties": {
                "CRMid": {
                  "type": "string"
                },
                "recordType": {
                  "type": "string"
                },
                "salesforceID": {
                  "type": "string"
                },
                "update": {
                  "type": "boolean"
                }
              },
              "required": [
                "CRMid",
                "recordType",
                "salesforceID",
                "update"
              ],
              "type": "object"
            }
          }
        }
      },

````


### Etapas 

Vamos começar com o registro em log. É necessário registrar a origem (Solicitação) do registro de paciente do Portal do CRMOL.

1. Primeiro, precisamos **Obter um novo registro de consulta** do CRMOL. O gatilho proveniente do CRM fornecerá a **CRM PatentId**, o **tipo de registro**, o **Registro Novo ou Atualizado** (valor booliano novo ou atualizado) e a **SalesforceId**. A SalesforceId pode ser nula, uma vez que é usada somente para uma Atualização. Obteremos o registro do CRM usando a PatientID do CRM e o tipo de registro.
1. Em seguida, precisamos adicionar a operação **InsertLogEntry** do nosso Aplicativo de API do Banco de Dados de Documentos como mostrado nas figuras a seguir:


#### Inserir Entrada de Log no Modo de Exibição de Designer

![Inserir Entrada de Log](./media/app-service-logic-scenario-error-and-exception-handling/lognewpatient.png)

#### Inserir Entrada de Erro no Modo de Exibição de Designer
![Inserir Entrada de Log](./media/app-service-logic-scenario-error-and-exception-handling/insertlogentry.png)

#### Condição - verificação de falha em Criar Registro 

![Condição](./media/app-service-logic-scenario-error-and-exception-handling/condition.png)


## Código-fonte do Aplicativo Lógico 

>[AZURE.NOTE]  A seguir, mostraremos exemplos apenas. Devido à natureza de que o tutorial se baseia em uma implementação real atualmente em produção, o valor de um **nó de origem** não pode exibir as propriedades relacionadas ao agendamento de uma consulta.

### Registro em log
O exemplo de código de aplicativo de lógica a seguir mostra como lidar com registros em log

#### Inserir entrada de log
Este é o código-fonte do aplicativo lógico para a inserção de uma entrada de log

``` json
"InsertLogEntry": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "date": "@{outputs('Gets_NewPatientRecord')['headers']['Date']}",
            "operation": "New Patient",
            "patientId": "@{triggerBody()['CRMid']}",
            "providerId": "@{triggerBody()['providerID']}",
            "source": "@{outputs('Gets_NewPatientRecord')['headers']}"
        },
        "method": "post",
        "uri": "https://.../api/Log"
        },
        "runAfter":    {
            "Gets_NewPatientecord": ["Succeeded"]
        }
}
```

#### Solicitação de log

Esta é a mensagem de solicitação de log postada para o Aplicativo de API

``` json
    {
    "uri": "https://.../api/Log",
    "method": "post",
    "body": {
	    "date": "Fri, 10 Jun 2016 22:31:56 GMT",
	    "operation": "New Patient",
	    "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
	    "providerId": "",
	    "source": "{"Pragma":"no-cache","x-ms-request-id":"e750c9a9-bd48-44c4-bbba-1688b6f8a132","OData-Version":"4.0","Cache-Control":"no-cache","Date":"Fri, 10 Jun 2016 22:31:56 GMT","Set-Cookie":"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1","Server":"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0","X-AspNet-Version":"4.0.30319","X-Powered-By":"ASP.NET","Content-Length":"1935","Content-Type":"application/json; odata.metadata=minimal; odata.streaming=true","Expires":"-1"}"
    	}
    }
    
```


#### Resposta de log

Esta é a mensagem de resposta de log postada para o Aplicativo de API

``` json
{
    "statusCode": 200,
    "headers": {
	    "Pragma": "no-cache",
	    "Cache-Control": "no-cache",
	    "Date": "Fri, 10 Jun 2016 22:32:17 GMT",
	    "Server": "Microsoft-IIS/8.0",
	    "X-AspNet-Version": "4.0.30319",
	    "X-Powered-By": "ASP.NET",
	    "Content-Length": "964",
	    "Content-Type": "application/json; charset=utf-8",
	    "Expires": "-1"
    },
    "body": {
	    "ttl": 2592000,
	    "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0_1465597937",
	    "_rid": "XngRAOT6IQEHAAAAAAAAAA==",
	    "_self": "dbs/XngRAA==/colls/XngRAOT6IQE=/docs/XngRAOT6IQEHAAAAAAAAAA==/",
	    "_ts": 1465597936,
	    "_etag": ""0400fc2f-0000-0000-0000-575b3ff00000"",
	    "patientID": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
	    "timestamp": "2016-06-10T22:31:56Z",
	    "source": "{"Pragma":"no-cache","x-ms-request-id":"e750c9a9-bd48-44c4-bbba-1688b6f8a132","OData-Version":"4.0","Cache-Control":"no-cache","Date":"Fri, 10 Jun 2016 22:31:56 GMT","Set-Cookie":"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1","Server":"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0","X-AspNet-Version":"4.0.30319","X-Powered-By":"ASP.NET","Content-Length":"1935","Content-Type":"application/json; odata.metadata=minimal; odata.streaming=true","Expires":"-1"}",
	    "operation": "New Patient",
	    "salesforceId": "",
	    "expired": false
    }
}
    
```

Agora vejamos as etapas de tratamento de erros:

----------    
    
### Tratamento de erros

O exemplo de código de aplicativo de lógica a seguir mostra como você pode implementar o tratamento de erros.

#### Criar registro de erro

Este é o código-fonte do Aplicativo Lógico para a criação de um registro de erro.

``` json
"actions": {
    "CreateErrorRecord": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "action": "New_Patient",
            "isError": true,
            "crmId": "@{triggerBody()['CRMid']}",
            "patientID": "@{triggerBody()['CRMid']}",
            "message": "@{body('Create_NewPatientRecord')['message']}",
            "providerId": "@{triggerBody()['providerId']}",
            "severity": 4,
            "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
            "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
            "salesforceId": "",
            "update": false
        },
        "method": "post",
        "uri": "https://.../api/CrMtoSfError"
        },
        "runAfter": 
        {
            "Create_NewPatientRecord": ["Failed" ]
        }
    }
}  	       
```

#### Inserir erro no Banco de Dados de Documentos - solicitação

``` json
  
{
    "uri": "https://.../api/CrMtoSfError",
    "method": "post",
    "body": {
        "action": "New_Patient",
        "isError": true,
        "crmId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "providerId": "",
        "severity": 4,
        "salesforceId": "",
        "update": false,
        "source": "{"Account_Class_vod__c":"PRAC","Account_Status_MED__c":"I","CRM_HUB_ID__c":"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0","Credentials_vod__c","DTC_ID_MED__c":"","Fax":"","FirstName":"A","Gender_vod__c":"","IMS_ID__c":"","LastName":"BAILEY","MasterID_mp__c":"","C_ID_MED__c":"851588","Middle_vod__c":"","NPI_vod__c":"","PDRP_MED__c":false,"PersonDoNotCall":false,"PersonEmail":"","PersonHasOptedOutOfEmail":false,"PersonHasOptedOutOfFax":false,"PersonMobilePhone":"","Phone":"","Practicing_Specialty__c":"FM - FAMILY MEDICINE","Primary_City__c":"","Primary_State__c":"","Primary_Street_Line2__c":"","Primary_Street__c":"","Primary_Zip__c":"","RecordTypeId":"012U0000000JaPWIA0","Request_Date__c":"2016-06-10T22:31:55.9647467Z","ONY_ID__c":"","Specialty_1_vod__c":"","Suffix_vod__c":"","Website":""}",
        "statusCode": "400"
    }
}
```

#### Inserir erro no Banco de Dados de Documentos - resposta


``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:57 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "1561",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0-1465597917",
        "_rid": "sQx2APhVzAA8AAAAAAAAAA==",
        "_self": "dbs/sQx2AA==/colls/sQx2APhVzAA=/docs/sQx2APhVzAA8AAAAAAAAAA==/",
        "_ts": 1465597912,
        "_etag": ""0c00eaac-0000-0000-0000-575b3fdc0000"",
        "prescriberId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:57.3651027Z",
        "action": "New_Patient",
        "salesforceId": "",
        "update": false,
        "body": "CRM failed to complete task: Message: duplicate value found: CRM_HUB_ID__c duplicates value on record with id: 001U000001c83gK",
        "source": "{"Account_Class_vod__c":"PRAC","Account_Status_MED__c":"I","CRM_HUB_ID__c":"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0","Credentials_vod__c":"DO - Degree level is DO","DTC_ID_MED__c":"","Fax":"","FirstName":"A","Gender_vod__c":"","IMS_ID__c":"","LastName":"BAILEY","MterID_mp__c":"","Medicis_ID_MED__c":"851588","Middle_vod__c":"","NPI_vod__c":"","PDRP_MED__c":false,"PersonDoNotCall":false,"PersonEmail":"","PersonHasOptedOutOfEmail":false,"PersonHasOptedOutOfFax":false,"PersonMobilePhone":"","Phone":"","Practicing_Specialty__c":"FM - FAMILY MEDICINE","Primary_City__c":"","Primary_State__c":"","Primary_Street_Line2__c":"","Primary_Street__c":"","Primary_Zip__c":"","RecordTypeId":"012U0000000JaPWIA0","Request_Date__c":"2016-06-10T22:31:55.9647467Z","XXXXXXX":"","Specialty_1_vod__c":"","Suffix_vod__c":"","Website":""}",
        "code": 400,
        "errors": null,
        "isError": true,
        "severity": 4,
        "notes": null,
        "resolved": 0
        }
}
```

#### Resposta de erro do Salesforce

``` json
{
    "statusCode": 400,
    "headers": {
        "Pragma": "no-cache",
        "x-ms-request-id": "3e8e4884-288e-4633-972c-8271b2cc912c",
        "X-Content-Type-Options": "nosniff",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "Set-Cookie": "ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1",
        "Server": "Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "205",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "status": 400,
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "source": "Salesforce.Common",
        "errors": []
    }
}
    
```

### Retornar a resposta ao aplicativo lógico pai

Assim que você tiver a resposta, poderá passá-la para o aplicativo lógico pai.

#### Retornar resposta de êxito para aplicativo lógico pai

``` json
"SuccessResponse": {
    "runAfter": 
        {
            "UpdateNew_CRMPatientResponse": ["Succeeded"]
        },
    "inputs": {
        "body": {
            "status": "Success"
    },
    "headers": {
    "	Content-type": "application/json",
        "x-ms-date": "@utcnow()"
    },
    "statusCode": 200
    },
    "type": "Response"
}
```

#### Retornar resposta de erro para aplicativo lógico pai

``` json
"ErrorResponse": {
    "runAfter":
        {
            "Create_NewPatientRecord": ["Failed"]
        },
    "inputs": {
        "body": {
            "status": "BadRequest"
        },
        "headers": {
            "Content-type": "application/json",
            "x-ms-date": "@utcnow()"
        },
        "statusCode": 400
    },
    "type": "Response"
}
  
```

----------
  
## Repositório e portal do Banco de Dados de Documentos

Nossa solução adicionou outros recursos com o [Banco de Dados de Documentos](https://azure.microsoft.com/services/documentdb)

### Portal de gerenciamento de erros

Para exibir os erros, você pode criar um Aplicativo Web do MVC para exibir os registros de erro do Banco de Dados de Documentos. Para a versão atual, as operações **Listar**, **Detalhes**, **Editar** e **Excluir** foram incluídas.

> [AZURE.NOTE] Operação Editar: o Banco de Dados de Documentos faz uma substituição de todo o documento. Os registros mostrados nos modos de exibição de Listar e de Detalhe são apenas exemplos. Eles não são registros reais de consultas de pacientes
        
A seguir, exemplos de detalhes do nosso Aplicativo MVC usando a abordagem acima.

#### Lista de gerenciamento de erros

![Lista de Erros](./media/app-service-logic-scenario-error-and-exception-handling/errorlist.png)
        
#### Modo de exibição de detalhes do gerenciamento de erros

![Detalhes do Erro](./media/app-service-logic-scenario-error-and-exception-handling/errordetails.png)

### Portal de gerenciamento de logs

Para exibir os logs, também criamos um aplicativo Web do MVC. A seguir, exemplos de detalhes do nosso Aplicativo MVC usando a abordagem acima.

#### Modo de exibição de detalhes de log de exemplo

![Modo de Exibição de Detalhes de Log](./media/app-service-logic-scenario-error-and-exception-handling/samplelogdetail.png)
    
### Detalhes do Aplicativo de API

#### API de gerenciamento de exceções de Aplicativos Lógicos

Nosso Aplicativo de API de gerenciamento de exceções de Aplicativos Lógicos de software livre forneceu a funcionalidade a seguir.

Há dois controladores
- ErrorController insere um registro de erro (documento) em uma coleção do Banco de Dados de Documentos
- LogController insere um registro de log (documento) em uma coleção do Banco de Dados de Documentos

> [AZURE.TIP] Ambos os controladores usam operações `async Task<dynamic>`. Isso permite que as operações sejam resolvidas em tempo de execução, de forma que possamos criar o esquema do Banco de Dados de Documentos no corpo da operação.

Todos os documentos do Banco de Dados de Documentos devem ter uma ID exclusiva. Estamos usando o `PatientId` e adicionando um carimbo de data/hora convertido em um valor de carimbo de data/hora do Unix (double). Nós o truncamos para remover o valor fracionário.

Você pode exibir o código-fonte da nossa API de controlador de erros [do GitHub aqui](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/Logic App Exception Management API/Controllers/ErrorController.cs)

Vamos chamar a API de um aplicativo lógico usando a seguinte sintaxe:

``` json
 "actions": {
        "CreateErrorRecord": {
          "metadata": {
            "apiDefinitionUrl": "https://.../swagger/docs/v1",
            "swaggerSource": "website"
          },
          "type": "Http",
          "inputs": {
            "body": {
              "action": "New_Patient",
              "isError": true,
              "crmId": "@{triggerBody()['CRMid']}",
              "prescriberId": "@{triggerBody()['CRMid']}",
              "message": "@{body('Create_NewPatientRecord')['message']}",
              "salesforceId": "@{triggerBody()['salesforceID']}",
              "severity": 4,
              "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
              "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
              "update": false
            },
            "method": "post",
            "uri": "https://.../api/CrMtoSfError"
          },
          "runAfter": {
              "Create_NewPatientRecord": ["Failed"]
            }
        }
 }
```

A expressão no exemplo de código acima está verificando o status de *Create\_NewPatientRecord*, que é **Falha**

## Resumo 

- Você pode implementar facilmente o tratamento de logs e de erros em um aplicativo lógico.
- Você pode aproveitar o Banco de Dados de Documentos como o repositório de registros de log e de erros (documentos)
- Você pode usar o MVC para criar um portal para exibir os registros de log e de erros.

### Código-fonte
O código-fonte para o Aplicativo de API de Gerenciamento de Exceções de Aplicativos Lógicos está disponível neste [Repositório GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "API de Gerenciamento de Exceções de Aplicativos Lógicos").


## Próximas etapas 
- [Exibir mais exemplos e cenários dos Aplicativos Lógicos](app-service-logic-examples-and-scenarios.md)
- [Saiba mais sobre ferramentas de monitoramento de Aplicativos Lógicos](app-service-logic-monitor-your-logic-apps.md)
- [Criar um modelo de implantação de Aplicativos Lógicos](app-service-logic-create-deploy-template.md)

<!---HONumber=AcomDC_0803_2016-->