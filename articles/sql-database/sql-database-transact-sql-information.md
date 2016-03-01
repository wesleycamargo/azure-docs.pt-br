<properties
   pageTitle="Sem suporte no T-SQL de Banco de Dados SQL do Azure | Microsoft Azure"
   description="Instruções Transact-SQL que têm suporte menor que o total pelo Banco de Dados SQL"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jeffreyg"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="02/16/2016"
   ms.author="rick.byham@microsoft.com"/>

# Diferenças de Transact-SQL de Banco de Dados SQL do Azure


Há suporte, tanto no Microsoft SQL Server quanto no Banco de Dados SQL, para a maioria dos recursos Transact-SQL dos quais os aplicativos dependem. A seguir, uma lista parcial dos recursos com suporte para aplicativos:

- Tipos de dados.
- Operadores.
- Funções de cadeia de caracteres, aritmética, lógica e de cursor.

No entanto, o Banco de Dados SQL foi projetado para isolar recursos de qualquer dependência no banco de dados **mestre**. Como consequência, muitas atividades de nível de servidor são inapropriadas para o Banco de Dados SQL e não têm suporte. Este tópico fornece detalhes sobre os recursos que não têm suporte total no Banco de Dados SQL.

Além disso, o Banco de Dados SQL geralmente não dá suporte a recursos preteridos no SQL Server.

## Atualizando para o Banco de Dados SQL V12

Este tópico aborda os recursos que estão disponíveis com o banco de dados SQL quando atualizados para o Banco de Dados SQL V12 gratuito. Para obter mais informações sobre o V12, consulte [O que há de novo no Banco de Dados SQL V12](sql-database-v12-whats-new.md). O Banco de Dados SQL V12 adiciona melhorias de desempenho e gerenciamento, bem como suporte a recursos adicionais. Os recursos adicionados são listados abaixo, separados em recursos para os quais há suporte total e aqueles para os quais há suporte parcial.

## Recursos para os quais há suporte parcial no Banco de Dados SQL V12

O Banco de Dados SQL V12 dá suporte a alguns, mas não todos os argumentos existentes nas instruções Transact-SQL correspondentes do SQL Server 2016. Por exemplo, a instrução CREATE PROCEDURE está disponível; no entanto, a opção WITH ENCRYPTION de CREATE PROCEDURE não está disponível. Consulte os tópicos de sintaxe vinculados para obter detalhes sobre as áreas de cada instrução para as quais há suporte.

