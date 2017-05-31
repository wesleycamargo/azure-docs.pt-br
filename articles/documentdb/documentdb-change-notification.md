---
redirect_url: https://docs.microsoft.com/azure/documentdb/documentdb-change-feed-hl7-fhir-logic-apps
ROBOTS: NOINDEX, NOFOLLOW
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 58c0ea0bd8bda994c8314d2866304cb3ab367bfd
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
# <a name="notifications-for-new-or-changed-azure-cosmos-db-resources-using-logic-apps"></a>Notificações sobre recursos novos ou alterados do Azure Cosmos DB que usam Aplicativos Lógicos
Este artigo surgiu de uma pergunta que eu vi publicada em um dos fóruns da comunidade do Banco de Dados de Documentos do Azure. A pergunta foi **O Banco de Dados de Documentos oferece suporte a notificações sobre recursos modificados**?

Trabalhei com o BizTalk Server por muitos anos e esse é um cenário muito comum ao usar o [Adaptador LOB do WCF](https://msdn.microsoft.com/library/bb798128.aspx). Portanto, decidi verificar se eu poderia duplicar essa funcionalidade no Banco de Dados de Documentos para documentos novos e/ou modificados.

Este artigo fornece uma visão geral dos componentes da solução de notificação de alteração, que inclui um [gatilho](documentdb-programming.md#trigger) e um [Aplicativo Lógico](../logic-apps/logic-apps-what-are-logic-apps.md). Há trechos de código importantes embutidos e toda a solução está disponível no [GitHub](https://github.com/HEDIDIN/DocDbNotifications).

## <a name="use-case"></a>Caso de uso
A história a seguir é o caso de uso para este artigo.

O Azure Cosmos DB é o repositório de documentos FHIR (Fast Healthcare Interoperability Resources) da HL7 (Health Level Seven International). Vamos supor que seu banco de dados do Azure Cosmos DB combinado com sua API e o Aplicativo Lógico formem um Servidor HL7 FHIR.  Uma instalação de serviços de saúde armazena dados de pacientes no banco de dados “Pacientes” do Azure Cosmos DB. Há várias coleções no banco de dados de pacientes; Hospitalar, Identificação etc. As informações sobre o paciente se enquadram em identificação.  Há uma coleção chamada "Paciente".

O departamento de cardiologia monitora dados pessoais de saúde e sobre exercícios. Procurar registros de pacientes novos ou modificados é algo demorado. Eles perguntaram ao departamento de TI se há uma maneira de receber uma notificação sobre registros novos ou modificados dos pacientes.  

O departamento de TI disse que poderia fornecer isso com facilidade. Também disse que poderia enviar os documentos ao [Armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/) , de modo que o departamento de cardiologia pudesse acessá-los facilmente.

## <a name="how-the-it-department-solved-the-problem"></a>Como o departamento de TI resolveu o problema
Para criar esse aplicativo, o departamento de TI decidiu modelá-lo primeiro.  O bom de usar o BPMN (Modelo e Notação de Processo de Negócios) é que tanto os técnicos quanto os não técnicos podem compreendê-lo facilmente. Todo esse processo de notificação é considerado um processo corporativo. 

## <a name="high-level-view-of-notification-process"></a>Exibição de alto nível do processo de notificação
1. Você começa com um Aplicativo Lógico que tem um gatilho com temporizador. Por padrão, o gatilho é executado a cada hora.
2. Em seguida, você faz um HTTP POST para o Aplicativo Lógico.
3. O Aplicativo Lógico faz todo o trabalho.

![exibição de alto nível](./media/documentdb-change-notification/high-level-view.png)

### <a name="lets-take-a-look-at-what-this-logic-app-does"></a>Vamos examinar o que faz esse Aplicativo Lógico
Se você observar a figura a seguir, há várias etapas no fluxo de trabalho do Aplicativo Lógico.

![Processo de Lógica Principal](./media/documentdb-change-notification/main-logic-app-process.png)

As etapas são as seguintes:

1. Você precisa obter a DateTime UTC atual de um Aplicativo de API.  O valor padrão é uma hora a menos.
2. A DateTime UTC é convertida em um formato de Carimbo de Data/Hora Unix. Esse é o formato padrão para carimbos de data/hora no Banco de Dados de Documentos.
3. Você faz POST do valor em um Aplicativo de API, que realiza uma consulta ao Banco de Dados de Documentos. O valor é usado em uma consulta.
   
    ```SQL
         SELECT * FROM Patients p WHERE (p._ts >= @unixTimeStamp)
    ```
   
   > [!NOTE]
   > O _ts representa os metadados do Carimbo de dara/hora para todos os recursos do Banco de Dados de Documentos.
   > 
   > 
4. Se algum documento for encontrado, o corpo da resposta será enviado ao Armazenamento de Blobs do Azure.
   
   > [!NOTE]
   > O Armazenamento de Blobs exige uma conta de Armazenamento do Azure. Você precisa provisionar uma conta de Armazenamento de Blobs do Azure e adicionar um novo Blob chamado pacientes. Para saber mais, confira [Sobre as contas de armazenamento do Azure](../storage/storage-create-storage-account.md) e [Introdução ao Armazenamento de Blobs do Azure](../storage/storage-dotnet-how-to-use-blobs.md).
   > 
   > 
5. Por fim, um email é enviado notificando o destinatário sobre o número de documentos encontrados. Se nenhum documento for encontrado, o corpo do email deverá ser "0 Documentos Encontrados". 

Agora que você tem uma ideia sobre a função do fluxo de trabalho, vamos dar uma olhada em como implementá-lo.

### <a name="lets-start-with-the-main-logic-app"></a>Vamos começar com o Aplicativo Lógico principal
Se você não estiver familiarizado com os Aplicativos Lógicos, eles estão disponíveis no [Azure Marketplace](https://portal.azure.com/), e você pode saber mais sobre eles em [O que são Aplicativos Lógicos?](../logic-apps/logic-apps-what-are-logic-apps.md)

Ao criar um novo Aplicativo Lógico, você recebe a pergunta **Como você deseja iniciar?**

Quando você clica dentro da caixa de texto, há algumas opções de eventos. Para este Aplicativo Lógico, selecione **Manual - Quando uma solicitação HTTP é recebida** conforme mostrado abaixo.

![Início](./media/documentdb-change-notification/starting-off.png)

### <a name="design-view-of-your-completed-logic-app"></a>Modo de Design de seu Aplicativo Lógico concluído
Vamos avançar e examinar a exibição do design concluído para o Aplicativo Lógico, que é chamado de DocDB.

![Fluxo de trabalho do Aplicativo Lógico](./media/documentdb-change-notification/workflow-expanded.png)

Ao editar as ações no Designer do Aplicativo Lógico, você tem a opção de selecionar **Saídas** na Solicitação HTTP ou na ação anterior, conforme mostrado a ação sendMail abaixo.

![Escolha as saídas](./media/documentdb-change-notification/choose-outputs.png)

Antes de cada ação em seu fluxo de trabalho, você pode tomar uma decisão; **Adicionar uma ação** ou **Adicionar uma condição**, conforme mostra a figura a seguir.

![Tome uma decisão](./media/documentdb-change-notification/add-action-or-condition.png)

Se você selecionar **Adicionar uma condição**, receberá um formulário, conforme mostra a figura a seguir, para inserir sua lógica.  Isso é, essencialmente, uma regra de negócio.  Se você clicar em um campo, poderá selecionar parâmetros da ação anterior. Você também pode inserir diretamente os valores.

![Adicionar uma condição](./media/documentdb-change-notification/condition1.png)

> [!NOTE]
> Também há a possibilidade de inserir tudo no Modo de Exibição de Código.
> 
> 

Vamos examinar o Aplicativo Lógico concluído no Modo de Exibição de Código.  

```JSON

       "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
    "actions": {
        "Conversion": {
            "conditions": [
                {
                    "dependsOn": "GetUtcDate"
                }
            ],
            "inputs": {
                "method": "post",
                "queries": {
                    "currentdateTime": "@{body('GetUtcDate')}"
                },
                "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Conversion"
            },
            "metadata": {
                "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1",
                "swaggerSource": "custom"
            },
            "type": "Http"
        },
        "Createfile": {
            "conditions": [
                {
                    "expression": "@greater(length(body('GetDocuments')), 0)"
                },
                {
                    "dependsOn": "GetDocuments"
                }
            ],
            "inputs": {
                "body": "@body('GetDocuments')",
                "host": {
                    "api": {
                        "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                    },
                    "connection": {
                        "name": "@parameters('$connections')['azureblob']['connectionId']"
                    }
                },
                "method": "post",
                "path": "/datasets/default/files",
                "queries": {
                    "folderPath": "/patients",
                    "name": "Patient_@{guid()}.json"
                }
            },
            "type": "ApiConnection"
        },
        "GetDocuments": {
            "conditions": [
                {
                    "dependsOn": "Conversion"
                }
            ],
            "inputs": {
                "method": "post",
                "queries": {
                    "unixTimeStamp": "@body('Conversion')"
                },
                "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Patient"
            },
            "metadata": {
                "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1",
                "swaggerSource": "custom"
            },
            "type": "Http"
        },
        "GetUtcDate": {
            "conditions": [],
            "inputs": {
                "method": "get",
                "queries": {
                    "hoursBack": "@{int(triggerBody()['GetUtcDate_HoursBack'])}"
                },
                "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Authorization"
            },
            "metadata": {
                "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1",
                "swaggerSource": "custom"
            },
            "type": "Http"
        },
        "sendMail": {
            "conditions": [
                {
                    "dependsOn": "GetDocuments"
                }
            ],
            "inputs": {
                "body": "api_user=@{triggerBody()['sendgridUsername']}&api_key=@{triggerBody()['sendgridPassword']}&from=@{parameters('fromAddress')}&to=@{triggerBody()['EmailTo']}&subject=@{triggerBody()['Subject']}&text=@{int(length(body('GetDocuments')))} Documents Found",
                "headers": {
                    "Content-type": "application/x-www-form-urlencoded"
                },
                "method": "POST",
                "uri": "https://api.sendgrid.com/api/mail.send.json"
            },
            "type": "Http"
        }
    },
    "contentVersion": "1.0.0.0",
    "outputs": {
        "Results": {
            "type": "String",
            "value": "@{int(length(body('GetDocuments')))} Records Found"
        }
    },
    "parameters": {
        "$connections": {
            "defaultValue": {},
            "type": "Object"
        },
        "fromAddress": {
            "defaultValue": "user@msn.com",
            "type": "String"
        },
        "toAddress": {
            "defaultValue": "XXXXX@XXXXXXX.net",
            "type": "String"
        }
    },
    "triggers": {
        "manual": {
            "inputs": {
                "schema": {
                    "properties": {},
                    "required": [],
                    "type": "object"
                }
            },
            "type": "Manual"
        }

```

Se você não estiver familiarizado com o que representa as diferentes seções no código, confira a documentação [Logic App Workflow Definition Language](http://aka.ms/logicappsdocs) .

Para este fluxo de trabalho você está usando um [Gatilho HTTP Webhook](https://sendgrid.com/blog/whats-webhook/). Se você examinar o código acima, verá parâmetros como no exemplo a seguir.

```C#

    =@{triggerBody()['Subject']}

```

O `triggerBody()` representa os parâmetros incluídos no corpo de um POST REST para a API REST do Aplicativo Lógico. O `()['Subject']` representa o campo. Todos esses parâmetros compõem o corpo formatado em JSON. 

> [!NOTE]
> Com um Webhook, você pode ter acesso completo ao cabeçalho e ao corpo da solicitação do gatilho. Neste aplicativo, o foco é o corpo.
> 
> 

Conforme mencionado anteriormente, você pode usar o designer para atribuir parâmetros, ou fazê-lo no modo de exibição de código.
Se você fizer isso no modo de exibição de código, poderá definir quais propriedades exigem um valor, conforme mostra o exemplo de código a seguir. 

```JSON

    "triggers": {
        "manual": {
            "inputs": {
            "schema": {
                "properties": {
            "Subject": {
                "type" : "String"    

            }
            },
                "required": [
            "Subject"
                 ],
                "type": "object"
            }
            },
            "type": "Manual"
        }
        }
```

Você está criando um esquema JSON que será passado pelo corpo da solicitação HTTP POST.
Para disparar o gatilho, você precisará de uma URL de Retorno de Chamada.  Você aprenderá como gerá-la mais tarde no tutorial.  

## <a name="actions"></a>Ações
Vamos ver qual é a função de cada ação em nosso Aplicativo Lógico.

### <a name="getutcdate"></a>GetUTCDate
**Modo de Exibição de Designer**

![](./media/documentdb-change-notification/getutcdate.png)

**Modo de Exibição de Código**

```JSON

    "GetUtcDate": {
            "conditions": [],
            "inputs": {
            "method": "get",
            "queries": {
                "hoursBack": "@{int(triggerBody()['GetUtcDate_HoursBack'])}"
            },
            "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Authorization"
            },
            "metadata": {
            "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1"
            },
            "type": "Http"
        },

```

Essa ação HTTP executa uma operação GET.  Ela chama o método GetUtcDate do APLICATIVO de API. O URI usa a propriedade "GetUtcDate_HoursBack" passada no corpo do gatilho.  O valor de "GetUtcDate_HoursBack" é definido no primeiro Aplicativo Lógico. Você aprenderá mais sobre o Aplicativo Lógico de Gatilho posteriormente no tutorial.

Essa ação chama seu Aplicativo API com o objetivo de retornar o valor de cadeia de caracteres de Data UTC.

#### <a name="operations"></a>Operações
**Solicitação**

```JSON

    {
        "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Authorization",
        "method": "get",
        "queries": {
          "hoursBack": "24"
        }
    }

```

**Resposta**

```JSON

    {
        "statusCode": 200,
        "headers": {
          "pragma": "no-cache",
          "cache-Control": "no-cache",
          "date": "Fri, 26 Feb 2016 15:47:33 GMT",
          "server": "Microsoft-IIS/8.0",
          "x-AspNet-Version": "4.0.30319",
          "x-Powered-By": "ASP.NET"
        },
        "body": "Fri, 15 Jan 2016 23:47:33 GMT"
    }

```

A próxima etapa é converter o valor de Data/Hora UTC para o Carimbo de data/hora Unix, que é um tipo duplo de .NET.

### <a name="conversion"></a>Conversão
##### <a name="designer-view"></a>Modo de Exibição de Designer
![Conversão](./media/documentdb-change-notification/conversion.png)

##### <a name="code-view"></a>Modo de Exibição de Código
```JSON

    "Conversion": {
        "conditions": [
        {
            "dependsOn": "GetUtcDate"
        }
        ],
        "inputs": {
        "method": "post",
        "queries": {
            "currentDateTime": "@{body('GetUtcDate')}"
        },
        "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Conversion"
        },
        "metadata": {
        "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1"
        },
        "type": "Http"
    },

```

Nesta etapa, você passa o valor que retornou de GetUTCDate.  Há uma condição dependsOn, o que significa que a ação GetUTCDate deve ser concluída com êxito. Caso contrário, essa ação será ignorada. 

Essa ação chama o Aplicativo de API para lidar com a conversão.

#### <a name="operations"></a>Operações
##### <a name="request"></a>Solicitação
```JSON

    {
        "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Conversion",
        "method": "post",
        "queries": {
        "currentDateTime": "Fri, 15 Jan 2016 23:47:33 GMT"
        }
    }   
```

##### <a name="response"></a>Resposta
```JSON

    {
        "statusCode": 200,
        "headers": {
          "pragma": "no-cache",
          "cache-Control": "no-cache",
          "date": "Fri, 26 Feb 2016 15:47:33 GMT",
          "server": "Microsoft-IIS/8.0",
          "x-AspNet-Version": "4.0.30319",
          "x-Powered-By": "ASP.NET"
        },
        "body": 1452901653
    }
```

Na próxima ação, você realizará uma operação POST em nosso Aplicativo de API.

### <a name="getdocuments"></a>GetDocuments
##### <a name="designer-view"></a>Modo de Exibição de Designer
![Obter Documento](./media/documentdb-change-notification/getdocuments.png)

##### <a name="code-view"></a>Modo de Exibição de Código
```JSON

    "GetDocuments": {
        "conditions": [
        {
            "dependsOn": "Conversion"
        }
        ],
        "inputs": {
        "method": "post",
        "queries": {
            "unixTimeStamp": "@{body('Conversion')}"
        },
        "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Patient"
        },
        "metadata": {
        "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1"
        },
        "type": "Http"
    },

```

Para a ação de GetDocuments, você passará o corpo da resposta da ação de Conversão. Este é um parâmetro no URI:

```C#

    unixTimeStamp=@{body('Conversion')}

```

A ação QueryDocuments executa uma operação HTTP POST no Aplicativo de API. 

O método chamado é **QueryForNewPatientDocuments**.

#### <a name="operations"></a>Operações
##### <a name="request"></a>Solicitação
```JSON

    {
        "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Patient",
        "method": "post",
        "queries": {
        "unixTimeStamp": "1452901653"
        }
    }
```

##### <a name="response"></a>Resposta
```JSON

    {
        "statusCode": 200,
        "headers": {
        "pragma": "no-cache",
        "cache-Control": "no-cache",
        "date": "Fri, 26 Feb 2016 15:47:35 GMT",
        "server": "Microsoft-IIS/8.0",
        "x-AspNet-Version": "4.0.30319",
        "x-Powered-By": "ASP.NET"
        },
        "body": [
        {
            "id": "xcda",
            "_rid": "vCYLAP2k6gAXAAAAAAAAAA==",
            "_self": "dbs/vCYLAA==/colls/vCYLAP2k6gA=/docs/vCYLAP2k6gAXAAAAAAAAAA==/",
            "_ts": 1454874620,
            "_etag": "\"00007d01-0000-0000-0000-56b79ffc0000\"",
            "resourceType": "Patient",
            "text": {
            "status": "generated",
            "div": "<div>\n      \n      <p>Henry Levin the 7th</p>\n    \n    </div>"
            },
            "identifier": [
            {
                "use": "usual",
                "type": {
                "coding": [
                    {
                    "system": "http://hl7.org/fhir/v2/0203",
                    "code": "MR"
                    }
                ]
                },
                "system": "urn:oid:2.16.840.1.113883.19.5",
                "value": "12345"
            }
            ],
            "active": true,
            "name": [
            {
                    "family": [
                        "Levin"
                    ],
                    "given": [
                        "Henry"
                    ]
                }
            ],
            "gender": "male",
            "birthDate": "1932-09-24",
            "managingOrganization": {
                "reference": "Organization/2.16.840.1.113883.19.5",
                "display": "Good Health Clinic"
            }
        },

```

A próxima ação é salvar os documentos no [Armazenamento de Blob do Azure](https://azure.microsoft.com/services/storage/). 

> [!NOTE]
> O Armazenamento de Blobs exige uma conta de Armazenamento do Azure. Você precisa provisionar uma conta de Armazenamento de Blobs do Azure e adicionar um novo Blob chamado pacientes. Para saber mais, confira [Introdução ao Armazenamento de Blobs do Azure](../storage/storage-dotnet-how-to-use-blobs.md).
> 
> 

### <a name="create-file"></a>Criar arquivo
##### <a name="designer-view"></a>Modo de Exibição de Designer
![Criar arquivo](./media/documentdb-change-notification/createfile.png)

##### <a name="code-view"></a>Modo de Exibição de Código
```JSON

    {
    "host": {
        "api": {
            "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
        },
        "connection": {
            "name": "subscriptions/fxxxxxc079-4e5d-b002-xxxxxxxxxx/resourceGroups/Api-Default-Central-US/providers/Microsoft.Web/connections/azureblob"
        }
    },
    "method": "post",
    "path": "/datasets/default/files",
    "queries": {
        "folderPath": "/patients",
        "name": "Patient_17513174-e61d-4b56-88cb-5cf383db4430.json"
    },
    "body": [
        {
            "id": "xcda",
            "_rid": "vCYLAP2k6gAXAAAAAAAAAA==",
            "_self": "dbs/vCYLAA==/colls/vCYLAP2k6gA=/docs/vCYLAP2k6gAXAAAAAAAAAA==/",
            "_ts": 1454874620,
            "_etag": "\"00007d01-0000-0000-0000-56b79ffc0000\"",
            "resourceType": "Patient",
            "text": {
                "status": "generated",
                "div": "<div>\n      \n      <p>Henry Levin the 7th</p>\n    \n    </div>"
            },
            "identifier": [
                {
                    "use": "usual",
                    "type": {
                        "coding": [
                            {
                                "system": "http://hl7.org/fhir/v2/0203",
                                "code": "MR"
                            }
                        ]
                    },
                    "system": "urn:oid:2.16.840.1.113883.19.5",
                    "value": "12345"
                }
            ],
            "active": true,
            "name": [
                {
                    "family": [
                        "Levin"
                    ],
                    "given": [
                        "Henry"
                    ]
                }
            ],
            "gender": "male",
            "birthDate": "1932-09-24",
            "managingOrganization": {
                "reference": "Organization/2.16.840.1.113883.19.5",
                "display": "Good Health Clinic"
            }
        },

```

O código é gerado a partir da ação no designer. Não é necessário modificar o código.

Se não estiver familiarizado com o uso da API de Blobs do Azure, confira [Introdução à API de armazenamento de blobs do Azure](../connectors/connectors-create-api-azureblobstorage.md).

#### <a name="operations"></a>Operações
##### <a name="request"></a>Solicitação
```JSON

    "host": {
        "api": {
            "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
        },
        "connection": {
            "name": "subscriptions/fxxxxxc079-4e5d-b002-xxxxxxxxxx/resourceGroups/Api-Default-Central-US/providers/Microsoft.Web/connections/azureblob"
        }
    },
    "method": "post",
    "path": "/datasets/default/files",
    "queries": {
        "folderPath": "/patients",
        "name": "Patient_17513174-e61d-4b56-88cb-5cf383db4430.json"
    },
    "body": [
        {
            "id": "xcda",
            "_rid": "vCYLAP2k6gAXAAAAAAAAAA==",
            "_self": "dbs/vCYLAA==/colls/vCYLAP2k6gA=/docs/vCYLAP2k6gAXAAAAAAAAAA==/",
            "_ts": 1454874620,
            "_etag": "\"00007d01-0000-0000-0000-56b79ffc0000\"",
            "resourceType": "Patient",
            "text": {
                "status": "generated",
                "div": "<div>\n      \n      <p>Henry Levin the 7th</p>\n    \n    </div>"
            },
            "identifier": [
                {
                    "use": "usual",
                    "type": {
                        "coding": [
                            {
                                "system": "http://hl7.org/fhir/v2/0203",
                                "code": "MR"
                            }
                        ]
                    },
                    "system": "urn:oid:2.16.840.1.113883.19.5",
                    "value": "12345"
                }
            ],
            "active": true,
            "name": [
                {
                    "family": [
                        "Levin"
                    ],
                    "given": [
                        "Henry"
                    ]
                }
            ],
            "gender": "male",
            "birthDate": "1932-09-24",
            "managingOrganization": {
                "reference": "Organization/2.16.840.1.113883.19.5",
                "display": "Good Health Clinic"
            }
        },….


```

##### <a name="response"></a>Resposta
```JSON

    {
        "statusCode": 200,
        "headers": {
        "pragma": "no-cache",
        "x-ms-request-id": "2b2f7c57-2623-4d71-8e53-45c26b30ea9d",
        "cache-Control": "no-cache",
        "date": "Fri, 26 Feb 2016 15:47:36 GMT",
        "set-Cookie": "ARRAffinity=29e552cea7db23196f7ffa644003eaaf39bc8eb6dd555511f669d13ab7424faf;Path=/;Domain=127.0.0.1",
        "server": "Microsoft-HTTPAPI/2.0",
        "x-AspNet-Version": "4.0.30319",
        "x-Powered-By": "ASP.NET"
        },
        "body": {
        "Id": "0B0nBzHyMV-_NRGRDcDNMSFAxWFE",
        "Name": "Patient_47a2a0dc-640d-4f01-be38-c74690d085cb.json",
        "DisplayName": "Patient_47a2a0dc-640d-4f01-be38-c74690d085cb.json",
        "Path": "/Patient/Patient_47a2a0dc-640d-4f01-be38-c74690d085cb.json",
        "LastModified": "2016-02-26T15:47:36.215Z",
        "Size": 65647,
        "MediaType": "application/octet-stream",
        "IsFolder": false,
        "ETag": "\"c-g_a-1OtaH-kNQ4WBoXLp3Zv9s/MTQ1NjUwMTY1NjIxNQ\"",
        "FileLocator": "0B0nBzHyMV-_NRGRDcDNMSFAxWFE"
        }
    }
```

Sua última etapa é enviar uma notificação por email

### <a name="sendemail"></a>sendEmail
##### <a name="designer-view"></a>Modo de Exibição de Designer
![Enviar Email](./media/documentdb-change-notification/sendemail.png)

##### <a name="code-view"></a>Modo de Exibição de Código
```JSON


    "sendMail": {
        "conditions": [
        {
            "dependsOn": "GetDocuments"
        }
        ],
        "inputs": {
        "body": "api_user=@{triggerBody()['sendgridUsername']}&api_key=@{triggerBody()['sendgridPassword']}&from=@{parameters('fromAddress')}&to=@{triggerBody()['EmailTo']}&subject=@{triggerBody()['Subject']}&text=@{int(length(body('GetDocuments')))} Documents Found",
        "headers": {
            "Content-type": "application/x-www-form-urlencoded"
        },
        "method": "POST",
        "uri": "https://api.sendgrid.com/api/mail.send.json"
        },
        "type": "Http"
    }
```

Nessa ação, você envia uma notificação por email.  Você está usando [SendGrid](https://sendgrid.com/marketing/sendgrid-services?cvosrc=PPC.Bing.sendgrib&cvo_cid=SendGrid%20-%20US%20-%20Brand%20-%20&mc=Paid%20Search&mcd=BingAds&keyword=sendgrib&network=o&matchtype=e&mobile=&content=&search=1&utm_source=bing&utm_medium=cpc&utm_term=%5Bsendgrib%5D&utm_content=%21acq%21v2%2134335083397-8303227637-1649139544&utm_campaign=SendGrid+-+US+-+Brand+-+%28English%29).   

O código para isso foi gerado usando um modelo do Aplicativo Lógico e o SendGrid que estão no [repositório do GitHub 101-logic-app-sendgrid](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sendgrid).

A operação HTTP é um POST. 

Os parâmetros de autorização estão nas propriedades do gatilho

```JSON

    },
        "sendgridPassword": {
             "type": "SecureString"
         },
         "sendgridUsername": {
            "type": "String"
         }

        In addition, other parameters are static values set in the Parameters section of the Logic App. These are:
        },
        "toAddress": {
            "defaultValue": "XXXX@XXXX.com",
            "type": "String"
        },
        "fromAddress": {
            "defaultValue": "XXX@msn.com",
            "type": "String"
        },
        "emailBody": {
            "defaultValue": "@{string(concat(int(length(actions('QueryDocuments').outputs.body)) Records Found),'/n', actions('QueryDocuments').outputs.body)}",
            "type": "String"
        },

```

O emailBody está concatenando o número de documentos retornados da consulta, que pode ser "0" ou mais, além de "Registros Encontrados". O restante dos parâmetros é definido pelos parâmetros do Gatilho.

Essa ação depende da ação **GetDocuments** .

#### <a name="operations"></a>Operações
##### <a name="request"></a>Solicitação
```JSON

    {
        "uri": "https://api.sendgrid.com/api/mail.send.json",
        "method": "POST",
        "headers": {
        "Content-type": "application/x-www-form-urlencoded"
        },
        "body": "api_user=azureuser@azure.com&api_key=Biz@Talk&from=user@msn.com&to=XXXX@XXXX.com&subject=New Patients&text=37 Documents Found"
    }

```

##### <a name="response"></a>Resposta
```JSON

    {
        "statusCode": 200,
        "headers": {
        "connection": "keep-alive",
        "x-Frame-Options": "DENY,DENY",
        "access-Control-Allow-Origin": "https://sendgrid.com",
        "date": "Fri, 26 Feb 2016 15:47:35 GMT",
        "server": "nginx"
        },
        "body": {
        "message": "success"
        }
    }
```

Por fim, você deseja ver os resultados de seu Aplicativo Lógico no Portal do Azure. Para fazer isso, adicione um parâmetro à seção de saídas.

```JSON

    "outputs": {
        "Results": {
            "type": "String",
            "value": "@{int(length(actions('QueryDocuments').outputs.body))} Records Found"
        }

```

Isso retorna o mesmo valor que é enviado no corpo do email. A figura a seguir mostra um exemplo com "29 Registros Encontrado".

![Resultados](./media/documentdb-change-notification/logic-app-run.png)

## <a name="metrics"></a>Métricas
Você pode configurar o monitoramento do Aplicativo Lógico principal no portal. Isso permite que você exiba a Latência de Execução e outros eventos, como mostra a figura a seguir.

![](./media/documentdb-change-notification/metrics.png)

## <a name="docdb-trigger"></a>Gatilho de DocDb
Esse Aplicativo Lógico é o gatilho que inicia o fluxo de trabalho em seu Aplicativo Lógico principal.

A figura a seguir mostra o Modo de Exibição de Designer.

![](./media/documentdb-change-notification/trigger-recurrence.png)

```JSON

    {
        "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
        "actions": {
        "Http": {
            "conditions": [],
            "inputs": {
            "body": {
                "EmailTo": "XXXXXX@XXXXX.net",
                "GetUtcDate_HoursBack": "24",
                "Subject": "New Patients",
                "sendgridPassword": "********",
                "sendgridUsername": "azureuser@azure.com"
            },
            "method": "POST",
            "uri": "https://prod-01.westus.logic.azure.com:443/workflows/12a1de57e48845bc9ce7a247dfabc887/triggers/manual/run?api-version=2015-08-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=ObTlihr529ATIuvuG-dhxOgBL4JZjItrvPQ8PV6973c"
            },
            "type": "Http"
        }
        },
        "contentVersion": "1.0.0.0",
        "outputs": {
        "Results": {
            "type": "String",
            "value": "@{body('Http')['status']}"
        }
        },
        "parameters": {},
        "triggers": {
        "recurrence": {
            "recurrence": {
            "frequency": "Hour",
            "interval": 24
            },
            "type": "Recurrence"
        }
        }
    }

```

O Gatilho é configurado com uma recorrência de vinte e quatro horas. A Ação é um HTTP POST que usa a URL de Retorno de Chamada para o Aplicativo Lógico principal. O corpo contém os parâmetros especificados no Esquema JSON. 

#### <a name="operations"></a>Operações
##### <a name="request"></a>Solicitação
```JSON

    {
        "uri": "https://prod-01.westus.logic.azure.com:443/workflows/12a1de57e48845bc9ce7a247dfabc887/triggers/manual/run?api-version=2015-08-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=ObTlihr529ATIuvuG-dhxOgBL4JZjItrvPQ8PV6973c",
        "method": "POST",
        "body": {
        "EmailTo": "XXXXXX@XXXXX.net",
        "GetUtcDate_HoursBack": "24",
        "Subject": "New Patients",
        "sendgridPassword": "********",
        "sendgridUsername": "azureuser@azure.com"
        }
    }

```

##### <a name="response"></a>Resposta
```JSON

    {
        "statusCode": 202,
        "headers": {
        "pragma": "no-cache",
        "x-ms-ratelimit-remaining-workflow-writes": "7486",
        "x-ms-ratelimit-burst-remaining-workflow-writes": "1248",
        "x-ms-request-id": "westus:2d440a39-8ba5-4a9c-92a6-f959b8d2357f",
        "cache-Control": "no-cache",
        "date": "Thu, 25 Feb 2016 21:01:06 GMT"
        }
    }
```

Agora vamos analisar o Aplicativo de API.

## <a name="docdbnotificationapi"></a>DocDBNotificationApi
Embora haja várias operações no aplicativo, você só usará três.

* GetUTCDate
* ConvertToTimeStamp
* QueryForNewPatientDocuments

### <a name="docdbnotificationapi-operations"></a>Operações de DocDBNotificationApi
Vamos dar uma olhada na documentação sobre o Swagger

> [!NOTE]
> Para poder chamar as operações externamente, você precisa adicionar um valor de origem de "*" (sem as aspas) permitido por CORS nas configurações de seu Aplicativo de API, conforme mostra a figura a seguir.
> 
> 

![Configuração de Cors](./media/documentdb-change-notification/cors.png)

#### <a name="getutcdate"></a>GetUTCDate
![G](./media/documentdb-change-notification/getutcdateswagger.png)

#### <a name="converttotimestamp"></a>ConvertToTimeStamp
![Obter a Data UTC](./media/documentdb-change-notification/converion-swagger.png)

#### <a name="queryfornewpatientdocuments"></a>QueryForNewPatientDocuments
![Consultar](./media/documentdb-change-notification/patientswagger.png)

Vamos analisar o código por trás dessa operação.

#### <a name="getutcdate"></a>GetUTCDate
```C#

    /// <summary>
    /// Gets the current UTC Date value
    /// </summary>
    /// <returns></returns>
    [H ttpGet]
    [Metadata("GetUtcDate", "Gets the current UTC Date value minus the Hours Back")]
    [SwaggerOperation("GetUtcDate")]
    [SwaggerResponse(HttpStatusCode.OK, type: typeof (string))]
    [SwaggerResponse(HttpStatusCode.InternalServerError, "Internal Server Operation Error")]
    public string GetUtcDate(
       [Metadata("Hours Back", "How many hours back from the current Date Time")] int hoursBack)
    {


        return DateTime.UtcNow.AddHours(-hoursBack).ToString("r");
    }
```

Essa operação simplesmente mostra o retorno da DateTime UTC atual menos o valor de HoursBack.

#### <a name="converttotimestamp"></a>ConvertToTimeStamp
``` C#

        /// <summary>
        ///     Converts DateTime to double
        /// </summary>
        /// <param name="currentdateTime"></param>
        /// <returns></returns>
        [Metadata("Converts Universal DateTime to number")]
        [SwaggerResponse(HttpStatusCode.OK, null, typeof (double))]
        [SwaggerResponse(HttpStatusCode.BadRequest, "DateTime is invalid")]
        [SwaggerResponse(HttpStatusCode.InternalServerError)]
        [SwaggerOperation(nameof(ConvertToTimestamp))]
        public double ConvertToTimestamp(
            [Metadata("currentdateTime", "DateTime value to convert")] string currentdateTime)
        {
            double result;

            try
            {
                var uncoded = HttpContext.Current.Server.UrlDecode(currentdateTime);

                var newDateTime = DateTime.Parse(uncoded);
                //create Timespan by subtracting the value provided from the Unix Epoch
                var span = newDateTime - new DateTime(1970, 1, 1, 0, 0, 0, 0).ToLocalTime();

                //return the total seconds (which is a UNIX timestamp)
                result = span.TotalSeconds;
            }
            catch (Exception e)
            {
                throw new Exception("unable to convert to Timestamp", e.InnerException);
            }

            return result;
        }

```

Essa operação converte a resposta da operação GetUtcDate em um valor duplo.

#### <a name="queryfornewpatientdocuments"></a>QueryForNewPatientDocuments
```C#

        /// <summary>
        ///     Query for new Patient Documents
        /// </summary>
        /// <param name="unixTimeStamp"></param>
        /// <returns>IList</returns>
        [Metadata("QueryForNewDocuments",
            "Query for new Documents where the Timestamp is greater than or equal to the DateTime value in the query parameters."
            )]
        [SwaggerOperation("QueryForNewDocuments")]
        [SwaggerResponse(HttpStatusCode.OK, type: typeof (Task<IList<Document>>))]
        [SwaggerResponse(HttpStatusCode.BadRequest, "The syntax of the SQL Statement is incorrect")]
        [SwaggerResponse(HttpStatusCode.NotFound, "No Documents were found")]
        [SwaggerResponse(HttpStatusCode.InternalServerError, "Internal Server Operation Error")]
        // ReSharper disable once ConsiderUsingAsyncSuffix
        public IList<Document> QueryForNewPatientDocuments(
            [Metadata("UnixTimeStamp", "The DateTime value used to search from")] double unixTimeStamp)
        {
            var context = new DocumentDbContext();
            var filterQuery = string.Format(InvariantCulture, "SELECT * FROM Patient p WHERE p._ts >=  {0}",
                unixTimeStamp);
            var options = new FeedOptions {MaxItemCount = -1};


            var collectionLink = UriFactory.CreateDocumentCollectionUri(DocumentDbContext.DatabaseId,
                DocumentDbContext.CollectionId);

            var response =
                context.Client.CreateDocumentQuery<Document>(collectionLink, filterQuery, options).AsEnumerable();

            return response.ToList();
    }

```

Essa operação usa o [SDK do .NET para o Banco de Dados de Documentos](documentdb-sdk-dotnet.md) para criar uma consulta de documento. 

```C#
     CreateDocumentQuery<Document>(collectionLink, filterQuery, options).AsEnumerable();
```

A resposta da operação ConvertToTimeStamp (unixTimeStamp) é passada. A operação retorna uma Lista de documentos, `IList<Document>`.

Anteriormente, falamos sobre o CallbackURL. Para iniciar o fluxo de trabalho em seu Aplicativo Lógico principal, será necessário chamá-lo usando o CallbackURL.

## <a name="callbackurl"></a>CallbackURL
Para começar, você precisará de seu Token do Azure AD.  Pode ser difícil obter esse token. Eu estava procurando um método fácil, e Jeff Hollan, que é um gerente de programa do Aplicativo Lógico do Azure, recomendou o uso do [armclient](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) no PowerShell.  Você pode instalá-lo seguindo as instruções fornecidas.

As operações que você deseja usar são Logon e Chamar API ARM.

Logon: use as mesmas credenciais para fazer logon no Portal do Azure. 

A operação Chamar API ARM é a que gerará seu CallBackURL.

No PowerShell, você a cham da seguinte maneira:    

```powershell

    ArmClient.exe post https://management.azure.com/subscriptions/[YOUR SUBSCRIPTION ID/resourcegroups/[YOUR RESOURCE GROUP]/providers/Microsoft.Logic/workflows/[YOUR LOGIC APP NAME/triggers/manual/listcallbackurl?api-version=2015-08-01-preview

```

O resultado deve ter esta aparência:

```powershell

    https://prod-02.westus.logic.azure.com:443/workflows/12a1de57e48845bc9ce7a247dfabc887/triggers/manual/run?api-version=2015-08-01-prevaiew&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=XXXXXXXXXXXXXXXXXXX

```

Você pode usar uma ferramenta como o [postman](http://www.getpostman.com/) para testar o Aplicativo Lógico principal, conforme mostra na figura a seguir.

![postman](./media/documentdb-change-notification/newpostman.png)

A tabela a seguir lista os parâmetros de Gatilho que constituem o corpo do Aplicativo Lógico do Gatilho de DocDB.

| Parâmetro | Descrição |
| --- | --- |
| GetUtcDate_HoursBack |Usado para definir o número de horas para a data de início da pesquisa |
| sendgridUsername |Usado para definir o número de horas para a data de início da pesquisa |
| sendgridPassword |O nome de usuário para o email de Send Grid |
| EmailTo |O endereço de email que receberá a notificação por email |
| Subject |O Assunto do email |

## <a name="viewing-the-patient-data-in-the-azure-blob-service"></a>Exibição dos dados dos pacientes no serviço Blob do Azure
Acesse sua conta de armazenamento do Azure e selecione Blobs em serviços, conforme mostra a figura a seguir.

![Conta de armazenamento](./media/documentdb-change-notification/docdbstorageaccount.png) 

Você poderá ver as informações do arquivo de blob do Paciente, conforme mostra abaixo.

![Serviço Blob](./media/documentdb-change-notification/blobservice.png)

## <a name="summary"></a>Resumo
Neste passo a passo, você aprendeu o seguinte:

* É possível implementar notificações no Banco de Dados de Documentos.
* Ao usar Aplicativos Lógicos, você pode automatizar o processo.
* Com os Aplicativos Lógicos, você pode reduzir o tempo necessário para entregar um aplicativo.
* Com HTTP, você pode consumir um Aplicativo de API em um Aplicativo Lógico.
* Você pode criar facilmente um CallBackURL que substitui o Ouvinte de HTTP.
* Você pode criar facilmente fluxos de trabalho personalizados com o Designer de Aplicativos Lógicos.

O segredo é planejar e modelar com antecedência seu fluxo de trabalho.

## <a name="next-steps"></a>Próximas etapas
Baixe e use o código do Aplicativo Lógico fornecido no [GitHub](https://github.com/HEDIDIN/DocDbNotifications). Convido você a compilar o aplicativo e enviar as alterações ao repositório. 

