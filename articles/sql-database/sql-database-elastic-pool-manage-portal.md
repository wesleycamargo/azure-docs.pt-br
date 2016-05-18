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
	ms.date="05/02/2016"
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


Você pode usar o Portal do Azure para monitorar, gerenciar e configurar um pool de banco de dados elástico e os bancos de dados no pool. O Banco de Dados SQL conta com inteligência interna que analisa o uso histórico de todos os bancos de dados em um servidor (estejam eles em pools ou não) e recomenda de forma proativa um pool para bancos de dados quando essa opção é mais econômica.

O portal permite alterar as configurações do pool e do banco de dados, visualizar as alterações e confirmar todas as alterações simultaneamente. Você pode visualizar alterações como a adição ou a remoção de bancos de dados. O efeito potencial sobre o desempenho e os preços também serão exibidos.

Para realizar as etapas neste artigo, você precisará de alguns bancos de dados e de um pool. Se você tiver bancos de dados existentes, consulte [criar um pool](sql-database-elastic-pool-create-portal.md). Se não tiver um banco de dados, consulte o [tutorial do banco de dados SQL](sql-database-get-started.md).

## Selecionar um pool com que trabalhar

1. No [Portal do Azure](https://portal.azure.com), clique em **Procurar**
2. Clique em **pools elásticos do SQL**
3. Na lista, clique no pool com que deseja trabalhar.

## Mover um banco de dados para um pool elástico

Você pode adicionar ou remover bancos de dados de um pool existente. Os bancos de dados podem estar em outros pools. No entanto, você só pode adicionar bancos de dados que estão no mesmo servidor lógico.

1. Na folha do pool, em **Bancos de dados elásticos**, clique em **Configurar pool**.

    ![Clique em Configurar pool][1]

2. Na folha **Configurar pool**, clique em **Adicionar ao pool**.

	![Clique em Adicionar ao pool](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)

	
3. Na folha **Adicionar bancos de dados**, selecione o banco de dados ou os bancos de dados para adicionar ao pool. Em seguida, clique em **Selecionar**.

	![Selecione os bancos de dados a serem adicionados](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)

    Agora, a folha **Configurar pool** lista o banco de dados que você acabou de adicionar, com o status definido como **Pendente**.

    ![Adições de pool pendentes](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)

3. Na folha "Configurar pool", clique em **Salvar**.

    ![Clique em Salvar](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## Remover um banco de dados de um pool elástico

1. Na folha **Configurar pool**, selecione o banco de dados ou os bancos de dados a serem removidos.

    ![lista de bancos de dados](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)

2. Clique em **Remover do pool**.

    ![lista de bancos de dados](./media/sql-database-elastic-pool-manage-portal/remove-from-pool.png)

	Os bancos de dados selecionados aparecem na interface do usuário como "bancos de dados selecionados para serem removidos".


## Monitorar a utilização de recursos de um pool


1. Selecionar um pool com que trabalhar.
2. Em **Monitoramento de Pool Elástico**, blocos dinâmicos e um gráfico mostram as informações importantes de utilização do pool.

![Monitorar um pool elástico](./media/sql-database-elastic-pool-manage-portal/monitor-elastic-pool.png)

**Para alterar o gráfico e exibir:**

- Clique em **Editar**.

	![Clique em editar](./media/sql-database-elastic-pool-manage-portal/edit-resource-utlization.png)

- Na folha **Editar gráfico**, selecione um novo intervalo de tempo (última hora, hoje ou última semana) ou clique em **personalizado** para definir um período diferente. Selecione o tipo de gráfico (barra ou linhas) e selecione os recursos a serem monitorados.

	![Clique em editar](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

- Em seguida, clique em **OK**.


## Adicionar um alerta a um recurso de pool

Você pode adicionar regras a recursos que enviam emails para as pessoas ou cadeias de caracteres de alerta para pontos de extremidade da URL quando o recurso atingir o limite de utilização que você configurou.

**Para adicionar um alerta a qualquer recurso:**

1. Clique no gráfico **Utilização de recursos** para abrir a folha **Métrica**, clique em **Adicionar alerta** e preencha as informações na folha **Adicionar uma regra de alerta** (**Recurso** é configurado automaticamente para ser o pool com que você está trabalhando).
2. Digite um **Nome** e **Descrição** que identifiquem o alerta para você e os destinatários.
3. Escolha na lista uma **Métrica** segundo a qual deseja gerar o alerta.

    O gráfico mostra dinamicamente a utilização de recursos para a métrica para ajudá-lo a escolher um limite.

4. Escolha uma **Condição** (maior que, menor que, etc.) e um **Limite**.
5. Clique em **OK**.

## Alterar as configurações de desempenho de um pool

Conforme monitora a utilização de recursos de um pool, você pode descobrir que o pool precisa de mais eDTUs. Ou que bancos de dados individuais no pool precisam de configurações de eDTU diferentes. Você pode alterar a configuração do pool a qualquer momento para obter o melhor equilíbrio entre desempenho e custo. Para obter mais informações, consulte [Quando um Pool de Banco de Dados Elástico deve ser usado?](sql-database-elastic-pool-guidance.md)

**Para alterar as eDTUs por pool e as eDTUs por banco de dados:**

1. Abra a folha **Configurar pool**.

    Em **Configurações do pool de banco de dados elástico**, use os controles deslizantes para alterar as configurações do pool.

    ![Utilização de recursos de pool elástico](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. Quando a configuração for alterada, a exibição mostrará o custo mensal estimado da alteração.

    ![Atualização de um pool e o novo custo mensal](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)

## Visualizar ações do banco de dados

Você pode visualizar a adição e remoção de bancos de dados antes de confirmar a ação na folha **Configurar pool**:

![visualização de adição ou remoção de banco de dados](./media/sql-database-elastic-pool-manage-portal/pools-tab.png).


## Criar e gerenciar trabalhos elásticos

Trabalhos elásticos facilitam a execução de scripts Transact-SQL em qualquer quantidade de bancos de dados no pool. Antes de usar os trabalhos, instale os componentes de trabalhos elásticos e forneça suas credenciais. Para saber mais, confira [Visão geral sobre os trabalhos elásticos de banco de dados](sql-database-elastic-jobs-overview.md).

Confira [Escalando horizontalmente com o Banco de Dados SQL do Azure](sql-database-elastic-scale-introduction.md): use ferramentas de banco de dados elástico para escalar horizontalmente, mover os dados, consultar ou criar transações.


## Recursos adicionais

- [Pool elástico de Banco de Dados SQL](sql-database-elastic-pool.md)
- [Criar um pool de banco de dados elástico com o portal](sql-database-elastic-pool-create-csharp.md)
- [Criar um pool de banco de dados elástico com o PowerShell](sql-database-elastic-pool-create-powershell.md)
- [Criar um pool de banco de dados elástico com o C#](sql-database-elastic-pool-create-csharp.md)
- [Considerações de preço e desempenho para um pool de banco de dados elástico](sql-database-elastic-pool-guidance.md)


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-manage-portal/configure-pool.png

<!---HONumber=AcomDC_0504_2016-->