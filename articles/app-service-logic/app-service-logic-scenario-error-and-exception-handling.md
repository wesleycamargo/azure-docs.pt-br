---
title: "Tratamento de logs e de erros nos Aplicativos Lógicos | Microsoft Docs"
description: "Exibir um caso de uso real de tratamento e registro em log avançados de erros com Aplicativos Lógicos"
keywords: 
services: logic-apps
author: hedidin
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 63b0b843-f6b0-4d9a-98d0-17500be17385
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: b-hoedid
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4d211ab7f34017f7fcfd6150b916a038e7391c51


---
# <a name="logging-and-error-handling-in-logic-apps"></a>Registro em log e tratamento de erros nos Aplicativos Lógicos
Este artigo descreve como você pode estender um aplicativo lógico para dar um suporte melhor ao tratamento de exceções. É um caso de uso real e nossa resposta à pergunta “Os Aplicativos Lógicos dão suporte ao tratamento de exceções e de erros”?

> [!NOTE]
> A versão atual do recurso Aplicativos Lógicos do Serviço de Aplicativo do Microsoft Azure fornece um modelo padrão para respostas de ação.
> Isso inclui validação interna e respostas de erro retornadas de um aplicativo de API.
> 
> 

## <a name="overview-of-the-use-case-and-scenario"></a>Visão geral do cenário e do caso de uso
A história a seguir é o caso de uso para este artigo.
Uma organização de saúde conhecida nos pediu para desenvolver uma solução do Azure que criasse um portal de paciente usando o Microsoft Dynamics CRM Online. Ela precisava enviar registros de consultas entre o portal de pacientes do Dynamics CRM Online e o Salesforce.  Foi solicitado que usássemos o padrão [HL7 FHIR](http://www.hl7.org/implement/standards/fhir/) para todos os registros de pacientes.

O projeto tinha dois requisitos principais:  

* Um método para registrar em log os registros enviados do portal do Dynamics CRM Online
* Uma maneira de exibir os erros ocorridos no fluxo de trabalho

## <a name="how-we-solved-the-problem"></a>Como solucionamos o problema
> [!TIP]
> Você pode exibir um vídeo de alto nível do projeto no [Grupo de Usuários de Integração](http://www.integrationusergroup.com/do-logic-apps-support-error-handling/ "Integration User Group").
> 
> 

Escolhemos o [Banco de Dados de Documentos do Azure](https://azure.microsoft.com/services/documentdb/ "Banco de Dados de Documentos do Azure") como um repositório para os registros de log e de erros (O Banco de Dados de Documentos trata os registros como documentos). Como os Aplicativos Lógicos têm um modelo padrão para todas as respostas, não seria necessário criar um esquema personalizado. Nós poderíamos criar um aplicativo de API para **Inserir** e **Consultar** registros de erro e de log. Também poderíamos definir um esquema para cada um deles no aplicativo de API.  

Outro requisito era eliminar registros após determinada data. O DocumentDB tem uma propriedade chamada [Vida útil](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "Vida útil") (TTL), que nos permitiu definir um valor **Vida útil** para cada registro ou coleção. Isso elimina a necessidade de excluir manualmente os registros do Banco de Dados de Documentos.

### <a name="creation-of-the-logic-app"></a>Criação do aplicativo lógico
A primeira etapa é criar o aplicativo lógico e carregá-lo no designer. Neste exemplo, estamos usando aplicativos lógicos pai-filho. Vamos supor que nós criamos o pai e vamos criar um aplicativo lógico filho.

Já que vamos registrar em log o registro vindo do Dynamics CRM Online, começaremos do início. Precisamos usar um gatilho Solicitação, já que o aplicativo lógico pai irá disparar esse filho.

> [!IMPORTANT]
> Para concluir este tutorial, você precisará criar um Banco de Dados de Documentos e duas coleções (Registro em Log e de Erros).
> 
> 

### <a name="logic-app-trigger"></a>Gatilho de aplicativo lógico
Estamos usando um gatilho Solicitação, conforme mostrado no exemplo a seguir.

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


### <a name="steps"></a>Etapas
Precisamos registrar a origem (solicitação) do registro do paciente do portal do Dynamics CRM Online.

1. Precisamos obter um novo registro de consulta do Dynamics CRM Online.
    O gatilho proveniente do CRM fornece a **CRM PatentId**, o **tipo de registro**, o **Registro Novo ou Atualizado** (valor booliano novo ou atualizado) e a **SalesforceId**. O **SalesforceId** pode ser nulo porque é usado somente para uma atualização.
    Obteremos o registro do CRM usando a **PatientID** do CRM e o **Tipo de Registro**.
2. Em seguida, precisamos adicionar a operação **InsertLogEntry** do nosso aplicativo de API do Banco de Dados de Documentos como mostrado nas figuras a seguir.

#### <a name="insert-log-entry-designer-view"></a>Inserir entrada de log no modo de exibição de designer
![Inserir Entrada de Log](./media/app-service-logic-scenario-error-and-exception-handling/lognewpatient.png)

#### <a name="insert-error-entry-designer-view"></a>Inserir entrada de erro no modo de exibição de designer
![Inserir Entrada de Log](./media/app-service-logic-scenario-error-and-exception-handling/insertlogentry.png)

#### <a name="check-for-create-record-failure"></a>Verificar falha em criar registro
![Condição](./media/app-service-logic-scenario-error-and-exception-handling/condition.png)

## <a name="logic-app-source-code"></a>Código-fonte do aplicativo lógico
> [!NOTE]
> A seguir, mostraremos exemplos apenas. Como o tutorial se baseia em uma implementação atualmente em produção, o valor de um **Nó de Origem** não pode exibir as propriedades relacionadas ao agendamento de uma consulta.
> 
> 

### <a name="logging"></a>Registro em log
O exemplo de código de aplicativo lógico a seguir mostra como lidar com registros em log.

#### <a name="log-entry"></a>Entrada de log
Este é o código-fonte do aplicativo lógico para a inserção de uma entrada de log.

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

#### <a name="log-request"></a>Solicitação de log
Essa é a mensagem de solicitação de log postada para o aplicativo de API.

``` json
    {
    "uri": "https://.../api/Log",
    "method": "post",
    "body": {
        "date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "operation": "New Patient",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "providerId": "",
        "source": "{\"Pragma\":\"no-cache\",\"x-ms-request-id\":\"e750c9a9-bd48-44c4-bbba-1688b6f8a132\",\"OData-Version\":\"4.0\",\"Cache-Control\":\"no-cache\",\"Date\":\"Fri, 10 Jun 2016 22:31:56 GMT\",\"Set-Cookie\":\"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1\",\"Server\":\"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0\",\"X-AspNet-Version\":\"4.0.30319\",\"X-Powered-By\":\"ASP.NET\",\"Content-Length\":\"1935\",\"Content-Type\":\"application/json; odata.metadata=minimal; odata.streaming=true\",\"Expires\":\"-1\"}"
        }
    }

```


#### <a name="log-response"></a>Resposta de log
Essa é a mensagem de resposta de log postada para o aplicativo de API.

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
        "_etag": "\"0400fc2f-0000-0000-0000-575b3ff00000\"",
        "patientID": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:56Z",
        "source": "{\"Pragma\":\"no-cache\",\"x-ms-request-id\":\"e750c9a9-bd48-44c4-bbba-1688b6f8a132\",\"OData-Version\":\"4.0\",\"Cache-Control\":\"no-cache\",\"Date\":\"Fri, 10 Jun 2016 22:31:56 GMT\",\"Set-Cookie\":\"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1\",\"Server\":\"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0\",\"X-AspNet-Version\":\"4.0.30319\",\"X-Powered-By\":\"ASP.NET\",\"Content-Length\":\"1935\",\"Content-Type\":\"application/json; odata.metadata=minimal; odata.streaming=true\",\"Expires\":\"-1\"}",
        "operation": "New Patient",
        "salesforceId": "",
        "expired": false
    }
}

```

Agora vejamos as etapas de tratamento de erros.

### <a name="error-handling"></a>Tratamento de erros
O exemplo de código de aplicativos lógicos a seguir mostra como você pode implementar o tratamento de erros.

#### <a name="create-error-record"></a>Criar registro de erro
Este é o código-fonte dos Aplicativos Lógicos para a criação de um registro de erro.

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

#### <a name="insert-error-into-documentdb--request"></a>Inserir erro no Banco de Dados de Documentos - solicitação
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
        "source": "{\"Account_Class_vod__c\":\"PRAC\",\"Account_Status_MED__c\":\"I\",\"CRM_HUB_ID__c\":\"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0\",\"Credentials_vod__c\",\"DTC_ID_MED__c\":\"\",\"Fax\":\"\",\"FirstName\":\"A\",\"Gender_vod__c\":\"\",\"IMS_ID__c\":\"\",\"LastName\":\"BAILEY\",\"MasterID_mp__c\":\"\",\"C_ID_MED__c\":\"851588\",\"Middle_vod__c\":\"\",\"NPI_vod__c\":\"\",\"PDRP_MED__c\":false,\"PersonDoNotCall\":false,\"PersonEmail\":\"\",\"PersonHasOptedOutOfEmail\":false,\"PersonHasOptedOutOfFax\":false,\"PersonMobilePhone\":\"\",\"Phone\":\"\",\"Practicing_Specialty__c\":\"FM - FAMILY MEDICINE\",\"Primary_City__c\":\"\",\"Primary_State__c\":\"\",\"Primary_Street_Line2__c\":\"\",\"Primary_Street__c\":\"\",\"Primary_Zip__c\":\"\",\"RecordTypeId\":\"012U0000000JaPWIA0\",\"Request_Date__c\":\"2016-06-10T22:31:55.9647467Z\",\"ONY_ID__c\":\"\",\"Specialty_1_vod__c\":\"\",\"Suffix_vod__c\":\"\",\"Website\":\"\"}",
        "statusCode": "400"
    }
}
```

#### <a name="insert-error-into-documentdb--response"></a>Inserir erro no Banco de Dados de Documentos - resposta
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
        "_etag": "\"0c00eaac-0000-0000-0000-575b3fdc0000\"",
        "prescriberId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:57.3651027Z",
        "action": "New_Patient",
        "salesforceId": "",
        "update": false,
        "body": "CRM failed to complete task: Message: duplicate value found: CRM_HUB_ID__c duplicates value on record with id: 001U000001c83gK",
        "source": "{\"Account_Class_vod__c\":\"PRAC\",\"Account_Status_MED__c\":\"I\",\"CRM_HUB_ID__c\":\"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0\",\"Credentials_vod__c\":\"DO - Degree level is DO\",\"DTC_ID_MED__c\":\"\",\"Fax\":\"\",\"FirstName\":\"A\",\"Gender_vod__c\":\"\",\"IMS_ID__c\":\"\",\"LastName\":\"BAILEY\",\"MterID_mp__c\":\"\",\"Medicis_ID_MED__c\":\"851588\",\"Middle_vod__c\":\"\",\"NPI_vod__c\":\"\",\"PDRP_MED__c\":false,\"PersonDoNotCall\":false,\"PersonEmail\":\"\",\"PersonHasOptedOutOfEmail\":false,\"PersonHasOptedOutOfFax\":false,\"PersonMobilePhone\":\"\",\"Phone\":\"\",\"Practicing_Specialty__c\":\"FM - FAMILY MEDICINE\",\"Primary_City__c\":\"\",\"Primary_State__c\":\"\",\"Primary_Street_Line2__c\":\"\",\"Primary_Street__c\":\"\",\"Primary_Zip__c\":\"\",\"RecordTypeId\":\"012U0000000JaPWIA0\",\"Request_Date__c\":\"2016-06-10T22:31:55.9647467Z\",\"XXXXXXX\":\"\",\"Specialty_1_vod__c\":\"\",\"Suffix_vod__c\":\"\",\"Website\":\"\"}",
        "code": 400,
        "errors": null,
        "isError": true,
        "severity": 4,
        "notes": null,
        "resolved": 0
        }
}
```

#### <a name="salesforce-error-response"></a>Resposta de erro do Salesforce
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

### <a name="returning-the-response-back-to-the-parent-logic-app"></a>Retornar a resposta ao aplicativo lógico pai
Assim que você tiver a resposta, poderá passá-la para o aplicativo lógico pai.

#### <a name="return-success-response-to-the-parent-logic-app"></a>Retornar resposta de êxito para aplicativo lógico pai
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
    "    Content-type": "application/json",
        "x-ms-date": "@utcnow()"
    },
    "statusCode": 200
    },
    "type": "Response"
}
```

