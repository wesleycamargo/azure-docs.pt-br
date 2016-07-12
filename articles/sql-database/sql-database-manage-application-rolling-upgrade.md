<properties
   pageTitle="Soluções de recuperação de desastre na nuvem - Replicação geográfica ativa do Banco de Dados SQL | Microsoft Azure"
   description="Saiba como usar a replicação geográfica do Banco de Dados SQL do Azure para dar suporte a atualizações online de seu aplicativo na nuvem."
   services="sql-database"
   documentationCenter=""
   authors="anosov1960"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-bcdr"
   ms.date="06/16/2016"
   ms.author="sashan"/>

# Gerenciando a rolagem de atualizações de aplicativos na nuvem usando a Replicação Geográfica Ativa do Banco de Dados SQL


> [AZURE.NOTE] [Active Geo-Replication](sql-database-geo-replication-overview.md) agora está disponível para todos os bancos de dados em todas as camadas.


Saiba como usar a [Replicação Geográfica](sql-database-geo-replication-overview.md) no Banco de Dados SQL para habilitar a rolagem de atualizações de seu aplicativo na nuvem. Como a atualização é uma operação com interrupção, ele deve fazer parte de seu design e planejamento de continuidade dos negócios. Neste artigo, examinamos dois métodos diferentes de orquestrar o processo de atualização e discutiremos os benefícios e as desvantagens de cada opção. Para os fins deste artigo, usaremos um aplicativo simples que consiste em um site conectado a um banco de dados individual como sua camada de dados. Nosso objetivo é atualizar a versão 1 do aplicativo para a versão 2, sem impactos significativos na experiência do usuário final.

Ao avaliar as opções de atualização, você deve considerar os seguintes fatores:

+ Impacto na disponibilidade do aplicativo durante atualizações. Por quanto tempo a função do aplicativo pode ser limitada ou degradada.
+ Capacidade de reversão em caso de falha durante a atualização.
+ Vulnerabilidade do aplicativo se ocorrer uma falha catastrófica não relacionada durante a atualização.
+ Custo total em dólares. Isso inclui redundância adicional e custos incrementais dos componentes temporários usados pelo processo de atualização.

## Atualizando aplicativos que dependem de backups de banco de dados para recuperação de desastre 

Se seu aplicativo depende de backups automáticos de banco de dados e usa a restauração geográfica para recuperação de desastre, ele normalmente é implantado em uma única região do Azure. Nesse caso, o processo de atualização envolve a criação de uma implantação de backup de todos os componentes do aplicativo de backup envolvidos na atualização. Para minimizar a interrupção do usuário final, você utilizará o WATM (Gerenciador de Tráfego do Azure) com o perfil de failover. O diagrama a seguir ilustra o ambiente operacional antes do processo de atualização. O ponto de extremidade <i>contoso-1.azurewebsites.net</i> representa um slot de produção do aplicativo que precisa ser atualizado. Para habilitar a capacidade de reverter a atualização, você precisará criar um slot de preparo com uma cópia totalmente sincronizada do aplicativo. As etapas a seguir são necessárias para preparar o aplicativo para a atualização:

1.  Crie um slot de preparo para a atualização. Para fazer isso, crie um banco de dados secundário (1) e implante um site idêntico na mesma região do Azure. Monitore o secundário para ver se o processo de propagação é concluído.
3.  Crie um perfil de failover no WATM com <i>contoso-1.azurewebsites.net</i> como ponto de extremidade online e <i>contoso-2.azurewebsites.net</i> como offline.

> [AZURE.NOTE] Observe que as etapas de preparação não terão impacto sobre o aplicativo no slot de produção e poderão funcionar no modo de acesso completo.

![Configuração da Replicação Geográfica do Banco de Dados SQL. Recuperação de desastre em nuvem.](media/sql-database-manage-application-rolling-upgrade/Option1-1.png)

Depois de concluir as etapas de preparação, o aplicativo estará pronto para a atualização real. O diagrama a seguir ilustra as etapas envolvidas no processo de atualização.

1. Defina o banco de dados primário no slot de produção para o modo somente leitura (3). Isso garantirá que a instância de produção do aplicativo (V1) permanecerá somente leitura durante a atualização, evitando assim divergência de dados entre as instâncias de banco de dados V1 e V2.
2. Desconecte o banco de dados secundário usando o modo de encerramento planejado (4). Ele criará uma cópia independente totalmente sincronizada do banco de dados primário. Este banco de dados será atualizado.
3. Modifique o banco de dados primário para o modo de leitura/gravação e execute o script de atualização no slot de preparo (5).

![Configuração da replicação geográfica do banco de dados SQL. Recuperação de desastre em nuvem.](media/sql-database-manage-application-rolling-upgrade/Option1-2.png)

Se a atualização foi concluída com êxito, você agora está pronto para alternar os usuários finais para a cópia de preparo do aplicativo. Agora, ele se tornará o slot de produção do aplicativo. Isso envolve mais algumas etapas conforme ilustrado no diagrama a seguir.

