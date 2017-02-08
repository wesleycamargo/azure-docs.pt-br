---
title: "Design de aplicativo de recuperação de desastres - Banco de Dados SQL Azure | Microsoft Docs"
description: "Saiba mais sobre o design de aplicativo para recuperação de desastre usando replicação geográfica no Banco de Dados SQL."
keywords: "recuperação de desastre em nuvem, soluções de recuperação de desastre, backup de dados de aplicativo, replicação geográfica, planejamento de continuidade de negócios"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: e8a346ac-dd08-41e7-9685-46cebca04582
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 07/20/2016
ms.author: sashan
translationtype: Human Translation
ms.sourcegitcommit: 7e607debe47efb6a22ca6fa47a40554d13d29359
ms.openlocfilehash: dd56a8d1ee428b1845ed80f0b899cc73c2c4b7f6


---
# <a name="application-design-for-cloud-disaster-recovery-using-active-geo-replication-in-sql-database"></a>Design de aplicativo para recuperação de desastre na nuvem usando a Replicação Geográfica Ativa no Banco de Dados SQL
> [!NOTE]
> A [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md) agora está disponível para todos os bancos de dados em todas as camadas.
>
>

Saiba como usar a [replicação geográfica ativa](sql-database-geo-replication-overview.md) no Banco de Dados SQL para criar aplicativos de banco de dados resilientes a falhas regionais e interrupções catastróficas. Para o planejamento de continuidade de negócios, leve em consideração a topologia de implantação do aplicativo, o contrato de nível de serviço que você está buscando, a latência de tráfego e os custos. Neste artigo, examinamos os padrões comuns de aplicativos e abordamos as vantagens e as desvantagens de cada opção. Para obter informações sobre o uso da replicação geográfica ativa com Pools Elásticos, confira [Elastic Pool disaster recovery strategies](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)(Estratégias de recuperação de desastre do pool elástico).

## <a name="design-pattern-1-active-passive-deployment-for-cloud-disaster-recovery-with-a-co-located-database"></a>Padrão de design 1: Implantação ativa-passiva para recuperação de desastre em nuvem com banco de dados colocalizado
Essa opção é mais adequada para aplicativos com as seguintes características:

* Instância ativa em uma única região do Azure
* Alta dependência do acesso de RW (leitura/gravação) aos dados
* A conectividade entre regiões entre a lógica do aplicativo e o banco de dados não é aceitável devido ao custo de tráfego e à latência    

Nesse caso, a topologia de implantação do aplicativo é otimizada para lidar com desastres regionais quando todos os componentes do aplicativo são afetados e precisam fazer failover como uma unidade. Para redundância geográfica, a lógica do aplicativo e o banco de dados são replicados para outra região, mas não são usados para a carga de trabalho de aplicativo sob as condições normais. O aplicativo na região secundária deve ser configurado para usar uma cadeia de conexão SQL para o banco de dados secundário. O gerenciador de tráfego é configurado para usar o [método de roteamento de failover](../traffic-manager/traffic-manager-configure-failover-routing-method.md).  

> [!NOTE]
> O [gerenciador de tráfego do ](../traffic-manager/traffic-manager-overview.md) é usado em todo este artigo apenas para fins ilustrativos. Você pode usar qualquer solução de balanceamento de carga que dê suporte ao método de roteamento de failover.    
>
>

