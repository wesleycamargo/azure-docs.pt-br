<properties 
	pageTitle="Criar, editar e executar consultas SQL em uma coleção do Banco de Dados de Documentos usando o Gerenciador de Consultas | Azure" 
	description="Saiba mais sobre o Gerenciador de Consultas do Banco de Dados de Documentos, uma ferramenta de Portal de Visualização do Azure para criar, editar e executar consultas SQL em uma coleção do Banco de Dados de Documentos." 
	services="documentdb" 
	authors="stephbaron" 
	manager="johnmac" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2/13/2015" 
	ms.author="stbaro"/>

# Criar, editar e executar consultas SQL em uma coleção do Banco de Dados de Documentos usando o Gerenciador de Consultas #

Este artigo fornece uma visão geral do Gerenciador de Consultas do [Banco de Dados de Documentos do Microsoft Azure](http://azure.microsoft.com/services/documentdb/), uma ferramenta do Portal de Visualização do Azure que habilita criar, editar e executar consultas em uma coleção do Banco de Dados de Documentos. 

Depois de concluir este tutorial, você poderá responder às seguintes perguntas:  

-	Como posso criar, editar e executar facilmente consultas em uma coleção do Banco de Dados de Documentos por meio de um navegador da Web?
-	Como eu posso navegar facilmente entre as páginas de resultados de consultas do Banco de Dados de Documentos por meio de um navegador da Web?
-	Como eu posso solucionar erros de sintaxe com minha consulta do Banco de Dados de Documentos? 

##<a id="Launch"></a>Iniciar e navegar pelo Gerenciador de Consultas##

O Gerenciador de Consultas pode ser iniciado por meio de qualquer conta, banco de dados ou lâmina de coleção do Banco de Dados de Documentos.
  
1. Na parte inferior de cada lâmina há uma lente de **Ferramentas de Desenvolvedor** que contém o **Gerenciador de Consultas**.
	
	![Screenshot of Query Explorer part](./media/documentdb-query-collections-query-explorer/queryexplorerpart.png) 

2. Basta clicar no bloco para iniciar o Gerenciador de Consultas.

	As caixas das listas suspensas **Banco de dados** e **Coleção** são preenchidas previamente dependendo da situação em que você inicia o Gerenciador de Consultas.  Por exemplo, se você iniciá-lo por meio de uma lâmina de banco de dados, o banco de dados atual será preenchido. Se iniciá-lo por meio de uma lâmina de coleção, a coleção atual será preenchida.

	![Screenshot of Query Explorer](./media/documentdb-query-collections-query-explorer/queryexplorerinitial.png)

##<a id="Create"></a>Criar, editar e executar consultas com o Gerenciador de Consultas##

O Gerenciador de Consultas permite criar, editar e executar consultas facilmente em uma coleção do Banco de Dados de Documentos, e inclui o realce de valores e palavras-chave básicas para aprimorar a experiência de criação de consultas.  

- Ao abrir o Gerenciador de Consultas inicialmente, uma consulta padrão SELECT * FROM c é fornecida.  Você pode aceitar a consulta padrão ou construir a sua e clicar no botão **Executar consulta** para exibir os resultados. O Gerenciador de Consultas dá suporte à linguagem de consulta SQL do Banco de Dados de Documentos, conforme descrito em [Consultar o Banco de Dados de Documentos](documentdb-sql-query.md).

	![Screenshot of Query Explorer query results](./media/documentdb-query-collections-query-explorer/queryresults1.png) 

- Por padrão, o Gerenciador de Consultas retorna resultados em conjuntos de 100.  Se sua consulta produz mais de 100 resultados, basta usar os comandos **Próxima página** e **Página anterior** para percorrer o conjunto de resultados.

	![Screenshot of Query Explorer pagination support](./media/documentdb-query-collections-query-explorer/queryresultspagination.png)

- Consultas bem-sucedidas fornecem informações como a carga de solicitação, o conjunto de resultados em exibição e se há mais resultados, que podem ser acessados usando o comando **Próxima página**, como mencionado anteriormente.

	![Screenshot of Query Explorer query information](./media/documentdb-query-collections-query-explorer/queryinformation.png)

- Da mesma forma, se uma consulta for concluída com erros, o Gerenciador de Consultas exibe uma lista de erros que podem ajudar em esforços de solução de problemas.

	![Screenshot of Query Explorer query errors](./media/documentdb-query-collections-query-explorer/queryerror.png)

##<a name="NextSteps"></a>Próximas etapas

- Para saber mais sobre o Banco de Dados de Documentos, clique [aqui](http://azure.com/docdb).
- Para saber mais sobre a gramática SQL do Banco de Dados de Documentos com suporte pelo Gerenciador de Consultas, clique [aqui](documentdb-sql-query.md).

<!--HONumber=49-->