1. Alterne o ponto de extremidade online no perfil do WATM para <i>contoso-2.azurewebsites.net</i>, que aponta para a versão V2 do site da Web (6). Agora, ele se torna o slot de produção com o aplicativo V2 e o tráfego de usuário final é direcionado a ele.
2. Se você não precisar mais dos componentes do aplicativo V1, poderá removê-los com segurança (7).

![Configuração da replicação geográfica do banco de dados SQL. Recuperação de desastre em nuvem.](media/sql-database-manage-application-rolling-upgrade/Option1-3.png)

Se o processo de atualização não for bem-sucedido, por exemplo, devido a um erro no script de atualização, o slot de preparo deverá ser considerado como comprometido. Para reverter o aplicativo para o estado de pré-atualização, você simplesmente deve reverter o aplicativo no slot de produção para acesso completo. As etapas envolvidas são mostradas no diagrama seguinte.

1. Defina a cópia do banco de dados para o modo de leitura/gravação (8). Isso vai restaurar a funcionalidade completa do V1 no slot de produção.
2. Execute a análise da causa raiz e remova os componentes comprometidos no slot de preparo (9).

Neste ponto, o aplicativo é totalmente funcional e as etapas de atualização podem ser repetidas.

> [AZURE.NOTE] A reversão não requer alterações no perfil do WATM, pois ele já aponta para <i>contoso-1.azurewebsites.net</i> como o ponto de extremidade ativo.

![Configuração da replicação geográfica do banco de dados SQL. Recuperação de desastre em nuvem.](media/sql-database-manage-application-rolling-upgrade/Option1-4.png)

A principal **vantagem** dessa opção é que você pode atualizar um aplicativo em uma única região usando um conjunto de etapas simples. O custo da atualização é relativamente baixo. A principal **compensação** é que, se ocorrer uma falha catastrófica durante a atualização, a recuperação para o estado de pré-atualização envolverá reimplantação do aplicativo em uma região diferente e a restauração do banco de dados de backup usando a restauração geográfica. Esse processo resultará em um tempo de inatividade significativo.

## Atualizando aplicativos que dependem de Replicação Geográfica de banco de dados para recuperação de desastre

Se seu aplicativo usar a Replicação Geográfica para continuidade dos negócios, ele será implantado em ao menos duas regiões diferentes com uma implantação ativa na região Primária e uma implantação em espera na região de Backup. Além dos fatores mencionados anteriormente, o processo de atualização deve garantir que:

+ O aplicativo permaneça protegido contra falhas catastróficas em todos os momentos durante o processo de atualização
+ Os componentes com redundância geográfica do aplicativo sejam atualizados em paralelo com os componentes ativos

Para atingir essas metas, você utilizará o WATM (Gerenciador de Tráfego do Azure) usando o perfil de failover com um ponto de extremidade ativo e três de backup. O diagrama a seguir ilustra o ambiente operacional antes do processo de atualização. Os sites da Web <i>contoso-1.azurewebsites.net</i> e <i>contoso-dr.azurewebsites.net</i> representam um slot de produção do aplicativo com redundância geográfica completa. Para habilitar a capacidade de reverter a atualização, você precisará criar um slot de preparo com uma cópia totalmente sincronizada do aplicativo. Como você precisa garantir que o aplicativo pode se recuperar rapidamente no caso de uma falha catastrófica durante o processo de atualização, o slot de preparo também deve ter redundância geográfica. As etapas a seguir são necessárias para preparar o aplicativo para a atualização:

1.  Crie um slot de preparo para a atualização. Para fazer isso, crie um banco de dados secundário (1) e implante uma cópia idêntica do site da Web na mesma região do Azure. Monitore o secundário para ver se o processo de propagação é concluído.
2.  Crie um banco de dados secundário com redundância geográfica no slot de preparo ao fazer replicação geográfica do banco de dados secundário na região de backup (isso é chamado de "replicação geográfica encadeada"). Monitore o secundário do backup para ver se o processo de propagação é concluído (3).
3.  Crie uma cópia em espera do site da Web na região de backup e vincule-a ao secundário com redundância geográfica (4).
4.  Adicione pontos de extremidade adicionais <i>contoso-2.azurewebsites.net</i> e <i>contoso-3.azurewebsites.net</i> ao perfil de failover no WATM como pontos de extremidade offline (5).

> [AZURE.NOTE] Observe que as etapas de preparação não terão impacto sobre o aplicativo no slot de produção e poderão funcionar no modo de acesso completo.

![Configuração da replicação geográfica do banco de dados SQL. Recuperação de desastre em nuvem.](media/sql-database-manage-application-rolling-upgrade/Option2-1.png)

Depois de concluir as etapas de preparação, o slot de preparo estará pronto para a atualização. O diagrama a seguir ilustra as etapas de atualização.

