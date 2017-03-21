---
title: "Inteligência + Análises do Azure Government | Microsoft Docs"
description: "Este guia fornece uma comparação dos recursos e orientações sobre como desenvolver aplicativos para o Azure Government"
services: azure-government
cloud: gov
documentationcenter: 
author: MeganYount
manager: zakramer
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/06/2016
ms.author: MeganYount
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 0233aa66bc4f4f135456ec15bd09756e63192b14
ms.lasthandoff: 03/06/2017


---
# <a name="azure-government-intelligence--analytics"></a>Inteligência + Análises do Azure Government
Este artigo descreve os serviços, variações e considerações sobre inteligência e análise para o ambiente do Azure Government.

## <a name="hdinsight"></a>HDInsight
O HDInsight no Linux Standard está geralmente disponível no Azure Government. Você pode ver uma demonstração sobre como criar soluções centralizadas em dados no Azure Governamental usando o HDInsight <a href=https://channel9.msdn.com/Blogs/Azure/Cognitive-Services-HDInsight-and-Power-BI-on-Azure-Government/>aqui</a>.

HDInsight no Linux Premium estará disponível em breve.

### <a name="variations"></a>Variações
Os seguintes recursos do HDInsight não estão disponíveis no Azure Government no momento.

* O HDInsight não está disponível no Windows.
* O Azure Data Lake Store não está disponível no Azure Government no momento. O Armazenamento de Blobs do Azure é a única opção de armazenamento disponível no momento.

As URLs do Log Analytics são diferentes no Azure Governamental:

| Tipo de serviço | Público do Azure | Azure Government |
| --- | --- | --- |
| Cluster do HDInsight | \*.azurehdinsight.net | \*.azurehdinsight.us |

Para obter mais informações, consulte a [documentação pública do HDInsight](../hdinsight/hdinsight-hadoop-introduction.md).

## <a name="power-bi"></a>Power BI
O Power BI Governamental está geralmente disponível como parte das assinaturas da Comunidade do Office 365 US Government. Saiba mais sobre o Power BI US Government <a href=https://powerbi.microsoft.com/en-us/documentation/powerbi-service-govus-overview/>aqui</a>. Veja uma demonstração sobre como criar soluções centralizadas em dados no Azure Governamental usando o Power BI <a href=https://channel9.msdn.com/Blogs/Azure/Cognitive-Services-HDInsight-and-Power-BI-on-Azure-Government/>aqui</a>.

O Power BI Embedded estará disponível em breve.

### <a name="variations"></a>Variações

As URLs para o Power BI são diferentes no US Government:

| Tipo de serviço | Power BI Comercial | Power BI US Government |
| --- | --- | --- |
| URL do Power BI | app.powerbi.com | app.powerbigov.us |

## <a name="next-steps"></a>Próximas etapas
Para obter informações complementares e atualizações, assine o <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog do Microsoft Azure Governamental. </a>

