<properties
	pageTitle="Criar e gerenciar um pool de banco de dados elástico do Banco de Dados SQL | Microsoft Azure"
	description="Criar um pool de recursos único para compartilhar em um grupo de Bancos de Dados SQL do Azure."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="08/25/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Criar e gerenciar um pool de banco de dados elástico do Banco de Dados SQL do Azure

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-portal.md)
- [C#](sql-database-client-library.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)

Este artigo mostra como criar um pool de banco de dados elástico com o [Portal de visualização do Azure](https://portal.azure.com).

Os pools de banco de dados elástico simplificam o processo de criação, manutenção e gerenciamento do desempenho e também do custo para grandes quantidades de bancos de dados.


> [AZURE.NOTE]No momento, os pools de banco de dados elástico estão em visualização e disponíveis apenas com servidores V12 do Banco de Dados SQL.




## Pré-requisitos

Para criar um pool de banco de dados elástico, você precisa do seguinte:

- Uma assinatura do Azure. Se você precisar de uma assinatura do Azure basta clicar em **AVALIAÇÃO GRATUITA** na parte superior desta página e, em seguida, voltar para concluir este artigo.
- Um servidor do Banco de Dados SQL versão 12. Se você não tiver um servidor V12, crie um seguindo as etapas neste artigo: [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md).



## Criar um pool de banco de dados elástico

Crie um pool de banco de dados elástico adicionando um novo pool a um servidor. Você pode adicionar vários pools a um servidor, mas apenas um (1) servidor pode ser associado a cada pool. Além disso, é possível adicionar todos os bancos de dados em um servidor, ou alguns deles, a um pool.


1.	Selecione um servidor V12 de Banco de Dados SQL que contenha os bancos de dados que você deseja adicionar ao pool.
2.	Crie o pool selecionando **adicionar pool** na parte superior da folha **SQL Server**.

   ![Criar Pool Elástico][1]

## Configurar um pool de banco de dados elástico

Configure o pool definindo a camada de preços, adicionando bancos de dados e configurando as características de desempenho do pool.

> [AZURE.NOTE]Ao selecionar o comando **Adicionar pool**, você precisa aceitar os termos da visualização selecionando **TERMOS DA VISUALIZAÇÃO** e preenchendo a folha **Termos da Visualização**. Basta aceitar uma vez os termos para cada assinatura.

   ![Configurar pool elástico][2]

## Configurar a camada de preços

A camada de preços de um pool de banco de dados elástico determina os recursos disponíveis para os bancos de dados elásticos no pool, o número máximo de eDTUs (eDTU MAX) e o armazenamento (GB) disponíveis para cada banco de dados. Para obter detalhes, consulte [Limites de eDTU e armazenamento para pools elásticos e bancos de dados elásticos](sql-database-elastic-pool-reference.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases).

>[AZURE.NOTE]Atualmente na visualização, você não pode alterar a camada de preços de um pool de banco de dados elástico após a sua criação. Para alterar a camada de preços para um pool elástico existente, crie um novo pool elástico na camada de preços desejada e migre os bancos de dados elásticos para esse novo pool.

   ![Camada de preços][9]


## Recomendações para camada de preços de pool de banco de dados elástico

O serviço de Banco de Dados SQL avalia o histórico de utilização e recomenda um ou mais pools de banco de dados elástico quando essa alternativa é mais econômica do que o uso de bancos de dados individuais. Camadas de preços com uma estrela (![estrela][10]) são recomendadas com base nas cargas de trabalho dos bancos de dados.

Se mais de uma camada de preços é recomendada, isso indica que vários pools de banco de dados elástico devem ser criados. Cada recomendação é configurada com um subconjunto exclusivo dos bancos de dados do servidor que melhor se enquadram no pool.

Além de simplesmente sugerir uma camada de preços de pool de banco de dados elástico, cada recomendação de pool contém o seguinte:

- Camada de preços para o pool (Basic, Standard ou Premium).
- Quantidade apropriada de eDTUs do pool.
- As configurações de limite mínimo/máximo de eDTUs no banco de dados.  
- Lista de bancos de dados recomendados.

O serviço leva os últimos 30 dias de telemetria em conta ao recomendar pools de banco de dados elástico. Para que um banco de dados seja considerado como candidato para um pool de banco de dados elástico, ele deve existir há pelo menos 7 dias. Bancos de dados que já estão em um pool de banco de dados elástico não são considerados candidatos para as recomendações de pool de banco de dados elástico.

O serviço avalia as os recursos necessários e o custo-benefício de mover os bancos de dados individuais em cada camada de serviço para pools de banco de dados elástico pertencentes ao mesmo nível. Por exemplo, todos os bancos de dados padrão em um servidor são avaliados para sua adaptação em um pool elástico Standard. Isso significa que o serviço não faz recomendações entre camadas diferentes, como mover um banco de dados Standard para um pool Premium.

>[AZURE.NOTE]Bancos de dados da Web e comerciais são mapeados para uma das novas camadas Basic, Standard ou Premium com base em seu tamanho de banco de dados e histórico de utilização. O mapeamento para as novas camadas recomenda bancos de dados da Web e comerciais para o pool apropriado.


### Adicionar bancos de dados

Você pode selecionar, a qualquer momento, os bancos de dados específicos para inclusão no pool. Quando você cria um novo pool, o Azure recomenda os bancos de dados que se beneficiarão por estarem em um pool e os marca para inclusão. Você pode adicionar todos os bancos de dados disponíveis no servidor, ou marcar ou desmarcar os bancos de dados na lista inicial como desejado.

   ![Adicionar bancos de dados][5]

Ao selecionar um banco de dados para adição a um pool, as condições a seguir devem ser atendidas:

- O pool deve ter espaço para o banco de dados (não pode conter o número máximo de bancos de dados). Mais especificamente, o pool deve ter eDTUs suficientes disponíveis para cobrir a garantia de eDTUs por banco de dados (por exemplo, se a garantia de eDTUs para o grupo for de 400, e a garantia de eDTUs para cada banco de dados for de 10, o número máximo de bancos de dados permitidos no pool será de 40 (400 eDTUs/10 eDTUs garantidos por banco de dados = Máx. de 40 bancos de dados).
- Os recursos atuais usados pelo banco de dados devem estar disponíveis no pool.


### Configurar o desempenho

Você pode configurar o desempenho do pool definindo os parâmetros de desempenho para o pool e para os bancos de dados elásticos no pool. Lembre-se de que as **configurações do banco de dados elástico** se aplicam a todos os bancos de dados no pool.

   ![Configurar Pool Elástico][3]

É possível configurar três parâmetros para definir o desempenho do pool: a Garantia de eDTUs para o pool e o MÍN DE eDTUs e MÁX. DE eDTUs para os bancos de dados elásticos no pool. A tabela a seguir descreve cada um deles e fornece orientações sobre como configurá-los. Para configurações de valor específicas disponíveis, consulte [referência de pool do banco de dados elástico](sql-database-elastic-pool-reference.md).

| Parâmetro de desempenho | Descrição |
| :--- | :--- |
| **eDTU DE POOL** – garantia de eDTU para o pool | A garantia de eDTUs para o pool é o número garantido de eDTUs disponíveis e compartilhados por todos os bancos de dados no pool. <br> O tamanho específico da garantia de eDTUs para um grupo deve ser fornecido levando em consideração o histórico de utilização de eDTUs do grupo. Como alternativa, esse tamanho pode ser definido pela garantia desejada de eDTUs por banco de dados e pela utilização de bancos de dados ativos simultaneamente. A garantia de eDTUs para o pool também está correlacionada à quantidade de armazenamento disponível para o pool. Para cada DTU alocado para o pool, você obtém uma quantidade fixa de armazenamento no banco de dados. <br> **Para que devo definir a garantia de eDTU do pool?** <br>No mínimo, você deve definir a garantia de eDTUs do pool como ([Nº de bancos de dados] x [utilização média de DTUs por banco de dados]). |
| **MÍN. DE eDTUs** – garantia de eDTUs para cada banco de dados | A garantia de eDTUs por banco de dados é o número de eDTUs garantidos para um único banco de dados no pool. Por exemplo, em pools Standard, você pode definir essa garantia como 0, 10, 20, 50 ou 100 eDTUs, ou você pode optar por não fornecer uma garantia aos bancos de dados no grupo (MÍN. DE eDTUs = 0). <br> **Para que devo definir a garantia de eDTUs por banco de dados?** <br> Normalmente, a garantia de eDTU por banco de dados (MÍN. DE eDTUs) é definida como qualquer valor entre 0 e a ([utilização média por banco de dados]). A garantia eDTU por banco de dados é uma configuração global que define a garantia de eDTUs para todos os bancos de dados no pool. |
| **MÁX. DE eDTUs** – limite de eDTUs por banco de dados | O MÁX. DE eDTUs por banco de dados é o número máximo de eDTUs que podem ser usados por um único banco de dados no pool. Defina o limite de eDTUs por banco de dados com um valor alto o suficiente para lidar com picos máximos que seus bancos de dados podem enfrentar. Você pode definir esse limite até o limite do sistema, o que depende da camada de preços do pool (1.000 eDTUs para Premium). O tamanho específico deste limite deve acomodar padrões de pico de utilização de bancos de dados dentro do grupo. Espera-se algum grau de sobrecarga do grupo, uma vez que o pool normalmente assume padrões de uso dos bancos de dados com altos e baixos, em que todos os bancos de dados não atingem um pico simultaneamente.<br> **Com qual valor eu devo definir o limite de eDTUs por banco de dados?** <br> Defina o MÁX. DE eDTUs ou limite de eDTUs por banco de dados para ([pico de utilização do banco de dados]). Por exemplo, suponha que o pico de utilização por banco de dados seja de 50 DTUs e apenas 20% dos 100 bancos de dados no grupo atinjam o pico simultaneamente. Se o limite de eDTUs por banco de dados for definido como 50 eDTUs, será razoável sobrecarregar o grupo em cinco vezes e definir a garantia de eDTUs para o grupo (eDTUs do POOL) como 1.000 eDTUs. Também vale a pena observar que o limite de eDTUs não é uma garantia de recursos para um banco de dados, mas sim um teto de eDTUs que pode ser atingido se estiver disponível. |


## Adicionar bancos de dados em um pool, e remover bancos de dados elásticos de um pool

Depois que o pool for criado, você poderá adicionar ou remover bancos de dados dentro e fora do pool, marcando ou desmarcando bancos de dados na página **Adicionar Bancos de Dados**.

*Depois de criar um pool você também pode usar o Transact-SQL para criar um novo banco de dados elástico no pool, e mover bancos de dados existentes dentro e fora de um pool. Para saber mais detalhes, consulte [Referência de pool de banco de dados elástico - Transact-SQL](sql-database-elastic-pool-reference.md#Transact-SQL).*


## Monitorar e gerenciar um pool de banco de dados elástico

Depois de criar um pool de banco de dados elástico, você pode monitorar e gerenciar o pool no portal navegando até a lista de pools existentes e selecionando o pool desejado.

Depois de criar um pool, você pode:

- Selecione **Configurar pool** para alterar as configurações de eDTU do pool e de eDTU por banco de dados.
- Selecionar **Criar trabalho** e gerenciar os bancos de dados no pool, criando trabalhos elásticos. Trabalhos elásticos facilitam a execução de scripts Transact-SQL com relação a qualquer quantidade de bancos de dados no pool. Para saber mais, confira [Visão geral sobre os trabalhos elásticos de banco de dados](sql-database-elastic-jobs-overview.md).
- Selecionar **Gerenciar trabalhos** para administrar os trabalhos elásticos existentes.



![Monitorar um pool elástico][8]




![Monitorar um pool elástico][4]

Ao selecionar um pool existente, você pode ver a utilização de recursos do pool. Clique no gráfico **Utilização de Recursos** para abrir a folha **Métrica** na qual é possível personalizar o gráfico e configurar alertas.


![utilização de recursos][6]

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
[1]: ./media/sql-database-elastic-pool-portal/new-elastic-pool.png
[2]: ./media/sql-database-elastic-pool-portal/configure-elastic-pool.png
[3]: ./media/sql-database-elastic-pool-portal/configure-performance.png
[4]: ./media/sql-database-elastic-pool-portal/monitor-elastic-pool.png
[5]: ./media/sql-database-elastic-pool-portal/add-databases.png
[6]: ./media/sql-database-elastic-pool-portal/metric.png
[7]: ./media/sql-database-elastic-pool-portal/edit-chart.png
[8]: ./media/sql-database-elastic-pool-portal/configure-pool.png
[9]: ./media/sql-database-elastic-pool-portal/pricing-tier.png
[10]: ./media/sql-database-elastic-pool-portal/star.png

<!---HONumber=August15_HO9-->