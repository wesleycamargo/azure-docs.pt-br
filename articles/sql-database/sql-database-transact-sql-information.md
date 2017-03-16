---
title: Recursos sem suporte no T-SQL do Banco de Dados SQL do Azure | Microsoft Docs
description: "Instruções Transact-SQL que têm suporte menor que o total pelo Banco de Dados SQL"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: c05abd9e-28a7-4c97-9bdf-bc60d08fc92e
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/22/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: a08d9f2ef29002f10473b0e041737c9c607f3ca0
ms.openlocfilehash: 7d6de93c99141248ea970ea668fb0b2191267b62
ms.lasthandoff: 03/01/2017


---
# <a name="azure-sql-database-transact-sql-differences"></a>Diferenças de Transact-SQL de Banco de Dados SQL do Azure   
Há suporte, tanto no Microsoft SQL Server quanto no Banco de Dados SQL, para a maioria dos recursos Transact-SQL dos quais os aplicativos dependem. Por exemplo, os componentes principais do SQL, como tipos de dados, operadores, cadeia de caracteres, funções aritméticas, lógicas e de cursor, etc., funcionam da mesma maneira que no SQL Server.

# <a name="why-some-transact-sql-is-not-supported"></a>Por que não há suporte para algumas funções Transact-SQL
O Banco de Dados SQL do Azure foi criado para isolar recursos de dependências no banco de dados mestre e no sistema operacional. Como consequência, muitas atividades no servidor são inapropriadas para o Banco de Dados SQL. Em geral, as instruções Transact-SQL não estão disponíveis se elas configuram opções no nível do servidor, componentes do sistema operacional ou se especificam a configuração do sistema de arquivos. Quando os recursos que estão fora do banco de dados de usuário são necessários, uma alternativa apropriada costuma estar disponível de alguma forma no Banco de Dados SQL ou em outro recurso ou serviço do Azure. 

Por exemplo, o Always On não se aplica ao Banco de Dados SQL do Azure uma vez que a alta disponibilidade está incorporada ao serviço. Cada banco de dados é altamente disponível. Por esse motivo, não há suporte para as instruções Transact-SQL relacionadas aos grupos de disponibilidade no Banco de Dados SQL nem para as exibições de gerenciamento dinâmico relacionadas ao AlwaysOn.  

Para obter uma lista dos recursos com e sem suporte no Banco de Dados SQL, consulte [Considerações, diretrizes e recursos do Banco de Dados SQL do Azure](sql-database-features.md).


## <a name="transact-sql-syntax-partially-supported-in-sql-database"></a>Sintaxe do Transact-SQL com suporte parcial no Banco de Dados SQL
O Banco de Dados SQL dá suporte a alguns, mas não a todos os argumentos existentes nas instruções Transact-SQL correspondentes do SQL Server 2016 para o gerenciamento de banco de dados e logins. Por exemplo, a instrução `CREATE DATABASE` está disponível no Banco de Dados SQL do Azure e nem todas as opções com suporte no SQL Server têm suporte no Banco de Dados SQL e vice-versa. Consulte os tópicos de sintaxe vinculados para obter detalhes sobre as áreas de cada instrução para as quais há suporte.

