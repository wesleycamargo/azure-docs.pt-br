<properties
	pageTitle="Como gerenciar a segurança após a recuperação de desastre"
	description="Este tópico explica as considerações sobre segurança para gerenciar cenários de Replicação Geográfica Ativa para o Banco de Dados SQL."
	services="sql-database"
	documentationCenter="na"
	authors="carlrabeler"
	manager="jhubbard"
	editor="monicar" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="05/10/2016"
	ms.author="carlrab" />

# Como gerenciar a segurança após a recuperação de desastre

>[AZURE.NOTE] [Active Geo-Replication](sql-database-geo-replication-overview.md) agora está disponível para todos os bancos de dados em todas as camadas de serviço.

## Visão geral dos requisitos de autenticação para a recuperação de desastre

Este tópico descreve os requisitos de autenticação para configurar e controlar a [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md) e as etapas necessárias para configurar o acesso de usuário ao banco de dados secundário. Ele também descreve como habilitar o acesso ao banco de dados recuperado depois de usar a restauração geográfica. Para obter mais informações sobre as opções de recuperação, consulte [Recuperar um Banco de Dados SQL do Azure de uma interrupção](sql-database-disaster-recovery.md).

## Recuperação de desastre com usuários independentes

Com a [versão V12 do Banco de Dados SQL do Azure](sql-database-v12-whats-new.md), o Banco de Dados SQL agora dá suporte a usuários independentes. Ao contrário de usuários tradicionais, que devem ser mapeados para logons no banco de dados mestre, um usuário independente é totalmente gerenciado pelo próprio banco de dados. Isso oferece dois benefícios. No cenário de recuperação de desastre, os usuários podem continuar a conectar ao novo banco de dados primário recuperado usando restauração geográfica sem qualquer configuração adicional, pois o banco de dados gerencia os usuários. Também há possíveis benefícios de desempenho e escalabilidade com esta configuração de uma perspectiva de logon. Para obter mais informações, veja [Usuários de bancos de dados independentes - Tornando seu banco de dados portátil](https://msdn.microsoft.com/library/ff929188.aspx).

A principal desvantagem é que gerenciar o processo de recuperação de desastre em grande escala é mais desafiador. Quando você tiver vários bancos de dados que usam o mesmo logon, manter as credenciais usando usuários independentes em vários banco de dados pode invalidar os benefícios de usuários independentes. Por exemplo, a política de rotação de senha requer que alterações ocorram consistentemente em vários bancos de dados em vez de alterar a senha do logon apenas uma vez no banco de dados mestre. Por esse motivo, se você tiver vários bancos de dados que usam o mesmo nome de usuário e senha, a utilização de usuários independentes não será recomendada.

## Como configurar logons e usuários

Se estiver usando logons e usuários (em vez de usuários independentes), será necessário realizar etapas extras para garantir que os mesmos logons existam no banco de dados mestre. As seções a seguir descrevem as etapas envolvidas e considerações adicionais.

### Configurar o acesso do usuário a um banco de dados secundário ou recuperado

Para o banco de dados secundário poder ser usado como banco de dados secundário somente leitura e para garantir o acesso apropriado ao novo banco de dados primário ou o banco de dados recuperado usando a restauração geográfica, o banco de dados mestre do servidor de destino deve ter a configuração de segurança apropriadas em vigor antes da recuperação.

As permissões específicas para cada etapa são descritas posteriormente neste tópico.

A preparação do acesso do usuário a uma replicação geográfica secundária deve ser realizada como parte da configuração da Replicação Geográfica. A preparação do acesso do usuário aos bancos de dados restaurados geograficamente pode ser realizada a qualquer momento em que o servidor original estiver online (ex.: como parte do teste de DR).

>[AZURE.NOTE] Se o failover ou a restauração geográfica for para um servidor que não tem acesso de logons configurado corretamente, eles ficarão limitados à conta de administrador do servidor.

Configurar logons no servidor de destino envolve três etapas descritas abaixo:


#### 1\. Determine os logons com acesso ao banco de dados primário:
A primeira etapa do processo é determinar quais logons devem ser duplicados no servidor de destino. Isso é feito com um par de instruções SELECT, uma no banco de dados mestre lógico no servidor de origem e outra no próprio banco de dados primário.

Somente o administrador de servidores ou um membro da função de servidor **LoginManager** pode determinar os logons no servidor de origem com a instrução SELECT a seguir.

	SELECT [name], [sid] 
	FROM [sys].[sql_logins] 
	WHERE [type_desc] = 'SQL_Login'

Somente um membro da função de banco de dados db\_owner, o usuário dbo ou o administrador de servidores pode determinar todas as entidades de usuário do banco de dados no banco de dados primário.

	SELECT [name], [sid]
	FROM [sys].[database_principals]
	WHERE [type_desc] = 'SQL_USER'

#### 2\. Localize o SID dos logons identificados na etapa 1:
Ao comparar a saída das consultas da seção anterior e fazer a correspondência dos SIDs, é possível mapear o logon do servidor para o usuário do banco de dados. Logons que têm um usuário de banco de dados com um SID correspondente têm acesso de usuário a esse banco de dados como essa entidade de usuário de banco de dados.

A consulta a seguir pode ser usada para ver todas as entidades de usuário e seus SIDs em um banco de dados. Somente um membro da função de banco de dados db\_owner ou o administrador de servidores pode executar essa consulta.

	SELECT [name], [sid]
	FROM [sys].[database_principals]
	WHERE [type_desc] = 'SQL_USER'

>[AZURE.NOTE] Os usuários **INFORMATION\_SCHEMA** e **sys** têm SIDs *NULL* e o SID **Convidado** é **0x00**. O SID **dbo** pode começar com *0x01060000000001648000000000048454* se o criador do banco de dados foi o administrador do servidor, em vez de um membro do **DbManager**.

#### 3\. Crie os logons no servidor de destino:
A última etapa é acessar o servidor de destino, ou servidores, e gerar os logons com os SIDs apropriados. A sintaxe básica é mostrada a seguir.

	CREATE LOGIN [<login name>]
	WITH PASSWORD = <login password>,
	SID = <desired login SID>

>[AZURE.NOTE] Se desejar conceder ao usuário o acesso ao secundário, mas não ao primário, você poderá fazer isso alterando o logon do usuário no servidor primário usando a sintaxe a seguir.
>
>ALTER LOGIN <login name> DISABLE
>
>DISABLE não altera a senha, portanto você sempre poderá habilitá-la se necessário.

## Próximas etapas

- Para obter mais informações sobre como gerenciar logons e acesso ao banco, consulte [Segurança do Banco de Dados SQL: gerenciar a segurança de acesso e de logon do banco de dados](sql-database-manage-logins.md).
- Para saber mais sobre usuários de bancos de dados independentes, consulte [Usuários do bancos de dados independentes - Tornando seu banco de dados portátil](https://msdn.microsoft.com/library/ff929188.aspx).

## Recursos adicionais

- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md)
- [Criando aplicativos para recuperação de desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Finalizar seu Banco de Dados SQL do Azure recuperado](sql-database-recovered-finalize.md)
- [Perguntas frequentes sobre BCDR no Banco de Dados SQL](sql-database-bcdr-faq.md)

<!---HONumber=AcomDC_0608_2016-->