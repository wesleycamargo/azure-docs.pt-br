<properties
   pageTitle="Renomear no SQL Data Warehouse | Microsoft Azure"
   description="Dicas para renomear objetos e bancos de dados no SQL Data Warehouse do Azure para desenvolvimento de soluções."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/21/2015"
   ms.author="twounder;JRJ@BigBangData.co.uk;barbkess"/>

# Renomear no SQL Data Warehouse
O SQL Server oferece suporte à renomeação de banco de dados por meio do procedimento armazenado ```sp_renamedb```. O SQL Data Warehouse usa a sintaxe DDL para atingir o mesmo objetivo. O comando DDL é ```RENAME DATABASE```.

## Renomear banco de dados

Para renomear um banco de dados, execute o comando a seguir.

```
RENAME DATABASE AdventureWorks TO Contoso;
```

É importante lembrar que não é possível renomear um banco de dados ou um objeto se outros usuários estiverem conectados a ele ou o estiverem usando. Talvez seja necessário encerrar sessões abertas primeiro. Para encerrar uma sessão, é necessário usar o comando [KILL](https://msdn.microsoft.com/library/ms173730.aspx). Tenha cuidado ao usar ```KILL```. Depois de executado, a sessão será encerrada e qualquer trabalho não confirmado será revertido.

> [AZURE.NOTE]As sessões no SQL Data Warehouse são prefixadas por 'SID', você precisará incluí-lo, e também o número da sessão, ao invocar o comando KILL. Por exemplo, ```KILL 'SID1234'``` encerraria a sessão 1234, supondo que você tenha as permissões corretas para executá-lo.

## Encerramento de sessões
Para renomear um banco de dados, talvez seja necessário encerrar sessões conectadas ao seu SQL Data Warehouse. A consulta a seguir gerará uma lista distinta de comandos KILL para limpar as conexões (salvar para a sessão atual).

```
SELECT
    'KILL ''' + session_id + ''''
FROM
    sys.dm_pdw_exec_requests r
WHERE
    r.session_id <> SESSION_ID()
    AND EXISTS
    (
        SELECT
            *
        FROM
            sys.dm_pdw_lock_waits w
        WHERE
            r.session_id = w.session_id
    )
GROUP BY
    session_id;
```

## Alterar esquemas
Se a intenção for alterar o esquema ao qual um objeto pertence, isso pode ser alcançado por meio de `ALTER SCHEMA`:

```
ALTER SCHEMA dbo TRANSFER OBJECT::product.item;
```

## Próximas etapas
Para obter mais dicas de desenvolvimento, consulte a [Visão geral de desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Visão geral de desenvolvimento]: sql-data-warehouse-overview-develop.md

<!---HONumber=Oct15_HO4-->