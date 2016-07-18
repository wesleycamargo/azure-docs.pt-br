<properties
   pageTitle="Introdução ao TSQL de Transparent Data Encryption (TDE) do SQL Data Warehouse | Microsoft Azure"
   description="Introdução ao TSQL de Transparent Data Encryption (TDE) do SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="06/07/2016"
   ms.author="rortloff;barbkess;sonyama"/>

# Introdução ao Transparent Data Encryption (TDE)


> [AZURE.SELECTOR]
- [Visão Geral da Segurança](sql-data-warehouse-overview-manage-security.md)
- [Detecção de ameaças](sql-data-warehouse-security-threat-detection.md)
- [Criptografia (Portal)](sql-data-warehouse-encryption-tde.md)
- [Criptografia (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
- [Visão Geral da Auditoria](sql-data-warehouse-auditing-overview.md)
- [Clientes de nível inferior da auditoria](sql-data-warehouse-auditing-downlevel-clients.md)


O Transparent Data Encryption (TDE) do SQL Data Warehouse do Azure ajuda a proteger contra atividades mal-intencionadas por meio da execução de criptografia e descriptografia em tempo real do banco de dados, de backups associados e de arquivos de log de transações em repouso, sem exigir mudanças no aplicativo.

A TDE criptografa o armazenamento de um banco de dados inteiro usando uma chave simétrica chamada de chave de criptografia de banco de dados. No Banco de Dados SQL, a chave de criptografia do banco de dados está protegida por um certificado de servidor interno. O certificado de servidor interno é exclusivo para cada servidor de Banco de Dados SQL. A Microsoft alterna automaticamente esses certificados pelo menos a cada 90 dias. O algoritmo de criptografia usado pelo SQL Data Warehouse é o AES-256. Para obter uma descrição geral da TDE, consulte [Transparent Data Encryption (TDE)].

##Habilitando a criptografia

Para habilitar a TDE para um SQL Data Warehouse, siga estas etapas:

1. Conecte ao banco de dados *mestre* no servidor que está hospedando o banco de dados que usa um logon de administrador ou de um membro da função **dbmanager** no banco de dados mestre
2. Execute a instrução a seguir para criptografar o banco de dados.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

##Desabilitando a criptografia

Para desabilitar a TDE para um SQL Data Warehouse, siga estas etapas:

1. Conecte-se ao banco de dados *mestre* que usa um logon de administrador ou de um membro da função **dbmanager** no banco de dados mestre
2. Execute a instrução a seguir para criptografar o banco de dados.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

##Verificando a criptografia

Para verificar o status de criptografia para um SQL Data Warehouse, siga estas etapas:

1. Conecte-se ao banco de dados *mestre* ou de instância que usa um logon de administrador ou de um membro da função **dbmanager** no banco de dados mestre
2. Execute a instrução a seguir para criptografar o banco de dados.

```sql
SELECT
	[name],
	[is_encrypted]
FROM
	sys.databases;
```

Um resultado de ```1``` indica um banco de dados criptografado, ```0``` indica um banco de dados não criptografado.

##DMVs de criptografia  

- [sys.databases][]
- [sys.dm\_pdw\_nodes\_database\_encryption\_keys][]


<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx
[sys.dm\_pdw\_nodes\_database\_encryption\_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->

<!--Link references-->

<!---HONumber=AcomDC_0706_2016-->