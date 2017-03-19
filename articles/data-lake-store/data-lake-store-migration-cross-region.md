---
title: "Diretrizes do Azure Data Lake Store para migração entre regiões | Microsoft Docs"
description: "Diretrizes do Azure Data Lake Store para migração entre regiões"
services: data-lake-store
documentationcenter: 
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/27/2017
ms.author: stewu
translationtype: Human Translation
ms.sourcegitcommit: 3c83a76c589178d5a2a01ed4edd208e42fb2e83e
ms.openlocfilehash: 785f97a28c060f88a0e0f3a5aa9cf6555ed2b5a4
ms.lasthandoff: 02/22/2017


---
# <a name="guidance-to-migrate-azure-data-lake-store-across-regions"></a>Diretrizes para migrar o Azure Data Lake Store entre regiões

Com o Azure Data Lake Store sendo disponibilizado em novas regiões, você pode decidir executar uma migração única para aproveitar o que nova região tem a oferecer.  Aqui estão algumas diretrizes sobre o que considerar ao planejar e executar a migração.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do Azure Data Lake Store em duas regiões diferentes**. Para obter instruções sobre como criar uma, consulte [Introdução ao Azure Data Lake Store](data-lake-store-get-started-portal.md).
* **Azure Data Factory**.  Para saber mais, consulte [Introdução ao Azure Data Factory](../data-factory/data-factory-introduction.md).


## <a name="guidance-for-migration"></a>Diretrizes para migração

Recomendamos que você primeiro identifique a estratégia de migração apropriada para o aplicativo que faz a gravação, leitura ou o processamento de dados no Azure Data Lake Store. Ao escolher uma estratégia, você deve considerar os requisitos de disponibilidade do aplicativo (como tempo de inatividade). A abordagem mais simples para seus dados pode ser a “lift-and-shift”, por exemplo.  Isso significa que pausar os aplicativos na região antiga enquanto todos os dados são copiados para a nova região.  Quando o processo de cópia for concluído, você pode reiniciar o aplicativo na nova região e excluir a conta antiga do Azure Data Lake Store.  No entanto, haverá um tempo de inatividade durante a migração.

Como alternativa para reduzir a inatividade, é possível iniciar a ingestão de novos dados na nova região imediatamente e começar a executar seus aplicativos na nova região assim que tiver o mínimo de dados necessário.  Em segundo plano, você pode copiar os dados da conta antiga para a conta do Azure Data Lake Store na nova região.  Isso permite fazer a transição para a nova região com pouco tempo de inatividade.  Depois que todos os dados mais antigos forem copiados, você pode excluir a conta antiga do ADLS.

Outros detalhes importantes a considerar no planejamento da migração são:

* **Volume de dados** - O volume de dados (GBs, número de arquivos e pastas etc.) terá impacto sobre o tempo e os recursos necessários para a movimentação.

* **Nome da conta do Azure Data Lake Store** - O nome da nova conta na nova região precisará ser único para todo o mundo. Por exemplo, se contosoeastus2.azuredatalakestore.net é o nome da antiga conta de armazenamento no Leste dos EUA 2, você pode nomear sua conta de armazenamento no Norte da Europa como contosonortheu.azuredatalakestore.net

* **Opção de ferramentas** - Para copiar os arquivos do Azure Data Lake Store, é recomendável que você use a [atividade de cópia do Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md).   O Azure Data Factory oferece suporte à movimentação de dados com alto desempenho e confiabilidade.  Tenha em mente que o Azure Data Factory só vai copiar a hierarquia de pastas e o conteúdo dos arquivos. Quaisquer listas de controle de acesso (ACLs) que você tenha aplicado precisarão ser reaplicadas manualmente na nova conta.  As [diretrizes de ajuste de desempenho do Azure Data Factory](../data-factory/data-factory-copy-activity-performance.md) são uma boa referência com metas de desempenho para os cenários mais favoráveis.  Se quiser que os dados sejam copiados em menos tempo, você precisará usar Unidades de Movimentação de Dados de Nuvem adicionais.  Observe que algumas outras ferramentas, como ADLCopy, não dão suporte a cópia de dados entre regiões.  

* **[Encargos de largura de banda](https://azure.microsoft.com/en-us/pricing/details/bandwidth/) ** Encargos serão aplicáveis, pois os dados serão transferidos para fora de uma região do Azure.

* **ACLs em seus dados** - Proteja seus dados na nova região ao aplicar ACLs a arquivos e pastas.  Para obter diretrizes sobre isso, clique [aqui](data-lake-store-secure-data.md).  Recomendamos que você aproveite essa migração como uma oportunidade para atualizar e ajustar suas ACLs.  Se quiser usar configuração semelhante, você pode exibir as ACLs aplicadas a qualquer arquivo por meio do portal, de [cmdlets PowerShell](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.datalakestore/v3.1.0/get-azurermdatalakestoreitempermission) ou SDKs.  

* **Localização dos serviços de análise** - Para melhor desempenho, os serviços de análise, como Data Lake Analytics ou HDInsight, devem ficar localizados na mesma região dos dados.  

## <a name="see-also"></a>Consulte também
* [Visão geral do Repositório Azure Data Lake](data-lake-store-overview.md)

