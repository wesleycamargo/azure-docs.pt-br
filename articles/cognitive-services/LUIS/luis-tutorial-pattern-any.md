---
title: Tutorial que usa a entidade pattern.any para aprimorar as previsões do LUIS - Azure | Microsoft Docs
titleSuffix: Cognitive Services
description: Neste tutorial, use a entidade pattern.any para aprimorar as previsões de entidade e intenção do LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: 43f169ae11191c2e98c4538189bce781821de980
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44157847"
---
# <a name="tutorial-improve-app-with-patternany-entity"></a>Tutorial: Aprimorar o aplicativo com a entidade pattern.any

Neste tutorial, use a entidade pattern.any para aumentar a previsão de entidade e intenção.  

> [!div class="checklist"]
* Saiba quando e como usar a pattern.any
* Criar um padrão que usa a pattern.any
* Como verificar melhorias na previsão

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Antes de começar
Caso não tenha o aplicativo de Recursos Humanos do tutorial das [funções de padrão](luis-tutorial-pattern-roles.md), [importe](luis-how-to-start-new-app.md#import-new-app) o JSON em um aplicativo novo no site do [LUIS](luis-reference-regions.md#luis-website). O aplicativo a ser importado pode ser encontrado no repositório GitHub [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-roles-HumanResources.json).

Caso queira manter o aplicativo de recursos humanos original, clone a versão na página [Configurações](luis-how-to-manage-versions.md#clone-a-version) e nomeie-a como `patt-any`. A clonagem é uma ótima maneira de testar vários recursos de LUIS sem afetar a versão original. 

## <a name="the-purpose-of-patternany"></a>A finalidade da pattern.any
A entidade pattern.any permite localizar dados de formulário livres em que as palavras da entidade dificultam determinar o fim da entidade no resto do enunciado. 

Este aplicativo de Recursos Humanos ajuda os funcionários a encontrar formulários da empresa. Os formulários foram adicionados no [tutorial de expressão regular](luis-quickstart-intents-regex-entity.md). Os nomes de formulário desse tutorial usaram uma expressão regular para extrair um nome do formulário que foi bem formatado, tal como os nomes de formulário em negrito na tabela de enunciados a seguir:

|Enunciado|
|--|
|Onde está **HRF-123456**?|
|Quem criou **HRF 123234**?|
|**HRF-456098** está publicado em francês?|

No entanto, cada formulário tem um nome formatado, usado na tabela anterior, bem como um nome amigável, como `Request relocation from employee new to the company 2018 version 5`. 

Enunciados com o nome amigável do formulário se parecem com:

|Enunciado|
|--|
|Onde está **Solicitar a realocação do funcionário novo para a versão 5 da empresa 2018**?|
|Quem criou **“Solicitar a realocação do funcionário novo para a versão 5 da empresa 2018”**?|
|**Solicitar a realocação do funcionário novo para a versão 5 da empresa 2018** está publicado em francês?|

O comprimento variável inclui frases que podem confundir o LUIS sobre onde a entidade termina. Usando uma entidade Pattern.any em um padrão permite especificar o início e o fim do nome do formulário para que o LUIS extraia corretamente o nome do formulário.

**Embora os padrões permitem que você forneça menos enunciados de exemplo, se as entidades não são detectadas, o padrão não coincide.**

## <a name="add-example-utterances-to-the-existing-intent-findform"></a>Adicionar enunciados de exemplo à intenção FindForm existente 
Remova a entidade keyPhrase predefinida se for difícil criar e identificar a entidade FormName. 

1. Selecione **Construir** no painel de navegação superior e, em seguida, selecione **Intenções** na barra de navegação esquerda.

2. Selecione **FindForm** na lista de intenções.

3. Adicione alguns enunciados de exemplo:

    |Exemplo de enunciado|
    |--|
    |Onde está o formulário **O que fazer quando ocorrer um incêndio no laboratório** e quem precisa para assiná-lo depois que eu lê-lo?|
    |Onde está **Solicitar a realocação do funcionário novo para a empresa** no servidor?|
    |Quem criou "**Solicitações de saúde e bem-estar no campus principal**" e qual é a versão mais atual?|
    |Estou procurando o formulário nomeado "**Solicitação de mudança do escritório incluindo ativos físicos**". |

    Sem uma entidade Pattern.any, seria difícil para o LUIS reconhecer onde o título do formulário termina por causa das muitas variações dos nomes de formulário.

## <a name="create-a-patternany-entity"></a>Criar uma entidade Pattern.any
A entidade Pattern.any extrai entidades de comprimento variável. Isso só funciona em um padrão, porque o ele marca o início e o fim da entidade. Se você achar que seu padrão, quando ele incluir um Pattern.any, extrai as entidades incorretamente, use uma [lista explícita](luis-concept-patterns.md#explicit-lists) para corrigir esse problema. 

1. Selecione **Entidades** no painel de navegação esquerdo.

2. Selecione **Criar nova entidade**, insira o nome `FormName` e selecione **Pattern.any** como o tipo. Selecione **Concluído**. 

    Não é possível identificar a entidade na intenção porque uma Pattern.any somente é válida em um padrão. 

    Se você quiser que os dados extraídos incluam outras entidades, como número ou datetimeV2, será necessário criar uma entidade de composição que inclua a Pattern.any, bem como o número e o datetimeV2.

## <a name="add-a-pattern-that-uses-the-patternany"></a>Adicionar um padrão que usa o Pattern.any

1. Selecione **Padrões** no painel de navegação esquerdo.

2. Selecione a intenção **FindForm**.

3. Insira os seguintes enunciados de modelo que usam a nova entidade:

    |Enunciados de modelo|
    |--|
    |Qual é o formulário ["]{FormName}["] e quem precisa para assiná-lo depois que eu lê-lo[?]|
    |Onde está ["]{FormName}["] no servidor[?]|
    |Quem criou ["]{FormName}["] e qual é a versão mais atual[?]|
    |Estou procurando o formulário nomeado ["]{FormName}["][.]|

    Se você quiser contabilizar variações do formulário, como aspas simples em vez de aspas duplas ou um ponto final em vez de um ponto de interrogação, crie um novo padrão para cada variação.

4. Se você tiver removido a entidade keyPhrase, adicione-a de volta ao aplicativo. 

## <a name="train-the-luis-app"></a>Treinar o aplicativo LUIS

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Testar o novo padrão quanto à extração de dados de forma livre
1. Selecione **Testar** na barra superior para abrir o painel de teste. 

2. Insira o enunciado a seguir: 

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

3. Selecione **Inspecionar** embaixo do resultado para ver os resultados de teste para a entidade e para a intenção.

    A entidade `FormName` é encontrada primeiro e o padrão é encontrado depois, determinando a intenção. Se tiver um resultado do teste em que as entidades não forem detectadas e, portanto, o padrão não for encontrado, será necessário adicionar mais declarações de exemplo sobre a intenção (não o padrão).

4. Feche o painel de teste selecionando o botão **Testar** na navegação superior.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como usar funções com um padrão](luis-tutorial-pattern-roles.md)