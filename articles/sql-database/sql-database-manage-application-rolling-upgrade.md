---
title: Atualizações de aplicativo sem interrupção - Banco de dados SQL do Azure | Microsoft Docs
description: Saiba como usar a replicação geográfica do Banco de Dados SQL do Azure para dar suporte a atualizações online de seu aplicativo na nuvem.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 01/29/2019
ms.openlocfilehash: 50f6f114a4d90f48218f751e1649e8694e664491
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55295739"
---
# <a name="managing-rolling-upgrades-of-cloud-applications-using-sql-database-active-geo-replication"></a>Gerenciando a rolagem de atualizações de aplicativos na nuvem usando a replicação geográfica ativa do Banco de dados SQL

Saiba como usar a [replicação geográfica ativa](sql-database-auto-failover-group.md) no Banco de Dados SQL para habilitar a rolagem de atualizações de seu aplicativo na nuvem. Como a atualização é uma operação com interrupção, ele deve fazer parte de seu design e planejamento de continuidade dos negócios. Neste artigo, examinamos dois métodos diferentes de orquestrar o processo de atualização e discutiremos os benefícios e as desvantagens de cada opção. Para os fins deste artigo, usaremos um aplicativo que consiste em um site conectado a um banco de dados individual como sua camada de dados. Nosso objetivo é atualizar a versão 1 do aplicativo para a versão 2, sem impactos significativos na experiência do usuário final.

Ao avaliar as opções de atualização, você deve considerar os seguintes fatores:

* Impacto na disponibilidade do aplicativo durante atualizações. Por quanto tempo a função do aplicativo pode ser limitada ou degradada.
* Capacidade de reversão se a atualização falhar.
* Vulnerabilidade do aplicativo se ocorrer uma falha catastrófica não relacionada durante a atualização.
* Custo total em dólares.  Isso inclui redundância adicional e custos incrementais dos componentes temporários usados pelo processo de atualização.

## <a name="upgrading-applications-that-rely-on-database-backups-for-disaster-recovery"></a>Atualizando aplicativos que dependem de backups de banco de dados para recuperação de desastre

Se o aplicativo depende de backups automáticos de banco de dados e usa a restauração geográfica para recuperação de desastre, ele é implantado em uma única região do Azure. Para minimizar a interrupção ao usuário final, você criará um ambiente de preparo nessa região com todos os componentes de aplicativo envolvidos na atualização. O diagrama a seguir ilustra o ambiente operacional antes do processo de atualização. O ponto de extremidade `contoso.azurewebsites.net` representa um slot de produção do aplicativo Web. Para habilitar a capacidade de reverter a atualização, você precisará criar um slot de preparo com uma cópia totalmente sincronizada do banco de dados. As etapas a seguir criarão um ambiente de preparo para a atualização:

1. Crie um banco de dados secundário na mesma região do Azure. Monitore o secundário para ver se o processo de propagação é concluído (1).
2. Crie um novo slot de implantação para seu aplicativo Web chamado de 'Preparo'. Ele será registrado no DNS com a URL `contoso-staging.azurewebsites.net` (2).

> [!NOTE]
> Observe que as etapas de preparação não terão impacto sobre o slot de produção e poderão funcionar no modo de acesso completo.
>  

![Configuração da Replicação Geográfica do Banco de Dados SQL. Recuperação de desastre em nuvem.](media/sql-database-manage-application-rolling-upgrade/option1-1.png)

Depois de concluir as etapas de preparação, o aplicativo estará pronto para a atualização real. O diagrama a seguir ilustra as etapas envolvidas no processo de atualização.

1. Defina o banco de dados primário para o modo somente leitura (3). Esse modo garantirá que o slot de produção do aplicativo Web (V1) permaneça somente leitura durante a atualização, evitando divergência de dados entre as instâncias de banco de dados V1 e V2.  
2. Desconecte o banco de dados secundário usando o modo de encerramento planejado (4). Ele criará uma cópia independente totalmente sincronizada do banco de dados primário. Este banco de dados será atualizado.
3. Modifique o banco de dados primário para o modo de leitura/gravação e execute o script de atualização (5).

![Configuração da replicação geográfica do banco de dados SQL. Recuperação de desastre em nuvem.](media/sql-database-manage-application-rolling-upgrade/option1-2.png)

Se a atualização tiver sido concluída com êxito, você estará pronto para alternar os usuários finais para a cópia de atualização do aplicativo. Ele agora se tornará um slot de produção.  A comutação envolve mais algumas etapas conforme ilustrado no diagrama a seguir.

1. Ative uma operação de troca entre os slots de preparo e de produção do aplicativo Web (6). Ele alternará as URLs dos dois slots. Agora `contoso.azurewebsites.net` apontará para a versão V2 do site e do banco de dados (ambiente de produção).  
2. Se você não precisa mais da versão V1, que se tornou uma cópia de preparo após a troca, pode encerrar o ambiente de preparo (7).

