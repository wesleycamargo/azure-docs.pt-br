<properties 
	pageTitle="Novo esquema versão 2016-06-01 | Microsoft Azure" 
	description="Aprenda a escrever a definição JSON para os aplicativos lógicos mais recentes" 
	authors="jeffhollan" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/25/2016"
	ms.author="jehollan"/>
	
# Novo versão de esquema 2016-06-01

A nova versão de esquema e API para aplicativos lógicos tem uma série de aprimoramentos que melhoram a confiabilidade e a facilidade de uso dos aplicativos lógicos. Há três diferenças principais:

1. Adição de escopos, que são ações que contêm coleções de ações.
1. Condições e loops são ações de primeira classe
1. Ordem de execução mais detalhada por meio da propriedade `runAfter` (que substitui o `dependsOn`)

Para obter informações sobre como atualizar seus aplicativos lógicos do esquema 2015-08-01-preview para o esquema de 2016-06-01, [confira a seção de atualização abaixo.](#upgrading-to-2016-06-01-schema)


## 1\. Escopos

Uma das maiores alterações nesse esquema é a inclusão de escopos e a capacidade de aninhar ações uma dentro da outra. Isso é útil ao agrupar um conjunto de ações ou quando precisar aninhar ações entre si (por exemplo, uma condição pode conter outra condição). Mais detalhes sobre a sintaxe de escopo podem ser encontrados [aqui](app-service-logic-loops-and-scopes.md), mas um exemplo simples de escopo pode ser encontrado abaixo:


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

## 2\. Alterações de condições e loops

Nas versões anteriores do esquema, condições e loops eram parâmetros associados a uma única ação. Essa limitação foi eliminada nesse esquema e agora condições e loops aparecem como um tipo de ação. Mais informações podem ser encontradas [neste artigo](app-service-logic-loops-and-scopes.md), e um exemplo simples de uma ação de condição é mostrado abaixo:

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

## 3\. Propriedade RunAfter

A nova propriedade `runAfter` está substituindo a propriedade `dependsOn` para permitir uma maior precisão na ordem de execução. `dependsOn` era sinônimo de "a ação foi executada e bem-sucedida". No entanto, muitas vezes você precisará executar uma ação se a ação anterior tiver êxito, falha ou for ignorada. `runAfter` permite essa flexibilidade. Ela é um objeto que especifica todos os nomes de ação que serão executadas antes dela e define uma matriz de status de onde pode ser disparada adequadamente. Por exemplo, se você quisesse executar depois que a etapa A foi bem-sucedida e que a etapa B foi bem-sucedida ou falhou, poderia construir a seguinte propriedade `runAfter`:

```
{
    "...",
    "runAfter": {
        "A": ["Succeeded"],
        "B": ["Succeeded", "Failed"]
    }
}
```

## Atualização para o esquema 2016-06-01

A atualização para o novo esquema 2016-06-01 exige apenas algumas etapas. Detalhes sobre as alterações de esquema podem ser encontrados [neste artigo](app-service-logic-schema-2016-04-01.md). O processo de atualização inclui executar o script de atualização, salvar como um novo aplicativo lógico e possivelmente substituir um aplicativo lógico anterior, se necessário.

1. Abra seu aplicativo lógico atual.
1. Clique no botão **Atualizar Esquema** na barra de ferramentas
   
    ![][1]
   
    A definição atualizada será retornada. Você poderá copiá-la e colá-la em uma definição de recurso se precisar, mas **recomendamos fortemente** que você use o botão **Salvar como** para garantir que todas as referências de conexão sejam válidas no aplicativo lógico atualizado.
1. Clique no botão **Salvar como** na barra de ferramentas da folha de atualização.
1. Insira o nome e o status do aplicativo lógico e clique em **Criar** para implantar seu aplicativo lógico de atualização.
1. Verifique se seu aplicativo lógico atualizado está funcionando conforme o esperado.

    >[AZURE.NOTE] Se você estiver usando um gatilho manual ou de solicitação, a URL de retorno de chamada será alterada em seu novo aplicativo lógico. Use a nova URL para verificar se ela funciona de ponta a ponta. Você pode clonar seu aplicativo lógico existente para preservar URLs anteriores.

1. *Opcional* Use o botão **Clonar** na barra de ferramentas (ao lado do ícone **Atualizar Esquema** na imagem acima) para substituir seu aplicativo lógico anterior pela nova versão do esquema. Isso será necessário apenas se você quiser manter a mesma ID de recurso ou URL de gatilho de solicitação do aplicativo lógico.

### Observações sobre a ferramenta de atualização

#### Mapeamento de condição

A ferramenta se esforçará para agrupar as ações de ramificação true e false juntas em um escopo na definição atualizada. Mais especificamente, o padrão de designer de `@equals(actions('a').status, 'Skipped')` deve aparecer como uma ação `else`. No entanto, se a ferramenta detectar padrões não reconhecidos, ela possivelmente criará condições separadas tanto para a ramificação true quanto para a false. As ações podem ser remapeadas após a atualização, se necessário.

#### ForEach com condição
  
O padrão anterior de um loop foreach com uma condição por item pode ser replicado no novo esquema com a ação de filtro. Isso deve ocorrer automaticamente na atualização. A condição se torna uma ação de filtro antes do loop foreach (para retornar apenas uma matriz de itens que correspondem à condição) e essa matriz é passada para a ação foreach. Você pode ver um exemplo disso [neste artigo](app-service-logic-loops-and-scopes.md)

#### Marcações de recursos

As marcas de recurso serão removidas na atualização e você precisará defini-las novamente para o fluxo de trabalho atualizado.

## Outras alterações

### Gatilho manual renomeado como Gatilho de solicitação

O tipo `manual` foi preterido e renomeado como `request` com o tipo `http`. Isso é mais consistente com o tipo de padrão que o disparador costuma usar.

### Nova ação 'filter'

Se você estiver trabalhando com uma matriz grande e precisar filtrá-los em um conjunto menor de itens, poderá usar o novo tipo 'filter'. Ela aceita uma matriz e uma condição e avaliará a condição de cada item e retornará uma matriz de itens que atendem à condição.

### Restrições de ação ForEach e until

Os loops Foreach e until estão restritos a uma única ação.

### TrackedProperties em ações

As ações agora podem ter uma propriedade adicional (semelhante a `runAfter` e `type`) chamada `trackedProperties`. É um objeto que especifica que certas entradas ou saídas de ação devem ser incluídas na telemetria do Diagnóstico do Azure emitida como parte de um fluxo de trabalho. Por exemplo:

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

## Próximas etapas
- [Usar a definição de fluxo de trabalho do aplicativo lógico](app-service-logic-author-definitions.md)
- [Criar um modelo de implantação do aplicativo lógico](app-service-logic-create-deploy-template.md)


<!-- Image references -->
[1]: ./media/app-service-logic-schema-2016-04-01/upgradeButton.png

<!---HONumber=AcomDC_0727_2016-->