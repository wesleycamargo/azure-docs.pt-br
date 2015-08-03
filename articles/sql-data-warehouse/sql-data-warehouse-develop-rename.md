<properties
   pageTitle="Renomear no SQL Data Warehouse | Microsoft Azure"
   description="Dicas para renomear objetos e bancos de dados no SQL Data Warehouse do Azure para desenvolvimento de soluções."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/26/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Renomear no SQL Data Warehouse
O SQL Server oferece suporte à ação de renomear objetos e bancos de dados por meio dos procedimentos armazenados sp_rename e sp_renamedb, respectivamente.

O SQL Data Warehouse usa a sintaxe DDL para atingir o mesmo objetivo. Os comandos DDL são RENAME OBJECT e RENAME DATABASE.

## Renomear o objeto

É importante entender que o nome só afeta o objeto que está sendo renomeado, não há qualquer propagação da alteração do nome. Consequentemente, quaisquer exibições usando o nome antigo de um objeto serão inválidas até que um objeto com o nome antigo tenha sido criado. Assim, você verá frequentemente `RENAME OBJECT` aparecendo em pares.

```
RENAME OBJECT product.item to item_old;
RENAME OBJECT product.item_new to item;
```

## Renomear banco de dados

A renomeação de bancos de dados é muito semelhante à de objetos.

```
RENAME DATABASE AdventureWorks TO Contoso;
```

É importante lembrar que não é possível renomear um banco de dados ou um objeto se outros usuários estiverem conectados a ele ou o estiverem usando. Talvez seja necessário encerrar sessões abertas primeiro. Para encerrar uma sessão, é necessário usar o comando [KILL][]. Tenha cuidado ao usar KILL. Depois de executado, a sessão será encerrada e qualquer trabalho não confirmado será revertido.

> [AZURE.NOTE]As sessões no SQL Data Warehouse são prefixadas por 'SID', você precisará incluí-lo, e também o número da sessão, ao invocar o comando KILL. Por exemplo, KILL 'SID1234' encerraria a sessão 1234, supondo que você tenha as permissões corretas para executá-lo.

## Encerramento de sessões
Para renomear um banco de dados, talvez seja necessário encerrar sessões conectadas ao seu SQL Data Warehouse. A consulta a seguir gerará uma lista distinta de comandos KILL para limpar as conexões (salvar para a sessão atual).

```
SELECT 'KILL '''+session_id+''''
FROM	sys.dm_pdw_exec_requests r
WHERE r.session_id <> SESSION_ID()
AND EXISTS
(	SELECT 	*
	FROM 	sys.dm_pdw_lock_waits w
	WHERE 	r.session_id = w.session_id
)
GROUP BY session_id
;
```

## Alterar esquemas
Se a intenção for alterar o esquema ao qual um objeto pertence, isso pode ser alcançado por meio de `ALTER SCHEMA`:

```
ALTER SCHEMA dbo TRANSFER OBJECT::product.item;
```


## Próximas etapas
Para obter mais dicas de desenvolvimento, consulte [Visão geral do desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Visão geral do desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[KILL]: https://msdn.microsoft.com/en-us/library/ms173730.aspx

<!--Other Web references-->
[Azure management portal]: http://portal.azure.com/

<!---HONumber=July15_HO4-->