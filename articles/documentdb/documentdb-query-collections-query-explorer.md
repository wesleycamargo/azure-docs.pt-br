---
title: 'Gerenciador de Consultas do Banco de Dados de Documentos: um editor de consultas SQL | Microsoft Docs'
description: Saiba mais sobre o Gerenciador de Consultas do Banco de Dados de Documentos, um editor de consultas SQL no portal do Azure para gravar consultas SQL e executá-las em uma coleção do Banco de Dados de Documentos NoSQL.
keywords: gravação de consultas sql, editor de consultas sql
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: monicar
documentationcenter: ''

ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2016
ms.author: anhoh

---
# Gravar, editar e executar consultas SQL para o Banco de Dados de Documentos usando o Gerenciador de Consultas
Este artigo oferece uma visão geral do Gerenciador de Consultas do [Banco de Dados de Documentos do Microsoft Azure](https://azure.microsoft.com/services/documentdb/), uma ferramenta do portal do Azure que permite gravar, editar e executar consultas SQL em uma coleção do [Banco de Dados de Documentos](documentdb-create-collection.md).

1. No portal do Azure, na barra de navegação, clique em **DocumentDB (NoSQL)**. Se **DocumentDB (NoSQL)** não estiver visível, clique em **Mais Serviços** e clique em **DocumentDB (NoSQL)**.
2. No menu de recursos, clique em **Gerenciador de Consultas**.
   
    ![Captura de tela do portal do Azure com o Gerenciador de Consultas realçado](./media/documentdb-query-collections-query-explorer/queryexplorercommand.png)
3. Na folha **Gerenciador de Consultas**, selecione os **Bancos de Dados** e as **Coleções** a serem consultados nas listas suspensas e digite a consulta a ser executada.
   
    As listas suspensas **Banco de dados** e **Coleções** terão sido preenchidas previamente dependendo da situação em que você iniciar o Gerenciador de Consultas.
   
    Uma consulta padrão `SELECT TOP 100 * FROM c` é fornecida. Você pode aceitar a consulta padrão ou construir sua própria consulta usando a linguagem de consulta SQL descrita na [referência rápida da consulta SQL](documentdb-sql-query-cheat-sheet.md) ou no artigo [Consulta SQL e sintaxe SQL](documentdb-sql-query.md) artigo.
   
    Clique em **Executar consulta** para exibir os resultados.
   
    ![Captura de tela da gravação de consultas SQL no Gerenciador de Consultas, um editor de consultas SQL](./media/documentdb-query-collections-query-explorer/queryexplorerinitial.png)
4. A folha **Resultados** exibe a saída da consulta.
   
    ![Captura de tela dos resultados de gravação de consultas SQL no Gerenciador de Consultas](./media/documentdb-query-collections-query-explorer/queryresults1.png)

## Trabalhar com os resultados
Por padrão, o Gerenciador de Consultas retorna resultados em conjuntos de 100. Se sua consulta produzir mais de 100 resultados, basta usar os comandos **Próxima página** e **Página anterior** para percorrer o conjunto de resultados.

![Captura de tela de suporte à paginação do Gerenciador de Consultas](./media/documentdb-query-collections-query-explorer/queryresultspagination.png)

Para as consultas bem-sucedidas, o painel **Informação** contém métricas como a carga de solicitação, o número de viagens de ida e volta feitas pela consulta, o conjunto de resultados em exibição e se há mais resultados, que podem ser acessados usando o comando **Próxima página**, como mencionado anteriormente.

![Captura de tela de informações de consulta do Gerenciador de Consultas](./media/documentdb-query-collections-query-explorer/queryinformation.png)

## Usar várias consultas
Se você estiver usando várias consultas e se quiser alternar rapidamente entre elas, poderá inserir todas as consultas na caixa de texto de consulta da folha **Gerenciador de Consultas**, realçar a que você deseja executar clicar em **Executar consulta** para exibir os resultados.

![Captura de tela da gravação de várias consultas SQL no Gerenciador de Consultas (um editor de consultas SQL) e realçando e executando consultas individuais.](./media/documentdb-query-collections-query-explorer/queryexplorerhighlightandrun.png)

## Adicionar consultas de um arquivo no editor de consultas SQL
Você pode carregar o conteúdo de um arquivo existente usando o comando **Carregar Arquivo**.

![Captura de tela que mostra como carregar consultas SQL de um arquivo no Gerenciador de Consultas usando Carregar arquivo](./media/documentdb-query-collections-query-explorer/loadqueryfile.png)

## Solucionar problemas
Se uma consulta for concluída com erros, o Gerenciador de Consultas exibirá uma lista de erros que podem ajudar em esforços de solução de problemas.

![Captura de tela de erros de consulta do Gerenciador de Consultas](./media/documentdb-query-collections-query-explorer/queryerror.png)

## Executar consultas SQL do Banco de Dados de Documentos fora do portal
O Gerenciador de Consultas no portal do Azure é apenas uma maneira de executar consultas SQL no Banco de Dados de Documentos. Você também pode executar consultas SQL usando o [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou [SDKs de cliente](documentdb-sdk-dotnet.md). Para saber mais sobre como usar esses outros métodos, veja [Executar consultas SQL](documentdb-sql-query.md#executing-sql-queries)

## Próximas etapas
Para saber mais sobre a gramática SQL do DocumentDB com suporte no Gerenciador de Consultas, veja o artigo [Consulta SQL e sintaxe SQL](documentdb-sql-query.md) ou imprima a [referência rápida de consulta SQL](documentdb-sql-query-cheat-sheet.md). Talvez você também goste de experimentar o [Query Playground](https://www.documentdb.com/sql/demo), onde poderá testar as consultas online usando um conjunto de dados de exemplo.

<!---HONumber=AcomDC_0831_2016-->