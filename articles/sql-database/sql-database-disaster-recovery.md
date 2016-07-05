<properties 
   pageTitle="Recuperação de desastre do Banco de Dados SQL | Microsoft Azure" 
   description="Saiba como recuperar um banco de dados de uma interrupção do datacenter regional ou de uma falha com os recursos de Restauração geográfica e Replicação geográfica Ativa do Banco de Dados SQL do Azure." 
   services="sql-database" 
   documentationCenter="" 
   authors="carlrabeler" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="06/16/2016"
   ms.author="carlrab"/>

# Restaurar um Banco de Dados SQL ou fazer failover para um secundário

O Banco de Dados SQL do Azure oferece os seguintes recursos para a recuperação de uma paralisação:

- [Replicação geográfica ativa](sql-database-geo-replication-overview.md)
- [Restauração geográfica](sql-database-geo-restore.md)

Para saber mais sobre a preparação para desastres e quando recuperar o banco de dados, visite nossa página [Design para continuidade dos negócios](sql-database-business-continuity-design.md).

## Quando iniciar a recuperação

A operação de recuperação afeta o aplicativo. Ele exige a alteração da cadeia de conexão SQL e pode resultar em perda permanente de dados. Portanto, deve ser feita somente quando houver a probabilidade de a interrupção durar mais do que o RTO do aplicativo. Quando o aplicativo for implantado na produção você deverá executar o monitoramento regular da integridade do aplicativo e usar os seguintes pontos de dados para ter certeza de a recuperação é garantida:

1.	Falha de conectividade permanente da camada do aplicativo com o banco de dados.
2.	O Portal do Azure mostra um alerta sobre um incidente na região com grande impacto.
3.	O servidor do Banco de Dados SQL do Azure está marcado como degradado. 

Dependendo da tolerância a tempo de inatividade de seu aplicativo e possível responsabilidade comercial, você pode considerar as opções de recuperação a seguir.

Use [Obter Banco de Dados Recuperável](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*) para obter o ponto de restauração de replicação geográfica mais recente.

## Aguarde a recuperação de serviço

