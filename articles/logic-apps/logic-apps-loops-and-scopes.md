---
title: "Criar loops e escopos ou retirar dados de lote em fluxos de trabalho - Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Criar loops para iterar por meio de dados, as ações de grupo em escopos, ou retirar dados de lote para iniciar mais fluxos de trabalho em Aplicativos Lógicos do Azure."
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 9cdbe4a12a0b16341a1e52f176901045baf327b5
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2017
---
# <a name="logic-apps-loops-scopes-and-debatching"></a>Loops, Escopos e Debatch dos Aplicativos Lógicos
  
Os Aplicativos Lógicos fornecem várias maneiras de trabalhar com matrizes, coleções, lotes e loops em um fluxo de trabalho.
  
## <a name="foreach-loop-and-arrays"></a>Matrizes e loop ForEach
  
Os Aplicativos Lógicos permitem que você faça um loop em um conjunto de dados e execute uma ação para cada item.  Um loop em uma coleção é possível por meio da ação `foreach`.  No designer, você pode adicionar um loop for each.  Depois de selecionar a matriz em que você deseja iterar, você poderá começar a adicionar ações.  Você pode adicionar várias ações por loop foreach.  Uma vez no loop, você poderá começar a especificar o que deve ocorrer em cada valor da matriz.

  Este exemplo envia um email para cada endereço de email que contenha 'microsoft.com'. Se você estiver usando o modo de exibição de código, especifique um loop for each como no exemplo a seguir:

``` json
{
    "email_filter": {
        "type": "query",
        "inputs": {
            "from": "@triggerBody()['emails']",
            "where": "@contains(item()['email'], 'microsoft.com')"
        }
    },
    "forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "actions": {
            "send_email": {
                "type": "ApiConnection",
                "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                },
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                },
                }
            }
        },
        "runAfter":{
            "email_filter": [ "Succeeded" ]
        }
    }
}
```
  
  Uma ação `foreach` pode iterar em matrizes com milhares de entidades.  Por padrão, as iterações são executadas paralelamente.  Consulte [Limites e configuração](logic-apps-limits-and-config.md) para obter detalhes sobre os limites e a simultaneidade da matriz.

### <a name="sequential-foreach-loops"></a>Loops ForEach sequenciais

Para habilitar um loop foreach para ser executado sequencialmente, a opção de operação `Sequential` deve ser adicionada.

``` json
"forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "operationOptions": "Sequential",
        "..."
}
```
  
## <a name="until-loop"></a>Loop Until
  
  Você pode executar uma ação ou uma série de ações até que uma condição seja atendida.  O cenário mais comum para o uso de um loop until é chamar um ponto de extremidade até você chegar à resposta que está procurando.  No designer, você pode especificar a adição de um loop until.  Depois de adicionar ações dentro do loop, você poderá definir a condição de saída, bem como os limites do loop.
  
  Este exemplo chama um ponto de extremidade HTTP até que o corpo da resposta tenha o valor 'Concluído'.  Ele é concluído quando: 
  
  * Resposta HTTP tiver o status 'Concluído'
  * Tiver tentando por uma hora
  * Ele entrou em loop 100 vezes
  
  Se você estiver usando o modo de exibição de código, especifique um loop until como no exemplo a seguir:
  
  ``` json
  {
      "until_successful":{
        "type": "until",
        "expression": "@equals(actions('http')['status'], 'Completed')",
        "limit": {
            "count": 100,
            "timeout": "PT1H"
        },
        "actions": {
            "create_resource": {
                "type": "http",
                "inputs": {
                    "url": "http://provisionRseource.com",
                    "body": {
                        "resourceId": "@triggerBody()"
                    }
                }
            }
        }
      }
  }
  ```
  
## <a name="spliton-and-debatching"></a>SplitOn e debatching

Às vezes, um gatilho pode receber uma matriz de itens nos quais deseja fazer debatch e iniciar um fluxo de trabalho por item.  Esse debatching pode ser feito por meio do comando `spliton`.  Por padrão, se o gatilho swagger especificar uma carga que seja uma matriz, um `spliton` será adicionado. O comando `spliton` inicia uma execução por item na matriz.  Só é possível adicionar SplitOn a um gatilho que possa ser configurado ou substituído manualmente. Você não pode ter um `spliton` e também implementar o padrão de resposta síncrona.  Qualquer fluxo de trabalho chamado com uma ação `response` além de `spliton` será executado de forma assíncrona e enviará uma resposta `202 Accepted` imediata.  

  Esse exemplo recebe uma matriz de itens e faz debatch em cada linha. SplitOn pode ser especificado no modo de exibição de código como o exemplo a seguir:

```
{
    "myDebatchTrigger": {
        "type": "Http",
        "inputs": {
            "url": "http://getNewCustomers",
        },
        "recurrence": {
            "frequencey": "Second",
            "interval": 15
        },
        "spliton": "@triggerBody()['rows']"
    }
}
```

## <a name="scopes"></a>Escopos

É possível agrupar uma série de ações usando um escopo.  Os escopos são particularmente úteis para implementar o tratamento de exceções.  No designer, você pode adicionar um novo escopo e começar a adicionar ações dentro dele.  É possível definir escopos no modo de exibição de código semelhante ao seguinte exemplo:


```
{
    "myScope": {
        "type": "scope",
        "actions": {
            "call_bing": {
                "type": "http",
                "inputs": {
                    "url": "http://www.bing.com"
                }
            }
        }
    }
}
```
