---
title: Continuidade dos negócios em nuvem - recuperação de banco de dados - Banco de Dados SQL | Microsoft Docs
description: Saiba como o Banco de Dados SQL do Azure dá suporte para a continuidade dos negócios em nuvem e para a recuperação de banco de dados, além de ajudar a manter os aplicativos em nuvem críticos em execução.
keywords: continuidade dos negócios, continuidade dos negócios em nuvem, recuperação de desastre do banco de dados, recuperação de banco de dados
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/20/2016
ms.author: carlrab

---
# Visão geral da continuidade dos negócios com o Banco de Dados SQL do Azure
Esta visão geral descreve os recursos que o Banco de Dados SQL do Azure fornece para a continuidade dos negócios e a recuperação de desastre. Ele fornece opções, recomendações e tutoriais para recuperação de eventos com interrupção que poderiam causar a perda dos dados ou fazer com que o banco de dados e o aplicativo se tornassem indisponíveis. A discussão inclui o que fazer quando um erro de usuário ou de aplicativo afeta a integridade dos dados, quando uma região do Azure tem uma interrupção ou quando seu aplicativo necessita de manutenção.

## Recursos do Banco de Dados SQL que você pode usar para proporcionar a continuidade dos negócios
O Banco de Dados SQL fornece vários recursos de continuidade dos negócios, incluindo backups automatizados e replicação opcional do banco de dados. Cada um deles tem características diferentes para o ERT (tempo de recuperação estimado) e para a perda potencial de dados em transações recentes. Depois de compreender essas opções, você poderá escolher entre elas e, na maioria dos cenários, usá-las simultaneamente em cenários diferentes. À medida que desenvolve seu plano de continuidade de negócios, você precisará compreender o tempo máximo aceitável antes que o aplicativo se recupere totalmente após um evento de interrupção - este é o RTO (objetivo do tempo de recuperação). Você também precisará compreender a quantidade máxima de atualizações de dados recentes (intervalo de tempo) que o aplicativo poderá tolerar perder durante a recuperação após um evento de interrupção - o RPO (objetivo de ponto de recuperação).

A tabela a seguir compara o ERT e RPO para os três cenários mais comuns.

| Recurso | Camada básica | Camada padrão | Camada premium |
| --- | --- | --- | --- |
| Recuperação Pontual do backup |Qualquer ponto de restauração dentro de 7 dias |Qualquer ponto de restauração dentro de 35 dias |Qualquer ponto de restauração dentro de 35 dias |
| Restauração geográfica de backups replicados geograficamente |ERT < 12h, RPO < 1h |ERT < 12h, RPO < 1h |ERT < 12h, RPO < 1h |
| Replicação geográfica ativa |ERT < 30s, RPO < 5s |ERT < 30s, RPO < 5s |ERT < 30s, RPO < 5s |

### Usar backups de banco de dados para recuperar um banco de dados
O Banco de Dados SQL executa automaticamente uma combinação de backups de banco de dados semanais, backups de bancos de dados diferenciais por hora e backups de logs de transação a cada cinco minutos para proteger sua empresa contra a perda de dados. Esses backups são armazenados no armazenamento com redundância local por 35 dias, no caso dos bancos de dados das camadas de serviço Standard e Premium, e por sete dias no caso dos bancos de dados da camada de serviço Básica - confira a seção [Camadas de serviço](sql-database-service-tiers.md) para obter mais detalhes sobre elas. Se o período de retenção para a camada de serviço não atender seus requisitos de negócios, você poderá aumentar o período de retenção ao [alterar a camada de serviço](sql-database-scale-up.md). Os backups de banco de dados completos e diferenciais também são replicados para um [data center emparelhado](../best-practices-availability-paired-regions.md) para proteção contra uma interrupção do data center - confira [Backups automáticos do banco de dados](sql-database-automated-backups.md) para obter mais detalhes.

Você pode usar esses backups automáticos do banco de dados para recuperar um banco de dados de diversos eventos de interrupção, tanto em seu data center quanto em outro. Ao usar os backups automáticos de banco de dados, o tempo estimado de recuperação dependerá de vários fatores, incluindo o número total de bancos de dados de recuperação na mesma região e ao mesmo tempo, o tamanho do banco de dados, o tamanho do log de transações e a largura de banda da rede. Na maioria dos casos, o tempo de recuperação é menor que 12 horas. Ao recuperar em outra região de dados, a perda de dados potencial será limitada a uma hora pelo armazenamento com redundância geográfica dos backups de banco de dados diferenciais por hora.

