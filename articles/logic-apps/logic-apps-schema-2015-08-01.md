---
title: "Novo versão de esquema 2015-08-01-preview"
description: "Aprenda a escrever a definição JSON para os aplicativos lógicos mais recentes"
author: stepsic-microsoft-com
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 0d03a4d4-e8a8-4c81-aed5-bfd2a28c7f0c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2016
ms.author: stepsic
translationtype: Human Translation
ms.sourcegitcommit: 061b68ec5fc9c14fd0c3f00cc0be9f09717fc1ad
ms.openlocfilehash: 8f1e2680aff5c1d5c9b90654a334cae2245db5fb
ms.lasthandoff: 02/15/2017


---
# <a name="new-schema-version-2015-08-01-preview"></a>Novo versão de esquema 2015-08-01-preview
A nova versão de API e esquema para Aplicativo Lógico do Azure tem uma série de aprimoramentos que melhoram a confiabilidade e a facilidade de uso dos aplicativos lógicos. Há quatro diferenças principais:

1. O tipo de ação **APIApp** foi atualizado para um novo tipo de ação **APIConnection**.
2. **Repeat** foi renomeado para **Foreach**.
3. O aplicativo de API **Ouvinte HTTP** não é mais necessário.
4. A ação de chamar fluxos de trabalho secundários usa um novo esquema.

## <a name="1-moving-to-api-connections"></a>1. Mudança para conexões de API
A maior mudança é que você não precisa mais implantar aplicativos de API em sua Assinatura do Azure para usar as APIs. Existem duas maneiras de usar as APIs:

* APIs gerenciadas
* Suas APIs Web personalizadas

Cada modo é tratado de forma ligeiramente diferente, pois seu gerenciamento e modelos de hospedagem são diferentes. Uma vantagem desse modelo é que você não fica restrito aos recursos que são implantados em seu Grupo de Recursos. 

### <a name="managed-apis"></a>APIs gerenciadas
A Microsoft gerencia algumas APIs em seu nome, por exemplo o Office 365, Salesforce, Twitter e FTP. Algumas dessas APIs gerenciadas como o Bing Translate podem ser usadas como estão, enquanto outras exigem configuração. Essa configuração é denominada *conexão*.

Por exemplo, quando você usa o Office 365, precisa criar uma conexão que contém o token de entrada do Office 365. Esse token é armazenado e atualizado com segurança para que seu aplicativo lógico sempre possa chamar a API do Office 365. Como alternativa, se você quiser se conectar ao seu servidor SQL ou FTP, será necessário criar uma conexão que tenha cadeia de conexão. 

Nessa definição, essas ações são denominadas `APIConnection`. Veja um exemplo de uma conexão que chama o Office 365 para enviar um email:

```
{
    "actions": {
        "Send_Email": {
            "type": "ApiConnection",
            "inputs": {
                "host": {
                    "api": {
                        "runtimeUrl": "https://msmanaged-na.azure-apim.net/apim/office365"
                    },
                    "connection": {
                        "name": "@parameters('$connections')['shared_office365']['connectionId']"
                    }
                },
                "method": "post",
                "body": {
                    "Subject": "Reminder",
                    "Body": "Don't forget!",
                    "To": "me@contoso.com"
                },
                "path": "/Mail"
            }
        }
    }
}
```

O objeto `host` é uma fração das entradas que é exclusiva para conexões de API e contém duas partes: `api` e `connection`.

`api` tem a URL de execução de onde a API gerenciada está hospedada. Você pode ver todas as APIs gerenciadas disponíveis chamando `GET https://management.azure.com/subscriptions/{subid}/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview`.

Quando você usa uma API, existe a possibilidade de ela não ter nenhum **parâmetro de conexão** definido ou de ter algum. Se a API não tiver, nenhuma **conexão** será necessária. Se a API tiver, você precisará criar uma conexão. Quando você criar essa conexão, a conexão terá o nome que você escolher. Você faz então referência ao nome no objeto `connection` dentro do objeto `host`. Para criar uma conexão em um grupo de recursos, chame:

```
PUT https://management.azure.com/subscriptions/{subid}/resourceGroups/{rgname}/providers/Microsoft.Web/connections/{name}?api-version=2015-08-01-preview
```

