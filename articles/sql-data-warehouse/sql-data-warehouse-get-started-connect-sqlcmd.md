<properties
   pageTitle="Introdução: conectar-se ao SQL Data Warehouse do Azure| Microsoft Azure"
   description="Introdução ao SQL Data Warehouse e execução de algumas consultas."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/07/2016"
   ms.author="mausher;barbkess;sonyama"/>

# Conectar e consultar com SQLCMD

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md)

Este passo a passo mostra como se conectar e consultar um banco de dados do SQL Data Warehouse do Azure em apenas alguns minutos usando o utilitário sqlcmd.exe. Neste passo a passo, você vai:

+ Instalar software de pré-requisito
+ Conectar-se a um banco de dados que contém o banco de dados AdventureWorksDW
+ Executar uma consulta em relação o banco de dados de exemplo  

## Pré-requisitos

+ [sqlcmd.exe](https://msdn.microsoft.com/library/azure/ms162773.aspx) - para baixar sqlcmd.exe, consulte os [Utilitários de Linha de Comando 11 da Microsoft para SQL Server](http://go.microsoft.com/fwlink/?LinkId=321501).

## Obtenha o nome de servidor totalmente qualificado do SQL Azure

Para conectar-se ao banco de dados, você precisará do nome completo do servidor (****nomedoservidor**.database.windows.net*) que contenha o banco de dados ao qual você deseja se conectar.

1. Vá para o [Portal do Azure](https://portal.azure.com).
2. Navegue até o banco de dados ao qual você deseja se conectar.
3. Localize o nome do servidor completo (vamos usá-lo nas etapas abaixo):

![][1]


## Conecte-se ao SQL Data Warehouse com sqlcmd

Para se conectar a uma instância específica do SQL Data Warehouse usando o sqlcmd, você precisará abrir o prompt de comando e inserir **sqlcmd** seguido pela cadeia de conexão do banco de dados do SQL Data Warehouse. A cadeia de conexão deverá conter os seguintes parâmetros:

+ **Usuário (-U):** usuário do servidor no formato `<`Usuário`>`
+ **Senha (-P):** senha associada ao usuário.
+ **Servidor (-S):** Servidor no formato `<`Nome do servidor`>`.database.windows.net
+ **Banco de dados (-D):** nome do banco de dados.
+ **Habilitar Identificadores entre Aspas (-I):** os identificadores entre aspas devem ser habilitados para se conectarem a uma instância do SQL Data Warehouse.

Portanto, para se conectar a uma instância do SQL Data Warehouse, insira o seguinte:

```
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
```

## Executar consultas de exemplo

Após a conexão, você pode executar quaisquer instruções Transact-SQL compatíveis com a instância.

```
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
1> SELECT COUNT(*) FROM dbo.FactInternetSales;
2> GO
3> QUIT
```

Para saber mais sobre sqlcmd, consulte a [documentação do sqlcmd](https://msdn.microsoft.com/library/azure/ms162773.aspx).


## Próximas etapas

Agora que você pode se conectar e consultar, tente [conectar-se ao PowerBI][].

[conectar-se ao PowerBI]: ./sql-data-warehouse-integrate-power-bi.md
 

<!--Image references-->
[1]: ./media/sql-data-warehouse-get-started-connect/get-server-name.png

<!---HONumber=AcomDC_0114_2016-->