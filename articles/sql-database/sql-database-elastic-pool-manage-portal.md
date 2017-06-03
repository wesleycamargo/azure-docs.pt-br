---
title: "Portal do Azure: criar e gerenciar um pool elástico do Banco de Dados SQL | Microsoft Docs"
description: "Saiba como usar o Portal do Azure e a inteligência interna do Banco de Dados SQL para gerenciar, monitorar e dimensionar corretamente um pool elástico escalonável para otimizar o desempenho do banco de dados e gerenciar o custo."
keywords: 
services: sql-database
documentationcenter: 
author: ninarn
manager: jhubbard
editor: cgronlun
ms.assetid: 3dc9b7a3-4b10-423a-8e44-9174aca5cf3d
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.date: 05/08/2017
ms.author: ninarn
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: 66f7cc63d311b6b5cd223fe0d8a9bf9f636abef1
ms.contentlocale: pt-br
ms.lasthandoff: 04/20/2017


---
# <a name="create-and-manage-an-elastic-pool-with-the-azure-portal"></a>Criar e gerenciar um pool elástico com o portal do Azure
Este tópico mostra como criar e gerenciar [pools elásticos](sql-database-elastic-pool.md) escalonáveis com o Portal do Azure. Você também pode criar e gerenciar um pool elástico do Azure com o [PowerShell](sql-database-elastic-pool-manage-powershell.md), a API REST ou [C#](sql-database-elastic-pool-manage-csharp.md). Você também pode criar e mover bancos de dados de e para os pools elásticos usando [Transact-SQL](sql-database-elastic-pool-manage-tsql.md).

## <a name="create-an-elastic-pool"></a>Criar um pool elástico 

Há duas maneiras de criar um pool elástico. É possível fazer isso do zero, se você souber a configuração de pool que deseja, ou começar com uma recomendação do serviço. O banco de dados SQL possui inteligência interna que recomenda uma configuração de um pool elástico, caso seja mais econômico com base na telemetria de uso anterior de seus bancos de dados.

Você pode criar vários pools em um servidor, mas não pode adicionar bancos de dados de servidores diferentes ao mesmo pool.

> [!NOTE]
> Os pools elásticos têm uma disponibilidade geral (GA) em todas as regiões do Azure, exceto na Índia Ocidental, onde atualmente estão em preview.  A GA dos pools elásticos nessa região ocorrerá assim que possível.
>
>

### <a name="step-1-create-an-elastic-pool"></a>Etapa 1: criar um pool elástico

Criar um pool elástico de uma folha de **servidor** existente no portal é a maneira mais fácil de mover os bancos de dados existentes para um pool elástico.

> [!NOTE]
> Você também pode criar um pool elástico pesquisando sobre **pool elástico do SQL** no **Marketplace** ou clicando em **+ Adicionar** na folha de procura de **pools elásticos do SQL**. Você pode especificar um servidor novo ou existente por este fluxo de trabalho de provisionamento de pool.
>
>

1. No [portal do Azure](http://portal.azure.com/), clique em **Mais serviços** **>** **Servidores SQL** e clique no servidor que contém os bancos de dados que deseja adicionar a um pool elástico.
2. Clique em **Novo pool**.

    ![Adicionar pool a um servidor](./media/sql-database-elastic-pool-create-portal/new-pool.png)

    **-OU-**

    Talvez você veja uma mensagem informando que há pools elásticos recomendados para o servidor. Clique na mensagem para ver os pools recomendados com base na telemetria de uso histórico do banco de dados e clique na camada para ver mais detalhes e personalizar o pool. Confira [Entender as recomendações de pool](#understand-pool-recommendations) mais adiante neste tópico para saber como a recomendação é feita.

    ![pool recomendado](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)

3. A folha do **pool elástico** aparece, que é onde você especifica as configurações para o pool. Se você clicou em **Novo pool** na etapa anterior, o tipo de preço é **Standard** por padrão e nenhum banco de dados é selecionado. Você pode criar um pool vazio ou especificar um conjunto de bancos de dados existentes do servidor para mover para o pool. Se você está criando um pool recomendado, o tipo de preço, as configurações de desempenho e a lista de bancos de dados recomendados são preenchidos automaticamente, mas você ainda pode alterá-los.

    ![Configurar pool elástico](./media/sql-database-elastic-pool-create-portal/configure-elastic-pool.png)

4. Especifique um nome para o pool elástico ou deixe o padrão.

### <a name="step-2-choose-a-pricing-tier"></a>Etapa 2: escolher um tipo de preço

O tipo de preço do pool determina os recursos disponíveis para os elásticos no pool, o número máximo de eDTUs (MÁX DE eDTUs) e o armazenamento (GBs) disponível para cada banco de dados. Para obter detalhes, consulte Camadas de serviço.

Para alterar o tipo de preço do pool, clique em **Tipo de preço**, clique no tipo desejado e em **Selecionar**.

> [!IMPORTANT]
> Depois de escolher o tipo de preço e confirmar suas alterações clicando em **OK** na última etapa, não será possível alterar o tipo de preço do pool. Para alterar o tipo de preço para um pool elástico existente, crie um pool elástico no tipo de preço desejado e migre os bancos de dados para esse pool novo.
>
>

![Selecione uma faixa de preço.](./media/sql-database-elastic-pool-create-portal/pricing-tier.png)

### <a name="step-3-configure-the-pool"></a>Etapa 3: configurar o pool

Depois de definir o tipo de preço, clique em Configurar pool, onde você adicionará bancos de dados, definirá eDTUs e armazenamento do pool (GBs do pool) e onde você definirá os eDTUs mínimo e máximo para os elásticos no pool.

1. Clique em **Configurar pool**
2. Selecione os bancos de dados que você deseja adicionar ao pool. Esta etapa é opcional durante a criação do pool. É possível adicionar bancos de dados após a criação do pool.
    Para adicionar bancos de dados, clique em Para adicionar bancos de dados, clique em **Adicionar banco de dados**, clique nos bancos de dados que você deseja adicionar e clique no botão **Selecionar**.

    ![Adicionar bancos de dados](./media/sql-database-elastic-pool-create-portal/add-databases.png)

    Se os bancos de dados nos quais você está trabalhando tiverem telemetria de uso histórico suficiente, o gráfico **Uso estimado de eDTU e GB** e o gráfico de barras **Uso real de eDTU** serão atualizados para ajudá-lo a tomar decisões de configuração. Além disso, o serviço pode fornecer uma mensagem de recomendação para ajudá-lo a planejar o tamanho do pool. Veja [Recomendações dinâmicas](#dynamic-recommendations).

3. Use os controles na página **Configurar pool** para explorar as configurações e configurar o pool. Confira [Limites dos pools elásticos](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools) para saber mais sobre os limites de cada camada de serviço, e confira as [Considerações de preço e desempenho dos pools elásticos](sql-database-elastic-pool.md) para obter uma orientação detalhada sobre o tamanho correto de um pool elástico. Para ler mais informações sobre as configurações do pool, confira as [Propriedades do pool elástico](sql-database-elastic-pool.md#database-properties-for-pooled-databases).

    ![Configurar pool elástico](./media/sql-database-elastic-pool-create-portal/configure-performance.png)

4. Clique em **Selecionar** in the **Configure Pool** depois de alterar as configurações.
5. Clique em **OK** para criar o pool.

## <a name="understand-elastic-pool-recommendations"></a>Compreensão das recomendações de pool elástico

O serviço do Banco de Dados SQL avalia o histórico de uso e recomenda um ou mais pools quando essa alternativa for mais econômica do que usar bancos de dados individuais. Cada recomendação é configurada com um subconjunto exclusivo de bancos de dados do servidor que melhor se enquadram no pool.

![pool recomendado](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)  

A recomendação de pool inclui:

- Um tipo de preço para o pool (Básico, Standard, Premium ou Premium RS)
- Os **eDTUs do POOL** apropriados (também chamados de Máx. de eDTUs por pool)
- O **MÁX. DE eDTU** e o **MÍN. DE eDTU** por banco de dados
- A lista dos bancos de dados recomendados para o pool

> [!IMPORTANT]
> O serviço leva em consideração os últimos 30 dias de telemetria ao recomendar os pools. Para que um banco de dados seja considerado como candidato para um pool elástico, ele deve existir há pelo menos sete dias. Os bancos de dados que já estão em um pool elástico não são considerados candidatos para as recomendações de pool elástico.
>

O serviço avalia os recursos necessários e o custo-benefício de mover os bancos de dados individuais em cada camada de serviço para os pools na mesma camada. Por exemplo, todos os bancos de dados padrão em um servidor são avaliados para sua adaptação em um pool elástico Standard. Isso significa que o serviço não faz recomendações entre camadas diferentes, como mover um banco de dados Standard para um pool Premium.

Depois de adicionar os bancos de dados ao pool, as recomendações são geradas dinamicamente com base no uso histórico dos bancos de dados selecionados. Essas recomendações são mostradas no gráfico de uso de GB e de eDTU e em uma faixa de recomendação na parte superior da folha **Configurar pool**. Essas recomendações devem ajudar na criação de um pool elástico otimizado para bancos de dados específicos.

![Recomendações dinâmicas](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

## <a name="manage-and-monitor-an-elastic-pool"></a>Gerenciamento e monitoração um pool elástico

Use o portal do Azure para monitorar e gerenciar um pool elástico e os bancos de dados no pool. No portal, você pode monitorar a utilização de um pool elástico e os bancos de dados no pool. Você também pode criar um conjunto de alterações para o pool elástico e enviar todas as alterações ao mesmo tempo. Essas alterações incluem adicionar ou remover bancos de dados, alterar as configurações de pool elástico ou alterar suas configurações de banco de dados.

O gráfico a seguir mostra um exemplo de pool elástico. A exibição inclui:

*  Gráficos para monitorar o uso do recurso de pool elástico e os bancos de dados contidos no pool.
*  O botão do pool **Configurar** para fazer alterações ao pool elástico.
*  O botão **Criar banco de dados**, que cria um banco de dados e o adiciona ao pool elástico atual.
*  Trabalhos elásticos que ajudam a gerenciar muitos bancos de dados executando scripts Transact SQL em todos os bancos de dados em uma lista.

![Exibição de pool][2]

Você pode ir para um determinado pool para ver sua utilização de recursos. Por padrão, o pool está configurado para mostrar o uso de armazenamento e de eDTU na última hora. O gráfico pode ser configurado para mostrar métricas diferentes em vários períodos.

1. Selecione um pool elástico para trabalhar.
2. Em **Monitoramento de Pool Elástico** há um gráfico rotulado **Utilização de recursos**. Clique no gráfico.

    ![Monitoramento de pool elástico][3]

    A folha **Métrica** abre, mostrando uma exibição detalhada das métricas especificadas durante o período determinado.   

    ![Lâmina Métrica][9]

### <a name="to-customize-the-chart-display"></a>Para personalizar a exibição do gráfico

Você pode editar o gráfico e a folha de métrica para exibir outras métricas como percentual da CPU, percentual de E/S de dados e percentual de E/S de log usado.

1. Na folha de métricas, clique em **Editar**.

    ![Clique em editar][6]

2. Na folha **Editar Gráfico**, selecione um intervalo de tempo (última hora, hoje ou semana passada) ou clique em **personalizado** para selecionar um intervalo de datas nas duas últimas semanas. Selecione o tipo de gráfico (barra ou linhas) e selecione os recursos a serem monitorados.

   > [!Note]
   > Somente as métricas com a mesma unidade de medida podem ser exibidas no gráfico ao mesmo tempo. Por exemplo, caso selecione "percentual de eDTU", você só pode selecionar outras métricas com porcentagem como a unidade de medida.
   >

    ![Clique em editar](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)



3. Em seguida, clique em **OK**.

## <a name="manage-and-monitor-databases-in-an-elastic-pool"></a>Gerenciamento e monitoração de bancos de dados um pool elástico

Bancos de dados individuais também podem ser monitorados para identificar potenciais problemas.

1. Em **Monitoramento de Banco de Dados Elástico**, há um gráfico que exibe as métricas para cinco bancos de dados. Por padrão, o gráfico exibe os 5 principais bancos de dados no pool por uso médio de eDTU na última hora. Clique no gráfico.

    ![Monitoramento de pool elástico][4]

2. A folha **Utilização de Recursos do Banco de Dados** é exibida. Isso fornece uma exibição detalhada do uso do banco de dados no pool. Usando a grade na parte inferior da folha, você pode selecionar bancos de dados no pool para exibir seu uso no gráfico (até 5 bancos de dados). Você também pode personalizar a janela de métricas e tempo exibida no gráfico clicando em **Editar gráfico**.

    ![Folha Utilização de Recursos do Banco de Dados][8]

### <a name="to-customize-the-view"></a>Para personalizar a exibição

1. Na folha **Utilização de recursos do banco de dados**, clique em **Editar gráfico**.

    ![Clique em Editar gráfico](./media/sql-database-elastic-pool-manage-portal/db-utilization-blade.png)

2. Na folha **Editar** gráfico, selecione um intervalo de tempo (como última hora ou 24 horas) ou clique em **personalizado** para selecionar um período diferente nas duas últimas semanas.

    ![Clique em Personalizar](./media/sql-database-elastic-pool-manage-portal/editchart-date-time.png)

3. Clique na lista suspensa **Comparar bancos de dados** para selecionar uma métrica diferente a ser usada ao comparar os bancos de dados.

    ![Edite o gráfico](./media/sql-database-elastic-pool-manage-portal/edit-comparison-metric.png)

### <a name="to-select-databases-to-monitor"></a>Para selecionar os bancos de dados a serem monitorados

Na lista de banco de dados da folha **Utilização de Recursos do Banco de Dados** , você pode localizar bancos de dados específicos examinando as páginas na lista ou digitando o nome de um banco de dados. Use a caixa de seleção para escolher o banco de dados.

![Pesquisar por bandos de dados a serem monitorados][7]


## <a name="add-an-alert-to-an-elastic-pool-resource"></a>Adicionar um alerta para um recurso de pool elástico

Você pode adicionar regras a um pool elástico para enviar emails para pessoas ou cadeias de caracteres de alerta para pontos de extremidade da URL quando o pool elástico atingir um limite de utilização definido.

> [!IMPORTANT]
> O monitoramento de utilização de recursos para pools elásticos tem um atraso de, pelo menos, 20 minutos. No momento, não é permitido definir alertas de menos de 30 minutos para pools elásticos. Todos os alertas definidos para Pools Elásticos com um período (parâmetro chamado "-WindowSize" na API do PowerShell) de menos de 30 minutos não podem ser disparados. Certifique-se de que todos os alertas definidos para pools elásticos usem um período (WindowSize) de 30 minutos ou mais.
>
>

**Para adicionar um alerta a qualquer recurso:**

1. Clique no gráfico **Utilização de recursos** para abrir a folha **Métrica**, clique em **Adicionar alerta** e preencha as informações na folha **Adicionar uma regra de alerta** (**Recurso** é configurado automaticamente para ser o pool com o qual você está trabalhando).
2. Digite um **Nome** e uma **Descrição** que identifiquem o alerta para você e os destinatários.
3. Escolha na lista uma **Métrica** para a qual deseja o alerta.

    O gráfico mostra dinamicamente a utilização de recursos para a métrica para ajudá-lo a escolher um limite.

4. Escolha uma **Condição** (maior que, menor que, etc.) e um **Limite**.
5. Clique em **OK**.

## <a name="move-a-database-into-an-elastic-pool"></a>Mover um banco de dados para um pool elástico

Você pode adicionar ou remover bancos de dados de um pool existente. Os bancos de dados podem estar em outros pools. No entanto, você só pode adicionar bancos de dados que estão no mesmo servidor lógico.

1. Na folha do pool, em **Bancos de dados elásticos**, clique em **Configurar pool**.

    ![Clique em Configurar pool][1]

2. Na folha **Configurar pool**, clique em **Adicionar ao pool**.

    ![Clique em Adicionar ao pool](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)


3. Na folha **Adicionar bancos de dados** , escolha os bancos de dados para adicionar ao pool. Em seguida, clique em **Selecionar**.

    ![Selecione os bancos de dados a serem adicionados](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)

    Agora, a folha **Configurar pool** lista o banco de dados que você selecionou para adicionar, com o status definido como **Pendente**.

    ![Adições de pool pendentes](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)

3. Na folha **Configurar pool**, clique em **Salvar**.

    ![Clique em Salvar](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## <a name="move-a-database-out-of-an-elastic-pool"></a>Remover um banco de dados de um pool elástico

1. Na folha **Configurar pool** , escolha os bancos de dados a serem removidos.

    ![lista de bancos de dados](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)

2. Clique em **Remover do pool**.

    ![lista de bancos de dados](./media/sql-database-elastic-pool-manage-portal/click-remove.png)

    Agora, a folha **Configurar pool** lista o banco de dados que você escolheu para remoção, com o status definido como **Pendente**.

    ![visualização de adição ou remoção de banco de dados](./media/sql-database-elastic-pool-manage-portal/pending-removal.png)

3. Na folha **Configurar pool**, clique em **Salvar**.

    ![Clique em Salvar](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## <a name="change-performance-settings-of-an-elastic-pool"></a>Alterar as configurações de desempenho de um pool elástico

À medida que monitora a utilização de recursos de um pool elástico, você pode perceber que alguns ajustes são necessários. Talvez o pool precise de uma alteração nos limites de armazenamento ou de desempenho. Pode ser útil alterar as configurações de banco de dados no pool. Você pode alterar a configuração do pool a qualquer momento para obter o melhor equilíbrio entre desempenho e custo. Veja [Quando um pool elástico deve ser usado?](sql-database-elastic-pool.md) para saber mais.

Para alterar as eDTUs ou os limites de armazenamento por pool e as eDTUs por banco de dados:

1. Abra a folha **Configurar pool** .

    Em **configurações do pool elástico**, use os controles deslizantes de qualquer lado para alterar as configurações do pool.

    ![Utilização de recursos de pool elástico](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. Quando a configuração for alterada, a exibição mostrará o custo mensal estimado da alteração.

    ![Atualização de um pool elástico e o novo custo mensal](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)

## <a name="latency-of-elastic-pool-operations"></a>Latência de operações do pool elástico
* Normalmente, a alteração das eDTUs mínimas ou das eDTUs máximas por banco de dados é um processo concluído em cinco minutos ou menos.
* A alteração do limite de eDTUs por pool depende da quantidade total de espaço usado por todos os bancos de dados no pool. As alterações levam, em média, 90 minutos ou menos a cada 100 GB. Por exemplo, se o espaço total usado por todos os bancos de dados no pool for de 200 GB, a latência prevista para alterar os eDTUs do pool será de 3 horas por pool ou menos.

## <a name="next-steps"></a>Próximas etapas

- Para entender o que um pool Elástico é, confira [pool elástico do banco de dados SQL](sql-database-elastic-pool.md).
- Para ler mais diretrizes sobre o uso dos pools elásticos, confira [Considerações de preço e desempenho para pools elásticos](sql-database-elastic-pool.md).
- Para usar trabalhos elásticos para execução de scripts Transact-SQL em qualquer quantidade de bancos de dados no pool, confira [Visão geral de trabalhos elásticos](sql-database-elastic-jobs-overview.md).
- Para pesquisar em qualquer número de bancos de dados no pool, confira [Visão geral de consulta elástica](sql-database-elastic-query-overview.md).
- Para transações de qualquer número de bancos de dados no pool, confira [Transações elásticas](sql-database-elastic-transactions-overview.md).


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

