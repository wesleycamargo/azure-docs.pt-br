<properties 
	pageTitle="Criar e gerenciar um pool elástico de bancos de dados de Banco de Dados SQL (visualização)" 
	description="Criar um pool de recursos único para compartilhar em um grupo de Bancos de Dados SQL do Azure." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="04/29/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# Criar e gerenciar um pool elástico do Banco de Dados SQL (visualização)

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-portal.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)

Este artigo mostra como criar um pool elástico com o [Portal do Azure](https://portal.azure.com).

Os pools elásticos simplificam o processo de criação, manutenção e gerenciamento do desempenho e do custo de uma grande quantidade de bancos de dados.
 

> [AZURE.NOTE]No momento, os pools elásticos estão em versão de visualização e disponíveis apenas com Servidores V12 do Banco de Dados SQL.

 


## Pré-requisitos

Para criar um pool elástico, você precisa do seguinte:

- Uma assinatura do Azure. Se você precisar de uma assinatura do Azure basta clicar em AVALIAÇÃO GRATUITA na parte superior desta página e, em seguida, voltar para concluir este artigo.
- Um servidor do Banco de Dados SQL versão 12. Se você não tiver um servidor V12, crie um seguindo as etapas: [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md).



## Criar um pool elástico

Crie um pool elástico adicionando um novo pool a um servidor. Você pode adicionar vários pools a um servidor, mas apenas um servidor pode ser associado a cada pool. Além disso, é possível adicionar todos os bancos de dados em um servidor, ou alguns deles, a um pool.


1.	Selecione um servidor V12 de Banco de Dados SQL que contenha os bancos de dados que você deseja adicionar ao pool.
2.	Crie o pool selecionando **adicionar pool** na parte superior da folha **SQL Server**.

   ![Criar Pool Elástico][1]

## Configurar um pool elástico

Configure o pool definindo a camada de preços, adicionando bancos de dados e configurando as características de desempenho do pool.

*Ao selecionar o comando **Adicionar pool** você precisa aceitar os termos da versão de visualização selecionando **TERMOS DA VISUALIZAÇÃO** e preenchendo a folha **Termos da Visualização**. Basta aceitar uma vez os termos de cada assinatura.*

   ![Configurar pool elástico][2]


### Camada de preços

A camada de preços de um pool elástico é um pouco parecida com a camada de serviços do banco de dados SQL. A camada de preços determina os recursos disponíveis para os bancos de dados flexível no pool, o número máximo de DTUs (MÁX. DE DTUs) e armazenamento (GB) disponível para cada banco de dados.

> [AZURE.NOTE]Atualmente, a versão de visualização está limitada à camada de preços **Padrão**.

| Camada de Preços | MÁX. DE DTUs por banco de dados |
| :--- | :--- |
| Padrão | MÁX. de 100 DTUs por banco de dados |

### Adicionar bancos de dados

Você pode selecionar, a qualquer momento, os bancos de dados específicos para inclusão no pool. Quando você cria um novo pool, o Azure recomenda os bancos de dados que se beneficiarão por estarem em um pool e os marca para inclusão. Você pode adicionar todos os bancos de dados disponíveis no servidor, ou marcar ou desmarcar os bancos de dados na lista inicial como desejado.

   ![Adicionar bancos de dados][5]

Ao selecionar um banco de dados para adição a um pool, as condições a seguir devem ser atendidas:

- O pool deve ter espaço para o banco de dados (não pode conter o número máximo de bancos de dados). Mais especificamente, o pool deve ter DTUs suficientes disponíveis para cobrir a garantia de DTUs por banco de dados (por exemplo, se a garantia de DTUs para o grupo for de 400, e a garantia de DTUs para cada banco de dados for de 10, o número máximo de bancos de dados permitidos no pool será de 40 (400 DTUs/10 DTUs garantidos por banco de dados = Máx. de 40 bancos de dados).
- Os recursos atuais usados pelo banco de dados devem estar disponíveis no pool. 


### Configurar o desempenho

Você pode configurar o desempenho do pool definindo os parâmetros de desempenho para o pool e para os bancos de dados elásticos no pool. Lembre-se de que as **configurações do banco de dados elástico** se aplicam a todos os bancos de dados no pool.

   ![Configurar Pool Elástico][3]

Três parâmetros podem ser configurados para definir o desempenho do pool; a Garantia de DTUs para o pool, o MÍN. DE DTUs e MÁX. DE DTUs para os bancos de dados elásticos no pool. A tabela a seguir descreve cada um deles e fornece orientações sobre como configurá-los.

| Parâmetro de desempenho | Descrição |
| :--- | :--- |
| **DTU DE POOL/GB** - garantia de DTUs para o pool | A garantia de DTUs para o pool é o número garantido de DTUs disponíveis e compartilhados por todos os bancos de dados no pool. <br> Atualmente, você pode definir esse valor como 200, 400, 800 ou 1200. <br> O tamanho específico da garantia de DTUs de um grupo deve ser fornecido levando em consideração a utilização histórica de DTUs do grupo. Como alternativa, esse tamanho pode ser definido pela garantia desejada de DTUs por banco de dados e pela utilização de bancos de dados ativos simultaneamente. A garantia de DTUs para o pool também está correlacionada à quantidade de armazenamento disponível para o pool. Para cada DTU alocado para o pool, você obtém 1 GB de armazenamento no banco de dados (1 DTU = 1 GB de armazenamento). <br> **Com qual valor eu devo definir a garantia de DTUs do pool?** <br>No mínimo, você deve definir a garantia de DTUs do pool como ([Nº de bancos de dados] x [utilização média de DTUs por banco de dados]) |
| **MÍN. DE DTUs** - garantia de DTUs para cada banco de dados | A garantia de DTUs por banco de dados é o número de DTUs garantidos para um único banco de dados no pool. No momento, você pode definir essa garantia como 0, 10, 20 ou 50 DTUs, ou você pode optar por não fornecer uma garantia aos bancos de dados no grupo (MÍN. DE DTUs = 0). <br> **Com qual valor eu devo definir a garantia de DTUs por banco de dados?** <br> No mínimo, você deve definir a garantia de DTUs por banco de dados (MÍN. DE DTUs) de acordo com a ([utilização média por banco de dados]). A garantia DTU por banco de dados é uma configuração global que define a garantia de DTUs para todos os bancos de dados no pool. |
| **MÁX. DE DTUs** - Limite de DTUs por banco de dados | O MÁX. DE DTUs por banco de dados é o número máximo de DTUs que podem ser usados por um único banco de dados no pool. Defina o limite de DTUs por banco de dados com um valor alto o suficiente para lidar com picos máximos que seus bancos de dados podem enfrentar. Você pode definir esse limite até o limite do sistema, o que depende da camada de preços do pool (100 DTUs para Padrão). O tamanho específico deste limite deve acomodar padrões de pico de utilização de bancos de dados dentro do grupo. Espera-se algum grau de sobrecarga do grupo, uma vez que o pool normalmente assume padrões de uso dos bancos de dados com altos e baixos, em que todos os bancos de dados não atingem um pico simultaneamente.<br> **Com qual valor eu devo definir o limite de DTUs por banco de dados?** <br> Defina o MÁX. DE DTUs, ou limite de DTUs por banco de dados, como ([pico de utilização de banco de dados]). Por exemplo, suponha que o pico de utilização por banco de dados seja de 50 DTUs e apenas 20% dos 100 bancos de dados no grupo atinjam o pico simultaneamente. Se o limite de DTUs por banco de dados for definido como 50 DTUs, será razoável sobrecarregar o grupo em cinco vezes e definir a garantia de DTUs para o grupo como 1000 DTUs. Também vale a pena observar que o limite de DTUs não é uma garantia de recursos para um banco de dados, mas sim um teto de DTUs que pode ser atingido se estiver disponível. |


## Adicionando bancos de dados a um pool elástico

Após a criação do pool, você pode adicionar ou remover bancos de dados do pool


## Monitorar e gerenciar um pool elástico

Depois de criar um pool elástico, você pode monitorar e gerenciar o pool no portal navegando até a lista de pools existentes e selecionando o pool desejado.

Depois de criar um pool, você pode:

- Selecionar **Configurar pool** para alterar as configurações de DTU do pool e de DTU por banco de dados.
- Selecionar **Criar trabalho** e gerenciar os bancos de dados no pool, criando trabalhos elásticos. Trabalhos elásticos facilitam a execução de scripts T-SQL em qualquer quantidade de bancos de dados no pool. Para saber mais, confira [Visão geral sobre os trabalhos elásticos de banco de dados](sql-database-elastic-jobs-overview.md).
- Selecionar **Gerenciar trabalhos** para administrar os trabalhos elásticos existentes.



![Monitorar um pool elástico][8]




![Monitorar um pool elástico][4]

Ao selecionar um pool existente, você pode ver a utilização de recursos do pool. Clique no gráfico **Utilização de Recursos** para abrir a folha **Métrica** na qual é possível personalizar o gráfico e configurar alertas.


![utilização de recursos][6]

Clique em **Editar gráfico** para adicionar parâmetros de modo que você possa exibir facilmente os dados de telemetria do pool.


![editar gráfico][7]







## Próximas etapas
Após a criação de um pool elástico, você pode gerenciar os bancos de dados no pool criando trabalhos elásticos. Trabalhos elásticos facilitam a execução de scripts T-SQL em qualquer quantidade de bancos de dados no pool.

Para saber mais, confira [Visão geral sobre os trabalhos elásticos de banco de dados](sql-database-elastic-jobs-overview.md).



## Relacionados

- [Pool elástico de Banco de Dados SQL](sql-database-elastic-pool.md)
- [Criar um pool elástico de Banco de Dados SQL com o PowerShell](sql-database-elastic-pool-powershell.md)
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

<!---HONumber=58-->