---
title: Atualizações de esquema 1 de agosto de 2015 preview - Aplicativo Lógico do Azure | Microsoft Docs
description: Criar definições de JSON para Aplicativos Lógicos do Azure com a versão de esquema 2015-08-01-preview
author: stepsic-microsoft-com
manager: SyntaxC4
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: 0d03a4d4-e8a8-4c81-aed5-bfd2a28c7f0c
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 05/31/2016
ms.author: stepsic; LADocs
ms.openlocfilehash: bdadc2e33082421500f21d5926ac1e660f4164d4
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2018
ms.locfileid: "32774294"
---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Atualizações de esquema para Aplicativos Lógicos do Azure - visualização de 1º de junho de 2015

Esse esquema e a versão da API para Aplicativos Lógicos do Azure incluem os principais aprimoramentos que tornam a lógica de aplicativos mais confiável e fácil de usar:

* O tipo de ação **APIApp** é chamada agora de [**APIConnection**](#api-connections).
* A ação **Repitir** é chamada agora de [**Foreach**](#foreach).
* O [**aplicativo de API** Ouvinte HTTP](#http-listener) não é mais necessário.
* A ação de chamar fluxos de trabalho secundários usa um [novo esquema](#child-workflows).

<a name="api-connections"></a>

## <a name="move-to-api-connections"></a>Mover para conexões de API

A maior mudança é que você não precisa mais implantar Aplicativos de API em sua Assinatura do Azure para que você possa usar as APIs. Estas são as maneiras como você pode usar APIs:

* APIs gerenciadas
* Suas APIs Web personalizadas

Cada modo é tratado de forma ligeiramente diferente, pois seu gerenciamento e modelos de hospedagem são diferentes. Uma vantagem desse modelo é que você não fica restrito aos recursos que são implantados em seu grupo de recursos do Azure. 

### <a name="managed-apis"></a>APIs gerenciadas

A Microsoft gerencia algumas APIs em seu nome, por exemplo o Office 365, Salesforce, Twitter e FTP. Algumas dessas APIs gerenciadas como o Bing Translate podem ser usadas como estão, enquanto outras exigem configuração, também chamadas de uma *conexão*.

Por exemplo, quando você usa o Office 365, deve criar uma conexão que inclui o token de entrada do Office 365. Seu token é armazenado e atualizado com segurança para que seu aplicativo lógico sempre possa chamar a API do Office 365. Se você quiser se conectar ao seu servidor SQL ou FTP, será necessário criar uma conexão que tenha cadeia de conexão. 

Nessa definição, essas ações são denominadas `APIConnection`. Veja um exemplo de uma conexão que chama o Office 365 para enviar um email:

``` json
{
   "actions": {
      "Send_an_email": {
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
            "method": "POST",
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

O objeto `host` é uma parte das entradas que é exclusiva para conexões de API e contém dessas partes: `api` e `connection`. O objeto `api` especifica a URL de tempo de execução para onde a API gerenciada está hospedada. Você pode ver todas as APIs gerenciadas disponíveis chamando `GET https://management.azure.com/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview`.

Quando você usa uma API, existe a possibilidade dessa API não ter nenhum *parâmetro de conexão* definido. Portanto, se a API não definir esses parâmetros, nenhuma conexão é necessária. Se a API definir esses parâmetros, você deve criar uma conexão com um nome especificado.  
Você faz então referência a esse nome no objeto `connection` dentro do objeto `host`. Para criar uma conexão em um grupo de recursos, chame este método:

```
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/<name>?api-version=2015-08-01-preview
```

Com o seguinte corpo:

``` json
{
   "properties": {
      "api": {
         "id": "/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/managedApis/azureblob"
      },
      "parameterValues": {
         "accountName": "<Azure-storage-account-name-with-different-parameters-for-each-API>"
      }
   },
   "location": "<logic-app-location>"
}
```

### <a name="deploy-managed-apis-in-an-azure-resource-manager-template"></a>Implantar APIs gerenciadas em um modelo do Azure Resource Manager

Você pode criar um aplicativo completo em um modelo do Azure Resource Manager, desde que ele não exija entrada interativa.
Se for necessário entrar, você poderá configurar tudo com o modelo do Azure Resource Manager, mas ainda precisará visitar o portal do Azure a fim de autorizar as conexões. 

``` json
"resources": [ {
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
    },
},
{
   "type": "Microsoft.Logic/workflows",
   "apiVersion": "2015-08-01-preview",
   "name": "[parameters('logicAppName')]",
   "location": "[resourceGroup().location]",
   "dependsOn": ["[resourceId('Microsoft.Web/connections', 'azureblob')]"],
   "properties": {
      "sku": {
         "name": "[parameters('sku')]",
         "plan": {
            "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('svcPlanName'))]"
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
      },
      "definition": {
         "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "type": "Object",
            "$connections": {
               "defaultValue": {},
 
            }
         },
         "triggers": {
            "Recurrence": {
               "type": "Recurrence",
               "recurrence": {
                  "frequency": "Day",
                  "interval": 1
               }
            }
         },
         "actions": {
            "Create_file": {
               "type": "ApiConnection",
               "inputs": {
                  "host": {
                     "api": {
                        "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                     },
                     "connection": {
                       "name": "@parameters('$connections')['azureblob']['connectionId']"
                     }
                  },
                  "method": "POST",
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
         "outputs": {}
      }
   }
} ]
```

Você pode ver neste exemplo que as conexões são apenas recursos que residem em seu grupo de recursos. Elas fazem referência às APIs gerenciadas disponíveis em sua assinatura.

### <a name="your-custom-web-apis"></a>Suas APIs Web personalizadas

Se você usar suas próprias APIs, aqueles não gerenciados pela Microsoft, usar interno **HTTP** ação chamá-los. Para uma experiência ideal, você deve expor um ponto de extremidade do Swagger para sua API. Esse ponto de extremidade permite que o Designer de Aplicativos Lógicos renderize as entradas e saídas de sua API. Sem o Swagger, o designer só poderá mostrar as entradas e saídas como objetos JSON opacos.

Veja um exemplo que mostra a nova propriedade `metadata.apiDefinitionUrl` :

``` json
"actions": {
   "mycustomAPI": {
      "type": "Http",
      "metadata": {
         "apiDefinitionUrl": "https://mysite.azurewebsites.net/api/apidef/"  
      },
      "inputs": {
         "uri": "https://mysite.azurewebsites.net/api/getsomedata",
         "method": "GET"
      }
   }
}
```

Se você hospedar sua API Web no Serviço de Aplicativo do Azure, ela aparecerá automaticamente na lista de ações disponíveis no designer. Caso você não faça isso, será necessário colar a URL diretamente. O ponto de extremidade do Swagger não deve ser autenticado para que possa ser usado dentro do Designer de Aplicativos Lógicos, embora você possa proteger a API com quaisquer métodos com suporte no Swagger.

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>Chamar aplicativos de API implantados com 2015-08-01-preview

Se você tiver implantado um Aplicativo de API, chame esse aplicativo por meio da ação **HTTP**.
Por exemplo, se você usar o Dropbox para listar os arquivos, poderá ter algo assim em sua definição da versão do esquema **2014-12-01-preview**:

``` json
"definition": {
   "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
         "defaultValue": "eyJ0eX...wCn90",
         "type": "String",
         "metadata": {
            "token": {
               "name": "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
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
                "id": "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                "gateway": "https://avdemo.azurewebsites.net"
             },
             "operation": "ListFiles",
             "parameters": {
                "FolderPath": "/myfolder"
             },
             "authentication": {
                "type": "Raw",
                "scheme": "Zumo",
                "parameter": "@parameters('/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
             }
          }
       }
    }
}
```

Agora, você pode construir a ação HTTP como o exemplo a seguir, deixando a seção de parâmetros para a definição do aplicativo lógico inalterada:

``` json
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
            "parameter": "@parameters('/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
         }
      }
   }
}
```

Percorra essas propriedades, uma por vez:

| Propriedade da ação | DESCRIÇÃO |
| --- | --- |
| `type` | `Http` em vez de `APIapp` |
| `metadata.apiDefinitionUrl` | Para usar essa ação no Designer de Aplicativos Lógicos, inclua o ponto de extremidade de metadados que é construído com base em: `{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Construído com base em: `{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Sempre `POST` |
| `inputs.body` | Idêntico aos parâmetros do Aplicativo de API |
| `inputs.authentication` | Idêntico à autenticação do aplicativo de API |

Essa abordagem deve funcionar para todas as ações de Aplicativo de API. No entanto, lembre-se de que esses aplicativos de API anterior não têm mais suporte. Portanto, você deve mover para uma das duas outras opções anteriores, uma API gerenciada ou hospedagem sua API da Web personalizado.

<a name="foreach"></a>

## <a name="renamed-repeat-to-foreach"></a>'repeat' renomeado para 'foreach'

Recebemos muitos comentários de clientes na versão anterior do esquema informando que o nome da ação **Repeat** era confuso e não capturava corretamente que **Repeat** era, na verdade, um loop for each. Portanto, `repeat` foi renomeado para `foreach`. Anteriormente, você deve escrever esta ação, como neste exemplo:

``` json
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
```

Agora você deve escrever esta versão em vez disso:

``` json
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
```

Além disso, a função `repeatItem()`, que é referenciada no item que o loop está processando durante a iteração atual, agora é renomeada `item()`. 

### <a name="reference-outputs-from-foreach"></a>Saídas de referência de 'foreach'

Para simplificar, as saídas de ações `foreach` não são mais encapsuladas em um objeto chamado `repeatItems`. Além disso, com essas alterações, as funções `repeatItem()`, `repeatBody()` e `repeatOutputs()` são removidas.

Portanto, usando o exemplo `repeat` anterior, você obter essas saídas:

``` json
"repeatItems": [ {
   "name": "pingBing",
   "inputs": {
      "uri": "https://www.bing.com/search?q=0",
      "method": "GET"
   },
   "outputs": {
      "headers": { },
      "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
   },
   "status": "Succeeded"
} ]
```

Agora você obtém essas saídas em vez disso:

``` json
[ {
   "name": "pingBing",
      "inputs": {
         "uri": "https://www.bing.com/search?q=0",
         "method": "GET"
      },
      "outputs": {
         "headers": { },
         "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
      },
      "status": "Succeeded"
} ]
```

Anteriormente, para obter o `body` da ação ao fazer referência a essas saídas:

``` json
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
```

Agora você deve escrever esta versão em vez disso:

``` json
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
```

<a name="http-listener"></a>

## <a name="native-http-listener"></a>Ouvinte HTTP nativo:

Os recursos de Ouvinte HTTP agora são internos. Portanto, você não precisa mais implantar um Aplicativo de API do Ouvinte HTTP. Veja [os detalhes completos de como fazer com que seu ponto de extremidade do aplicativo lógico seja chamado aqui](../logic-apps/logic-apps-http-endpoint.md). 

Com essas alterações, removemos o `@accessKeys()` função, que são substituídos com o `@listCallbackURL()` função para obter o ponto de extremidade quando necessário. Além disso, agora você deve definir pelo menos um gatilho em seu aplicativo lógico. Se você quiser `/run` o fluxo de trabalho, deverá ter um dos gatilhos a seguir: `manual`, `apiConnectionWebhook` ou `httpWebhook`.

<a name="child-workflows"></a>

## <a name="call-child-workflows"></a>Chamar fluxos de trabalho secundários

Anteriormente, chamar os fluxos de trabalho secundários exigia ir para esse fluxo de trabalho, obter o token de acesso e colar o token na definição do aplicativo lógico no qual você deseja chamar o fluxo de trabalho secundário. Com o novo esquema, o mecanismo de Aplicativos Lógicos gera automaticamente um SAS em tempo de execução para fluxo de trabalho filho para que você não precise colar informações secretas na definição. Veja um exemplo:

``` json
"myNestedWorkflow": {
   "type": "Workflow",
   "inputs": {
      "host": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/myWorkflow001",
         "triggerName": "myEndpointTrigger"
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

Por fim, há alterações necessárias no fluxo de trabalho secundário. Enquanto antes você podia chamar um fluxo de trabalho filho diretamente, agora você precisa definir um ponto de extremidade de gatilho no fluxo de trabalho para o pai chamar. Em geral, você adicionaria um disparador que possui `manual` digite e, em seguida, usar esse gatilho na definição do pai. Observe que a propriedade `host`, especificamente, tem um `triggerName`, pois você sempre deve especificar qual gatilho está invocando.

## <a name="other-changes"></a>Outras alterações

### <a name="new-queries-property"></a>Nova propriedade ‘queries’

Todos os tipos de ação oferecem suporte a uma nova entrada denominada `queries`. Essa entrada pode ser um objeto estruturado em vez de precisar montar a cadeia de caracteres manualmente.

### <a name="renamed-parse-function-to-json"></a>Função 'parse()' renomeada para 'json()'

Adicionaremos mais tipos de conteúdo em breve e, portanto, renomeamos a função `parse()` para `json()`.

## <a name="coming-soon-enterprise-integration-apis"></a>Em breve: APIs de Integração Corporativa

Não temos versões gerenciadas ainda das APIs de integração do Enterprise, como o AS2. Enquanto isso, você pode usar suas APIs do BizTalk implantadas existentes por meio da ação de HTTP. Para obter detalhes, veja "Uso dos seus aplicativos de API já implantados" no [roteiro de integração](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). 
