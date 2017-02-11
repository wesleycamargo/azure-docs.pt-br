---
title: "Criar definições de Aplicativo Lógico | Microsoft Docs"
description: "Aprenda a escrever a definição JSON para Aplicativos lógicos"
author: jeffhollan
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 253459f431c43b7018fb2a70a966f7652d3f327f


---
# <a name="author-logic-app-definitions"></a>Criar definições de Aplicativo Lógico
Este tópico demonstra como usar as definições de [Aplicativos Lógicos do Azure](app-service-logic-what-are-logic-apps.md) , que se tratam de uma linguagem JSON simples e declarativa. Se você ainda não tiver feito isso ainda, confira primeiro [como Criar um novo Aplicativo Lógico](app-service-logic-create-a-logic-app.md) . Você também pode ler o [material de referência completo da linguagem de definição no MSDN](http://aka.ms/logicappsdocs).

## <a name="several-steps-that-repeat-over-a-list"></a>Várias etapas que se repetem em uma lista
Você pode aproveitar o [tipo foreach](app-service-logic-loops-and-scopes.md) para repetir em uma matriz de até 10 mil itens e executar uma ação para cada um.

## <a name="a-failure-handling-step-if-something-goes-wrong"></a>Uma etapa para tratar de falhas caso algo dê errado
Muitas vezes você deseja ser capaz de gravar uma *etapa de correção* — alguma lógica que é executada se, **e somente se**, uma ou mais de suas chamadas falhou. Neste exemplo estamos obtendo dados de uma variedade de locais, mas se a chamada falhar, desejo gravar uma mensagem por POST em algum lugar, para que possa rastrear essa falha posteriormente:  

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            }
        },
        "postToErrorMessageQueue": {
            "type": "ApiConnection",
            "inputs": "...",
            "runAfter": {
                "readData": ["Failed"]
            }
        }
    },
    "outputs": {}
}
```

Você pode fazer uso da propriedade `runAfter` para especificar que `postToErrorMessageQueue` deverá ser executado somente quando `readData` **Falhar**.  Isso também poderia ser uma lista de valores possíveis e, portanto, `runAfter` poderia ser `["Succeeded", "Failed"]`.

Finalmente, já que agora você tratou o erro, nós não marcamos mais a execução como **Falha**. Como você pode ver, essa prática foi realizada com **Êxito** embora uma etapa tenha falhado, porque eu escrevi a etapa para tratar dessa falha.

## <a name="two-or-more-steps-that-execute-in-parallel"></a>Duas (ou mais) etapas executadas em paralelo
Para a execução de várias ações em paralelo, a propriedade `runAfter` deverá ser equivalente no tempo de execução. 

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            }
        },
        "branch1": {
            "type": "Http",
            "inputs": "...",
            "runAfter": {
                "readData": ["Succeeded"]
            }
        },
        "branch2": {
            "type": "Http",
            "inputs": "...",
            "runAfter": {
                "readData": ["Succeeded"]
            }
        }
    },
    "outputs": {}
}
```

Como você pode ver no exemplo acima, tanto `branch1` quanto `branch2` estão definidos para executar `readData`. Como resultado, ambas essas ramificações serão executadas em paralelo:

![Paralelo](./media/app-service-logic-author-definitions/parallel.png)

Você pode ver que o carimbo de data/hora para ambas as ramificações é idêntico. 

## <a name="join-two-parallel-branches"></a>Unir duas ramificações paralelas
Você pode unir duas ações que foram definidas para executar em paralelo com a adição de itens à propriedade `runAfter` , semelhante ao que está acima.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-04-01-preview/workflowdefinition.json#",
    "actions": {
        "readData": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {},
            "type": "Http"
        },
        "branch1": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "readData": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        },
        "branch2": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "readData": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        },
        "join": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "branch1": [
                    "Succeeded"
                ],
                "branch2": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        }
    },
    "contentVersion": "1.0.0.0",
    "outputs": {},
    "parameters": {},
    "triggers": {
        "manual": {
            "inputs": {
                "schema": {}
            },
            "kind": "Http",
            "type": "Request"
        }
    }
}
```

![Paralelo](./media/app-service-logic-author-definitions/join.png)

## <a name="mapping-items-in-a-list-to-some-different-configuration"></a>Mapear itens em uma lista para algumas configurações diferentes
Em seguida, digamos que desejamos obter conteúdo completamente diferente dependendo do valor de uma propriedade. Podemos criar um mapa de valores para destinos, como um parâmetro:  

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "specialCategories": {
            "defaultValue": ["science", "google", "microsoft", "robots", "NSA"],
            "type": "Array"
        },
        "destinationMap": {
            "defaultValue": {
                "science": "http://www.nasa.gov",
                "microsoft": "https://www.microsoft.com/en-us/default.aspx",
                "google": "https://www.google.com",
                "robots": "https://en.wikipedia.org/wiki/Robot",
                "NSA": "https://www.nsa.gov/"
            },
            "type": "Object"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "getArticles": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
            },
            "conditions": []
        },
        "getSpecialPage": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
            },
            "conditions": [{
                "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)"
            }],
            "forEach": "@body('getArticles').responseData.feed.entries"
        }
    }
}
```

