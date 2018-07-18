---
title: Machine Learning – Serviços Cognitivos do Azure | Microsoft Docs
description: Um tutorial para o aprendizado de máquina no Serviço de Decisão Personalizada do Azure, uma API baseada em nuvem para a tomada de decisão contextual.
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/08/2018
ms.author: slivkins;marcozo;alekh
ms.openlocfilehash: 50814d67ee39c6657954610358462d877843416e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364423"
---
# <a name="machine-learning"></a>Aprendizado de máquina

Este tutorial aborda a funcionalidade avançada de aprendizado de máquina no Serviço de Decisão Personalizada. O tutorial consiste em duas partes: [personalização](#featurization-concepts-and-implementation) e [especificação de recurso](#feature-specification-format-and-apis). Personalização refere-se à representação dos dados como "recursos" para o aprendizado de máquina. A especificação de recurso abrange o formato JSON e as APIs auxiliares para especificar os recursos.

Por padrão, o aprendizado de máquina no Serviço de Decisão Personalizada é transparente para o cliente. Os recursos são extraídos automaticamente do conteúdo, e um algoritmo de aprendizado de reforço padrão é usado. A extração de recurso aproveita vários outros Serviços Cognitivos do Azure: [Vinculação de Entidade](../entitylinking/home.md), [Análise de Texto](../text-analytics/overview.md), [Detecção de Emoções](../emotion/home.md) e [Pesquisa Visual Computacional](../computer-vision/home.md). Este tutorial pode ser ignorado apenas se a funcionalidade padrão é usada.

## <a name="featurization-concepts-and-implementation"></a>Personalização: conceitos e implementação

O Serviço de Decisão Personalizada toma as decisões uma por vez. Cada decisão envolve a escolha entre várias alternativas, também conhecidas como ações. Dependendo do aplicativo, a decisão pode consistir em uma única ação ou uma lista classificada (curta) de ações.

Por exemplo, a personalização da seleção de artigos na página frontal de um site. Aqui, as ações correspondem aos artigos, e cada decisão consiste em definir quais artigos serão mostrados a determinado usuário.

Cada ação é representada por um vetor de propriedades, daqui em diante denominadas *recursos*. Você pode especificar novos recursos, além dos recursos extraídos automaticamente. Você também pode instruir o Serviço de Decisão Personalizada a registrar alguns recursos em log, mas ignorá-los para o aprendizado de máquina.

### <a name="native-vs-internal-features"></a>Recursos nativos versus internos

Você pode especificar os recursos em um formato mais natural para seu aplicativo, seja ele um número, uma cadeia de caracteres ou uma matriz. Esses recursos são chamados de "recursos nativos". O Serviço de Decisão Personalizada converte cada recurso nativo em um ou mais recursos numéricos, chamados "recursos internos".

A conversão em recursos internos é a seguinte:

- os recursos numéricos permanecem os mesmos.
- uma matriz numérica é convertida em vários recursos numéricos, um para cada elemento da matriz.
- um recurso `"Name":"Value"` com valor de cadeia de caracteres, por padrão, convertido em um recurso com o nome `"NameValue"` e o valor 1.
- Opcionalmente, uma cadeia de caracteres pode ser representada como um [recipiente de palavras](https://en.wikipedia.org/wiki/Bag-of-words_model). Em seguida, um recurso interno é criado para cada palavra na cadeia de caracteres, cujo valor é o número de ocorrências dessa palavra.
- Os recursos internos com valor zero são omitidos.

### <a name="shared-vs-action-dependent-features"></a>Recursos compartilhados vs. dependentes de ação

Alguns recursos se referem à toda a decisão e são os mesmos para todas as ações. Nós os chamamos de *recursos compartilhados*. Alguns outros recursos são específicos a determinada ação. Nós os chamamos de *ADFs* (recursos dependentes de ação).

No exemplo em execução, os recursos compartilhados podem descrevem o usuário e/ou o estado do mundo. Recursos como geolocalização, idade e gênero do usuário, além de quais eventos principais estão ocorrendo no momento. Os recursos dependentes de ação podem descrever as propriedades de determinado artigo, como os tópicos abordados por este artigo.

### <a name="interacting-features"></a>Interação de recursos

Com frequência, os recursos "interagem": o efeito de um depende de outros. Por exemplo, o recurso X indica se o usuário está interessado em esportes. O recurso Y indica se determinado artigo refere-se a esportes. Assim, o efeito do recurso Y é altamente dependente do recurso X.

Para levar em conta de interação entre os recursos X e Y, crie um recurso *quadrático* cujo valor é X\*Y. (Também dizemos X e Y "cruzado".) Escolha quais pares de recursos são cruzados.

> [!TIP]
> Um recurso compartilhado deve ser cruzado com recursos dependentes de ação para influenciar sua classificação. Um recurso dependente de ação deve ser cruzado com recursos compartilhados para contribuir para a personalização.

Em outras palavras, um recurso compartilhado não cruzado com nenhum ADF influencia cada ação da mesma maneira. Um ADF não cruzado com qualquer recurso compartilhado influencia também cada decisão. Esses tipos de recursos podem reduzir a variação das estimativas de recompensa.

### <a name="implementation-via-namespaces"></a>Implementação por meio de namespaces

Você pode implementar recursos cruzados (bem como outros conceitos de personalização) por meio da "linha de comando VW" no Portal. A sintaxe baseia na linha de comando [Vowpal Wabbit](http://hunch.net/~vw/).

Um aspecto central da implementação é o conceito de *namespace*: um subconjunto nomeado de recursos. Cada recurso pertence a exatamente um namespace. O namespace pode ser especificado explicitamente, quando o valor de recurso é fornecido para o Serviço de Decisão Personalizada. Essa é a única maneira de referenciar um recurso na linha de comando VW.

Um namespace é "compartilhado" ou "dependente de ação": ele consiste apenas em recursos compartilhados ou apenas em recursos dependentes de ação da mesma ação.

> [!TIP]
> É uma boa prática encapsular os recursos nos namespaces explicitamente especificados. Agrupe recursos relacionados no mesmo namespace.

Se o namespace não for fornecido, o recurso será atribuído automaticamente ao namespace padrão.

> [!IMPORTANT]
> Os recursos e os namespaces não precisam ser consistentes nas ações. Em particular, um namespace pode ter diferentes recursos para diferentes ações. Além disso, um namespace específico pode ser definido para algumas ações e não para outras.

Vários recursos internos que vieram com o mesmo recurso nativo com valor de cadeia de caracteres são agrupados no mesmo namespace. Dois recursos nativos quaisquer que residem em namespaces diferentes são tratados como distintos, mesmo se têm o mesmo nome de recurso.

> [!IMPORTANT]
> Embora as IDs de namespace longas e descritivas sejam comuns, a linha de comando VW não faz distinção entre namespaces cuja ID começa com a mesma letra. A seguir, as IDs de namespace são letras individuais, como `x` e `y`.

Os detalhes de implementação são os seguintes:

- Para cruzar os namespaces `x` e `y`, escreva `-q xy` ou `--quadratic xy`. Em seguida, cada recurso em `x` é cruzado com cada recurso em `y`. Use `-q x:` para cruzar `x` com cada namespace e `-q ::` para cruzar todos os pares de namespaces.

- Para ignorar todos os recursos no namespace `x`, escreva `--ignore x`.

Esses comandos são aplicados a cada ação separadamente, sempre que os namespaces são definidos.

### <a name="estimated-average-as-a-feature"></a>Média estimada como um recurso

Como um experimento conceitual, qual seria a recompensa média de determinada ação se ela fosse escolhida para todas as decisões? Essa recompensa média pode ser usada como uma medida da "qualidade geral" dessa ação. Não se sabe exatamente quando outras ações foram escolhidas em algumas decisões. No entanto, isso pode ser estimado por meio de técnicas de aprendizado de reforço. A qualidade dessa estimativa normalmente melhora com o tempo.

Você pode optar por incluir essa "recompensa média estimada" como um recurso para determinada ação. Em seguida, o Serviço de Decisão Personalizada atualizará essa estimativa automaticamente conforme novos dados são recebidos. Esse recurso é chamado de o *recurso marginal* dessa ação. Os recursos marginais podem ser usados para aprendizado de máquina e auditoria.

Para adicionar recursos marginais, escreva `--marginal <namespace>` na linha de comando VW. Defina `<namespace>` em JSON da seguinte maneira:

```json
{<namespace>: {"mf_name":1 "action_id":1}
```

Insira esse namespace juntamente com outros recursos dependentes de ação de uma ação específica. Forneça essa definição para cada decisão, usando o mesmo `mf_name` e a mesma `action_id` para todas as decisões.

O recurso marginal é adicionado a cada ação com `<namespace>`. A `action_id` pode ser qualquer nome de recurso que identifique exclusivamente a ação. O nome do recurso é definido como `mf_name`. Em particular, os recursos marginais com `mf_name` diferentes são tratados como recursos diferentes – um peso diferente é aprendido para cada `mf_name`.

O uso padrão é que o `mf_name` é o mesmo para todas as ações. Em seguida, um peso é aprendido para todos os recursos marginais.

Você também pode especificar vários recursos marginais para a mesma ação, com os mesmos valores, mas nomes de recurso diferentes.

```json
{<namespace>: {"mf_name1":1 "action_id":1 "mf_name2":1 "action_id":1}}
```

### <a name="1-hot-encoding"></a>Codificação 1-hot

Você pode optar por representar alguns recursos como vetores de bits, em que cada bit corresponde a um intervalo de valores possíveis. Esse bit é definido como 1 se e somente se o recurso está nesse intervalo. Portanto, há um bit "ativo" que é definido como 1 e os outros são definidos como 0. Essa representação é geralmente conhecida como *codificação 1-hot*.

A codificação 1-hot é típica para recursos categóricos, como uma "região geográfica", que não têm uma representação numérica inerentemente significativa. Ela também é recomendável para recursos numéricos cuja influência sobre a recompensa provavelmente não será linear. Por exemplo, um artigo específico pode ser relevante para determinada faixa etária e irrelevante para qualquer pessoa mais velha ou mais nova.

Qualquer recurso com valor de cadeia de caracteres é codificado em 1-hot por padrão: um recurso interno distinto é criado para cada valor possível. Atualmente, a codificação 1-hot automática para recursos numéricos e/ou intervalos personalizados não é fornecida.

> [!TIP]
> Os algoritmos de aprendizado de máquina tratam todos os valores possíveis de determinado recurso interno de uma maneira uniforme: por meio de um "peso" comum. A codificação 1-hot permite um "peso" separado para cada intervalo de valores. A redução dos intervalos leva a melhores recompensas quando dados suficientes são coletados, mas pode aumentar a quantidade de dados necessários para convergir para melhores recompensas.

## <a name="feature-specification-format-and-apis"></a>Especificação de recurso: formato e APIs

Especifique recursos por meio de várias APIs auxiliares. Todas as APIs usam um formato JSON comum. Abaixo, são mostradas as APIs e o formato em um nível conceitual. A especificação é complementada por um esquema do Swagger.

O modelo básico de JSON para a especificação de recurso é o seguinte:

```json
{
"<name>":<value>, "<name>":<value>, ... ,
"namespace1": {"<name>":<value>, ... },
"namespace2": {"<name>":<value>, ... },
...
}
```

Aqui, `<name>` e `<value>` referem-se ao nome do recurso e ao valor do recurso, respectivamente. `<value>` pode ser uma cadeia de caracteres, um inteiro, um float, um booliano ou uma matriz. Um recurso não encapsulado em um namespace é atribuído automaticamente ao namespace padrão.

Para representar uma cadeia de caracteres como um recipiente de palavras, use uma sintaxe especial `"_text":"string"` em vez de `"<name>":<value>`. Na verdade, um recurso interno separado é criado para cada palavra na cadeia de caracteres. Seu valor é o número de ocorrências dessa palavra.

Se `<name>` começa com "_" (e não é `"_text"`), o recurso é ignorado.

> [!TIP]
> Às vezes, você mescla recursos de várias fontes JSON. Para sua conveniência, você pode representá-los da seguinte maneira:
>
> ```json
> {
> "source1":<features>,
> "source2":<features>,
> ...
> }
> ```

Aqui, `<features>` refere-se à especificação básica de recurso definida anteriormente. Níveis mais profundos de "aninhamento" também são permitidos. O Serviço de Decisão Personalizada encontra automaticamente os objetos JSON "mais profundos" que podem ser interpretados como `<features>`.

#### <a name="feature-set-api"></a>API de Conjunto de Recursos

A API de Conjunto de Recursos retorna uma lista de recursos no formato JSON descrito anteriormente. Você pode usar vários pontos de extremidade da API de Conjunto de Recursos. Cada ponto de extremidade é identificado pela ID do conjunto de recursos e por uma URL. O mapeamento entre as IDs do conjunto de recursos e as URLs é definido no Portal.

Chame a API de Conjunto de Recursos inserindo a ID do conjunto de recursos correspondente no local apropriado em JSON. Para os recursos dependentes de ação, a chamada é parametrizada automaticamente pela ID de ação. Você pode especificar várias IDs do conjunto de recursos para a mesma ação.

#### <a name="action-set-api-json-version"></a>API de Conjunto de Ações (versão JSON)

A API de Conjunto de Ações tem uma versão na qual as ações e os recursos são especificados em JSON. Os recursos podem ser especificados explicitamente e/ou por meio das APIs de Conjunto de Recursos. Os recursos compartilhados podem ser especificados uma vez para todas as ações.

#### <a name="ranking-api-http-post-call"></a>API de Classificação (chamada HTTP POST)

A API de Classificação tem uma versão que usa a chamada HTTP POST. O corpo dessa chamada especifica ações e recursos por meio de uma sintaxe JSON flexível.

As ações podem ser especificadas explicitamente e/ou por meio de IDs de conjunto de ações. Sempre que uma ID do conjunto de ações é encontrada, uma chamada ao ponto de extremidade da API de Conjunto de Ações correspondente é executada.

Quando à API de Conjunto de Ações, os recursos podem ser especificados explicitamente e/ou por meio das APIs de Conjunto de Recursos. Os recursos compartilhados podem ser especificados uma vez para todas as ações.