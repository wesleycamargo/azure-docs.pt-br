<properties
    pageTitle="Exemplos de consulta Lucene para a Pesquisa do Azure | Pesquisa do Microsoft Azure"
    description="Sintaxe de consulta Lucene para pesquisa difusa, pesquisa por proximidade, aumento de termos, pesquisa com expressão regular e pesquisa com curinga."
    services="search"
    documentationCenter=""
	authors="LiamCa"
	manager="pablocas"
	editor=""
    tags="Lucene query analyzer syntax"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="03/25/2016"
    ms.author="liamca"
/>

# Exemplos de sintaxe de consulta Lucene para criar consultas na Pesquisa do Azure

Uma alternativa à [sintaxe de consulta simples](https://msdn.microsoft.com/library/azure/dn798920.aspx) padrão é usar o [Analisador de Consulta Lucene na Pesquisa do Azure](https://msdn.microsoft.com/library/azure/mt589323.aspx). O Analisador de Consulta Lucene dá suporte a construções de consulta mais complexas, como consultas com escopo de campo, pesquisa difusa, pesquisa por proximidade, aumento de termos e pesquisa de expressão regular.

Neste artigo, você pode percorrer os exemplos que exibem a sintaxe de consulta e os resultados lado a lado. Os exemplos são executados em um índice pré-carregado da Pesquisa no [JSFiddle](https://jsfiddle.net/), um editor de código online para testar o script e o HTML.

Clique com botão direito do mouse nas URLs de exemplo de consulta para abrir o JSFiddle em uma janela separada do navegador.

> [AZURE.NOTE] Os exemplos a seguir aproveitam um índice de pesquisa que consiste em trabalhos disponíveis com base em um conjunto de dados fornecido pela iniciativa [OpenData da cidade de Nova York](https://nycopendata.socrata.com/). Esses dados não devem ser considerados atuais ou completos. O índice está em um serviço de área restrita fornecido pela Microsoft. Você não precisa de uma assinatura do Azure ou da Pesquisa do Azure para experimentar essas consultas.

## Definir o Analisador de Consulta Lucene na solicitação de pesquisa

Para usar o Analisador de Consulta Lucene, defina o parâmetro de pesquisa **queryType** para especificar qual analisador será usado. Você especificará o **queryType** em todas as solicitações. Os valores válidos incluem **simple**|**full**, com **simple** como o padrão. Veja [Pesquisar documentos (API REST do serviço Pesquisa do Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx) para obter detalhes sobre a especificação de parâmetros de consulta.

**Exemplo 1** – clique com o botão direito do mouse no trecho da consulta a seguir para abri-la em uma nova página do navegador que carregue o JSFiddle e execute a consulta. Essa consulta retorna documentos de nosso índice de trabalhos (carregado em um serviço de área restrita):
- [&queryType=full&search=*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*)

## Operação de consulta por campo

Nos exemplos a seguir, você especificará uma construção **nomedocampo:termodepesquisa** para definir uma operação de consulta por campo, onde o campo é uma única palavra e o termo de pesquisa também é uma única palavra ou uma frase, opcionalmente com operadores boolianos. Alguns exemplos incluem o seguinte:

- cargo:sênior NÃO júnior
- estado:"Nova York" E "Nova Jersey"

Coloque várias cadeias de caracteres entre aspas se quiser que ambas as cadeias de caracteres sejam avaliadas como uma única entidade, como neste caso, pesquisar duas cidades distintas no campo de local. Além disso, verifique se o operador está em maiúsculas, como você pode ver com NÃO e E.

O campo especificado em **nomedocampo:termodepesquisa** deve ser um campo pesquisável. Veja [Criar índice (API REST do Serviço de Pesquisa do Azure)](https://msdn.microsoft.com/library/azure/dn798941.aspx) para obter detalhes sobre como os atributos de índice são usados em definições de campo.

## Pesquisa difusa

Uma pesquisa difusa encontra correspondências em termos com uma construção semelhante. De acordo com a [documentação do Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), as pesquisas difusas se baseiam na [distância de Damerau-Levenshtein](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance).

Para fazer uma pesquisa difusa, use o símbolo til "~" no final de uma única palavra com um parâmetro opcional, um valor entre 0 e 2, que especifica a distância de edição. Por exemplo, "mar~" ou "mar~1" retornaria mar, amar e maré.

**Exemplo 2** – clique com o botão direito do mouse no trecho de consulta a seguir para experimentá-lo. Essa consulta pesquisa cargos com o termo sênior neles, mas não júnior:

- [&queryType=full&search= cargo:sênior NÃO júnior](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:senior+NOT+junior)

## Pesquisa por proximidade

As pesquisas de proximidade são usadas para localizar termos que estejam próximos um do outro em um documento. Insira um símbolo til "~" no final de uma frase seguida pelo número de palavras que criam o limite de proximidade. Por exemplo, "hotel aeroporto"~5 encontrará os termos hotel e aeroporto em cinco palavras uma da outra em um documento.

**Exemplo 3** – clique com o botão direito do mouse no trecho de consulta a seguir. Essa consulta pesquisa os trabalhos com o termo associado (onde ele é escrito incorretamente):

- [&queryType=full&search= cargo:asosiato~](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:asosiate~)

**Exemplo 4** – clique com o botão direito do mouse na consulta. Procure trabalhos com o termo “analista sênior”, onde ele estiver separado por até uma palavra:

- [&queryType=full&search=cargo:"analista sênior"~1](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~1)

**Exemplo 5** - experimente novamente ao remover as palavras entre o termo "analista sênior".

- [&queryType=full&search=cargo:"analista sênior"~0](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~0)

## Aumento de termos

O aumento de termos refere-se ao aumento da classificação de um documento caso ele contenha o termo aumentado, em relação aos documentos que não contêm o termo. Isso é diferente dos perfis de pontuação, já que os perfis de pontuação aumentam determinados campos, em vez de termos específicos. O exemplo a seguir ajuda a ilustrar as diferenças.

Considere um perfil de pontuação que aumente as correspondências em um determinado campo, como **gênero** no exemplo de índice de loja de música. O aumento de termos pode ser usado para melhorar a posição de determinados termos de pesquisa. Por exemplo, "rock^2 eletrônico" melhorará a posição dos documentos que contêm os termos de pesquisa no campo **gênero**, à frente de outros campos pesquisáveis no índice. Além disso, os documentos com o termo de pesquisa "rock" serão mais bem classificados do que o outro termo de pesquisa "eletrônico" como resultado do valor de aumento de termo (2).

Para aumentar um termo, use o sinal de interpolação, "^", com um fator de aumento (um número) no final do termo que você está pesquisando. Quanto maior o fator de aumento, mais relevante será o termo em relação a outros termos de pesquisa. Por padrão, o fator de aumento é 1. Embora o fator de aumento deva ser positivo, ele pode ser menor do que 1 (por exemplo, 0,2).

**Exemplo 6** – clique com o botão direito do mouse na consulta. Procure trabalhos com o termo "analista de computador", onde podemos ver que não há resultados com ambas as palavras, computador e analista, ainda que trabalhos de analista estejam na parte superior dos resultados.

- [&queryType=full&search=cargo:analista de computador](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

**Exemplo 7** -tente novamente, desta vez aumentando os resultados com o termo computador em relação ao termo analista, caso ambas palavras não existam.

- [&queryType=full&search=cargo:computador^2 analista](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

## Expressão regular

Uma pesquisa de expressão regular encontra uma correspondência com base no conteúdo entre as barras "/", como documentado na [classe RegExp](http://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).

**Exemplo 8** – clique com o botão direito do mouse na consulta. Procure trabalhos com o termo Sênior ou Júnior.

- `&queryType=full&$select=business_title&search=business_title:/(Sen|Jun)ior/`

A URL para este exemplo não será renderizada corretamente na página. Como solução alternativa, copie a URL abaixo e cole-a no endereço de URL do navegador: `http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26queryType=full%26$select=business_title%26search=business_title:/(Sen|Jun)ior/)`


## Pesquisa curinga

Você pode usar a sintaxe geralmente reconhecida para pesquisas com vários caracteres curinga (*) ou um caractere curinga (?). Observe que o analisador de consulta Lucene oferece suporte ao uso desses símbolos com um único termo e não uma frase.

**Exemplo 9** – clique com o botão direito do mouse na consulta. Procure os trabalhos que contenham o prefixo 'prog', que inclui cargos com os termos programação e o programador neles.

- [&queryType=full&$select=cargo&search=cargo:prog*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26queryType=full%26$select=business_title%26search=business_title:prog*)

Não é possível usar um símbolo * ou ? como o primeiro caractere de uma pesquisa.


## Próximas etapas

Tente especificar o Analisador de Consulta Lucene em seu código. Os links a seguir explicam como configurar consultas de pesquisa para o .NET e para a API REST. Os links usam a sintaxe simples padrão e, portanto, será necessário aplicar o que você aprendeu neste artigo para especificar o **queryType**.

- [Consultar seu índice da Pesquisa do Azure usando o SDK do .NET](search-query-dotnet.md)
- [Consultar seu índice da Pesquisa do Azure usando a API REST](search-query-rest-api.md)


 

<!---HONumber=AcomDC_0330_2016-->