> [!IMPORTANT]
> Para recuperar usando os backups automáticos, você deverá ser um membro da função Colaborador do SQL Server ou o proprietário da assinatura, consulte [RBAC: funções internas](../active-directory/role-based-access-built-in-roles.md). Você pode recuperar usando o Portal do Azure, o PowerShell ou a API REST. Você não pode usar o Transact-SQL.
> 
> 

Use os backups automatizados como o mecanismo de continuidade e recuperação dos negócios se seu aplicativo:

* Não for considerado crítico.
* Não tiver um SLA associado, portanto, o tempo de inatividade de 24 horas ou mais não resultará em responsabilidade financeira.
* Tiver uma taxa baixa de alteração de dados (poucas transações por hora), uma vez que perder até uma hora de alterações é uma perda de dados aceitável.
* Seja suscetível aos custos.

Se você precisar de uma recuperação mais rápida, use a [replicação geográfica ativa](sql-database-geo-replication-overview.md) (discutida a seguir). Se você precisa ter a capacidade de recuperar dados de um período maior que 35 dias, considere o arquivamento do banco de dados regularmente para um arquivo BACPAC (um arquivo compactado que contém o esquema do banco de dados e os dados associados) armazenado no Armazenamento de Blobs do Azure ou em outra localização de sua escolha. Para obter mais informações sobre como criar um arquivo de banco de dados consistente de forma transacional, confira [Criar uma cópia do banco de dados](sql-database-copy.md) e [Exportar a cópia do banco de dados](sql-database-export.md).

### Use a replicação geográfica ativa para reduzir o tempo de recuperação e limitar a perda de dados associada a uma recuperação
Além de usar os backups de banco de dados para a recuperação de banco de dados no caso de uma interrupção de negócios, você poderá usar a [replicação geográfica ativa](sql-database-geo-replication-overview.md) para configurar um banco de dados para ter até quatro bancos de dados secundários legíveis em regiões de sua escolha. Esses bancos de dados secundários são mantidos sincronizados com o banco de dados primário usando um mecanismo de replicação assíncrona. Esse recurso é usado para proteger contra interrupções de negócios, no caso de uma interrupção do data center, ou durante uma atualização de aplicativo. A replicação geográfica ativa pode ser usada para fornecer melhor desempenho em consultas do tipo somente leitura para usuários geograficamente dispersos.

Se o banco de dados primário ficar offline inesperadamente ou se precisar colocá-lo offline para atividades de manutenção, você poderá promover rapidamente um secundário para se tornar o primário (também chamado de failover) e configurar os aplicativos para se conectarem ao primário recém-promovido. Com um failover planejado, não há nenhuma perda de dados. Com um failover não planejado, há uma pequena perda de dados em transações muito recentes devido à natureza da replicação assíncrona. Depois de um failover, você poderá fazer o failback posteriormente, ou de acordo com um plano ou quando o data center voltar a ficar online. Em todos os casos, os usuários enfrentam um breve tempo de inatividade e precisarão ser reconectados.

> [!IMPORTANT]
> Para usar a replicação geográfica ativa, você deverá ser o proprietário da assinatura ou ter permissões administrativas no SQL Server. Você pode configurar e fazer failover usando o Portal do Azure, o PowerShell ou a API REST utilizando permissões na assinatura, ou utilizando o Transact-SQL com permissões no SQL Server.
> 
> 

Use a replicação geográfica ativa caso seu aplicativo atenda a qualquer um desses critérios:

* Seja crítico.
* Tenha um SLA (Contrato de Nível de Serviço) que não permita um tempo de inatividade de 24 horas ou superior.
* Que o tempo de inatividade resulte em responsabilidade financeira.
* Tenha uma alta taxa de alteração de dados e que a perda de uma hora de dados não seja aceitável.
* Que o custo adicional da replicação geográfica ativa seja menor que a responsabilidade financeira potencial e das perdas associadas do negócio.