Nesse caso, primeiro obtemos uma lista de artigos e, em seguida, a segunda etapa procura em um mapa, com base na categoria que foi definida como parâmetro, a URL da qual obter o conteúdo. 

Dois itens aos quais prestar atenção aqui: a função [`intersection()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#intersection) é usada para verificar se a categoria corresponde a uma das categorias conhecidas definidas. Em segundo lugar, uma vez que obtemos a categoria, podemos extrair o item do mapa usando colchetes: `parameters[...]`. 

## <a name="working-with-strings"></a>Trabalhando com Cadeias de Caracteres
Há diversas funções que podem ser usadas para manipular a cadeias de caracteres. Vejamos um exemplo no qual temos uma cadeia de caracteres que queremos passar para um sistema, mas não estamos confiantes que a codificação de caracteres será tratada apropriadamente. Uma opção é para codificar essa cadeia de caracteres em formato base64. No entanto, para evitar o uso de caracteres de escape em uma URL, vamos substituir alguns caracteres. 

Também queremos uma subcadeia de caracteres do nome do autor da ordem, porque os cinco primeiros caracteres não são usados.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "order": {
            "defaultValue": {
                "quantity": 10,
                "id": "myorder1",
                "orderer": "NAME=Stèphén__Šīçiłianö"
            },
            "type": "Object"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "order": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').orderer,5,sub(length(parameters('order').orderer), 5) )),'+','-') ,'/' ,'_' )}"
            }
        }
    },
    "outputs": {}
}
```

Trabalho de dentro para fora:

1. Obter o [`length()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#length) do nome do autor da ordem, que retorna o número total de caracteres
2. Subtrair 5 (porque desejamos uma cadeia de caracteres mais curta)
3. Coletar efetivamente o [`substring()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#substring) . Vamos começar no `5` do índice e seguir pelo restante da cadeia de caracteres.
4. Converter esta subcadeia de caracteres em uma cadeia de caracteres [`base64()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#base64)
5. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) todos os caracteres `+` com `-`
6. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) todos os caracteres `/` com `_`

## <a name="working-with-date-times"></a>Trabalho com valores de Data/Hora
Valores de Data/Hora podem ser úteis, especialmente quando você estiver tentando extrair dados de uma fonte de dados na qual, naturalmente, não há suporte para **Gatilhos**.  Você também pode usar valores de Data/Hora para calcular quanto tempo diversas etapas estão levando. 

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{parameters('order').id}"
      }
    },
    "ifTimingWarning": {
      "type": "If",
      "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
      "actions": {
        "timingWarning": {
          "type": "Http",
          "inputs": {
            "method": "GET",
            "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
          }
        }
      },
      "runAfter": {
        "order": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

Neste exemplo, estamos extraindo o `startTime` da etapa anterior. Em seguida obtemos a hora atual e subtraímos um segundo: [`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/mt643789.aspx#addseconds) (você pode usar outras unidades de tempo, como `minutes` ou `hours`). Por fim, podemos comparar esses dois valores. Se o primeiro for menor do que o segundo, isso significa que mais de um segundo decorreu desde o primeiro momento em que a ordem foi emitida. 

Observe também que podemos usar formatadores de cadeia de caracteres para formatar datas: na cadeia de caracteres de consulta, utilizo [`utcnow('r')`](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow) para obter o RFC1123. Toda a formatação das datas [está documentada no MSDN](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow). 

## <a name="using-deployment-time-parameters-for-different-environments"></a>Uso de parâmetros de tempo de implantação para ambientes diferentes
É comum ter um ciclo de vida de implantação em que você tem um ambiente de desenvolvimento, um ambiente de preparo e um ambiente de produção. Em todos eles você pode, por exemplo, desejar a mesma definição, mas usar bancos de dados diferentes. Do mesmo modo, talvez você queira usar a mesma definição em muitas regiões diferentes para alta disponibilidade, mas deseje que cada instância de Aplicativo lógico se comunique com o banco de dados dessa região. 

Observe que isso é diferente de pegar parâmetros diferentes em *tempo de execução*; para isso, você deve usar a função `trigger()` conforme indicado acima. 

Você pode iniciar com uma definição muito simplista como essa:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "uri": {
            "type": "string"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('uri')"
            }
        }
    },
    "outputs": {}
}
```

Então, na verdadeira solicitação `PUT` para o Aplicativo lógico, você pode fornecer o parâmetro `uri`. Observe que, como não existe mais um valor padrão, esse parâmetro é requerido na carga do Aplicativo lógico:

```
{
    "properties": {},
        "definition": {
          // Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
``` 

Em cada ambiente, você pode fornecer então um valor diferente para o parâmetro `connection` . 

Consulte a [documentação da API REST](https://msdn.microsoft.com/library/azure/mt643787.aspx) para todas as opções que você tem para criar e gerenciar Aplicativos lógicos. 




<!--HONumber=Nov16_HO3-->


