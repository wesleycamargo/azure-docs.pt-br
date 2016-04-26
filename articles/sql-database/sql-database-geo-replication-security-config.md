<properties
	pageTitle="Configuração de segurança para a Replicação Geográfica Ativa ou Padrão"
	description="Este tópico explica as considerações sobre segurança para gerenciar cenários de Replicação Geográfica Ativa ou Padrão para o Banco de Dados SQL."
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
	ms.date="02/01/2016"
	ms.author="carlrab" />

# Configuração de segurança para a Replicação Geográfica Ativa ou Padrão

## Visão geral
Este tópico descreve os requisitos de autenticação para configurar e controlar a [Replicação Geográfica Ativa e Padrão](sql-database-geo-replication-overview.md) e as etapas necessárias para configurar o acesso de usuário ao banco de dados secundário. Para obter mais informações sobre como usar a Replicação Geográfica, veja [Recuperar um Banco de Dados SQL do Azure de uma interrupção](sql-database-disaster-recovery.md).

## Usando usuários independentes
Com a [versão V12 do Banco de Dados SQL do Azure](sql-database-v12-whats-new.md), o Banco de Dados SQL agora dá suporte a usuários independentes. Ao contrário de usuários tradicionais, que devem ser mapeados para logons no banco de dados mestre, um usuário independente é totalmente gerenciado pelo próprio banco de dados. Isso oferece dois benefícios. No cenário de replicação geográfica, os usuários podem continuar a se conectar ao banco de dados secundário sem qualquer configuração adicional, porque o banco de dados gerencia os usuários. Também há possíveis benefícios de desempenho e escalabilidade com esta configuração de uma perspectiva de logon. Para obter mais informações, veja [Usuários de bancos de dados independentes - Tornando seu banco de dados portátil](https://msdn.microsoft.com/library/ff929188.aspx).

Com usuários independentes, se você tiver vários bancos de dados que usam o mesmo logon, você deve gerenciar esse usuário separadamente para cada banco de dados (por exemplo, para alterar a senha), em vez de gerenciar o logon no nível do servidor.

>[AZURE.NOTE] Se desejar alterar o acesso de leitura do primário e secundário de maneira independente, é necessário usar logons e usuários tradicionais. Os usuários independentes não podem ser gerenciados no secundário de modo independente do primário.

## Usando logons e usuários tradicionais
Se estiver usando logons e usuários tradicionais (em vez de usuários independentes), é necessário realizar etapas extras para assegurar que os mesmos logons existam no servidor de banco de dados secundário. As seções a seguir descrevem as etapas envolvidas e considerações adicionais.

### Configurar o acesso do usuário para o secundário online
Para que o banco de dados secundário possa ser usado como um banco de dados somente leitura (secundário online) ou uma cópia do banco de dados viável em uma situação de failover, o banco de dados secundário deve ter a configuração de segurança apropriada em vigor.

Somente o administrador de servidores pode concluir com êxito todas as etapas descritas posteriormente no tópico. As permissões específicas para cada etapa são descritas posteriormente neste tópico.

A preparação do acesso do usuário ao secundário online da Replicação Geográfica Ativa pode ser executada a qualquer momento. Isso envolve as três etapas descritas abaixo:

1. Determine os logons com acesso ao banco de dados primário.
2. Localize o SID desses logons no servidor de origem.
3. Gere os logons no servidor de destino com o SID correspondente do servidor de origem.

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

#### 3\. Gere os logons no servidor de destino:
A última etapa é acessar o servidor de destino, ou servidores, e gerar os logons com os SIDs apropriados. A sintaxe básica é mostrada a seguir.

	CREATE LOGIN [<login name>]
	WITH PASSWORD = <login password>,
	SID = <desired login SID>

>[AZURE.NOTE] Se desejar conceder ao usuário o acesso ao secundário, mas não ao primário, você poderá fazer isso alterando o logon do usuário no servidor primário usando a sintaxe a seguir.
>
>ALTER LOGIN <login name> DISABLE
>
>DISABLE não altera a senha, portanto você sempre poderá habilitá-la se necessário.

## Configurar o acesso do usuário após o encerramento de um relacionamento de cópia contínuo
No caso de failover, o relacionamento de cópia contínuo deve ser interrompido entre o primário e os secundários. Para obter informações sobre este processo, veja [Recuperar um Banco de Dados SQL do Azure de uma interrupção](sql-database-disaster-recovery.md).

No caso de Replicação Geográfica Standard, o usuário não pode acessar o secundário offline, portanto as alterações às contas do usuário devem ser feitas após o encerramento do relacionamento de cópia contínuo.

Se os SIDs de logon não forem duplicados no servidor de destino, o acesso ao banco de dados secundário após o encerramento será limitado apenas ao administrador do servidor. Se o usuário que iniciar a replicação for um DbManager, ele não terá acesso ao secundário, a menos que seu SID de logon seja duplicado do servidor de origem. Isso permanece válido enquanto durar o processo de replicação.

Quando a replicação for encerrada, como parte do processo de encerramento, a entidade de usuário [dbo] será alterada para corresponder ao SID de logon para o usuário que iniciou a replicação e esse usuário terá o acesso restaurado. Este não é o caso para outros usuários do banco de dados.

A conta de usuário e o logon associado que foi usado para iniciar a operação de encerramento devem estar presentes no servidor de destino e no banco de dados para se certificar de que a conta de usuário pode acessar o secundário após a conclusão do encerramento.

Para obter mais informações sobre as etapas necessárias após o failover, veja [Finalizar seu Banco de Dados SQL do Azure recuperado](sql-database-recovered-finalize.md).

## Próximas etapas
Para obter mais informações sobre a Replicação Geográfica e recursos adicionais de continuidade dos negócios do Banco de Dados SQL, confira [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).

<!---HONumber=AcomDC_0413_2016-->