1. Defina o banco de dados primário no slot de produção para o modo somente leitura (6). Isso garantirá que a instância de produção do aplicativo (V1) permanecerá somente leitura durante a atualização, evitando assim divergência de dados entre as instâncias de banco de dados V1 e V2.
2. Desconecte o banco de dados secundário na mesma região usando o modo de encerramento planejado (7). Ele criará uma cópia independente totalmente sincronizada do banco de dados primário, que se tornará automaticamente um primário após a finalização. Este banco de dados será atualizado.
3. Modifique o banco de dados primário no slot de preparo para o modo de leitura/gravação e execute o script de atualização (8).

![Configuração da replicação geográfica do banco de dados SQL. Recuperação de desastre em nuvem.](media/sql-database-manage-application-rolling-upgrade/Option2-2.png)

Se a atualização foi concluída com êxito, você agora está pronto para alternar os usuários finais para a versão V2 do aplicativo. O diagrama a seguir ilustra as etapas envolvidas.

1. Alterne o ponto de extremidade ativo no perfil do WATM para <i>contoso-2.azurewebsites.net</i>, que aponta para a versão V2 do site da Web (9). Agora, ele se torna um slot de produção com o aplicativo V2 e o tráfego de usuário final é direcionado a ele.
2. Se você não precisar mais do aplicativo V1, poderá removê-lo com segurança (10 e 11).

![Configuração da replicação geográfica do banco de dados SQL. Recuperação de desastre em nuvem.](media/sql-database-manage-application-rolling-upgrade/Option2-3.png)

Se o processo de atualização não for bem-sucedido, por exemplo, devido a um erro no script de atualização, o slot de preparo deverá ser considerado como comprometido. Para reverter o aplicativo para o estado de pré-atualização, você simplesmente deve reverter o aplicativo no slot de produção com acesso completo. As etapas envolvidas são mostradas no diagrama seguinte.

1. Defina a cópia do banco de dados primário no slot de produção para o modo de leitura/gravação (12). Isso vai restaurar a funcionalidade completa do V1 no slot de produção.
2. Execute a análise da causa raiz e remova os componentes comprometidos no slot de preparo (13 e 14).

Neste ponto, o aplicativo é totalmente funcional e as etapas de atualização podem ser repetidas.

> [AZURE.NOTE] A reversão não requer alterações no perfil do WATM, pois ele já aponta para <i>contoso-1.azurewebsites.net</i> como o ponto de extremidade ativo.

![Configuração da replicação geográfica do banco de dados SQL. Recuperação de desastre em nuvem.](media/sql-database-manage-application-rolling-upgrade/Option2-4.png)

A principal **vantagem** dessa opção é que você pode atualizar o aplicativo e sua cópia com redundância geográfica em paralelo sem comprometer a continuidade dos negócios durante a atualização. A principal **compensação** é que ela exige redundância dupla de cada componente do aplicativo e, portanto, incorre custos mais altos. Ele também envolve um fluxo de trabalho mais complicado.

## Resumo

Os dois métodos de atualização descritos no artigo diferem em complexidade e custo, mas ambos focam na redução do tempo quando o usuário final está limitado às operações de somente leitura. Esse tempo é definido diretamente pela duração do script de atualização. Ele não depende do tamanho do banco de dados, da camada de serviço escolhida, da configuração de site da Web e de outros fatores que você não pode controlar facilmente. Isso ocorre porque todas as etapas de preparação são desacopladas das etapas de atualização e podem ser feitas sem afetar o aplicativo de produção. A eficiência do script de atualização é o principal fator que determina a experiência do usuário final durante as atualizações. Portanto, a melhor maneira aprimorá-la é concentrando seus esforços em tornar o script de atualização o mais eficiente possível.


## Próximas etapas

- Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, confira [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md)
- Para saber mais sobre cenários de design e recuperação de continuidade dos negócios, veja [Cenários de continuidade](sql-database-business-continuity-scenarios.md)
- Para saber mais sobre como usar backups automatizados de recuperação, veja [Restaurar um banco de dados de backups iniciados pelo serviço](sql-database-recovery-using-backups.md)
- Para saber mais sobre opções de recuperação mais rápidas, veja [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md)
- Para saber mais sobre como usar backups automatizados de arquivamento, veja [Cópia de banco de dados](sql-database-copy.md)

## Recursos adicionais

As páginas abaixo ajudarão você a saber mais sobre as operações específicas necessárias para implementar o fluxo de trabalho de atualização:

- [Adicionar banco de dados secundário](https://msdn.microsoft.com/library/azure/mt603689.aspx)
- [Fazer failover do banco de dados para o secundário](https://msdn.microsoft.com/library/azure/mt619393.aspx)
- [Desconectar o secundário da Replicação Geográfica](https://msdn.microsoft.com/library/azure/mt603457.aspx)
- [Banco de dados de restauração geográfica](https://msdn.microsoft.com/library/azure/mt693390.aspx)
- [Remover banco de dados](https://msdn.microsoft.com/library/azure/mt619368.aspx)
- [Copiar banco de dados](https://msdn.microsoft.com/library/azure/mt603644.aspx)
- [Definir o banco de dados para o modo somente leitura ou leitura/gravação](https://msdn.microsoft.com/library/bb522682.aspx)

<!---HONumber=AcomDC_0629_2016-->