![Configuração da replicação geográfica do banco de dados SQL. Recuperação de desastre em nuvem.](media/sql-database-manage-application-rolling-upgrade/option1-3.png)

Se o processo de atualização não for bem-sucedido, por exemplo, devido a um erro no script de atualização, o slot de preparo deverá ser considerado como comprometido. Para reverter o aplicativo para o estado de pré-atualização, reverta o aplicativo no slot de produção para acesso completo. As etapas envolvidas são mostradas no diagrama seguinte.

1. Defina a cópia do banco de dados para o modo de leitura/gravação (8). Isso vai restaurar a funcionalidade completa da V1 da cópia de produção.
2. Execute a análise da causa raiz e encerre o ambiente de preparo (9).

Neste ponto, o aplicativo é totalmente funcional e as etapas de atualização podem ser repetidas.

> [!NOTE]
> A reversão não requer alterações de DNS, pois você ainda não realizou uma operação de troca.

![Configuração da replicação geográfica do banco de dados SQL. Recuperação de desastre em nuvem.](media/sql-database-manage-application-rolling-upgrade/option1-4.png)

A principal **vantagem** dessa opção é que você pode atualizar um aplicativo em uma única região usando um conjunto de etapas simples. O custo da atualização é relativamente baixo. A principal **vantagem** é que, se ocorrer uma falha catastrófica durante a atualização, a recuperação para o estado de pré-atualização envolverá reimplantação do aplicativo em uma região diferente e a restauração do banco de dados de backup usando a restauração geográfica. Esse processo resultará em um tempo de inatividade significativo.

## <a name="upgrading-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Atualizando aplicativos que dependem de replicação geográfica de banco de dados para recuperação de desastre

Se o aplicativo usar a replicação geográfica ativa ou grupos de Failover para continuidade dos negócios, ele será implantado em pelo menos duas regiões diferentes com um banco de dados primário ativo na região Primária e um banco de dados secundário somente leitura na região de Backup. Além dos fatores mencionados anteriormente, o processo de atualização deve garantir que:

* O aplicativo permaneça protegido contra falhas catastróficas em todos os momentos durante o processo de atualização
* Os componentes com redundância geográfica do aplicativo sejam atualizados em paralelo com os componentes ativos

Para atingir essas metas, além de usar os slots de implantação do Aplicativo Web, você aproveitará o ATM (Gerenciador de Tráfego do Azure) usando um perfil de failover com um ponto de extremidade ativo e um de backup.  O diagrama a seguir ilustra o ambiente operacional antes do processo de atualização. Os sites `contoso-1.azurewebsites.net` e `contoso-dr.azurewebsites.net` representam um ambiente de produção do aplicativo com redundância geográfica completa. O ambiente de produção inclui os seguintes componentes:

1. Slot de produção do aplicativo Web `contoso-1.azurewebsites.net` na região primária (1)
2. Banco de dados primário na região primária (2) 
3. Uma instância de espera do aplicativo Web na região de backup (3)
4. Banco de dados secundário replicado geograficamente na região de backup (4)
5. Perfil de desempenho do gerenciador de tráfego do Azure com o ponto de extremidade online `contoso-1.azurewebsites.net` e o ponto de extremidade offline `contoso-dr.azurewebsites.net`

Para habilitar a capacidade de reverter a atualização, você precisará criar um ambiente de preparo com uma cópia totalmente sincronizada do aplicativo. Como você precisa garantir que o aplicativo pode se recuperar rapidamente no caso de uma falha catastrófica durante o processo de atualização, o ambiente de preparo também deve ter redundância geográfica. As etapas a seguir são necessárias para criar um ambiente de preparo para a atualização:

1. Implantar um slot de preparo do aplicativo Web na região primária (6)
2. Crie um banco de dados secundário na região primária do Azure (7). Configure o slot de preparo do aplicativo Web para conectar-se a ele. 
3. Crie outro banco de dados secundário com redundância geográfica na região de backup replicando o banco de dados secundário na região primária (isso é chamado de "replicação geográfica encadeada") (8).
3. Implantar um slot de preparo da instância do aplicativo Web na região de backup (9) e configurá-lo para conectar-se ao secundário geográfico criado na etapa (9).


> [!NOTE]
> Observe as etapas de preparação não terão impacto sobre o aplicativo no slot de produção e permanecerão totalmente funcionais no modo de leitura/gravação.

![Configuração da replicação geográfica do banco de dados SQL. Recuperação de desastre em nuvem.](media/sql-database-manage-application-rolling-upgrade/option2-1.png)

