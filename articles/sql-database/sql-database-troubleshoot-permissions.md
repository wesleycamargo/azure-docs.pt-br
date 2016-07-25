<properties
	pageTitle="Como executar tarefas de administração, por exemplo, redefinir a senha de administrador | Microsoft Azure"
	description="Descreve como executar tarefas administrativas comuns no banco de dados SQL. Por exemplo, redefinir a senha do administrador, conceder e remover acesso."
	services="sql-database"
	documentationCenter=""
	authors="v-shysun"
	manager="felixwu"
	editor=""
	keywords="redefinir senha de administrador"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="v-shysun"/>

# Como executar tarefas administrativas comuns, como a redefinição de senha de administrador no Banco de Dados SQL
Use este tópico para conhecer as etapas rápidas de concessão e remoção do acesso de um Banco de Dados SQL do Azure. Para obter informações mais completas, confira:

- [Gerenciamento de bancos de dados e logons no Banco de Dados SQL do Azure](sql-database-manage-logins.md)
- [Proteção do Banco de Dados SQL](sql-database-security.md)
- [Central de segurança do mecanismo de banco de dados do SQL Server e banco de dados SQL do Azure](https://msdn.microsoft.com/library/bb510589)


[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## Para redefinir a senha de administrador para um servidor lógico

- No [Portal do Azure](https://portal.azure.com), clique em **Servidores SQL**, escolha o servidor na lista e clique em **Redefinir Senha**.

## Para assegurar que somente endereços IP autorizados tenham permissão de acesso ao servidor
- Confira [Como definir as configurações de firewall no Banco de Dados SQL](sql-database-configure-firewall-settings.md).

## Para criar usuários de banco de dados contidos no banco de dados do usuário
- Use a instrução [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx) e veja [Usuários de bancos de dados independentes – Tornando seu banco de dados portátil](https://msdn.microsoft.com/library/ff929188.aspx).

## Para autenticar os usuários de banco de dados independente usando o Active Directory do Azure
- Veja [Conectar-se ao Banco de Dados SQL usando a autenticação do Azure Active Directory](sql-database-aad-authentication.md).

## Para criar logons adicionais para usuários com privilégios elevados no banco de dados mestre virtual
- -Use a instrução [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx) e confira a seção Gerenciamento de Logons de [Gerenciamento de bancos de dados e logons no Banco de Dados SQL do Azure](sql-database-manage-logins.md) para obter mais detalhes.

<!---HONumber=AcomDC_0713_2016-->