<properties
	pageTitle="Monitorar e gerenciar um pool de banco de dados elástico com o Portal do Azure | Microsoft Azure"
	description="Saiba como usar o Portal do Azure e a inteligência interna do Banco de Dados SQL para gerenciar, monitorar e dimensionar corretamente um pool de banco de dados elástico escalonável para otimizar o desempenho do banco de dados e gerenciar o custo."
	keywords=""
	services="sql-database"
	documentationCenter=""
	authors="ninarn"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="05/09/2016"
	ms.author="ninarn"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Monitorar e gerenciar um pool de banco de dados elástico com o Portal do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)


Você pode usar o Portal do Azure para monitorar e gerenciar um pool de banco de dados elástico e os bancos de dados no pool. No portal, você pode monitorar a utilização de um pool elástico e os bancos de dados no pool. Você também pode criar um conjunto de alterações para o pool elástico e enviar todas as alterações ao mesmo tempo. Essas alterações incluem adicionar ou remover bancos de dados, alterar as configurações de pool elástico ou alterar suas configurações de banco de dados.

O gráfico a seguir mostra um exemplo do pool elástico. A exibição inclui:

*  Gráficos para monitorar o uso do recurso de pool elástico e os bancos de dados contidos no pool. 
*  O botão do pool **Configurar** para fazer alterações ao pool elástico. 
*  O botão **Criar banco de dados** que cria um novo banco de dados e o adiciona ao pool elástico atual.
*  Trabalhos elásticos que ajudam a gerenciam muitos bancos de dados executando scripts Transact SQL em todos os bancos de dados em uma lista. 

![Exibição de pool][2]

Para trabalhar com as etapas neste artigo, você precisará de um SQL Server no Azure com pelo menos um banco de dados e um pool elástico. Se você tiver um pool elástico, consulte [criar um pool](sql-database-elastic-pool-create-portal.md). Se não tiver um banco de dados, consulte o [Tutorial de banco de dados SQL](sql-database-get-started.md).

## Monitoramento de pool elástico

Você pode ir para um determinado pool para ver sua utilização de recursos. Por padrão, o pool está configurado para mostrar o uso de armazenamento e de eDTU na última hora. O gráfico pode ser configurado para mostrar métricas diferentes em vários períodos.

1. Selecionar um pool com que trabalhar.
2. Em **Monitoramento de Pool Elástico** é um gráfico de rotulado **Utilização de Recursos**. Clique no gráfico.

	![Monitoramento de pool elástico][3]

	A folha **métrica** abrirá, mostrando uma exibição detalhada das métricas especificadas durante o período determinado.

	![Lâmina Métrica][9]

### Para personalizar a exibição do gráfico

Você pode editar o gráfico e a folha de métrica para exibir outras métricas como percentual da CPU, percentual de E/S de dados e percentual de E/S de log usado.
 
2. Na folha de métricas, clique em **Editar**.

	![Clique em editar][6]