Com o seguinte corpo:

```
{
  "properties": {
    "api": {
      "id": "/subscriptions/{subid}/providers/Microsoft.Web/managedApis/azureblob"
    },
    "parameterValues": {
        "accountName": "{The name of the storage account -- the set of parameters is different for each API}"
    }
  },
  "location": "{Logic app's location}"
}
```

### <a name="deploying-managed-apis-in-an-azure-resource-manager-template"></a>Implantando APIs gerenciadas em um modelo do Azure Resource Manager
Você pode criar um aplicativo completo em um modelo do Azure Resource Manager, desde que ele não exija entrada interativa.
Se for necessário entrar, você poderá configurar tudo com o modelo do Azure Resource Manager, mas ainda precisará visitar o portal a fim de autorizar as conexões. 

```
    "resources": [{
        "apiVersion": "2015-08-01-preview",
        "name": "azureblob",
        "type": "Microsoft.Web/connections",
        "location": "[resourceGroup().location]",
        "properties": {
            "api": {
                "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
            },
            "parameterValues": {
                "accountName": "[parameters('storageAccountName')]",
                "accessKey": "[parameters('storageAccountKey')]"
            }
        }
    }, {
        "type": "Microsoft.Logic/workflows",
        "apiVersion": "2015-08-01-preview",
        "name": "[parameters('logicAppName')]",
        "location": "[resourceGroup().location]",
        "dependsOn": ["[resourceId('Microsoft.Web/connections', 'azureblob')]"
        ],
        "properties": {
            "sku": {
                "name": "[parameters('sku')]",
                "plan": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/',parameters('svcPlanName'))]"
                }
            },
            "definition": {
                "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
                "actions": {
                    "Create_file": {
                        "type": "apiconnection",
                        "inputs": {
                            "host": {
                                "api": {
                                    "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                                },
                                "connection": {
                                    "name": "@parameters('$connections')['azureblob']['connectionId']"
                                }
                            },
                            "method": "post",
                            "queries": {
                                "folderPath": "[concat('/', parameters('containerName'))]",
                                "name": "helloworld.txt"
                            },
                            "body": "@decodeDataUri('data:, Hello+world!')",
                            "path": "/datasets/default/files"
                        },
                        "conditions": []
                    }
                },
                "contentVersion": "1.0.0.0",
                "outputs": {},
                "parameters": {
                    "$connections": {
                        "defaultValue": {},
                        "type": "Object"
                    }
                },
                "triggers": {
                    "recurrence": {
                        "type": "Recurrence",
                        "recurrence": {
                            "frequency": "Day",
                            "interval": 1
                        }
                    }
                }
            },
            "parameters": {
                "$connections": {
                    "value": {
                        "azureblob": {
                            "connectionId": "[concat(resourceGroup().id,'/providers/Microsoft.Web/connections/azureblob')]",
                            "connectionName": "azureblob",
                            "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
                        }

                    }
                }
            }
        }
    }]
```

Você pode ver neste exemplo que as conexões são apenas recursos que residem em seu grupo de recursos. Elas fazem referência às APIs gerenciadas disponíveis em sua assinatura.

### <a name="your-custom-web-apis"></a>Suas APIs Web personalizadas
Se você usar suas próprias APIs (especificamente, não aquelas gerenciadas pela Microsoft), deverá usar a ação **HTTP** interna para chamá-las. Para uma experiência ideal, você deve expor um ponto de extremidade do Swagger para sua API. Esse ponto de extremidade permite que o Designer de Aplicativos Lógicos renderize as entradas e saídas de sua API. Sem o Swagger, o designer só poderá mostrar as entradas e saídas como objetos JSON opacos.

Veja um exemplo que mostra a nova propriedade `metadata.apiDefinitionUrl` :

```
{
   "actions": {
        "mycustomAPI": {
            "type": "http",
            "metadata": {
              "apiDefinitionUrl": "https://mysite.azurewebsites.net/api/apidef/"  
            },
            "inputs": {
                "uri": "https://mysite.azurewebsites.net/api/getsomedata",
                "method": "GET"
            }
        }
    }
}
```

