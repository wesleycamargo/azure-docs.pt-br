<properties
	pageTitle="Como pesquisar dados do StackExchange usando a Pesquisa do Azure | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
	description="Saiba como executar pesquisas REST usando a Pesquisa do Azure, um serviço de pesquisa hospedado na nuvem do Microsoft Azure."
	services="search"
	documentationCenter=""
	authors="liamca"
	manager="pablocas"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="11/04/2015"
	ms.author="liamca"/>

# Como pesquisar dados do StackExchange usando a Pesquisa do Azure

Este artigo é um passo a passo que destaca alguns dos principais recursos de pesquisa de texto completo que podem ser realizados com a [Pesquisa do Azure](https://azure.microsoft.com/pt-BR/services/search/). Ele utiliza dados [disponibilizados](https://archive.org/details/stackexchange) pelo Stack Exchange para uso de licença Creative Commons com a [atribuição](http://blog.stackoverflow.com/2009/06/attribution-required/) a seguir.

## Introdução

Para destacar alguns desses recursos, criei um índice da Pesquisa do Azure para você experimentar, que contém dados do StackExchange do Programador de 14 de outubro de 2015. OBSERVAÇÃO: também mostrarei como criar seu próprio índice da Pesquisa do Azure com esses dados posteriormente.

Vamos começar com uma consulta de pesquisa de texto completo realmente simples em que os usuários podem digitar a palavra “azure” para encontrar quaisquer postagens do StackExchange relacionadas ao Azure. Experimente clicando no link para ver isso em ação:

> [http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure](http://fiddle.jshell.net/liamca/gkvfLe6s/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure)

Neste exemplo, simplesmente transmitimos a palavra “azure” como um parâmetro de pesquisa e exibimos os resultados formatados em JSON retornados. Veja alguns exemplos de consultas que você pode experimentar.

-	`Faceting`: depois que o usuário pesquisar o conjunto de dados, conseguir filtrar os dados é uma ótima maneira de ajudar a navegar pelos resultados. Para implementar isso, você normalmente começa com um conjunto de categorias (facetas) que são exibidos para o usuário. Veja alguns exemplos de facetas que desejamos aproveitar:
  -	**Marcas**: muitas das perguntas têm marcas associadas a elas para permitir que os usuários façam uma busca detalhada em categorias específicas
  -	**Datas**: um usuário talvez só deseje ver as perguntas que foram feitas ou respondidas em um período de tempo específico
  -	**Usuário**: talvez você só deseje ver ou limitar os resultados de usuários específicos. Neste exemplo, pesquisaremos “azure”, mas retornaremos as contagens de faceta para os nomes de usuário tagsCollection e acceptedAnswerDisplayName.

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure%26facet=tagsCollection%26facet=acceptedAnswerDisplayName>

-	`Filtering`: depois que um usuário escolher uma faceta, talvez você deseje realizar outra pesquisa, mas utilizar um filtro para limitar os resultados desse valor de faceta. Por exemplo, para pesquisar “Azure”, mas limitar os resultados para onde houver uma marca de “arquitetura” organizada por viewCount em ordem decrescente:

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure%26$filter=tagsCollection/any(t:+t+eq+'architecture')%26$orderby=viewCount+desc>

-	`Spelling Mistakes`: nossa nova (visualização) dá suporte para as [Expressões de Consulta da Lucene](https://msdn.microsoft.com/library/mt589323.aspx) e também permite que você execute algumas consultas bem interessantes, como a correspondência difusa dos resultados e limitação da pesquisa a campos específicos. Este exemplo procura a palavra “visualizar” no campo de título, mas o ~ indica a correspondência difusa, o que significa que resultados como “visualize” e “visualizando” também serão retornados.

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28&search%3Dtitle%3Avisualise~%26querytype%3Dfull%26searchMode%3Dall%26%24select%3Dtitle>

-	`Scoring and Tuning`: os [Perfis de Pontuação](https://msdn.microsoft.com/library/azure/dn798928.aspx) são extremamente úteis para ajudá-lo a ajustar os resultados retornados pelo serviço de pesquisa. Na verdade, agora também podemos usar as [Expressões de Consulta da Lucene](https://msdn.microsoft.com/library/mt589323.aspx) para aplicar a pontuação a campos e termos individuais imediatamente. Por exemplo, se desejarmos procurar as palavras “visualizar” ou “gráfico” no campo do título, mas dar mais importância para itens que contenham a palavra “gráfico”, podemos fazer isso:

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26search%3Dtitle%3Avisualise+OR+title%3Achart%5E3+%26querytype%3Dfull%26searchMode%3Dall%26%24select%3Dtitle>

  Há muitos outros campos neste conjunto de dados que podem ser usados para melhorar os resultados relevantes para os usuários. Por exemplo, posso usar:

  -	A pontuação de **Magnitude** em relação a campos numéricos como answerCount, commentCount, favoriteCount e viewCount para fornecer um aumento dos resultados da pesquisa se eles tiverem contagens altas.
  -	A pontuação de **Atualização** em campos de data/hora como creationDate e lastActivityDate para melhorar itens que foram criados ou que estiveram ativos mais recentemente
  -	**Pesos de campos** para indicar que, se a pesquisa de texto for encontrada no corpo da pergunta, isso será mais relevante do que se for encontrada na resposta.

Outras coisas que talvez você deseje experimentar incluem:

-	[`Suggestions`](https://msdn.microsoft.com/library/azure/mt131377.aspx): conforme os usuários digitam na caixa de pesquisa, será conveniente usar campos como Título, Marcas e Nome de Usuário para o preenchimento automático.  

-	`Recommendations`: muitas vezes você precisará de ferramentas como o Apache Mahout ou o Aprendizado de Máquina do Azure para ajudá-lo a criar recomendações que permitem mostrar perguntas semelhantes que possam ser de interesse para os usuários; porém, felizmente, esse conjunto de dados já tem algumas recomendações.

Fique à vontade para experimentar com essa página do JSFiddle para experimentar diferentes tipos de consultas. Se desejar saber mais sobre como esse índice foi criado, continue lendo. Fique à vontade também para entrar em contato comigo diretamente em minha conta do Twitter [@liamca](https://twitter.com/liamca).

## Como este Índice da Pesquisa do Azure foi criado?

Brent já fez a maior parte do trabalho pesado mostrando como preparar os dados em um banco de dados SQL. Se desejar realizar este processo de preparo dos dados, confira seu [tutorial aqui](http://www.brentozar.com/archive/2014/01/how-to-query-the-stackexchange-databases/). Caso contrário, você pode simplesmente aproveitar o Banco de Dados SQL do Azure que eu já pré-populei com alguns dados de 15 de outubro de 2015 ou experimentar o índice da Pesquisa do Azure que eu criei. Os detalhes estão descritos abaixo na seção “Importando dados do Banco de Dados SQL do Azure preparado”. A única coisa que fiz além do que Brent descreveu foi criar uma Exibição em meu Banco de Dados SQL do Azure que será usada pelo [Indexador da Pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn946891.aspx) para rastrear e receber dados de um grupo de tabelas no qual estou interessado em usar. Veja como essa Exibição é definida.

    CREATE VIEW StackExchangeSearchData as
    SELECT
          PQ.[Id]
          ,PQ.[AcceptedAnswerId]
          ,PQ.[AnswerCount]
          ,PQ.[Body]
          ,PQ.[ClosedDate]
          ,PQ.[CommentCount]
          ,PQ.[CommunityOwnedDate]
          ,PQ.[CreationDate]
          ,PQ.[FavoriteCount]
          ,PQ.[LastActivityDate]
          ,PQ.[LastEditDate]
          ,PQ.[LastEditorDisplayName]
    	  ,PUQ.DisplayName OwnerDisplayName
    	  ,PUQ.Reputation OwnerReputation
          ,PQ.[Score]
          ,reverse(REPLACE(LTRIM(REPLACE(reverse(REPLACE(REPLACE (PQ.[Tags], '>' , '],' ), '<' , '[' )), ISNULL(',', '0'), ' ')), ' ', ISNULL(',', '0'))) TagsCollection
          ,PQ.[Title]
          ,PQ.[ViewCount]
    	  ,PA.[Body] AcceptedAnswerBody
    	  ,PA.[Score] AcceptedAnswerScore
    	  ,PUQ.DisplayName AcceptedAnswerDisplayName
    	  ,PUQ.Reputation AcceptedAnswerReputation
    	  ,PA.[FavoriteCount] AcceptedAnswerFavoriteCount
    	  ,PL.[RelatedPostId]
      FROM [StackExchange].[dbo].[Posts] PQ
      LEFT OUTER JOIN [StackExchange].[dbo].[Posts] PA
      and PQ.AcceptedAnswerId = PA.Id
      LEFT OUTER JOIN [StackExchange].[dbo].[PostLinks] PL
      on PQ.Id = PL.Id
      LEFT OUTER JOIN [StackExchange].[dbo].[Users] PUQ
      on PQ.OwnerUserId = PUQ.Id
      LEFT OUTER JOIN [StackExchange].[dbo].[Users] PUA
      on PA.[OwnerUserId] = PUA.Id
      WHERE PQ.PostTypeId = 1

Depois de fazer isso, é possível usar o [Portal do Azure](https://portal.azure.com) “Importar dados” do Modo SQL do Azure acima que, por sua vez, criará um índice da Pesquisa do Azure com base no esquema dos campos no Modo. Se desejar usar o banco de dados SQL do Azure que eu preparei, veja abaixo a cadeia de conexão Somente Leitura que você pode usar:

    Server=tcp:azs-playground.database.windows.net,1433;Database=StackExchange;User ID=reader@azs-playground;
    Password=EdrERBt3j6mZDP;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;

<!---HONumber=Nov15_HO3-->