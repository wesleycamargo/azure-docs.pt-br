---
title: Adicionar loops que repetem ações ou processam matrizes – Aplicativo Lógico do Azure | Microsoft Docs
description: Como criar loops que repetem ações de fluxo de trabalho ou processam matrizes no Aplicativo Lógico do Azure
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.date: 03/05/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 5ba5e5abef4ebdc58c44cbe7f5ba584efe8abfc7
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233099"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>Criar loops que repetem ações de fluxo de trabalho ou processam matrizes no Aplicativo Lógico do Azure

Para iterar sobre matrizes no aplicativo lógico, é possível utilizar um [loop "Foreach"](#foreach-loop) ou um [loop "Foreach" sequencial](#sequential-foreach-loop). As iterações em um loop "Foreach" padrão são executadas em paralelo, enquanto as iterações em um loop "Foreach" sequencial são executadas um de cada vez. Para o número máximo de itens de matriz que loops "Foreach" podem processar em uma única execução do aplicativo lógico, consulte [Limites e configurações](../logic-apps/logic-apps-limits-and-config.md). 

> [!TIP] 
> Se você tiver um gatilho que recebe uma matriz e deseja executar um fluxo de trabalho para cada item de matriz, é possível fazer *debatch* dessa matriz com o [**SplitOn** da propriedade de gatilho](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). 
  
Para repetir ações até que uma condição seja atendida ou algum estado alterado, utilize um [loop "Until"](#until-loop). O aplicativo lógico primeiro executa todas as ações dentro do loop e, em seguida, verifica a condição como a última etapa. Se a condição é atendida, o loop para. Caso contrário, o loop repete. Para o número máximo de loops "Until" em uma única execução do aplicativo lógico, consulte [Limites e configurações](../logic-apps/logic-apps-limits-and-config.md). 

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/). 

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>Loop "Foreach"

Para repetir ações para cada item em uma matriz, utilize um loop "Foreach" no fluxo de trabalho do aplicativo lógico. É possível incluir várias ações em um loop "Foreach" e aninhar loops "Foreach" uns nos outros. Por padrão, os ciclos em um loop "Foreach" padrão são executados em paralelo. Para o número máximo de ciclos paralelos que loops "Foreach" podem executar, consulte [Limites e Configurações](../logic-apps/logic-apps-limits-and-config.md).

> [!NOTE] 
> Um loop "Foreach" funciona apenas com uma matriz e as ações no loop usam a `@item()` referência para processar cada item na matriz. Se você especificar dados que não estejam em uma matriz, o fluxo de trabalho do aplicativo lógico falhará. 

Por exemplo, esse aplicativo lógico envia-lhe um resumo diário do feed RSS de um site. O aplicativo usa um loop "Foreach" que envia um email para cada novo item localizado.

1. [Crie esse aplicativo lógico de exemplo](../logic-apps/quickstart-create-first-logic-app-workflow.md) com uma conta do Outlook.com ou Outlook para Office 365.

2. Entre o gatilho RSS e enviar ação de email, adicione um loop "Foreach". 

   Para adicionar um loop entre as etapas, mova o ponteiro sobre a seta onde deseja adicionar o loop. 
   Escolha o **sinal de adição** (**+**) que aparece e, em seguida, escolha **Adicionar um foreach**.

   ![Adicione um loop "Foreach" entre as etapas](media/logic-apps-control-flow-loops/add-for-each-loop.png)

3. Agora, compile o loop. Em **Selecionar uma saída das etapas anteriores** após a lista **Adicionar conteúdo dinâmico** aparecer, selecione a matriz de **Links de feed**, que é emitida pelo gatilho RSS. 

   ![Selecionar a partir da lista de conteúdo dinâmico](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > É possível selecionar *apenas* saídas de matriz da etapa anterior.

   A matriz selecionada agora aparece aqui:

   ![Selecionar matriz](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Para executar uma ação em cada item de matriz, arraste a ação **Enviar um email** para o loop **Foreach**. 

   O aplicativo lógico pode ser semelhante a este exemplo:

   ![Adicionar etapas para loop "Foreach"](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. Salve seu aplicativo lógico. Para testar manualmente o aplicativo lógico, na barra de ferramentas do Designer, escolha **Executar**.

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>Definição de loop "Foreach" (JSON)

Se você estiver trabalhando na exibição de código para o aplicativo lógico, será possível definir o loop `Foreach` na definição JSON do aplicativo lógico, por exemplo:

``` json
"actions": {
    "myForEachLoopName": {
        "type": "Foreach",
        "actions": {
            "Send_an_email": {
                "type": "ApiConnection",
                "inputs": {
                    "body": {
                        "Body": "@{item()}",
                        "Subject": "New CNN post @{triggerBody()?['publishDate']}",
                        "To": "me@contoso.com"
                    },
                    "host": {
                        "api": {
                            "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/office365"
                        },
                        "connection": {
                            "name": "@parameters('$connections')['office365']['connectionId']"
                        }
                    },
                    "method": "post",
                    "path": "/Mail"
                },
                "runAfter": {}
            }
        },
        "foreach": "@triggerBody()?['links']",
        "runAfter": {},
    }
},
```

<a name="sequential-foreach-loop"></a>

## <a name="foreach-loop-sequential"></a>Loop "Foreach": sequencial

Por padrão, cada ciclo em um loop "Foreach" executa em paralelo para cada item de matriz. Para executar cada ciclo sequencialmente, defina a opção **Sequencial** no loop "Foreach".

1. No canto superior direito do loop, escolha **reticências** (**...**) > **Configurações**.

   ![No loop "Foreach" loop, escolha "..." > "Configurações"](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

2. Ative a configuração **Sequencial** e, em seguida, escolha **Concluído**.

   ![Ativar a configuração Sequencial do loop "Foreach"](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

Também é possível definir o parâmetro **operationOptions** para `Sequential` na definição JSON do aplicativo lógico. Por exemplo: 

``` json
"actions": {
    "myForEachLoopName": {
        "type": "Foreach",
        "actions": {
            "Send_an_email": {               
            }
        },
        "foreach": "@triggerBody()?['links']",
        "runAfter": {},
        "operationOptions": "Sequential"
    }
},
```

<a name="until-loop"></a>

## <a name="until-loop"></a>Loop "Until"
  
Para repetir ações até que uma condição seja atendida ou algum estado alterado, utilize um loop "Until" no fluxo de trabalho do aplicativo lógico. Aqui estão alguns casos de uso comum em que é possível utilizar um loop "Until":

* Chamar um ponto de extremidade até obter a resposta desejada.
* Criar um registro em um banco de dados, aguardar até que um campo específico desse registro seja aprovado e continue processando. 

Por exemplo, todos os dias às 8h00 esse aplicativo lógico incrementará uma variável até que o valor da variável seja igual a 10. Em seguida, o aplicativo lógico envia um email que confirmará o valor atual. Embora esse exemplo utilize Outlook para Office 365, é possível utilizar qualquer provedor de email com suporte para Aplicativos Lógicos ([revise os conectores listados aqui](https://docs.microsoft.com/connectors/)). Se você usar outra conta de email, as etapas gerais são as mesmos, mas a interface do usuário pode ser ligeiramente diferente. 

1. Criar um aplicativo lógico em branco. No Designer do Aplicativo Lógico, pesquise "recorrência" e selecione esse gatilho: **Agenda - Recorrência** 

   ![Adicionar gatilho de "Agenda - Recorrência"](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

2. Especifique quando o gatilho será disparado, configurando o intervalo, a frequência e a hora do dia. Para definir a hora, escolha **Mostrar opções avançadas**.

   ![Adicionar gatilho de "Agenda - Recorrência"](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Propriedade | Valor |
   | -------- | ----- |
   | **Intervalo** | 1 | 
   | **Frequência** | Dia |
   | **A estas horas** | 8 |
   ||| 

3. No gatilho, escolha **Nova etapa** > **Adicionar uma ação**. Pesquise "variáveis" e, em seguida, selecione esta ação: **Variáveis - Inicializar variável**

   ![Adicionar ação "Variáveis - Inicializar variável"](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

4. Configure a variável com estes valores:

   ![Definir propriedades variáveis](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Propriedade | Valor | DESCRIÇÃO |
   | -------- | ----- | ----------- |
   | **Nome** | Limite | Nome da variável | 
   | **Tipo** | Número inteiro | Tipo de dados da variável | 
   | **Valor** | 0 | Valor inicial de variável | 
   |||| 

5. Na ação **Inicializar variável**, escolha **Nova etapa** > **Mais**. Selecione este loop: **Adicionar um Do Until**

   ![Adicionar loop "Do Until"](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

6. Compile a condição de saída do loop, selecionando a variável **Limite** e o operador **é igual**. Insira **10** como o valor da comparação.

   ![Compile a condição de saída para parar o loop](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

7. Dentro do loop, escolha **Adicionar uma ação**. Pesquise "variáveis" e, em seguida, adicione esta ação: **Variáveis - Incrementar variável**

   ![Adicionar ação para incrementar a variável](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

8. Para **Nome**, selecione a variável **Limite**. Para **Valor**, insira "1". 

   ![Incrementar "Limite" por 1](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

9. Em, mas fora do loop, adicione uma ação que envia emails. Se solicitado, entre em sua conta de email.

   ![Adicionar ação que envia email](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

10. Defina as propriedades do email. Adicione a variável **Limite** para o assunto. Dessa forma, é possível confirmar se o valor atual da variável atende à condição especificada, por exemplo:

    ![Configurar propriedades de email](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

    | Propriedade | Valor | DESCRIÇÃO |
    | -------- | ----- | ----------- | 
    | **Para** | *<email-address@domain>* | O endereço de email do destinatário. Para testes, use seu próprio endereço de email. | 
    | **Assunto** | O valor atual para "Limit" é **Limite** | Especifique o assunto do email. Para esse exemplo, certifique-se de incluir a variável **Limite**. | 
    | **Corpo** | <*email-conteúdo*> | Especifique o conteúdo da mensagem de email que deseja enviar. Para esse exemplo, insira o texto de sua escolha. | 
    |||| 

11. Salve seu aplicativo lógico. Para testar manualmente o aplicativo lógico, na barra de ferramentas do Designer, escolha **Executar**.

    Após a lógica iniciar a execução, você receberá um email com o conteúdo especificado:

    ![Email recebido](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>Impedir loops infinitos

Um loop "Until" possui limites padrão que param a execução se alguma destas condições ocorrer:

| Propriedade | Valor padrão | DESCRIÇÃO | 
| -------- | ------------- | ----------- | 
| **Contagem** | 60 | O número máximo de loops que são executados antes da saída do loop. O padrão é 60 ciclos. | 
| **Tempo limite** | PT1H | O tempo máximo para executar um loop antes da saída do loop. O padrão é uma hora e é especificado no formato ISO 8601. <p>O valor de tempo limite é avaliado para cada ciclo de loop. Se qualquer ação no loop demorar mais que o limite de tempo, o ciclo atual não irá parar, mas o próximo ciclo não iniciará porque a condição de limite não é atendida. | 
|||| 

Para alterar esses limites padrão, escolha **Mostrar opções avançadas** na forma de ação do loop.

<a name="until-json"></a>

## <a name="until-definition-json"></a>Definição "Until" (JSON)

Se você estiver trabalhando na exibição de código para o aplicativo lógico, será possível definir um loop `Until` na definição JSON do aplicativo lógico, por exemplo:

``` json
"actions": {
    "Initialize_variable": {
        // Definition for initialize variable action
    },
    "Send_an_email": {
        // Definition for send email action
    },
    "Until": {
        "type": "Until",
        "actions": {
            "Increment_variable": {
                "type": "IncrementVariable",
                "inputs": {
                    "name": "Limit",
                    "value": 1
                },
                "runAfter": {}
            }
        },
        "expression": "@equals(variables('Limit'), 10)",
        // To prevent endless loops, an "Until" loop 
        // includes these default limits that stop the loop. 
        "limit": { 
            "count": 60,
            "timeout": "PT1H"
        },
        "runAfter": {
            "Initialize_variable": [
                "Succeeded"
            ]
        },
    }
},
```

Em outro exemplo, esse loop "Until" chama um ponto de extremidade HTTP que cria um recurso e para quando o corpo de resposta HTTP retorna com o status "Concluído". Para impedir loops infinitos, o loop também irá parar se alguma destas condições ocorrer:

* O loop foi executado 10 vezes, conforme especificado pelo atributo `count`. O padrão é 60 vezes. 
* O loop tentou executar durante duas horas, conforme especificado pelo atributo `timeout` no formato ISO 8601. O padrão é uma hora.
  
``` json
"actions": {
    "myUntilLoopName": {
        "type": "Until",
        "actions": {
            "Create_new_resource": {
                "type": "Http",
                "inputs": {
                    "body": {
                        "resourceId": "@triggerBody()"
                    },
                    "url": "https://domain.com/provisionResource/create-resource",
                    "body": {
                        "resourceId": "@triggerBody()"
                    }
                },
                "runAfter": {},
                "type": "ApiConnection"
            }
        },
        "expression": "@equals(triggerBody(), 'Completed')",
        "limit": {
            "count": 10,
            "timeout": "PT2H"
        },
        "runAfter": {}
    }
},
```

## <a name="get-support"></a>Obtenha suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em recursos e sugestões, visite o [site de comentários do usuário de Aplicativos Lógicos do Azure](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* [Executar etapas baseadas em uma condição (instruções condicionais)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Executar etapas baseadas em valores diferentes (instruções de comutador)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Executar ou mesclar etapas paralelas (branches)](../logic-apps/logic-apps-control-flow-branches.md)
* [Executar etapas baseadas no status da ação agrupada (escopos)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
