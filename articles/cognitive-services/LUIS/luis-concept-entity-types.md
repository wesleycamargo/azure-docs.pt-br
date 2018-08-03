---
title: Noções básicas sobre tipos de entidade em aplicativos LUIS no Azure | Microsoft Docs
description: Adicione entidades (principais dados no domínio do seu aplicativo) em aplicativos LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/28/2018
ms.author: diberry
ms.openlocfilehash: ace4aa48d3bfce5f88bce8947ab568f0990d67fa
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226604"
---
# <a name="entities-in-luis"></a>Entidades no LUIS

Entidades são palavras ou frases em declarações que são importantes dados no domínio do seu aplicativo.

## <a name="entity-compared-to-intent"></a>Entidade comparada com a intenção
A entidade representa uma palavra ou frase dentro da declaração que você deseja que seja extraída. Uma declaração pode incluir muitas entidades ou nenhuma. Uma entidade representa uma classe que inclui uma coleção de objetos semelhantes (locais, coisas, pessoas, eventos ou conceitos). As entidades descrevem informações relevantes para a intenção e, às vezes, elas são essenciais para seu aplicativo realizar sua tarefa. Por exemplo, um aplicativo de Pesquisa de Notícias pode incluir entidades como "tópico", "origem", "palavra-chave" e "data de publicação", que são dados importantes para pesquisar notícias. Em um aplicativo de reservas de viagem, o "local", "data", "companhia aérea", "classe da viagem" e "passagens" são informações importantes para reserva de voos (relevantes para a intenção "Bookflight").

Por comparação, a intenção representa a previsão de toda a declaração. 

## <a name="entities-represent-data"></a>Entidades representam dados
Entidades são dados que você deseja extrair da declaração. Elas podem ser um nome, data, nome de produto ou qualquer grupo de palavras. 

|Enunciado|Entidade|Dados|
|--|--|--|
|Comprar três passagens para Nova York|Número predefinido<br>Location.Destination|3<br>Nova Iorque|
|Comprar uma passagem de Nova York para Londres em 5 de março|Location.Origin<br>Location.Destination<br>datetimeV2 predefinido|Nova Iorque<br>Londres<br>5 de março de 2018|

## <a name="entities-are-optional-but-highly-recommended"></a>As entidades são opcionais, mas altamente recomendadas
Embora as intenções sejam obrigatórias, as entidades são opcionais. Não é necessário criar entidades para todo conceito em seu aplicativo, mas apenas para os necessários para o aplicativo executar uma ação. 

Se as declarações não tiverem detalhes, seu bot precisará continuar e não será necessário adicioná-las. À medida que seu aplicativo amadurece, você pode adicioná-las depois. 

Se não tiver certeza de como você usaria as informações, adicione algumas entidades comuns predefinidas como datetimeV2, ordinal, email e número de telefone.

## <a name="label-for-word-meaning"></a>Rótulo para o significado da palavra
Se a escolha ou a disposição das palavras for a mesma, mas não significar a mesma coisa, não a rotule com a entidade. 

Nas declarações a seguir, a palavra `fair` é um homógrafo. Sua ortografia é a mesma, mas tem um significado diferente:

```
What kind of county fairs are happening in the Seattle area this summer?
Is the current rating for the Seattle review fair?
```

Se você quiser que uma entidade de evento localize todos os dados do evento, rotule a palavra `fair` na primeira declaração, mas não na segunda.

## <a name="entities-are-shared-across-intents"></a>Entidades são compartilhadas entre intenções
Entidades são compartilhadas entre intenções. Elas não pertencem a uma única intenção. Intenções e entidades podem ser associadas semanticamente, mas isso não é uma relação exclusiva.

Na declaração "Reserve-me uma passagem para Paris", Paris" é uma entidade de local de tipo. Reconhecendo que as entidades mencionadas na entrada do usuário, o LUIS ajuda você a escolher as ações específicas a serem executadas para atender a uma intenção.

## <a name="assign-entities-in-none-intent"></a>Atribuir entidades na intenção None
Todas as intenções, incluindo a intenção **None**, deve ter entidades rotuladas. Isso ajuda o LUIS a saber mais sobre onde as entidades estão nas declarações e quais palavras estão em torno das entidades. 

## <a name="types-of-entities"></a>Tipos de entidades
O LUIS oferece muitos tipos de entidades; entidades predefinidas, entidades de aprendizado de máquina personalizadas e entidades de lista.

