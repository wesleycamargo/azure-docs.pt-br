---
title: Configurar uma regra de firewall no servidor de Banco de Dados SQL | Microsoft Docs
description: "Saiba como configurar o firewall para endereços IP que acessam o Azure SQL Server."
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
ms.assetid: c3b206b5-af6e-41af-8306-db12ecfc1b5d
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 11/28/2016
ms.author: rickbyh;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 867f06c1fae3715ab03ae4a3ff4ec381603e32f7
ms.openlocfilehash: 1479595fb7de2e0a37520c7d323624142e624af1


---
# <a name="create-and-manage-azure-sql-database-server-level-firewall-rules-using-the-azure-portal"></a>Criar e gerenciar regras de firewall no nível do servidor do Banco de Dados SQL do Azure usando o portal do Azure
> [!div class="op_single_selector"]
> * [Visão geral](sql-database-firewall-configure.md)
> * [Portal do Azure](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [API REST](sql-database-configure-firewall-settings-rest.md)
> 

As regras de firewall de nível de servidor permitem que os administradores acessem um servidor de Banco de Dados SQL de um endereço IP especificado ou do intervalo de endereços IP. Você tambem pode usar as regras de firewall no nível do servidor quando tiver muitos bancos de dados com os mesmos requisitos de acesso e não quer gastar tempo configurando cada um individualmente. A Microsoft recomenda o uso de regras de firewall no nível do banco de dados sempre que possível, a fim de tornar seu banco de dados mais portátil. Para obter uma visão geral dos firewalls de Banco de Dados SQL, confira a [Visão geral das regras de firewall do Banco de Dados SQL](sql-database-firewall-configure.md).

> [!Note]
> Para obter informações sobre bancos de dados portáteis no contexto de continuidade de negócios, confira [Requisitos de autenticação para a recuperação de desastres](sql-database-geo-replication-security-config.md).
>

[!INCLUDE [Create SQL Database firewall rule](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Gerenciar regras de firewall existentes no nível de servidor pelo Portal do Azure
Repita as etapas para gerenciar as regras de firewall de nível de servidor.

* Para adicionar o computador atual, clique em Adicionar IP do cliente.
* Para adicionar mais endereços IP, digite o Nome da Regra, o Endereço IP Inicial e o Endereço IP Final.
* Para modificar uma regra existente, clique em qualquer um dos campos na regra e modifique.
* Para excluir uma regra existente, passe o mouse sobre a regra até que o X apareça no final da linha. Clique no X para remover a regra.

Clique em **Salvar** para salvar as alterações.

## <a name="next-steps"></a>Próximas etapas

- Para obter um tutorial de introdução, confira [Tutorial do Banco de Dados SQL: criar um servidor, uma regra de firewall de nível de servidor, um banco de dados de exemplo, uma regra de firewall de nível de banco de dados e conectar-se com o SQL Server](sql-database-get-started.md).
- Para obter uma introdução ao tutorial de segurança, confira [Introdução à segurança](sql-database-get-started-security.md)
- Para obter ajuda com a conexão de um Banco de Dados SQL do Azure a partir de aplicativos de fonte aberta ou de terceiros, consulte [Exemplos de código do cliente de início rápido para o Banco de Dados SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).
- Para entender como criar usuários adicionais que podem se conectar aos bancos de dados, consulte [Autenticação e Autorização do Banco de Dados SQL: Concessão de Acesso](https://msdn.microsoft.com/library/azure/ee336235.aspx).

## <a name="additional-resources"></a>Recursos adicionais
* [Protegendo o banco de dados](sql-database-security.md)   
* [Central de segurança do mecanismo de banco de dados do SQL Server e banco de dados SQL do Azure](https://msdn.microsoft.com/library/bb510589)   






<!--HONumber=Dec16_HO2-->


