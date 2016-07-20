<properties
	pageTitle="Replicação Geográfica Ativa para o Banco de Dados SQL do Azure"
	description="A Replicação Geográfica Ativa permite configurar 4 réplicas de seu banco de dados em qualquer um dos datacenters do Azure."
	services="sql-database"
	documentationCenter="na"
	authors="stevestein"
	manager="jhubbard"
	editor="monicar" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
   ms.workload="sqldb-bcdr"
	ms.date="06/14/2016"
	ms.author="sstein" />

# Visão geral: Replicação Geográfica Ativa para o Banco de Dados SQL

A Replicação Geográfica Ativa permite que você configure até 4 bancos de dados secundários legíveis, na mesma localização de centro de dados ou em localizações (regiões) diferentes. Os bancos de dados secundários estão disponíveis para consulta e failover no caso de uma paralisação do data center ou da incapacidade de conectar ao banco de dados primário.

>[AZURE.NOTE] Replicação Geográfica Ativa (secundários legíveis) agora está disponível para todos os bancos de dados em todas as camadas de serviço. Em abril de 2017 o tipo de secundário não legível será descontinuado e bancos de dados não legíveis existentes serão automaticamente atualizados para secundários legíveis.

 Você pode configurar a Replicação geográfica ativa usando o [Portal do Azure](sql-database-geo-replication-portal.md), o [PowerShell](sql-database-geo-replication-powershell.md), [Transact-SQL](sql-database-geo-replication-transact-sql.md) ou [API REST - criar ou atualizar banco de dados](https://msdn.microsoft.com/library/azure/mt163685.aspx).

> [AZURE.SELECTOR]
- [Configurar o Portal do Azure](sql-database-geo-replication-portal.md)
- [Configurar o PowerShell](sql-database-geo-replication-powershell.md)
- [Configurar o T-SQL](sql-database-geo-replication-transact-sql.md)

Se, por qualquer motivo, o seu banco de dados primário falhar ou simplesmente precisar ser colocado offline, você poderá fazer *failover* para qualquer um dos seus bancos de dados secundários. Quando o failover é ativado para um dos bancos de dados secundários, todos os outros secundários são vinculados automaticamente ao novo primário.

Você pode fazer o failover para um secundário usando o [Portal do Azure](sql-database-geo-replication-failover-portal.md), o [PowerShell](sql-database-geo-replication-failover-powershell.md), [Transact-SQL](sql-database-geo-replication-failover-transact-sql.md), a [API REST - Failover planejado](https://msdn.microsoft.com/ibrary/azure/mt575007.aspx) ou a [API REST - Failover não planejado](https://msdn.microsoft.com/library/azure/mt582027.aspx).


> [AZURE.SELECTOR]
- [Failover: Portal do Azure](sql-database-geo-replication-failover-portal.md)
- [Failover: PowerShell](sql-database-geo-replication-failover-powershell.md)
- [Failover: T-SQL](sql-database-geo-replication-failover-transact-sql.md)

O recurso de Replicação Geográfica Ativa implementa um mecanismo para fornecer a redundância de banco de dados na mesma região do Microsoft Azure ou em regiões diferentes (redundância geográfica). A Replicação Geográfica Ativa replica de forma assíncrona as transações confirmadas de um banco de dados para até quatro cópias do banco de dados em servidores diferentes usando o RCSI (isolamento de instantâneo confirmando por leitura) para o isolamento. Quando a Replicação Geográfica Ativa é configurada, um banco de dados secundário é criado no servidor especificado. O banco de dados original se torna o banco de dados primário. O banco de dados primário replica de forma assíncrona as transações confirmadas para cada um dos bancos de dados secundários. Embora, a qualquer momento, o banco de dados secundário possa estar um pouco atrás do banco de dados primário, os dados secundários têm a garantia de sempre serem transacionalmente consistentes com as alterações confirmadas no banco de dados primário.

Um dos principais benefícios da Replicação Geográfica Ativa é que ela fornece uma solução de recuperação de desastre no nível do banco de dados com tempo de recuperação muito baixo. Ao colocar o banco de dados secundário em um servidor em uma região diferente, você adiciona resiliência máxima ao aplicativo. A redundância entre regiões habilita os aplicativos a se recuperar de uma perda permanente de um datacenter inteiro ou de partes de um datacenter, causada por desastres naturais, falhas humanas catastróficas ou crimes. A figura a seguir mostra que um exemplo de Replicação Geográfica Ativa configurada em um banco de dados Premium com um primário na região Centro-Norte dos EUA e um secundário na região Centro-Sul dos EUA.

![Relacionamento de Replicação Geográfica](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Outro importante benefício é que os bancos de dados secundários são legíveis e podem ser usados para descarregar cargas de trabalho somente leitura, como trabalhos de relatórios. Se pretende usar o banco de dados secundário apenas para o balanceamento de carga, você pode criá-lo na mesma região que o primário. No entanto, isso não aumentará a resiliência do aplicativo a falhas catastróficas.

Entre outros cenários em que a Replicação Geográfica Ativa pode ser usada estão:

- **Migração de banco de dados**: você pode usar a Replicação Geográfica Ativa para migrar um banco de dados de um servidor para outro online com tempo de inatividade mínimo.
- **Atualizações de aplicativos**: você pode criar um secundário extra como uma cópia de failback durante as atualizações de aplicativos.

Para garantir a continuidade de negócios real, a adição de redundância de banco de dados entre datacenters é apenas parte da solução. A recuperação de um aplicativo (serviço) de ponta a ponta após uma falha catastrófica exige a recuperação de todos os componentes que constituem o serviço e quaisquer serviços dependentes. O software cliente (por exemplo, um navegador com um JavaScript personalizado), front-ends da Web, armazenamento e DNS são exemplos desses componentes. É fundamental que todos os componentes sejam resilientes às mesmas falhas e fiquem disponíveis dentro do RTO (objetivo de tempo de recuperação) de seu aplicativo. Portanto, você precisa identificar todos os serviços dependentes e entender as garantias e os recursos que eles fornecem. Em seguida, você deve tomar as medidas necessárias para garantir que seu serviço funcione durante o failover dos serviços dos quais ele depende. Para obter mais informações sobre como criar soluções para a recuperação de desastre, veja [Criando soluções de nuvem para a recuperação de desastre usando a Replicação Geográfica Ativa](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## Recursos da replicação geográfica ativa
O recurso de Replicação Geográfica Ativa fornece os seguintes recursos essenciais:

- **Replicação Assíncrona Automática**: você só pode criar um banco de dados secundário adicionando-o a um banco de dados existente. O secundário só pode ser criado em um servidor de Banco de Dados SQL do Azure diferente. Depois de criado, o banco de dados secundário é populado com os dados copiados do banco de dados primário. Este processo é conhecido como propagação. Depois que o banco de dados secundário for criado e propagado, as atualizações para o banco de dados primário serão replicadas de forma assíncrona para o banco de dados secundário automaticamente. Isso significa que as transações são confirmadas no banco de dados primário antes de serem replicadas para o banco de dados secundário. O Banco de Dados SQL garante que, após a conclusão da propagação, o banco de dados secundário permaneça sempre transacionalmente consistente.

- **Vários bancos de dados secundários**: dois ou mais bancos de dados secundários aumentam a redundância e o nível de proteção para o banco de dados primário e o aplicativo. Se existirem vários bancos de dados secundários, o aplicativo permanecerá protegido mesmo se houver uma falha em um dos bancos de dados secundários. Se houver apenas um banco de dados secundário e ele falhar, o aplicativo será exposto a um risco maior até que um novo banco de dados secundário seja criado.

- **Bancos de dados secundários legíveis**: um aplicativo pode acessar um banco de dados secundário para operações somente leitura usando as mesmas entidades de segurança usadas para acessar o banco de dados primário, ou outras diferentes. Os bancos de dados secundários operam no modo de isolamento de instantâneo para garantir que a replicação das atualizações do primário (repetição de log) não seja atrasada por consultas executadas no secundário.

>[AZURE.NOTE] A repetição de log será atrasada no secundário se houver atualizações de esquema que ele esteja recebendo do Primário, pois isso requer um bloqueio de esquema no banco de dados secundário.

- **Replicação Geográfica Ativa de bancos de dados do pool elástico**: a Replicação Geográfica Ativa pode ser configurada para qualquer banco de dados em qualquer pool de banco de dados elástico. O banco de dados secundário pode estar em outro pool de banco de dados elástico. Para bancos de dados regulares, o secundário pode ser um pool de banco de dados elástico e vice-versa, contanto que as camadas de serviço sejam as mesmas.

- **Nível de desempenho configurável do banco de dados secundário**: um banco de dados secundário pode ser criado com um nível de desempenho menor do que o do primário. Os bancos de dados primário e secundário devem ter a mesma camada de serviço. Essa opção não é recomendada para aplicativos com elvada atividade de gravação de banco de dados, pois isso pode resultar em um maior retardo de replicação e, assim, existe um alto risco de perda de dados substancial após o failover. Além disso, após o failover, o desempenho do aplicativo será afetado até que o novo primário seja atualizado para um nível mais alto de desempenho. O gráfico de percentual de E/S de log no Portal do Azure fornece uma boa maneira de estimar o nível mínimo de desempenho do secundário que será necessário para sustentar a carga de replicação. Por exemplo, se o banco de dados Primário for P6 (1000 DTUS) e seu percentual de E/S de log for 50%, o secundário precisará ser pelo menos P4 (500 DTU). Você também pode recuperar os dados de E/S de log usando as exibições de banco de dados [sys.resource\_stats](https://msdn.microsoft.com/library/dn269979.aspx) ou [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx). Para obter mais informações sobre os níveis de desempenho do Banco de Dados SQL, confira [Opções de Banco de Dados SQL e desempenho](sql-database-service-tiers.md).

- **Failover e failback controlados pelo usuário**: um banco de dados secundário pode ser alternado para a função primária a qualquer momento por meio de uma ação explícita do aplicativo ou do usuário. Durante uma interrupção real, deve ser usada a opção "não planejada", que promoverá imediatamente um secundário para primário. Quando o primário com falha se recuperar e estiver disponível novamente, o sistema o marcará automaticamente como um secundário e o atualizará de acordo com o novo primário. Devido à natureza assíncrona da replicação, uma pequena quantidade de dados poderá ser perdida durante failovers não planejados se o primário falhar antes de replicar as alterações mais recentes para o secundário. Quando um primário com vários secundários passar por failover, o sistema automaticamente reconfigurará as relações de replicação e vinculará os secundários restantes para o primário recém-promovido, sem a necessidade de intervenção do usuário. Depois que a interrupção que causou o failover for reduzida, poderá ser desejável retornar o aplicativo para a região primária. Para fazer isso, o comando de failover deve ser invocado com a opção "planejada".

- **Como manter regras de firewall e credenciais em sincronia**: recomendamos o uso de [regras de firewall de banco de dados](sql-database-firewall-configure.md) para bancos de dados com replicação geográfica, de modo que essas regras possam ser replicadas com o banco de dados para garantir que todos os bancos de dados secundários tenham a mesma configuração de firewall que o primário. Isso elimina a necessidade de os clientes configurarem manualmente e manterem as regras de firewall nos servidores que hospedam os bancos de dados primários e secundários. Da mesma forma, o uso de [usuários de banco de dados independente](sql-database-manage-logins.md) para o acesso a dados garante que os bancos de dados primários e secundários sempre tenham as mesmas credenciais de usuário para que, em caso de failovers, não haja interrupções devido à incompatibilidade nos logons e senhas. Com a adição de [Azure Active Directory](../active-directory/active-directory-whatis.md), os clientes podem gerenciar o acesso do usuário aos bancos de dados primários e secundários, eliminando a necessidade de gerenciamento de credenciais em todos os bancos de dados juntos.

## Evitando a perda de dados críticos
Devido à alta latência das redes de longa distância, a cópia contínua usa um mecanismo de replicação assíncrona. Isso tornará a perda de alguns dados inevitável se ocorrer uma falha. No entanto, alguns aplicativos podem exigir nenhuma perda de dados. Para proteger essas atualizações críticas, um desenvolvedor de aplicativo pode chamar o procedimento de sistema [sp\_wait\_for\_database\_copy\_sync](https://msdn.microsoft.com/library/dn467644.aspx) imediatamente após a confirmação da transação. Chamar **sp\_wait\_for\_database\_copy\_sync** bloqueia o thread de chamada até que a última transação confirmada seja replicada para o banco de dados secundário. O procedimento aguardará até que todas as transações na fila tenham sido confirmadas pelo banco de dados secundário. **sp\_wait\_for\_database\_copy\_sync** é delimitado para um link de uma cópia contínua específico. Qualquer usuário com os direitos de conexão para o banco de dados primário pode chamar este procedimento.

>[AZURE.NOTE] O atraso causado por uma chamada de procedimento **sp\_wait\_for\_database\_copy\_sync** pode ser significativo. O atraso dependerá do tamanho do comprimento de log de transação no momento e não retornará até que o log inteiro seja replicado. Evite chamar esse procedimento, a menos que seja absolutamente necessário.

## Gerenciando a replicação geográfica ativa programaticamente

Conforme discutido acima, além do Portal do Azure, a Replicação geográfica ativa pode ser gerenciada programaticamente usando o Azure PowerShell e a API REST. As tabelas a seguir descrevem o conjunto de comandos disponíveis.

- **API do Azure Resource Manager e segurança baseada em funções**: a Replicação Geográfica Ativa inclui um conjunto de [APIs do ARM (Azure Resource Manager)](https://msdn.microsoft.com/library/azure/mt163571.aspx) para gerenciamento, incluindo [cmdlets do PowerShell baseados no ARM](sql-database-geo-replication-powershell.md). Essas APIs exigem o uso de grupos de recursos e dão suporte a RBAC (segurança baseada em funções). Para obter mais informações sobre como implementar funções de acesso, confira [Controle de Acesso Baseado em Funções do Azure](../active-directory/role-based-access-control-configure.md).

>[AZURE.NOTE] Muitos dos novos recursos de Replicação Geográfica Ativa só têm suporte usando a [API REST do Azure SQL](https://msdn.microsoft.com/library/azure/mt163571.aspx) e [cmdlets do PowerShell do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx) baseados em [ARM (Azure Resource Manager)](../resource-group-overview.md). A [API REST do Gerenciamento de Serviços SQL do Azure (clássico)](https://msdn.microsoft.com/library/azure/dn505719.aspx) e os [cmdlets do Banco de Dados SQL do Azure (clássico)](https://msdn.microsoft.com/library/azure/dn546723.aspx) existentes têm suporte para compatibilidade com versões anteriores, portanto, é recomendável usar as APIs baseadas em ARM.


### Transact-SQL

|Command|Descrição|
|-------|-----------|
|[ALTER DATABASE (Banco de Dados SQL do Azure)]https://msdn.microsoft.com/pt-BR/library/mt574871.aspx)|Use o argumento ADD SECONDARY ON SERVER para criar um banco de dados secundário para um banco de dados existente e inicie a replicação de dados|
|[ALTER DATABASE (Banco de Dados SQL do Azure)]https://msdn.microsoft.com/pt-BR/library/mt574871.aspx)|Usar o FAILOVER ou FORCE\_FAILOVER\_ALLOW\_DATA\_LOSS para alternar um banco de dados secundário para primário a fim de iniciar o failover
|[ALTER DATABASE (Banco de Dados SQL do Azure)]https://msdn.microsoft.com/pt-BR/library/mt574871.aspx)|Use REMOVE SECONDARY ON SERVER para encerrar uma replicação de dados entre um Banco de Dados SQL e o banco de dados secundário especificado.|
|[sys.geo\_replication\_links (Banco de Dados SQL do Azure)](https://msdn.microsoft.com/library/mt575501.aspx)|Retorna informações sobre todos os links de replicação existentes para cada banco de dados no servidor lógico do Banco de Dados SQL.|
|[sys.dm\_geo\_replication\_link\_status (Banco de Dados SQL do Azure)](https://msdn.microsoft.com/library/mt575504.aspx)|Obtém a hora da última replicação, latência da última replicação e outras informações sobre o link de replicação para um determinado Banco de Dados SQL.|
|[sys.dm\_operation\_status (Banco de Dados SQL do Azure)](https://msdn.microsoft.com/library/dn270022.aspx)|Mostra o status de todas as operações de banco de dados, incluindo o status dos links de replicação.|
|[sp\_wait\_for\_database\_copy\_sync (Banco de Dados SQL do Azure)](https://msdn.microsoft.com/library/dn467644.aspx)|faz com que o aplicativo espere até que todas as transações confirmadas sejam replicadas e reconhecidas pelo banco de dados secundário ativo.|
||||

### PowerShell

|Cmdlet|Descrição|
|------|-----------|
|[Get-AzureRmSqlDatabase](https://msdn.microsoft.com/pt-BR/library/azure/mt603648.aspx)|Obtém um ou mais bancos de dados.|
|[New-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx)|Cria um banco de dados secundário para um banco de dados existente e inicia a replicação de dados.|
|[Set-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/pt-BR/library/mt619393.aspx)|Alterna um banco de dados secundário para primário a fim de iniciar o failover.|
|[Remove-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/pt-BR/library/mt603457.aspx)|Encerra uma replicação de dados entre um Banco de Dados SQL e o banco de dados secundário especificado.|
|[Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx)|Obtém os links de replicação geográfica entre um Banco de Dados SQL do Azure e um grupo de recursos ou do SQL Server.|
||||

### API REST

|API|Descrição|
|---|-----------|
|[REST (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx)|Cria, atualiza ou restaura um banco de dados primário ou secundário.|
|[Obter, Criar ou Atualizar o Status de um Banco de Dados](https://msdn.microsoft.com/library/azure/mt643934.aspx)|Retorna o status durante uma operação de criação.|
|[Definir o banco de dados secundário como primário r (Failover planejado)](https://msdn.microsoft.com/ibrary/azure/mt575007.aspx)|Promova um banco de dados secundário em uma parceria de replicação geográfica como o novo banco de dados primário.|
|[Definir o banco de dados secundário como primário r (Failover não planejado)](https://msdn.microsoft.com/library/azure/mt582027.aspx)|Para forçar um failover para o banco de dados secundário e definir o secundário como primário.|
|[Obter Links de replicação](https://msdn.microsoft.com/library/azure/mt600929.aspx)|Obtém todos os links de replicação para um determinado Banco de Dados SQL em uma parceria de replicação geográfica. Recupera as informações visíveis no modo de exibição de catálogo sys.geo\_replication\_links.|
|[Obter link de replicação](https://msdn.microsoft.com/library/azure/mt600778.aspx)|Obtém um link de replicação específico para um determinado Banco de Dados SQL em uma parceria de replicação geográfica. Recupera as informações visíveis no modo de exibição de catálogo sys.geo\_replication\_links.|
||||



## Próximas etapas

- Para obter uma visão geral sobre a continuidade de negócios, veja [Visão geral da continuidade de negócios](sql-database-business-continuity.md)
- Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, confira [SQL Database automated backups (Backups automatizados do Banco de Dados SQL)](sql-database-automated-backups.md)
- Para saber mais sobre cenários de design e recuperação de continuidade dos negócios, veja [Cenários de continuidade](sql-database-business-continuity-scenarios.md)
- Para saber mais sobre como usar backups automatizados de recuperação, veja [Restaurar um banco de dados de backups iniciados pelo serviço](sql-database-recovery-using-backups.md)
- Para saber mais sobre como usar backups automatizados de arquivamento, veja [Cópia de banco de dados](sql-database-copy.md)

<!---HONumber=AcomDC_0706_2016-->