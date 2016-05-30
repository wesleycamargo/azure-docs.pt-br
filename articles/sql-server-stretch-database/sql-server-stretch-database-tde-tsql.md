<properties
   pageTitle="Habilitar TDE (Transparent Data Encryption) para SQL Server Stretch Database no Azure TSQL | Microsoft Azure"
   description="Habilitar TDE (Transparent Data Encryption) para SQL Server Stretch Database no Azure TSQL"
   services="sql-server-stretch-database"
   documentationCenter=""
   authors="douglaslMS"
   manager=""
   editor=""/>

<tags
   ms.service="sql-server-stretch-database"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="05/17/2016"
   ms.author="douglaslMS"/>

# Habilitar TDE (Transparent Data Encryption) para SQL Server Stretch Database no Azure
> [AZURE.SELECTOR]
- [Portal do Azure](sql-server-stretch-database-encryption-tde.md)
- [TSQL](sql-server-stretch-database-encryption-tde-tsql.md)

O TDE (Transparent Data Encryption) ajuda a proteger contra ameaças de atividades mal-intencionadas por meio da execução de criptografia e descriptografia em tempo real do banco de dados, de backups associados e de arquivos de log de transações em repouso, sem exigir mudanças no aplicativo.

A TDE criptografa o armazenamento de um banco de dados inteiro usando uma chave simétrica chamada de chave de criptografia de banco de dados. A chave de criptografia do banco de dados está protegida por um certificado do servidor interno. O certificado do servidor interno é exclusivo para cada servidor do Azure. A Microsoft alterna automaticamente esses certificados pelo menos a cada 90 dias. Para obter uma descrição geral da TDE, consulte [Transparent Data Encryption (TDE)].

##Habilitando a criptografia

Para habilitar a TDE para um banco de dados do Azure que armazena os dados migrados de um banco de dados do SQL Server habilitado para ampliação, faça o seguinte:

1. Conecte ao banco de dados *mestre* no servidor que está hospedando o banco de dados do Azure, usando um logon de administrador ou de um membro da função **dbmanager** no banco de dados mestre
2. Execute a instrução a seguir para criptografar o banco de dados.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

##Desabilitando a criptografia

Para desabilitar a TDE para um banco de dados do Azure que armazena os dados migrados de um banco de dados do SQL Server habilitado para ampliação, faça o seguinte:

1. Conecte-se ao banco de dados *mestre* que usa um logon de administrador ou de um membro da função **dbmanager** no banco de dados mestre
2. Execute a instrução a seguir para criptografar o banco de dados.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

##Verificando a criptografia

Para verificar o status da criptografia para um banco de dados do Azure que armazena os dados migrados de um banco de dados do SQL Server habilitado para ampliação, faça o seguinte:

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


<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->

<!---HONumber=AcomDC_0518_2016-->