## Recuperar um banco de dados após um erro de usuário ou de aplicativo
*Ninguém é perfeito! Um usuário pode acidentalmente excluir alguns dados, remover uma tabela importante inadvertidamente ou até mesmo um banco de dados inteiro. Ou, um aplicativo pode acidentalmente substituir dados corretos por incorretos por causa de um defeito.

Neste cenário, estas são as opções de recuperação.

### Executar uma recuperação pontual
Você pode usar os backups automatizados para recuperar uma cópia do banco de dados para um momento adequado e conhecido, desde que este momento esteja dentro do período de retenção do banco de dados. Depois que o banco de dados for restaurado, você poderá substituir o banco de dados original pelo banco de dados restaurado ou copiar os dados necessários dos dados restaurados para o banco de dados original. Se o banco de dados usar a replicação geográfica ativa, será recomendável copiar os dados necessários da cópia restaurada para o banco de dados original. Se substituir o banco de dados original pelo banco de dados restaurado, você precisará reconfigurar e ressincronizar a replicação geográfica ativa (o que poderá levar algum tempo em um banco de dados grande).

Para obter mais informações e as etapas detalhadas para restaurar um banco de dados para determinado momento usando o Portal do Azure ou o PowerShell, confira [Recuperação pontual](sql-database-recovery-using-backups.md#point-in-time-restore). Você não pode recuperar usando o Transact-SQL.

### Restaurar um banco de dados excluído
Se o banco de dados tiver sido excluído, mas o servidor lógico não, você poderá restaurar o banco de dados excluído para o momento da sua exclusão. Isso restaura um backup do banco de dados para o mesmo SQL Server lógico do qual ele foi excluído. Você pode restaurá-lo usando o nome original, fornecendo um novo nome ou o banco de dados restaurado.

Para obter mais informações e as etapas detalhadas para restaurar um banco de dados excluído usando o Portal do Azure ou o PowerShell, confira [Restaurar um banco de dados excluído](sql-database-recovery-using-backups.md#deleted-database-restore). Você não pode restaurar usando o Transact-SQL.

> [!IMPORTANT]
> Se o servidor lógico for excluído, você não poderá recuperar um banco de dados excluído.
> 
> 

### Importar de um arquivo de banco de dados
Se a perda de dados tiver ocorrido fora do período de retenção atual para backups automáticos e você tiver arquivado o banco de dados, você poderá [Importar um arquivo BACPAC](sql-database-import.md) para um novo banco de dados. Neste momento, você poderá substituir o banco de dados original pelo banco de dados importado ou copiar os dados necessários dos dados importados para o banco de dados original.

## Recuperar um banco de dados para outra região de uma interrupção no centro de dados regionais do Azure
<!-- Explain this scenario -->

Embora seja raro, um data center do Azure pode ter uma interrupção. Quando uma interrupção ocorre, ela causa uma parada nos negócios, que pode durar alguns minutos ou horas.

* Uma opção é esperar que seu banco de dados volte a ficar online quando a interrupção do data center terminar. Isso funciona para aplicativos que podem manter o banco de dados offline. Por exemplo, um projeto de desenvolvimento ou uma avaliação gratuita não precisam funcionar constantemente. Quando um data center tiver uma interrupção, você não saberá quanto tempo ela durará. Portanto, essa opção só funcionará se o banco de dados não for necessário por um tempo.
* Outra opção será fazer failover em outra região de dados, se você estiver usando a replicação geográfica ativa, ou recuperar usando os backups de banco de dados com redundância geográfica (restauração geográfica). O failover demora apenas alguns segundos, enquanto a recuperação de backups demora horas.

Ao executar uma ação, o tempo que levará para você recuperar e a quantidade de dados que serão perdidos no caso de uma interrupção do data center dependerão de como você decide usar os recursos de continuidade dos negócios discutidos acima em seu aplicativo. Na verdade, você poderá escolher por usar uma combinação de backups de banco de dados e a replicação geográfica ativa dependendo dos requisitos do seus aplicativo. Para uma discussão sobre as considerações de design do aplicativo para bancos de dados independentes em pools elásticos usando esses recursos de continuidade dos negócios, confira [Criar um aplicativo para recuperação de desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md) e [Estratégias de recuperação de desastre para Pool Elástico](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

As seções a seguir fornecem uma visão geral das etapas para recuperar usando os backups de banco de dados ou a replicação geográfica ativa. Para obter as etapas detalhadas, incluindo os requisitos de planejamento, as etapas pós-recuperação e as informações sobre como simular uma interrupção para executar uma análise de recuperação de desastre, confira [Recover a SQL Database from an outage (Recuperar um Banco de Dados SQL de uma interrupção)](sql-database-disaster-recovery.md).

### Prepare-se para uma interrupção
Independentemente do recurso de continuidade de negócios usados, você deve:

* Identificar e preparar o servidor de destino, incluindo as regras de firewall no nível do servidor, logons e permissões de nível de banco de dados mestre.
* Determinar como redirecionar os clientes e aplicativos de cliente para o novo servidor
* Documentar outras dependências, como as configurações e alertas de auditoria

Se você não se planejar e se preparar corretamente, colocar seus aplicativos online após um failover ou uma recuperação exigirá um tempo adicional e, provavelmente, a solução de problemas também será exigida em um momento de estresse, ou seja, uma combinação ruim.

### Fazer failover em um banco de dados secundário com replicação geográfica
Se você estiver usando a replicação geográfica ativa como o mecanismo de recuperação, [force um failover em um secundário com replicação geográfica](sql-database-disaster-recovery.md#failover-to-geo-replicated-secondary-database). Em segundos, o secundário será promovido para se tornar o novo primário e estará pronto para registrar novas transações e responder a todas as consultas - com apenas alguns segundos de perda de dados para os dados que ainda não foram replicados. Para obter informações sobre como automatizar o processo de failover, confira [Criar um aplicativo para recuperação de desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> Quando o data center voltar a ficar online, você poderá fazer failback para o primário original (ou não).
> 
> 

### Executar uma restauração geográfica
Se você estiver usando backups automatizados com a replicação de armazenamento com redundância geográfica como o mecanismo de recuperação, [inicie uma recuperação de banco de dados usando a restauração geográfica](sql-database-disaster-recovery.md#recover-using-geo-restore). A recuperação normalmente ocorre em 12 horas, com perda de dados de até uma hora determinada pela ocasião em que o último backup diferencial ocorreu e foi replicado. Até que a recuperação seja concluída, o banco de dados não poderá registrar nenhuma transação ou responder a qualquer consulta.

> [!NOTE]
> Se o data center voltar a ficar online antes de você transferir seu aplicativo para o banco de dados recuperado, você poderá simplesmente cancelar a recuperação.
> 
> 

### Executar pós-failover / tarefas de recuperação
Após recuperar de um dos mecanismos de recuperação, você deverá executar as seguintes tarefas adicionais antes que os usuários e aplicativos entrem em funcionamento novamente:

* Redirecionar clientes e aplicativos de cliente para o novo servidor e banco de dados restaurado
* Verificar se as regras de firewall de nível de servidor apropriadas estão em vigor para que os usuários se conectem (ou use os [firewalls de nível de banco de dados](sql-database-firewall-configure.md#creating-database-level-firewall-rules))
* Verificar se os logons apropriados e as permissões nível de banco de dados mestre estão em vigor (ou usar os [usuários independentes](https://msdn.microsoft.com/library/ff929188.aspx))
* Configurar a auditoria, conforme apropriado
* Configurar os alertas, conforme apropriado

## Atualize um aplicativo com tempo de inatividade mínimo
Às vezes, um aplicativo precisa ser colocado offline devido à manutenção planejada, como uma atualização do aplicativo. A seção [Gerenciar atualizações de aplicativos](sql-database-manage-application-rolling-upgrade.md) descreve como usar a replicação geográfica ativa para habilitar as atualizações sem interrupção do seu aplicativo em nuvem para minimizar o tempo de inatividade durante as atualizações e fornecer um caminho de recuperação no caso de algo sair errado. Este artigo abrange dois métodos diferentes para orquestrar o processo de atualização, bem como os benefícios e as compensações de cada opção.

## Próximas etapas
Para uma discussão sobre as considerações de design de aplicativo para bancos de dados independentes em pools elásticos, confira [Criar um aplicativo para recuperação de desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md) e [Estratégias de recuperação de desastre para Pool Elástico](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

<!---HONumber=AcomDC_0824_2016-->