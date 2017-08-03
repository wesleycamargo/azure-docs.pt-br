---
title: "Criar um serviço altamente disponível usando o Banco de Dados SQL do Azure | Microsoft Docs"
description: "Saiba mais sobre a criação de um aplicativo para serviços altamente disponíveis usando o Banco de Dados SQL do Azure."
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
ms.date: 04/21/2017
ms.author: sashan
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 40fe0ae04eb94322356ed19773512e3bc383639c
ms.contentlocale: pt-br
ms.lasthandoff: 05/18/2017


---
# <a name="designing-highly-available-services-using-azure-sql-database"></a>Criar serviços altamente disponíveis usando o Banco de Dados SQL do Azure

Ao criar e implantar serviços altamente disponíveis no Banco de Dados SQL do Azure, use [grupos de failover e replicação geográfica ativa](sql-database-geo-replication-overview.md) para criar resistência a falhas regionais e interrupções catastróficas e permitir a recuperação rápida de bancos de dados secundários. Este artigo concentra-se em padrões de aplicativos comuns e descreve as vantagens e desvantagens de cada opção dependendo dos requisitos de implantação do aplicativo, do contrato de nível de serviço que você deseja, da latência de tráfego e dos custos. Para obter informações sobre o uso da replicação geográfica ativa com Pools elásticos, confira [Estratégias de recuperação de desastres do pool elástico](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

## <a name="design-pattern-1-active-passive-deployment-for-cloud-disaster-recovery-with-a-co-located-database"></a>Padrão de design 1: Implantação ativa-passiva para recuperação de desastre em nuvem com banco de dados colocalizado
Essa opção é mais adequada para aplicativos com as seguintes características:

* Instância ativa em uma única região do Azure
* Alta dependência do acesso de RW (leitura/gravação) aos dados
* A conectividade entre regiões entre o aplicativo Web e o banco de dados não é aceitável devido ao custo de tráfego e à latência    

Nesse caso, a topologia de implantação do aplicativo é otimizada para lidar com desastres regionais quando todos os componentes do aplicativo são afetados e precisam fazer failover como uma unidade. Para redundância geográfica, a lógica do aplicativo e o banco de dados são replicados para outra região, mas não são usados para a carga de trabalho de aplicativo sob as condições normais. O aplicativo na região secundária deve ser configurado para usar uma cadeia de conexão SQL para o banco de dados secundário. O gerenciador de tráfego é configurado para usar o [método de roteamento de failover](../traffic-manager/traffic-manager-configure-failover-routing-method.md).  

> [!NOTE]
> O [gerenciador de tráfego do ](../traffic-manager/traffic-manager-overview.md) é usado em todo este artigo apenas para fins ilustrativos. Você pode usar qualquer solução de balanceamento de carga que dê suporte ao método de roteamento de failover.    
>

O diagrama a seguir mostra essa configuração antes de uma interrupção.

![Configuração da replicação geográfica do banco de dados SQL. Recuperação de desastre em nuvem.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-1.png)

Após uma interrupção na região primária, o serviço do Banco de dados SQL detecta que o banco de dados primário não está acessível e dispara um failover para o banco de dados secundário, com base nos parâmetros da política de failover automático. Dependendo do SLA do seu aplicativo, você pode optar por configurar um período de carência entre a detecção de interrupção e o failover em si. Configurar um período de carência reduz o risco de perda de dados para os casos em que a interrupção é catastrófica e a disponibilidade na região não pode ser restaurada rapidamente. Se o failover do ponto de extremidade for iniciado pelo Gerenciador de Tráfego antes de o grupo de failover disparar o failover do banco de dados, o aplicativo Web não poderá se reconectar ao banco de dados. A tentativa do aplicativo de reconectar-se automaticamente terá êxito assim que o failover de banco de dados for concluído. 

> [!NOTE]
> Para conseguir um failover totalmente coordenado do aplicativo e dos bancos de dados, você deve planejar seu próprio método de monitoramento e usar o failover manual dos pontos de extremidade do aplicativo Web e dos bancos de dados.
>

Após o failover dos pontos de extremidade do aplicativo e do banco de dados ser concluído, o aplicativo reiniciará o processamento das solicitações de usuário na região B e permanecerá co-localizado com o banco de dados porque o banco de dados primário está agora na região B. Esse cenário é ilustrado no diagrama a seguir. Em todos os diagramas, linhas sólidas indicam conexões ativas, linhas pontilhadas indicam conexões suspensas e pontos indicam gatilhos de ação.

![Replicação geográfica: failover para banco de dados secundário. Backup de dados do aplicativo.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-2.png)

Se ocorrer uma interrupção na região secundária, o link de replicação entre os bancos de dados primário e secundário é suspenso, mas o failover não é disparado pois banco de dados primário não foi afetado. A disponibilidade do aplicativo não é afetada neste caso, mas ele funcionará exposto e, portanto, correrá um risco mais elevado caso ambas as regiões falhem sucessivamente.

> [!NOTE]
> Para recuperação de desastres, recomendamos a configuração com implantação de aplicativo limitada a duas regiões. Isso ocorre porque a maioria das regiões geográficas do Azure tem somente duas regiões. Essa configuração não protegerá seu aplicativo contra uma falha catastrófica simultânea nas duas regiões.  Caso ocorra essa falha improvável, você poderá recuperar seus bancos de dados em uma terceira região usando a [operação de restauração geográfica](sql-database-disaster-recovery.md#recover-using-geo-restore).
>

Depois que a interrupção for atenuada, o banco de dados secundário será sincronizado automaticamente com o primário. Durante a sincronização, o desempenho do primário poderá ser ligeiramente afetado, dependendo da quantidade de dados que precisam ser sincronizados. O diagrama a seguir ilustra uma interrupção na região secundária.

![Banco de dados secundário sincronizado com o primário. Recuperação de desastre em nuvem.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-3.png)

As principais **vantagens** desse padrão de design são:

* O mesmo aplicativo Web é implantado em ambas as regiões sem qualquer configuração específica de região e nenhuma lógica adicional para reagir ao failover. 
* O desempenho do aplicativo não é afetado pelo failover, pois o aplicativo Web e o banco de dados sempre estão co-localizados.

A principal **desvantagem** é que a instância do aplicativo redundante na região secundária é usada apenas para recuperação de desastres.

## <a name="design-pattern-2-active-active-deployment-for-application-load-balancing"></a>Padrão de design 2: implantação ativa-ativa para balanceamento de carga de aplicativo
Essa opção de recuperação de desastre em nuvem é mais adequada para aplicativos com as seguintes características:

* Alta taxa de leituras de banco de dados em relação às gravações
* A latência de leitura do banco de dados é mais importante para a experiência do usuário final que a latência de gravação 
* A lógica somente leitura pode ser separada da lógica de leitura/gravação usando uma cadeia de conexão diferente
* A lógica somente leitura não requer que os dados sejam totalmente sincronizados com as últimas atualizações  

Se seus aplicativos tiverem essas características, o balanceamento de carga de conexões de usuário final em várias instâncias do aplicativo em regiões diferentes pode melhorar de forma significativa a experiência geral do usuário final. Duas das regiões devem ser selecionadas como o par de recuperação de desastres e o grupo de failover deve incluir os bancos de dados nessas regiões. Para implementar o balanceamento de carga, cada região deve ter uma instância ativa do aplicativo com a lógica de leitura-gravação (RW) conectada ao ponto de extremidade do ouvinte de leitura/gravação do grupo de failover. Isso garante que o failover será iniciado automaticamente se o banco de dados primário for afetado por uma interrupção. A lógica de somente leitura (RO) no aplicativo Web deve se conectar diretamente ao banco de dados nessa região. O gerenciador de tráfego deve ser configurado para usar o [roteamento de desempenho](../traffic-manager/traffic-manager-configure-performance-routing-method.md) com o [monitoramento de ponto de extremidade](../traffic-manager/traffic-manager-monitoring.md) habilitado para cada instância do aplicativo.

Como no padrão 1, considere a implantação de um aplicativo de monitoramento semelhante. Porém, diferentemente do padrão n. 1, o aplicativo de monitoramento não será responsável por disparar o failover de ponto de extremidade.

> [!NOTE]
> Embora esse padrão use mais de um banco de dados secundário, apenas o banco de dados secundário na Região B seria usado para o failover devendo fazer parte do grupo de failover.
>

O gerenciador de tráfego deve ser configurado para roteamento de desempenho para direcionar as conexões de usuário à instância do aplicativo mais próxima da localização geográfica do usuário. O diagrama a seguir ilustra essa configuração antes de uma interrupção.

![Nenhuma interrupção: roteamento de desempenho para o aplicativo mais próximo. Replicação geográfica.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-1.png)

Se for detectada uma interrupção no banco de dados na região, o grupo de failover iniciará automaticamente o failover do banco de dados primário na região A para o secundário na região B. Ele também atualizará automaticamente o ponto de extremidade do ouvinte de leitura/gravação para a região B para que as conexões de leitura/gravação no aplicativo Web não sejam afetados. O gerenciador de tráfego exclui o ponto de extremidade offline da tabela de roteamento, mas continua a rotear o tráfego do usuário final para as instâncias online restantes. As cadeias de conexão do SQL de somente leitura não serão afetadas, pois elas sempre apontam para o banco de dados na mesma região. 

O diagrama a seguir ilustra a nova configuração após o failover.

![Configuração após o failover. Recuperação de desastre em nuvem.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-2.png)

No caso de uma interrupção em uma das regiões secundários, o gerenciador de tráfego removerá automaticamente da tabela de roteamento o ponto de extremidade offline nessa região. O canal de replicação para o banco de dados secundário nessa região será suspenso. Como as demais regiões recebem tráfego de usuário adicional, o desempenho do aplicativo será afetado durante a interrupção. Depois que a interrupção é atenuada, o banco de dados secundário na região afetada será imediatamente sincronizado com o primário. Durante a sincronização, o desempenho do primário poderá ser ligeiramente afetado, dependendo da quantidade de dados que precisam ser sincronizados. O diagrama a seguir ilustra uma interrupção na região B.

![Interrupção na região secundária. Recuperação de desastre em nuvem – replicação geográfica.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-3.png)

A principal **vantagem** desse padrão de design é que você pode dimensionar a carga de trabalho do aplicativo em vários secundários para obter o desempenho ideal para o usuário final. As **desvantagens** dessa opção são:

* As conexões de leitura e gravação entre as instâncias do aplicativo e o banco de dados têm latência e custos variáveis
* O desempenho do aplicativo é afetado durante a interrupção

> [!NOTE]
> Uma abordagem semelhante pode ser usada para descarregar cargas de trabalho especializadas, como trabalhos de geração de relatórios, ferramentas de business intelligence ou backups. Normalmente, essas cargas de trabalho consomem recursos significativos do banco de dados. Portanto, é recomendável que você designe um dos bancos de dados secundários para elas com o nível de desempenho correspondente à carga de trabalho antecipada.
>

## <a name="design-pattern-3-active-passive-deployment-for-data-preservation"></a>Padrão de design 3: implantação ativa-passiva para preservação dos dados
Essa opção é mais adequada para aplicativos com as seguintes características:

* Qualquer perda de dados é um risco comercial elevado. O failover de banco de dados só poderá ser usado como último recurso, se a interrupção for catastrófica.
* O aplicativo oferece suporte aos modos somente leitura e leitura/gravação de operações e pode operar em “modo somente leitura” por um período de tempo específico.

Nesse padrão, o aplicativo alterna para o modo somente leitura quando as conexões de leitura/gravação começam a receber erros de tempo limite. O aplicativo Web é implantado em ambas as regiões e inclui uma conexão para o ponto de extremidade do ouvinte de leitura/gravação e uma conexão diferente para o ponto de extremidade do ouvinte de somente leitura. O gerenciador de tráfego deve ser configurado para usar o [roteamento de failover](../traffic-manager/traffic-manager-configure-failover-routing-method.md) com o [monitoramento do ponto de extremidade](../traffic-manager/traffic-manager-monitoring.md) habilitado o ponto de extremidade do aplicativo em cada região.

O diagrama a seguir ilustra essa configuração antes de uma interrupção.

![Implantação ativo-passivo antes do failover. Recuperação de desastre em nuvem.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-1.png)

Quando o gerenciador de tráfego detecta uma falha de conectividade para região A, ele alternará automaticamente o tráfego de usuário para a instância do aplicativo na região B. Com esse padrão, é importante definir o período de carência de perda de dados com um valor suficientemente alto, por exemplo, 24 horas. Ele garantirá que a perda de dados é impedida se a interrupção for atenuada dentro desse período. Quando o aplicativo Web na região B é ativado as operações de leitura/gravação começarão a falhar. Nesse ponto, ele deve alternar para o modo somente leitura. Nesse modo as solicitações serão roteadas para o banco de dados secundário automaticamente. No caso de uma falha catastrófica a interrupção não será atenuada dentro do período de carência e o grupo de failover irá disparar o failover. Depois disso o ouvinte de leitura/gravação ficará disponível e as chamadas para ele não irão mais falhar. Isso é ilustrado pelo diagrama a seguir.

![Interrupção: aplicativo em modo somente leitura. Recuperação de desastre em nuvem.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-2.png)

Se a interrupção na região primária for atenuada dentro do período de carência, o gerenciador de tráfego detecta a restauração da conectividade na região primária e alterna o tráfego do usuário de volta para a instância do aplicativo na região A. Essa instância do aplicativo continua e opera em modo leitura/gravação usando o banco de dados primário na região A.

No caso de uma interrupção na região B, o Gerenciador de Tráfego detecta a falha do ponto de extremidade do aplicativo na região B e o grupo de failover troca o ouvinte de somente leitura para a região A. Essa interrupção não afeta a experiência do usuário final, mas o banco de dados primário ficará exposto durante a interrupção. Isso é ilustrado pelo diagrama a seguir.

![Interrupção: banco de dados secundário. Recuperação de desastre em nuvem.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-3.png)

Após a interrupção ser atenuada, o banco de dados secundário é sincronizado imediatamente com o primário e o ouvinte de somente leitura é revertido para o banco de dados secundário na região B. Durante a sincronização, o desempenho do banco de dados primário pode ser afetado ligeiramente dependendo da quantidade de dados que precisam ser sincronizados.

Esse padrão de design apresenta várias **vantagens**:

* Ele evita a perda de dados durante as interrupções temporárias.
* O tempo de inatividade depende somente da velocidade com a qual o gerenciador de tráfego detecta a falha de conectividade, o que pode ser configurado.

As **desvantagens** são:

* O aplicativo deve ser capaz de operar no modo somente leitura.

> [!NOTE]
> No caso de uma interrupção de serviço permanente na região, ative manualmente o failover de banco de dados e aceite a perda de dados. O aplicativo será funcional na região secundária com acesso de leitura e gravação ao banco de dados.
>

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Planejamento de continuidade de negócios: escolher um design de aplicativo para recuperação de desastre em nuvem
A estratégia específica de recuperação de desastre em nuvem pode combinar ou estender esses padrões de design para atender da melhor forma às necessidades do aplicativo.  Como mencionado anteriormente, a estratégia escolhida é baseada no SLA que você deseja oferecer a seus clientes e na topologia de implantação do aplicativo. Para ajudar a orientar sua decisão, a tabela a seguir compara as opções com base na perda de dados estimada ou no RPO (objetivo de ponto de recuperação) e no ERT (tempo de recuperação estimado).

| Padrão | RPO | ERT |
|:--- |:--- |:--- |
| Implantação ativa-passiva para recuperação de desastres com acesso de banco de dados colocalizado |Acesso de leitura/gravação < 5 s |Tempo de detecção de falha + TTL do DNS |
| Implantação ativa-ativa para balanceamento de carga de aplicativo |Acesso de leitura/gravação < 5 s |Tempo de detecção de falha + TTL do DNS |
| Implantação ativa-passiva para preservação de dados |Acesso somente leitura < 5 seg | Acesso somente leitura = 0 |
||Acesso de leitura/gravação = zero | Acesso de leitura/gravação = Tempo de detecção de falha + período de carência de perda de dados |
|||

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, consulte [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md)
* Para obter uma visão geral e os cenários de continuidade dos negócios, confira [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
* Para saber mais sobre como usar backups automatizados de recuperação, veja [Restaurar um banco de dados de backups iniciados pelo serviço](sql-database-recovery-using-backups.md)
* Para saber mais sobre opções de recuperação mais rápidas, confira [replicação geográfica ativa](sql-database-geo-replication-overview.md)  
* Para saber mais sobre como usar backups automatizados para arquivamento, consulte [cópia de banco de dados](sql-database-copy.md)
* Para obter informações sobre o uso da replicação geográfica ativa com Pools elásticos, confira [Estratégias de recuperação de desastres do pool elástico](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

