<properties
	pageTitle="Criar pools de bancos de dados elásticos escalonáveis | Microsoft Azure"
	description="Como adicionar um pool de banco de dados elástico escalonável à sua configuração do banco de dados SQL para facilitar a administração e o compartilhamento de recursos entre vários bancos de dados."
	keywords="banco de dados escalonável, configuração do banco de dados"
	services="sql-database"
	documentationCenter=""
	authors="jeffgoll"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="03/16/2016"
	ms.author="jeffreyg"
	ms.workload="data-management"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="NA"/>


# Criar um pool de bancos de dados elástico escalonável para bancos de dados SQL no portal do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-elastic-pool-create-portal.md)
- [C#](sql-database-elastic-pool-create-csharp.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)

Este artigo mostra como criar um [pool de banco de dados elástico](sql-database-elastic-pool.md) escalonável no [portal do Azure](https://portal.azure.com/). Há duas maneiras de criar um pool. Você pode fazer isso do zero, se souber a configuração de pool desejada, ou iniciar com uma recomendação do serviço. Esse serviço possui inteligência interna para recomendar uma configuração de pool se ela for mais econômica com base na telemetria de uso anterior do banco de dados.

Você pode adicionar vários pools a um servidor, mas não pode adicionar bancos de dados de servidores diferentes ao mesmo pool. Para criar um pool, você precisa de pelo menos um banco de dados em um servidor V12. Se você não tiver um, consulte [Criar seu primeiro banco de dados SQL do Azure](sql-database-get-started.md). Você pode criar um pool com apenas um banco de dados, mas os pools são econômicos apenas quando há vários bancos de dados. Consulte [Considerações de preço e desempenho para um pool de banco de dados elástico](sql-database-elastic-pool-guidance.md).

> [AZURE.NOTE] Os pools só estão disponíveis com os servidores V12 do Banco de Dados SQL. Se você tiver uma versão anterior do servidor, poderá [usar o PowerShell para atualizar para o V12 e criar um pool](sql-database-upgrade-server-powershell.md) em uma única etapa.

##Criar um novo pool
1. No [portal do Azure](http://portal.azure.com/), clique em **Servidores SQL** e clique no servidor que contém os bancos de dados que você deseja adicionar a um pool.
2. Clique em **Novo pool**.

    ![Adicionar pool a um servidor](./media/sql-database-elastic-pool-create-portal/new-pool.png)

    **-OU-**

    Clique na mensagem para ver os pools recomendados com base na telemetria de uso histórico do banco de dados e clique na camada para ver mais detalhes e personalizar o pool. Consulte [Entender as recomendações de pool](#understand-pool-recommendations) mais adiante neste tópico para saber como a recomendação é feita.

    ![pool recomendado](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)

    A folha **pool de banco de dados elástico** aparece e é onde você irá configurar o pool. Se você clicou em **Novo pool** na etapa anterior, o portal escolherá um **Pool padrão** em **Camada de preço**, um **Nome** exclusivo para o pool e uma configuração padrão para o pool. Se você escolheu um pool recomendado, a camada e a configuração recomendadas do pool já terão sido escolhidas, mas você ainda poderá alterá-las.

    ![Configurar pool elástico](./media/sql-database-elastic-pool-create-portal/configure-elastic-pool.png)

3. Para alterar o tipo de preço do pool, clique em **Tipo de preço**, clique no tipo desejado e em **Selecionar**.

    > [AZURE.IMPORTANT] Depois de escolher o tipo de preço e confirmar suas alterações clicando em **OK** na última etapa, não será possível alterar o tipo de preço do pool.

4. Clique em **Configurar pool**, adicione os bancos de dados e escolha as configurações de recursos do pool.
5. Para adicionar bancos de dados, clique em **Adicionar banco de dados**, clique nos bancos de dados que você deseja adicionar, em seguida, clique no botão **Selecionar**.

    ![Adicionar bancos de dados](./media/sql-database-elastic-pool-create-portal/add-databases.png)

    Se os bancos de dados nos quais você está trabalhando tiverem telemetria de uso histórico suficiente, o gráfico **Uso estimado de eDTU e GB** e o gráfico de barras **Uso real de eDTU** serão atualizados para ajudá-lo a tomar decisões de configuração. Além disso, o serviço pode fornecer uma mensagem de recomendação para ajudá-lo a planejar o tamanho do pool. Consulte [Entender as recomendações de pool](#understand-pool-recommendations).

    ![recomendações dinâmicas](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

6. Use os controles na página **Configurar pool** para explorar as configurações e configure o pool de acordo com as diretrizes a seguir:

    ![Configurar Pool Elástico](./media/sql-database-elastic-pool-create-portal/configure-performance.png)

    | Configuração do desempenho | Descrição |
    | :--- | :--- |
    | **eDTU do POOL** e **GB do Pool** (de acordo com a configuração do pool)| O Máximo de eDTUs disponíveis e compartilhados por todos os bancos de dados no pool. O máximo de eDTUs disponíveis em um pool dependem do tipo de preço (camada de serviço). O **eDTU do Pool** corresponde ao armazenamento disponível para o pool. Para cada eDTU alocado para o pool, você obtém uma quantidade fixa de armazenamento de bancos de dados, e vice-versa. |
    | **MÍN. DE eDTU** (de acordo com a configuração do banco de dados)| O número mínimo de eDTUs do pool garantido para um banco de dados individual a qualquer momento. O **MÍN DE eDTU** normalmente é definido entre 0 e a utilização história média do eDTU por banco de dados. Essa é uma configuração global que se aplica a todos os bancos de dados no pool. |
    | **MÁX. DE eDTU** (de acordo com a configuração do banco de dados) | O número máximo de eDTUs que um banco de dados individual no pool pode usar. Você pode definir esse limite até o valor do **eDTU do POOL**. Configure o **MÁX. DE eDTU** por banco de dados com um valor alto o suficiente para lidar com as intermitências ou picos de utilização do banco de dados. Espera-se um grau de sobrecarga do grupo, uma vez que o pool normalmente assume padrões de uso dos bancos de dados com altos e baixos, em que todos os bancos de dados não atingem um pico simultaneamente. **Por exemplo**: suponha que o pico de utilização por banco de dados seja de 50 DTUs e apenas 20% dos 100 bancos de dados no grupo atinjam o pico simultaneamente. Se o limite de eDTUs por banco de dados for definido para 50 eDTUs, será razoável sobrecarregar o pool em cinco vezes e definir o **eDTU do POOL** para 1.000. O **MÁX. DE eDTU** não é uma garantia de recursos para um banco de dados, mas sim um teto de eDTUs que pode ser atingido se estiver disponível. |

    Consulte a [Referência do pool de banco de dados elástico](sql-database-elastic-pool-reference.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases) para obter mais detalhes sobre os limites de cada camada de serviço e consulte as [Considerações de preço e desempenho dos pools de bancos de dados elástico](sql-database-elastic-pool-guidance.md) para obter uma orientação detalhada sobre o tamanho correto de um pool.

7. Clique em **Selecionar** quando concluir, em seguida, clique em **OK** para criar o pool.

##Entender as recomendações de pool
O serviço do Banco de Dados SQL avalia o histórico de uso e recomenda um ou mais pools quando essa alternativa for mais econômica do que usar bancos de dados individuais. Cada recomendação é configurada com um subconjunto exclusivo de bancos de dados do servidor que melhor se enquadram no pool. A recomendação de pool inclui:

- Um tipo de preço para o pool (Basic, Standard ou Premium)
- Os **eDTUs do POOL** apropriados (também chamados de Máx. de eDTUs por pool)
- O **MÁX. DE eDTU** e o **MÍN. DE eDTU** por banco de dados
- A lista dos bancos de dados recomendados para o pool

O serviço leva em consideração os últimos 30 dias de telemetria ao recomendar os pools. Para que um banco de dados seja considerado como candidato para um pool de banco de dados elástico, ele deve existir há pelo menos 7 dias. Bancos de dados que já estão em um pool de banco de dados elástico não são considerados candidatos para as recomendações de pool de banco de dados elástico.

O serviço avalia os recursos necessários e o custo-benefício de mover os bancos de dados individuais em cada camada de serviço para os pools na mesma camada. Por exemplo, todos os bancos de dados padrão em um servidor são avaliados para sua adaptação em um pool elástico Standard. Isso significa que o serviço não faz recomendações entre camadas diferentes, como mover um banco de dados Standard para um pool Premium.

## Recursos adicionais

- [Gerenciar um pool elástico do Banco de Dados SQL com o portal](sql-database-elastic-pool-manage-portal.md)
- [Gerenciar um pool elástico do Banco de Dados SQL com o PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [Gerenciar um pool elástico do Banco de Dados SQL com o C#](sql-database-client-library.md)
- [Referência de banco de dados elástico](sql-database-elastic-pool-reference.md)

<!---HONumber=AcomDC_0316_2016-->