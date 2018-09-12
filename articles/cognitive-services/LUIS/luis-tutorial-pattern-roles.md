---
title: Tutorial Usando Funções de Padrão para Melhorar as Previsões do LUIS - Azure | Microsoft Docs
titleSuffix: Cognitive Services
description: Neste tutorial, use funções de padrão para entidades relacionadas ao contexto para melhorar as previsões de LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 08/03/2018
ms.author: diberry
ms.openlocfilehash: 6f3e7c9db7bbdb6bc24d123208355fc7a1d8e7e8
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44161927"
---
# <a name="tutorial-improve-app-with-pattern-roles"></a>Tutorial: melhorar o aplicativo com papéis padrão

Neste tutorial, use uma entidade simples com funções combinadas com padrões para aumentar a intenção e a predição da entidade.  Ao usar padrões, menos declarações de exemplo são necessárias para a intenção.

> [!div class="checklist"]
* Entender as funções padrão
* Usar a entidade simples com funções 
* Criar padrão para declarações usando a entidade simples com funções
* Como verificar aprimoramentos na previsão de padrões

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Antes de começar
Se você não tiver o aplicativo Recursos Humanos no tutorial [padrão](luis-tutorial-pattern.md), [importe](luis-how-to-start-new-app.md#import-new-app) o JSON para um novo aplicativo no site [LUIS](luis-reference-regions.md#luis-website). O aplicativo a ser importado pode ser encontrado no repositório GitHub [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-patterns-HumanResources-v2.json).

Caso queira manter o aplicativo de recursos humanos original, clone a versão na página [Configurações](luis-how-to-manage-versions.md#clone-a-version) e nomeie-a como `roles`. A clonagem é uma ótima maneira de testar vários recursos de LUIS sem afetar a versão original. 

## <a name="the-purpose-of-roles"></a>A finalidade de funções
O propósito das funções é extrair entidades relacionadas ao contexto em um enunciado. No enunciado, `Move new employee Robert Williams from Sacramento and San Francisco`, a cidade de origem e os valores da cidade de destino estão relacionados entre si e usam uma linguagem comum para denotar cada local. 

Ao usar padrões, qualquer entidade no padrão deve ser detectada _antes de_ o padrão corresponder à expressão. 

Quando você cria um padrão, o primeiro passo é selecionar a intenção do padrão. Ao selecionar a intenção, se o padrão corresponder, a intenção correta será sempre retornada com uma pontuação alta (normalmente 99-100%). 

### <a name="compare-hierarchical-entity-to-simple-entity-with-roles"></a>Comparar a entidade hierárquica com entidade simples com funções

No [tutorial hierárquico](luis-quickstart-intent-and-hier-entity.md), a intenção **MoveEmployee** detectou quando mover um funcionário existente de um prédio e escritório para outro. Os exemplos de declarações tinham localizações de origem e destino, mas não utilizavam funções. Em vez disso, a origem e o destino eram filhos da entidade hierárquica. 

Neste tutorial, o aplicativo Recursos Humanos detecta enunciados sobre como mover novos funcionários de uma cidade para outra. Esses dois tipos de enunciados são semelhantes, mas resolvidos com diferentes habilidades de LUIS.

|Tutorial|Exemplo de enunciado|Locais de origem e destino|
|--|--|--|
|[Hierarchical (não há funções)](luis-quickstart-intent-and-hier-entity.md)|MV Jill Jones partir **a-2349** para **b-1298**|a-2349, b-1298|
|Neste tutorial (com funções)|Mover Billy Patterson partir **Yuma** à **Denver**.|Yuma, Denver|

Você não pode usar a entidade hierárquica no padrão porque somente os pais hierárquicos são usados em padrões. Para retornar os locais nomeados de origem e destino, você deve usar um padrão.

### <a name="simple-entity-for-new-employee-name"></a>Entidade simples para o novo nome do funcionário
O nome do novo funcionário, Billy Patterson, não faz parte da entidade lista **funcionário** ainda. O nome do novo funcionário é extraído primeiro, para enviar o nome a um sistema externo para criar as credenciais da empresa. Depois que as credenciais da empresa são criadas, as credenciais dos funcionários são adicionadas à entidade da lista **Funcionários**.

O **funcionário** lista foi criada na [tutorial lista](luis-quickstart-intent-and-list-entity.md).

A entidade **NewEmployee** é uma entidade simples sem funções. 

### <a name="simple-entity-with-roles-for-relocation-cities"></a>Entidade simples com funções para cidades de realocação
O novo funcionário e a família precisam ser transferidos da cidade atual para uma cidade onde a empresa fictícia esteja localizada. Como um novo funcionário pode vir de qualquer cidade, os locais precisam ser descobertos. Uma lista de configurações, como uma entidade de lista, não funcionaria porque apenas as cidades da lista seriam extraídas.

Os nomes de funções associados às cidades de origem e de destino precisam ser exclusivos em todas as entidades. Uma maneira fácil de garantir que as funções sejam exclusivas é vinculá-las à entidade que as contém por meio de uma estratégia de nomenclatura. A entidade **NewEmployeeRelocation** é uma entidade simples com duas funções: **NewEmployeeReloOrigin** e **NewEmployeeReloDestination**.

### <a name="simple-entities-need-enough-examples-to-be-detected"></a>Entidades simples precisam suficiente exemplos para ser detectada
Porque a expressão de exemplo `Move new employee Robert Williams from Sacramento and San Francisco` tem somente entidades computador aprendeu, é importante fornecer suficiente declarações de exemplo à intenção, de modo que as entidades são detectadas.  

**Embora os padrões permitem que você forneça declarações menos de exemplo, se as entidades não são detectadas, o padrão não coincide.**

Se você tiver dificuldades com a detecção de entidade simples, porque é um nome como uma cidade, considere adicionar uma lista de frases de valores semelhantes. Isso ajuda na detecção do nome da cidade, dando LUIS um sinal adicional sobre esse tipo de palavra ou frase. As listas de frases só ajudam o padrão ajudando na detecção de entidade, que é necessária para que o padrão corresponda. 

## <a name="create-new-entities"></a>Criar novas entidades
1. Selecione **Build** no menu superior.

2. Selecione **entidades** na navegação à esquerda. 

3. Selecione **Criar nova entidade**.

4. Na janela pop-up, insira `NewEmployee` como um **simples** entidade.

5. Selecione **Criar nova entidade**.

6. Na janela pop-up, insira `NewEmployeeRelocation` como um **simples** entidade.

7. Selecione **NewEmployeeRelocation** na lista de entidades. 

8. Digite a primeira função como `NewEmployeeReloOrigin` e selecione enter.

9. Digite a segunda função como `NewEmployeeReloDestination` e selecione enter.

## <a name="create-new-intent"></a>Criar nova tentativa
Rotular as entidades nessas etapas pode ser mais fácil se a entidade keyPhrase pré-construída for removida antes de começar e depois incluída novamente depois que você concluir as etapas desta seção. 

1. Selecione **Intenções** no painel de navegação esquerdo.

2. Selecione **Criar nova intenção**. 

3. Insira `NewEmployeeRelocationProcess` como o nome intencional na caixa de diálogo pop-up.

4. Insira as seguintes declarações de exemplo, rotulação novas entidades. Os valores de entidade e função estão em negrito. Lembre-se de mudar para o **Tokens View** se achar mais fácil rotular o texto. 

    Você não especifica o papel da entidade ao rotular a intenção. Você faz isso mais tarde ao criar o padrão. 

    |Enunciado|NewEmployee|NewEmployeeRelocation|
    |--|--|--|
    |Mover **Bob Jones** partir **Seattle** para **Los Colinas**|Bob Jones|Seattle, Los Colinas|
    |Mover **Dave C. Cooper** partir **Redmond** para **cidade de Nova York**|Dave C. Cooper|Cidade de Redmond, Nova York|
    |Mover **Jim Paul Smith** partir **Toronto** para **Vancouver Oeste**|Jim Paul Smith|Toronto, Vancouver Oeste|
    |Mover **J. Mello** partir **Boston** para **Staines após Tâmisa**|J. Benson|Boston, Staines após Tâmisa|
    |Mover **Travis "Trav" Hinton** partir **Castelo Branco** para **Orlando**|Travis "Trav" Hinton|Castelo Branco, Orlando|
    |Mover **Trevor Nottington III** partir **Aranda de Duero** para **Boise**|Trevor Nottington III|Aranda de Duero, Boise|
    |Mover **Dr. Greg Williams** partir **Orlando** para **Ellicott cidade**|Dr. Greg Williams|Orlando, cidade de Ellicott|
    |Mover **Robert "Bobby" Gregson** de **Cidade de Kansas** para **San Juan Capistrano**|Robert "Bobby" Gregson|Kansas City, San Juan Capistrano|
    |Mover **Patti Owens** partir **Bellevue** para **Rockford**|Patti Owens|Bellevue, Rockford|
    |Mover **Janet Bartlet** partir **Tuscan** para **Santa fé**|Janet Bartlet|Tuscan, Santa fé|

    O nome do funcionário tem uma variedade de prefixo, contagem de palavras, sintaxe e sufixo. Isso é importante para o LUIS entender as variações de um novo nome de funcionário. Os nomes das cidades também têm uma variedade de palavras e sintaxe. Essa variedade é importante para ensinar ao LUIS como essas entidades podem aparecer no enunciado do usuário. 
    
    Se uma das entidades tivesse a mesma contagem de palavras e nenhuma outra variação, você ensinaria a LUIS que essa entidade tem apenas a contagem de palavras e nenhuma outra variação. LUIS não seria capaz de prever corretamente um conjunto mais amplo de variações porque não foi mostrado nenhum. 

    Se você removeu a entidade keyPhrase, adicione-a novamente ao aplicativo agora.

## <a name="train-the-luis-app"></a>Treinar o aplicativo LUIS

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicar o aplicativo para obter a URL do ponto de extremidade

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-without-pattern"></a>Consulta o ponto de extremidade sem padrão

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. Vá até o final da URL no endereço e insira `Move Wayne Berry from Miami to Mount Vernon`. O último parâmetro de querystring é `q`, o enunciado **consulta**. 

    ```JSON
    {
      "query": "Move Wayne Berry from Newark to Columbus",
      "topScoringIntent": {
        "intent": "NewEmployeeRelocationProcess",
        "score": 0.514479756
      },
      "intents": [
        {
          "intent": "NewEmployeeRelocationProcess",
          "score": 0.514479756
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.017118983
        },
        {
          "intent": "MoveEmployee",
          "score": 0.009982505
        },
        {
          "intent": "GetJobInformation",
          "score": 0.008637771
        },
        {
          "intent": "ApplyForJob",
          "score": 0.007115978
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.006120186
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00452428637
        },
        {
          "intent": "None",
          "score": 0.00400899537
        },
        {
          "intent": "OrgChart-Reports",
          "score": 0.00240071164
        },
        {
          "intent": "Utilities.Help",
          "score": 0.001770991
        },
        {
          "intent": "EmployeeFeedback",
          "score": 0.001697356
        },
        {
          "intent": "OrgChart-Manager",
          "score": 0.00168116146
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00163952739
        },
        {
          "intent": "FindForm",
          "score": 0.00112958835
        }
      ],
      "entities": [
        {
          "entity": "wayne berry",
          "type": "NewEmployee",
          "startIndex": 5,
          "endIndex": 15,
          "score": 0.629158735
        },
        {
          "entity": "newark",
          "type": "NewEmployeeRelocation",
          "startIndex": 22,
          "endIndex": 27,
          "score": 0.638941
        }
      ]
    }  
    ```

A pontuação de predição de intenção é apenas cerca de 50%. Se o seu aplicativo cliente requer um número maior, isso precisa ser corrigido. As entidades não estavam previstos qualquer um.

Os padrões ajudarão a pontuação de previsão, no entanto, as entidades devem ser corretamente previstas antes que o padrão corresponda à expressão. 

## <a name="add-a-pattern-that-uses-roles"></a>Adicionar um padrão que usa funções
1. Selecione **Build** no painel de navegação superior.

2. Selecione **padrões** no painel de navegação à esquerda.

3. Selecione **NewEmployeeRelocationProcess** da **selecionar uma intenção** lista suspensa. 

4. Insira o seguinte padrão: `move {NewEmployee} from {NewEmployeeRelocation:NewEmployeeReloOrigin} to {NewEmployeeRelocation:NewEmployeeReloDestination}[.]`

    Se você treina, publica e consulta o endpoint, pode ficar desapontado ao ver que as entidades não são encontradas, portanto, o padrão não corresponde, portanto, a previsão não melhorou. Essa é uma consequência de não haver exemplos de declarações suficientes com entidades rotuladas. Em vez de adicionar mais exemplos, adicione uma lista de frases para corrigir esse problema.

## <a name="create-a-phrase-list-for-cities"></a>Criar uma lista de frases para cidades
As cidades, como os nomes das pessoas, são complicadas, pois podem ser qualquer mistura de palavras e pontuação. Mas as cidades da região e do mundo são conhecidas, então LUIS precisa de uma lista de frases de cidades para começar a aprender. 

1. Selecione **lista de frases** da **melhorar o desempenho do aplicativo** seção do menu à esquerda. 

2. A lista de nomes `Cities` e adicione o seguinte `values` para obter a lista:

    |Valores da lista de frases|
    |--|
    |Seattle|
    |San Diego|
    |Cidade de Nova York|
    |Los Angeles|
    |Portland|
    |Philadephia|
    |Miami|
    |Dallas|

    Não adicione todas as cidades do mundo ou mesmo todas as cidades da região. LUIS precisa ser capaz de generalizar o que uma cidade é da lista. 

    Certifique-se de manter **esses valores são intercambiáveis** selecionado. Essa configuração significa que as palavras na lista em tratado como sinônimos. Isso é exatamente como eles devem ser tratados no padrão.

    Lembre-se [a última vez](luis-quickstart-primary-and-secondary-data.md) também foi criada uma lista de frases de série de tutoriais melhorar a detecção de entidade de uma entidade simples.  

3. Treine e publique o aplicativo.

## <a name="query-endpoint-for-pattern"></a>Ponto final da consulta para o padrão
1. Na página **Publicar**, selecione o link do **ponto de extremidade** na parte inferior da página. Essa ação abre outra janela do navegador com a URL de ponto de extremidade na barra de endereços. 

2. Vá até o final da URL no endereço e insira `Move wayne berry from miami to mount vernon`. O último parâmetro de querystring é `q`, o enunciado **consulta**. 

    ```JSON
    {
      "query": "Move Wayne Berry from Miami to Mount Vernon",
      "topScoringIntent": {
        "intent": "NewEmployeeRelocationProcess",
        "score": 0.9999999
      },
      "intents": [
        {
          "intent": "NewEmployeeRelocationProcess",
          "score": 0.9999999
        },
        {
          "intent": "Utilities.Confirm",
          "score": 1.49678385E-06
        },
        {
          "intent": "MoveEmployee",
          "score": 8.240291E-07
        },
        {
          "intent": "GetJobInformation",
          "score": 6.3131273E-07
        },
        {
          "intent": "None",
          "score": 4.25E-09
        },
        {
          "intent": "OrgChart-Manager",
          "score": 2.8E-09
        },
        {
          "intent": "OrgChart-Reports",
          "score": 2.8E-09
        },
        {
          "intent": "EmployeeFeedback",
          "score": 1.64E-09
        },
        {
          "intent": "Utilities.StartOver",
          "score": 1.64E-09
        },
        {
          "intent": "Utilities.Help",
          "score": 1.48181822E-09
        },
        {
          "intent": "Utilities.Stop",
          "score": 1.48181822E-09
        },
        {
          "intent": "Utilities.Cancel",
          "score": 1.35E-09
        },
        {
          "intent": "FindForm",
          "score": 1.23846156E-09
        },
        {
          "intent": "ApplyForJob",
          "score": 5.692308E-10
        }
      ],
      "entities": [
        {
          "entity": "wayne berry",
          "type": "builtin.keyPhrase",
          "startIndex": 5,
          "endIndex": 15
        },
        {
          "entity": "miami",
          "type": "builtin.keyPhrase",
          "startIndex": 22,
          "endIndex": 26
        },
        {
          "entity": "wayne berry",
          "type": "NewEmployee",
          "startIndex": 5,
          "endIndex": 15,
          "score": 0.9410646,
          "role": ""
        },
        {
          "entity": "miami",
          "type": "NewEmployeeRelocation",
          "startIndex": 22,
          "endIndex": 26,
          "score": 0.9853915,
          "role": "NewEmployeeReloOrigin"
        },
        {
          "entity": "mount vernon",
          "type": "NewEmployeeRelocation",
          "startIndex": 31,
          "endIndex": 42,
          "score": 0.986044347,
          "role": "NewEmployeeReloDestination"
        }
      ],
      "sentimentAnalysis": {
        "label": "neutral",
        "score": 0.5
      }
}
    ```

A pontuação da intenção agora é muito maior e os nomes das funções são parte da resposta da entidade.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Conheça as práticas recomendadas para aplicativos do LUIS](luis-concept-best-practices.md)
