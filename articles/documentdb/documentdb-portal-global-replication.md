---
title: "Replicação de banco de dados global do DocumentDB | Microsoft Docs"
description: "Saiba como gerenciar a replicação global de sua conta do Banco de Dados de Documentos por meio do Portal do Azure."
services: documentdb
keywords: "banco de dados global, replicação"
documentationcenter: 
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: cc393967fec1a98a4dd596a156c7a12e88959b04
ms.lasthandoff: 03/15/2017


---
# <a name="how-to-perform-global-database-replication-using-the-azure-portal"></a>Como executar a replicação de banco de dados global usando o Portal do Azure

Saiba como usar o Portal do Azure para replicar dados em várias regiões para disponibilidade global dos dados no Azure DocumentDB e API para MongoDB.

Para obter informações sobre como a replicação de banco de dados global funciona no Banco de Dados de Documentos, veja [Distribuir dados globalmente com o Banco de Dados de Documentos](documentdb-distribute-data-globally.md). Para obter informações sobre como executar a replicação de banco de dados global de forma programática, veja [Desenvolver com contas do Banco de Dados de Documentos de várias regiões](documentdb-developing-with-multiple-regions.md).

## <a id="addregion"></a>Adicionar regiões de bancos de dados globais
O DocumentDB está disponível na maioria das [regiões do Azure][azureregions]. Após a seleção do nível de consistência padrão para sua conta de banco de dados, você pode associar uma ou mais regiões (dependendo da sua escolha do nível de consistência padrão e das necessidades de distribuição global).

1. No [Portal do Azure](https://portal.azure.com/), na barra esquerda, clique em **NoSQL (DocumentDB)**.
2. Na folha **NoSQL (DocumentDB)**, escolha a conta do banco de dados a ser modificada.
3. Na folha da conta, clique em **Replicar dados globalmente** no menu.
4. Na folha **Replicar dados globalmente**, selecione as regiões a serem adicionadas ou removidas e clique em **Salvar**. Há um custo para adicionar regiões. Veja a [página de preços](https://azure.microsoft.com/pricing/details/documentdb/) ou o artigo [Distribuir dados globalmente com o DocumentDB](documentdb-distribute-data-globally.md) para obter mais informações.
   
    ![Clicar nas regiões no mapa para adicioná-las ou removê-las][1]
    
Depois de adicionar uma segunda região, a opção **Failover Manual** é habilitada na folha **Replicar dados localmente** no portal. Você pode usar essa opção para testar o processo de failover. Depois de adicionar uma terceira região, a opção **Prioridades de Failover** é habilitada na mesma folha para que você possa alterar a ordem de failover das leituras.  

### <a name="selecting-global-database-regions"></a>Selecionar regiões de bancos de dados globais
Ao configurar duas ou mais regiões, é recomendável que as regiões sejam selecionadas com base nos pares de região descritos no artigo [Continuidade dos negócios e recuperação de desastre (BCDR): Regiões Emparelhadas do Azure][bcdr].

Especificamente, ao configurar para várias regiões, certifique-se de selecionar o mesmo número de regiões (+/-&1; para par/ímpar) de cada uma das colunas de região emparelhada. Por exemplo, se quiser implantar em quatro regiões dos EUA, selecione duas regiões dos EUA na coluna à esquerda e duas à direita. Assim, a definição apropriada seria: Oeste dos EUA, Leste dos EUA, Centro-Norte dos EUA e Centro-Sul dos EUA.

É importante seguir essa instrução quando apenas duas regiões são configuradas para cenários de recuperação de desastre. Para mais de duas regiões, seguir essa instrução é uma boa prática, mas não crítica, pois algumas da regiões selecionadas adotam esse emparelhamento.

<!---
## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your DocumentDB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **NoSQL (DocumentDB)** blade, select the database account to modify.
2. In the account blade, if the **All Settings** blade is not already opened, click **All Settings**.
3. In the **All Settings** blade, click **Write Region Priority**.
    ![Change the write region under DocumentDB Account > Settings > Add/Remove Regions][2]
4. Click and drag regions to order the list of regions. The first region in the list of regions is the active write region.
    ![Change the write region by reordering the region list under DocumentDB Account > Settings > Change Write Regions][3]
-->

### <a name="verifying-your-regional-setup-in-api-for-mongodb"></a>Verificar a configuração regional na API para MongoDB
A maneira mais simples de verificar sua configuração global dentro de API para MongoDB é executar o comando *isMaster()* no Shell do Mongo.

No Shell do Mongo:

   ```
      db.isMaster()
   ```
   
Exemplos de resultados:

   ```JSON
      {
         "_t": "IsMasterResponse",
         "ok": 1,
         "ismaster": true,
         "maxMessageSizeBytes": 4194304,
         "maxWriteBatchSize": 1000,
         "minWireVersion": 0,
         "maxWireVersion": 2,
         "tags": {
            "region": "South India"
         },
         "hosts": [
            "vishi-api-for-mongodb-southcentralus.documents.azure.com:10250",
            "vishi-api-for-mongodb-westeurope.documents.azure.com:10250",
            "vishi-api-for-mongodb-southindia.documents.azure.com:10250"
         ],
         "setName": "globaldb",
         "setVersion": 1,
         "primary": "vishi-api-for-mongodb-southindia.documents.azure.com:10250",
         "me": "vishi-api-for-mongodb-southindia.documents.azure.com:10250"
      }
   ```

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
[azureregions]: https://azure.microsoft.com/en-us/regions/#services
[offers]: https://azure.microsoft.com/en-us/pricing/details/documentdb/

