---
title: Método de Tradução de API de Tradução de Texto
titleSuffix: Azure Cognitive Services
description: Usar o método de Tradução de API de Tradução de Texto.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 1841730a39d29c5fe1f3451b7614818e924b339f
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46128750"
---
# <a name="translator-text-api-30-translate"></a>API de Tradução de Texto 3.0: tradução

Traduz texto.

## <a name="request-url"></a>URL de Solicitação

Envie uma solicitação `POST` para:

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>Parâmetros da solicitação

Os parâmetros de solicitação passados na cadeia de caracteres de consulta são:

<table width="100%">
  <th width="20%">Parâmetro de consulta</th>
  <th>DESCRIÇÃO</th>
  <tr>
    <td>api-version</td>
    <td>*Parâmetro obrigatório*.<br/>Versão da API solicitada pelo cliente. O valor precisa ser `3.0`.</td>
  </tr>
  <tr>
    <td>de</td>
    <td>*Parâmetro opcional*.<br/>Especifica o idioma do texto de entrada. Localize quais idiomas estão disponíveis para tradução, consultando [idiomas com suporte](.\v3-0-languages.md) usando o escopo `translation`. Se o parâmetro `from` não for especificado, a detecção automática de idioma será aplicada para determinar o idioma de origem.</td>
  </tr>
  <tr>
    <td>para</td>
    <td>*Parâmetro obrigatório*.<br/>Especifica o idioma do texto de saída. O idioma de destino deve ser um dos [idiomas com suporte](.\v3-0-languages.md) incluídos no escopo `translation`. Por exemplo, use `to=de` para traduzir para alemão.<br/>É possível traduzir para vários idiomas simultaneamente, repetindo o parâmetro na cadeia de caracteres de consulta. Por exemplo, use `to=de&to=it` para traduzir para alemão e italiano.</td>
  </tr>
  <tr>
    <td>textType</td>
    <td>*Parâmetro opcional*.<br/>Define se o texto que está sendo traduzido é texto sem formatação ou texto HTML. Qualquer HTML precisa ser um elemento bem formado e completo. Os valores possíveis são: `plain` (padrão) ou `html`.</td>
  </tr>
  <tr>
    <td>categoria</td>
    <td>*Parâmetro opcional*.<br/>Uma cadeia de caracteres especificando a categoria (domínio) da tradução. Esse parâmetro é usado para obter traduções de um sistema personalizado compilado com [Tradutor Personalizado](../customization.md). O valor padrão é: `general`.</td>
  </tr>
  <tr>
    <td>profanityAction</td>
    <td>*Parâmetro opcional*.<br/>Especifica como os conteúdos ofensivos devem ser tratados nas traduções. Os valores possíveis são: `NoAction` (padrão), `Marked` ou `Deleted`. Para reconhecer maneiras de tratar conteúdo ofensivo, consulte [Tratamento de conteúdo ofensivo](#handle-profanity).</td>
  </tr>
  <tr>
    <td>profanityMarker</td>
    <td>*Parâmetro opcional*.<br/>Especifica como os conteúdos ofensivos devem ser marcados nas traduções. Os valores possíveis são: `Asterisk` (padrão) ou `Tag`. Para reconhecer maneiras de tratar conteúdo ofensivo, consulte [Tratamento de conteúdo ofensivo](#handle-profanity).</td>
  </tr>
  <tr>
    <td>includeAlignment</td>
    <td>*Parâmetro opcional*.<br/>Especifica se deve incluir a projeção de alinhamento do texto de origem para texto traduzido. Os valores possíveis são: `true` ou `false` (padrão). </td>
  </tr>
  <tr>
    <td>includeSentenceLength</td>
    <td>*Parâmetro opcional*.<br/>Especifica se deve incluir limites de sentença para o texto de entrada e o texto traduzido. Os valores possíveis são: `true` ou `false` (padrão).</td>
  </tr>
  <tr>
    <td>suggestedFrom</td>
    <td>*Parâmetro opcional*.<br/>Especifica um idioma de fallback se o idioma do texto de entrada não pode ser identificado. A detecção automática de idioma é aplicada quando o parâmetro `from` é omitido. Se a detecção falhar, será assumido o idioma `suggestedFrom`.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*Parâmetro opcional*.<br/>Especifica o script do texto de entrada.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*Parâmetro opcional*.<br/>Especifica o script do texto traduzido.</td>
  </tr>
</table> 

Os cabeçalhos de solicitação incluem:

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>DESCRIÇÃO</th>
  <tr>
    <td>_Uma autorização_<br/>_cabeçalho_</td>
    <td>*Cabeçalho de solicitação obrigatório*.<br/>Veja [Opções disponíveis para autenticação](./v3-0-reference.md#authentication).</td>
  </tr>
  <tr>
    <td>Tipo de conteúdo</td>
    <td>*Cabeçalho de solicitação obrigatório*.<br/>Especifica o tipo de conteúdo da carga. Os valores possíveis são: `application/json`.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>*Cabeçalho de solicitação obrigatório*.<br/>O tamanho do corpo da solicitação.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Opcional*.<br/>Um GUID gerado pelo cliente para identificar exclusivamente a solicitação. É possível omitir esse cabeçalho se incluir a ID de rastreamento na cadeia de caracteres de consulta usando um parâmetro de consulta nomeado `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Corpo da solicitação

O corpo da solicitação é uma matriz JSON. Cada elemento da matriz é um objeto JSON com uma propriedade de cadeia nomeada `Text`, que representa a cadeia de caracteres a ser traduzida.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

As seguintes limitações se aplicam:

* A matriz pode ter no máximo 25 elementos.
* Todo o texto incluído na solicitação não pode exceder 5.000 caracteres incluindo espaços.

## <a name="response-body"></a>Corpo da resposta

Uma resposta com êxito é uma matriz JSON com um resultado para cada cadeia de caracteres na matriz de entrada. Um objeto de resultado inclui as seguintes propriedades:

  * `detectedLanguage`: um objeto que descreve o idioma detectado por meio das seguintes propriedades:

      * `language`: uma cadeia de caracteres que representa o código do idioma detectado.

      * `score`: um valor flutuante indicando a confiança no resultado. A pontuação é entre zero e um, e uma pontuação baixa indica uma baixa confiança.

    A propriedade `detectedLanguage` somente está presente no objeto de resultado quando a detecção automática de idioma é solicitada.

  * `translations`: uma matriz de resultados de tradução. O tamanho da matriz corresponde ao número de idiomas de destino especificados por meio do parâmetro de consulta `to`. Cada elemento na matriz inclui:

    * `to`: uma cadeia de caracteres representando o código de idioma do idioma de destino.

    * `text`: uma cadeia de caracteres dando o texto traduzido.

    * `transliteration`: um objeto que fornece o texto traduzido no script especificado pelo parâmetro `toScript`.

      * `script`: uma cadeia de caracteres especificando o script de destino.   

      * `text`: uma cadeia de caracteres que fornece o texto traduzido no script de destino.

    O objeto `transliteration` não será incluído se a transliteração não ocorrer.

    * `alignment`: um objeto com uma única propriedade de cadeia de caracteres nomeada `proj`, que mapeia o texto de entrada para o texto traduzido. As informações de alinhamento serão fornecidas somente quando o parâmetro de solicitação `includeAlignment` for `true`. O alinhamento é retornado como um valor de cadeia de caracteres do seguinte formato: `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]`.  Os dois-pontos separam o índice inicial e final, o traço separa os idiomas e o espaço separa as palavras. Uma palavra pode alinhar com zero, uma, ou várias palavras em outro idioma e, as palavras alinhadas podem ser não contíguas. Quando nenhuma informação de alinhamento estiver disponível, o elemento de alinhamento ficará vazio. Consulte [Obter informações de alinhamento](#obtain-alignment-information) para um exemplo e restrições.

    * `sentLen`: um objeto retornando limites de sentença nos textos de entrada e saída.

      * `srcSentLen`: uma matriz inteira representando os comprimentos das sentenças no texto de entrada. O comprimento da matriz é o número de sentenças, e os valores são o comprimento de cada sentença.

      * `transSentLen`:  uma matriz inteira representando os comprimentos das sentenças no texto traduzido. O comprimento da matriz é o número de sentenças, e os valores são o comprimento de cada sentença.

    Limites de sentença serão incluídos somente quando o parâmetro de solicitação `includeSentenceLength` for `true`.

  * `sourceText`: um objeto com uma única propriedade de cadeia de caracteres nomeada `text`, que fornece o texto de entrada no script padrão do idioma de origem. `sourceText` a propriedade está presente apenas quando a entrada é expressa em um script que não é o script usual para o idioma. Por exemplo, se a entrada fosse árabe gravada em script de latim, `sourceText.text` seria o mesmo texto árabe convertido em script de árabe.

Exemplos de respostas JSON são fornecidos na seção [exemplos](#examples).

## <a name="response-status-codes"></a>Códigos de status de resposta

Veja a seguir os possíveis códigos de status HTTP retornados por uma solicitação. 

<table width="100%">
  <th width="20%">Código de status</th>
  <th>DESCRIÇÃO</th>
  <tr>
    <td>200</td>
    <td>Sucesso.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Um dos parâmetros de consulta está ausente ou é inválido. Corrija os parâmetros de solicitação antes de tentar novamente.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>A solicitação não pôde ser autenticada. Verifique se as credenciais estão especificadas e são válidas.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>A solicitação não foi autorizada. Verifique os detalhes da mensagem de erro. Isso geralmente indica que todas as traduções gratuitas fornecidas com uma assinatura de avaliação foram consumidas.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>O chamador está enviando muitas solicitações.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Erro inesperado. Se o erro persistir, relate-o com: data e hora da falha, identificador da solicitação do cabeçalho de resposta `X-RequestId` e identificador do cliente do cabeçalho de solicitação `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Servidor temporariamente não disponível. Tente novamente a solicitação. Se o erro persistir, relate-o com: data e hora da falha, identificador da solicitação do cabeçalho de resposta `X-RequestId` e identificador do cliente do cabeçalho de solicitação `X-ClientTraceId`.</td>
  </tr>
</table> 

## <a name="examples"></a>Exemplos

### <a name="translate-a-single-input"></a>Traduzir uma única entrada

Este exemplo mostra como traduzir uma única sentença de inglês para chinês simplificado.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

O corpo da resposta é:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

A matriz `translations` inclui um elemento, que fornece a tradução da única parte do texto na entrada.

### <a name="translate-a-single-input-with-language-auto-detection"></a>Traduzir uma entrada única com detecção automática de idioma

Este exemplo mostra como traduzir uma única sentença de inglês para chinês simplificado. A solicitação não especifica o idioma de entrada. Em vez disso, é usada a detecção automática do idioma de origem.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

O corpo da resposta é:

```
[
    {
        "detectedLanguage": {"language": "en", "score": 1.0},
        "translations":[
            {"text": "你好, 你叫什么名字？", "to": "zh-Hans"}
        ]
    }
]
```
A resposta é semelhante à resposta do exemplo anterior. Como a detecção automática de idioma foi solicitada, a resposta também incluirá informações sobre o idioma detectado para o texto de entrada. 

### <a name="translate-with-transliteration"></a>Traduzir com transliteração

Vamos estender o exemplo anterior, adicionando transliteração. A solicitação a seguir pede uma tradução de chinês gravada em script de latim.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

O corpo da resposta é:

```
[
    {
        "detectedLanguage":{"language":"en","score":1.0},
        "translations":[
            {
                "text":"你好, 你叫什么名字？",
                "transliteration":{"text":"nǐ hǎo , nǐ jiào shén me míng zì ？","script":"Latn"},
                "to":"zh-Hans"
            }
        ]
    }
]
```

O resultado da tradução agora inclui uma propriedade `transliteration` que fornece o texto traduzido usando caracteres de latim.

### <a name="translate-multiple-pieces-of-text"></a>Traduzir várias partes de texto

Traduzir várias cadeias de caracteres de uma vez é simplesmente uma questão de especificar uma matriz de cadeia de caracteres no corpo da solicitação.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

---

O corpo da resposta é:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    },            
    {
        "translations":[
            {"text":"我很好，谢谢你。","to":"zh-Hans"}
        ]
    }
]
```

### <a name="translate-to-multiple-languages"></a>Traduzir para vários idiomas

Este exemplo mostra como traduzir a mesma entrada para vários idiomas em uma solicitação.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

O corpo da resposta é:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"},
            {"text":"Hallo, was ist dein Name?","to":"de"}
        ]
    }
]
```