- Bancos de dados: [CREATE](https://msdn.microsoft.com/library/dn268335.aspx)/[ALTER DATABASE](https://msdn.microsoft.com/library/mt574871.aspx)   
- Logons: [CREATE](https://msdn.microsoft.com/library/ms189751.aspx)/[ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)   

## <a name="transact-sql-syntax-not-supported-in-sql-database"></a>Sintaxe do Transact-SQL sem suporte no Banco de Dados SQL   
Além das instruções Transact-SQL relacionadas aos recursos sem suporte descritos em [Considerações, diretrizes e recursos do Banco de Dados SQL do Azure](sql-database-features.md), não há suporte para as instruções e grupos de instruções a seguir.
- Agrupamento de objetos do sistema
- Relacionado à conexão: instruções de ponto de extremidade, `ORIGINAL_DB_NAME`. O Banco de dados SQL não dá suporte à autenticação do Windows, mas dá suporte à autenticação do Azure Active Directory semelhante. Alguns tipos de autenticação exigem a versão mais recente do SSMS. Para obter mais informações, consulte [Conectar-se ao Banco de Dados SQL ou ao SQL Data Warehouse usando a autenticação do Azure Active Directory](sql-database-aad-authentication.md).
- Consultas cruzadas de banco de dados usando nomes de três ou quatro partes. (As consultas de bancos de dados somente leitura têm suporte por meio de [consulta de banco de dados elástico](sql-database-elastic-query-overview.md).)
- encadeamento de propriedades de bancos de dados, configuração `TRUSTWORTHY`
- `DATABASEPROPERTY` Em vez disso, use `DATABASEPROPERTYEX`.
- `EXECUTE AS LOGIN` Use “EXECUTE AS USER”.
- Há suporte para criptografia, exceto para o gerenciamento extensível de chaves
- Eventos: eventos, notificações de eventos, notificações de consulta
- Sintaxe relacionada ao posicionamento de arquivos de banco de dados, ao tamanho e aos arquivos de banco de dados que são gerenciados automaticamente pelo Microsoft Azure.
- Sintaxe relacionada à alta disponibilidade, gerenciada por meio de sua conta do Microsoft Azure. Isso inclui a sintaxe de backup, restauração, do AlwaysOn, espelhamento de banco de dados, envio de logs e dos modos de recuperação.
- Sintaxe que se baseia no leitor de log, que não está disponível no Banco de Dados SQL: Replicação Push, Captura de Dados de Alteração. O Banco de Dados SQL pode ser um assinante de um artigo de replicação de push.
- Sintaxe que se baseia no SQL Server Agent ou no banco de dados MSDB: alertas, operadores, servidores de gerenciamento central. Em vez disso, use scripts, como o Azure PowerShell.
- Funções: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Tabelas temporárias globais
- Sintaxe relacionada às configurações de servidor relacionadas ao hardware: memória, threads de trabalho, afinidade da CPU, sinalizadores de rastreamento etc. Em vez disso, use níveis de serviço.
- `HAS_DBACCESS`
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET`, `OPENDATASOURCE` e nomes de quatro partes
- [integração CLR com o SQL Server](http://msdn.microsoft.com/library/ms254963.aspx)
- Pesquisa semântica
- Credenciais do servidor. Em vez disso, use credenciais no escopo do banco de dados.
- Itens no nível do servidor: funções do servidor, `IS_SRVROLEMEMBER`, `sys.login_token`. `GRANT`, `REVOKE` e `DENY` das permissões no nível do servidor não estão disponíveis, embora algumas delas sejam substituídas por permissões no nível do banco de dados. Algumas DMVs úteis no nível do servidor têm DMVs equivalentes no nível do banco de dados.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- Opções `sp_configure` e `RECONFIGURE`. Algumas opções estão disponíveis usando [ALTERAR CONFIGURAÇÃO DE ESCOPO DO BANCO DE DADOS](https://msdn.microsoft.com/library/mt629158.aspx).
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- Auditoria do SQL Server. Em vez disso, use a auditoria do Banco de Dados SQL.
- Rastreamento do SQL Server
- Sinalizadores de rastreamento. Alguns itens do sinalizador de rastreamento foram movidos para os modos de compatibilidade.
- Depuração de Transact-SQL
- Disparadores: no escopo do servidor ou gatilhos de logon
- Instrução `USE`: para alterar o contexto do banco de dados para um banco de dados diferente, é necessário estabelecer uma nova conexão com o novo banco de dados.

## <a name="full-transact-sql-reference"></a>Referência completa do Transact-SQL
Para obter mais informações sobre gramática, uso e exemplos do Transact-SQL, veja [Referência do Transact-SQL (mecanismo de banco de dados)](https://msdn.microsoft.com/library/bb510741.aspx) nos Manuais Online do SQL Server. 

### <a name="about-the-applies-to-tags"></a>Sobre as marcas "Aplica-se a"
A referência do Transact-SQL inclui tópicos relacionados a versões do SQL Server da 2008 à atual. Abaixo do título do tópico, há um ícone de barra, listando as quatro plataformas do SQL Server e que indica a aplicabilidade. Por exemplo, grupos de disponibilidade foram introduzidos no SQL Server 2012. O tópico [CREATE AVAILABILTY GROUP](https://msdn.microsoft.com/library/ff878399.aspx) indica que a instrução se aplica ao **SQL Server (a partir do 2012)**. A instrução não se aplica ao SQL Server 2008, ao SQL Server 2008 R2, ao Banco de Dados SQL do Azure, ao SQL Data Warehouse ou ao Parallel Data Warehouse.

Em alguns casos, o assunto geral de um tópico pode ser usado em um produto, mas há pequenas diferenças entre produtos. As diferenças são indicadas em pontos médios no tópico, conforme apropriado.