| NOME | Rótulo Pode | DESCRIÇÃO |
| -- |--|--|
| **Predefinido** <br/>[Personalizado](#prebuilt)| |  **Definição**<br>Tipos internas que representam conceitos comuns. <br><br>**Lista**<br/>número da frase-chave, ordinal, temperatura, dimensão, dinheiro, idade, percentual, email, URL, número de telefone e frase-chave. <br><br>Os nomes da entidade predefinida são reservados. <br><br>Todas as entidades predefinidas adicionadas ao aplicativo são retornadas na consulta [ponto de extremidade](luis-glossary.md#endpoint). Para obter mais informações, confira [Entidades predefinidas](./luis-prebuilt-entities.md). <br/><br/>[Exemplo de resposta para entidade](luis-concept-data-extraction.md#prebuilt-entity-data)|
|<!-- added week of 3/21/08 --> **Expressão regular**<br/>[RegEx](#regex)||**Definição**<br>Expressão regular personalizada para texto de declaração em formato bruto. Não diferencia maiúsculas de minúsculas e ignora a variante cultural.  <br><br>Essa entidade é boa para palavras ou frases formatadas consistentemente com qualquer variação também consistente.<br><br>A correspondência de expressão regular é aplicada após alterações ortográficas. <br><br>Se a expressão regular for complexa demais, como usar muitos colchetes, você não poderá adicioná-la ao modelo. <br><br>**Exemplo**<br>`kb[0-9]{6,}` faz a correspondência com kb123456.<br/><br/>[Início rápido](luis-quickstart-intents-regex-entity.md)<br>[Exemplo de resposta para entidade](luis-concept-data-extraction.md)|
| **Simples** <br/>[Aprendizado de máquina](#machine-learned) | ✔ | **Definição**<br>Uma entidade simples é uma entidade genérica que descreve um único conceito e é aprendida do contexto de aprendizado de máquina. O contexto inclui a escolha de palavras, o posicionamento de palavras e o comprimento da declaração.<br/><br/>Isso é uma boa entidade para palavras ou frases sem formatação consistente, mas que indicam a mesma coisa. <br/><br/>[Início rápido](luis-quickstart-primary-and-secondary-data.md)<br/>[Exemplo de resposta para entidade](luis-concept-data-extraction.md#simple-entity-data)|  
| **Lista** <br/>[Correspondência exata](#exact-match)|| **Definição**<br>As entidades de lista representam um conjunto fixo e fechado de palavras relacionadas juntamente com seus sinônimos em seu sistema. <br><br>Cada entidade de lista pode ter um ou mais formulários. Usadas melhor para um conjunto conhecido de variações de maneiras que representam o mesmo conceito.<br/><br/>O LUIS não descobre valores adicionais para entidades de lista. Use o recurso **Recomendado** para consultar sugestões de novas palavras com base na lista atual.<br/><br>Se houver mais de uma entidade de lista com o mesmo valor, cada entidade será retornada na consulta de ponto de extremidade. <br/><br/>[Início rápido](luis-quickstart-intent-and-list-entity.md)<br>[Exemplo de resposta para entidade](luis-concept-data-extraction.md#list-entity-data)| 
| **Pattern.any** <br/>[Misto](#mixed) | ✔|**Definição**<br>Patterns.any é um espaço reservado de comprimento variável usado somente na declaração de modelo de um padrão para marcar onde a entidade começa e termina.  <br><br>**Exemplo**<br>Dada uma pesquisa de declaração de livros com base no título, o pattern.any extrai o título completo. Uma declaração de modelo que usa pattern.any é `Who wrote {BookTitle}[?]`.<br/><br/>[Tutorial](luis-tutorial-pattern.md)<br>[Exemplo de resposta para entidade](luis-concept-data-extraction.md#composite-entity-data)|  
| **Composição** <br/>[Aprendizado de máquina](#machine-learned) | ✔|**Definição**<br>Uma entidade composta é composta de outras entidades, como entidades pré-compiladas, simples, regex, lista, hierárquica. As entidades separadas formam uma entidade inteira. As entidades de lista não são permitidas em entidades compostas. <br><br>**Exemplo**<br>Uma entidade composta denominada PlaneTicketOrder pode ter `number` e `ToLocation` predefinidos de entidades filho. <br/><br/>[Tutorial](luis-tutorial-composite-entity.md)<br>[Exemplo de resposta para entidade](luis-concept-data-extraction.md#composite-entity-data)|  
| **Hierárquico** <br/>[Aprendizado de máquina](#machine-learned) |✔ | **Definição**<br>Uma entidade hierárquica é uma categoria de entidades simples aprendidas contextualmente.<br><br>**Exemplo**<br>Dada uma entidade hierárquica de `Location` com `ToLocation` e `FromLocation` filhos, cada filho pode ser determinado com base no **contexto** dentro da declaração. Na declaração, `Book 2 tickets from Seattle to New York`, o `ToLocation` e `FromLocation` são contextualmente diferentes com base nas palavras ao redor deles. <br/><br/>**Não use "se"**<br>Se estiver procurando uma entidade que tem correspondências exatas do texto para filhos independentemente do contexto, você deverá usar uma Entidade de lista. Se você estiver procurando uma relação pai-filho com outros tipos de entidade, deverá usar a Entidade composta.<br/><br/>[Início rápido](luis-quickstart-intent-and-hier-entity.md)<br>[Exemplo de resposta para entidade](luis-concept-data-extraction.md#hierarchical-entity-data)|

Entidades <a name="prebuilt"></a>
**predefinidas** são entidades personalizadas fornecida pelo LUIS. Algumas dessas entidades são definidas no projeto [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) de software livre. Há vários [exemplos](https://github.com/Microsoft/Recognizers-Text/tree/master/Specs) no diretório /Specs para culturas compatíveis. Se não houver suporte à sua cultura ou entidade específica, contribua para o projeto. 

Entidades <a name="machine-learned"></a>
**de aprendizado de máquina** funcionam melhor quando testadas por meio de [consultas de ponto de extremidade](luis-concept-test.md#endpoint-testing) e [do exame de declarações de ponto de extremidade](luis-how-to-review-endoint-utt.md). 

<a name="regex"></a>
**Entidades de expressão regular** são definidas por uma expressão regular que o usuário oferece como parte da definição de entidade. 

Entidades de <a name="exact-match"></a>
**correspondência exata** usam o texto fornecido na entidade para criar uma correspondência exata do texto.

Entidades <a name="mixed"></a>
**mistas** usam uma combinação de métodos de detecção de entidade.

## <a name="entity-limits"></a>Limites de entidade
Examine os [limites](luis-boundaries.md#model-boundaries) para entender a quantidade de cada tipo de entidade que você pode adicionar a um modelo.

## <a name="entity-roles"></a>Funções de entidade
As [funções](luis-concept-roles.md) são usadas somente em padrões. 

## <a name="composite-vs-hierarchical-entities"></a>Entidades compostas vs hierárquicas
Entidades compostas e hierárquicas têm relações pai-filho e se tratam de aprendizado de máquina. O aprendizado de máquina permite que o LUIS compreenda as entidades baseadas em diferentes contextos (disposição de palavras). Entidades compostas são mais flexíveis, porque permitem tipos de entidade diferente como filhos. Os filhos de uma entidade hierárquica são apenas entidades simples. 

|Tipo|Finalidade|Exemplo|
|--|--|--|
|Hierárquico|Pai-filho de entidades simples|Location.Origin=New York<br>Location.Destination=London|
|Composição|Entidades pai-filho: predefinidas, lista, simples, hierárquicas| number=3<br>list=first class<br>prebuilt.datetimeV2=March 5|

## <a name="data-matching-multiple-entities"></a>Dados que correspondem a várias entidades
Se uma palavra ou frase for correspondente a mais de uma entidade, a consulta de ponto de extremidade retornará cada entidade. Se você adicionar a entidade de número predefinida e o datetimeV2 predefinido e tiver uma declaração `create meeting on 2018/03/12 for lunch with wayne`, o LUIS reconhecerá todas as entidades e retornará uma matriz de entidades como parte da resposta do ponto de extremidade JSON: 

```JSON
{
  "query": "create meeting on 2018/03/12 for lunch with wayne",
  "topScoringIntent": {
    "intent": "Calendar.Add",
    "score": 0.9333419
  },
  "entities": [
    {
      "entity": "2018/03/12",
      "type": "builtin.datetimeV2.date",
      "startIndex": 18,
      "endIndex": 27,
      "resolution": {
        "values": [
          {
            "timex": "2018-03-12",
            "type": "date",
            "value": "2018-03-12"
          }
        ]
      }
    },
    {
      "entity": "2018",
      "type": "builtin.number",
      "startIndex": 18,
      "endIndex": 21,
      "resolution": {
        "value": "2018"
      }
    },
    {
      "entity": "03/12",
      "type": "builtin.number",
      "startIndex": 23,
      "endIndex": 27,
      "resolution": {
        "value": "0.25"
      }
    }
  ]
}
```

## <a name="data-matching-multiple-list-entities"></a>Dados que correspondem a várias entidades de lista
Se uma palavra ou frase for correspondente a mais de uma entidade de lista, a consulta de ponto de extremidade retornará cada entidade de lista.

Para a consulta `when is the best time to go to red rock?`, e o aplicativo tiver a palavra `red` em mais de uma lista, o LUIS reconhecerá todas as entidades e retornará uma matriz de entidades como parte da resposta do ponto de extremidade JSON: 

```JSON
{
  "query": "when is the best time to go to red rock?",
  "topScoringIntent": {
    "intent": "Calendar.Find",
    "score": 0.06701678
  },
  "entities": [
    {
      "entity": "red",
      "type": "Colors",
      "startIndex": 31,
      "endIndex": 33,
      "resolution": {
        "values": [
          "Red"
        ]
      }
    },
    {
      "entity": "red rock",
      "type": "Cities",
      "startIndex": 31,
      "endIndex": 38,
      "resolution": {
        "values": [
          "Destinations"
        ]
      }
    }
  ]
}
```

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Se você precisar de mais do que o número máximo de entidades 

Você precisará usar entidades compostas e hierárquicas. As entidades hierárquicas refletem a relação entre entidades que compartilham características ou que são membros de uma categoria. Todas as entidades filho são membros da categoria de seu pai. Por exemplo, uma entidade hierárquica denominada PlaneTicketClass pode ter as entidades filho EconomyClass e FirstClass. A hierarquia abrange apenas um nível de profundidade.  

As entidades compostas representam partes de um todo. Por exemplo, uma entidade composta denominada PlaneTicketOrder pode ter as entidades filho Airline, Destination, DepartureCity, DepartureDate e PlaneTicketClass. Você cria uma entidade composta de entidades simples pré-existentes, filhos de entidades hierárquicas ou entidades predefinidas.  

O LUIS também oferece o tipo de entidade de lista que não se trata de aprendizado de máquina, mas permite que seu aplicativo LUIS especifique uma lista de valores fixa. Confira a referência [Limites do LUIS](luis-boundaries.md) para examinar os limites do tipo de entidade de lista. 

Se você considerou as entidades hierárquicas, compostas e de lista e ainda precisa de mais do que o limite, contate o suporte. Para fazer isso, colete informações detalhadas sobre o sistema, acesse o site do [LUIS](luis-reference-regions.md#luis-website) e, em seguida, selecione **Suporte**. Se a assinatura do Azure incluir serviços de suporte, contate o [suporte técnico do Azure](https://azure.microsoft.com/support/options/). 

## <a name="best-practices"></a>Práticas recomendadas

Crie uma [entidade](luis-concept-entity-types.md) quando o aplicativo de chamada ou bot precisa de alguns parâmetros ou de dados da declaração necessária para executar uma ação. Uma entidade é uma palavra ou frase na declaração que você precisa extrair – talvez como um parâmetro para uma função. 

Para selecionar o tipo de entidade correto a ser adicionado ao seu aplicativo, é necessário saber como esses dados são inseridos pelos usuários. Cada tipo de entidade é encontrado usando um mecanismo diferente, como aprendizado de máquina, lista fechada ou expressão regular. Se você não tiver certeza, comece com uma entidade simples e rotule a palavra ou frase que representa esses dados em todas as declarações, entre todas as intenções, incluindo a intenção None.  

Examine declarações de ponto de extremidade regularmente para encontrar o uso comum em que uma entidade pode ser identificada como uma expressão regular ou encontrada com uma correspondência exata do texto.  

Como parte do exame, considere adicionar uma lista de frase para adicionar um sinal ao LUIS para palavras ou frases significativas para seu domínio, mas não são correspondências exatas, e para as quais o LUIS não tem uma alta confiabilidade.  

Confira as [melhores práticas](luis-concept-best-practices.md) para obter mais informações.

## <a name="next-steps"></a>Próximas etapas

Aprenda os conceitos sobre [declarações](luis-concept-utterance.md) boas. 

Confira [Adicionar entidades](luis-how-to-add-entities.md) para saber como adicionar entidades ao seu aplicativo LUIS.