As equipes do Azure trabalham cuidadosamente para restaurar a disponibilidade do serviço o mais rapidamente possível, mas dependendo da causa raiz, isso pode levar horas ou dias. Se seu aplicativo pode tolerar tempo de inatividade significativo, você pode simplesmente esperar a conclusão da recuperação. Nesse caso, nenhuma ação sua é necessária. Você pode ver o status atual do serviço no nosso [Painel de Integridade do Serviço Azure](https://azure.microsoft.com/status/). Após a recuperação da região, a disponibilidade do aplicativo será restaurada.

## Failover para banco de dados secundário replicado geograficamente

Se o tempo de inatividade do aplicativo puder resultar em responsabilidade comercial, você deverá usar bancos de dados replicados geograficamente em seu aplicativo. Isso permitirá que o aplicativo restaure rapidamente a disponibilidade em uma região diferente em caso de uma interrupção. Saiba como [configurar a Replicação Geográfica](sql-database-geo-replication-portal.md).

Para restaurar a disponibilidade do(s) banco(s) de dados, você precisa iniciar o failover para o secundário replicado geograficamente usando um dos métodos com suporte.


Use um dos guias a seguir para fazer failover para um banco de dados de secundário replicado geograficamente:

- [Fazer failover para um secundário replicado geograficamente usando o Portal do Azure](sql-database-geo-replication-portal.md)
- [Fazer failover para um secundário replicado geograficamente usando o PowerShell](sql-database-geo-replication-powershell.md)
- [Fazer failover para um secundário replicado geograficamente usando o T-SQL](sql-database-geo-replication-transact-sql.md) 



## Recuperação usando a restauração geográfica

Se o tempo de inatividade do aplicativo não resultar em responsabilidade de negócios, você poderá usar a restauração geográfica como método para recuperar os bancos de dados do aplicativo. Ela cria uma cópia do banco de dados com base em seu último backup com redundância geográfica.

Use um dos seguintes guias para realizar a restauração geográfica de um banco de dados para uma nova região:

- [Restauração geográfica de um banco de dados para uma nova região usando o Portal do Azure](sql-database-geo-restore-portal.md)
- [Restauração geográfica de um banco de dados para uma nova região usando o PowerShell](sql-database-geo-restore-powershell.md) 


## Configurar o banco de dados após a recuperação

Se estiver usando failover de replicação geográfica ou a restauração geográfica para se recuperar de uma interrupção, você deverá se certificar de que a conectividade com os novos bancos de dados esteja configurada corretamente para que o funcionamento normal do aplicativo possa ser retomado. Esta é uma lista de verificação de tarefas para preparar para produção o seu banco de dados recuperado.

### Atualizar cadeias de conexão

Já que o banco de dados recuperado residirá em um servidor diferente, você precisa atualizar a cadeia de conexão do seu aplicativo para apontar para esse servidor.

Para saber mais sobre como alterar as cadeias de conexão, confira a linguagem de desenvolvimento apropriada para sua [biblioteca de conexão](sql-database-libraries.md).

### Configurar regras de firewall

Você precisa certificar-se de que as regras de firewall configuradas no servidor e no banco de dados correspondam àquelas que foram configuradas no servidor primário e no banco de dados primário. Para saber mais, consulte [Como definir configurações de firewall (Banco de Dados SQL do Azure)](sql-database-configure-firewall-settings.md).


### Configurar logons e usuários do banco de dados

Você deve verificar se todos os logons usados pelo aplicativo existem no servidor que está hospedando o banco de dados recuperado. Para saber mais, confira [Configuração de segurança para Replicação Geográfica](sql-database-geo-replication-security-config.md).

>[AZURE.NOTE] Você deve configurar e testar suas regras de firewall de servidor e logons (e suas permissões) durante uma análise de recuperação de desastre. Esses objetos no nível de servidor e sua configuração podem não estar disponíveis durante a interrupção. Para saber mais, confira [Executar análise de recuperação de desastre](sql-database-disaster-recovery-drills.md).

### Configurar alertas de telemetria

Você precisa certificar-se de que as configurações de regra de alerta existentes sejam atualizadas para mapear para o banco de dados recuperado e para o outro servidor.

Para obter mais informações sobre regras de alerta de banco de dados, consulte [Receber notificações de alerta](../azure-portal/insights-receive-alert-notifications.md) e [Acompanhar a integridade do serviço](../azure-portal/insights-service-health.md).

### Habilitar a auditoria

Se a auditoria for necessária para acessar o banco de dados, você precisará habilitar a auditoria após a recuperação do banco de dados. Um bom indicador de que a auditoria é obrigatória é o uso, pelos aplicativos cliente, de cadeias de conexão seguras em um padrão *.database.secure.windows.net. Para saber mais, confira [Introdução à Auditoria do Banco de Dados SQL](sql-database-auditing-get-started.md).


## Próximas etapas

- Para saber mais sobre como usar e configurar a Replicação Geográfica Ativa para recuperação de desastre, confira [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md)
- Para saber mais sobre como usar a Restauração Geográfica para recuperação de desastre, confira [Restauração Geográfica](sql-database-geo-restore.md)

## Recursos adicionais

- [Recuperação de desastre e continuidade de negócios do Banco de Dados SQL](sql-database-business-continuity.md)
- [Restauração pontual](sql-database-point-in-time-restore.md)
- [Restauração geográfica](sql-database-geo-restore.md)
- [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md)
- [Criando aplicativos para recuperação de desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Finalizar seu Banco de Dados SQL do Azure recuperado](sql-database-recovered-finalize.md)
- [Configuração de segurança para a Replicação Geográfica](sql-database-geo-replication-security-config.md)
- [Perguntas frequentes sobre BCDR no Banco de Dados SQL](sql-database-bcdr-faq.md)

<!---HONumber=AcomDC_0622_2016-->