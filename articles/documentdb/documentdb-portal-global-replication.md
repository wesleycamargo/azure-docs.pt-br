<properties
	pageTitle="Replicação de bancos de dados globais do Banco de Dados de Documentos | Microsoft Azure"
	description="Saiba como gerenciar a replicação global de sua conta do Banco de Dados de Documentos por meio do Portal do Azure."
	services="documentdb"
    keywords="banco de dados global, replicação"
	documentationCenter=""
	authors="mimig1"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/26/2016"
	ms.author="mimig"/>

# Como executar a replicação de banco de dados global do Banco de Dados de Documentos usando o Portal do Azure

Saiba como usar o Portal do Azure para replicar dados em várias regiões para disponibilidade global dos dados no Banco de Dados de Documentos do Azure.

Para obter informações sobre como a replicação de banco de dados global funciona no Banco de Dados de Documentos, veja [Distribuir dados globalmente com o Banco de Dados de Documentos](documentdb-distribute-data-globally.md). Para obter informações sobre como executar a replicação de banco de dados global de forma programática, veja [Desenvolver com contas do Banco de Dados de Documentos de várias regiões](documentdb-developing-with-multiple-regions.md).

> [AZURE.NOTE] A distribuição global dos bancos de dados do Banco de Dados de Documentos geralmente é disponibilizada e habilitada automaticamente para contas do Banco de Dados de Documentos recentemente criadas. Estamos trabalhando para habilitar a distribuição global em todas as contas existentes, mas nesse ínterim, se quiser que a distribuição global seja habilitada para sua conta, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) e nós a habilitaremos para você agora mesmo.

## <a id="addregion"></a>Adicionar regiões de bancos de dados globais

O Banco de Dados de Documentos está disponível na maioria das [regiões do Azure][azureregions]. Após a seleção do nível de consistência padrão para sua conta de banco de dados, você pode associar uma ou mais regiões (dependendo da sua escolha do nível de consistência padrão e das necessidades de distribuição global).

1. No [Portal do Azure](https://portal.azure.com/), no Jumpbar, clique em **Contas do Banco de Dados de Documentos**.
2. Na folha **Conta do Banco de Dados de Documentos**, selecione a conta do banco de dados a ser modificada.
3. Na folha da conta, clique em **Adicionar/Remover Regiões** no menu.
4. Na folha **Adicionar/Remover Regiões**, selecione as regiões a serem adicionadas ou removidas e clique em **OK**. Há um custo para adicionar regiões. Veja a [página de preços](https://azure.microsoft.com/pricing/details/documentdb/) ou o artigo [Distribuir dados globalmente com o Banco de Dados de Documentos](documentdb-distribute-data-globally.md) para obter mais informações.

    ![Clicar nas regiões no mapa para adicioná-las ou removê-las][1]

### Selecionar regiões de bancos de dados globais

Ao configurar duas ou mais regiões, é recomendável que as regiões sejam selecionadas com base nos pares de região descritos no artigo [Continuidade dos negócios e recuperação de desastres (BCDR): Regiões Emparelhadas do Azure][bcdr].

Especificamente, ao configurar para várias regiões, certifique-se de selecionar o mesmo número de regiões (+/- 1 para par/ímpar) de cada uma das colunas de região emparelhada. Por exemplo, se quiser implantar em quatro regiões dos EUA, selecione duas regiões dos EUA na coluna à esquerda e duas à direita. Assim, a definição apropriada seria: Oeste dos EUA, Leste dos EUA, Centro-Norte dos EUA e Centro-Sul dos EUA.

É importante seguir essa instrução quando apenas duas regiões são configuradas para cenários de recuperação de desastre. Para mais de duas regiões, seguir essa instrução é uma boa prática, mas não crítica, pois algumas da regiões selecionadas adotam esse emparelhamento.

<!---
## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your DocumentDB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **DocumentDB Account** blade, select the database account to modify.
2. In the account blade, if the **All Settings** blade is not already opened, click **All Settings**.
3. In the **All Settings** blade, click **Write Region Priority**.
    ![Change the write region under DocumentDB Account > Settings > Add/Remove Regions][2]
4. Click and drag regions to order the list of regions. The first region in the list of regions is the active write region.
    ![Change the write region by reordering the region list under DocumentDB Account > Settings > Change Write Regions][3]
-->

## <a id="next"></a>Próximas etapas

Saiba como gerenciar a consistência de sua conta globalmente replicada lendo [Níveis de consistência no Banco de Dados de Documentos](documentdb-consistency-levels.md).

Para obter informações sobre como a replicação de banco de dados global funciona no Banco de Dados de Documentos, veja [Distribuir dados globalmente com o Banco de Dados de Documentos](documentdb-distribute-data-globally.md). Para obter informações sobre a replicação de dados de forma programática em várias regiões, veja [Desenvolver com contas do Banco de Dados de Documentos de várias regiões](documentdb-developing-with-multiple-regions.md).

<!--Image references-->
[1]: ./media/documentdb-portal-global-replication/documentdb-add-region.png
[2]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-1.png
[3]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/pt-BR/regions/#services
[offers]: https://azure.microsoft.com/pt-BR/pricing/details/documentdb/

<!---HONumber=AcomDC_0831_2016-->