### <a name="handle-profanity"></a>Tratar conteúdo ofensivo

Normalmente, o serviço do Translator reterá a linguagem vulgar presente no texto de origem da tradução. O grau de vulgaridade e o contexto que torna palavras vulgar diferem entre as culturas, assim, o grau de vulgaridade no idioma de destino pode ser amplificado ou reduzido.

Se você quiser evitar conteúdo ofensivo na tradução, independentemente da presença de linguagem vulgar no texto de origem, poderá usar a opção de filtragem de linguagem vulgar. A opção permite escolher se você quer ver o conteúdo ofensivo excluído, se quer marcar conteúdos ofensivos com marcas apropriadas (dando a opção de adicionar seu próprio pós-processamento), ou se não quer executar nenhuma ação. Os valores aceitos de `ProfanityAction` são `Deleted`, `Marked` e `NoAction` (padrão).

<table width="100%">
  <th width="20%">ProfanityAction</th>
  <th>Ação</th>
  <tr>
    <td>`NoAction`</td>
    <td>Esse é o comportamento padrão. O conteúdo ofensivo passará da origem para o destino.<br/><br/>
    **Origem de exemplo (japonês)**: 彼はジャッカスです。<br/>
    **Tradução de exemplo (inglês)**: Ele é um idiota.
    </td>
  </tr>
  <tr>
    <td>`Deleted`</td>
    <td>Palavras ofensivas serão removidas da saída sem substituição.<br/><br/>
    **Origem de exemplo (japonês)**: 彼はジャッカスです。<br/>
    **Tradução de exemplo (inglês)**: Ele é um.
    </td>
  </tr>
  <tr>
    <td>`Marked`</td>
    <td>Palavras ofensivas são substituídas por um marcador na saída. O marcador depende do parâmetro `ProfanityMarker`.<br/><br/>