- Na folha **Editar Gráfico**, selecione um novo intervalo (última hora, hoje ou semana passada) ou clique em **personalizado** para selecionar qualquer intervalo de datas nas duas últimas semanas. Selecione o tipo de gráfico (barra ou linhas) e selecione os recursos a serem monitorados. 

	![Clique em editar](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

- Em seguida, clique em **OK**.


## Monitoramento de banco de dados elástico

Bancos de dados individuais também podem ser monitorados para identificar potenciais problemas.

1. Em **Monitoramento de Banco de Dados Elástico**, há um gráfico que exibe as métricas para cinco bancos de dados. Por padrão, o gráfico exibe os 5 principais bancos de dados no pool por uso médio de eDTU na última hora. Clique no gráfico.

	![Monitoramento de pool elástico][4]

2. A folha **Utilização de Recursos do Banco de Dados** é exibida. Isso fornece uma exibição detalhada do uso do banco de dados no pool. Usando a grade na parte inferior da folha, você pode selecionar qualquer bancos de dados no pool para exibir seu gráfico de uso (até 5 bancos de dados). Você também pode personalizar a janela de métricas e tempo exibida no gráfico clicando em **Editar gráfico**.

	![Folha Utilização de Recursos do Banco de Dados][8]

### Para personalizar a exibição

1. Na folha **Utilização de recursos do banco de dados**, clique em **Editar gráfico**.

	![Clique em Editar gráfico](./media/sql-database-elastic-pool-manage-portal/db-utilization-blade.png)

2. Na folha **Editar**, selecione um novo intervalo de tempo (como última hora ou 24 horas) ou clique em **personalizado** para definir um período diferente das duas últimas semanas.

	![Clique em Personalizar](./media/sql-database-elastic-pool-manage-portal/editchart-date-time.png)

3. Clique na lista suspensa **Comparar bancos de dados** para selecionar uma métrica diferente a ser usada ao comparar os bancos de dados.

	![Edite o gráfico](./media/sql-database-elastic-pool-manage-portal/edit-comparison-metric.png)

### Para selecionar os bancos de dados a serem monitorados

Na lista de banco de dados da folha **Utilização de Recursos do Banco de Dados**, você pode localizar bancos de dados específicos examinando as páginas na lista ou digitando o nome de um banco de dados. Use a caixa de seleção para escolher o banco de dados.

![Pesquisar por bandos de dados a serem monitorados][7]
  

## Adicionar um alerta a um recurso de pool

Você pode adicionar regras a recursos que enviam emails para as pessoas ou cadeias de caracteres de alerta para pontos de extremidade da URL quando o recurso atingir o limite de utilização que você configurou.

**Para adicionar um alerta a qualquer recurso:**

1. Clique no gráfico **Utilização de recursos** para abrir a folha **Métrica**, clique em **Adicionar alerta** e preencha as informações na folha **Adicionar uma regra de alerta** (**Recurso** é configurado automaticamente para ser o pool com que você está trabalhando).
2. Digite um **Nome** e **Descrição** que identifiquem o alerta para você e os destinatários.
3. Escolha na lista uma **Métrica** segundo a qual deseja gerar o alerta.

    O gráfico mostra dinamicamente a utilização de recursos para a métrica para ajudá-lo a escolher um limite.

4. Escolha uma **Condição** (maior que, menor que, etc.) e um **Limite**.
5. Clique em **OK**.



## Mover um banco de dados para um pool elástico

Você pode adicionar ou remover bancos de dados de um pool existente. Os bancos de dados podem estar em outros pools. No entanto, você só pode adicionar bancos de dados que estão no mesmo servidor lógico.

1. Na folha do pool, em **Bancos de dados elásticos**, clique em **Configurar pool**.

    ![Clique em Configurar pool][1]

2. Na folha **Configurar pool**, clique em **Adicionar ao pool**.

	![Clique em Adicionar ao pool](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)

	
3. Na folha **Adicionar bancos de dados**, selecione o banco de dados ou os bancos de dados para adicionar ao pool. Em seguida, clique em **Selecionar**.

	![Selecione os bancos de dados a serem adicionados](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)

    Agora, a folha **Configurar pool** lista o banco de dados que você selecionou para adicionar, com o status definido como **Pendente**.

    ![Adições de pool pendentes](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)

3. Na folha **Configurar pool**, clique em **Salvar**.

    ![Clique em Salvar](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## Remover um banco de dados de um pool elástico

1. Na folha **Configurar pool**, selecione os bancos de dados a serem removidos.

    ![lista de bancos de dados](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)

2. Clique em **Remover do pool**.

    ![lista de bancos de dados](./media/sql-database-elastic-pool-manage-portal/click-remove.png)

    Agora, a folha **Configurar pool** lista o banco de dados que você selecionou para remoção, com o status definido como **Pendente**.
    
    ![visualização de adição ou remoção de banco de dados](./media/sql-database-elastic-pool-manage-portal/pending-removal.png)

3. Na folha **Configurar pool**, clique em **Salvar**.

    ![Clique em Salvar](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## Alterar as configurações de desempenho de um pool

À medida que você monitora a utilização de recursos de um pool, poderá descobrir que alguns ajustes são necessários. Talvez o pool precise de uma alteração nos limites de armazenamento ou de desempenho. Pode ser útil alterar as configurações de banco de dados no pool. Você pode alterar a configuração do pool a qualquer momento para obter o melhor equilíbrio entre desempenho e custo. Para obter mais informações, consulte [Quando um Pool de Banco de Dados Elástico deve ser usado?](sql-database-elastic-pool-guidance.md)

**Para alterar as eDTUs ou os limites de armazenamento por pool e as eDTUs por banco de dados:**

1. Abra a folha **Configurar pool**.

    Em **Configurações do pool de banco de dados elástico**, use os controles deslizantes de qualquer lado para alterar as configurações do pool.

    ![Utilização de recursos de pool elástico](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. Quando a configuração for alterada, a exibição mostrará o custo mensal estimado da alteração.

    ![Atualização de um pool e o novo custo mensal](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)


## Criar e gerenciar trabalhos elásticos

Trabalhos elásticos facilitam a execução de scripts Transact-SQL em qualquer quantidade de bancos de dados no pool. Você pode criar novos trabalhos ou gerenciar os trabalhos existentes usando o portal.

![Criar e gerenciar trabalhos elásticos][5]


Antes de usar os trabalhos, instale os componentes de trabalhos elásticos e forneça suas credenciais. Para saber mais, confira [Visão geral sobre os trabalhos elásticos de banco de dados](sql-database-elastic-jobs-overview.md).

Consulte [Escalando horizontalmente com o Banco de Dados SQL do Azure](sql-database-elastic-scale-introduction.md): use as ferramentas de banco de dados elástico para escalar horizontalmente, mover os dados e consultar ou criar transações.



## Recursos adicionais

- [Pool elástico de Banco de Dados SQL](sql-database-elastic-pool.md)
- [Criar um pool de banco de dados elástico com o portal](sql-database-elastic-pool-create-csharp.md)
- [Criar um pool de banco de dados elástico com o PowerShell](sql-database-elastic-pool-create-powershell.md)
- [Criar um pool de banco de dados elástico com o C#](sql-database-elastic-pool-create-csharp.md)
- [Considerações de preço e desempenho para um pool de banco de dados elástico](sql-database-elastic-pool-guidance.md)


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-manage-portal/configure-pool.png
[2]: ./media/sql-database-elastic-pool-manage-portal/basic.png
[3]: ./media/sql-database-elastic-pool-manage-portal/basic-2.png
[4]: ./media/sql-database-elastic-pool-manage-portal/basic-3.png
[5]: ./media/sql-database-elastic-pool-manage-portal/elastic-jobs.png
[6]: ./media/sql-database-elastic-pool-manage-portal/edit-metric.png
[7]: ./media/sql-database-elastic-pool-manage-portal/select-dbs.png
[8]: ./media/sql-database-elastic-pool-manage-portal/db-utilization.png
[9]: ./media/sql-database-elastic-pool-manage-portal/metric.png

<!---HONumber=AcomDC_0511_2016-->