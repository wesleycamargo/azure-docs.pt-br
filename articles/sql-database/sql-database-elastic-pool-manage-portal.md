<properties
	pageTitle="Monitorar, gerenciar e dimensionar um pool de banco de dados elástico"
	description="Saiba como usar o Portal do Azure e a inteligência interna do Banco de Dados SQL para gerenciar, monitorar e dimensionar corretamente um pool de banco de dados elástico escalonável para otimizar o desempenho do banco de dados e gerenciar o custo."
	keywords=""
	services="sql-database"
	documentationCenter=""
	authors="jeffgoll"
	manager="jeffreyg"
	editor="cgronlun"/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="03/18/2016"
	ms.author="jeffreyg"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Monitorar, gerenciar e dimensionar um pool de banco de dados elástico com o Portal do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-elastic-pool-manage-portal.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)


Este artigo descreve como usar o Portal do Azure para monitorar, gerenciar e planejar o tamanho de um pool de banco de dados elástico e de bancos de dados no pool. O Banco de Dados SQL conta com inteligência interna que analisa a telemetria do histórico de uso e recomenda de forma proativa um pool para bancos de dados quando essa opção é mais econômica. Você também pode adicionar e remover bancos de dados antes de confirmar as alterações e ver o impacto sobre o armazenamento e o desempenho do pool.

Para trabalhar com as etapas neste artigo, você precisará de bancos de dados e um pool. Consulte [criar um pool](sql-database-elastic-pool-create-portal.md) se você já tiver bancos de dados e o [Tutorial do Banco de Dados SQL](sql-database-get-started) se não tiver.

**Escolha um pool com que trabalhar:**

