---
title: Distribuição global do Azure Cosmos DB
description: Saiba como replicar dados globalmente com o Azure Cosmos DB no Portal do Azure
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 03/26/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: fb9418d47d2888467e1c1c40862833029111b75b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
ms.locfileid: "31431713"
---
Você pode saber mais sobre a distribuição global do Azure Cosmos DB no vídeo a seguir, no qual o Gerente de programa do Azure Cosmos DB, Andrew Liu, mostra a funcionalidade de distribuição global.

>[!VIDEO https://www.youtube.com/embed/1D06yjTVxt8]

Para obter mais informações sobre como a replicação de banco de dados global funciona no Azure Cosmos DB, veja [Distribuir dados globalmente com o Cosmos DB](../articles/cosmos-db/distribute-data-globally.md).

## <a id="addregion"></a>Adicionar regiões de banco de dados globais usando o Portal do Azure
O BD Cosmos do Azure está disponível em todas as [regiões do Azure][azureregions] pelo mundo. Após a seleção do nível de consistência padrão para sua conta de banco de dados, você pode associar uma ou mais regiões (dependendo da sua escolha do nível de consistência padrão e das necessidades de distribuição global).

1. No [Portal do Azure](https://portal.azure.com/), na barra esquerda, clique em **BD Cosmos do Azure**.
2. Na página do **Azure Cosmos DB**, selecione a conta do banco de dados a ser modificada.
3. Na página da conta, clique em **Replicar dados globalmente** no menu.
4. Na página **Replicar dados globalmente**, clicando nas regiões no mapa, selecione aquelas a serem adicionadas ou removidas e clique em **Salvar**. Há um custo para adicionar regiões. Veja a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) ou o artigo [Distribuir dados globalmente com o Azure Cosmos DB](../articles/cosmos-db/distribute-data-globally.md) para obter mais informações.
   
    ![Clicar nas regiões no mapa para adicioná-las ou removê-las][1]
    
Depois de adicionar uma segunda região, a opção **Failover Manual** é habilitada na página **Replicar dados globalmente** no portal. Você pode usar essa opção para testar o processo de failover ou alterar a região de gravação principal. Depois de adicionar uma terceira região, a opção **Prioridades de Failover** é habilitada na mesma página para que você possa alterar a ordem de failover das leituras.  

### <a name="selecting-global-database-regions"></a>Selecionar regiões de bancos de dados globais
Há dois cenários comuns para configurar duas ou mais regiões:

1. Fornecimento de acesso a dados de baixa latência para os usuários finais, independentemente de onde estejam localizados em todo o mundo
2. Adição de resiliência regional para continuidade dos negócios e recuperação de desastre (BCDR)

Para oferecer baixa latência para os usuários finais, é recomendável implantar o aplicativo e o Azure Cosmos DB nas regiões que correspondem aos locais em que os usuários do aplicativo estão localizados.

Para o BCDR, é recomendável adicionar regiões com base nos pares de regiões descritos no artigo [Continuidade dos negócios e recuperação de desastre (BCDR): Regiões Emparelhadas do Azure][bcdr].

<!--

## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your Cosmos DB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **Azure Cosmos DB** blade, select the database account to modify.
2. In the account blade, click **Replicate data globally** from the menu.
3. In the **Replicate data globally** blade, click **Manual Failover** from the top bar.
    ![Change the write region under Azure Cosmos DB Account > Replicate data globally > Manual Failover][2]
4. Select a read region to become the new write region, click the checkbox to confirm triggering a failover, and click OK
    ![Change the write region by selecting a new region in list under Azure Cosmos DB Account > Replicate data globally > Manual Failover][3]

--->

<!--Image references-->
[1]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-add-region.png
[2]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-1.png
[3]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: ../articles/cosmos-db/consistency-levels.md
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/cosmos-db/
