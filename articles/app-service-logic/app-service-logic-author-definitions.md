<properties 
	pageTitle="Criar definições de Aplicativo Lógico" 
	description="Aprenda a escrever a definição JSON para Aplicativos lógicos." 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/08/2015"
	ms.author="stepsic"/>
	
#Criar definições de Aplicativo Lógico
Este tópico demonstra como usar as definições de [Aplicativos Lógicos de Serviços de Aplicativos](app-service-logic-what-are-logic-apps.md), que se tratam de uma linguagem JSON simples e declarativa. Se você ainda não tiver feito isso ainda, confira primeiro [como Criar um novo Aplicativo Lógico](../app-service-create-a-logic-app.md). Você também pode ler o [material de referência completo da linguagem de definição no MSDN](https://msdn.microsoft.com/library/azure/dn948512.aspx).

## Várias etapas que se repetem em uma lista

Um padrão comum é ter uma etapa que obtém uma lista de itens e, em seguida, você tem na lista uma série de duas ou mais ações que deseja executar.

![Repetir em listas](./media/app-service-logic-author-definitions/repeatoverlists.png)

Neste exemplo, há três ações:

1. Obtenha uma lista de artigos. Isso retorna um objeto que contém uma matriz.

2. Uma ação que vai para uma propriedade de link em cada artigo, a qual retornará o verdadeiro local desse artigo.

3. Uma ação que itera sobre todos os resultados da segunda ação, para baixar os artigos em si.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "triggers": {},
    "actions": {
        "getArticles": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
            }
        },
        "readLinks": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@repeatItem().link"
            },
            "repeat": "@body('getArticles').responseData.feed.entries"
        },
        "downloadLinks": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@repeatOutputs().headers.location"
            },
            "conditions": [
                {
                    "expression": "@not(equals(actions('readLinks').status, 'Skipped'))"
                }
            ],
            "repeat": "@actions('readLinks').outputs.repeatItems"
        }
    },
    "outputs": {}
}
```

Conforme abordado em [usar recursos de aplicativo lógico](app-service-logic-use-logic-app-features.md), você itera sobre a primeira lista usando a propriedade `repeat:` na segunda ação. Porém, para a terceira ação, você deve selecionar a propriedade `@actions('readLinks').outputs.repeatItems`, porque a segunda foi executada para cada artigo.

Dentro da ação, você pode usar qualquer uma das funções [`repeatItem()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatItem), [`repeatOutputs()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatOutputs) ou [`repeatBody()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatBody). Neste exemplo, desejei obter o cabeçalho `location`, por isso usei a função [`repeatOutputs()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatOutputs) para obter as saídas da execução da segunda ação sobre a qual estamos agora iterando.

## Mapear itens em uma lista para algumas configurações diferentes

Em seguida, digamos que desejamos obter conteúdo completamente diferente dependendo do valor de uma propriedade. Podemos criar um mapa de valores para destinos, como um parâmetro.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "specialCategories": {
            "defaultValue": [
                "science",
                "google",
                "microsoft",
                "robots",
                "NSA"
            ],
            "type": "Array"
        },
        "destinationMap": {
            "defaultValue": {
                "science": "http://www.nasa.gov",
                "microsoft": "https://www.microsoft.com/pt-BR/default.aspx",
                "google": "https://www.google.com",
                "robots": "https://en.wikipedia.org/wiki/Robot",
                "NSA": "https://www.nsa.gov/"
            },
            "type": "Object"
        }
    },
    "triggers": {},
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
            "repeat": "@body('getArticles').responseData.feed.entries",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(repeatItem().categories, parameters('specialCategories')))]"
            },
            "conditions": [
                {
                    "expression": "@greater(length(intersection(repeatItem().categories, parameters('specialCategories'))), 0)"
                }
            ]
        }
    }
}
```

Nesse caso, primeiro obtemos uma lista de artigos e, em seguida, a segunda etapa procura em um mapa, com base na categoria que foi definida como parâmetro, a URL da qual obter o conteúdo.

Dois itens aos quais prestar atenção aqui: a função [`intersection()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#intersection) é usada para verificar se a categoria corresponde a uma das categorias conhecidas definidas. Em segundo lugar, uma vez que obtemos a categoria, podemos extrair o item do mapa usando colchetes: `parameters[...]`.

## Encadear/aninhar Aplicativos Lógicos enquanto eles são repetidos em uma lista

Muitas vezes pode ser mais fácil gerenciar seus Aplicativos Lógicos quando eles são mais discretos. Você pode fazer isso fatorando a lógica em várias definições e chamando-as na mesma definição pai. Neste exemplo, haverá um Aplicativo lógico pai que recebe pedidos e um aplicativo lógico filho que executa algumas etapas para cada pedido.

No aplicativo lógico pai:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "orders": {
            "defaultValue": [
                {
                    "quantity": 10,
                    "id": "myorder1"
                },
                {
                    "quantity": 200,
                    "id": "specialOrder"
                },
                {
                    "quantity": 5,
                    "id": "myOtherOrder"
                }
            ],
            "type": "Array"
        }
    },
    "triggers": {},
    "actions": {
        "iterateOverOrders": {
            "repeat": "@parameters('orders')",
            "type": "Workflow",
            "inputs": {
                "uri": "https://westus.logic.azure.com/subscriptions/xxxxxx-xxxxx-xxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Logic/workflows/xxxxxxx",
                "apiVersion": "2015-02-01-preview",
                "trigger": {
                    "name": "submitOrder",
                    "outputs": {
                        "body": "@repeatItem()"
                    }
                },
                "authentication": {
                    "type": "Basic",
                    "username": "default",
                    "password": "xxxxxxxxxxxxxx"
                }
            }
        },
        "sendInvoices": {
            "repeat": "@outputs('iterateOverOrders').repeatItems",
            "type": "Http",
            "inputs": {
                "uri": "http://www.example.com/?invoiceID=@{repeatOutputs().run.outputs.deliverTime.value}",
                "method": "GET"
            }
        }
    },
    "outputs": {}
}
```

Em seguida, no aplicativo lógico filho, você usará a função [`triggerBody()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#triggerBody) para obter os valores passados para o fluxo de trabalho filho. Você vai então popular as saídas com os dados que deseja retornar ao fluxo pai.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "triggers": {},
    "actions": {
        "calulatePrice": {
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?action=calcPrice&id=@{triggerBody().id}&qty=@{triggerBody().quantity}"
            }
        },
        "calculateDeliveryTime": {
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?action=calcTime&id=@{triggerBody().id}&qty=@{triggerBody().quantity}"
            }
        }
    },
    "outputs": {
        "deliverTime": {
            "type": "String",
            "value": "@outputs('calculateDeliveryTime').headers.etag"
        }
    }
}
```

Você pode ler sobre a [ação de tipo Aplicativo lógico no MSDN](https://msdn.microsoft.com/pt-BR/library/azure/dn948511.aspx).

>[AZURE.NOTE]O designer de Aplicativo lógico não oferece suporte a ações de tipo Aplicativo lógico, portanto, você precisará editar a definição manualmente.


## Uma etapa para tratar de falhas caso algo dê errado

Normalmente você deseja ser capaz de gravar uma *etapa de correção* – alguma lógica que é executada se, **e somente se**, uma ou mais de suas chamadas falhou. Neste exemplo estamos obtendo dados de uma variedade de locais, mas se a chamada falhar, desejo gravar uma mensagem por POST em algum lugar, para que possa rastrear essa falha posteriormente.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "dataFeeds": {
            "defaultValue": [
                "https://www.microsoft.com/pt-BR/default.aspx",
                "https://gibberish.gibberish/"
            ],
            "type": "Array"
        }
    },
    "triggers": {},
    "actions": {
        "readData": {
            "repeat": "@parameters('dataFeeds')",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@repeatItem()"
            }
        },
        "postToErrorMessageQueue": {
            "repeat": "@actions('readData').outputs.repeatItems",
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?noteAnErrorFor=@{repeatItem().inputs.uri}"
            },
            "conditions": [
                {
                    "expression": "@equals(actions('readData').status, 'Failed')"
                },
                {
                    "expression": "@equals(repeatItem().status, 'Failed')"
                }
            ]
        }
    },
    "outputs": {}
}
```

Estou usando duas condições, já que na primeira etapa estou repetindo ao longo de uma lista. Se você teve apenas uma única ação, você precisaria de apenas uma condição (a primeira). Observe também que você pode usar as *entradas* para a ação que falhou na etapa de correção - passo aqui a URL com falha para a segunda etapa.

![Correção](./media/app-service-logic-author-definitions/remediation.png)

Finalmente, já que agora você tratou o erro, nós não marcamos mais a execução como **Falha**. Como você pode ver, essa prática foi realizada com **Êxito** embora uma etapa tenha falhado, porque eu escrevi a etapa para tratar dessa falha.

## Duas (ou mais) etapas executadas em paralelo

Para ter a execução de várias ações em paralelo e não em sequência, você precisa remover a condição `dependsOn` que vincula essas duas ações. Depois que a dependência é removida as ações são executadas automaticamente em paralelo, a menos que cada uma delas precise de dados da outra.

![Ramificações](./media/app-service-logic-author-definitions/branches.png)

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "dataFeeds": {
            "defaultValue": [
                "https://www.microsoft.com/pt-BR/default.aspx",
                "https://office.live.com/start/default.aspx"
            ],
            "type": "Array"
        }
    },
    "triggers": {},
    "actions": {
        "readData": {
            "repeat": "@parameters('dataFeeds')",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@repeatItem()"
            }
        },
        "branch1": {
            "repeat": "@actions('readData').outputs.repeatItems",
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?branch1Logic=@{repeatItem().inputs.uri}"
            }
        },
        "branch2": {
            "repeat": "@actions('readData').outputs.repeatItems",
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?branch2Logic=@{repeatItem().inputs.uri}"
            }
        }
    },
    "outputs": {}
}
```

