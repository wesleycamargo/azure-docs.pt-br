---
title: 'T-SQL: regras de firewall do Banco de dados SQL | Microsoft Docs'
description: "Saiba como configurar as regras de firewall de nível de banco de dados e de nível de servidor dos endereços IP que acessam os bancos de dados SQL do Azure usando o Transact-SQL."
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
ms.assetid: 71e692a1-5e2f-4a18-a6d6-527b849cf68e
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/07/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: 84efba61fbb0248edef3036de702e3a1aa8ae903
ms.lasthandoff: 02/17/2017


---
# <a name="configure-azure-sql-database-server-level-and-database-level-firewall-rules-using-t-sql"></a>Configurar regras de firewall no nível do servidor e do banco de dados do Banco de Dados SQL usando o T-SQL

O Banco de Dados SQL do Microsoft Azure usa regras de firewall para permitir conexões com servidores e bancos de dados. Você pode definir configurações de firewall nos níveis de banco de dados e de servidor para o banco de dados de usuário ou mestre no seu servidor do Banco de Dados SQL do Azure a fim de permitir seletivamente o acesso ao banco de dados.

> [!IMPORTANT]
> Para permitir que os aplicativos do Azure se conectem ao seu servidor de banco de dados, as conexões do Azure devem estar habilitadas. Para obter mais informações sobre regras de firewall e como habilitar conexões do Azure, confira [Firewall do Banco de Dados SQL do Azure](sql-database-firewall-configure.md). Se você estiver fazendo conexões dentro do limite de nuvem do Azure, terá que abrir algumas portas TCP adicionais. Para obter mais informações, veja a seção **V12 do Banco de Dados SQL: fora versus dentro** de [Portas além da 1433 para ADO.NET 4.5 e Banco de Dados SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md)
> 
> 

## <a name="server-level-firewall-rules"></a>Regras de firewall no nível do servidor
Somente o logon do administrador do servidor SQL do Azure ou o administrador do Azure Active Directory pode criar uma regra de firewall de nível de servidor usando o Transact-SQL.

1. Inicie uma janela de consulta e conecte-se ao banco de dados mestre virtual usando o SQL Server Management Studio.
2. As regras de firewall no nível de servidor podem ser selecionadas, criadas, atualizadas ou excluídas de dentro da janela de consulta.
3. Para criar ou atualizar regras de firewall no nível de servidor, execute o procedimento armazenado `sp_set_firewall_rule` . O exemplo a seguir habilita um intervalo de endereços IP no servidor Contoso.<br/>Comece vendo quais regras já existem.
   
        SELECT * FROM sys.firewall_rules ORDER BY name;
   
    Em seguida, adicione uma regra de firewall.
   
        EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
            @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
   
    Para excluir uma regra de firewall no nível de servidor, execute o procedimento armazenado sp_delete_firewall_rule. O exemplo a seguir exclui a regra denominada ContosoFirewallRule.
   
        EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
   
   Para saber mais sobre esses procedimentos armazenados, confira [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) e [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx).

## <a name="database-level-firewall-rules"></a>Regras de firewall no nível de banco de dados
Somente um usuário de banco de dados com a permissão **CONTROL** no banco de dados (como o proprietário do banco de dados) pode criar uma regra de firewall no nível de banco de dados.

1. Depois de criar um firewall no nível de servidor para seu endereço IP, inicie uma janela de consulta por meio do portal do Azure ou do SQL Server Management Studio.
2. Conecte-se ao banco de dados para o qual você deseja criar uma regra de firewall no nível de banco de dados.
   
    Para criar uma nova regra ou atualizar uma regra de firewall existente no nível de banco de dados, execute o procedimento armazenado `sp_set_database_firewall_rule` . O exemplo a seguir cria uma nova regra de firewall denominada ContosoFirewallRule.
   
        EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', 
            @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
   
    Para excluir uma regra de firewall no nível do banco de dados, execute o procedimento armazenado `sp_delete_database_firewall_rule` . O exemplo a seguir exclui a regra denominada ContosoFirewallRule.
   `
   
        EXEC sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'

Para saber mais sobre esses procedimentos armazenados, veja [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) e [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx).

> [!NOTE]
> Para obter um tutorial que demonstra o uso de firewalls de nível de banco de dados, consulte [Autenticação e autorização do SQL](sql-database-control-access-sql-authentication-get-started.md).
>


## <a name="next-steps"></a>Próximas etapas
Para ver artigos sobre como criar regras de firewall no nível de servidor usando outros métodos, confira: 

* [Configurar regras de firewall no nível de servidor do Banco de Dados SQL do Azure usando o Portal do Azure](sql-database-configure-firewall-settings.md)
* [Configurar regras de firewall no nível de servidor do Banco de Dados SQL do Azure usando o PowerShell](sql-database-configure-firewall-settings-powershell.md)
* [Configurar regras de firewall no nível de servidor do Banco de Dados SQL do Azure usando a API REST](sql-database-configure-firewall-settings-rest.md)

Para ver um tutorial sobre como criar um banco de dados, consulte [Seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md).
Para obter ajuda com a conexão de um Banco de Dados SQL do Azure a partir de aplicativos de fonte aberta ou de terceiros, consulte [Exemplos de código do cliente de início rápido para o Banco de Dados SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Para entender como navegar para os bancos de dados, consulte [Gerenciar o acesso ao banco de dados e a segurança de logon](https://msdn.microsoft.com/library/azure/ee336235.aspx).

## <a name="additional-resources"></a>Recursos adicionais
* [Protegendo o banco de dados](sql-database-security-overview.md)
* [Central de segurança do mecanismo de banco de dados do SQL Server e banco de dados SQL do Azure](https://msdn.microsoft.com/library/bb510589)


