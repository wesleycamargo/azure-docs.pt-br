---
title: "Versão 2016-06-01 do novo esquema | Microsoft Docs"
description: "Aprenda a escrever a definição JSON para os aplicativos lógicos mais recentes"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 349d57e8-f62b-4ec6-a92f-a6e0242d6c0e
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: aaacb76fe845ca8892e4fe3979be5ea6ac5e902b


---
# <a name="new-schema-version-2016-06-01"></a>Novo versão de esquema 2016-06-01
A nova versão de esquema e API para aplicativos lógicos tem uma série de aprimoramentos que melhoram a confiabilidade e a facilidade de uso dos aplicativos lógicos. Há três diferenças principais:

1. Adição de escopos, que são ações que contêm coleções de ações.
2. Condições e loops são ações de primeira classe
3. Ordem de execução mais detalhada por meio da propriedade `runAfter` (que substitui `dependsOn`)

Para obter informações sobre como atualizar seus aplicativos lógicos do esquema 2015-08-01-preview para o esquema de 2016-06-01, [confira a seção de atualização abaixo.](#upgrading-to-2016-06-01-schema)

## <a name="1-scopes"></a>1. Escopos
Uma das maiores alterações nesse esquema é a inclusão de escopos e a capacidade de aninhar ações uma dentro da outra.  Isso é útil ao agrupar um conjunto de ações ou quando precisar aninhar ações entre si (por exemplo, uma condição pode conter outra condição).  Mais detalhes sobre a sintaxe de escopo podem ser encontrados [aqui](../logic-apps/logic-apps-loops-and-scopes.md), mas um exemplo simples de escopo pode ser encontrado abaixo:

```
{
    "actions": {
        "My_Scope": {
            "type": "scope",
            "actions": {                
                "Http": {
                    "inputs": {
                        "method": "GET",
                        "uri": "http://www.bing.com"
                    },
                    "runAfter": {},
                    "type": "Http"
                }
            }
        }
    }
}
```

## <a name="2-conditions-and-loops-changes"></a>2. Alterações de condições e loops
Nas versões anteriores do esquema, condições e loops eram parâmetros associados a uma única ação.  Essa limitação foi eliminada nesse esquema e agora condições e loops aparecem como um tipo de ação.  Mais informações podem ser encontradas [neste artigo](../logic-apps/logic-apps-loops-and-scopes.md), e um exemplo simples de uma ação de condição é mostrado abaixo:

```
{
    "If_trigger_is_foo": {
        "type": "If",
        "expression": "@equals(triggerBody(), 'foo')",
        "runAfter": { },
        "actions": {
            "Http_2": {
                "inputs": {
                    "method": "GET",
                    "uri": "http://www.bing.com"
                },
                "runAfter": {},
                "type": "Http"
            }
        },
        "else": 
        {
            "if_trigger_is_bar": "..."
        }      
    }
}
```

## <a name="3-runafter-property"></a>3. Propriedade RunAfter
A nova propriedade `runAfter` está substituindo `dependsOn` para ajudar a permitir mais precisão na ordem de execução.  `dependsOn` era sinônimo de "a ação foi executada e bem-sucedida". No entanto, muitas vezes você precisará executar uma ação se a ação anterior tiver sido bem-sucedida, tiver falhado ou tiver sido ignorada.  `runAfter` proporciona essa flexibilidade.  Ela é um objeto que especifica todos os nomes de ação que serão executadas antes dela e define uma matriz de status de onde pode ser disparada adequadamente.  Por exemplo, se quisesse executar uma ação depois que a etapa A tivesse sido bem-sucedida e a etapa B tivesse sido bem-sucedida ou falhado, você construiria a seguinte propriedade `runAfter`:

```
{
    "...",
    "runAfter": {
        "A": ["Succeeded"],
        "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrading-to-2016-06-01-schema"></a>Atualização para o esquema 2016-06-01
A atualização para o novo esquema 2016-06-01 exige apenas algumas etapas.  Detalhes sobre as alterações de esquema podem ser encontrados [neste artigo](../logic-apps/logic-apps-schema-2016-04-01.md).  O processo de atualização inclui executar o script de atualização, salvar como um novo aplicativo lógico e possivelmente substituir um aplicativo lógico anterior, se necessário.

1. Abra seu aplicativo lógico atual.
2. Clique no botão **Atualizar Esquema** na barra de ferramentas
   
    ![][1]
   
    A definição atualizada será retornada.  Você poderá copiá-la e colá-la em uma definição de recurso se precisar, mas é **enfaticamente recomendável** usar o botão **Salvar como** para garantir que todas as referências de conexão sejam válidas no aplicativo lógico atualizado.
3. Clique no botão **Salvar como** na barra de ferramentas da folha de atualização.
4. Insira o nome e o status do aplicativo lógico e clique em **Criar** para implantar seu aplicativo lógico de atualização.
5. Verifique se seu aplicativo lógico atualizado está funcionando conforme o esperado.
   
   > [!NOTE]
   > Se você estiver usando um gatilho manual ou de solicitação, a URL de retorno de chamada será alterada em seu novo aplicativo lógico.  Use a nova URL para verificar se ela funciona de ponta a ponta. Você pode clonar seu aplicativo lógico existente para preservar URLs anteriores.
   > 
   > 
6. *Opcional* Use o botão **Clonar** na barra de ferramentas (ao lado do ícone **Atualizar Esquema** na imagem acima) para substituir seu aplicativo lógico anterior pela nova versão do esquema.  Isso será necessário apenas se você quiser manter a mesma ID de recurso ou URL de gatilho de solicitação do aplicativo lógico.

### <a name="upgrade-tool-notes"></a>Observações sobre a ferramenta de atualização
#### <a name="condition-mapping"></a>Mapeamento de condição
A ferramenta se esforçará para agrupar as ações de ramificação true e false juntas em um escopo na definição atualizada.  Mais especificamente, o padrão de designer de `@equals(actions('a').status, 'Skipped')` deve aparecer como uma ação `else`.  No entanto, se a ferramenta detectar padrões não reconhecidos, ela possivelmente criará condições separadas tanto para a ramificação true quanto para a false.  As ações podem ser remapeadas após a atualização, se necessário.

#### <a name="foreach-with-condition"></a>ForEach com condição
O padrão anterior de um loop foreach com uma condição por item pode ser replicado no novo esquema com a ação de filtro.  Isso deve ocorrer automaticamente na atualização.  A condição se torna uma ação de filtro antes do loop foreach (para retornar apenas uma matriz de itens que correspondem à condição) e essa matriz é passada para a ação foreach.  Você pode ver um exemplo disso [neste artigo](../logic-apps/logic-apps-loops-and-scopes.md)

#### <a name="resource-tags"></a>Marcações de recursos
As marcas de recurso serão removidas na atualização e você precisará defini-las novamente para o fluxo de trabalho atualizado.

## <a name="other-changes"></a>Outras alterações
### <a name="manual-trigger-renamed-to-request-trigger"></a>Gatilho manual renomeado como Gatilho de solicitação
O tipo `manual` foi preterido e renomeado como `request` com o tipo `http`.  Isso é mais consistente com o tipo de padrão que o disparador costuma usar.

### <a name="new-filter-action"></a>Nova ação 'filter'
Se você estiver trabalhando com uma matriz grande e precisar filtrá-los em um conjunto menor de itens, poderá usar o novo tipo 'filter'.  Ela aceita uma matriz e uma condição e avaliará a condição de cada item e retornará uma matriz de itens que atendem à condição.

### <a name="foreach-and-until-action-restrictions"></a>Restrições de ação ForEach e until
Os loops Foreach e until estão restritos a uma única ação.

### <a name="trackedproperties-on-actions"></a>TrackedProperties em ações
As ações agora podem ter uma propriedade adicional (semelhante a `runAfter` e `type`) chamada `trackedProperties`.  É um objeto que especifica que certas entradas ou saídas de ação devem ser incluídas na telemetria do Diagnóstico do Azure emitida como parte de um fluxo de trabalho.  Por exemplo:

```
{                
    "Http": {
        "inputs": {
            "method": "GET",
            "uri": "http://www.bing.com"
        },
        "runAfter": {},
        "type": "Http",
        "trackedProperties": {
            "responseCode": "@action().outputs.statusCode",
            "uri": "@action().inputs.uri"
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas
* [Usar a definição de fluxo de trabalho do aplicativo lógico](../logic-apps/logic-apps-author-definitions.md)
* [Criar um modelo de implantação do aplicativo lógico](../logic-apps/logic-apps-create-deploy-template.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png



<!--HONumber=Jan17_HO3-->