Para `ProfanityMarker=Asterisk`, palavras ofensivas são substituídas por `***`:<br/>
    **Origem de exemplo (japonês)**: 彼はジャッカスです。<br/>
    **Tradução de exemplo**: Ele é um \*\*\*.<br/><br/>
Para `ProfanityMarker=Tag`, palavras ofensivas são circundadas por marcas XML &lt;profanity&gt; e &lt;/profanity&gt;:<br/>
    **Origem de exemplo (japonês)**: 彼はジャッカスです。<br/>
    **Exemplo de tradução (inglês)**: Ele é um &lt;profanity&gt;idiota&lt;/profanity&gt;.
  </tr>
</table> 

Por exemplo: 

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'This is a fucking good idea.'}]"
```

---

Isso retorna:

```
[
    {
        "translations":[
            {"text":"Das ist eine *** gute Idee.","to":"de"}
        ]
    }
]
```

Compara com:

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked&profanityMarker=Tag" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'This is a fucking good idea.'}]"
```

---

Essa última solicitação retorna:

```
[
    {
        "translations":[
            {"text":"Das ist eine <profanity>verdammt</profanity> gute Idee.","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Traduzir conteúdo com marcação e decidir o que é traduzido

É comum traduzir conteúdo que inclui marcação, como conteúdo de uma página HTML ou conteúdo de um documento XML. Inclua o parâmetro de consulta `textType=html` ao traduzir conteúdo com marcas. Além disso, às vezes é útil excluir conteúdo específico da tradução. É possível usar o atributo `class=notranslate` para especificar o conteúdo que deve permanecer no idioma original. No exemplo a seguir, o conteúdo dentro do primeiro elemento `div` não será traduzido, enquanto o conteúdo do segundo elemento `div` será traduzido.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Aqui, está uma solicitação de exemplo para ilustrar.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

---

A resposta é:

```
[
    {
        "translations":[
            {"text":"<div class=\"notranslate\">This will not be translated.</div><div>这将被翻译。</div>","to":"zh-Hans"}
        ]
    }
]
```

### <a name="obtain-alignment-information"></a>Obter informações de alinhamento

Para receber informações de alinhamento, especifique `includeAlignment=true` na cadeia de caracteres de consulta.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeAlignment=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'The answer lies in machine translation.'}]"
```