- No [Portal do Azure](https://portal.azure.com), clique em **Procurar**, clique em **Pools elásticos SQL** e clique no pool com que você deseja trabalhar na lista.

##Monitorar a utilização de recursos de um pool
Depois de selecionar um pool com que trabalhar, em **Monitoramento de Pool Elástico**, blocos dinâmicos e um gráfico mostram as informações de utilização importantes para o pool.

![Monitorar um pool elástico](./media/sql-database-elastic-pool-manage-portal/monitor-elastic-pool.png)

**Para alterar o intervalo de datas, o tipo de gráfico (barra ou linha) ou os recursos mostrados:**

- Clique em **Editar**, escolha as configurações que você deseja e clique em **Salvar** para atualizar o gráfico.

**Para alterar os blocos online:**

- Clique em **Adicionar blocos** e selecione os blocos desejados na galeria de blocos que aparece à esquerda.

##Adicionar um alerta a um recurso de pool
Você pode adicionar regras a recursos que enviam emails para as pessoas ou cadeias de caracteres de alerta para pontos de extremidade da URL quando o recurso atingir o limite de utilização que você configurou.

**Para adicionar um alerta a qualquer recurso:**

1. Clique no gráfico **Utilização de recursos** para abrir a folha **Métrica**, clique em **Adicionar alerta** e preencha as informações na folha **Adicionar uma regra de alerta** (**Recurso** é configurado automaticamente para ser o pool com que você está trabalhando).
2. Digite um **Nome** e **Descrição** que identifiquem o alerta para você e os destinatários.
3. Escolha uma **Métrica** que você deseja alertar da lista.

    O gráfico mostra dinamicamente a utilização de recursos para a métrica para ajudá-lo a escolher um limite.

4. Escolha uma **Condição** (maior que, menor que, etc.) e um **Limite**.
5. Clique em **OK**.

##Alterar eDTU por pool e eDTU do banco de dados
Quando vir a utilização de recursos de um pool, você pode descobrir que o pool precisa de uma configuração diferente de eDTU ou que bancos de dados individuais no pool precisam de configurações de eDTU diferentes. Você pode alterar a configuração do pool a qualquer momento para obter o melhor equilíbrio entre desempenho e custo. Consulte [Considerações de preço e desempenho](sql-database-elastic-pool-guidance.md) para obter mais informações.

**Para alterar os eDTU do pool e eDTU por banco de dados:**

1. Clique em **Configurar pool** para abrir a folha **Configurar desempenho**.

    Em **Configurações do pool de banco de dados elástico**, um gráfico mostra a tendência recente de utilização de armazenamento e eDTU em porcentagem da capacidade do pool.

    ![Utilização de recursos de pool elástico](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. Clique em um **eDTU do Pool** diferente e você verá o custo mensal estimado da alteração que deseja fazer, e o gráfico será atualizado para mostrar os valores previstos de utilização com o novo eDTU máximo selecionado.

    ![Atualização de um pool e o novo custo mensal](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)

3. Em **Configuração de banco de dados elástico**, um gráfico de barras mostra a utilização de eDTU de cada banco de dados no pool.

4. Clique em **eDTU máximo** para definir o máximo e **eDTU mínimo** para definir o número mínimo de eDTUs para os bancos de dados no pool.

    ![Atualizando o eDTU mínimo e máximo de um banco de dados elástico](./media/sql-database-elastic-pool-manage-portal/change-db-edtuminmax.png)

##Adicionar e remover bancos de dados

Após criar um pool, você pode adicionar bancos de dados ao pool ou removê-los. Você só pode adicionar bancos de dados no mesmo SQL Server.

**Para adicionar bancos de dados:**

1. Na folha do pool, em **Bancos de dados elásticos**, clique no link que mostra o número de bancos de dados no pool.

    ![lista de bancos de dados](./media/sql-database-elastic-pool-manage-portal/db-listing.png)

2. Na folha **Bancos de dados elásticos**, clique em **Adicionar banco de dados**, clique nos bancos de dados que você deseja adicionar, em seguida, clique no botão **Selecionar**.

    A folha **Bancos de dados elásticos** agora lista o banco de dados que você acabou de adicionar, com **AVG DTU** e a utilização de armazenamento como **TAMANHO (GB)**, com um status **Pendente**. Os valores de utilização do pool agora mostram qual será o **Novo** valor se você salvar as alterações.

    ![pool recomendado](./media/sql-database-elastic-pool-manage-portal/add-remove-databases.png)

3. Clique em **Salvar** e, em seguida, clique em **OK** quando o portal informar que a solicitação foi enviada. O número de bancos de dados no pool aparece na folha para o pool quando a operação for concluída.

**Para remover bancos de dados:**

1. Na folha do pool, em **Bancos de dados elásticos**, clique no link que mostra o número de bancos de dados no pool.

    ![lista de bancos de dados](./media/sql-database-elastic-pool-manage-portal/db-listing.png)

2. Na folha **Bancos de dados elásticos**, na lista de bancos de dados no pool, clique nos bancos de dados que você deseja remover e clique em **Remover bancos de dados**.

    Os valores de utilização do pool agora mostram qual será o **Novo** valor se você salvar as alterações.

3. Clique em **Salvar** e, em seguida, clique em **OK** quando o portal informar que a solicitação foi enviada. O número de bancos de dados no pool aparece na folha para o pool quando a operação for concluída.

## Criar um novo servidor de banco de dados em um pool

Na folha de banco de dados, basta clicar em **Criar banco de dados**. O banco de dados SQL já está configurado para o servidor e o pool corretos, portanto prossiga definindo outras configurações e clique em **OK** para criar um novo banco de dados no pool.

   ![criar banco de dados elástico](./media/sql-database-elastic-pool-portal/create-database.png)

##Criar e gerenciar trabalhos elásticos

Trabalhos elásticos facilitam a execução de scripts Transact-SQL em qualquer quantidade de bancos de dados no pool. Antes de usar os trabalhos, instale os componentes de trabalhos elásticos e forneça suas credenciais. Para saber mais, confira [Visão geral sobre os trabalhos elásticos de banco de dados](sql-database-elastic-jobs-overview.md).

## Recursos adicionais

- [Referência de banco de dados elástico](sql-database-elastic-pool-reference.md)
- [Pool elástico de Banco de Dados SQL](sql-database-elastic-pool.md)
- [Criar um pool de banco de dados elástico com o portal](sql-database-elastic-pool-create-csharp.md)
- [Criar um pool de banco de dados elástico com o PowerShell](sql-database-elastic-pool-create-powershell.md)
- [Criar um pool de banco de dados elástico com o C#](sql-database-elastic-pool-create-csharp.md)
- [Considerações de preço e desempenho para um pool de banco de dados elástico](sql-database-elastic-pool-guidance.md)

<!-----------HONumber=AcomDC_0330_2016-->