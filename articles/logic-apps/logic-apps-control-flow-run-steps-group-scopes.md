---
title: "Executar etapas com base no status da ação agrupada - Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Agrupar ações em escopos e executar as etapas com base no status do grupo"
services: logic-apps
keywords: branches, processamento paralelo
documentationcenter: 
author: ecfan
manager: anneta
editor: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 052af45962f442e96ca28f05ffaa1b9814b2588b
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2018
---
# <a name="scopes-run-steps-based-on-group-status-in-logic-apps"></a>Escopos: executar etapas com base no status do grupo em aplicativos lógicos

Para executar as etapas somente após um outro grupo de ações ter êxito ou falhar, coloque esse grupo dentro de um *escopo*. Essa estrutura é útil quando deseja organizar ações como um grupo lógico, avaliar o status desse grupo e executar ações baseadas no status do escopo. Depois que todas as ações em um escopo concluem a execução, o escopo também obtém seu próprio status. Por exemplo, é possível usar escopos quando você quiser implementar [tratamento de erro e exceção](../logic-apps/logic-apps-exception-handling.md#scopes). 

Para verificar o status de um escopo, é possível usar os mesmos critérios utilizados para determinar um status de execução de aplicativos lógicos, como "Com êxito", "Com falha", "Cancelado", e assim por diante. Por padrão, quando todas as ações do escopo são bem-sucedidas, o status do escopo é marcado como "Com êxito." Mas, quando qualquer ação no escopo apresenta falha ou é cancelada, o status do escopo é marcado como "Com falha." Para limites nos escopos, consulte [Limites e configurações](../logic-apps/logic-apps-limits-and-config.md). 

Por exemplo, aqui é um aplicativo lógico de alto nível que usa um escopo para executar ações específicas e uma condição para verificar o status do escopo. Se alguma ação no escopo falhar ou encerrar inesperadamente, o escopo é marcado como "Com falha" ou "Anulado", respectivamente, e o aplicativo lógico envia uma mensagem "Falha de escopo". Se todas as ações de escopo tiverem êxito, o aplicativo lógico envia uma mensagem "Escopo com êxito".

![Configurar o gatilho "Agenda - Recorrência"](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>pré-requisitos

Para seguir o exemplo neste artigo, é necessário estes itens:

* Uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/). 

* Uma conta de email de qualquer provedor de email com suporte de Aplicativos Lógicos. Este exemplo usa Outlook.com. Se você utiliza um provedor diferente, o fluxo geral permanece o mesmo, mas sua interface do usuário aparece diferente.

* Uma chave do Bing Mapas. Para obter essa chave, consulte <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">Obter uma chave do Bing Mapas</a>.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-sample-logic-app"></a>Criar aplicativo lógico de exemplo

Primeiro, crie esse aplicativo lógico de exemplo para que seja possível adicionar um escopo posteriormente:

![Criar aplicativo lógico de exemplo](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* Um gatilho **Agenda - Recorrência** que verifica o serviço Bing Mapas em um intervalo especificado
* Uma ação do **Bing Mapas - Obter rota** que verifica o tempo de viagem entre dois locais
* Uma instrução condicional que verifica se o tempo de viagem excede o tempo de viagem especificado
* Uma ação que envia o email de que o tempo de viagem atual excede o tempo especificado

É possível salvar o aplicativo lógico a qualquer momento, portanto, salve seu trabalho frequentemente.

1. Entre no <a href="https://portal.azure.com" target="_blank">Portal do Azure</a>, se você ainda não fez isso. Criar um aplicativo lógico em branco.

2. Adicione o gatilho **Agenda - Recorrência** com estas configurações: **Intervalo** = "1" e **Frequência** = "Minuto"

   ![Configurar o gatilho "Agenda - Recorrência"](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > Para simplificar visualmente a exibição e ocultar os detalhes de cada ação no designer, recolha a forma de cada ação na medida em que você progride através dessas etapas.

3. Adicione a ação **Bing Mapas - Obter rota**. 

   1. Se você ainda não tiver uma conexão do Bing Mapas, a criação será solicitada.

      | Configuração | Valor | DESCRIÇÃO |
      | ------- | ----- | ----------- |
      | **Nome da Conexão** | BingMapsConnection | Forneça um nome para a conexão. | 
      | **Chave de API** | <*your-Bing-Maps-key*> | Insira a chave do Bing Mapas que você recebeu anteriormente. | 
      ||||  

   2. Configure a ação **Obter rota**, conforme mostrado na tabela abaixo da imagem:

      ![Configurar a ação "Bing Mapas - Obter rota"](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      Para saber mais sobre esses parâmetros, consulte [Calcular uma rota](https://msdn.microsoft.com/library/ff701717.aspx).

      | Configuração | Valor | DESCRIÇÃO |
      | ------- | ----- | ----------- |
      | **Localizador 1** | <*iniciar*> | Insira a origem da rota. | 
      | **Localizador 2** | <*finalizar*> | Insira o destino da rota. | 
      | **Evitar** | Nenhum | Insira os itens a serem evitados na rota, como rodovias, pedágios, e assim por diante. Para possíveis valores, consulte [Calcular uma rota](https://msdn.microsoft.com/library/ff701717.aspx). | 
      | **Otimizar** | timeWithTraffic | Selecione um parâmetro para otimizar a rota, como distância, tempo com informações de trânsito atuais, e assim por diante. Esse exemplo usa este valor: "timeWithTraffic" | 
      | **Unidades de distância** | <*your-preference*> | Insira a unidade de distância para calcular a rota. Esse exemplo usa este valor: "Milha" | 
      | **Modo de navegação** | Automóvel | Insira o modo de viagem para a rota. Esse exemplo usa este valor "Automóvel" | 
      | **Data /Hora de trânsito** | Nenhum | Aplica-se apenas ao modo de trânsito. | 
      | **Tipo de data de trânsito** | Nenhum | Aplica-se apenas ao modo de trânsito. | 
      ||||  

4. Adicione uma condição para verificar se o tempo de viagem atual com trânsito excede um tempo especificado. Para esse exemplo, siga as etapas abaixo desta imagem:

   ![Condição da compilação](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. Renomeie a condição com esta descrição: **Se o tempo de trânsito for maior que o tempo especificado**

   2. Na lista de parâmetros, selecione o campo **Tráfego de Duração da Viagem**, que está em segundos. 

   3. Para o operador de comparação, selecione este operador: **é maior que**

   4. Para o valor de comparação, insira **600**, que é em segundos e equivalente a 10 minutos.

5. No branch **Se verdadeiro** da condição, adicione uma ação de "enviar email" para seu provedor de email. Configure essa ação com os detalhes, conforme mostrado na tabela abaixo desta imagem:

   ![Adicionar ação "Enviar um email" para branch "Se verdadeiro"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. Para o campo **Para**, insira o endereço de email para finalidade de teste.

   2. Para o campo **Assunto**, insira este texto:

      ```Time to leave: Traffic more than 10 minutes```

   3. Para o campo **Corpo**, insira este texto com um espaço à direita: 

      ```Travel time: ```

      Enquanto o cursor aparecer no campo **Corpo**, a lista de conteúdo dinâmico permanecerá aberta para que você possa selecionar os parâmetros disponíveis neste ponto.

   4. Na lista Conteúdo dinâmico, escolha **Expressão**.

   5. Localizar e selecionar a função **div( )**.

   6. Enquanto o cursor estiver dentro dos parênteses da função, escolha **Conteúdo dinâmico** para que você possa adicionar o próximo parâmetro **Tráfego de Duração do Tráfego**.

   7. Em **Obter rota** na lista de parâmetro dinâmico, selecione o campo **Tráfego de Duração do Tráfego**.

      ![Selecionar "Tráfego de Duração do Tráfego"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   8. Depois que o campo resolver o formato JSON, adicione uma **vírgula** (```,```) seguida do número ```60``` para converter o valor no **Tráfego de Duração do Tráfego** de segundos para minutos. 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      A expressão agora é semelhante a este exemplo:

      ![Concluir expressão](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   9. Certifique-se de escolher **OK** quando terminar.

  10. Depois que a expressão for resolvida, adicione este texto com um espaço à esquerda: ``` minutes```
  
      O campo **Corpo** agora é semelhante a este exemplo:

      ![Campo "Corpo" concluído](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)

6. Salve seu aplicativo lógico.

Em seguida, adicione um escopo para que seja possível agrupar ações específicas e avaliar os respectivos status.

## <a name="add-a-scope"></a>Adicionar um escopo

1. Abra o aplicativo lógico no Designer do Aplicativo Lógico, caso ainda não tenha feito isso. 

2. Adicione um escopo na localização do fluxo de trabalho desejado. Por exemplo: 

   * Para adicionar um escopo entre as etapas existentes no fluxo de trabalho do aplicativo lógico, mova o ponteiro sobre a seta onde deseja adicionar o escopo. 
   Escolha o **sinal de adição** (**+**) > **Adicionar um escopo**.

     ![Adicionar um escopo](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

     Quando você quiser adicionar um escopo no final do fluxo de trabalho, na parte inferior do aplicativo lógico, escolha **+ Nova etapa** > **...Mais** > **Adicionar um escopo**.

3. Agora, adicione as etapas ou arraste as etapas existentes que deseja executar dentro do escopo. Para esse exemplo, arraste estas ações para o escopo:
      
   * **Obter rota**
   * **Se o tempo de tráfego for maior que o tempo especificado**, que inclui ambos branches **verdadeiro** e **falso**

   O aplicativo lógico agora é semelhante a este exemplo:

   ![Escopo adicionado](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

4. No escopo, adicione uma condição que verifica o status do escopo. Renomeie a condição com esta descrição: **Se falha de escopo**

   ![Adicionar condição para verificar o status do escopo](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
5. Compile essa expressão que verifica se o status do escopo é igual a `Failed` ou `Aborted`.

   ![Adicionar expressão que verifica o status do escopo](./media/logic-apps-control-flow-run-steps-group-scopes/build-expression-check-scope-status.png)

   Ou, para inserir essa expressão como texto, escolha **Editar em modo avançado**.

   ```@equals('@result(''Scope'')[0][''status'']', 'Failed, Aborted')```

6. Nos branches **Se verdadeiro** e **Se falso**, adicione as ações que você quer executar, por exemplo, enviar email ou uma mensagem.

   ![Adicionar expressão que verifica o status do escopo](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

7. Salve seu aplicativo lógico.

O aplicativo lógico concluído agora é semelhante a este exemplo com todas as formas expandidas:

![Aplicativo lógico concluído com escopo](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>Teste o trabalho

Na barra de ferramentas do Designer, escolha **Executar**. Se todas as ações com escopo tiverem êxito, você receberá uma mensagem "Escopo com êxito". Se alguma ação de escopo não tiver êxito, você receberá uma mensagem "Escopo com falha". 

<a name="scopes-json"></a>

## <a name="json-definition"></a>Definição JSON

Se você estiver trabalhando na exibição de código, será possível definir uma estrutura de escopo na definição JSON do aplicativo lógico. Por exemplo, aqui é a definição JSON para gatilho e ações no aplicativo lógico anterior:

``` json
"triggers": {
  "Recurrence": {
    "type": "Recurrence",
    "recurrence": {
       "frequency": "Minute",
       "interval": 1
    },
  }
}
```

```json
"actions": {
  "If_scope_failed": {
    "type": "If",
    "actions": {
      "Scope_failed": {
        "type": "ApiConnection",
        "inputs": {
          "body": {
            "Body": "Scope failed",
            "Subject": "Scope failed",
            "To": "<your-email@domain.com>"
          },
          "host": {
            "connection": {
              "name": "@parameters('$connections')['outlook']['connectionId']"
            }
          },
          "method": "post",
          "path": "/Mail"
        },
        "runAfter": {}
      }
    },
    "else": {
      "actions": {
        "Scope_succeded": {
          "type": "ApiConnection",
          "inputs": {
            "body": {
              "Body": "None",
              "Subject": "Scope succeeded",
              "To": "<your-email@domain.com>"
            },
            "host": {
              "connection": {
               "name": "@parameters('$connections')['outlook']['connectionId']"
              }
            },
            "method": "post",
            "path": "/Mail"
          },
          "runAfter": {}
        }
      }
    },
    "expression": "@equals('@result(''Scope'')[0][''status'']', 'Failed, Aborted')",
    "runAfter": {
      "Scope": [
        "Succeeded"
      ]
    }
  },
  "Scope": {
    "type": "Scope",
    "actions": {
      "Get_route": {
        "type": "ApiConnection",
        "inputs": {
          "host": {
            "connection": {
              "name": "@parameters('$connections')['bingmaps']['connectionId']"
            }
          },
          "method": "get",
          "path": "/REST/V1/Routes/Driving",
          "queries": {
            "distanceUnit": "Mile",
            "optimize": "timeWithTraffic",
            "travelMode": "Driving",
            "wp.0": "<start>",
            "wp.1": "<end>"
          }
        },
        "runAfter": {}
      },
      "If_traffic_time_more_than_specified_time": {
        "type": "If",
        "actions": {
          "Send_mail_when_traffic_exceeds_10_minutes": {
            "type": "ApiConnection",
            "inputs": {
              "body": {
                 "Body": "Travel time:@{div(body('Get_route')?['travelDurationTraffic'], 60)} minutes",
                 "Subject": "Time to leave: Traffic more than 10 minutes",
                 "To": "<your-email@domain.com>"
              },
              "host": {
                "connection": {
                   "name": "@parameters('$connections')['outlook']['connectionId']"
                }
              },
              "method": "post",
              "path": "/Mail"
            },
            "runAfter": {}
          }
        },
        "expression": "@greater(body('Get_route')?['travelDurationTraffic'], 600)",
        "runAfter": {
          "Get_route": [
            "Succeeded"
          ]
        }
      }
    },
    "runAfter": {}
  }
}
```

## <a name="get-support"></a>Obtenha suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em recursos e sugestões, visite o [site de comentários do usuário de Aplicativos Lógicos do Azure](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* [Executar etapas baseadas em uma condição (instruções condicionais)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Executar etapas baseadas em valores diferentes (instruções de comutador)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Executar e repetir (loops)](../logic-apps/logic-apps-control-flow-loops.md)
* [Executar ou mesclar etapas paralelas (branches)](../logic-apps/logic-apps-control-flow-branches.md)