Depois de concluir as etapas de preparação, o ambiente de preparo estará pronto para a atualização. O diagrama a seguir ilustra as etapas de atualização.

1. Defina o banco de dados primário no slot de produção para o modo somente leitura (10). Esse modo vai garantir que o banco de dados de produção (V1) não seja alterado durante a atualização, evitando divergência de dados entre as instâncias de banco de dados V1 e V2.  
2. Desconecte o banco de dados secundário na mesma região usando o modo de encerramento planejado (11). Ele criará uma cópia independente totalmente sincronizada do banco de dados de produção. Este banco de dados será atualizado.
3. Execute o script de atualização com relação a `contoso-1-staging.azurewebsites.net`, `contoso-dr-staging.azurewebsites.net` e o banco de dados primário de preparo (12). As alterações ao banco de dados serão replicadas automaticamente para o secundário de preparo 

![Configuração da replicação geográfica do banco de dados SQL. Recuperação de desastre em nuvem.](media/sql-database-manage-application-rolling-upgrade/option2-2.png)

Se a atualização foi concluída com êxito, você agora está pronto para alternar os usuários finais para a versão V2 do aplicativo. O diagrama a seguir ilustra as etapas envolvidas.

1. Ative uma operação de troca entre os slots de preparo do aplicativo Web na região primária (13) e na região de backup (14) e de produção. A V2 do aplicativo agora se torna um slot de produção com uma cópia redundante na região de backup.
2. Se você não precisar mais do aplicativo V1 (15 e 16), poderá encerrar o ambiente de preparo.  

![Configuração da replicação geográfica do banco de dados SQL. Recuperação de desastre em nuvem.](media/sql-database-manage-application-rolling-upgrade/option2-3.png)

Se o processo de atualização não for bem-sucedido, por exemplo devido a um erro no script de atualização, o ambiente de preparo será considerado em estado inconsistente. Para reverter o aplicativo para o estado pré-atualização, reverta usando a V1 do aplicativo no ambiente de produção. As etapas necessárias são mostradas no diagrama a seguir.

1. Defina a cópia do banco de dados primário no slot de produção para o modo de leitura/gravação (17). Isso vai restaurar a funcionalidade completa do V1 no slot de produção.
2. Execute a análise da causa raiz e conserte ou remova o ambiente de preparo (18 e 19).

Neste ponto, o aplicativo está totalmente funcional e as etapas de atualização podem ser repetidas.

> [!NOTE]
> A reversão não requer alterações de DNS, pois você não realizou uma operação de troca.

![Configuração da replicação geográfica do banco de dados SQL. Recuperação de desastre em nuvem.](media/sql-database-manage-application-rolling-upgrade/option2-4.png)

A principal **vantagem** dessa opção é que você pode atualizar o aplicativo e sua cópia com redundância geográfica em paralelo sem comprometer a continuidade dos negócios durante a atualização. A principal **compensação** é que ela exige redundância dupla de cada componente do aplicativo e, portanto, incorre custos mais altos. Ele também envolve um fluxo de trabalho mais complicado.

## <a name="summary"></a>Resumo

Os dois métodos de atualização descritos no artigo diferem em complexidade e custo, mas ambos focam na redução do tempo quando o usuário final está limitado às operações de somente leitura. Esse tempo é definido diretamente pela duração do script de atualização. Ele não depende do tamanho do banco de dados, da camada de serviço escolhida, da configuração de site da Web e de outros fatores que você não pode controlar facilmente. Todas as etapas de preparação são desacopladas das etapas de atualização e não afetam o aplicativo de produção. A eficiência do script de atualização é o principal fator que determina a experiência do usuário final durante as atualizações. Portanto, a melhor maneira aprimorá-la é concentrando seus esforços em tornar o script de atualização o mais eficiente possível.  

## <a name="next-steps"></a>Próximas etapas

* Para obter uma visão geral e os cenários de continuidade dos negócios, consulte [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).
* Para saber mais sobre a replicação geográfica ativa para Banco de Dados SQL do Azure, confira [Criar bancos de dados secundários legíveis usando replicação geográfica ativa](sql-database-active-geo-replication.md).
* Para saber mais sobre grupos de Failover de Banco de Dados SQL do Azure, confira [Usar grupos de failover automático para habilitar o failover transparente e coordenado de vários bancos de dados](sql-database-auto-failover-group.md).
* Para saber mais sobre os slots de implantação e o ambiente de preparo no Serviço de Aplicativo do Azure, confira [Configurar ambientes de preparo no Serviço de Aplicativo do Azure](../app-service/deploy-staging-slots.md).  
* Para conhecer os perfis do Gerenciador de Tráfego do Azure, confira [Gerenciar um perfil do Gerenciador de Tráfego do Azure](../traffic-manager/traffic-manager-manage-profiles.md).  