Se você hospedar sua API Web no **Serviço de Aplicativo**, ela aparecerá automaticamente na lista de ações disponíveis no designer. Caso você não faça isso, será necessário colar a URL diretamente. O ponto de extremidade do Swagger não deve ser autenticado para que possa ser usado dentro do Designer de Aplicativos Lógicos (embora você possa proteger a API com quaisquer métodos com suporte no Swagger).

### <a name="using-your-already-deployed-api-apps-with-2015-08-01-preview"></a>Usando seus aplicativos de API já implantados com 2015-08-01-preview
Se você implantou anteriormente um aplicativo da API, chame-o por meio da ação **HTTP** .

Por exemplo, se você usar o Dropbox para listar os arquivos, poderá ter algo assim em sua definição da versão do esquema **2014-12-01-preview**:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
            "defaultValue": "eyJ0eX...wCn90",
            "type": "String",
            "metadata": {
                "token": {
                    "name": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
                }
            }
        }
    },
    "actions": {
        "dropboxconnector": {
            "type": "ApiApp",
            "inputs": {
                "apiVersion": "2015-01-14",
                "host": {
                    "id": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                    "gateway": "https://avdemo.azurewebsites.net"
                },
                "operation": "ListFiles",
                "parameters": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

Você pode construir a ação HTTP equivalente, conforme mostrado abaixo (a seção parâmetros da definição do aplicativo lógico permanece inalterada):

```
{
    "actions": {
        "dropboxconnector": {
            "type": "Http",
            "metadata": {
              "apiDefinitionUrl": "https://avdemo.azurewebsites.net/api/service/apidef/dropboxconnector/?api-version=2015-01-14&format=swagger-2.0-standard"  
            },
            "inputs": {
                "uri": "https://avdemo.azurewebsites.net/api/service/invoke/dropboxconnector/ListFiles?api-version=2015-01-14",
                "method": "POST",
                "body": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

Percorra essas propriedades, uma por vez:

| Propriedade da ação | Descrição |
| --- | --- |
| `type` |`Http` em vez de `APIapp` |
| `metadata.apiDefinitionUrl` |Para usar essa ação no Designer de Aplicativos Lógicos, inclua o ponto de extremidade de metadados que é construído com base em: `{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` |Construído com base em: `{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` |Sempre `POST` |
| `inputs.body` |Idêntico aos parâmetros do aplicativo de API |
| `inputs.authentication` |Idêntico à autenticação do aplicativo de API |

Essa abordagem deve funcionar para todas as ações de aplicativo de API. No entanto, lembre-se de que esses aplicativos de API anteriores não recebem mais suporte e que você deve mudar para uma das duas outras opções acima (uma API gerenciada ou hospedagem de sua API Web personalizada).

## <a name="2-repeat-renamed-to-foreach"></a>2. Repeat renomeado para Foreach

Recebemos muitos comentários de clientes na versão anterior do esquema informando que **Repeat** era confuso e não capturava corretamente que **Repeat** era, na verdade, um loop for each. Como resultado, **Repeat** foi renomeado para **Foreach**. Por exemplo, anteriormente você escreveria:

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "repeat": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{repeatItem()}"
            }
        }
    }
}
```

Agora você escreveria:

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "foreach": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{item()}"
            }
        }
    }
}
```

Anteriormente, a função `@repeatItem()` era usada para fazer referência ao item atual sendo iterado. Essa função foi agora simplificada para `@item()`. 

### <a name="referencing-the-outputs-of-the-foreach"></a>Como fazer referência às saídas do Foreach
Para simplificar ainda mais, as saídas das ações **Foreach** não são colocadas em um objeto denominado **repeatItems**. Enquanto as saídas do repeat acima eram:

```
{
    "repeatItems": [
        {
            "name": "pingBing",
            "inputs": {
                "uri": "https://www.bing.com/search?q=0",
                "method": "GET"
            },
            "outputs": {
                "headers": { },
                "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
            }
            "status": "Succeeded"
        }
    ]
}
```

Agora, essas saídas são:

```
[
    {
        "name": "pingBing",
        "inputs": {
            "uri": "https://www.bing.com/search?q=0",
            "method": "GET"
        },
        "outputs": {
            "headers": { },
            "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
        }
        "status": "Succeeded"
    }
]
```

Ao fazer referência a essas saídas, para chegar ao corpo da ação, você precisaria:

```
{
    "actions": {
        "secondAction": {
            "type": "Http",
            "repeat": "@outputs('pingBing').repeatItems",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com",
                "body": "@repeatItem().outputs.body"
            }
        }
    }
}
```

Agora você pode fazer:

```
{
    "actions": {
        "secondAction": {
            "type": "Http",
            "foreach": "@outputs('pingBing')",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com",
                "body": "@item().outputs.body"
            }
        }
    }
}
```

Com essas alterações, as funções `@repeatItem()`, `@repeatBody()` e `@repeatOutputs()` foram removidas.

## <a name="3-native-http-listener"></a>3. Ouvinte HTTP nativo:
Os recursos de Ouvinte HTTP agora são internos, portanto, você não precisa implantar mais um aplicativo de API de Ouvinte HTTP. Leia [os detalhes completos de como fazer com que seu ponto de extremidade do aplicativo lógico seja chamado aqui](../logic-apps/logic-apps-http-endpoint.md). 

Com essas alterações, a função `@accessKeys()` foi removida e substituída pela função `@listCallbackURL()` para obter o ponto de extremidade (quando necessário). Além disso, agora você deve definir pelo menos um gatilho em seu aplicativo lógico. Se você quiser `/run` o fluxo de trabalho, precisará ter um dos gatilhos a seguir: `manual`, `apiConnectionWebhook` ou `httpWebhook`

## <a name="4-calling-child-workflows"></a>4. Chamando os fluxos de trabalho secundários
Anteriormente, chamar os fluxos de trabalho secundários exigia ir para esse fluxo de trabalho, obter o token de acesso e colar isso na definição do aplicativo lógico no qual você deseja chamar o secundário. Com a nova versão do esquema, o mecanismo de Aplicativos Lógicos gera automaticamente um SAS em tempo de execução para fluxo de trabalho filho, o que significa que você não precisa colar informações secretas na definição.  Aqui está um exemplo:

```
"mynestedwf": {
    "type": "workflow",
    "inputs": {
        "host": {
            "id": "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName": "myendpointtrigger"
        },
        "queries": {
            "extrafield": "specialValue"
        },
        "headers": {
            "x-ms-date": "@utcnow()",
            "Content-type": "application/json"
        },
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        }
    },
    "conditions": []
}
```

Um segundo aprimoramento é que demos aos fluxos de trabalho filho acesso completo à solicitação de entrada. Isso significa que você pode passar parâmetros na seção *consultas* e no objeto *cabeçalhos*, e que pode definir completamente o corpo.

Por fim, há alterações necessárias no fluxo de trabalho secundário. Enquanto antes você podia chamar um fluxo de trabalho filho diretamente, agora você precisa definir um ponto de extremidade de gatilho no fluxo de trabalho para o pai chamar. Em geral, isso significa que você adicionará um gatilho do tipo **manual** e o usará na definição pai. Observe que a propriedade `host`, especificamente, tem um `triggerName`, pois você sempre deve especificar qual gatilho está invocando.

## <a name="other-changes"></a>Outras alterações
### <a name="new-queries-property"></a>Nova propriedade de consultas
Agora, todos os tipos de ação oferecem suporte a uma nova entrada denominada **consultas**. Essa entrada pode ser um objeto estruturado em vez de precisar montar a cadeia de caracteres manualmente.

### <a name="parse-function-renamed"></a>Função parse() renomeada
Estaremos adicionando mais tipos de conteúdo em breve, portanto, renomeamos a função `parse()` para `json()`.

## <a name="coming-soon-enterprise-integration-apis"></a>Em breve: APIs de Integração Corporativa
Ainda não temos versões gerenciadas das APIs de Integração Corporativa disponíveis (como AS2). Essas APIs estarão disponíveis em breve, conforme abordado no [mapa](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). Enquanto isso, você pode usar suas APIs BizTalk implantadas existentes por meio da ação HTTP, conforme abordado acima em "Como usar seus aplicativos de API já implantados".