Além das instâncias do aplicativo principal, você deve considerar a implantação de um pequeno [aplicativo de função de trabalho](../cloud-services/cloud-services-choose-me.md#tellmecs) que monitora o banco de dados primário ao emitir comandos RO (somente leitura) periódicos do T-SQL. Você pode usá-lo para disparar automaticamente o failover, para gerar um alerta no console de administração do aplicativo ou ambos. Para garantir que a monitoração não seja afetada por paralisações em toda a região, você deve implantar as instâncias do aplicativo de monitoramento para cada região e conectar cada instância ao banco de dados primário na região primária e ao banco de dados secundário na região local.

> [!NOTE]
> Ambos os aplicativos de monitoramento devem estar ativos e investigar bancos de dados primários e secundários. Essa última opção pode ser usada para detectar uma falha na região secundária e alertar quando o aplicativo não estiver protegido.     
>
>

O diagrama a seguir mostra essa configuração antes de uma interrupção.

![Configuração da replicação geográfica do banco de dados SQL. Recuperação de desastre em nuvem.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-1.png)

Após uma interrupção na região primária, o aplicativo de monitoramento detecta que o banco de dados primário não está acessível e registra um alerta. Dependendo do SLA do aplicativo, você pode decidir quantas sondagens de monitoramento consecutivas devem falhar antes de ser declarada uma interrupção do banco de dados. Para obter um failover coordenado do ponto de extremidade do aplicativo e do banco de dados, o aplicativo de monitoramento deve executar as seguintes etapas:

1. [Atualizar o status de ponto de extremidade primário](https://msdn.microsoft.com/library/hh758250.aspx) para disparar o failover do ponto de extremidade.
2. Chamar o banco de dados secundário para [iniciar o failover de banco de dados](sql-database-geo-replication-portal.md).

Após o failover, o aplicativo processará as solicitações do usuário na região secundária, mas permanecerá colocalizado com o banco de dados porque o banco de dados primário agora estará na região secundária. Esse cenário é ilustrado pelo diagrama a seguir. Em todos os diagramas, linhas sólidas indicam conexões ativas, linhas pontilhadas indicam conexões suspensas e pontos indicam gatilhos de ação.

![Replicação geográfica: failover para banco de dados secundário. Backup de dados do aplicativo.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-2.png)

Se ocorrer uma interrupção na região secundária, o link de replicação entre os bancos de dados primário e secundário será suspenso e o aplicativo de monitoramento registrará um alerta de que o banco de dados primário está exposto. O desempenho do aplicativo não é afetado, mas ele funcionará exposto e, assim, correrá um risco mais elevado caso ambas as regiões falhem sucessivamente.

> [!NOTE]
> Só recomendamos configurações de implantação com uma única região de DR. Isso ocorre porque a maioria das regiões geográficas do Azure tem duas regiões. Essas configurações não protegerão seu aplicativo contra uma falha catastrófica de ambas as regiões. Caso ocorra essa falha improvável, você poderá recuperar seus bancos de dados em uma terceira região usando a [operação de restauração geográfica](sql-database-disaster-recovery.md#recover-using-geo-restore).
>
>

Depois que a interrupção for atenuada, o banco de dados secundário será sincronizado automaticamente com o primário. Durante a sincronização, o desempenho do primário poderá ser ligeiramente afetado, dependendo da quantidade de dados que precisam ser sincronizados. O diagrama a seguir ilustra uma interrupção na região secundária.

![Banco de dados secundário sincronizado com o primário. Recuperação de desastre em nuvem.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-3.png)

As principais **vantagens** desse padrão de design são:

* A cadeia de conexão SQL é definida durante a implantação do aplicativo em cada região e não muda depois do failover.
* O desempenho do aplicativo não é afetado pelo failover, pois o aplicativo e o banco de dados sempre estão colocalizados.

A principal **desvantagem** é que a instância do aplicativo redundante na região secundária é usada apenas para recuperação de desastres.

## <a name="design-pattern-2-active-active-deployment-for-application-load-balancing"></a>Padrão de design 2: implantação ativa-ativa para balanceamento de carga de aplicativo
Essa opção de recuperação de desastre em nuvem é mais adequada para aplicativos com as seguintes características:

* Alta taxa de leituras de banco de dados em relação às gravações
* A latência de gravação do banco de dados não afeta a experiência do usuário final  
* A lógica somente leitura pode ser separada da lógica de leitura/gravação usando uma cadeia de conexão diferente
* A lógica somente leitura não requer que os dados sejam totalmente sincronizados com as últimas atualizações  

Se seus aplicativos tiverem essas características, o balanceamento de carga de conexões de usuário final em várias instâncias do aplicativo em regiões diferentes poderá melhorar o desempenho e a experiência do usuário final. Para implementar o balanceamento de carga, cada região deve ter uma instância ativa do aplicativo com a lógica de RW (leitura/gravação) conectada ao banco de dados primário na região primária. A lógica RO (somente leitura) deve ser conectada a um banco de dados secundário na mesma região que a instância do aplicativo. O gerenciador de tráfego deve ser configurado para usar o [roteamento round robin](../traffic-manager/traffic-manager-configure-round-robin-routing-method.md) ou o [roteamento de desempenho](../traffic-manager/traffic-manager-configure-performance-routing-method.md) com o [monitoramento de ponto de extremidade](../traffic-manager/traffic-manager-monitoring.md) habilitado para cada instância do aplicativo.

Como no padrão 1, considere a implantação de um aplicativo de monitoramento semelhante. Porém, diferentemente do padrão n. 1, o aplicativo de monitoramento não será responsável por disparar o failover de ponto de extremidade.

> [!NOTE]
> Embora esse padrão use mais de um banco de dados secundário, apenas um dos secundários seria usado para failover, pelos motivos observados anteriormente. Como esse padrão requer acesso somente leitura ao secundário, ele requer a replicação geográfica ativa.
>
>

O gerenciador de tráfego deve ser configurado para roteamento de desempenho para direcionar as conexões de usuário à instância do aplicativo mais próxima da localização geográfica do usuário. O diagrama a seguir ilustra essa configuração antes de uma interrupção.
![Nenhuma interrupção: roteamento de desempenho para o aplicativo mais próximo. Replicação geográfica.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-1.png)

Se for detectada uma falha de banco de dados na região primária, você iniciará o failover do banco de dados primário para uma das regiões secundárias, que alterará a localização do banco de dados primário. O gerenciador de tráfego exclui automaticamente o ponto de extremidade offline da tabela de roteamento, mas continua a rotear o tráfego de usuário final para as instâncias online restantes. Como o banco de dados primário está em uma região diferente, todas as instâncias online devem alterar sua cadeia de conexão SQL de leitura/gravação para se conectar ao novo primário. É importante que você faça essa alteração antes de iniciar o failover de banco de dados. As cadeias de conexão SQL somente leitura devem permanecer inalteradas, pois sempre apontam para o banco de dados na mesma região. As etapas de failover são:  

1. Alterar as cadeias de conexão SQL de leitura/gravação para apontar para o novo primário.
2. Chamar o banco de dados secundário designado para [iniciar o failover de banco de dados](sql-database-geo-replication-portal.md).

O diagrama a seguir ilustra a nova configuração após o failover.
![Configuração após o failover. Recuperação de desastre em nuvem.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-2.png)

No caso de uma interrupção em uma das regiões secundários, o gerenciador de tráfego remove automaticamente da tabela de roteamento o ponto de extremidade offline nessa região. O canal de replicação para o banco de dados secundário nessa região é suspenso. Como as demais regiões recebem tráfego de usuário adicional, o desempenho do aplicativo é afetado durante a interrupção. Depois que a interrupção é atenuada, o banco de dados secundário na região afetada é imediatamente sincronizado com o primário. Durante a sincronização, o desempenho do primário poderá ser ligeiramente afetado, dependendo da quantidade de dados que precisam ser sincronizados. O diagrama a seguir ilustra uma interrupção em uma das regiões secundárias.

![Interrupção na região secundária. Recuperação de desastre em nuvem - replicação geográfica.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-3.png)

A principal **vantagem** desse padrão de design é que você pode dimensionar a carga de trabalho do aplicativo em vários secundários para obter o desempenho ideal para o usuário final. As **desvantagens** dessa opção são:

* As conexões de leitura e gravação entre as instâncias do aplicativo e o banco de dados têm latência e custos variáveis
* O desempenho do aplicativo é afetado durante a interrupção
* As instâncias do aplicativo precisam alterar dinamicamente a cadeia de conexão SQL após o failover de banco de dados.  

> [!NOTE]
> Uma abordagem semelhante pode ser usada para descarregar cargas de trabalho especializadas, como trabalhos de geração de relatórios, ferramentas de business intelligence ou backups. Normalmente, essas cargas de trabalho consomem recursos significativos do banco de dados. Portanto, é recomendável que você designe um dos bancos de dados secundários para elas com o nível de desempenho correspondente à carga de trabalho antecipada.
>
>

## <a name="design-pattern-3-active-passive-deployment-for-data-preservation"></a>Padrão de design 3: implantação ativa-passiva para preservação dos dados
Essa opção é mais adequada para aplicativos com as seguintes características:

* Qualquer perda de dados é um risco comercial elevado. O failover de banco de dados só poderá ser usado como último recurso, se a interrupção for permanente.
* O aplicativo pode operar em "modo somente leitura" por um período de tempo.

Nesse padrão, o aplicativo alterna para o modo somente leitura quando conectado ao banco de dados secundário. A lógica do aplicativo na região primária está localizada no mesmo banco de dados primário e opera no modo RW (leitura e gravação). A lógica do aplicativo na região secundária está localizada no mesmo banco de dados secundário e está pronta para operar no modo RO (somente leitura).  O gerenciador de tráfego deve ser configurado para usar o [roteamento de failover](../traffic-manager/traffic-manager-configure-failover-routing-method.md) com o [monitoramento do ponto de extremidade](../traffic-manager/traffic-manager-monitoring.md) habilitado para ambas as instâncias do aplicativo.

O diagrama a seguir ilustra essa configuração antes de uma interrupção.
![Implantação ativo-passivo antes do failover. Recuperação de desastre em nuvem.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-1.png)

Quando o gerenciador de tráfego detecta uma falha de conectividade para a região primária, ele alterna automaticamente o tráfego do usuário para a instância do aplicativo na região secundária. Com esse padrão, é importante que você **não** inicie o failover de banco de dados após a interrupção ser detectada. O aplicativo na região secundária é ativado e opera em modo somente leitura usando o banco de dados secundário. Isso é ilustrado pelo diagrama a seguir.

![Interrupção: aplicativo em modo somente leitura. Recuperação de desastre em nuvem.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-2.png)

Depois que a interrupção na região primária é atenuada, o gerenciador de tráfego detecta a restauração da conectividade na região primária e alterna o tráfego do usuário de volta para a instância do aplicativo na região primária. Essa instância de aplicativo será retomada e operará no modo de leitura/gravação usando o banco de dados primário.

> [!NOTE]
> Como esse padrão requer acesso somente leitura ao secundário, ele requer a replicação geográfica ativa.
>
>

No caso de uma interrupção na região secundária, o gerenciador de tráfego marca o ponto de extremidade do aplicativo na região primária como degradado e o canal de replicação é suspenso. No entanto, isso não afeta o desempenho do aplicativo durante a interrupção. Depois que a interrupção é atenuada, o banco de dados secundário é imediatamente sincronizado com o primário. Durante a sincronização, o desempenho do primário poderá ser ligeiramente afetado, dependendo da quantidade de dados que precisam ser sincronizados.

![Interrupção: banco de dados secundário. Recuperação de desastre em nuvem.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-3.png)

Esse padrão de design apresenta várias **vantagens**:

* Ele evita a perda de dados durante as interrupções temporárias.
* Ele não exige que você implante um aplicativo de monitoramento, pois a recuperação é disparada pelo gerenciador de tráfego.
* O tempo de inatividade depende somente da velocidade com a qual o gerenciador de tráfego detecta a falha de conectividade, o que pode ser configurado.

As **desvantagens** são:

* O aplicativo deve ser capaz de operar no modo somente leitura.
* É necessário alternar dinamicamente para ele quando ele é conectado a um banco de dados somente leitura.
* A retomada das operações de leitura/gravação exige a recuperação da região primária, o que significa que o acesso a dados completo pode ser desabilitado por horas ou até mesmo dias.

> [!NOTE]
> No caso de uma interrupção de serviço permanente na região, você precisa ativar manualmente o failover de banco de dados e aceitar a perda de dados. O aplicativo será funcional na região secundária com acesso de leitura e gravação ao banco de dados.
>
>

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Planejamento de continuidade de negócios: escolher um design de aplicativo para recuperação de desastre em nuvem
A estratégia específica de recuperação de desastre em nuvem pode combinar ou estender esses padrões de design para atender da melhor forma às necessidades do aplicativo.  Como mencionado anteriormente, a estratégia escolhida é baseada no SLA que você deseja oferecer a seus clientes e na topologia de implantação do aplicativo. Para ajudar a orientar sua decisão, a tabela a seguir compara as opções com base na perda de dados estimada ou no RPO (objetivo de ponto de recuperação) e no ERT (tempo de recuperação estimado).

| Padrão | RPO | ERT |
|:--- |:--- |:--- |
| Implantação ativa-passiva para recuperação de desastres com acesso de banco de dados colocalizado |Acesso de leitura/gravação < 5 s |Tempo de detecção de falha + chamada à API de failover + teste de verificação de aplicativo |
| Implantação ativa-ativa para balanceamento de carga de aplicativo |Acesso de leitura/gravação < 5 s |Tempo de detecção de falha + chamada à API de failover + alteração da cadeia de conexão SQL + teste de verificação de aplicativo |
| Implantação ativa-passiva para preservação de dados |Acesso somente leitura < 5 s Acesso de leitura/gravação = zero |Acesso somente leitura = tempo de detecção de falha de conectividade + teste de verificação de aplicativo  <br>Acesso de leitura/gravação = tempo para atenuar a interrupção |

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, consulte [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md)
* Para obter uma visão geral e os cenários de continuidade dos negócios, confira [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
* Para saber mais sobre como usar backups automatizados de recuperação, veja [Restaurar um banco de dados de backups iniciados pelo serviço](sql-database-recovery-using-backups.md)
* Para saber mais sobre opções de recuperação mais rápidas, confira [Replicação geográfica ativa](sql-database-geo-replication-overview.md)  
* Para saber mais sobre como usar backups automatizados para arquivamento, confira [cópia de banco de dados](sql-database-copy.md)
* Para obter informações sobre o uso da replicação geográfica ativa com Pools Elásticos, confira [Elastic Pool disaster recovery strategies](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)(Estratégias de recuperação de desastre do pool elástico).



<!--HONumber=Jan17_HO4-->