#### <a name="return-error-response-to-the-parent-logic-app"></a>Retornar resposta de erro para aplicativo lógico pai
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


## <a name="documentdb-repository-and-portal"></a>Repositório e portal do Banco de Dados de Documentos
Nossa solução adicionou outros recursos com o [Banco de Dados de Documentos](https://azure.microsoft.com/services/documentdb).

### <a name="error-management-portal"></a>Portal de gerenciamento de erros
Para exibir os erros, você pode criar um aplicativo Web do MVC para exibir os registros de erro do Banco de Dados de Documentos. As operações **Listar**, **Detalhes**, **Editar** e **Excluir** foram incluídas na versão atual.

> [!NOTE]
> Operação Editar: o Banco de Dados de Documentos faz uma substituição de todo o documento.
> Os registros mostrados nos modos de exibição de **Lista** e de **Detalhe** são apenas exemplos. Eles não são registros reais de consultas de pacientes.
> 
> 

A seguir, temos exemplos de nossos detalhes do aplicativo MVC criados com a abordagem descrita anteriormente.

#### <a name="error-management-list"></a>Lista de gerenciamento de erros
![Lista de Erros](./media/app-service-logic-scenario-error-and-exception-handling/errorlist.png)

#### <a name="error-management-detail-view"></a>Modo de exibição de detalhes do gerenciamento de erros
![Detalhes do Erro](./media/app-service-logic-scenario-error-and-exception-handling/errordetails.png)

### <a name="log-management-portal"></a>Portal de gerenciamento de logs
Para exibir os logs, também criamos um aplicativo Web do MVC.  A seguir, temos exemplos de nossos detalhes do aplicativo MVC criados com a abordagem descrita anteriormente.

#### <a name="sample-log-detail-view"></a>Modo de exibição de detalhes de log de exemplo
![Modo de Exibição de Detalhes de Log](./media/app-service-logic-scenario-error-and-exception-handling/samplelogdetail.png)

### <a name="api-app-details"></a>Detalhes do aplicativo de API
#### <a name="logic-apps-exception-management-api"></a>API de gerenciamento de exceções de Aplicativos Lógicos
Nosso aplicativo de API de gerenciamento de exceções de Aplicativos Lógicos de software livre forneceu a funcionalidade a seguir.

Há dois controladores:

* **ErrorController** insere um registro de erro (documento) em uma coleção do Banco de Dados de Documentos.
* **LogController** insere um registro de log (documento) em uma coleção do Banco de Dados de Documentos.

> [!TIP]
> Ambos os controladores usam operações `async Task<dynamic>`. Isso permite que as operações sejam resolvidas em tempo de execução, de forma que possamos criar o esquema do Banco de Dados de Documentos no corpo da operação.
> 
> 

Todos os documentos do Banco de Dados de Documentos devem ter uma ID exclusiva. Estamos usando o `PatientId` e adicionando um carimbo de data/hora convertido em um valor de carimbo de data/hora do Unix (double). Nós o truncamos para remover o valor fracionário.

Você pode exibir o código-fonte da nossa API de controlador erro [o GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/Logic App Exception Management API/Controllers/ErrorController.cs).

Vamos chamar a API de um aplicativo lógico usando a sintaxe a seguir.

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

A expressão no exemplo de código anterior está verificando o status de *Create_NewPatientRecord*, que é **Falha**.

## <a name="summary"></a>Resumo
* Você pode implementar facilmente o tratamento de logs e de erros em um aplicativo lógico.
* Você pode usar o Banco de Dados de Documentos como o repositório de registros de log e de erros (documentos).
* Você pode usar o MVC para criar um portal para exibir os registros de log e de erros.

### <a name="source-code"></a>Código-fonte
O código-fonte para o aplicativo de API de gerenciamento de exceções de Aplicativos Lógicos está disponível neste [repositório GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "API de Gerenciamento de Exceções de Aplicativos Lógicos").

## <a name="next-steps"></a>Próximas etapas
* [Exibir mais exemplos e cenários dos Aplicativos Lógicos](app-service-logic-examples-and-scenarios.md)
* [Saiba mais sobre ferramentas de monitoramento de Aplicativos Lógicos](app-service-logic-monitor-your-logic-apps.md)
* [Criar um modelo de implantação de Aplicativos Lógicos](app-service-logic-create-deploy-template.md)




<!--HONumber=Nov16_HO3-->


