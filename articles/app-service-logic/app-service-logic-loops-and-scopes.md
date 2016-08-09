<properties
   pageTitle="Loops, escopos e debatching de Aplicativos Lógicos | Microsoft Azure"
   description="Conceitos de loop, escopo e debatching de Aplicativo Lógico"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/14/2016"
   ms.author="jehollan"/>
   
# Loops, Escopos e Debatch dos Aplicativos Lógicos
  
>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2016-04-01-preview e posterior dos Aplicativos Lógicos. Os conceitos são semelhantes a esquemas mais antigos, mas os escopos só estão disponíveis para este esquema e posteriores.
  
## Loop ForEach e Matrizes
  
Os Aplicativos Lógicos permitem que você faça um loop em um conjunto de dados e execute uma ação para cada item. Isso é possível por meio da ação `foreach`. No designer, você pode especificar a adição de um loop for each. Depois de selecionar a matriz em que você deseja iterar, você poderá começar a adicionar ações. No momento, você está limitado a apenas uma ação por loop foreach, mas essa restrição será eliminada nas próximas semanas. Uma vez no loop, você poderá começar a especificar o que deve ocorrer em cada valor da matriz.

Se você estiver usando o modo de exibição de código, poderá especificar um loop for each como o abaixo. Este é um exemplo de um loop for each que envia um email para cada endereço de email com 'microsoft.com':

```
{
    "email_filter": {
        "type": "query",
        "inputs": {
            "from": "@triggerBody()['emails']",
            "where": "@contains(item()['email'], 'microsoft.com')
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
                }
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                }
                }
            }
        },
        "runAfter":{
            "email_filter": [ "Succeeded" ]
        }
    }
}
```
  
  Uma ação `foreach` pode iterar em matrizes até 5.000 linhas. Cada iteração pode ser executada eem paralelo, portanto, pode ser necessário adicionar mensagens a uma fila, se houver necessidade de controle de fluxo.
  
## Loop Until
  
  Você pode executar uma ação ou uma série de ações até que uma condição seja atendida. O cenário mais comum para isso é chamar um ponto de extremidade até você chegar à resposta que está procurando. No designer, você pode especificar a adição de um loop until. Depois de adicionar ações dentro do loop, você poderá definir a condição de saída, bem como os limites do loop. Há um atraso de um minuto entre os ciclos de loop.
  
  Se você estiver usando o modo de exibição de código, poderá especificar um loop unitl como o abaixo. Este é um exemplo de chamada a um ponto de extremidade HTTP até que o corpo da resposta tenha o valor 'Concluído'. Ele será concluído quando a
  
  * Resposta HTTP tiver o status 'Concluído'
  * Ele tentou por uma hora
  * Ele entrou em loop 100 vezes
  
  ```
  {
      "until_successful":{
        "type": "until",
        "expression": "@equals(actions('http')['status'], 'Completed'),
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
  
## SplitOn e Debatching

Às vezes, um gatilho pode receber uma matriz de itens nos quais deseja fazer debatch e iniciar um fluxo de trabalho por item. Isso pode ser feito por meio do comando `spliton`. Por padrão, se o gatilho swagger especificar uma carga que seja uma matriz, um `spliton` será adicionado e iniciará uma execução por item. SplitOn só pode ser adicionado a um gatilho. Isso pode ser manualmente configurado ou substituído na exibição de código de definição. Atualmente, o SplitOn pode fazer debatch em matrizes de até 5.000 itens. Você não pode ter um `spliton` e também implementar o padrão de resposta síncrona. Qualquer fluxo de trabalho chamado com uma ação `response` além de `spliton` será executado de forma assíncrona e enviará uma resposta `202 Accepted` imediata.

SplitOn pode ser especificado no modo de exibição de código como o exemplo a seguir. Isso recebe uma matriz de itens e faz debatch em cada linha.

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

## Escopos

É possível agrupar uma série de ações usando um escopo. Isso é particularmente útil para implementar o tratamento de exceções. No designer, você pode adicionar um novo escopo e começar a adicionar ações dentro dele. É possível definir escopos no modo de exibição de código semelhante ao seguinte:


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

<!---HONumber=AcomDC_0727_2016-->