Como você pode ver no exemplo acima, ramificação1 e ramificação2 dependem somente do conteúdo de readData. Como resultado, ambas essas ramificações serão executadas em paralelo:

![Paralelo](./media/app-service-logic-author-definitions/parallel.png)

Você pode ver que o carimbo de data/hora para ambas as ramificações é idêntico.

## Unir duas ramificações condicionais de lógica

Você pode combinar dois fluxos condicionais de lógica (que podem ter ou não sido executados) tendo uma única ação que extrai dados de ambas as ramificações.

Sua estratégia para isso varia dependendo de você estar lidando com um item ou com uma coleção de itens. No caso de um único item, você desejará usar a função [`coalesce()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#coalesce):

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
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
    "triggers": {},
    "actions": {
        "handleNormalOrders": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?orderNormally=@{parameters('order').id}"
            },
            "conditions": [
                {
                    "expression": "@lessOrEquals(parameters('order').quantity, 100)"
                }
            ]
        },
        "handleSpecialOrders": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?orderSpecially=@{parameters('order').id}"
            },
            "conditions": [
                {
                    "expression": "@greater(parameters('order').quantity, 100)"
                }
            ]
        },
        "submitInvoice": {
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?invoice=@{coalesce(outputs('handleNormalOrders')?.headers?.etag,outputs('handleSpecialOrders')?.headers?.etag )}"
            },
            "conditions": [
                {
                    "expression": "@or(equals(actions('handleNormalOrders').status, 'Succeeded'), equals(actions('handleSpecialOrders').status, 'Succeeded'))"
                }
            ]
        }
    },
    "outputs": {}
}
```
 
Como alternativa, quando ambas as suas duas primeiras ramificações operam em uma lista de pedidos, por exemplo, você desejará usar a função [`union()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#union) para combinar os dados de ambas as ramificações.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "orders": {
            "defaultValue": [
                {
                    "quantity": 10,
                    "id": "myorder1"
                },
                {
                    "quantity": 200,
                    "id": "specialOrder"
                },
                {
                    "quantity": 5,
                    "id": "myOtherOrder"
                }
            ],
            "type": "Array"
        }
    },
    "triggers": {},
    "actions": {
        "handleNormalOrders": {
            "repeat": "@parameters('orders')",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?orderNormally=@{repeatItem().id}"
            },
            "conditions": [
                {
                    "expression": "@lessOrEquals(repeatItem().quantity, 100)"
                }
            ]
        },
        "handleSpecialOrders": {
            "repeat": "@parameters('orders')",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?orderSpecially=@{repeatItem().id}"
            },
            "conditions": [
                {
                    "expression": "@greater(repeatItem().quantity, 100)"
                }
            ]
        },
        "submitInvoice": {
            "repeat": "@union(actions('handleNormalOrders').outputs.repeatItems, actions('handleSpecialOrders').outputs.repeatItems)",
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?invoice=@{repeatOutputs().headers.etag}"
            },
            "conditions": [
                {
                    "expression": "@equals(repeatItem().status, 'Succeeded')"
                }
            ]
        }
    },
    "outputs": {}
}
```
## Trabalhando com Cadeias de Caracteres

Há diversas funções que podem ser usadas para manipular cadeias de caracteres. Vejamos um exemplo no qual temos uma cadeia de caracteres que queremos passar para um sistema, mas não estamos confiantes que a codificação de caracteres será tratada apropriadamente. Uma opção é para codificar essa cadeia de caracteres em formato base64. No entanto, para evitar o uso de caracteres de escape em uma URL, vamos substituir alguns caracteres.

Também queremos uma subcadeia de caracteres do nome do autor da ordem, porque os cinco primeiros caracteres não são usados.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
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
    "triggers": {},
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

1. Obter o [`length()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#length) do nome do autor da ordem, que retorna o número total de caracteres

2. Subtrair 5 (porque desejamos uma cadeia de caracteres mais curta)

3. Coletar efetivamente o [`substring()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#substring). Vamos começar no `5` do índice e seguir pelo restante da cadeia de caracteres.

4. Converter esta subcadeia de caracteres em uma cadeia de caracteres [`base64()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#base64)

5. [`replace()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#replace) todos os caracteres `+` com `-`

6. [`replace()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#replace) todos os caracteres `/` com `_`

## Trabalho com valores de Data/Hora

Valores de Data/Hora podem ser úteis, especialmente quando você estiver tentando extrair dados de uma fonte de dados na qual, naturalmente, não há suporte para **Gatilhos**. Você também pode usar valores de Data/Hora para calcular quanto tempo diversas etapas estão levando.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
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
    "triggers": {},
    "actions": {
        "order": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?id=@{parameters('order').id}"
            }
        },
        "timingWarning": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
        	},
            "conditions": [
                {
                    "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))"
                }
            ]
        }
    },
    "outputs": {}
}
```

Neste exemplo, estamos extraindo o `startTime` da etapa anterior. Em seguida obtemos a hora atual e subtraímos um segundo:[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/dn948512.aspx#addseconds) (você pode usar outras unidades de tempo, como `minutes` ou `hours`). Por fim, podemos comparar esses dois valores. Se o primeiro for menor do que o segundo, isso significa que mais de um segundo decorreu desde o primeiro momento em que a ordem foi emitida.

Observe também que podemos usar formatadores de cadeia de caracteres para formatar datas: na cadeia de caracteres de consulta, utilizo [`utcnow('r')`](https://msdn.microsoft.com/library/azure/dn948512.aspx#utcnow) para obter o RFC1123. Toda a formatação das datas [está documentada no MSDN](https://msdn.microsoft.com/library/azure/dn948512.aspx#utcnow).

## Passando valores em tempo de execução para variar o comportamento

Digamos que você tenha diferentes comportamentos que deseja executar com base em um valor que você usa para iniciar o Aplicativo lógico. Você pode usar a função [`triggerOutputs()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#triggerOutputs) para obter esses valores por meio do qual você passou:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "triggers": {},
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@triggerOutputs().uriToGet"
            }
        },
        "extraStep": {
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/extraStep"
            },
            "conditions": [
                {
                    "expression": "@triggerOutputs().doMoreLogic"
                }
            ]
        },  
    },
    "outputs": {}
}
```

Para fazer isso realmente funcionar, quando você inicia a execução, você precisa passar as propriedades que deseja (no exemplo acima, `uriToGet` e `doMoreLogic`). Eis aqui a chamada [para a qual você pode usar autenticação Básica](https://msdn.microsoft.com/library/azure/dn948513.aspx#basicAuth):

```
POST https://<<Logic app endpoint from the Essentials>>/run?api-version=2015-02-01-preview
Authorization: Basic <<Based 64 encoded username (default) : password (from the Settings blade)>>
Content-type: application/json
```

Com a carga a seguir. Observe que você forneceu o Aplicativo lógico com os valores a serem usados agora:

```
{
    "outputs": {
        "uriToGet" : "http://my.uri.I.want/",
        "doMoreLogic" : true
    }
}
``` 

Quando esse aplicativo lógico for executado, ele chamará o uri que passei e executará essa etapa adicional porque passei `true`. Se você deseja variar apenas os parâmetros no momento da implantação (não para *cada execução*), então você deve usar `parameters` conforme determinado abaixo.

## Uso de parâmetros de tempo de implantação para ambientes diferentes

É comum ter um ciclo de vida de implantação em que você tem um ambiente de desenvolvimento, um ambiente de preparo e um ambiente de produção. Em todos eles você pode, por exemplo, desejar a mesma definição, mas usar bancos de dados diferentes. Do mesmo modo, talvez você queira usar a mesma definição em muitas regiões diferentes para alta disponibilidade, mas deseje que cada instância de Aplicativo lógico se comunique com o banco de dados dessa região.

Observe que isso é diferente de pegar parâmetros diferentes em *tempo de execução*; para isso, você deve usar a função `trigger()` conforme indicado acima.

Você pode iniciar com uma definição muito simplista como essa:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "connection": {
            "type": "string"
        }
    },
    "triggers": {},
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('connection')"
            }
        }        
    },
    "outputs": {}
}
```

Então, na verdadeira solicitação `PUT` para o Aplicativo lógico, você pode fornecer o parâmetro `connection`. Observe que, como não existe mais um valor padrão, esse parâmetro é requerido na carga do Aplicativo lógico:

```
{
    "properties": {
        "sku": {
            "name": "Premium",
            "plan": {
                "id": "/subscriptions/xxxxx/resourceGroups/xxxxxx/providers/Microsoft.Web/serverFarms/xxxxxx"
            }
        },
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

Em cada ambiente, você pode fornecer então um valor diferente para o parâmetro `connection`.

## Executando uma etapa até que uma condição seja atendida

Talvez você tenha uma API que esteja chamando e deseje aguardar uma determinada resposta antes de continuar. Por exemplo, imagine que deseja aguardar que alguém carregue um arquivo em um diretório antes de processar o arquivo. Você pode fazer isso com *do-until*:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "triggers": {},
    "actions": {
        "http0": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://mydomain/listfiles"
            },
            "until": {
                "limit": {
                    "timeout": "PT10M"
                },
                "conditions": [
                    {
                        "expression": "@greater(length(action().outputs.body),0)"
                    }
                ]
            }
        }
    },
    "outputs": {}
}
```

Consulte a [documentação da API REST](https://msdn.microsoft.com/library/azure/dn948513.aspx) para todas as opções que você tem para criar e gerenciar Aplicativos lógicos.

<!---HONumber=Oct15_HO3-->