- Bancos de dados: [CREATE](https://msdn.microsoft.com/library/dn268335.aspx)/[ALTER](https://msdn.microsoft.com/library/ms174269.aspx)
- DMVs geralmente estão disponíveis para recursos que também estão disponíveis
- Funções: [CREATE](https://msdn.microsoft.com/library/ms186755.aspx)/[ALTER FUNCTION](https://msdn.microsoft.com/library/ms186967.aspx)
- [KILL](https://msdn.microsoft.com/library/ms173730.aspx) 
- Logons: [CREATE](https://msdn.microsoft.com/library/ms189751.aspx)/[ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)
- Procedimentos armazenados: [CREATE](https://msdn.microsoft.com/library/ms187926.aspx)/[ALTER PROCEDURE](https://msdn.microsoft.com/library/ms189762.aspx)
- Tabelas: [CREATE](https://msdn.microsoft.com/library/dn305849.aspx)/[ALTER](https://msdn.microsoft.com/library/ms190273.aspx)
- Tipos (personalizados): [CREATE TYPE](https://msdn.microsoft.com/library/ms175007.aspx)
- Usuários: [CREATE](https://msdn.microsoft.com/library/ms173463.aspx)/[ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx)
- Exibições: [CREATE](https://msdn.microsoft.com/library/ms187956.aspx)/[ALTER VIEW](https://msdn.microsoft.com/library/ms173846.aspx)

## Recursos para os quais não há suporte no Banco de Dados SQL

- Agrupamento de objetos do sistema
- Conexão relacionado: instruções de ponto de extremidade, ORIGINAL\_DB\_NAME. A autenticação do Windows não está disponível para logons ou usuários de bancos de dados independentes.
- Consultas cruzadas de banco de dados usando nomes de três ou quatro partes. (As consultas de bancos de dados somente leitura têm suporte por meio de consulta de banco de dados elástico).
- Encadeamento de propriedades de bancos de dados, configuração TRUSTWORTHY
- Coletor de dados
- Diagramas de banco de dados
- Database Mail
- DATABASEPROPERTY (em vez disso, use DATABASEPROPERTYEX)
- Logons de EXECUTE AS
- Criptografia: gerenciamento extensível de chaves
- Eventos: eventos, notificações de eventos, notificações de consulta
- Recursos relacionados à localização de arquivos de banco de dados, o tamanho e os arquivos de banco de dados que são gerenciados automaticamente pelo Microsoft Azure.
- Recursos relacionados à alta disponibilidade, que é gerenciada por meio de sua conta do Microsoft Azure: backup, restauração, AlwaysOn, espelhamento de banco de dados, envio de logs e modos de recuperação. Para obter mais informações, consulte Backup e restauração do Banco de dados SQL do Azure.
- Recursos que dependem do leitor de log: Replicação, Change Data Capture.
- Recursos que dependem do SQL Server Agent ou do banco de dados MSDB: trabalhos, alertas, operadores, gerenciamento baseado em políticas, database mail, servidores de gerenciamento central.
- FILESTREAM
- Funções: fn\_get\_sql, fn\_virtualfilestats e fn\_virtualservernodes
- Tabelas temporárias globais
- Configurações do servidor relacionadas ao hardware: memória, threads de trabalho, afinidade da CPU, sinalizadores de rastreamento, etc. Em vez disso, use níveis de serviço.
- HAS\_DBACCESS
- KILL STATS JOB
- Servidores vinculados, OPENQUERY, OPENROWSET, OPENDATASOURCE, BULK INSERT, nomes de 3 e 4 partes
- Servidores mestre/de destino
- [integração CLR com o SQL Server](http://msdn.microsoft.com/library/ms254963.aspx) do .NET framework
- Administrador de recursos
- Pesquisa semântica
- Credenciais do servidor
- Itens no nível de servidor: funções de servidor, IS\_SRVROLEMEMBER, sys.login\_token. Permissões de nível de servidor não estão disponíveis, embora algumas delas sejam substituídas por permissões de nível de banco de dados. Algumas DMVs (exibições de gerenciamento dinâmico) de nível de servidor não estão disponíveis, embora algumas delas sejam substituídas por DMVs de nível de banco de dados.
- Serverless express: localdb, instâncias de usuário
- Service broker
- SET REMOTE\_PROC\_TRANSACTIONS
- SHUTDOWN
- sp\_addmessage
- Opções de sp\_configure e RECONFIGURE
- sp\_helpuser
- sp\_migrate\_user\_to\_contained
- Auditoria do SQL Server (usar auditoria de Banco de Dados SQL em vez disso)
- SQL Server Profiler
- Rastreamento do SQL Server
- Sinalizadores de rastreamento
- Depuração de Transact-SQL
- Disparadores: no escopo do servidor ou gatilhos de logon
- Instrução USE


## Referência completa do Transact-SQL

Para obter mais informações sobre gramática, uso e exemplos do Transact-SQL, veja [Referência do Transact-SQL (mecanismo de banco de dados)](https://msdn.microsoft.com/library/bb510741.aspx) nos Manuais Online do SQL Server.

### Sobre as marcas "Aplica-se a"

A referência do Transact-SQL inclui tópicos relacionados a versões do SQL Server da 2008 à atual. Sob o título do tópico normalmente há uma linha "Aplica-se a" que lista as versões do SQL Server e talvez outros nomes de produtos também. Geralmente, as mesmas marcas "Aplica-se a" também listam o Banco de Dados SQL do Azure. Se uma "Aplica-se a" não lista um Banco de Dados SQL do Azure, o conteúdo do tópico não se aplica ao Banco de dados SQL do Azure. Às vezes, você poderá ver uma linha "Aplica-se a" que lista vários produtos, cada um com um pequeno ícone para indicar se o tópico se aplica a cada produto.

 Por exemplo, grupos de disponibilidade foram introduzidos no SQL Server 2012. O tópico **CRIAR GRUPO DE DISPONIBILIDADE** indica que ele se aplica ao **SQL Server (desde o SQL Server 2012 até a versão atual)**, pois ele não se aplica ao SQL Server 2008, SQL Server 2008 R2, nem ao Banco de Dados SQL do Microsoft Azure.

Em alguns casos, o assunto geral de um tópico pode ser usado em um produto, mas há pequenas diferenças entre produtos. As diferenças são indicadas em pontos médios no tópico, conforme apropriado.

<!---HONumber=AcomDC_0218_2016-->