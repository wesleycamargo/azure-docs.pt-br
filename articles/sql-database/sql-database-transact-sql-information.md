<properties
   pageTitle="Informações de Transact-SQL de Banco de Dados SQL do Azure | Microsoft Azure"
   description="Instruções Transact-SQL no Banco de Dados SQL do Azure"
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
   ms.date="11/02/2015"
   ms.author="rick.byham@microsoft.com"/>

# Informações de Transact-SQL de Banco de Dados SQL do Azure

No Banco de Dados SQL do Microsoft Azure, há suporte total para a maioria das instruções Transact-SQL do SQL Server 2016. Isso inclui os tipos de dados do SQL Server, seus operadores e as funções de cadeia de caracteres, aritméticas, lógicas e de cursor, além de outros elementos Transact-SQL que dependem da maioria dos aplicativos. Funções com suporte parcial ou sem suporte geralmente estão relacionadas às diferenças no modo como o Banco de Dados SQL gerencia o banco de dados (como recursos de segurança, de alta disponibilidade e de arquivos) ou para recursos de finalidade especial, como o service broker. Já que o Banco de Dados SQL isola muitos recursos de dependência no banco de dados mestre, muitas atividades de nível de servidor são inadequadas e sem suporte. O Banco de Dados SQL geralmente não dá suporte a recursos preteridos no SQL Server.

## Atualizando para o Banco de Dados SQL V12

Este tópico aborda os recursos que estão disponíveis com o banco de dados SQL quando atualizados para o Banco de Dados SQL V12 gratuito. Para obter mais informações sobre o V12, consulte [O que há de novo no Banco de Dados SQL V12](sql-database-v12-whats-new.md). O Banco de Dados SQL V12 adiciona melhorias de desempenho e gerenciamento, bem como suporte a recursos adicionais. Os recursos adicionados são listados abaixo, separados em recursos para os quais há suporte total e aqueles para os quais há suporte parcial.

## Recursos para os quais há suporte parcial no Banco de Dados SQL V12

O Banco de Dados SQL V12 dá suporte a alguns, mas não todos os argumentos existentes nas instruções Transact-SQL correspondentes do SQL Server 2016. Por exemplo, a instrução CREATE PROCEDURE está disponível; no entanto, a opção WITH ENCRYPTION de CREATE PROCEDURE não está disponível. Consulte os tópicos de sintaxe vinculados para obter detalhes sobre as áreas de cada instrução para as quais há suporte.

- Assemblies do CLR: [CREATE ASSEMBLY](https://msdn.microsoft.com/library/ms189524.aspx)
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
- Consultas entre banco de dados, encadeamento de propriedades de bancos de dados, configuração TRUSTWORTHY
- Coletor de dados
- Diagramas de banco de dados
- Database Mail
- DATABASEPROPERTY (em vez disso, use DATABASEPROPERTYEX)
- Transações distribuídas
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

A referência do Transact-SQL inclui tópicos relacionados ao SQL Server 2008, SQL Server 2008 R2, SQL Server 2012, SQL Server 2014 e Banco de Dados SQL do Microsoft Azure. Na parte superior de cada tópico há uma seção indicando quais produtos dão suporte ao assunto do tópico. Se um produto é omitido, o recurso descrito pelo tópico não está disponível nesse produto. Por exemplo, grupos de disponibilidade foram introduzidos no SQL Server 2012. O tópico **CRIAR GRUPO DE DISPONIBILIDADE** indica que ele se aplica ao **SQL Server (desde o SQL Server 2012 até a versão atual)**, pois ele não se aplica ao SQL Server 2008, SQL Server 2008 R2, nem ao Banco de Dados SQL do Microsoft Azure.

Em alguns casos, o assunto geral do tópico pode ser usado em um produto, mas não há suporte para todos os argumentos. Por exemplo, usuários de bancos de dados independentes foram introduzidos no SQL Server 2012. A instrução **CREATE USER** pode ser usada em qualquer produto do SQL Server; no entanto, a sintaxe **WITH PASSWORD** não pode ser usada com versões anteriores. Nesse caso, seções **Aplica-se a** adicionais são inseridas nas descrições apropriadas do argumento no corpo do tópico.

<!---HONumber=Nov15_HO2-->