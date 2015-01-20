<properties title="What's New in the Latest SQL Database Update V12 (preview)" pageTitle="O que há de novo na atualização mais recente da V12 do banco de dados SQL (visualização)" description="Lista e descreve as melhorias mais recentes para o banco de dados do SQL do Microsoft Azure, a visualização da versão 12, que estão disponíveis a partir de dezembro de 2014." metaKeywords="Azure, SQL DB, Update, Preview, V12, What's New, Get Started" services="sql-database" documentationCenter="" authors="Gene Milener" manager="jhubbard, jeffreyg" videoId="" scriptId=""/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="genemi"/>

<!--
Latest update to topic:
GeneMi  ,  2014-Dec-10 Wednesday 11:56am;
Applied feedback of JG, JH, JM (Added back Joanne's marketing text.).
-->


# O que há de novo na atualização mais recente da V12 do banco de dados SQL (visualização)


Este tópico descreve o banco de dados do SQL Azure V12 mais recente (visualização). A nova visualização fornece quase total compatibilidade com o mecanismo do Microsoft SQL Server. E a visualização traz mais desempenho para os clientes Premium. Esses aprimoramentos ajudam a simplificar as migrações de aplicativos do SQL Server no Azure e ajudar os clientes que têm cargas mais pesadas de trabalho do banco de dados. 

Essa visualização marca a primeira etapa para fornecer a próxima geração do serviço Azure SQL Database. Ela oferece aos clientes mais compatibilidade, flexibilidade e desempenho. Testes internos da visualização na camada de serviço Premium mostraram que algumas consultas agora são concluídas em uma fração do tempo que elas levam no banco de dados do SQL Azure Premium atual. Melhorias ainda maiores foram vistas em alguns cenários que se beneficiam com a tecnologia de columnstore na memória.

O caminho para planejar e implementar a visualização da V12 começa [aqui](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/).


### Principais destaques


Os principais destaques dessa visualização da V12 incluem o seguinte:

- **Gerenciamento mais fácil** de grandes bancos de dados para oferecer suporte a cargas de trabalho mais pesadas com consultas paralelas (Premium somente), [particionamento de tabela](http://msdn.microsoft.com/library/ms187802.aspx), [indexação online](http://msdn.microsoft.com/library/ms188388.aspx), recompilações de grandes índices sem preocupações com remoção do limite de tamanho de 2 GB e mais opções na instrução de[alter database](http://msdn.microsoft.com/library/bb522682.aspx).

- **Suporte para as principais funções de programação** para orientação de design do aplicativo mais robustos com [integração CLR](http://msdn.microsoft.com/library/ms189524.aspx), funções da janela [T-SQL](http://msdn.microsoft.com/library/ms189461.aspx), [índices XML](http://msdn.microsoft.com/library/bb934097.aspx), e [o controle de alterações](http://msdn.microsoft.com/library/bb933875.aspx) para dados.

- **Desempenho inovador** com suporte para consultas [columnstore](http://msdn.microsoft.com/library/gg492153.aspx) na memória (somente para a camada Premium) para menores cargas de trabalho analíticas e armazém de dados.

- **Monitoramento e Solução de problemas** foram aprimorados com visibilidade em mais de 100 novas exibições de tabela em um conjunto expandido de Exibições de Gerenciamento de banco de dados ([DMV](http://msdn.microsoft.com/library/ms188754.aspx))

- **Novo nível de desempenho S3 na camada padrão:** oferece mais flexibilidade de preço entre Standard e Premium. O S3 fornecerá mais DTU (unidades de taxa de transferência de banco de dados) e todos os recursos disponíveis na camada Standard.

## Detalhes dos aprimoramentos da visualização da nova V12 explicados

Esta seção menciona e explica os novos recursos em cada categoria.


### Categoria: Gerenciamento de banco de dados expandido


| Recurso | Descrição |
| :--- | :--- |
| As Limitações anteriores | do Particionamento de tabela [ no particionamento de tabela](http://msdn.microsoft.com/library/ms190787.aspx) foram eliminadas. |
| Transações maiores | <p>Com a visualização da V12 você não está mais limitado a um máximo de 2 GB de modificações de dados em uma única transação.</p> <p>Um benefício é que a recompilação de um índice grande não está mais limitada pelo limite de tamanho de transação de 2 GB.</p> Para obter informações gerais, consulte [Limites de recursos de banco de dados de SQL Azure](http://msdn.microsoft.com/library/azure/dn338081.aspx). |
| Compilação e recompilação de índices online | <p>Antes da visualização V12, o banco de dados do SQL Azure geralmente suportava a cláusula (ONLINE = ON) da instrução ALTER INDEX, mas isso não era suportado para coluna de tipo de índices em um BLOB. Agora a visualização da V12 é compatível com (ONLINE = ON) até mesmo para índices em colunas BLOB.</p> <p>O recurso ONLINE permite consultas para aproveitar um índice, mesmo enquanto o índice estiver sendo recompilado.</p> |
| Suporte a CHECKPOINT | Com a visualização V12 você pode emitir a instrução CHECKPOINT T-SQL para o banco de dados. |
| Mais opções de ALTER DATABASE | A visualização V12 oferece suporte a mais opções que estão disponíveis na instrução ALTER DATABASE. <p> </p> Para obter mais informações, consulte [ALTER DATABASE (Transact-SQL)](http://msdn.microsoft.com/library/ms174269.aspx) ou a [Referência Transact-SQL do Azure SQL Database](http://msdn.microsoft.com/library/azure/ee336281.aspx). |
| Mais comandos DBCC | A visualização V12 suporta os seguintes comandos DBCC adicionais: <p> </p> CHECKALLOC <br/> CHECKCONSTRAINTS <br/> CHECKDB <br/> CHECKFILEGROUP <br/> CHECKIDENT <br/> CHECKTABLE <br/><br/> CLEANTABLE <br/> DBREINDEX <br/> INDEXDEFRAG <br/> INPUTBUFFER <br/> OPENTRAN <br/> PROCCACHE <br/> SHOWCONTIG <br/> SQLPERF <br/> TRACESTATUS <br/>UPDATEUSAGE <br/> USEROPTIONS |


### Categoria: Suporte a programação e aplicativos


| Recurso | Descrição |
| :--- | :--- |
| Funções de janela em consultas do T-SQL | As funções de janela ANSI são acessadas com a [cláusula OVER](http://msdn.microsoft.com/library/ms189461.aspx). <p></p> Itzik Ben-Gan possui uma excelente [publicação no blog](http://sqlmag.com/sql-server-2012/how-use-microsoft-sql-server-2012s-window-functions-part-1) que explica detalhadamente as funções de janela e a cláusula OVER. |
| Integração CLR .NET | A CLR (common language runtime) do .NET Framework está integrada na visualização V12. <p></p> Somente assemblies SAFE que não eram totalmente compiladas para código binário são suportadas. Para obter detalhes, consulte [Restrições do Modelo de programação de integração de CLR](http://msdn.microsoft.com/library/ms403273.aspx). <p></p> Você pode encontrar informações sobre esse recurso nos tópicos a seguir: <br/> * [Introdução à integração de CLR do SQL Server](http://msdn.microsoft.com/library/ms254498.aspx) <br/> * [Criar ASSEMBLY Transact-SQL)](http://msdn.microsoft.com/library/ms189524.aspx). |
| Controle de alterações de dados | O controle de alterações de dados agora pode ser configurado no banco de dados ou no nível de tabela. <p></p> Para obter informações sobre o controle de alterações, consulte [Sobre Controle de transações (SQL Server)](http://msdn.microsoft.com/library/bb933875.aspx). |
| Índices XML | A visualização V12 permite que você use as instruções T-SQL CREATE XML INDEX e CREATE SELECTIVE XML INDEX. <p></p> Informações sobre os índices XML estão disponíveis em: <br/> * [CREATE XML INDEX (Transact-SQL)](http://msdn.microsoft.com/library/bb934097.aspx) <br/> * [Criar, alterar e remover índices XML seletivos](http://msdn.microsoft.com/library/jj670109.aspx) |
| Tabela como um heap | A visualização V12 permite que você crie uma tabela que não tem nenhum índice clusterizado. <p></p> Esse recurso é especialmente útil para o suporte da instrução T-SQL SELECT...INTO que cria uma tabela a partir de um resultado de consulta. |
| Funções de aplicativo | Para controle de segurança e permissões, a visualização V12 permite emitir instruções GRANT - DENY - REMOVE em relação às [funções do aplicativo](http://msdn.microsoft.com/library/ms190998.aspx). |


### Categoria: Melhores resultados ao cliente


<!-- -----------------------------
GeneMi , 2014-Dec-07:

A.  PM Sasha Nosov recommends that our Help documentation make no mention of XEvents ("session events"?). 

B. "Change tracking" - as a feature name appears in two different rows in different categories in this topic draft. Yet I think both rows refer to the same feature. Is there any good reason to have two rows for Change Tracking?  Probably not.  Ask PMs.
----------------------- -->

| Recurso | Descrição |
| :--- | :--- |
| DMV (Exibições de gerenciamento dinâmico) | As DMV a seguir foram adicionadas: <br/><br/> dm_clr_properties <br/> dm_clr_tasks <br/><br/> dm_db_file_space_usage <br/> dm_db_log_space_usage <br/> dm_db_session_space_usage <br/> dm_db_task_space_usage <br/><br/> dm_exec_background_job_queue <br/> dm_exec_background_job_queue_stats <br/> dm_exec_query_optimizer_info <br/> dm_exec_query_profiles <br/> dm_exec_query_resource_semaphores <br/> dm_exec_query_transformation_stats <br/><br/>dm_tran_active_snapshot_database_transactions <br/> dm_tran_commit_table <br/> dm_tran_current_snapshot <br/> dm_tran_current_transaction <br/> dm_tran_top_version_generators <br/> dm_tran_transactions_snapshot <br/> dm_tran_version_store |
| Controle de alterações | A visualização V12 é totalmente compatível com o controle de alterações. <p></p> Para obter detalhes desse recurso, consulte [Habilitar e desabilitar o Controle de alterações (SQL Server)](http://msdn.microsoft.com/library/bb964713.aspx). |


### Melhorias de desempenho na camada de serviço Premium


Esses aprimoramentos de desempenho se aplicam aos níveis dentro da camada de serviço Premium P2 e P3.

| Recurso | Descrição |
| :--- | :--- |
| Processamento paralelo para consultas | A visualização V12 fornece um maior grau de paralelismo para consultas que podem se beneficiar dele. |
| Latência de e/s mais reduzida | A visualização V12 tem latência significativamente mais reduzida para operações de entrada/saída. |
| Aumento de IOPS | A visualização V12 pode processar uma quantidade maior de entrada/saída por segundo (IOPS). |
| Taxa de log | A visualização V12 pode registrar mais alterações de dados por segundo. |


### Resumo dos aprimoramentos


A visualização V12 eleva nosso Banco de dados SQL próximo a compatibilidade completa de recursos com nosso produto SQL Server. A visualização V12 concentra-se nos recursos mais populares e no suporte à programação. Isso torna o desenvolvimento mais eficiente e mais agradável.  Agora é ainda mais fácil mover seus aplicativos do banco de dados SQL para a nuvem.

E na camada Premium a visualização V12 traz mais melhorias de desempenho. Alguns aplicativos perceberão ganhos extremos na velocidade de consulta. Aplicativos com cargas de trabalho pesadas perceberão a taxa de transferência confiável robusta.


## Como proceder


Você pode aprender como experimentar a visualização V12 em [Planejar e preparar para atualizar para a visualização mais recente de atualização do banco de dados de SQL Azure](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/).

Durante o período de visualização V12, você pode usar a visualização em um valor com desconto. O novo nível S3 oferece 100 DTU (unidades de taxa de transferência de banco de dados) e todos os recursos disponíveis na camada Standard por US US$ 0,2016 por hora. O preço é descontado até US$ 0,1008 por hora durante a visualização V12. Para obter detalhes, consulte [Preços de Banco de dados SQL](http://azure.microsoft.com/pricing/details/sql-database/).


### Precauções para a visualização V12


- Qualquer banco de dados que for atualizado para a visualização não poderá ser revertido para a versão anterior.
- Novos bancos de dados, cópias de banco de dados ou bancos de dados de teste são bons candidatos para a atualização para a visualização. Como de costume, bancos de dados de produção dos quais sua empresa depende devem aguardar até após o período de visualização.
- A camada de preços dos serviços Web e Business não é suportada nesta versão de visualização.
 

<!-- EndOfFile -->


<!--HONumber=35.2-->
