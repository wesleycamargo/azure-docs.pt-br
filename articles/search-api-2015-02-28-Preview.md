<properties 
   pageTitle="Versão da API REST do Serviço Azure Search 2015-02-28-Preview | Microsoft Azure"
   description="A API do serviço Azure Search Versão 2015-02-28-Preview inclui recursos experimentais como analisadores de linguagem natural e pesquisas do tipo moreLikeThis." 
   services="search" 
   documentationCenter="na" 
   authors="HeidiSteen" 
   manager="mblythe" 
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="rest-api"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="search" 
   ms.date="04/20/2015"
   ms.author="heidist"/>

# API REST do serviço Azure Search: Versão 2015-02-28-Preview

Este artigo é a documentação de referência para a `api-version=2015-02-28-Preview`. Essa visualização estende a atual versão bloqueada, [api-version=2015-02-28](https://msdn.microsoft.com/library/dn798935.aspx), fornecendo os seguintes recursos experimentais:

- [Processadores de idioma natural](#LanguageSupport) da Microsoft (os mesmos usadas pelo Office e pelo Bing) oferecem maior precisão em relação aos resultados de consultas e mais idiomas.
- `moreLikeThis` é um parâmetro de consulta usado em [operações de pesquisa](#SearchDocs) que localiza outros documentos relevantes para outro documento específico.

Alguns recursos adicionais da `2015-02-28-Preview` são documentados separadamente. Estão incluídos:

- [Perfis de pontuação](../search-api-scoring-profiles-2015-02-28-preview/)
- [Indexadores](../search-api-indexers-2015-02-28-Preview/)

O serviço Azure Search está disponível em várias versões. Consulte [Controle de versão de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes.

##APIs neste documento

A API do serviço Azure Search dá suporte a duas sintaxes para pesquisa de entidades: sintaxe de OData simples e alternativa (consulte [Suporte a OData (API do Azure Search)](http://msdn.microsoft.com/library/azure/dn798932.aspx) para obter detalhes). A lista a seguir mostra a sintaxe simples.

[Criar o índice](#CreateIndex)

    POST /indexes?api-version=2015-02-28-Preview

[Atualizar o índice](#UpdateIndex)

    PUT /indexes/[index name]?api-version=2015-02-28-Preview

[Obter o índice](#GetIndex)

    GET /indexes/[index name]?api-version=2015-02-28-Preview

[Listando índices](#ListIndexes)

    GET /indexes?api-version=2015-02-28-Preview

[Obter estatísticas de índice](#GetIndexStats)

    GET /indexes/[index name]/stats?api-version=2015-02-28-Preview

[Excluir um índice](#DeleteIndex)

    DELETE /indexes/[index name]?api-version=2015-02-28-Preview

[Adicionar, excluir e atualizar dados em um índice](#AddOrUpdateDocuments)

    POST /indexes/[index name]/docs/index?api-version=2015-02-28-Preview

[Pesquisar documentos](#SearchDocs)

    GET /indexes/[index name]/docs?[query parameters]

[Procurar documento](#LookupAPI)

     GET /indexes/[index name]/docs/[key]?[query parameters]

[Contar documentos](#CountDocs)

    GET /indexes/[index name]/docs/$count?api-version=2015-02-28-Preview

[Sugestões](#Suggestions)

    GET /indexes/[index name]/docs/suggest?[query parameters]

________________________________________ <a name="IndexOps"></a>
## Operações de índice

Você pode criar e gerenciar índices no serviço Azure Search por meio de solicitações HTTP simples (POST, GET, PUT, DELETE) em relação a um recurso de índice específico. Para criar um índice, primeiro execute POST para um documento JSON que descreve o esquema de índice. O esquema define os campos do índice, seus tipos de dados e como eles podem ser usados (por exemplo, em pesquisas de texto completo, filtros, classificação ou facetamento). Ele também define os perfis de pontuação, sugestores e outros atributos para configurar o comportamento do índice.

O exemplo a seguir fornece uma ilustração de um esquema usado para pesquisar informações de hotel com o campo Descrição definido em dois idiomas. Observe como os atributos controlam como o campo é usado. Por exemplo, o `hotelId` é usado como a chave do documento (`"key": true`) e é excluído de pesquisas de texto completo (`"searchable": false`).

    {
    "name": "hotels",  
    "fields": [
      {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
      {"name": "baseRate", "type": "Edm.Double"},
      {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
	  {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, analyzer: "fr.lucene"},
      {"name": "hotelName", "type": "Edm.String"},
      {"name": "category", "type": "Edm.String"},
      {"name": "tags", "type": "Collection(Edm.String)"},
      {"name": "parkingIncluded", "type": "Edm.Boolean"},
      {"name": "smokingAllowed", "type": "Edm.Boolean"},
      {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
      {"name": "rating", "type": "Edm.Int32"},
      {"name": "location", "type": "Edm.GeographyPoint"}
     ],
     "suggesters": [
      {
       "name": "sg",
       "searchMode": "analyzingInfixMatching",
       "sourceFields": ["hotelName"]
      }
     ] 
    }
 
Depois que o índice for criado, você carregará documentos que populam o índice. Consulte [Adicionar ou Atualizar Documentos](#AddOrUpdateDocuments) para a próxima etapa.

Para um vídeo de introdução à indexação no Azure Search, consulte o [episódio do Cloud Cover do Channel 9 sobre o Azure Search](http://go.microsoft.com/fwlink/p/?LinkId=511509).


<a name="CreateIndex"></a>
### Criar o índice

Um índice é o principal meio de organizar e pesquisar documentos no Azure Search, de forma semelhante a como uma tabela organiza registros em um banco de dados. Cada índice tem uma coleção de documentos que estão todos em conformidade com o esquema do índice (nomes de campos, tipos de dados e propriedades), mas os índices também especificam constructos adicionais (sugestores, perfis de pontuação e opções de CORS) que definem outros comportamentos de pesquisa.

Você pode criar um novo índice em um serviço Azure Search usando uma solicitação HTTP POST ou PUT. O corpo da solicitação é um esquema JSON que especifica as informações de configuração e de índice.

    POST https://[service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Como alternativa, você pode usar PUT e especificar o nome do índice no URI. Se o índice não existir, ele será criado.

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]

A criação de um índice determina a estrutura dos documentos armazenados e usados em operações de pesquisa. Popular o índice é uma operação separada. Nessa etapa, você pode usar um [indexador](https://msdn.microsoft.com/library/azure/mt183328.aspx) (disponível para fontes de dados com suporte) ou uma operação [Adicionar, Atualizar ou Excluir Documentos](https://msdn.microsoft.com/library/azure/dn798930.aspx). O índice invertido é gerado quando os documentos são postados.

**Observação**: o número máximo de índices permitidos varia por camada de preços. O serviço gratuito permite até três índices. O serviço padrão permite 50 índices por serviço de pesquisa. Consulte [Limites e restrições](http://msdn.microsoft.com/library/azure/dn798934.aspx) para obter detalhes.

**Solicitação**

HTTPS é necessário para todas as solicitações de serviço. A solicitação **Criar Índice** pode ser criada usando um método POST ou PUT. Ao usar POST, você deve fornecer um nome de índice no corpo da solicitação, juntamente com a definição de esquema de índice. Com PUT, o nome do índice é parte da URL. Se o índice não existir, ele será criado. Se já existir, ele será atualizado para a nova definição.

O nome do índice deve estar em letras minúsculas, começar com uma letra ou número, não conter barras ou pontos e ter menos de 128 caracteres. Depois de iniciar o nome do índice com uma letra ou número, o restante do nome pode incluir qualquer letra, número e traços, desde que os traços não sejam consecutivos.

A `api-version` é obrigatória. A versão atual é `api-version=2015-02-28`. Consulte [Controle de versão de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.

**Cabeçalhos de solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.

- `Content-Type`: obrigatório. Defina-o como `application/json`
- `api-key`: obrigatório. A `api-key` é usada para 
- autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres exclusivo de seu serviço. A solicitação **Criar Índice** deve incluir um cabeçalho de `api-key` definido como sua chave de administração (em vez de uma chave de consulta). 
 
Você também precisará do nome de serviço para criar a URL da solicitação. Você pode obter o nome do serviço e a `api-key` por meio do painel de serviço no Portal do Azure. Consulte [Criar um serviço Azure Search no portal](../search-create-service-portal/) para obter ajuda sobre a navegação na página.

<a name="RequestData"></a> **Sintaxe de corpo da solicitação**

O corpo da solicitação contém uma definição de esquema, que inclui a lista de campos de dados em documentos que serão inseridos nesse índice, tipos de dados, atributos, bem como uma lista opcional de perfis de pontuação que são usados para pontuar documentos correspondentes no momento da consulta.

Observe que, para uma solicitação POST, você deve especificar o nome do índice no corpo da solicitação.

Pode haver somente um campo de chave no índice. Ele deve ser um campo de cadeia de caracteres. Esse campo representa o identificador exclusivo para cada documento armazenado no índice.

As principais partes de um índice incluem o seguinte:

- `name`
- `fields` que serão inseridos nesse índice, incluindo nome, tipo de dados e propriedades que definem as ações permitidas nesse campo.
- `suggesters` usados para consultas de preenchimento automático ou digitação antecipada.
- `scoringProfiles` usados para classificação de pontuação de pesquisa personalizada. Consulte [Adicionar perfis de pontuação](https://msdn.microsoft.com/library/azure/dn798928.aspx) para obter detalhes.
- `defaultScoringProfile` usado para substituir os comportamentos de pontuação padrão.
- `corsOptions` para permitir consultas entre origens em relação a seu índice.

A sintaxe para estruturar a carga da solicitação é indicada a seguir. Uma solicitação de exemplo é fornecida mais adiante neste tópico.

    {
      "name": (optional on PUT; required on POST) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false,
		  "analyzer": "name of text analyzer"
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive #'s),
              ...
            }
          },
          "functions": (optional) [
            {
              "type": "magnitude | freshness | distance | tag",
              "boost": # (positive number used as multiplier for raw score != 1),
              "fieldName": "...",
              "interpolation": "constant | linear (default) | quadratic | logarithmic",
              "magnitude": {
                "boostingRangeStart": #,
                "boostingRangeEnd": #,
                "constantBoostBeyondRange": true | false (default)
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
			  "tag": {
				"tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified) 
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }    
    
**Atributos de índice**

Os atributos a seguir podem ser definidos ao criar um índice. Para obter detalhes sobre pontuação e perfis de pontuação, consulte [Adicionar perfis de pontuação](https://msdn.microsoft.com/library/azure/dn798928.aspx).

`name` ‒ define o nome do campo.

`type` ‒ define o tipo de dados do campo. Consulte [Tipos de Dados Com Suporte](#DataTypes) para obter uma lista dos tipos com suporte.

`searchable` ‒ marca o campo como texto completo pesquisável. Isso significa que ele será submetido a análise, como separação de palavras, durante a indexação. Se você definir um campo `searchable` para um valor como "dia ensolarado", internamente ele será dividido nos tokens individuais "dia" e "ensolarado". Isso habilita pesquisas de texto completo para esses termos. Os campos dos tipos `Edm.String` ou `Collection(Edm.String)` são `searchable` por padrão. Campos de outros tipos não podem ser `searchable`.

  - **Observação**: `searchable` os campos consomem espaço extra no índice, pois o Azure Search armazenará uma versão com token adicional do valor do campo para pesquisas de texto completo. Se você desejar economizar espaço no índice e não for necessário incluir um campo em pesquisas, defina `searchable` como `false`.

`filterable` ‒ permite que o campo seja referenciado em consultas `$filter`. `filterable` difere de `searchable` na maneira como as cadeias de caracteres são tratadas. Campos dos tipos `Edm.String` ou `Collection(Edm.String)` que são `filterable` não são submetidos à quebra de palavras. Portanto, as comparações são apenas para correspondências exatas. Por exemplo, se você definir um campo `f` como "dia ensolarado", `$filter=f eq 'sunny'` não encontrará correspondências, mas `$filter=f eq 'sunny day'` as encontrará. Todos os campos são `filterable` por padrão.

`sortable` ‒ por padrão o sistema classifica os resultados pela pontuação, mas, em muitas experiências, os usuários desejarão classificar por campos nos documentos. Campos do tipo `Collection(Edm.String)` não podem ser `sortable`. Todos os outros campos são `sortable` por padrão.

`facetable` ‒ geralmente usado em uma apresentação dos resultados de pesquisa que inclui a contagem de ocorrências por categoria (por exemplo, pesquisar câmeras digitais e ver as ocorrências por marca, por megapixels, por preço etc.). Essa opção não pode ser usada com campos do tipo `Edm.GeographyPoint`. Todos os outros campos são `facetable` por padrão.

  - **Observação**: campos do tipo `Edm.String` que são `filterable`, `sortable` ou `facetable` podem ter no máximo 32 KB de comprimento. Isso ocorre porque esses campos são tratados como um único termo de pesquisa, e o comprimento máximo de um termo no Azure Search é 32 KB. Se precisar armazenar mais texto do que isso em um único campo de cadeia de caracteres, você precisará definir explicitamente `filterable`, `sortable` e `facetable` como `false` na definição do índice.

  - **Observação**: se um campo não tiver nenhum dos atributos acima definidos como `true` (`searchable`, `filterable`, `sortable` ou `facetable`) o campo será efetivamente excluído do índice invertido. Essa opção é útil para campos que não são usados em consultas, mas são necessários em resultados de pesquisa. A exclusão desses campos do índice melhora o desempenho.

  - `suggestions` ‒ versões anteriores da API incluída uma `suggestions` propriedade. Essa propriedade booliana foi substituída e não está mais disponível na `2015-02-28` ou `2015-02-28-Preview`. Use a [API de sugestores](#Suggesters) em vez disso. Na versão `2014-07-31`, a propriedade `suggestions` foi usada para especificar se o campo pode ser usado para preenchimento automático para digitação antecipada, para campos do tipo `Edm.String` ou `Collection(Edm.String)`. As `suggestions` eram `false` por padrão porque era preciso espaço adicional no índice, mas se você as habilitou, consulte [Transição da visualização para a versão geral no  Azure Search](../search-transition-from-preview/) para obter instruções sobre como fazer a transição para a nova API.

`key` ‒ marca o campo como contendo identificadores exclusivos para documentos no índice. Exatamente um campo deve ser escolhido como o campo `key`, e ele deve ser do tipo `Edm.String`. Campos de chave podem ser usados para pesquisar documentos diretamente por meio da [API de pesquisa](#LookupAPI).

`retrievable` ‒ define se o campo pode ser retornado em um resultado de pesquisa. Isso é útil quando você quer usar um campo (por exemplo, margem) como mecanismo de filtro, classificação ou pontuação, mas não deseja que o campo seja visível ao usuário final. Esse atributo deve ser `true` para campos `key`.

`analyzer` ‒ define o nome do analisador de texto a ser usado para o campo. Para obter o conjunto de valores permitidos, consulte [Suporte ao Idioma](#LanguageSupport). Essa opção só pode ser usada com campos `searchable`. Depois que o analisador for escolhido, ele não poderá ser alterado para o campo.

`sugggesters` ‒ define o modo de pesquisa e campos que são a origem do conteúdo para obter sugestões. Consulte [Sugestores](#Suggesters) para obter detalhes.

`scoringProfiles` ‒ define comportamentos de pontuação personalizados que permitem influenciam quais itens aparecem em posição mais elevada nos resultados de pesquisa. Perfis de pontuação são compostos de funções e pesos de campos. Consulte [Adicionar perfis de pontuação](https://msdn.microsoft.com/library/azure/dn798928.aspx) para obter mais informações sobre os atributos usados em um perfil de pontuação.

<!-- This is a standalone topic in MSDN -->
<a name="LanguageSupport"></a> **Suporte ao idioma**

Os campos pesquisáveis são submetidos a análise que, frequentemente, envolve quebra de palavras, normalização do texto e filtragem de termos. Por padrão, os campos pesquisáveis no Azure Search são analisados com o [Analisador Apache Lucene Padrão](http://lucene.apache.org/core/4_9_0/analyzers-common/index.html), que quebra o texto em elementos seguindo as regras de ["Segmentação de texto Unicode"](http://unicode.org/reports/tr29/). Além disso, o analisador padrão converte todos os caracteres em sua forma em letras minúsculas. Documentos indexados e termos de pesquisa são submetidos a análise durante a indexação e o processamento de consultas.

O Azure Search dá suporte à indexação de campos em diversos idiomas. Cada um desses idiomas requer um analisador de texto não padrão que leva em consideração as características de determinado idioma. O Azure Search oferece dois tipos de analisadores:

- 28 analisadores apoiados pela Lucene.
- 50 analisadores apoiados pela tecnologia de processamento de idioma natural proprietária da Microsoft usada no Office e no Bing. 

Alguns desenvolvedores talvez prefiram a solução mais familiar, simples e aberta da Lucene. A Lucene é mais rápida, mas os analisadores da Microsoft têm recursos avançados, como tematização. Se possível, você deve executar comparações entre os analisadores da Microsoft e da Lucene para decidir qual é a melhor opção.

***Veja a comparação***

O analisador de inglês da Lucene amplia o analisador padrão. Ele remove possessivos (apóstrofos à direita) de palavras, aplica a lematização conforme o [algoritmo de lematização de Porter](http://tartarus.org/~martin/PorterStemmer/) e remove as [palavras irrelevantes](http://en.wikipedia.org/wiki/Stop_words) do inglês. A consulta e a indexação com os analisadores da Lucene são muito rápidas.

Em comparação, o analisador da Microsoft implementa um lematizador expansiva que gera no momento da consulta todas as formas possíveis de cada termo de consulta, resultando em maior precisão de resultados, mas também mais latência. Um menor desempenho das consultas é comum em lematizadores expansivos. A indexação com analisadores da Microsoft é em média três vezes mais lenta do que seus equivalentes da Lucene.

***Configuração***

Para cada campo na definição do índice, você pode definir a propriedade `analyzer` como um nome de analisador que especifica o idioma e o fornecedor. Por exemplo, você pode ter campos separados para descrições de hotéis em inglês, francês e espanhol, existentes lado a lado no mesmo índice. A consulta especifica o campo específico do idioma a ser retornado em suas consultas de pesquisa. Você pode examinar os exemplos de consultas que incluem a propriedade `analyzer` em [Pesquisar documentos](#SearchDocs).

***Lista de analisadores***

A seguir está a lista de analisadores com suporte, juntamente com uma breve descrição de seus recursos:

<table style="font-size:12">
    <tr>
		<th>Linguagem</th>
		<th>Nome do analisador</th>
		<th>Descrição</th>
	</tr>
    <tr>
		<td>Árabe</td>
		<td>ar.lucene</td>
		<td>
		<ul>
			<li>Implementa a normalização ortográfica do árabe</li>
			<li>Aplica a lematização algorítmica leve</li>
			<li>Filtra palavras irrelevantes do árabe</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Árabe</td>
		<td>ar.microsoft</td>
		<td>
		<ul>		
			<li>Lematizador redutivo</li>
			<li>Filtra palavras irrelevantes do árabe</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Bangla</td>
		<td>bg.microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>			
		</ul>
		</td>
	</tr>
    <tr>
		<td>Búlgaro</td>
		<td>bn.microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>			
		</ul>
		</td>
	</tr>
    <tr>
		<td>Catalão</td>
		<td>ca.microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>		
			<li>Remove os sinais diacríticos</li>	
		</ul>
		</td>
	</tr>
    <tr>
		<td>Chinês (simplificado)</td>
		<td>zh-Hans.lucene</td>
		<td>
		<ul>
			<li>Usa modelos de conhecimento probabilístico para encontrar a segmentação de palavras ideal</li>
			<li>Filtra palavras irrelevantes do chinês</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Chinês (simplificado)</td>
		<td>zh-Hans.microsoft</td>
		<td>
		<ul>
			<li>Usa modelos de conhecimento probabilístico para encontrar a segmentação de palavras ideal</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Chinês (tradicional)</td>
		<td>zh-Hant.lucene</td>
		<td>
		<ul>
			<li>Indexa bigramas (grupos sobrepostos de dois caracteres chineses adjacentes)</li>
			<li>Normaliza diferenças de largura de caracteres</li>
		</ul>
		</td>
	<tr>
    <tr>
		<td>Chinês (tradicional)</td>
		<td>zh-Hant.microsoft</td>
		<td>
		<ul>
			<li>Usa modelos de conhecimento probabilístico para encontrar a segmentação de palavras ideal</li>
		</ul>
		</td>
	<tr>
    <tr>
		<td>Croata</td>
		<td>hr.microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Tcheco</td>
		<td>cs.lucene</td>
		<td>
		<ul>
			<li>Aplica a lematização leve</li>
			<li>Filtra palavras irrelevantes do tcheco</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Tcheco</td>
		<td>cs.microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>
			<li>Filtra palavras irrelevantes do tcheco</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Dinamarquês</td>
		<td>da.lucene</td>
		<td>
		<ul>
			<li>Aplica a lematização leve</li>
			<li>Filtra palavras irrelevantes do dinamarquês</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Dinamarquês</td>
		<td>da.Microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>
			<li>Filtra palavras irrelevantes do dinamarquês</li>
			<li>Decomposição</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Holandês</td>
		<td>nl.lucene</td>
		<td>
		<ul>
			<li>Aplica a lematização leve</li>
			<li>Filtra palavras irrelevantes do holandês</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Holandês</td>
		<td>nl.microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>
			<li>Filtra palavras irrelevantes do holandês</li>
			<li>Decomposição</li>
			<li>Remove os sinais diacríticos</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Inglês</td>
		<td>en.lucene</td>
		<td>
		<ul>
			<li>Aplica a lematização leve</li>
			<li>Filtra palavras irrelevantes do inglês</li>
			<li>Remove possessivos</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Inglês</td>
		<td>en.Microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>
			<li>Filtra palavras irrelevantes do inglês</li>
			<li>Remove possessivos e sinais diacríticos</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Estoniano</td>
		<td>et.microsoft</td>
		<td>
		<ul>
			<li>Lematizador redutivo</li>
			<li>Decomposição</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Finlandês</td>
		<td>fi.lucene</td>
		<td>
		<ul>
			<li>Aplica a lematização leve</li>
			<li>Filtra palavras irrelevantes do finlandês</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Finlandês</td>
		<td>fi.microsoft</td>
		<td>
		<ul>
			<li>Lematizador redutivo</li>
			<li>Filtra palavras irrelevantes do finlandês</li>
			<li>Decomposição</li>
		</ul>
		</td>
	</tr>	
    <tr>
		<td>Francês</td>
		<td>fr.lucene</td>
		<td>
		<ul>
			<li>Aplica a lematização leve</li>
			<li>Filtra palavras irrelevantes do francês</li>
			<li>Remove elisões</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Francês</td>
		<td>fr.microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>
			<li>Filtra palavras irrelevantes do francês</li>
			<li>Remove os sinais diacríticos</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Alemão</td>
		<td>de.lucene</td>
		<td>
		<ul>
			<li>Aplica a lematização leve</li>
			<li>Filtra palavras irrelevantes do alemão</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Alemão</td>
		<td>de.microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>
			<li>Filtra palavras irrelevantes do alemão</li>
			<li>Decomposição</li>
			<li>Remove os sinais diacríticos</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Grego</td>
		<td>el.lucene</td>
		<td>
		<ul>
			<li>Aplica a lematização leve</li>
			<li>Filtra palavras irrelevantes do grego</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Grego</td>
		<td>el.microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>
			<li>Filtra palavras irrelevantes do grego</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Guzerate</td>
		<td>gu.microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Hebraico</td>
		<td>he.microsoft</td>
		<td>
		<ul>
			<li>Lematizador redutivo</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Híndi</td>
		<td>hi.lucene</td>
		<td>
		<ul>
			<li>Aplica a lematização leve</li>
			<li>Filtra palavras irrelevantes do híndi</li>
			<li>Remove algumas diferenças de variações de ortografia</li>
			<li>Normaliza a representação Unicode de texto em idiomas indianos.</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Híndi</td>
		<td>hi.microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>
			<li>Filtra palavras irrelevantes do híndi</li>			
		</ul>
		</td>
	</tr>
    <tr>
		<td>Húngaro</td>
		<td>hu.lucene</td>
		<td>
		<ul>
			<li>Aplica a lematização leve</li>
			<li>Filtra palavras irrelevantes do húngaro</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Húngaro</td>
		<td>hu.Microsoft</td>
		<td>
		<ul>
			<li>Lematizador redutivo</li>
			<li>Filtra palavras irrelevantes do húngaro</li>
			<li>Decomposição</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Islandês</td>
		<td>is.microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>			
		</ul>
		</td>
	</tr>
    <tr>
		<td>Indonésio (Bahasa)</td>
		<td>id.lucene</td>
		<td>
		<ul>
			<li>Aplica a lematização leve</li>
			<li>Filtra palavras irrelevantes do indonésio</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Indonésio (Bahasa)</td>
		<td>id.microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>		
			<li>Filtra palavras irrelevantes do indonésio</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Italiano</td>
		<td>it.lucene</td>
		<td>
		<ul>
			<li>Aplica a lematização leve</li>
			<li>Filtra palavras irrelevantes do italiano</li>
			<li>Remove elisões</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Italiano</td>
		<td>it.microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>
			<li>Filtra palavras irrelevantes do italiano</li>
			<li>Remove os sinais diacríticos</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Japonês</td>
		<td>ja.lucene</td>
		<td>
		<ul>
			<li>Usa a análise morfológica</li>
			<li>Normaliza variações comuns de ortografia katakana</li>
			<li>Remoção leve de palavras/marcas irrelevantes</li>
			<li>Normalização de largura de caracteres</li>
			<li>Tematização ‒ reduz adjetivos e verbos flexionados à sua forma base</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Japonês</td>
		<td>ja.microsoft</td>
		<td>
		<ul>
			<li>Usa a análise morfológica</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Kannada</td>
		<td>ka.microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Coreano</td>
		<td>ko.lucene</td>
		<td>
		<ul>
			<li>Indexa bigramas (grupos sobrepostos de dois caracteres Hangul adjacentes)</li>
			<li>Normaliza diferenças de largura de caracteres</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Letão</td>
		<td>lv.lucene</td>
		<td>
		<ul>
			<li>Aplica a lematização leve</li>
			<li>Filtra palavras irrelevantes do letão</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Letão</td>
		<td>lv.microsoft</td>
		<td>
		<ul>
			<li>Lematizador redutivo</li>
			<li>Filtra palavras irrelevantes do letão</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Lituano</td>
		<td>lt.microsoft</td>
		<td>
		<ul>
			<li>Lematizador redutivo</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Malaiala</td>
		<td>ml.microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Malaio (latino)</td>
		<td>ms.microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Marati</td>
		<td>mr.microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Norueguês</td>
		<td>no.lucene</td>
		<td>
		<ul>
			<li>Aplica a lematização leve</li>
			<li>Filtra palavras irrelevantes do norueguês</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Norueguês</td>
		<td>no.Microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>
			<li>Filtra palavras irrelevantes do norueguês</li>
			<li>Decomposição</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Polonês</td>
		<td>pl.lucene</td>
		<td>
		<ul>
			<li>Aplica a lematização algorítmica (Stempel)</li>
			<li>Filtra palavras irrelevantes do polonês</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Polonês</td>
		<td>pl.Microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>
			<li>Filtra palavras irrelevantes do polonês</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Português (Brasil)</td>
		<td>pt-Br.lucene</td>
		<td>
		<ul>
			<li>Aplica a lematização leve</li>
			<li>Filtra palavras irrelevantes do português (Brasil)</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Português (Brasil)</td>
		<td>pt-Br.microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>			
			<li>Filtra palavras irrelevantes do português (Brasil)</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Português (Portugal)</td>
		<td>pt-Pt.lucene</td>
		<td>
		<ul>
			<li>Aplica a lematização leve</li>
			<li>Filtra palavras irrelevantes do português</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Português (Portugal)</td>
		<td>pt-Pt.microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>
			<li>Filtra palavras irrelevantes do português</li>
			<li>Remove os sinais diacríticos</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Punjabi</td>
		<td>pa.microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Romeno</td>
		<td>ro.lucene</td>
		<td>
		<ul>
			<li>Aplica a lematização leve</li>
			<li>Filtra palavras irrelevantes do romeno</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Romeno</td>
		<td>ro.microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>
			<li>Filtra palavras irrelevantes do romeno</li>
			<li>Remove os sinais diacríticos</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Russo</td>
		<td>ru.lucene</td>
		<td>
		<ul>
			<li>Aplica a lematização leve</li>
			<li>Filtra palavras irrelevantes do russo</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Russo</td>
		<td>ru.microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>
			<li>Filtra palavras irrelevantes do russo</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Sérvio (cirílico)</td>
		<td>sr-cyrillic.microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Sérvio (latino)</td>
		<td>sr-latin.microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>	
		</ul>
		</td>
	</tr>
    <tr>
		<td>Eslovaco</td>
		<td>sk.microsoft</td>
		<td>
		<ul>
			<li>Lematizador redutivo</li>	
			<li>Decomposição</li>		
		</ul>
		</td>
	</tr>
    <tr>
		<td>Esloveno</td>
		<td>sk.microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Espanhol</td>
		<td>es.lucene</td>
		<td>
		<ul>
			<li>Aplica a lematização leve</li>
			<li>Filtra palavras irrelevantes do espanhol</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Espanhol</td>
		<td>es.microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>
			<li>Filtra palavras irrelevantes do espanhol</li>
			<li>Remove os sinais diacríticos</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Sueco</td>
		<td>sv.lucene</td>
		<td>
		<ul>
			<li>Aplica a lematização leve</li>
			<li>Filtra palavras irrelevantes do sueco</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Sueco</td>
		<td>sv.microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>
			<li>Filtra palavras irrelevantes do sueco</li>
			<li>Decomposição</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Tâmil</td>
		<td>ta.microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Télugo</td>
		<td>te.microsoft</td>
		<td>
		<ul>
			<li>Lematizador expansivo (tematização)</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Tailandês</td>
		<td>th.lucene</td>
		<td>
		<ul>
			<li>Aplica a lematização leve</li>
			<li>Filtra palavras irrelevantes do tailandês</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Tailandês</td>
		<td>th.microsoft</td>
		<td>
		<ul>			
			<li>Filtra palavras irrelevantes do tailandês</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Turco</td>
		<td>tr.lucene</td>
		<td>
		<ul>
			<li>Remove todos os caracteres após um apóstrofo (inclusive o próprio apóstrofo)</li>
			<li>Aplica a lematização leve</li>
			<li>Filtra palavras irrelevantes do turco</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Turco</td>
		<td>tr.microsoft</td>
		<td>
		<ul>
			<li>Lematizador redutivo</li>
			<li>Filtra palavras irrelevantes do turco</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Ucraniano</td>
		<td>uk.microsoft</td>
		<td>
		<ul>
		<li>Lematizador expansivo (tematização)</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Urdu</td>
		<td>ur.microsoft</td>
		<td>
		<ul>
		<li>Lematizador expansivo (tematização)</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Vietnamita</td>
		<td>vi.microsoft</td>
		<td>
		<ul>
			
		</ul>
		</td>
	</tr>
	<td colspan="3">Além disso, o Azure Search fornece configurações do analisador que não são específicas de idiomas</td> 
    <tr>
		<td>Dobra ASCII padrão</td>
		<td>standardasciifolding.Lucene</td>
		<td>
		<ul>
			<li>Segmentação de texto Unicode (Criador de Token Padrão)</li>
			<li>Filtro de dobra ASCII ‒ converte caracteres Unicode que não pertencem ao conjunto dos primeiros 127 caracteres ASCII em seus equivalentes em ASCII. Isso é útil para remover os sinais diacríticos.</li>
		</ul>
		</td>
	</tr>
</table>

Todos os analisadores com nomes anotados com <i>lucene</i> são da plataforma de [analisadores de idiomas do Apache Lucene](http://lucene.apache.org/core/4_9_0/analyzers-common/overview-summary.html). Mais informações sobre o filtro de dobra ASCII podem ser encontradas [aqui](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html).

**Opções de CORS**

O Javascript do lado do cliente não pode chamar APIs por padrão, pois o navegador impedirá todas as solicitações entre origens. Habilite o CORS (Compartilhamento de Recursos entre Origens) definindo o atributo `corsOptions` para permitir consultas entre origens em seu índice. Observe que apenas APIs de consulta dão suporte a CORS por motivos de segurança. As seguintes opções podem ser definidas para CORS:

- `allowedOrigins` (obrigatório): essa é uma lista de origens às quais será concedido acesso ao índice. Isso significa que qualquer código Javascript fornecido por essas origens poderá consultar seu índice (supondo que ele forneça a chave de API correta). Cada origem normalmente tem o formato `protocol://fully-qualified-domain-name:port`, embora a porta muitas vezes seja omitida. Consulte [este artigo](http://go.microsoft.com/fwlink/?LinkId=330822) para obter mais detalhes.
 - Se você quiser permitir o acesso a todas as origens, inclua `*` como um único item na matriz `allowedOrigins`. Observe que **essa não é uma prática recomendável para serviços de pesquisa de produção.** No entanto, pode ser útil para fins de depuração ou de desenvolvimento.
- `maxAgeInSeconds` (opcional): navegadores usam esse valor para determinar a duração (em segundos) para respostas de simulação de CORS de cache. Esse deve ser um inteiro não negativo. Quanto maior for esse valor, melhor será o desempenho, porém, mais tempo levará para que as alterações de política CORS entrem em vigor. Se ele não for definido, uma duração padrão de cinco minutos será usada.

<a name="Suggesters"></a> **Sugestores**

Um sugestor permite o preenchimento automático em pesquisas. Normalmente, cadeias de caracteres de pesquisa parciais são enviadas à [API de sugestões](#Suggestions) enquanto o usuário está digitando uma consulta de pesquisa, e a API retorna um conjunto de frases sugeridas. Um sugestor que você define no índice determina se e como sugestões são retornadas.

Se você usou a versão de visualização pública do Azure Search, `suggesters` substitui uma propriedade booliana antiga (`"suggestions": false`) que dava suporte apenas a sugestões de prefixo para cadeias de caracteres curtas (3-25 caracteres). Sua substituição, `suggesters`, dá suporte à correspondências infixas que localizam os termos correspondentes no início ou no meio do conteúdo do campo, com melhor tolerância para erros em cadeias de caracteres de pesquisa. Essa é a única implementação da API de sugestões. A propriedade `suggestions` que foi introduzida na `api-version=2014-07-31-Preview` continua a funcionar nessa versão, mas não está operacional na versão `2015-02-28` do Azure Search.

**Importante:** `Suggesters` funcionam melhor quando usados para sugerir documentos específicos, em vez de termos ou frases flexíveis. Os campos de melhores candidatos são títulos, nomes e outras frases relativamente curtas que podem identificar um item. Os campos repetitivos, como categorias e marcas, ou campos muito longos, como campos de comentários ou descrições, são menos eficazes.

Como parte da definição do índice, você pode adicionar um único sugestor à coleção de `suggesters`. As propriedades que definem um sugestor são:

- `name`: o nome do sugestor. Use o nome do sugestor ao chamar a API de `suggest`.
- `searchMode`: a estratégia usada para pesquisar frases candidatas. O único modo com suporte atualmente é `analyzingInfixMatching`, que executa a correspondência flexível de frases no início ou no meio da frase.
- `sourceFields`: uma lista de um ou mais campos que são a fonte do conteúdo para obter sugestões. Somente os campos dos tipos `Edm.String` e `Collection(Edm.String)` podem ser fontes de sugestões. Somente os campos que não têm um analisador de idioma personalizado definido podem ser usados.

Atualmente você pode ter apenas um sugestor nas coleções de `suggesters` na versão atual da API.

<a name="CreateUpdateIndexExample"></a> **Exemplo de corpo de solicitação**
 
    {
      "name": "hotels",  
      "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
	    {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, analyzer="fr.lucene"},
        {"name": "hotelName", "type": "Edm.String"},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean"},
        {"name": "smokingAllowed", "type": "Edm.Boolean"},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
      ],
      "suggesters": [
        {
          "name": "sg",
          "searchMode": "analyzingInfixMatching",
          "sourceFields": ["hotelName"]
        }
      ] 
    }

**Resposta**

Para uma solicitação bem-sucedida: "201 Criado".

Por padrão, o corpo da resposta conterá o JSON para a definição de índice que foi criada. Se o cabeçalho da solicitação `Prefer` for definido como `return=minimal`, o corpo da resposta estará vazio, e o código de status de êxito será "204 Sem Conteúdo" em vez de "201 Criado". Isso ocorre independentemente de PUT ou POST ter sido usado para criar o índice.

**Comentários**

Atualmente, há suporte limitado para atualizações de esquema de índice. Atualmente, não há suporte às atualizações de esquema que exigem reindexação, como a alteração de tipos de campo. Embora os campos existentes não possam ser alterados ou excluídos, novos campos podem ser adicionados a um índice existente a qualquer momento. Quando um novo campo é adicionado, todos os documentos existentes no índice automaticamente têm um valor nulo para esse campo. Nenhum espaço de armazenamento adicional será consumido até que novos documentos sejam adicionados ao índice.

<a name="UpdateIndex"></a>
### Atualizar o índice

Você pode atualizar um índice existente no Azure Search usando uma solicitação HTTP PUT. As atualizações podem incluir adição de novos campos ao esquema existente, modificação de opções de CORS e modificação de perfis de pontuação. Confira [Adicionar perfis de pontuação](https://msdn.microsoft.com/library/azure/dn798928.aspx) para saber mais. Especifique o nome do índice a ser atualizado no URI da solicitação:

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Importante:** o suporte para atualizações de esquema de índice é limitado às operações que não exigem a recriação do índice de pesquisa. Atualmente, não há suporte às atualizações de esquema que exigem reindexação, como a alteração de tipos de campo. Novos campos podem ser adicionados a qualquer momento, embora os campos existentes não possam ser alterados nem excluídos. O mesmo se aplica a `suggesters`. Novos campos podem ser adicionados a um sugestor no momento em que os campos são adicionados, mas os campos não podem ser removidos dos `suggesters` e campos existentes não podem ser adicionados a `suggesters`.

Ao se adicionar um novo campo a um índice, todos os documentos existentes no índice automaticamente terão um valor nulo para esse campo. Nenhum espaço de armazenamento adicional será consumido até que novos documentos sejam adicionados ao índice.

**Solicitação**

HTTPS é necessário para todas as solicitações de serviço. A solicitação **Atualizar Índice** é criada usando HTTP PUT. Com PUT, o nome do índice é parte da URL. Se o índice não existir, ele será criado. Se já existir, ele será atualizado para a nova definição.

O nome do índice deve estar em letras minúsculas, começar com uma letra ou número, não conter barras ou pontos e ter menos de 128 caracteres. Depois de iniciar o nome do índice com uma letra ou número, o restante do nome pode incluir qualquer letra, número e traços, desde que os traços não sejam consecutivos.

`api-version=[string]` (obrigatório). A versão de visualização é `api-version=2015-02-28-Preview`. Consulte [Controle de versão de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.

**Cabeçalhos de solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.

- `Content-Type`: obrigatório. Defina-o como `application/json`
- `api-key`: obrigatório. A `api-key` é usada para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres exclusivo de seu serviço. A solicitação **Atualizar Índice** deve incluir um cabeçalho de `api-key` definido como sua chave de administração (em vez de uma chave de consulta).
 
Você também precisará do nome de serviço para criar a URL da solicitação. Você pode obter o nome do serviço e a `api-key` por meio do painel de serviço no Portal do Azure. Consulte [Criar um serviço Azure Search no portal](../search-create-service-portal/) para obter ajuda sobre a navegação na página.

**Sintaxe do Corpo da Solicitação**

Ao se atualizar um índice existente, o corpo deve incluir a definição de esquema original, além de novos campos que você está adicionando, bem como os perfis de pontuação modificados, sugestores e as opções de CORS, se houver. Se não estiver modificando os perfis de pontuação e as opções de CORS, você deverá incluir os originais de quando o índice foi criado. Em geral, o melhor padrão a ser usado para atualizações é recuperar a definição do índice com GET, modificá-la e atualizá-la com PUT.

A sintaxe do esquema usada para criar um índice é reproduzida aqui para sua conveniência. Consulte [Criar Índice](#CreateIndex) para obter mais detalhes.

    {
      "name": (optional) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false,
		  "analyzer": "name of text analyzer"
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive #'s),
              ...
            }
          },
          "functions": (optional) [
            {
              "type": "magnitude | freshness | distance | tag",
              "boost": # (positive number used as multiplier for raw score != 1),
              "fieldName": "...",
              "interpolation": "constant | linear (default) | quadratic | logarithmic",
              "magnitude": {
                "boostingRangeStart": #,
                "boostingRangeEnd": #,
                "constantBoostBeyondRange": true | false (default)
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
			  "tag": {
				"tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified) 
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }    


**Resposta**

Para uma solicitação bem-sucedida: "204 sem Conteúdo".

Por padrão, o corpo da resposta estará vazio. No entanto, se o cabeçalho `Prefer` da solicitação for definido como `return=representation`, o corpo da resposta conterá o JSON para a definição de índice que foi atualizada. Nesse caso, o código de status de êxito será "200 OK".

<a name="ListIndexes"></a>
### Listar Índices

A operação **Listar Índices** retorna uma lista dos índices existentes no momento em seu serviço Azure Search.

    GET https://[service name].search.windows.net/indexes?api-version=[api-version]
    api-key: [admin key]

**Solicitação**

HTTPS é necessário para todas as solicitações de serviço. A solicitação **Listar Índices** pode ser criada usando o método GET.

`api-version=[string]` (obrigatório). A versão de visualização é `api-version=2015-02-28-Preview`. Consulte Controle de versão de serviço de pesquisa para obter detalhes e versões alternativas.

**Cabeçalhos de solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.
 
- `api-key`: obrigatório. A `api-key` é usada para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres exclusivo de seu serviço. A solicitação **Listar Índices** deve incluir uma `api-key` definida como uma chave de administração (em vez de uma chave de consulta).
 
Você também precisará do nome de serviço para criar a URL da solicitação. Você pode obter o nome do serviço e a `api-key` por meio do painel de serviço no Portal do Azure. Consulte [Criar um serviço Azure Search no portal](../search-create-service-portal/) para obter ajuda sobre a navegação na página.

**Corpo da solicitação**

Nenhum.

**Resposta**

Código de status: 200 OK é retornado para uma resposta bem-sucedida.

Aqui está um exemplo de corpo de resposta:

    {
      "value": [
        {
          "name": "Books",
          "fields": [
            {"name": "ISBN", ...},
            ...
          ]
        },
        {
          "name": "Games",
          ...
        },
        ...
      ]
    }

Observe que você pode filtrar a resposta para apenas as propriedades em que está interessado. Por exemplo, se você quiser apenas uma lista de nomes de índice, use a opção de consulta OData `$select`:

    GET /indexes?api-version=2015-02-28-Preview&$select=name

Nesse caso, a resposta do exemplo anterior seria exibida da seguinte maneira:

    {
      "value": [
        {"name": "Books"},
        {"name": "Games"},
        ...
      ]
    }

Essa é uma técnica útil para economizar largura de banda se você tiver muitos índices em seu serviço de pesquisa.

<a name="GetIndex"></a>
### Obter o índice

A operação **Obter Índice** obtém a definição de índice do Azure Search.

    GET https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**Solicitação**

HTTPS é necessário para as solicitações de serviço. A solicitação **Obter Índice** pode ser criada usando o método GET.
 
O [nome do índice] na solicitação URI especifica qual índice deve ser retornado da coleção de índices.

`api-version=[string]` (obrigatório). A versão de visualização é `api-version=2015-02-28-Preview`. Consulte Controle de versão de serviço de pesquisa para obter detalhes e versões alternativas.

**Cabeçalhos de solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.
 
- `api-key`: a `api-key` é usada para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres exclusivo de seu serviço. A solicitação **Obter Índice** deve incluir uma `api-key` definida como uma chave de administração (em vez de uma chave de consulta).

Você também precisará do nome de serviço para criar a URL da solicitação. Você pode obter o nome do serviço e a `api-key` por meio do painel de serviço no Portal do Azure. Consulte [Criar um serviço Azure Search no portal](../search-create-service-portal/) para obter ajuda sobre a navegação na página.

**Corpo da solicitação**

Nenhum.

**Resposta**

Código de status: 200 OK é retornado para uma resposta bem-sucedida.

Consulte o exemplo de JSON em [Criando e atualizando um índice](#CreateUpdateIndexExample) para obter um exemplo da carga de resposta.

<a name="DeleteIndex"></a>
### Excluir o índice

A operação **Excluir Índice** remove um índice e os documentos associados de seu serviço Azure Search. Você pode obter o nome do índice por meio do painel de serviço no Portal  do Azure ou por meio da API. Consulte [Listar índices](#ListIndexes) para obter detalhes.

    DELETE https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]
    
**Solicitação**

HTTPS é necessário para as solicitações de serviço. A solicitação **Excluir Índice** pode ser criada usando o método DELETE.
 
O [nome do índice] no URI da solicitação especifica qual índice deve ser excluído da coleção de índices.

`api-version=[string]` (obrigatório). A versão de visualização é `api-version=2015-02-28-Preview`. Consulte Controle de versão de serviço de pesquisa para obter detalhes e versões alternativas.

**Cabeçalhos de solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.
 
- `api-key`: obrigatório. A `api-key` é usada para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres exclusivo para a URL do serviço. A solicitação **Excluir Índice** deve incluir um cabeçalho de `api-key` definido como sua chave de administração (em vez de uma chave de consulta).
 
Você também precisará do nome de serviço para criar a URL da solicitação. Você pode obter o nome do serviço e a `api-key` por meio do painel de serviço no Portal do Azure. Consulte [Criar um serviço Azure Search no portal](../search-create-service-portal/) para obter ajuda sobre a navegação na página.

**Corpo da solicitação**

Nenhum.

**Resposta**

Código de status: 204 sem conteúdo é retornado para uma resposta bem-sucedida.

<a name="GetIndexStats"></a>
### Obter estatísticas de índice

A operação **Obter Estatísticas do Índice** retorna uma contagem de documentos do Azure Search para o índice atual, além do uso do armazenamento.

	GET https://[service name].search.windows.net/indexes/[index name]/stats?api-version=[api-version]
    api-key: [admin key]

**Solicitação**

HTTPS é necessário para todas as solicitações de serviço. A solicitação **Obter Estatísticas do Índice** pode ser criada usando o método GET.

O [nome do índice] no URI da solicitação instrui o serviço a retornar as estatísticas de índice para o índice especificado.

`api-version=[string]` (obrigatório). A versão de visualização é `api-version=2015-02-28-Preview`. Consulte Controle de versão de serviço de pesquisa para obter detalhes e versões alternativas.


**Cabeçalhos de solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.
 
- `api-key`: a `api-key` é usada para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres exclusivo de seu serviço. A solicitação **Obter Estatísticas de Índice** deve incluir um `api-key` definido como uma chave de administração (em vez de uma chave de consulta).
 
Você também precisará do nome de serviço para criar a URL da solicitação. Você pode obter o nome do serviço e a `api-key` por meio do painel de serviço no Portal do Azure. Consulte [Criar um serviço Azure Search no portal](../search-create-service-portal/) para obter ajuda sobre a navegação na página.

**Corpo da solicitação**

Nenhum.

**Resposta**

Código de status: 200 OK é retornado para uma resposta bem-sucedida.

O corpo da resposta está no seguinte formato:

    {
      "documentCount": number,
	  "storageSize": number (size of the index in bytes)
    }

________________________________________ <a name="DocOps"></a>
## Operações de documento

No Azure Search, um índice é populado usando documentos JSON que você carrega no serviço. Todos os documentos que você carrega formam o corpus de seus dados de pesquisa. Documentos contêm campos, alguns dos quais são indexados em termos de pesquisa ao serem carregados. O segmento de URL `/docs` na API do Azure Search representa a coleção de documentos em um índice. Todas as operações executadas na coleção, como carregar, mesclar, excluir ou consultar documentos, ocorrem no contexto de um único índice. Portanto, as URLs para essas operações sempre começarão com `/indexes/[index name]/docs` para um nome de índice específico.

O código do aplicativo pode gerar documentos JSON para carregar no Azure Search usando um conjunto de resultados de um banco de dados relacional ou qualquer outra fonte de dados estruturados.

Na maioria dos cenários de desenvolvimento de aplicativos, dados de pesquisa são separados e externos à camada de dados do aplicativo. Se o aplicativo usar um banco de dados local para acompanhar o status de estoque, os documentos mantidos no Azure Search conterão valores de dados semelhantes ou idênticos em termos de nome do produto, preço e disponibilidade, mas eles serão armazenados em um índice invertido para otimizar as pesquisas.

Você deve planejar ter um documento para cada item que deseja pesquisar. Um aplicativo de aluguel de filmes pode ter um documento por filme, um aplicativo de vitrine pode ter um documento por SKU, um aplicativo de cursos online pode ter um documento por curso, uma empresa de pesquisa pode ter um documento para cada artigo acadêmico em seu repositório e assim por diante.

Os documentos consistem em um ou mais campos. Os campos podem conter texto indexado pelo Azure Search em termos de pesquisa, bem como valores não indexados ou que não são de texto, que podem ser usadas em filtros ou perfis de pontuação. Os nomes, tipos de dados e recursos de pesquisa com suporte para cada campo são determinados pelo esquema de índice. Um dos campos em cada esquema de índice deve ser designado como uma ID, e cada documento deve ter um valor para o campo de ID que identifique de forma exclusiva o documento no índice. Todos os outros campos de documento são opcionais e usarão como padrão um valor nulo, se o valor não for especificado. Observe que os valores nulos não ocupam espaço no índice invertido.

Para poder carregar documentos, você precisa já ter criado o índice no serviço. Consulte [Criar o índice](#CreateIndex) para obter detalhes sobre essa primeira etapa.


<a name="AddOrUpdateDocuments"></a>
### Adicionar, atualizar ou excluir documentos

Você pode carregar, mesclar, mesclar ou carregar ou excluir documentos de um índice especificado usando HTTP POST. Para um grande número de atualizações, recomenda-se o processamento em lotes de documentos (até 1000 documentos ou aproximadamente 16 MB por lote).

    POST https://[service name].search.windows.net/indexes/[index name]/docs/index?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]
    
**Solicitação**

HTTPS é necessário para todas as solicitações de serviço. Você pode carregar, mesclar, mesclar ou carregar ou excluir documentos de um índice especificado usando HTTP POST.

O URI da solicitação inclui o [nome do índice], especificando em qual índice postar documentos. Só é possível postar documentos em um índice por vez.

`api-version=[string]` (obrigatório). A versão de visualização é `api-version=2015-02-28-Preview`. Consulte Controle de versão de serviço de pesquisa para obter detalhes e versões alternativas.

**Cabeçalhos de solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.

- `Content-Type`: obrigatório. Defina-o como `application/json`
- `api-key`: obrigatório. A `api-key` é usada para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres exclusivo de seu serviço. A solicitação **Adicionar Documentos** deve incluir um cabeçalho de `api-key` definido como sua chave de administração (em vez de uma chave de consulta).
 
Você também precisará do nome de serviço para criar a URL da solicitação. Você pode obter o nome do serviço e a `api-key` por meio do painel de serviço no Portal do Azure. Consulte [Criar um serviço Azure Search no portal](../search-create-service-portal/) para obter ajuda sobre a navegação na página.

**Corpo da solicitação**

O corpo da solicitação contém um ou mais documentos a serem indexados. Os documentos são identificados por uma chave exclusiva. Cada documento está associado uma ação: carregar, mesclar, mesclar ou carregar ou excluir. As solicitações de carregamento devem incluir os dados do documento como um conjunto de pares de chave/valor.

    {
      "value": [
        {
          "@search.action": "upload (default) | merge | mergeOrUpload | delete",
          "key_field_name": "unique_key_of_document", (key/value pair for key field from index schema)
          "field_name": field_value (key/value pairs matching index schema)
            ...
        },
        ...
      ]
    }
    
**Ações do documento**

- `upload`: uma ação de carregamento é semelhante a um "upsert", em que o documento será inserido se for novo e será atualizado/substituído se ele existir. Observe que todos os campos são substituídos no caso da atualização.
- `merge`: a mesclagem atualiza um documento existente com os campos especificados. Se o documento não existir, a mesclagem falhará. Qualquer campo que você especificar em uma mesclagem substituirá o campo existente no documento. Isso inclui campos do tipo `Collection(Edm.String)`. Por exemplo, se o documento contiver um campo "marcas" com o valor `["budget"]` e você executar uma mesclagem com o valor `["economy", "pool"]` para "marcas", o valor final do campo "marcas" final será `["economy", "pool"]`. Ele **não** será `["budget", "economy", "pool"]`.
- `mergeOrUpload`: se comportará como `merge` se já existir um documento com a chave especificada no índice. Se o documento não existir, se comportará como `upload` com um novo documento.
- `delete`: a exclusão remove o documento especificado do índice. Observe que você pode especificar apenas o valor do campo de chave em uma operação `delete`. Se você tentar especificar outros campos, isso resultará em um erro HTTP 400. Se você quiser remover um campo individual de um documento, use `merge` em vez disso e apenas defina o campo explicitamente como `null`. 

**Resposta**

O código de status: 200 OK é retornado para uma resposta bem-sucedida, indicando que todos os itens forem indexados com êxito (conforme indicado pelo conjunto de campos 'status' definido como verdadeiro para todos os itens):

    {
      "value": [
        {
          "key": "unique_key_of_document",
          "status": true,
          "errorMessage": null
        }
      ]
    }  

O código de status: 207 é retornado quando pelo menos um item não é indexado com êxito (conforme indicado pelo campo 'status' definido como falso para itens que não foram indexados):
 
    {
      "value": [
        {
          "key": "unique_key_of_document",
          "status": false,
          "errorMessage": "The search service is too busy to process this document. Please try again later."
        }
      ]
    }  

A propriedade `errorMessage` indicará o motivo do erro de indexação, se possível.

**Observação**: se o seu código de cliente encontra com frequência uma resposta 207, um motivo possível para isso é que o sistema está sobrecarregado. Você pode confirmar isso verificando a propriedade `errorMessage`. Se esse for o caso, será recomendável a ***limitação de solicitações de indexação***. Caso contrário, se a indexação de tráfego não diminuir, o sistema poderá começar a rejeitar todas as solicitações com erros 503.

O código de status: 429 indica que você excedeu sua cota no número de documentos por índice. Você deve criar um novo índice ou atualizar para limites de capacidade mais altos.

**Exemplo:**

    {
      "value": [
        {
          "@search.action": "upload",
          "hotelId": "1",
          "baseRate": 199.0,
          "description": "Best hotel in town",
		  "description_fr": "Meilleur hôtel en ville", 
          "hotelName": "Fancy Stay",
		  "category": "Luxury",
          "tags": ["pool", "view", "wifi", "concierge"],
          "parkingIncluded": false,
		  "smokingAllowed": false,
          "lastRenovationDate": "2010-06-27T00:00:00Z",
          "rating": 5,
          "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
          "@search.action": "upload",
          "hotelId": "2",
          "baseRate": 79.99,
          "description": "Cheapest hotel in town",
	      "description_fr": "Hôtel le moins cher en ville",
          "hotelName": "Roach Motel",
		  "category": "Budget",
          "tags": ["motel", "budget"],
          "parkingIncluded": true,
		  "smokingAllowed": true,
          "lastRenovationDate": "1982-04-28T00:00:00Z",
          "rating": 1,
          "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
          "@search.action": "merge",
          "hotelId": "3",
          "baseRate": 279.99,
          "description": "Surprisingly expensive",
          "lastRenovationDate": null
        },
        {
          "@search.action": "delete",
          "hotelId": "4"
        }
      ]
    }
________________________________________ <a name="SearchDocs"></a>
### Pesquisar documentos

Uma operação **Pesquisar** é emitida como uma solicitação GET e especifica parâmetros de consulta que fornecem os critérios para a seleção de documentos correspondentes.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?[query parameters]
    api-key: [admin key]

**Solicitação**

HTTPS é necessário para as solicitações de serviço. A solicitação **Pesquisar** pode ser criada usando o método GET.

O URI da solicitação especifica qual índice deve ser consultado para todos os documentos que correspondem aos parâmetros de consulta.

Como prática recomendada, lembre-se de [codificar na URL](https://msdn.microsoft.com/library/system.uri.escapedatastring.aspx) os parâmetros de consulta específicos ao chamar a API REST diretamente. Para operações de pesquisa de documento, isso inclui:

- `$filter`
- `facet`
- `highlightPreTag`
- `highlightPostTag`
- `search`
- `moreLikeThis`

A codificação de URL é recomendada apenas nos parâmetros da consulta acima. Se você inadvertidamente codificar na URL a cadeia de caracteres de consulta inteira (tudo após o ?), as solicitações serão interrompidas.

Além disso, a codificação de URL só é necessária ao se chamar a API REST diretamente. Usar a [biblioteca de cliente .NET](https://msdn.microsoft.com/library/dn951165.aspx) lida com a codificação de URL para você.

**Parâmetros de consulta**

`search=[string]` (opcional) ‒ o texto a ser pesquisado. Todos os campos `searchable` são pesquisados por padrão, a menos que `searchFields` sejam especificados. Ao se pesquisar campos `searchable`, o próprio texto de pesquisa é indexado, assim, vários termos podem ser separados por espaços em branco (por exemplo: `search=hello world`). Para corresponder a qualquer termo, use `*` (isso pode ser útil para consultas de filtros boolianos). A omissão desse parâmetro tem o mesmo efeito que sua definição como `*`. Consulte "Sintaxe de consulta simples" a seguir para obter informações específicas sobre a sintaxe de pesquisa.

  - **Observação**: os resultados às vezes podem ser surpreendentes ao se consultar sobre campos `searchable`. O criador de token inclui lógica para lidar com casos comuns para texto em inglês, como apóstrofos, vírgulas em números etc. Por exemplo, `search=123,456` corresponderá a um único termo, 123,456, em vez de cada um do termos 123 e 456, já que as vírgulas são usadas como separadores de milhar para números grandes em inglês. Por esse motivo, recomendamos o uso de espaços em branco em vez de pontuação para separar os termos do parâmetro `search`.

`searchMode=any|all` (opcional; o padrão é `any`) ‒ se for necessária a correspondência de algum dos termos de pesquisa ou de todos eles para que o documento seja contado como uma correspondência.

`searchFields=[string]` (opcional) ‒ a lista de nomes de campos separados por vírgulas para pesquisar o texto especificado. Os campos de destino devem ser marcados como `searchable`.

`moreLikeThis=[key]` (opcional) **Importante:** esse recurso só está disponível na `2015-02-28-Preview`. Essa opção não pode ser usada em uma consulta que contém o parâmetro de pesquisa de texto `search=[string]`. O parâmetro `moreLikeThis` localiza os documentos que são semelhantes ao documento especificado pela chave do documento. Quando é feita uma solicitação de pesquisa com `moreLikeThis`, uma lista de termos de pesquisa é gerada com base na frequência e raridade dos termos no documento de origem. Esses termos são usados para fazer a solicitação. Por padrão, o conteúdo de todos os campos `searchable` será considerado, a menos que `searchFields` seja usado para restringir quais campos são pesquisados.

`$skip=#` (opcional) ‒ o número de resultados da pesquisa a serem ignorados. Não pode ser superior a 100.000. Se você precisar examinar os documentos em sequência, mas não puder usar `$skip` devido a essa limitação, considere o uso de `$orderby` em uma chave totalmente ordenada e `$filter` com um intervalo de consulta em vez disso.

`$top=#` (opcional) ‒ o número de resultados de pesquisa a serem recuperados. O padrão é 50. Se você especificar um valor maior que 1000 e houver mais de 1000 resultados, somente os primeiros 1000 resultados serão retornados, junto com um link para a próxima página de resultados (consulte `@odata.nextLink` no [exemplo a seguir](#SearchResponse)).

`$count=true|false` (opcional; o padrão é `false`) ‒ especifica se é necessário buscar a contagem total de resultados. A definição desse valor como `true` pode afetar o desempenho. Observe que a contagem retornada é uma aproximação.

`$orderby=[string]` (opcional) ‒ uma lista de expressões separadas por vírgulas para classificar os resultados. Cada expressão pode ser um nome de campo ou uma chamada para a função `geo.distance()`. Cada expressão pode ser seguida de `asc` para indicar a ordem crescente e `desc` para indicar a ordem decrescente. O padrão é a ordem crescente. Os empates serão resolvidos pelas pontuações de correspondência de documentos. Se nenhum `$orderby` for especificado, a ordem de classificação padrão será decrescente de acordo com a pontuação de correspondência dos documentos. Há um limite de 32 cláusulas para `$orderby`.

`$select=[string]` (opcional) ‒ uma lista de campos separados por vírgulas a serem recuperados. Se não for especificado, todos os campos marcados como recuperáveis no esquema serão incluídos. Você pode solicitar explicitamente todos os campos ao definir esse parâmetro como `*`.

`facet=[string]` (zero ou mais) ‒ um campo de acordo com o qual o facetamento deve ser realizado. Opcionalmente, a cadeia de caracteres pode conter parâmetros para personalizar o facetamento expressado como pares separados por vírgulas `name:value`. Os parâmetros válidos são:

- `count` (número máximo de termos de faceta; o padrão é 10). Não há um máximo, mas os valores mais altos incorrerão em uma penalidade de desempenho correspondente, principalmente se o campo facetado contiver um grande número de termos exclusivos.
  - Por exemplo: `facet=category,count:5` obtém as cinco principais categorias nos resultados de faceta.  
  - **Observação**: se o parâmetro `count` for menor do que o número de termos exclusivos, os resultados poderão não ser precisos. Isso ocorre devido à maneira como as consultas de facetamento são distribuídas entre fragmentos. O aumento de `count` geralmente aumenta a precisão da contagem de termos, mas isso prejudica o desempenho. 
- `sort` (uma das `count` para classificar em ordem *decrescente* por contagem, `-count` para classificar em ordem *crescente* por contagem, `value` para classificar em ordem *crescente* por valor ou `-value` para classificar em ordem *decrescente* por valor)
  - Por exemplo: `facet=category,count:3,sort:count` obtém as três principais categorias nos resultados de faceta em ordem decrescente pelo número de documentos com o nome de cada cidade. Por exemplo, se as três principais categorias forem Orçamento, Motel e Luxo, Orçamento tiver cinco ocorrências, Motel tiver seis e Luxo tiver quatro, as classificações, em ordem, serão Motel, Orçamento, Luxo.
  - Por exemplo: `facet=rating,sort:-value` produz todas as classificações possíveis, em ordem decrescente por valor. Por exemplo, se as classificações forem de 1 a 5, as classificações serão ordenadas como 5, 4, 3, 2, 1, independentemente de quantos documentos corresponderem a cada classificação.
- `values` (valores numéricos delimitados por barras verticais ou valores `Edm.DateTimeOffset` que especificam um conjunto dinâmico de valores de entrada de faceta)
  - Por exemplo: `facet=baseRate,values:10|20` produz três classificações: uma para a taxa de base 0 até 10, mas sem incluir esse valor, uma para 10 até 20, mas sem incluir esse valor e outra para 20 ou mais.
  - Por exemplo: `facet=lastRenovationDate,values:2010-02-01T00:00:00Z` produz duas classificações: uma para hotéis reformados antes de fevereiro de 2010 e outra para hotéis reformados em ou depois de 1º de fevereiro de 2010.
- `interval` (intervalo inteiro maior que 0 para números ou `minute`, `hour`, `day`, `week`, `month`, `quarter`, `year` para valores de tempo de data)
  - Por exemplo: `facet=baseRate,interval:100` produz classificações com base em intervalos de taxa de base de tamanho 100. Por exemplo, se as taxas de base estiverem todas entre US$ 60 e US$ 600, haverá classificações para 0-100, 100-200, 200-300, 300-400, 400-500 e 500-600.
  - Por exemplo: `facet=lastRenovationDate,interval:year` produz uma classificação para cada ano em que os hotéis foram reformados.
- **Observação**: `count` e `sort` podem ser combinados na mesma especificação de faceta, mas não podem ser combinados com `interval` ou `values`, e `interval` e `values` não podem ser combinados juntos.

`$filter=[string]` (opcional) ‒ uma expressão de pesquisa estruturada na sintaxe de OData padrão. Consulte [Sintaxe de expressão OData](#ODataExpressionSyntax) para obter detalhes sobre o subconjunto da gramática de expressões OData ao qual o Azure Search dá suporte.

`highlight=[string]` (opcional) ‒ realça um conjunto de nomes de campo separados por vírgulas usado para realçar ocorrências. Somente campos `searchable` podem ser usados para realçar ocorrências.

  `highlightPreTag=[string]` (opcional, o padrão é `<em>`) ‒ uma marca de cadeia de caracteres que é anexada ao início para realçar ocorrências. Deve ser definida com `highlightPostTag`. Caracteres reservados na URL devem ser codificados por percentual (por exemplo, %23, em vez de #).

  `highlightPostTag=[string]` (opcional, o padrão é `</em>`) ‒ uma marca de cadeia de caracteres que é anexada para realçar ocorrências. Deve ser definida com `highlightPreTag`. Caracteres reservados na URL devem ser codificados por percentual (por exemplo, %23, em vez de #).

`scoringProfile=[string]` (opcional) ‒ o nome de um perfil de pontuação para avaliar pontuações de correspondência de documentos correspondentes para classificar os resultados.

`scoringParameter=[string]` (zero ou mais) ‒ indica o valor de cada parâmetro definido em uma função de pontuação (por exemplo, `referencePointParameter`) usando o formato: nome:valor. Por exemplo, se o perfil de pontuação definir uma função com um parâmetro chamado "mylocation", a opção de cadeia de caracteres de consulta será &scoringParameter=mylocation:-122.2,44.8

`api-version=[string]` (obrigatório). A versão de visualização é `api-version=2015-02-28-Preview`. Consulte Controle de versão de serviço de pesquisa para obter detalhes e versões alternativas.

Observação: para essa operação, a `api-version` é especificada como um parâmetro de consulta.

**Cabeçalhos de solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.

- `api-key`: a `api-key` é usada para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres exclusivo para a URL do serviço. A solicitação **Pesquisar** pode especificar uma chave de administração ou a chave de consulta para `api-key`.
 
Você também precisará do nome de serviço para criar a URL da solicitação. Você pode obter o nome do serviço e a `api-key` por meio do painel de serviço no Portal do Azure. Consulte [Criar um serviço Azure Search no portal](../search-create-service-portal/) para obter ajuda sobre a navegação na página.

**Corpo da solicitação**

Nenhum.

<a name="SearchResponse"></a> **Resposta**

Código de status: 200 OK é retornado para uma resposta bem-sucedida.

    {
      "@odata.count": # (if $count=true was provided in the query),
      "@search.facets": { (if faceting was specified in the query)
        "facet_field": [
          {
            "value": facet_entry_value (for non-range facets),
            "from": facet_entry_value (for range facets),
            "to": facet_entry_value (for range facets),
            "count": number_of_documents
          }
        ],
        ...
      },
      "value": [
        {
          "@search.score": document_score (if a text query was provided),
          "@search.highlights": {
            field_name: [ subset of text, ... ],
            ...
          },
          key_field_name: document_key,
          field_name: field_value (retrievable fields or specified projection),
          ...
        },
        ...
      ],
      "@odata.nextLink": (URL to fetch the next page of results if $top is greater than 1000)
    }
    
**Exemplos:**

Você pode encontrar exemplos adicionais na página [Sintaxe de expressão OData para o Azure Search](https://msdn.microsoft.com/library/azure/dn798921.aspx).

1) Pesquisar o índice classificado em ordem decrescente por data.

    GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28-Preview

2) Em uma pesquisa facetada, pesquisar o índice e recuperar facetas para categorias, classificação, marcas, bem como itens com baseRate em intervalos específicos:

    GET /indexes/hotels/docs?search=test&facet=category&facet=rating&facet=tags&facet=baseRate,values:80|150|220&api-version=2015-02-28-Preview

3) Usando um filtro, restringir os resultados de consulta facetados anteriores depois que o usuário clicar na classificação 3 e na categoria "Motel":

    GET /indexes/hotels/docs?search=test&facet=tags&facet=baseRate,values:80|150|220&$filter=rating eq 3 and category eq 'Motel'&api-version=2015-02-28-Preview

4) Em uma pesquisa facetada, definir um limite superior para termos exclusivos retornados em uma consulta. O padrão é 10, mas você pode aumentar ou diminuir esse valor usando o parâmetro `count` no atributo `facet`:

    GET /indexes/hotels/docs?search=test&facet=city,count:5&api-version=2015-02-28-Preview

5) Pesquisar o índice em campos específicos. Por exemplo, um campo específico do idioma:

    GET /indexes/hotels/docs?search=hôtel&searchFields=description_fr&api-version=2015-02-28-Preview

6) Pesquisar o índice em vários campos. Por exemplo, você pode armazenar e consultar campos pesquisáveis em vários idiomas, todos no mesmo índice. Se descrições em inglês e francês coexistirem no mesmo documento, você poderá retornar qualquer uma das descrições ou todas elas nos resultados da consulta:

	GET /indexes/hotels/docs?search=hotel&searchFields=description,description_fr&api-version=2015-02-28-Preview
	
Observe que você pode consultar apenas um índice por vez. Não crie vários índices para cada idioma, a menos que você planeje consultar um de cada vez.

7) Paginar ‒ obter a primeira página de itens (o tamanho de página é 10):

    GET /indexes/hotels/docs?search=*&$skip=0&$top=10&api-version=2015-02-28-Preview

8) Paginar ‒ obter a segunda página de itens (o tamanho de página é 10):

    GET /indexes/hotels/docs?search=*&$skip=10&$top=10&api-version=2015-02-28-Preview

9) Recuperar um conjunto específico de campos:

    GET /indexes/hotels/docs?search=*&$select=hotelName,description&api-version=2015-02-28-Preview

10) Recuperar documentos que correspondem a uma expressão de filtro específica

    GET /indexes/hotels/docs?$filter=(baseRate ge 60 and baseRate lt 300) or hotelName eq 'Fancy Stay'&api-version=2015-02-28-Preview

11) Pesquisar o índice e retornar fragmentos com realces de ocorrências

    GET /indexes/hotels/docs?search=something&highlight=description&api-version=2015-02-28-Preview
    
12) Pesquisar o índice e retornar documentos classificados de mais próximo a mais distante em relação a um local de referência

    GET /indexes/hotels/docs?search=something&$orderby=geo.distance(location, geography'POINT(-122.12315 47.88121)')&api-version=2015-02-28-Preview

13) Pesquisar o índice, supondo que haja um perfil de pontuação chamado "geo" com duas funções de pontuação de distância, uma definindo um parâmetro chamado "currentLocation" e uma definindo um parâmetro chamado "lastLocation"

    GET /indexes/hotels/docs?search=something&scoringProfile=geo&scoringParameter=currentLocation:-122.123,44.77233&scoringParameter=lastLocation:-121.499,44.2113&api-version=2015-02-28-Preview

14) Localizar documentos no índice usando a sintaxe de consulta simples. Esta consulta retorna hotéis em que os campos de pesquisa contêm os termos "conforto" e "local", mas não "motel":

    GET /indexes/hotels/docs?search=comfort +location -motel&searchMode=all&api-version=2015-02-28-Preview

Observe o uso de `searchMode=all` acima. Incluir esse parâmetro substitui o padrão de `searchMode=any`, garantindo que `-motel` significa "E NÃO" em vez de "OU NÃO". Sem `searchMode=all`, você obtém "OU NÃO", que  expande em vez de restringir os resultados de pesquisa, e isso pode ser contraintuitivo para alguns usuários.


<a name="LookupAPI"></a>
### Pesquisar documento

A operação **Pesquisar Documento** recupera um documento do Azure Search. Isso é útil quando um usuário clica em um resultado de pesquisa específico e você deseja pesquisar detalhes específicos sobre esse documento.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/[key]?[query parameters] 
    api-key: [admin key]
    
**Solicitação**

HTTPS é necessário para as solicitações de serviço. A solicitação **Pesquisar Documento** pode ser criada da maneira a seguir.

    GET /indexes/[index name]/docs/key?[query parameters] 

Como alternativa, você pode usar a sintaxe tradicional de OData para pesquisa de chave:

    GET /indexes('[index name]')/docs('[key]')?[query parameters]

O URI da solicitação inclui um [nome de índice] e uma [chave], especificando qual documento deve ser recuperado do índice. Você pode obter apenas um documento por vez. Use **Pesquisar** para obter vários documentos em uma única solicitação.

**Parâmetros de consulta**

`$select=[string]` (opcional) ‒ uma lista de campos separados por vírgulas a serem recuperados. Se não for especificado ou se for definido como `*`, todos os campos marcados como recuperáveis no esquema serão incluídos na projeção.

`api-version=[string]` (obrigatório). A versão de visualização é `api-version=2015-02-28-Preview`. Consulte Controle de versão de serviço de pesquisa para obter detalhes e versões alternativas.

Observação: para essa operação, a `api-version` é especificada como um parâmetro de consulta.

**Cabeçalhos de solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.

- `api-key`: a `api-key` é usada para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres exclusivo para a URL do serviço. A solicitação **Pesquisar Documento** pode especificar uma chave de administração ou a chave de consulta para `api-key`.
 
Você também precisará do nome de serviço para criar a URL da solicitação. Você pode obter o nome do serviço e a `api-key` por meio do painel de serviço no Portal do Azure. Consulte [Criar um serviço Azure Search no portal](../search-create-service-portal/) para obter ajuda sobre a navegação na página.

**Corpo da solicitação**

Nenhum.

**Resposta**

Código de status: 200 OK é retornado para uma resposta bem-sucedida.

    {
      field_name: field_value (fields matching the default or specified projection)
    }
    
**Exemplo**

Pesquisar o documento que tem a chave '2'

    GET /indexes/hotels/docs/2?api-version=2015-02-28-Preview

Pesquisar o documento que tem a chave '3' usando a sintaxe de OData:

    GET /indexes('hotels')/docs('3')?api-version=2015-02-28-Preview

<a name="CountDocs"></a>
### Contar documentos

A operação **Contar Documentos** recupera uma contagem do número de documentos em um índice de pesquisa. A sintaxe de `$count` faz parte do protocolo OData.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/$count?api-version=[api-version]
    Accept: text/plain
    api-key: [admin key]
    
**Solicitação**

HTTPS é necessário para as solicitações de serviço. A solicitação **Contar Documentos** pode ser criada usando o método GET.

O [nome do índice] no URI da solicitação instrui o serviço a retornar uma contagem de todos os itens da coleção de documentos do índice especificado.

`api-version=[string]` (obrigatório). A versão de visualização é `api-version=2015-02-28-Preview`. Consulte Controle de versão de serviço de pesquisa para obter detalhes e versões alternativas.

**Cabeçalhos de solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.

- `Accept`: esse valor deve ser definido como `text/plain`.
- `api-key`: a `api-key` é usada para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres exclusivo para a URL do serviço. A solicitação **Contar Documentos** pode especificar uma chave de administração ou a chave de consulta para `api-key`.
 
Você também precisará do nome de serviço para criar a URL da solicitação. Você pode obter o nome do serviço e a `api-key` por meio do painel de serviço no Portal do Azure. Consulte [Criar um serviço Azure Search no portal](../search-create-service-portal/) para obter ajuda sobre a navegação na página.

**Corpo da solicitação**

Nenhum.

**Resposta**

Código de status: 200 OK é retornado para uma resposta bem-sucedida.

O corpo da resposta contém o valor da contagem como um inteiro formatado como texto sem formatação.

<a name="Suggestions"></a>
### Sugestões

A operação **Sugestões** recupera sugestões com base na entrada de pesquisa parcial. Ela é normalmente usada em caixas de pesquisa para fornecer sugestões de digitação antecipada à medida que os usuários inserem termos de pesquisa.

Solicitações de sugestão têm por objetivo sugerir documentos de destino, assim, o texto sugerido poderá ser repetido se a mesma pesquisa de entrada corresponder a vários documentos candidatos. Você pode usar `$select` para recuperar outros campos de documento (inclusive a chave do documento) para determinar qual documento é a fonte de cada sugestão.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/suggest?[query parameters]
    api-key: [admin key]


**Solicitação**

HTTPS é necessário para as solicitações de serviço. A solicitação **Sugestões** pode ser criada usando o método GET.

O URI da solicitação especifica o nome do índice a ser consultado. Ele também inclui o termo de pesquisa parcialmente inserido na cadeia de caracteres de consulta.

Como prática recomendada, lembre-se de [codificar na URL](https://msdn.microsoft.com/library/system.uri.escapedatastring.aspx) os parâmetros de consulta específicos ao chamar a API REST diretamente. Para operações de sugestões, isso inclui:

- `$filter`
- `highlightPreTag`
- `highlightPostTag`
- `search`

A codificação de URL é recomendada apenas nos parâmetros da consulta acima. Se você inadvertidamente codificar na URL a cadeia de caracteres de consulta inteira (tudo após o ?), as solicitações serão interrompidas.

Além disso, a codificação de URL só é necessária ao se chamar a API REST diretamente. Usar a [biblioteca de cliente .NET](https://msdn.microsoft.com/library/dn951165.aspx) lida com a codificação de URL para você.

**Parâmetros de consulta**

`search=[string]` ‒ o texto de pesquisa a ser usado para sugerir consultas. Deve ter pelo menos três caracteres e não mais que 25 caracteres.

`highlightPreTag=[string]` (opcional) ‒ uma cadeia de caracteres de marca que é anexada no início para pesquisar ocorrências. Deve ser definida com `highlightPostTag`. Caracteres reservados na URL devem ser codificados por percentual (por exemplo, %23, em vez de #).

`highlightPostTag=[string]` (opcional) ‒ uma cadeia de caracteres de marca que é anexada para pesquisar ocorrências. Deve ser definida com `highlightPreTag`. Caracteres reservados na URL devem ser codificados por percentual (por exemplo, %23, em vez de #).

`suggesterName=[string]` ‒ o nome do sugestor conforme especificado na coleção `suggesters` que faz parte da definição do índice. Um `suggester` determina quais campos são examinados em busca de termos de consulta sugeridos. Consulte [Sugestores](#Suggesters) para obter detalhes.

`fuzzy=[boolean]` (opcional, padrão = falso) ‒ quando definido como verdadeiro, essa API encontrará sugestões mesmo que haja um caractere ausente ou substituído no texto de pesquisa. Embora isso proporcione uma experiência melhor em alguns cenários, prejudica o desempenho, pois pesquisas com sugestões difusas são mais lentas e consumem mais recursos.

`searchFields=[string]` (opcional) ‒ a lista de nomes de campos separados por vírgulas para pesquisar o texto de pesquisa especificado. Os campos de destino devem ser habilitados para sugestões.

`$top=#` (opcional, padrão = 5) ‒ o número de sugestões a serem recuperadas. Deve ser um número entre 1 e 100.

`$filter=[string]` (opcional) par uma expressão que filtra os documentos considerados para sugestões.

`$orderby=[string]` (opcional) ‒ uma lista de expressões separadas por vírgulas para classificar os resultados. Cada expressão pode ser um nome de campo ou uma chamada para a função `geo.distance()`. Cada expressão pode ser seguida de `asc` para indicar a ordem crescente e `desc` para indicar a ordem decrescente. O padrão é a ordem crescente. Há um limite de 32 cláusulas para `$orderby`.

`$select=[string]` (opcional) ‒ uma lista de campos separados por vírgulas a serem recuperados. Se não for especificado, somente a chave do documento e o texto de sugestão serão retornados.

`api-version=[string]` (obrigatório). A versão de visualização é `api-version=2015-02-28-Preview`. Consulte Controle de versão de serviço de pesquisa para obter detalhes e versões alternativas.

Observação: para essa operação, a `api-version` é especificada como um parâmetro de consulta.

**Cabeçalhos de solicitação**

A lista a seguir descreve os cabeçalhos de solicitação necessários e opcionais

- `api-key`: a `api-key` é usada para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres exclusivo para a URL do serviço. A solicitação **Sugestões** pode especificar uma chave de administração ou a chave de consulta como a `api-key`.

  Você também precisará do nome de serviço para criar a URL da solicitação. Você pode obter o nome do serviço e a `api-key` por meio do painel de serviço no Portal do Azure. Consulte [Criar um serviço Azure Search no portal](../search-create-service-portal/) para obter ajuda sobre a navegação na página.

**Corpo da solicitação**

Nenhum.

**Resposta**

Código de status: 200 OK é retornado para uma resposta bem-sucedida.

    {
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
        },
        ...
      ]
    }

Se a opção de projeção for usada para recuperar campos, eles serão incluídos em cada elemento da matriz:

    {
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
          <other projected data fields>
        },
        ...
      ]
    }

 **Exemplo**

Recuperar cinco sugestões, em que a entrada de pesquisa parcial é 'lux'

    GET /indexes/hotels/docs/suggest?search=lux&$top=5&suggesterName=sg&api-version=2015-02-28-Preview





<!--HONumber=54-->