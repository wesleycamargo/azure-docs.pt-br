<properties 
	pageTitle="Níveis de compatibilidade no Banco de Dados SQL | Microsoft Azure" 
	description="Explica como definir o nível de compatibilidade para o banco de dados do Banco de Dados SQL do Azure e os recursos afetados."
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/21/2015" 
	ms.author="genemi"/>


# Níveis de compatibilidade no Banco de Dados SQL


Para o Banco de Dados SQL do Azure, este tópico descreve as principais opções para usar a instrução Transact-SQL **ALTER DATABASE**. O conceito de nível de compatibilidade foi projetado para reduzir qualquer risco de atualização.


A maioria dos recursos ativados ou desativados pelo nível de compatibilidade faz parte do otimizador de consulta. A Microsoft torna a ativação de um novo recurso do otimizador de consulta contingente no nível de compatibilidade quando:


- O contexto atualizado alterar a semântica de um recurso anterior.
- A otimização de consulta tiver uma chance plausível de prejudicar o desempenho de determinadas consultas SQL incomuns, ainda que legítimas.


Se sua consulta tiver um desempenho inferior logo após uma atualização de versão para seu servidor de Banco de Dados SQL do Azure ou um banco de dados, você terá a opção de reduzir o nível de compatibilidade. A configuração mais baixa pode desativar um pequeno conjunto de aprimoramentos de otimizador, provavelmente incluindo aquela desfavorável à sua consulta.


## Como funciona o nível de compatibilidade


O Microsoft SQL Server teve níveis de compatibilidade configurável por anos. Agora começando pela versão V12, o Banco de Dados SQL do Azure também adota níveis de compatibilidade. A ativação de alguns recursos novos é controlada pela configuração de nível de compatibilidade em cada banco de dados do Banco de Dados SQL. As configurações possíveis incluem:


- 110: a menor configuração possível e, portanto, a configuração que exclui a maioria dos novos recursos.
- 120: corresponde livremente ao Banco de Dados SQL V11 (que significa que `SELECT @@version;` retorna **11.**0.0.0).
 - A versão V11 também foi mencionada como 'SAWA V2' do Banco de Dados SQL.
 - Esse 120 é o valor mais alto no Microsoft SQL Server 2014.
- 130: corresponde livremente ao V12 (que significa que `SELECT @@version;` retorna **12.**0.0.0).
 - Esse 130 é o valor mais alto no Microsoft SQL Server 2016.


Por exemplo, uma configuração de 120 significa que seu banco de dados tem acesso ao subconjunto de recursos ativados no nível 120, *além* dos recursos que se tornarão ativos em qualquer configuração inferior, como 110. Quando definido em 120, seu banco de dados não terá acesso aos recursos do nível 130.


## Ler ou definir o nível de compatibilidade


### Ler o nível de compatibilidade


```
SELECT d.name, d.compatibility_level
	FROM sys.databases AS d
	WHERE d.name = db_name();
```


### Definir o nível de compatibilidade


```
ALTER DATABASE YourDatabaseName SET COMPATIBILITY_LEVEL = 130;
```


Para obter mais informações, consulte:


- [Nível de compatibilidade de ALTER TABLE (Transact-SQL)](http://msdn.microsoft.com/library/bb510680.aspx)
- [sys.databases (Transact-SQL)](http://msdn.microsoft.com/library/ms178534.aspx)


## Tabelas na memória e índices columnstore


A maioria dos recursos de nível de compatibilidade que são ativados nos níveis 120 e 130 estão relacionados a tabelas e índices *em memória*. Portanto, os itens importantes incluem:


- Tabelas com otimização de memória
- Índices ColumnStore


Onde houver itens em memória envolvidos, o nível de compatibilidade 130 oferecerá as seguintes vantagens:


- A capacidade do otimizador de consulta para processar mais consultas em modo de *lote* será ativada.
 - O modo de lote será mais rápido do que o modo de *linha* quando muitas linhas estiverem envolvidas.
- A adição do operador **SORT**.
- A adição de agregações do Windows.


Para saber mais sobre tabelas em memória e índices columnstore, confira:


- [Tabelas com otimização de memória](http://msdn.microsoft.com/library/dn133165.aspx)
- [CREATE CLUSTERED COLUMNSTORE INDEX (Transact-SQL)](http://msdn.microsoft.com/library/dn511016.aspx)
- [Índices columnstore descritos](http://msdn.microsoft.com/library/gg492088.aspx)


## Recursos gerais que exigem o nível mínimo de 130


O avaliador de cardinalidade (CE) gera e armazena estatísticas sobre o número aproximado de linhas em uma tabela. Muitas partes do otimizador de consulta usam as informações de cardinalidade.


Os algoritmos usados pelo CE foram aprimorados. Os aprimoramentos causam a alteração e a alteração pode fazer com que os casos especializados retrocedam.


| 130 é o nível mínimo<br/>necessário<br/> | Área da consulta,<br/>Geral | Detalhes do aprimoramento do<br/>plano de consulta<br/> |
| :-- | :-- | :-- |
| 130 | Avaliador de cardinalidade (CE) | Refinamentos do avaliador de cardinalidade (CE), em comparação ao CE anterior no nível 120.<br/><br/>No plano de consulta, você poderia ver: **CardinalityEstimationModelVersion = "130"**<br/><br/>**Sinalizador de rastreamento** [**9481**](http://www.sqlservergeeks.com/sql-server-2014-trace-flags-9481/) poderá ser ativado para usar o CE do nível 120 quando seu banco de dados estiver no nível 130.<br/><br/>**Sinalizador de rastreamento** [**4199**](http://support.microsoft.com/kb/974006), quando seu banco de dados estiver no nível de compatibilidade 130, poderá ser definido como desativado para recusar hotfixes para o otimizador de consulta. O sinalizador só se aplica a hotfixes implementados depois que o nível 130 ficar totalmente fora da visualização e será lançado para a Disponibilidade Geral (GA). Para obter detalhes, confira:<br/><br/>● [DBCC TRACEON](http://msdn.microsoft.com/library/ms187329.aspx) |
| 130 | Planos de consulta paralelos para tabelas em memória | As consultas podem usar vários threads e podem ser executadas em paralelo quando estiverem em uma tabela em memória, o que significa uma tabela criada com a cláusula **MEMORY\_OPTIMIZED = YES**. O paralelismo pode fazer com que as consultas sejam executadas mais rapidamente.<br/><br/>Esse aprimoramento tem suporte para procedimentos armazenados de usuário e de Transact-SQL regulares. Mas não há suporte para procedimentos armazenados nativos compilados em uma DLL. |


## Recursos de índice columnstore que exigem o nível mínimo de 130


No nível de compatibilidade 130, os aprimoramentos do otimizador de consulta podem resultar em um novo plano de consulta. Para planos alterados que envolvam um índice columnstore, a alteração geralmente envolve o seguinte:


- Uma redução nas circunstâncias em que os dados devem ser copiados para uma suboperação adicional.
- Uma alteração de processamento de *linha* para processamento de ***lote*** para operações como classificação.


Na maioria dos casos, a alteração do plano melhora o desempenho da consulta ao envolver:


- Inserções paralelas em um índice columnstore.
 - O nível 130 não fornece verificação paralela de índices não clusterizados.
- Maior uso do banco de dados **tempdb**.


| 130 é o nível mínimo<br/>necessário<br/> | Área de consulta,<br/>Índice columnstore | Detalhes do aprimoramento do<br/>plano de consulta<br/> |
| :-- | :-- | :-- |
| 130 | Consultas de função | O desempenho é aprimorado pela troca para o modo de lote nos seguintes casos:<br/><br/>• A classificação está envolvida.<br/><br/>• Agrega com *várias* funções distintas<br/>(uma função de cada uma de dois marcadores diferentes da lista a seguir):<br/>&nbsp;&nbsp;&nbsp;▫ **COUNT** *ou* **COUNT\_BIG**<br/>&nbsp;&nbsp;&nbsp;▫ **AVG** *ou* **SUM**<br/>&nbsp;&nbsp;&nbsp;▫ **CHECKSUM\_AGG**<br/>&nbsp;&nbsp;&nbsp;▫ **STDEV** *ou* **STDEVP**<br/><br/>• Funções agregadas do Window<br/>(descritas [aqui no MSDN](http://msdn.microsoft.com/library/ms189461.aspx) e [aqui, de Kathi Kellenberger](http://www.bidn.com/blogs/KathiKellenberger/sql-server/4397/what-is-a-window-aggregate-function)):<br/>&nbsp;&nbsp;&nbsp;▫ **COUNT**, **COUNT\_BIG**, **SUM**, **AVG**, **MIN**, **MAX**, **CLR**<br/><br/>• [Agregações definidas pelo usuário](http://msdn.microsoft.com/library/ms131057.aspx) do Windows:<br/>&nbsp;&nbsp;&nbsp;▫ [**CHECKSUM\_AGG**](http://msdn.microsoft.com/library/ms188920.aspx), [**STDEV**](http://msdn.microsoft.com/library/ms190474.aspx), [**STDEVP**](http://msdn.microsoft.com/library/ms176080.aspx), [**VAR**](http://msdn.microsoft.com/library/ms186290.aspx), [**VARP**](http://msdn.microsoft.com/library/ms188735.aspx), [**GROUPING**](http://msdn.microsoft.com/library/ms178544.aspx)<br/><br/>• Funções analíticas de agregação do Windows:<br/>&nbsp;&nbsp;&nbsp;▫ [**LAG**](http://msdn.microsoft.com/library/hh231256.aspx), [**LEAD**](http://msdn.microsoft.com/library/hh213125.aspx), [**FIRST\_VALUE**](http://msdn.microsoft.com/library/hh213018.aspx), [**LAST\_VALUE**](http://msdn.microsoft.com/library/hh231517.aspx), [**PERCENTILE\_CONT**](http://msdn.microsoft.com/library/hh231473.aspx), [**PERCENTILE\_DISC**](http://msdn.microsoft.com/library/hh231327.aspx), [**CUME\_DIST**](http://msdn.microsoft.com/library/hh231078.aspx), [**PERCENT\_RANK**](http://msdn.microsoft.com/library/hh213573.aspx) |
| 130 | Plano de consulta serial de thread único | Uma consulta executada em um único thread pode ser executada em modo de lote. Isso pode fazer com que a consulta seja executada mais rápido.<br/><br/>Um plano de consulta deve ser projetado como thread único, ou uma consulta pode ser executada em **MAXDOP 1**. |
| 130 | Inserção paralela | Seu plano de consulta pode executar algumas inserções em paralelo.< br/<br/>O [exemplo](#ExampleQueryParallelCciByCompatLevel) mais adiante neste tópico demonstra esse paralelismo. |
| 130 | Anti-semi join | Esse operador agora pode ser executado em modo de lote. |


## Recursos gerais que exigem o nível mínimo de 120


| 120 é o nível mínimo<br/>necessário<br/> | Área da consulta,<br/>Geral | Detalhes do aprimoramento do<br/>plano de consulta<br/> |
| :-- | :-- | :-- |
| 120 | [Alterando tabelas MEMORY\_OPTIMIZED](http://msdn.microsoft.com/library/dn269114.aspx) | Permite que você execute operações Transact-SQL **ALTER TABLE** em tabelas com **MEMORY\_OPTIMIZED = YES**.<br/><br/>O aplicativo de banco de dados pode continuar a ser executado, mas as operações que acessem a tabela serão bloqueadas até a conclusão de **ALTER TABLE**. |
| 120 | [Criando e gerenciando o armazenamento para objetos com otimização de memória](http://msdn.microsoft.com/library/dn133174.aspx) | O mecanismo OLTP em memória é integrado ao SQL Server. Isso permite que você tenha ambas as tabelas **MEMORY\_OPTIMIZED** e tabelas tradicionais baseadas em disco no mesmo banco de dados. |
| 120 | [Suporte a Transact-SQL para OLTP em memória](http://msdn.microsoft.com/library/dn133180.aspx) | Muitos comandos Transact-SQL foram aprimorados para dar suporte ao processamento de transações online em memória (OLTP).<br/><br/>Um exemplo é a nova palavra-chave **NATIVE\_COMPILATION** no comando [CREATE PROCEDURE](http://msdn.microsoft.com/library/ms187926.aspx). |
| 120 | Avaliador de cardinalidade (CE) | Os refinamentos feitos no avaliador de cardinalidade (CE) em comparação ao CE anterior no nível 110.<br/><br/>No plano de consulta, você verá: **CardinalityEstimationModelVersion = "120"**<br/><br/>Para obter detalhes sobre como o **sinalizador de rastreamento 4199** interage com o valor de nível de compatibilidade, consulte o [KB 974006](http://support.microsoft.com/kb/974006).|


## Recursos de índice columnstore que exigem o nível mínimo de 120


Esta seção descreve os [recursos de indexação columnstore](http://msdn.microsoft.com/library/dn934994.aspx) ativados no nível de compatibilidade 120 ou superior.


| 120 é o nível mínimo de<br/>compatibilidade<br/> | Nome do recurso de índice<br/>columnstore | Descrição do recurso<br/>de índice columnstore |
| :-- | :-- | :-- |
| 120 | Nível de isolamento de instantâneo (SI) e<br/><br/>nível de isolamento de instantâneo confirmado por leitura (RCSI) | Quando o plano de consulta envolver um índice columnstore, SI e RCSI impedirão que os dados de transações parcialmente concluídas sejam incluídos nos resultados da consulta, sem a necessidade de bloqueios excessivos. |
| 120 | Desfragmentação de índice | As linhas excluídas são removidas sem uma recriação explícita de índice.<br/><br/>**ALTER INDEX ... REORGANIZE * * remove as linhas excluídas do índice columnstore enquanto a tabela e o índice permanecem operacionais online. |
| 120 | Acessível em uma réplica secundária capaz de leitura [do AlwaysOn](http://msdn.microsoft.com/library/ff878253.aspx) | Você pode melhorar o desempenho de análise operacional descarregando consultas analíticas em uma réplica secundária do AlwaysOn. |
| 120 | Envio por push de agregação,<br/>durante a fase de verificação de tabela de funções de agregação | Melhora o desempenho concluindo cálculos provisórios anteriormente no plano de consulta, para que menos dados tenham de ser copiados para fases posteriores.<br/><br/>Aplica-se a **MIN**, **MAX**, **SUM**, **COUNT**, **AVG**.<br/><br/>Aplica-se somente quando o tipo de dados é de oito bytes ou menos, embora não seja para cadeias de caracteres. |
| 120 | Envio por push de cláusulas **WHERE** baseadas em cadeias de caracteres | A otimização de envio por push de predicado pode acelerar as consultas que comparem dados de cadeia de caracteres do tipo &#x5b;var&#x5d;char ou n&#x5b;var&#x5d;char. Esta otimização:<br/><br/>• Aplica-se aos operadores de comparação comuns, incluindo **LIKE**, que usam filtros de bitmap.<br/><br/>• Só funciona quando há um predicado de cadeia de caracteres.<br/><br/>• Funciona com todos os agrupamentos com suporte ao produto.<br/><br/>Se você quiser obter mais detalhes sobre os filtros de bitmap, consulte esta postagem de blog: [Introdução aos filtros de bitmap de execução de consulta](http://blogs.msdn.com/b/sqlqueryprocessing/archive/2006/10/27/query-execution-bitmaps.aspx). |



<a id="ExampleQueryParallelCciByCompatLevel" name="ExampleQueryParallelCciByCompatLevel"></a>


&nbsp;


## Exemplo de alteração do plano de consulta por nível de compatibilidade


Para uma instrução Transact-SQL **INSERT...SELECT**, esta seção exibe a alteração em seu plano de consulta entre os níveis de compatibilidade 120 e 130.


#### Esquema da tabela de origem


A tabela a seguir contém pelo menos 300.000 linhas. O plano de consulta avançada poderá não se preocupar com o paralelismo se houver muitos poucos dados para tornar o paralelismo válido.


```
CREATE TABLE [dbo].[ccitestoriginal](
	[FinanceKey] [int] NOT NULL,
	[DateKey] [int] NOT NULL,
	[OrganizationKey] [int] NOT NULL,
	[DepartmentGroupKey] [int] NOT NULL,
	[ScenarioKey] [int] NOT NULL,
	[AccountKey] [int] NOT NULL,
	[Amount] [float] NOT NULL,
	[Date] [datetime] NULL
) ON [PRIMARY];
GO

CREATE CLUSTERED COLUMNSTORE INDEX [cci_ccitestoriginal]
	ON [dbo].[ccitestoriginal]
	WITH (DROP_EXISTING = OFF) ON [PRIMARY];
GO
```


#### Script para gerar o plano de consulta


Aplique estas etapas ao script Transact-SQL a seguir:


1. Em **Ssms.exe**, conecte-se ao seu banco de dados.
2. Cole o script Transact-SQL em uma janela de consulta de **Ssms.exe**.
3. Edite o script para garantir que **120** seja o valor na instrução **ALTER DATABASE**.
4. Execute apenas a parte do script que seja *anterior* à instrução **INSERT INTO**.
5. Ative a opção de menu: **Consulta** > **Incluir Plano de Execução Real (Ctrl+M)**.
6. Execute somente a instrução **INSERT INTO**.<br/><br/>
7. Desative a opção de menu: **Consulta** > **Incluir Plano de Execução Real (Ctrl+M)**.
8. Por fim, execute apenas a parte do script que seja *posterior* à instrução **INSERT INTO**.
9. Observe o plano de consulta para **120** na guia **Plano de execução**, próxima à guia **Resultados**.<br/>-- -- -- -- -- --
10. Edite o script para garantir que **130** seja o valor em **ALTER DATABASE**.
11. Execute novamente o script como descrito nas etapas anteriores.
12. Observe que o plano de consulta para **130** é diferente e inclui o paralelismo.


&nbsp;


```
go
SET NOCOUNT ON;
go

ALTER DATABASE YourDatabaseName SET COMPATIBILITY_LEVEL =
	120
	--130
;
go

DROP TABLE ccitest_into_work;
go

SELECT *
	INTO ccitest_into_work  -- Create an empty copy of the table.
	FROM ccitestoriginal
	WHERE 1=2;
go

CREATE CLUSTERED COLUMNSTORE INDEX ccitest_into_work_cci
	ON ccitest_into_work;
go

SET NOCOUNT OFF;
go
	
	-- Run this INSERT statement alone!
	--
	-- First run all the preceding Transact-SQL statements.
	-- Then run this one INSERT statement alone.
	-- Then run all remaining Transact-SQL statements.

INSERT INTO ccitest_into_work WITH (TABLOCK)
	SELECT TOP 300000 *
		FROM ccitestoriginal;
go

SET NOCOUNT ON;
go

DROP TABLE ccitest_into_work;
go
```


#### Exibição dos dois planos



<br/>**120:** Este será o plano de consulta quando o nível de compatibilidade for **120**.


![Planejar no nível 120][1-Plan-at-level-120]


<br/>**130:** Este será o plano de consulta quando o nível de compatibilidade for **130**.


O plano **130** inclui o *paralelismo* que o plano **120** não tem.


A exibição desse plano é bastante ampla em **Ssms.exe**. Para obter uma exibição melhor aqui, a captura de tela será dividida em duas partes. A segunda parte é continuação da primeira parte.


![Planejar no nível 130][2-Plan-at-level-130]


## Nível de compatibilidade padrão no banco de dados


Quando você atualiza seu servidor do Banco de Dados SQL do Azure, como da versão V11 para a V12, o nível de compatibilidade em cada banco de dados permanecerá inalterado. Após a atualização, você poderá aumentar o nível de compatibilidade em qualquer banco de dados específico usando a instrução **ALTER DATABASE**.


Qualquer banco de dados recém-criado terá o nível de compatibilidade máximo que a versão do Banco de Dados SQL permite.



<!-- Image references. -->

[1-Plan-at-level-120]: ./media/sql-database-compatibility-level/sql-db-compat-level-query-plan-b-120.png

[2-Plan-at-level-130]: ./media/sql-database-compatibility-level/sql-db-compat-level-query-plan-b12-130.png

<!---HONumber=Oct15_HO3-->