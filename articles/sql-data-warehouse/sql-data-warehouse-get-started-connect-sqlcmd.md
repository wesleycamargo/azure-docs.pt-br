<properties
   pageTitle="Consulta com SQLCMD | Microsoft Azure"
   description="Consultando seu Data Warehouse com SQLCMD."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/09/2016"
   ms.author="mausher;barbkess;sonyama"/>

# Consulta com SQLCMD

> [AZURE.SELECTOR]
- [Power BI][]
- [Aprendizado de Máquina do Azure][]
- [SQLCMD][]

Este passo a passo mostra como consultar o SQL Data Warehouse do Azure usando o utilitário sqlcmd.exe.

## Pré-requisitos

+ Para baixar o [sqlcmd.exe][], consulte os [Utilitários da Linha de Comando do Microsoft 11 para o SQL Server][].

## Connect

Para começar com o sqlcmd, abra o prompt de comando e digite **sqlcmd** seguido da cadeia de conexão do banco de dados SQL Data Warehouse. A cadeia de conexão precisará dos seguintes parâmetros:

+ **Servidor (-S):** Servidor no formato `<`Nome do servidor`>`.database.windows.net
+ **Banco de dados (-d):** nome do banco de dados.
+ **Usuário (-U):** usuário do servidor no formato `<`Usuário`>`
+ **Senha (-P):** senha associada ao usuário.
+ **Habilitar Identificadores entre Aspas (-I):** os identificadores entre aspas devem ser habilitados para se conectarem a uma instância do SQL Data Warehouse.

Por exemplo, a cadeia de conexão pode parecer com o seguinte:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

> [AZURE.NOTE] A opção -I, que permite identificadores entre aspas, atualmente é necessária para conectar ao SQL Data Warehouse.

## Consultar

Após a conexão, você pode executar quaisquer instruções Transact-SQL compatíveis com a instância. Neste exemplo, as consultas são enviadas no modo interativo.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Os próximos exemplos mostram como você pode executar as consultas no modo de lote usando a opção -Q ou direcionando o SQL para sqlcmd.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
C:\>"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## Próximas etapas

Para saber mais sobre todas as opções sqlcmd, confira a [documentação do sqlcmd][sqlcmd.exe].

<!--Articles-->
[connecting with PowerBI]: ./sql-data-warehouse-integrate-power-bi.md
[Visual Studio]: ./sql-data-warehouse-get-started-connect.md
[Power BI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md
[Aprendizado de Máquina do Azure]: ./sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md
[SQLCMD]: ./sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other-->
[sqlcmd.exe]: https://msdn.microsoft.com/pt-BR/library/ms162773.aspx
[Utilitários da Linha de Comando do Microsoft 11 para o SQL Server]: http://go.microsoft.com/fwlink/?LinkId=321501
[Azure portal]: https://portal.azure.com

<!--Image references-->

<!---HONumber=AcomDC_0615_2016-->