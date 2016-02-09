<properties
	pageTitle="Criar pools de bancos de dados elásticos escalonáveis | Microsoft Azure"
	description="Como adicionar um pool de bancos de dados elásticos escalonável à sua configuração do banco de dados SQL para facilitar a administração e o compartilhamento de recursos entre vários bancos de dados."
	keywords="banco de dados escalonável, configuração do banco de dados"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="02/02/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="NA"/>


# Criar um pool de bancos de dados elástico escalonável para bancos de dados SQL no portal do Azure

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-portal.md)
- [C#](sql-database-elastic-pool-csharp.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)

Este artigo mostra como criar um [pool de bancos de dados elástico](sql-database-elastic-pool.md) escalonável usando o portal do Azure. Uma configuração de Banco de Dados SQL com pools de bancos de dados elásticos simplifica a administração e o compartilhamento de recursos entre vários bancos de dados.

> [AZURE.NOTE] No momento, os pools de banco de dados elástico estão em visualização e disponíveis apenas com Servidores V12 do Banco de Dados SQL. Se você tiver um servidor de Banco de Dados SQL V11, poderá [usar o PowerShell para atualizar para o V12 e criar um pool](sql-database-upgrade-server-powershell.md) em uma única etapa.


Antes de começar, será necessário pelo menos um banco de dados em um servidor do Banco de Dados SQL V12. Se você não tiver um, consulte [Criar seu primeiro banco de dados SQL do Azure](sql-database-get-started.md) para criar um em menos de cinco minutos. Ou se você já tiver um Banco de Dados SQL V11, poderá [atualizar para o V12 no portal de](sql-database-v12-upgrade.md), voltar e seguir estas instruções para criar um pool.


## Etapa 1: criar um novo pool

Crie um pool de banco de dados elástico adicionando um novo pool a um servidor. Você pode adicionar vários pools a um servidor, mas não pode adicionar bancos de dados de servidores diferentes ao mesmo pool.


1. No [portal do Azure](http://portal.azure.com/), clique em **Servidores SQL**.
2. Selecione o servidor que contém os bancos de dados que você deseja adicionar ao pool.
3. Clique em **Novo pool** (ou, se você receber uma mensagem informando que há um pool recomendado, clique nela para examinar e criar facilmente um pool otimizado para os bancos de dados do seu servidor). A seguir, mais detalhes sobre as recomendações.


    ![Adicionar pool a um servidor](./media/sql-database-elastic-pool-portal/new-pool.png)


4. Na folha **Pool de banco de dados elástico**, você pode deixar o nome padrão ou digitar um nome para o novo pool.

    ![Configurar pool elástico](./media/sql-database-elastic-pool-portal/configure-elastic-pool.png)


### Pools de banco de dados elástico recomendados

Navegue até um servidor do Banco de Dados SQL; talvez você veja uma mensagem informando que existem pools de banco de dados elástico recomendados para o servidor (somente V12).

### Por que estou recebendo uma recomendação?

O serviço de Banco de Dados SQL avalia o histórico de uso e recomenda um ou mais pools de banco de dados elástico quando essa alternativa é mais econômica do que o uso de bancos de dados individuais.

![pool recomendado](./media/sql-database-elastic-pool-portal/recommended-pool.png)

Cada recomendação é configurada com um subconjunto exclusivo dos bancos de dados do servidor que melhor se enquadram no pool.

Cada recomendação de pool contém o seguinte:

- Camada de preços para o pool (Basic, Standard ou Premium).
- Quantidade apropriada de eDTUs do pool.
- As configurações de limite mínimo/máximo de eDTUs no banco de dados elástico.
- Lista de bancos de dados recomendados.

O serviço leva os últimos 30 dias de telemetria em conta ao recomendar pools de banco de dados elástico. Para que um banco de dados seja considerado como candidato para um pool de banco de dados elástico, ele deve existir há pelo menos 7 dias. Bancos de dados que já estão em um pool de banco de dados elástico não são considerados candidatos para as recomendações de pool de banco de dados elástico.

O serviço avalia as os recursos necessários e o custo-benefício de mover os bancos de dados individuais em cada camada de serviço para pools de banco de dados elástico pertencentes ao mesmo nível. Por exemplo, todos os bancos de dados padrão em um servidor são avaliados para sua adaptação em um pool elástico Standard. Isso significa que o serviço não faz recomendações entre camadas diferentes, como mover um banco de dados Standard para um pool Premium.


### Criar um pool recomendado

1. Clique na mensagem para ver uma lista dos pools recomendados.
1. Clique em um pool para ver a recomendação detalhada.
2. Examine as configurações na recomendação e as aceite como estão ou edite a recomendação para ajustá-la às suas necessidades de negócios específicas.


## Etapa 2: escolher um tipo de preço

O tipo de preço do pool determina os recursos disponíveis para os bancos de dados elásticos no pool, o número máximo de eDTUs (MÁX DE eDTUs) e o armazenamento (GBs) disponível para cada banco de dados. Para obter detalhes, consulte [Camadas de serviço](sql-database-service-tiers.md#Service-tiers-for-elastic-database-pools).

>[AZURE.NOTE] Atualmente na visualização, você não pode alterar a camada de preços de um pool de banco de dados elástico após a sua criação. Para alterar a camada de preços para um pool elástico existente, crie um novo pool elástico na camada de preços desejada e migre os bancos de dados elásticos para esse novo pool.

   ![Escolher um tipo de preço](./media/sql-database-elastic-pool-portal/pricing-tier.png)

### Configurar o pool

Depois de definir o tipo de preço, clique em Configurar pool, onde você adicionará bancos de dados, definirá eDTUs e armazenamento do pool (GBs do pool) e onde você definirá os eDTUs mínimo e máximo para os bancos de dados elásticos no pool.

## Etapa 3: adicionar bancos de dados ao pool

Você pode adicionar bancos de dados a um pool ou removê-los a qualquer momento.

1. Durante a criação do pool, clique em **Adicionar bancos de dados** na folha **Configurar pool**.
2. Selecione os bancos de dados que você deseja adicionar ao pool:

    ![Adicionar bancos de dados](./media/sql-database-elastic-pool-portal/add-databases.png)

    Ao selecionar um banco de dados para adição a um pool, as condições a seguir devem ser atendidas:

    - O pool deve ter espaço para o banco de dados (não pode conter o número máximo de bancos de dados). Mais especificamente, o pool deve ter eDTUs suficientes disponíveis para cobrir a garantia de eDTUs por banco de dados (por exemplo, se a garantia de eDTUs para o grupo for de 400, e a garantia de eDTUs para cada banco de dados for de 10, o número máximo de bancos de dados permitidos no pool será de 40 (400 eDTUs/10 eDTUs garantidos por banco de dados = Máx. de 40 bancos de dados).
    - Os recursos atuais usados pelo banco de dados devem estar disponíveis no pool.

### Recomendações dinâmicas

Depois de adicionar os bancos de dados ao pool, as recomendações serão dinamicamente geradas com base no uso histórico dos bancos de dados selecionados. Essas recomendações serão mostradas no gráfico de uso de GB e de eDTU, bem como em uma faixa de recomendação na parte superior da folha **Configurar pool**. Essas recomendações devem ajudar na criação de um pool otimizado para bancos de dados específicos.


 ![recomendações dinâmicas](./media/sql-database-elastic-pool-portal/dynamic-recommendation.png)


## Etapa 4: definindo as características de desempenho do pool

Configure o desempenho do pool definindo os parâmetros de desempenho do pool e dos bancos de dados elásticos no pool. Lembre-se de que as **Configurações do banco de dados elástico** se aplicam a todos os bancos de dados no pool.

 ![Configurar Pool Elástico](./media/sql-database-elastic-pool-portal/configure-performance.png)

É possível configurar três parâmetros para definir o desempenho do pool: a Garantia de eDTUs para o pool e o MÍN DE eDTUs e MÁX. DE eDTUs para os bancos de dados elásticos no pool. A tabela a seguir descreve cada um deles e fornece orientações sobre como configurá-los. Para obter as configurações de valor específicas disponíveis, veja [referência do pool de banco de dados elástico](sql-database-elastic-pool-reference.md).

| Parâmetro de desempenho | Descrição |
| :--- | :--- |
| **eDTU DO POOL** – garantia de eDTU para o pool | A garantia de eDTUs para o pool é o número garantido de eDTUs disponíveis e compartilhados por todos os bancos de dados no pool. <br> O tamanho específico da garantia de eDTU para um grupo deve ser provisionado levando em consideração a utilização histórica de eDTUs do grupo. Como alternativa, esse tamanho pode ser definido pela garantia desejada de eDTUs por banco de dados e pela utilização de bancos de dados ativos simultaneamente. A garantia de eDTUs para o pool também está correlacionada à quantidade de armazenamento disponível para o pool. Para cada DTU alocado para o pool, você obtém uma quantidade fixa de armazenamento no banco de dados. <br> **Com qual valor devo definir a garantia de eDTU do pool?** <br>No mínimo, você deve definir a garantia de eDTU do pool como ([Nº de bancos de dados] x [utilização média de DTU por banco de dados]). |
| **MÍN. DE eDTUs** – garantia de eDTU para cada banco de dados | A garantia de eDTUs por banco de dados é o número de eDTUs garantidos para um único banco de dados no pool. <br> **Com qual valor devo definir a garantia de eDTU por banco de dados?** <br> Normalmente, a garantia de eDTU por banco de dados (MÍN. DE eDTU) é definida como qualquer valor entre 0 e a ([utilização média por banco de dados]). A garantia eDTU por banco de dados é uma configuração global que define a garantia de eDTUs para todos os bancos de dados no pool. |
| **MÁX. DE eDTU** – limite de eDTU por banco de dados | O MÁX. DE eDTUs por banco de dados é o número máximo de eDTUs que podem ser usados por um único banco de dados no pool. Defina o limite de eDTUs por banco de dados com um valor alto o suficiente para lidar com picos máximos que seus bancos de dados podem enfrentar. Você pode definir esse limite até o limite do sistema, o que depende da camada de preços do pool (1.000 eDTUs para Premium). O tamanho específico deste limite deve acomodar padrões de pico de utilização de bancos de dados dentro do grupo. Espera-se algum grau de sobrecarga do grupo, uma vez que o pool normalmente assume padrões de uso dos bancos de dados com altos e baixos, em que todos os bancos de dados não atingem um pico simultaneamente.<br> **Com qual valor eu devo definir o limite de eDTUs por banco de dados?** <br> Defina o MÁX. DE eDTU ou o limite de eDTU por banco de dados como ([pico de utilização do banco de dados]). Por exemplo, suponha que o pico de utilização por banco de dados seja de 50 DTUs e apenas 20% dos 100 bancos de dados no grupo atinjam o pico simultaneamente. Se o limite de eDTUs por banco de dados for definido como 50 eDTUs, será razoável sobrecarregar o grupo em cinco vezes e definir a garantia de eDTUs para o grupo (eDTUs do POOL) como 1.000 eDTUs. Também vale a pena observar que o limite de eDTUs não é uma garantia de recursos para um banco de dados, mas sim um teto de eDTUs que pode ser atingido se estiver disponível. |


## Adicionar bancos de dados ao pool e removê-los

Depois de criar um pool, você pode adicionar bancos de dados existentes ao pool ou removê-los dele, adicionando ou removendo bancos de dados na página **Bancos de dados elásticos** (procure seu pool e clique no link **Bancos de dados elásticos** em **Essentials**).

- Clique em **Adicionar bancos de dados** para abrir a lista de bancos de dados que você pode adicionar ao pool.

    -ou-

- Selecione os bancos de dados que você deseja remover do pool e clique em **Remover bancos de dados**.

![pool recomendado](./media/sql-database-elastic-pool-portal/add-remove-databases.png)

## Criar um novo servidor de banco de dados em um pool

Crie um novo banco de dados em um pool navegando até o pool desejado e clicando em **Criar banco de dados**.

O banco de dados SQL já está configurado para o servidor e o pool corretos, portanto, insira um nome e escolha suas opções de banco de dados e, em seguida, clique em **OK** para criar um novo banco de dados:


   ![criar banco de dados elástico](./media/sql-database-elastic-pool-portal/create-database.png)



## Monitorar e gerenciar um pool de banco de dados elástico

Depois de criar um pool de banco de dados elástico, você pode monitorar e gerenciar o pool no portal navegando até a lista de pools existentes e selecionando o pool desejado.

Depois de criar um pool, você pode:

- Selecione **Configurar pool** para alterar as configurações de eDTU do pool e de eDTU por banco de dados.
- Selecionar **Criar trabalho** e gerenciar os bancos de dados no pool, criando trabalhos elásticos. Trabalhos elásticos facilitam a execução de scripts Transact-SQL em qualquer quantidade de bancos de dados no pool. Para saber mais, confira [Visão geral sobre os trabalhos elásticos de banco de dados](sql-database-elastic-jobs-overview.md).
- Selecionar **Gerenciar trabalhos** para administrar os trabalhos elásticos existentes.
- Clique em **Criar banco de dados** para criar um novo banco de dados no pool.
- Clique no link de configurações do pool para ajustar os eDTUs e o GB por pool
- Clique no link de bancos de dados para adicionar bancos de dados ao pool ou para removê-los.
- Clique no link de configurações de banco de dados para ajustar as eDTUs mínima e máxima para os bancos de dados elásticos no pool. 

![Monitorar um pool elástico](./media/sql-database-elastic-pool-portal/pool-tasks.png)


Ao selecionar um pool existente, você pode ver a utilização de recursos do pool. Clique no gráfico **Utilização de Recursos** para abrir a folha **Métrica** na qual é possível personalizar o gráfico e configurar alertas.

![Monitorar um pool elástico][4] ![utilização de recursos][6]

Clique em **Editar gráfico** para adicionar parâmetros de modo que você possa exibir facilmente os dados de telemetria do pool.


![editar gráfico][7]


## Próximas etapas
Depois de criar um pool de banco de dados elástico, você pode gerenciar os bancos de dados no pool criando trabalhos elásticos. Trabalhos elásticos facilitam a execução de scripts Transact-SQL com relação a qualquer quantidade de bancos de dados no pool. Para saber mais, confira [Visão geral sobre os trabalhos elásticos de banco de dados](sql-database-elastic-jobs-overview.md).



## Recursos adicionais

- [Pool elástico de Banco de Dados SQL](sql-database-elastic-pool.md)
- [Criar um pool elástico de Banco de Dados SQL com o PowerShell](sql-database-elastic-pool-powershell.md)
- [Criar e gerenciar Banco de Dados SQL com C#](sql-database-client-library.md)
- [Referência de banco de dados elástico](sql-database-elastic-pool-reference.md)


<!--Image references-->
[4]: ./media/sql-database-elastic-pool-portal/monitor-elastic-pool.png
[6]: ./media/sql-database-elastic-pool-portal/metric.png
[7]: ./media/sql-database-elastic-pool-portal/edit-chart.png
[10]: ./media/sql-database-elastic-pool-portal/star.png

<!---HONumber=AcomDC_0204_2016-->