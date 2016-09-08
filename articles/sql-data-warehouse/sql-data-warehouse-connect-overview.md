<properties
   pageTitle="Conectar-se ao SQL Data Warehouse do Azure | Microsoft Azure"
   description="Visão geral da conexão com o SQL Data Warehouse do Azure"
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
   ms.date="08/27/2016"
   ms.author="sonyama;barbkess"/>

# Conectar-se ao SQL Data Warehouse do Azure

> [AZURE.SELECTOR]
- [Visão geral](sql-data-warehouse-connect-overview.md)
- [Autenticação](sql-data-warehouse-authentication.md)
- [Drivers](sql-data-warehouse-connection-strings.md)

Visão geral da conexão com o SQL Data Warehouse do Azure.

## Descobrir a cadeia de conexão do portal

O SQL Data Warehouse está associado a um SQL Server do Azure. Para conectar, você precisa do nome totalmente qualificado do servidor. Por exemplo, **myserver**.database.windows.net.

Para localizar o nome de servidor totalmente qualificado:

1. Vá para o [Portal do Azure][].
2. Clique em **Bancos de dados SQL** e clique no banco de dados ao qual você deseja se conectar. Este exemplo usa o banco de dados de exemplo AdventureWorksDW.
3. Localize o nome completo do servidor.

    ![Nome completo do servidor][1]

## Configurações de conexão

O SQL Data Warehouse padroniza algumas configurações durante a conexão e a criação do objeto. Elas não podem ser substituídas.

| Configuração de banco de dados | Valor |
| :--------------------- | :--------------------------- |
| [ANSI\_NULLS][] | ATIVADO |
| [QUOTED\_IDENTIFIERS][] | ATIVADO |
| [DATEFORMAT][] | mdy |
| [DATEFIRST][] | 7 |

## Monitorando conexões e consultas

Assim que uma conexão é criada e uma sessão é estabelecida, você está pronto para gravar e enviar consultas para o SQL Data Warehouse. Para saber como monitorar as sessões e as consultas, consulte [Monitorar sua carga de trabalho usando DMVs][].

## Próximas etapas

Para começar a consultar o data warehouse com o Visual Studio e outros aplicativos, consulte [Consulta com o Visual Studio][].

<!--Articles-->
[Consulta com o Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[Monitorar sua carga de trabalho usando DMVs]: ./sql-data-warehouse-manage-monitor.md

<!--MSDN references-->
[ANSI\_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED\_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Portal do Azure]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/get-server-name.png

<!---HONumber=AcomDC_0831_2016-->