---

A resposta é:

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique.",
                "to":"fr",
                "alignment":{"proj":"0:2-0:1 4:9-3:9 11:14-11:19 16:17-21:24 19:25-40:50 27:37-29:38 38:38-51:51"}
            }
        ]
    }
]
```

As informações de alinhamento iniciam com `0:2-0:1`, significando que os três primeiros caracteres no texto de origem (`The`) são mapeados para os dois primeiros caracteres no texto traduzido (`La`).

Observe as seguintes restrições:

* O alinhamento é retornado apenas para um subconjunto dos pares de idiomas:
  - do inglês para qualquer outro idioma;
  - de qualquer outro idioma para o inglês, exceto chinês simplificado, chinês tradicional e letão para inglês;
  - do japonês para o coreano ou do coreano para o japonês.
* Você não receberá alinhamento se a sentença for uma tradução predefinida. Exemplo de uma tradução predefinida é "Isso é um teste", "eu te amo", e outras frases de alta frequência.

### <a name="obtain-sentence-boundaries"></a>Obter limites de sentença

Para receber informações sobre o comprimento da sentença no texto de origem e no texto traduzido, especifique `includeSentenceLength=true` na cadeia de caracteres de consulta.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeSentenceLength=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'The answer lies in machine translation. The best machine translation technology cannot always provide translations tailored to a site or users like a human. Simply copy and paste a code snippet anywhere.'}]"
```

