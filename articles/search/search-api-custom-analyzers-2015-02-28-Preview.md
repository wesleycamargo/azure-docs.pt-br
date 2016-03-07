<properties
	pageTitle="Analisadores personalizados (API REST da Pesquisa do Azure Versão 2015-02-28-Preview) | Microsoft Azure"
	description="Analisadores personalizados (API REST de Pesquisa do Azure Versão 2015-02-28-Preview)"
	services="search"
	documentationCenter=""
	authors="Yahnoosh"
	manager="pablocas"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.author="jlembicz"
	ms.date="02/18/2016" />

# Analisadores (API REST de Pesquisa do Azure Versão 2015-02-28-Preview)

> [AZURE.NOTE] O suporte aos analisadores personalizados está atualmente em visualização. Você deve estar usando a versão 2015-02-28-Preview da API REST do serviço Pesquisa do Azure para aproveitar esse recurso. Observe que, no momento, os recursos de visualização não foram adicionados ao SDK do .NET, portanto, usar a API REST de visualização é sua única opção de programação no momento.

## Visão geral

Simplificando, a função de um mecanismo de pesquisa de texto completo é processar e armazenar documentos de uma maneira que permita uma consulta e recuperação eficientes. Em um alto nível, tudo se resume a extrair palavras importantes de documentos, colocá-las em um índice e usar o índice para localizar documentos que correspondam às palavras de uma determinada consulta. O processo de extração de palavras de documentos e consultas de pesquisa é chamado de análise léxica. Os componentes que executam a análise léxica são chamados de analisadores. Na Pesquisa do Azure, você pode escolher entre um conjunto de [analisadores independentes de idioma predefinidos](#Analyzers) e [analisadores de idioma específicos](https://msdn.microsoft.com/library/azure/dn879793.aspx). Você também tem a opção para definir seus próprios analisadores personalizados. Um analisador personalizado permite que você assuma o controle do processo de conversão de texto em tokens indexáveis/pesquisáveis. É uma configuração definida pelo usuário composta por um único criador de token predefinido e um ou mais filtros de token. O criador de token é responsável por quebrar o texto em tokens, e os filtros de token são responsáveis por modificar tokens emitidos pelo criador de token.

Entre os cenários populares habilitados pelos analisadores personalizados estão:

- Pesquisa fonética: adicione um filtro fonético para permitir a pesquisa com base no som de uma palavra, e não na ortografia.
- Desabilitar análise léxica: use o analisador de palavra-chave para criar campos de pesquisa que não são analisados
- Pesquisa rápida de prefixo/sufixo: adicione o filtro de token Edge N-gram para indexar prefixos de palavras a fim de habilitar a correspondência rápida de prefixo. Combine-a com o filtro de token Inverso para realizar a correspondência de sufixo.
- Criação de tokens personalizada: por exemplo, use o criador de token Whitespace para dividir as frases em tokens usando o espaço em branco como um delimitador
- Dobra em ASCII: adicione o filtro de dobra em ASCII para normalizar os sinais diacríticos como ö ou ê em termos de pesquisa.

Você pode definir vários analisadores personalizados para variar a combinação de filtros, mas cada campo só pode usar um analisador para análise de indexação e outro para a análise de pesquisa.
 
Esta página fornece uma lista de analisadores, criadores de token e filtros de token com suporte. Você também encontrará uma descrição das alterações na definição de índice, com um exemplo de uso. Para obter uma introdução e explorar os cenários, confira [Analisadores personalizados na Pesquisa do Azure](link). Para saber mais sobre a tecnologia subjacente utilizada na implementação da Pesquisa do Azure, confira [Resumo do pacote de análise (Lucene)](http://lucene.apache.org/core/4_10_3/core/org/apache/lucene/analysis/package-summary.html).


## Analisador padrão

O analisador padrão é o [Apache Lucene Standard](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html).

## Regras de validação

Os nomes de analisadores personalizados, criadores de token e filtros de token precisam ser exclusivos e não podem ser iguais a qualquer um dos analisadores, criadores de token ou filtros de token predefinidos.
 

## Definição de índice com analisadores personalizados 

Você pode definir os analisadores personalizados no momento da criação do índice. Veja abaixo a sintaxe dos componentes de análise personalizada na definição do índice. Veja um exemplo completo [aqui](#Example)

	{
	  	. . .
	 	(standard create index request body)
	 	. . .  
		"analyzers": (optional) [ 
		    {  
		       "name": "name of analyzer",  
		       "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer", 
		       "tokenizer": "tokenizer_name", (tokenizer_name is the name of a tokenizer from the [Tokenizers](#Tokenizers) table)
		       "tokenFilters": [
				  "token_filter_name", (token_filter_name is the name of a token filter from the [Token Filters](#TokenFilters) table)
				  "token_filter_name"
				] (token filters will be applied from left to right)
		    },
			{  
		       "name": "name of analyzer",  
		       "@odata.type": "#analyzer_type", 
		       "option1": value1,
		       "option2": value2,
		       ...  
		    }
		],  
		"tokenizers": (optional) [ 
		    {  
		       "name": "tokenizer_name",  
		       "@odata.type": "#tokenizer_type", 
		       "option1": value1,  
		       "option2": value2, 
		       ...  
		    }
		],
		"tokenFilters": (optional) [ 
		    {  
		       "name": "token_filter_name",  
		       "@odata.type": "#token_filter_type", 
		       "option1": value1,  
		       "option2": value2, 
		       ...  
		    }
		]	
	}

> [AZURE.NOTE] Os analisadores personalizados que você cria não são expostos no Portal do Azure. A única maneira de adicionar um analisador personalizado é por meio do código que faz chamadas para a API REST durante a definição de um índice.

##Atributos de índice para analisadores personalizados, criadores de token e filtros de token 

Esta seção especifica as propriedades de configuração para a seção de analisadores, criadores de token e filtros de token de uma definição de índice.

###Analisadores

<table>
  <tbody>
    <tr>
      <td>
        <b>Nome</b>
      </td>
      <td>
		Deve conter apenas letras, dígitos, espaços, traços ou sublinhados, pode começar e terminar somente com caracteres alfanuméricos e é limitado a 128 caracteres.
      </td>
    </tr>
    <tr>
      <td>
        <b>Tipo</b>
      </td>
      <td>
		Os valores válidos são "#Microsoft.Azure.Search.CustomAnalyzer" ou um nome de analisador da lista de analisadores com suporte. Confira a coluna <b>analyzer_type</b> na tabela [Analyzers](#Analyzers) a seguir.
      </td>
	</tr>
    <tr>
      <td>
        <b>Criador de token</b> (válido apenas para #Microsoft.Azure.Search.CustomAnalyzer)
      </td>
      <td>
		Obrigatório. Deve ser um dos criadores de token predefinidos listados na tabela [Tokenizers](#Tokenizers) abaixo ou qualquer dos criadores de token personalizados definidos na definição do índice.
      </td>
	</tr>
    <tr>
      <td>
        <b>Filtros de token</b> (válido apenas para #Microsoft.Azure.Search.CustomAnalyzer)
      </td>
      <td>
		Todos os filtros de token são um dos filtros de token predefinidos listados na tabela [Token Filters](#TokenFilters) ou qualquer um dos filtros de token personalizados definidos na definição do índice.
      </td>
	</tr>
    <tr>
      <td>
        <b>Opções</b>
      </td>
      <td>
		Devem ser as [valid options](#Analyzers) de um analisador predefinido (não personalizado).
      </td>
	</tr>
  </tbody>
</table>

###Criadores de token

Um criador de token divide o texto contínuo em uma sequência de tokens, assim como quebrar uma frase em palavras. Você pode especificar exatamente um criador de token por analisador personalizado. Se precisar de mais de um criador de token, você pode criar vários analisadores personalizados e atribuí-los campo por campo em seu esquema de índice. Um analisador personalizado pode usar um criador de token predefinido com opções personalizadas ou padrão.

<table>
  <tbody>
    <tr>
      <td>
        <b>Nome</b>
      </td>
      <td>
		Deve conter apenas letras, dígitos, espaços, traços ou sublinhados, pode começar e terminar somente com caracteres alfanuméricos e é limitado a 128 caracteres.
      </td>
    </tr>
    <tr>
      <td>
        <b>Tipo</b>
      </td>
      <td>
		Nome do criador de token da lista de criadores de token com suporte. Confira a coluna <b>tokenizer_type</b> na tabela [Tokenizers](#Tokenizers) a seguir.
      </td>
	</tr>
     <tr>
      <td>
        <b>Opções</b>
      </td>
      <td>
		Devem ser as [valid options](#Tokenizers) de um tipo de criador de token especificado.
      </td>
	</tr>
  </tbody>
</table>

###Filtros de token

Um filtro de token é usado para filtrar ou modificar os tokens gerados por um criador de token. Por exemplo, você pode especificar um filtro de minúsculas que converte todos os caracteres em letras minúsculas. Você pode ter vários filtros de token em um analisador personalizado. Os filtros de token são executados na ordem em que estão listados.

<table>
  <tbody>
    <tr>
      <td>
        <b>Nome</b>
      </td>
      <td>
		Deve conter apenas letras, dígitos, espaços, traços ou sublinhados, pode começar e terminar somente com caracteres alfanuméricos e é limitado a 128 caracteres.
      </td>
    </tr>
    <tr>
      <td>
        <b>Tipo</b>
      </td>
      <td>
		Nome do filtro de token da lista de filtros de token com suporte. Confira a coluna <b>token_filter_type</b> na tabela [Token Filters](#TokenFilters) abaixo.
      </td>
	</tr>
     <tr>
      <td>
        <b>Opções</b>
      </td>
      <td>
		Deve ser as [opções válidas](#TokenFilters) de um tipo de filtro de token especificado.
      </td>
	</tr>
  </tbody>
</table>

<a name="Example"></a>
##Exemplo de definição de índice

Este exemplo de definição de índice inclui um campo usando um analisador personalizado "my\_analyzer" que, por sua vez, usa um criador de token padrão personalizado "my\_standard\_tokenizer" e dois filtros de token: filtro asciifolding personalizado e em minúsculas em "my\_asciifolding".

	{
	   "name":"myindex",
	   "fields":[
	      {
	         "name":"id",
	         "type":"Edm.String",
	         "key":true,
	         "searchable":false
	      },
	      {
	         "name":"text",
	         "type":"Edm.String",
	         "searchable":true,
	         "analyzer":"my_analyzer"
	      }
	   ],
	   "analyzers":[
	      {
	         "name":"my_analyzer",
	         "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
	         "tokenizer":"my_standard_tokenizer",
	         "tokenFilters":[
	            "my_asciifolding",
	            "lowercase"
	         ]
	      }
	   ],
	   "tokenizers":[
	      {
	         "name":"my_standard_tokenizer",
	         "@odata.type":"#Microsoft.Azure.Search.StandardTokenizer",
	         "maxTokenLength":20
	      }
	   ],
	   "tokenFilters":[
	      {
	         "name":"my_asciifolding",
	         "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
	         "preserveOriginal":true
	      }
	   ]
	}

<a name="Analyzers"></a>
##Analisadores

<table>
  <tbody>
	<thead>
	<tr>
		<td>analyzer_name</td>
		<td>tipo_analisador</td>
		<td>Descrição</td>
		<td>Opções</td>
	</tr>
	</thead>
    <tr>
      <td>[keyword](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.KeywordAnalyzer</td>
	  <td>Trata todo o conteúdo de um campo como um único token. Isso é útil para dados como CEPs, IDs e alguns nomes de produto</td>
	  <td></td>
    </tr>
    <tr>
      <td>[pattern](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.PatternAnalyzer</td>
	  <td>Separa de forma flexível o texto em termos por meio de um padrão de expressão regular</td>
	  <td>
		- minúsculo - tipo: bool - caso os termos tenham que ser minúsculos, padrão true - [pattern](http://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) - tipo: cadeia de caracteres - um padrão de expressão regular para corresponder aos separadores de token, padrão: \w+ - [flags](http://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) - tipo: cadeia de caracteres - sinalizadores de expressão regular, padrão: uma cadeia de caracteres vazia - stopwords - tipo: matriz de cadeia de caracteres - uma lista de palavras irrelevantes, padrão: uma lista vazia
	  </td>
    </tr>
    <tr>
      <td>[simple](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.SimpleAnalyzer</td>	 
	  <td>Divide o texto em caracteres que não são letras e os converte em letras minúsculas</td>
	  <td></td>
    </tr>
    <tr>
      <td>[snowball](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.SnowballAnalyzer</td>
	  <td>Um analisador padrão com o [snowball stemming filter](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)</td>
	  <td>
		- linguagem - tipo: cadeia de caracteres- valores permitidos: danish, dutch, english, finnish, french, german, hungarian, italian, norwegian, portuguese, russian, spanish, swedish - palavras irrelevantes - tipo: matriz de cadeia de caracteres - uma lista de palavras irrelevantes, padrão: uma lista vazia
	  </td>
    </tr>
    <tr>
      <td>[standard](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.StandardAnalyzer</td>
	  <td>Analisador Lucene padrão: composto pelo criador de token padrão, filtro minúsculo e filtro parar</td>
	  <td>
		- maxTokenLength - tipo: int - o tamanho máximo do token, padrão: 255. Tokens maiores do que o tamanho máximo são divididos. - palavras irrelevantes - tipo: matriz de cadeia de caracteres - uma lista de palavras irrelevantes, padrão: uma lista vazia
	  </td>
    </tr>
    <tr>
      <td>standardasciifolding.Lucene</td>
      <td>#Microsoft.Azure.Search.StandardAsciiFoldingAnalyzer</td>	  
	  <td>Analisador padrão com filtro de dobra Ascii</td>
	  <td></td>
    </tr>
    <tr>
      <td>[stop](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.StopAnalyzer</td>
	  <td>Divide o texto em caracteres que não são letras, aplica os filtros de token minúsculo e palavras irrelevantes</td>
	  <td>
		- palavras irrelevantes - tipo: matriz de cadeia de caracteres - uma lista de palavras irrelevantes, padrão: uma lista vazia
	  </td>
    </tr>
    <tr>
      <td>[whitespace](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.WhitespaceAnalyzer</td>
	  <td>Um analisador que usa o criador de token espaço em branco.</td>
	  <td></td>
    </tr>
  </tbody>
</table>

<a name="Tokenizers"></a>
##Criadores de token

<table>
  <tbody>
	<thead>
	<tr>
		<td>nome_do_criador_de_token</td>
		<td>tipo_do_criador_de_token</td>
		<td>Descrição</td>
		<td>Opções</td>
	</tr>
	</thead>
    <tr>
      <td>[classic](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.ClassicTokenizer</td>
	  <td>Criador de token com base na gramática e que é adequado para o processamento da maioria dos documentos em idiomas europeus</td>
	  <td>
		- maxTokenLength - tipo: int - o tamanho máximo do token, padrão: 255. Tokens maiores do que o tamanho máximo são divididos. 
	  </td>	  
    </tr>
    <tr>
      <td>[edgeNGram](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.EdgeNGramTokenizer</td>
	  <td>Divide em tokens a entrada de uma borda em n-gramas de tamanho(s) específico(s)</td>
	  <td>
		- minGram - tipo: int - padrão: 1 - maxGram - tipo: int - padrão: 2 - tokenChars - tipo: matriz de cadeia de caracteres - classes de caracteres a serem mantidas nos tokens, valores permitidos: letter, digit, whitespace, punctuation, symbol
	  </td>	  
    </tr>
    <tr>
      <td>[keyword](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.KeywordTokenizer</td>
	  <td>Emite toda a entrada como um único token</td>
	  <td>
		- bufferSize - tipo: int - tamanho do buffer de leitura, padrão: 256
	  </td>
    </tr>
    <tr>
      <td>[letter](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.LetterTokenizer</td>
	  <td>Divide o texto em caracteres que não são letras</td>
	  <td></td>
    </tr>
    <tr>
      <td>[lowercase](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.LowercaseTokenizer</td>
	  <td>Divide o texto em caracteres que não são letras e os converte em letras minúsculas</td>
	  <td></td>
    </tr>
    <tr>
      <td>[nGram](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.NGramTokenizer</td>
	  <td>Divide em tokens a entrada em n-gramas de tamanho(s) específico(s)</td>
	  <td>
		- minGram - tipo: int - padrão: 1 - maxGram - tipo: int - padrão: 2 - tokenChars - tipo: matriz de cadeia de caracteres - classes de caracteres a serem mantidas nos tokens, valores permitidos: letter, digit, whitespace, punctuation, symbol
	  </td>
    </tr>
    <tr>
      <td>[path_hierarchy](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.PathHierarchyTokenizer</td>
	  <td>Criador de token para hierarquias no estilo caminho</td>
	  <td>
		- delimitador - tipo: cadeia de caracteres- padrão: '/' - substituição - tipo: cadeia de caracteres- se for definido, substitui o caractere delimitador, padrão: delimiter - bufferSize - tipo: int - padrão: 1024 - inverso - tipo: bool - se for true, gera o token na ordem inversa, padrão: true - ignorar - tipo: bool - ignora os primeiros tokens, padrão: 0
	  </td>
    </tr>
    <tr>
      <td>[pattern](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.PatternTokenizer</td>
	  <td>Este criador de token usa a correspondência de padrão regex para criar tokens distintos</td>
	  <td>
		 - padrão - tipo: cadeia de caracteres - padrão de expressão regular, padrão: \w+ - [flags](http://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) - tipo: cadeia de caracteres - sinalizadores de expressão regular, padrão: uma cadeia de caracteres vazia - grupo - tipo: int - qual grupo extrair nos tokens, padrão: -1 (split)
	  </td>
    </tr>
    <tr>
      <td>[standard](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.StandardTokenizer</td>
	  <td>Quebra o texto seguindo as [Unicode Text Segmentation rules](http://unicode.org/reports/tr29/)</td>
	  <td>
		 - maxTokenLength - tipo: int - o tamanho máximo do token, assume o padrão de 255. Tokens maiores do que o tamanho máximo são divididos. 
	  </td>
    </tr>
    <tr>
      <td>[uax_url_email](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.UaxEmailUrlTokenizer</td>
	  <td>Cria um token a partir de URLs e emails</td>
	  <td>
		- maxTokenLength - tipo: int - o tamanho máximo do token, assume o padrão de 255. Tokens maiores do que o tamanho máximo são divididos.
	  </td>
    </tr>
    <tr>
      <td>[whitespace](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.WhitespaceTokenizer</td>
	  <td>Divide o texto no espaço em branco</td>
	  <td></td>
    </tr>
  </tbody>
</table>

<a name="TokenFilters"></a>
##Filtros de token

<table>
  <tbody>
	<thead>
	<tr>
		<td>token_filter_name</td>
		<td>tipo_do_filtro_de_token</td>
		<td>Descrição</td>
		<td>Opções</td>
	</tr>
	</thead>
    <tr>
      <td>[arabic_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.ArabicNormalizationTokenFilter</td>
	  <td>Remove todos os caracteres após um apóstrofo (inclusive o próprio apóstrofo)</td>
    </tr>
    <tr>
      <td>[apostrophe](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)</td>
      <td>#Microsoft.Azure.Search.ApostropheTokenFilter</td>
	  <td>Remove todos os caracteres após um apóstrofo</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[asciifolding](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)</td>
      <td>#Microsoft.Azure.Search.AsciiFoldingTokenFilter</td>
	  <td>Converte caracteres Unicode alfabéticos, numéricos e simbólicos que não estejam nos primeiros 127 caracteres ASCII (o bloco Unicode "Latim básico") em seus equivalentes em ASCII, se houver algum</td>
	  <td>
		- preserveOriginal - tipo: bool - se for true, o token orginal será mantido, padrão: false
	  </td>	  
    </tr>
    <tr>
      <td>[cjk_bigram](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)</td>
      <td>#Microsoft.Azure.Search.CjkBigramTokenFilter</td>
	  <td>Forma bigramas dos termos CJK gerados a partir de StandardTokenizer</td>
	  <td>
		- ignoreScripts - tipo: bool - scripts a serem ignorados, valores permitidos: han, hiragana, katakana, hangul. padrão: uma lista vazia - outputUnigrams tipo: bool -- defina como true se você quiser sempre exibir unigramas e bigramas, padrão: false
	  </td>	  
    </tr>
    <tr>
      <td>[cjk_width](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)</td>
      <td>#Microsoft.Azure.Search.CjkWidthTokenFilter</td>
	  <td>Normaliza as diferenças de largura de CJK Dobra variantes de ASCII de largura total em variantes equivalentes no Latim básico, e Katakana de meia largura em equivalentes em kana</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[classic](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)</td>
      <td>#Microsoft.Azure.Search.ClassicTokenFilter</td>
	  <td>Remove os possessivos do inglês, e pontos de acrônimos</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[common_grams](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)</td>
      <td>#Microsoft.Azure.Search.CommonGramTokenFilter</td>
	  <td>Constrói bigramas para termos que ocorrem com frequência durante a indexação. Termos únicos ainda são indexados, com sobreposição de bigramas.</td>
	  <td>
		- commonWords - tipo: matriz de cadeia de caracteres - o conjunto de palavras comuns, padrão: uma lista vazia - ignoreCase - tipo: bool - se for definido como true, as palavras comuns correspondentes não farão distinção entre maiúsculas e minúsculas, padrão: false - queryMode - tipo: bool - gera bigramas e, em seguida, remove palavras comuns e termos individuais seguidos por uma palavra comum, padrão: false
	  </td>	  
    </tr>
    <tr>
      <td>[delimited_payload_filter](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/payloads/DelimitedPayloadTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.DelimitedPayloadTokenFilter</td>
	  <td>Os caracteres antes do delimitador são os "token", aqueles após o delimitador são a carga. Por exemplo, se o delimitador for '|', para a cadeia de caracteres "hello|world", "hello" será o token e "world" será a carga. Você também pode incluir um codificador para converter a carga de uma maneira apropriada (de caracteres em bytes)</td>
	  <td>
		- delimitador - tipo: char - padrão: '|' - codificação - tipo: cadeia de caracteres - valores permitidos: int, float, identity. Padrão: float
	  </td>	  
    </tr>
    <tr>
      <td>[dictionary_decompounder](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.DictionaryDecompounderTokenFilter</td>
	  <td>Decompõe palavras compostas encontradas em muitos idiomas germânicos</td>
	  <td>
		- lista de palavras - tipo: matriz de cadeia de caracteres - a lista de palavras para comparação, padrão: uma lista vazia - minWordSize - tipo: int - somente palavras maiores do que isso são processadas, padrão: 5 - minSubwordSize - tipo: int - somente subpalavras maiores do que isso são exibidas, padrão: 2 - maxSubwordSize - tipo: int - somente subpalavras menores do que isso são exibidas, padrão: 15 - onlyLongestMatch - tipo: bool - adiciona apenas a maior subpalavra correspondente à saída, padrão: false 
	  </td>	  
    </tr>
    <tr>
      <td>[edgeNGram](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.EdgeNGramTokenFilter</td>
	  <td>Gera n-gramas de tamanho(s) específico(s) desde o início a partir da frente ou do verso de um token de entrada</td>
	  <td>
		- minGram - tipo: int - padrão: 1 - maxGram - tipo: int - padrão: 2 - lado - tipo: cadeia de caracteres - especifica a partir de qual lado da entrada a n-grama deve ser gerada. Valores permitidos: front, back
	  </td>	  
    </tr>   
    <tr>
      <td>[elision](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)</td>
      <td>#Microsoft.Azure.Search.ElisionTokenFilter</td>
	  <td>Remove elisões. Por exemplo, "l'avion" (o avião) será convertido para "avion" (avião).</td>
	  <td>
		- artigos - tipo: matriz de cadeia de caracteres - um conjunto de artigos para remoção, padrão: uma lista vazia
	  </td>	  
    </tr>
    <tr>
      <td>[german_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.GermanNormalizationTokenFilter</td>
	  <td>Normaliza os caracteres de alemão de acordo com a heurística do [German2 snowball algorithm](http://snowball.tartarus.org/algorithms/german2/stemmer.html)</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[hindi_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.HindiNormalizationTokenFilter</td>
	  <td>Normaliza o texto em híndi para remover algumas diferenças de variações de ortografia</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[indic_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.IndicNormalizationTokenFilter</td>
	  <td>Normaliza a representação Unicode de texto em idiomas indianos</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[keep](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeepTokenFilter</td>
	  <td>Um filtro de token que mantém apenas os tokens com o texto contido em uma lista especificada de palavras</td>
	  <td>
		- keepWords - tipo: matriz de cadeia de caracteres - uma lista de palavras a serem mantidas, padrão: uma lista vazia - keepWordsCase - tipo: bool - se for true, primeiro coloca todas as palavras em letra minúscula, padrão: false
	  </td>	  
    </tr>
    <tr>
      <td>[keep_types](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/TypeTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeepTypesTokenFilter</td>
	  <td>Mantém os tokens cujos tipos aparecem na lista especificada de tipos permitidos</td>
	  <td>
		- tipos - tipo: matriz de cadeia de caracteres - uma lista de tipos a serem mantidos
	  </td>	  
    </tr>
    <tr>
      <td>[keyword_marker](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeywordMarkerTokenFilter</td>
	  <td>Marca termos como palavras-chave</td>
	  <td>
		- keywords - tipo: matriz de cadeia de caracteres - uma lista de palavras para marcar como palavras-chave, padrão: uma lista vazia - ignoreCase - tipo: bool - se for true, primeiro coloca todas as palavras em letra minúscula, padrão: false
	  </td>	  
    </tr>
    <tr>
      <td>[keyword_repeat](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeywordRepeatTokenFilter</td>
	  <td>Emite cada token de entrada duas vezes, uma vez como palavra-chave e outro não como palavra-chave</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[kstem](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)</td>
      <td>#Microsoft.Azure.Search.KStemTokenFilter</td>
	  <td>Um filtro kstem de alto desempenho para inglês</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[length](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html)</td>
      <td>#Microsoft.Azure.Search.LengthTokenFilter</td>
	  <td>Remove palavras que são muito grandes ou muito pequenas</td>
	  <td>
		- min - tipo: int - o número mínimo, padrão: 0 - max - tipo: int - o número máximo, padrão: valor máximo do número inteiro
	  </td>	  
    </tr>
    <tr>
      <td>[limit](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)</td>
      <td>#Microsoft.Azure.Search.LimitTokenFilter</td>
	  <td>Limita o número de tokens durante a indexação</td>
	  <td>
		- maxTokenCount - tipo: int - número máximo de tokens a serem produzidos, padrão: 1 - consumeAllTokens - tipo: bool - se todos os tokens da entrada devem ser consumidos, mesmo se maxTokenCount for atingido, padrão: false
	  </td>	  
    </tr>
    <tr>
      <td>[lowercase](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)</td>
      <td>#Microsoft.Azure.Search.LowercaseTokenFilter</td>
	  <td>Normaliza o texto do token para letras minúsculas</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[nGram](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.NGramTokenFilter</td>
	  <td>Gera n-gramas de tamanho(s) específico(s)</td>
	  <td>
		- minGram - tipo: int - padrão: 1 - maxGram - tipo: int - padrão: 2
	  </td>	  
    </tr>
    <tr>
      <td>[pattern_capture](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.PatternCaptureTokenFilter</td>
	  <td>Usa regexes Java para emitir vários tokens - um para cada grupo de captura em um ou mais padrões</td>
	  <td>
		- padrões - tipo: matriz de cadeia de caracteres - uma lista de padrões para comparação com cada token - preserveOriginal - tipo: bool - defina como true para retornar o token original, mesmo se um dos padrões corresponderem
	  </td>	  
    </tr>
    <tr>
      <td>[persian_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.PersianNormalizationTokenFilter</td>
	  <td>Aplica normalização para persa</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[phonetic]https://lucene.apache.org/core/4_10_3/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)</td>
      <td>#Microsoft.Azure.Search.PhoneticTokenFilter</td>
	  <td>Cria tokens para correspondências fonéticas.</td>
	  <td>
		- codificador - tipo: cadeia de caracteres  - codificador fonético a ser usado, valores permitidos: metaphone, doublemetaphone, soundex, refinedsoundex, caverphone1, caverphone2, cologne, nysiis, koelnerphonetik, haasephonetik, beidermorse. Padrão: metaphone - substituir - tipo: bool - true se os tokens codificados tiverem que substituir os tokens originais, false se tiverem que ser adicionados como sinônimos, padrão: true
	  </td>	  
    </tr>
    <tr>
      <td>[porter_stem](http://lucene.apache.org/core/4_10_3/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)</td>
      <td>#Microsoft.Azure.Search.PorterStemTokenFilter</td>
	  <td>Transforma o fluxo de token de acordo com o [Porter stemming algorithm](http://tartarus.org/~martin/PorterStemmer/)</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[reverse](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)</td>
      <td>#Microsoft.Azure.Search.ReverseTokenFilter</td>
	  <td>Inverte a cadeia de caracteres de token</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[scandinavian_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.ScandinavianNormalizationTokenFilter</td>
	  <td>Normaliza o uso dos caracteres escandinavos intercambiáveis</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[scandinavian_folding](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)</td>
      <td>#Microsoft.Azure.Search.ScandinavianFoldingNormalizationTokenFilter</td>
	  <td>Dobras caracteres escandinavos åÅäæÄÆ->a e öÖøØ->o. Também discrimina o uso de vogais duplas aa, ae, ao, oe e oo, deixando apenas a primeiro delas.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[shingle](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)</td>
      <td>#Microsoft.Azure.Search.ShingleTokenFilter</td>
	  <td>Cria combinações de tokens como um único token</td>
	  <td>
		- maxShingleSize - tipo: int - padrão: 2 - minShingleSize - tipo: int - padrão: 2 - outputUnigrams - tipo: bool - se for true, o fluxo da saída conterá os tokens de entrada (unigramas) e também shingles, padrão: true - outputUnigramsIfNoShingles - tipo: bool - se for true, substitui o comportamento de outputUnigrams==false para aqueles momentos quando não há shingles disponíveis, padrão: false - tokenSeparator - tipo: cadeia de caracteres - a cadeia de caracteres a ser usada ao unir tokens adjacentes a fim de formar um shingle, padrão: " " - filterToken - tipo: cadeia de caracteres - a cadeia de caracteres a ser inserida para cada posição na qual não há token, padrão: "_"
	  </td>	  
    </tr>
    <tr>
      <td>[snowball](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)</td>
      <td>#Microsoft.Azure.Search.SnowballTokenFilter</td>
	  <td>Filtro de token de lematização Snowaball</td>
	  <td>
		- linguagem - tipo: cadeia de caracteres - valores permitidos: Armenian, Basque, Catalan, Danish, Dutch, English, Finnish, French, German, German2, Hungarian, Italian, Kp, Lovins, Norwegian, Porter, Portuguese, Romanian, Russian, Spanish, Swedish, Turkish
	  </td>	  
    </tr>
	<tr>
      <td>[sorani_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.SoraniNormalizationTokenFilter</td>
	  <td>Normaliza a representação Unicode de texto em sorani.</td>
	  <td></td>	  
    </tr>
	<tr>
      <td>lematizador</td>
      <td>#Microsoft.Azure.Search.StemmerTokenFilter</td>
	  <td>Filtro de lematização específico ao idioma</td>
	  <td>
		- language - tipo: cadeia de caracteres- valores permitidos: - [arabic](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html) - [armenian](http://snowball.tartarus.org/algorithms/armenian/stemmer.html) - [basque](http://snowball.tartarus.org/algorithms/basque/stemmer.html) - [brazilian](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html) - [bulgarian](http://members.unine.ch/jacques.savoy/Papers/BUIR.pdf) - [catalan](http://snowball.tartarus.org/algorithms/catalan/stemmer.html) - [czech](http://portal.acm.org/citation.cfm?id=1598600) - [danish](http://snowball.tartarus.org/algorithms/danish/stemmer.html) - [dutch](http://snowball.tartarus.org/algorithms/dutch/stemmer.html) - [dutch_kp](http://snowball.tartarus.org/algorithms/kraaij_pohlmann/stemmer.html) - [english](http://snowball.tartarus.org/algorithms/porter/stemmer.html) - [light_english](http://ciir.cs.umass.edu/pubfiles/ir-35.pdf) - [minimal_english](http://www.researchgate.net/publication/220433848_How_effective_is_suffixing) - [possessive_english](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html) - [porter2](http://snowball.tartarus.org/algorithms/english/stemmer.html) - [lovins](http://snowball.tartarus.org/algorithms/lovins/stemmer.html) - [finnish](http://snowball.tartarus.org/algorithms/finnish/stemmer.html) - [light_finnish](http://clef.isti.cnr.it/2003/WN_web/22.pdf) - [french](http://snowball.tartarus.org/algorithms/french/stemmer.html) - [light_french](http://dl.acm.org/citation.cfm?id=1141523) - [minimal_french](http://dl.acm.org/citation.cfm?id=318984) - [galician](http://bvg.udc.es/recursos_lingua/stemming.jsp) - [minimal_galician](http://bvg.udc.es/recursos_lingua/stemming.jsp) - [german](http://snowball.tartarus.org/algorithms/german/stemmer.html) - [german2](http://snowball.tartarus.org/algorithms/german2/stemmer.html) - [light_german](http://dl.acm.org/citation.cfm?id=1141523) - [minimal_german](http://members.unine.ch/jacques.savoy/clef/morpho.pdf) - [greek](http://sais.se/mthprize/2007/ntais2007.pdf) - [hindi](http://computing.open.ac.uk/Sites/EACLSouthAsia/Papers/p6-Ramanathan.pdf) - [hungarian](http://snowball.tartarus.org/algorithms/hungarian/stemmer.html) - [light_hungarian](http://dl.acm.org/citation.cfm?id=1141523&amp;dl=ACM&amp;coll=DL&amp;CFID=179095584&amp;CFTOKEN=80067181) - [indonesian](http://www.illc.uva.nl/Publications/ResearchReports/MoL-2003-02.text.pdf) - [irish](http://snowball.tartarus.org/otherapps/oregan/intro.html) - [italian](http://snowball.tartarus.org/algorithms/italian/stemmer.html) - [light_italian](http://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf) - [sorani](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html) - [latvian](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html) - [norwegian](http://snowball.tartarus.org/algorithms/norwegian/stemmer.html) - [light_norwegian](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html) - [minimal_norwegian](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html) - [light_nynorsk](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html) - [minimal_nynorsk](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html) - [portuguese](http://snowball.tartarus.org/algorithms/portuguese/stemmer.html) - [light_portuguese](http://dl.acm.org/citation.cfm?id=1141523&amp;dl=ACM&amp;coll=DL&amp;CFID=179095584&amp;CFTOKEN=80067181) - [minimal_portuguese](http://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf) - [portuguese_rslp](http://www.inf.ufrgs.br//~viviane/rslp/index.htm) - [romanian](http://snowball.tartarus.org/algorithms/romanian/stemmer.html) - [russian](http://snowball.tartarus.org/algorithms/russian/stemmer.html) - [light_russian](http://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf) - [spanish](http://snowball.tartarus.org/algorithms/spanish/stemmer.html) - [light_spanish](http://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf) - [swedish](http://snowball.tartarus.org/algorithms/swedish/stemmer.html) - [light_swedish](http://clef.isti.cnr.it/2003/WN_web/22.pdf) - [turkish](http://snowball.tartarus.org/algorithms/turkish/stemmer.html)
	  </td>	  
    </tr>
	<tr>
      <td>[stemmer_override](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)</td>
      <td>#Microsoft.Azure.Search.StemmerOverrideTokenFilter</td>
	  <td>Quaisquer termos lematizados de dicionários serão marcados como palavras-chave para que não sejam lematizados com lematizadores na cadeia. Devem ser colocados antes de qualquer filtro de lematização.</td>
	  <td>
		- rules - tipo: matriz de cadeia de caracteres - regras de lematização no seguinte formato "word => stem" por exemplo "ran => run", padrão: uma lista vazia
	  </td>	  
    </tr>
	<tr>
      <td>[stop](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)</td>
      <td>#Microsoft.Azure.Search.StopTokenFilter</td>
	  <td>Remove palavras irrelevantes de um fluxo de token</td>
	  <td>
		- palavras irrelevantes - tipo: matriz de cadeia de caracteres - a lista de palavras irrelevantes, padrão: uma lista vazia. - stopwords_list - tipo: cadeia de caracteres - lista predefinida de palavras irrelevantes, valores permitidos: _arabic_, _armenian_, _basque_, _brazilian_, _bulgarian_, _catalan_, _czech_, _danish_, _dutch_, _english_, _finnish_, _french_, _galician_, _german_, _greek_, _hindi_, _hungarian_, _indonesian_, _irish_, _italian_, _latvian_, _norwegian_, _persian_, _portuguese_, _romanian_, _russian_, _sorani_, _spanish_, _swedish_, _thai_, _turkish_, default: _english_ - ignoreCase - tipo: bool - se for true, todas as palavras serão colocadas em minúscula primeiro, padrão: false - removeTrailing - tipo: bool - se for true, ignora o último termo de pesquisa, se for uma palavra irrelevante, padrão: true
	  </td>	  
    </tr>
	<tr>
      <td>[synonym](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)</td>
      <td>#Microsoft.Azure.Search.SynonymTokenFilter</td>
	  <td>Corresponde sinônimos de palavra única ou múltiplas palavras em um fluxo de token</td>
	  <td>
		- sinônimos - tipo: matriz de cadeia de caracteres - lista de sinônimos em um dos dois formatos: - incrível, inacreditável, fabuloso => impressionante - todos os termos à esquerda do símbolo => serão substituídos por todos os termos no lado direito - incrível, inacreditável, fabuloso, impressionante - lista de palavras equivalentes separadas por vírgulas. Defina a opção expandir para alterar como essa lista é interpretada - ignoreCase - tipo: bool - dobras a capitalização da entrada para correspondências, padrão: false - expandir - tipo: bool - se for true, todas as palavras na lista de sinônimos (se a notação = > não for usada) serão mapeadas para si mesmas. A lista a seguir: incrível, inacreditável, fabuloso, impressionante é equivalente a: incrível, inacreditável, fabuloso, impressionante => incrível, inacreditável, fabuloso, impressionante - se for false, a lista a seguir: incrível, inacreditável, fabuloso, impressionante será equivalente a: incrível, inacreditável, fabuloso, impressionante => incrível   
	  </td>	  
    </tr>
	<tr>
      <td>[trim](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)</td>
      <td>#Microsoft.Azure.Search.TrimTokenFilter</td>
	  <td>Corta o espaço em branco à esquerda e à direita dos tokens</td>
	  <td></td>	  
    </tr>
	<tr>
      <td>[truncate](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.TruncateTokenFilter</td>
	  <td>Trunca os termos em um tamanho específico</td>
	  <td>
		- comprimento - tipo: int - opção obrigatória
	  </td>	  
    </tr>
	<tr>
      <td>[unique](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.UniqueTokenFilter</td>
	  <td>Filtra os tokens com o mesmo texto que o token anterior</td>
	  <td>
		- onlyOnSamePosition - tipo: bool - se for definido, remove apenas as duplicatas que estão na mesma posição, padrão: true
	  </td>	  
    </tr>
    <tr>
      <td>[uppercase](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)</td>
      <td>#Microsoft.Azure.Search.UppercaseTokenFilter</td>
	  <td>Normaliza o texto do token para letras maiúsculas</td>
	  <td></td>	  
    </tr>
	<tr>
      <td>[word_delimiter](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)</td>
      <td>#Microsoft.Azure.Search.WordDelimiterTokenFilter</td>
	  <td>Divide as palavras em subpalavras e executa transformações opcionais em grupos de subpalavras</td>
	  <td>
		- generateWordParts - tipo: bool - causa a geração de partes das palavras, por exemplo "PesquisaAzure" -> "Pesquisa" "Azure", padrão: true - generateNumberParts - tipo: bool - causa a geração de subpalavras com números, padrão: true - catenateWords - tipo: bool - causa a concatenação de partes de palavras, por exemplo "Pesquisa-Azure" -> "PesquisaAzure", padrão: false - catenateNumbers - tipo: bool - causa a concatenação de partes com número, por exemplo "1-2" -> "12", padrão: false - catenateAll - tipo: bool - causa a concatenação de todas as partes com subpalavras, por exemplo "Pesquisa-Azure-1" -> "PesquisaAzure1", padrão: false - splitOnCaseChange - tipo: bool - se for true, divide as palavras em caseChange, por exemplo "PesquisaAzure" -> "Pesquisa" "Azure", padrão: true - preserveOriginal - causa a preservação das palavras originais e a adição à lista de subpalavras, padrão: false - splitOnNumerics - tipo: bool - se for true, divide nos números, por exemplo, "Pesquisa1Azure" -> "Pesquisa" "8" "Azure", padrão: true - stemEnglishPossessive - tipo: bool - causa a remoção do "'s" à direita de cada subpalavra, padrão: true - protectedWords - tipo: matriz de cadeia de caracteres - tokens protegidos contra delimitação, padrão: uma lista vazia
	  </td>	  
    </tr>
  </tbody>
</table>

**Consulte também** [API REST do serviço Azure Search](http://msdn.microsoft.com/library/azure/dn798935.aspx) no MSDN <br/> [Criar índice (API de Pesquisa do Azure)](http://msdn.microsoft.com/library/azure/dn798941.aspx) no MSDN<br/>

<!---HONumber=AcomDC_0224_2016-->