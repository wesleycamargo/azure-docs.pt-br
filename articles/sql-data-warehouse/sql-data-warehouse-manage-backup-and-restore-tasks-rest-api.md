<properties
   pageTitle="Fazer backup e restaurar um banco de dados no SQL Data Warehouse do Azure (REST) | Microsoft Azure"
   description="Tarefas da API REST para restaurar um banco de dados dinâmico, excluído ou inacessível no SQL Data Warehouse do Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/05/2016"
   ms.author="elfish;barbkess;sonyama"/>

# Fazer backup e restaurar um banco de dados no SQL Data Warehouse do Azure (REST)

> [AZURE.SELECTOR]
- [Visão geral](sql-data-warehouse-overview-manage-backup-and-restore.md)
- [Portal](sql-data-warehouse-manage-backup-and-restore-tasks-portal.md)
- [PowerShell](sql-data-warehouse-manage-backup-and-restore-tasks-powershell.md)
- [REST](sql-data-warehouse-manage-backup-and-restore-tasks-rest-api.md)

Como fazer backup e restaurar um banco de dados no SQL Data Warehouse do Azure usando APIs REST.

Tarefas neste tópico:

- Restaurar um banco de dados dinâmico
- Restaurar um banco de dados excluído
- Restaurar um banco de dados inacessível de outra região geográfica do Azure

[AZURE.INCLUDE [Política de retenção de backup do SQL Data Warehouse](../../includes/sql-data-warehouse-backup-retention-policy.md)]

## Antes de começar

**Verifique a capacidade de DTU do Banco de Dados SQL.** Como o SQL Data Warehouse restaura para um novo banco de dados no SQL Server lógico, é importante verificar se o SQL Server para o qual você está restaurando tem capacidade de DTU suficiente para o novo banco de dados. Consulte esta postagem no blog para obter mais informações sobre [como exibir e aumentar a cota de DTU][].

## Restaurar um banco de dados dinâmico

Para restaurar um banco de dados:

1. Obtenha a lista de pontos de restauração do banco de dados usando a operação de obtenção de pontos de restauração de banco de dados.
2. Comece sua restauração usando a operação [Criar solicitação de restauração do banco de dados][].
3. Acompanhe o status de sua restauração usando a operação [Status de operações do banco de dados][].

>[AZURE.NOTE] Depois que a restauração estiver concluída, você poderá configurar o banco de dados recuperado seguindo o guia [Finalizar um banco de dados recuperado][].

## Restaurar um banco de dados excluído

Para restaurar um banco de dados excluído

1.	Liste todos os bancos de dados excluídos restauráveis usando a operação [Listar bancos de dados descartados restauráveis][].
2.	Obtenha os detalhes do banco de dados excluído que deseja restaurar usando a operação [Obter banco de dados descartados restauráveis][].
3.	Comece sua restauração usando a operação [Criar solicitação de restauração do banco de dados][].
4.	Acompanhe o status de sua restauração usando a operação [Status de operações do banco de dados][].

>[AZURE.NOTE] Depois que a restauração estiver concluída, você poderá configurar o banco de dados recuperado seguindo o guia [Finalizar um banco de dados recuperado][].

## Restaurar geograficamente um data warehouse por meio de um backup com redundância geográfica

Para executar uma restauração geográfica:

1. Obtenha a lista de bancos de dados recuperáveis usando a operação [Listar bancos de dados recuperáveis][].
2. Obtenha o banco de dados que você deseja recuperar usando a operação [Obter banco de dados recuperável][].
3. Crie a solicitação de recuperação usando a operação [Criar solicitação de recuperação de banco de dados][].
4. Acompanhe o status da recuperação usando a operação [Status da operação de banco de dados.][].

### Configurar o banco de dados depois de executar uma restauração geográfica
Esta é uma lista de verificação para ajudar a preparar para produção seu banco de dados recuperado.

1. **Atualizar Cadeias de Conexão**: verifique se as cadeias de conexão das ferramentas cliente estão apontando para o banco de dados recentemente recuperado.
2. **Modificar as Regras de Firewall**: verifique as regras de firewall no servidor de destino para ter certeza de que as conexões dos computadores cliente ou do Azure com o servidor e com o banco de dados recentemente recuperado estão habilitadas.
3. **Verificar Logons do Servidor e Usuários do Banco de Dados**: verifique se todos os logons usados pelo aplicativo existem no servidor que está hospedando o banco de dados recuperado. Recrie os logons ausentes e conceda a eles permissões apropriadas no banco de dados recuperado. 
4. **Habilitar a Auditoria**: se a auditoria for necessária para acessar o banco de dados, você precisará habilitar a Auditoria após a recuperação do banco de dados.

O banco de dados recuperado será habilitado para TDE se o banco de dados de origem for habilitado para TDE.


## Próximas etapas
Para saber mais sobre os recursos de continuidade de negócios das edições do Banco de Dados SQL do Azure, leia a [Visão geral de continuidade de negócios do Banco de Dados SQL do Azure][].

<!--Image references-->

<!--Article references-->
[Visão geral de continuidade de negócios do Banco de Dados SQL do Azure]: sql-database-business-continuity.md
[Finalizar um banco de dados recuperado]: sql-database-recovered-finalize.md
[How to install and configure Azure PowerShell]: powershell-install-configure.md

<!--MSDN references-->
[Criar solicitação de restauração do banco de dados]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[Status da operação de banco de dados.]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[Status de operações do banco de dados]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[Obter banco de dados descartados restauráveis]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[Listar bancos de dados descartados restauráveis]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Blog references-->
[como exibir e aumentar a cota de DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0518_2016-->