---

A resposta é:

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique. La meilleure technologie de traduction automatique ne peut pas toujours fournir des traductions adaptées à un site ou des utilisateurs comme un être humain. Il suffit de copier et coller un extrait de code n’importe où.",
                "to":"fr",
                "sentLen":{"srcSentLen":[40,117,46],"transSentLen":[53,157,62]}
            }
        ]
    }
]
```

### <a name="translate-with-dynamic-dictionary"></a>Traduzir com dicionário dinâmico

Se você já souber a tradução que deseja aplicar a uma palavra ou frase, poderá fornecê-la como marcação dentro da solicitação. O dicionário dinâmico é seguro somente para substantivos compostos como nomes próprios e nomes de produtos.

A marcação para fornecer usa a seguinte sintaxe.

``` 
<mstrans:dictionary translation=”translation of phrase”>phrase</mstrans:dictionary>
```

Por exemplo, considere a frase em inglês "A palavra wordomatic é uma entrada de dicionário." Para preservar a palavra _wordomatic_ na tradução, envie a solicitação:

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

O resultado é:

```
[
    {
        "translations":[
            {"text":"Das Wort "wordomatic" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

Esse recurso funciona da mesma maneira com `textType=text` ou com `textType=html`. O recurso deve ser usado com moderação. A maneira apropriada e muito melhor de personalizar a tradução é usando o Tradutor Personalizado. O Tradutor Personalizado faz uso total das probabilidades de estatística e contexto. Se você tiver ou puder criar dados de treinamento que mostrem o trabalho ou a frase no contexto, obterá resultados muito melhores. [Saiba mais sobre o Tradutor Personalizado](../customization.md).
 





