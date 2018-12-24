---
title: Criar, editar ou estender o JSON para definições de aplicativo lógico - Aplicativos Lógicos do Azure | Microsoft Docs
description: Criar e estender o JSON para definições de aplicativo lógico nos Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.topic: article
ms.date: 01/01/2018
ms.openlocfilehash: 1f2e136810194ad044255f9d129b5c03549221b9
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128653"
---
# <a name="create-edit-or-extend-json-for-logic-app-definitions-in-azure-logic-apps"></a>Criar, editar ou estender o JSON para definições de aplicativo lógico nos Aplicativos Lógicos do Azure

Quando você cria soluções de integração corporativa com fluxos de trabalho automatizados em [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md), as definições de aplicativo lógico subjacentes usam JSON (JavaScript Object Notation) simples e declarativo, junto com o [ Esquema de Linguagem de Definição do Fluxo de Trabalho (WDL)](../logic-apps/logic-apps-workflow-definition-language.md) para sua descrição e validação. Esses formatos tornam as definições do aplicativo lógico mais fáceis de ler e entender sem saber muito sobre o código. Quando você quiser automatizar a criação e implantação de aplicativos lógicos, você pode incluir definições de aplicativo lógica como [recursos do Azure](../azure-resource-manager/resource-group-overview.md) dentro de [modelos do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment). Para criar, gerenciar e implantar aplicativos lógicos, você pode usar [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.logicapp), [CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md), ou [APIs REST de Aplicativo Lógico do Azure](https://docs.microsoft.com/rest/api/logic/).

Para trabalhar com definições de aplicativos lógicos em JSON, abra o editor do modo de exibição de código ao trabalhar no portal do Azure ou no Visual Studio ou copie a definição em qualquer editor que você deseja. Se ainda não estiver familiarizado com aplicativos lógicos, examine [Como criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

> [!NOTE]
> Alguns recursos de aplicativos lógicos do Azure, como parâmetros de definição e vários gatilhos em definições de aplicativo lógico, estão disponíveis somente em JSON, não no Designer de Aplicativos Lógicos. Portanto, para essas tarefas, você deve trabalhar no modo de exibição de código ou em outro editor.

## <a name="edit-json---azure-portal"></a>Editar o JSON - portal do Azure

1. Entre no <a href="https://portal.azure.com" target="_blank">Portal do Azure</a>.

2. No menu à esquerda, escolha **Todos os serviços**. Na caixa de pesquisa, localize "aplicativos lógicos" e, em seguida, nos resultados, selecione seu aplicativo lógico.

3. No menu do aplicativo lógico, em **Ferramentas de desenvolvimento**, selecione **modo de exibição de código do aplicativo lógico**.

   A janela do editor do modo de exibição de código é aberta e mostra a definição do aplicativo lógico no formato JSON.

## <a name="edit-json---visual-studio"></a>Editar JSON - Visual Studio

Antes de trabalhar em sua definição do aplicativo lógico no Visual Studio, certifique-se de que você já [instalou as ferramentas necessárias](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites). Para criar um aplicativo lógico com o Visual Studio, examine [Início Rápido: automatizar tarefas e processos com os Aplicativos Lógicos do Azure - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

No Visual Studio, é possível abrir aplicativos lógicos que foram criados e implantados diretamente do portal do Azure ou como projetos do Azure Resource Manager do Visual Studio.

1. Abra a solução do Visual Studio, ou o projeto [Grupo de Recursos do Azure](../azure-resource-manager/resource-group-overview.md), que contém o aplicativo lógico.

2. Localize e abra a definição do aplicativo lógico, que, por padrão, aparece em um [modelo do Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment), denominado **LogicApp.json**. Você pode usar e personalizar este modelo de implantação para ambientes diferentes.

3. Abra o menu de atalho para o modelo e a definição do aplicativo lógico. Selecione **Abrir com o Designer de Aplicativo Lógico**.

   ![Abra o aplicativo lógico em uma solução do Visual Studio](./media/logic-apps-author-definitions/open-logic-app-designer.png)

4. Na parte inferior do designer, escolha **modo de exibição de código**. 

   A janela do editor do modo de exibição de código é aberta e mostra a definição do aplicativo lógico no formato JSON.

5. Para retornar ao modo de exibição designer, na parte inferior do editor de modo de exibição de código, escolha **Design**.

## <a name="parameters"></a>parâmetros

Os parâmetros permitem que você reutilize valores em todo o aplicativo lógico e são bons para substituir os valores que possam sofrer alteração com frequência. Por exemplo, se você tiver um endereço de email que deseje usar em vários locais, deverá defini-lo como um parâmetro. 

Os parâmetros também são úteis quando você precisa substituir parâmetros em ambientes diferentes, saiba mais sobre [parâmetros para implantação](#deployment-parameters) e [API REST para obter a documentação de Aplicativos Lógicos do Azure](https://docs.microsoft.com/rest/api/logic).

> [!NOTE]
> Parâmetros só estão disponíveis no modo de exibição de código.

No [primeiro exemplo de aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md), você criou um fluxo de trabalho que envia emails quando novas postagens aparecem em um feed RSS de um site. A URL do feed é codificada, portanto, esse exemplo mostra como substituir o valor da consulta por um parâmetro para que você possa alterar a URL do feed mais facilmente.

1. Na exibição de código, localize o objeto `parameters : {}` e adicione um objeto `currentFeedUrl`:

   ``` json
     "currentFeedUrl" : {
      "type" : "string",
            "defaultValue" : "http://rss.cnn.com/rss/cnn_topstories.rss"
   }
   ```

2. Na ação `When_a_feed-item_is_published`, localize a seção `queries` e substitua o valor da consulta por `"feedUrl": "#@{parameters('currentFeedUrl')}"`. 

   **Antes**
   ``` json
   }
      "queries": {
          "feedUrl": "https://s.ch9.ms/Feeds/RSS"
       }
   },   
   ```

   **Depois**
   ``` json
   }
      "queries": {
          "feedUrl": "#@{parameters('currentFeedUrl')}"
       }
   },   
   ```

   Para unir duas ou mais cadeias de caracteres, você também pode usar a função `concat`. 
   Por exemplo, `"@concat('#',parameters('currentFeedUrl'))"` funciona como no exemplo anterior.

3.  Quando terminar, escolha **Salvar**. 

Agora você pode alterar o feed RSS do site passando uma URL diferente pelo objeto `currentFeedURL`.

<a name="deployment-parameters"></a>

## <a name="deployment-parameters-for-different-environments"></a>Parâmetros de implantação para ambientes diferentes

Geralmente, os ciclos de vida de implantação têm ambientes de desenvolvimento, preparo e produção. Por exemplo, você pode usar a mesma definição de aplicativo lógico em todos esses ambientes, mas usar bancos de dados diferentes. Do mesmo modo, talvez você queira usar a mesma definição em regiões diferentes para alta disponibilidade, mas deseje que cada instância de Aplicativo lógico use o banco de dados dessa região. 

> [!NOTE] 
> Esse cenário é diferente de obter parâmetros em *tempo de execução* em que, em vez disso, você deve usar a função `trigger()`.

Aqui está uma definição básica:

``` json
{
    "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "uri": {
            "type": "string"
        }
    },
    "triggers": {
        "request": {
          "type": "request",
          "kind": "http"
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
Na verdadeira solicitação `PUT` para Aplicativos Lógicos, você pode fornecer o parâmetro `uri`. Em cada ambiente, você pode fornecer um valor diferente para o parâmetro `connection` . Como não existe mais um valor padrão, a carga do aplicativo lógico requer este parâmetro:

``` json
{
    "properties": {},
        "definition": {
          /// Use the definition from above here
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

Para obter mais informações, consulte a [API REST para obter documentação de Aplicativos Lógicos do Azure](https://docs.microsoft.com/rest/api/logic/).

## <a name="process-strings-with-functions"></a>Cadeias de caracteres de processo com funções

Os Aplicativos Lógicos têm várias funções para trabalhar com cadeias de caracteres. Por exemplo, suponha que você deseje passar o nome de uma empresa de um pedido para outro sistema. No entanto, você não tem certeza sobre como tratar adequadamente a codificação de caracteres. Você pode executar a codificação base64 nessa cadeia de caracteres, mas para evitar escapes na URL, você pode substituir alguns caracteres no lugar. Além disso, você só precisa de uma subcadeia de caracteres do nome da empresa, porque os cinco primeiros caracteres não são usados. 

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "companyName": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "Request",
      "kind": "Http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

Estas etapas descrevem como esse exemplo processa essa cadeia de caracteres, trabalhando de dentro para fora:

``` 
"uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. Obtenha o [`length()`](../logic-apps/logic-apps-workflow-definition-language.md) para o nome da empresa, para obter o número total de caracteres.

2. Para obter uma cadeia de caracteres mais curta, subtraia `5`.

3. Agora, você obtem [`substring()`](../logic-apps/logic-apps-workflow-definition-language.md). Comece no `5` do índice e siga pelo restante da cadeia de caracteres.

4. Converter esta subcadeia de caracteres em uma cadeia de caracteres [`base64()`](../logic-apps/logic-apps-workflow-definition-language.md).

5. Agora, [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) todos os `+` caracteres com `-` caracteres.

6. Finalmente, [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) todos os `/` caracteres com `_` caracteres.

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>Mapeie itens de lista a valores de propriedade e, em seguida, use mapas como parâmetros

Para obter resultados diferentes com base em um valor de propriedade, você pode criar um mapa que corresponda cada valor de propriedade a um resultado e, então, usar esse mapa como parâmetro. 

Por exemplo, esse fluxo de trabalho define algumas categorias como parâmetros e um mapa que corresponde essas categorias a uma URL específica. Primeiro, o fluxo de trabalho obtém uma lista de artigos. Em seguida, o fluxo de trabalho usa o mapa para localizar a URL correspondente à categoria de cada artigo.

*   A função [`intersection()`](../logic-apps/logic-apps-workflow-definition-language.md) verifica se a categoria corresponde a uma categoria definida conhecida.

*   Depois de obter uma categoria correspondente, o exemplo obtém o item do mapa usando colchetes: `parameters[...]`

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
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
        "microsoft": "https://www.microsoft.com/en-us/default.aspx",
        "google": "https://www.google.com",
        "robots": "https://en.wikipedia.org/wiki/Robot",
        "NSA": "https://www.nsa.gov/"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "http"
    }
  },
  "actions": {
    "getArticles": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
      }
    },
    "forEachArticle": {
      "type": "foreach",
      "foreach": "@body('getArticles').responseData.feed.entries",
      "actions": {
        "ifGreater": {
          "type": "if",
          "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)",
          "actions": {
            "getSpecialPage": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
              }
            }
          }
        }
      },
      "runAfter": {
        "getArticles": [
          "Succeeded"
        ]
      }
    }
  }
}
```

## <a name="get-data-with-date-functions"></a>Obter dados com funções de Data

Para obter dados de uma fonte de dados que não ofereça suporte a *gatilhos*, você pode, em vez disso, usar funções de Data para trabalhar com tempos de data e datas. Por exemplo, essa expressão descobre quanto tempo estão demorando as etapas do fluxo de trabalho, trabalhando de dentro para fora:

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. Na ação `order`, extraia `startTime`. 
2. Obtenha o tempo atual com `utcNow()`.
3. Subtraia um segundo:

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md) 

   Você pode usar outras unidades de tempo, como `minutes` ou `hours`. 

3. Agora, você pode comparar esses dois valores. 

   Se o primeiro valor for menor que o segundo valor, mais de um segundo terá decorrido desde o primeiro pedido.

Para formatar datas, você pode usar formatadores de cadeia de caracteres. Por exemplo, para obter RFC1123, use [`utcnow('r')`](../logic-apps/logic-apps-workflow-definition-language.md). Saiba mais sobre [formatação de data](../logic-apps/logic-apps-workflow-definition-language.md).

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder-id"
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


## <a name="next-steps"></a>Próximas etapas

* [Executar etapas baseadas em uma condição (instruções condicionais)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Executar etapas baseadas em valores diferentes (instruções de comutador)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Executar e repetir (loops)](../logic-apps/logic-apps-control-flow-loops.md)
* [Executar ou mesclar etapas paralelas (branches)](../logic-apps/logic-apps-control-flow-branches.md)
* [Executar etapas baseadas no status da ação agrupada (escopos)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* Saiba mais sobre o [Esquema de linguagem de definição de fluxo de trabalho para os Aplicativos Lógicos do Azure](../logic-apps/logic-apps-workflow-definition-language.md)
* Saiba mais sobre [ações do fluxo de trabalho e gatilhos para os Aplicativos Lógicos do Azure](../logic-apps/logic-apps-workflow-actions-triggers.md)