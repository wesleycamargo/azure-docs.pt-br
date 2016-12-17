---
title: Mover dados de e para o Armazenamento de Blobs do Azure | Microsoft Docs
description: Mover dados de e para o Armazenamento de Blobs do Azure
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: d6681e30-ab45-45ea-a9fb-ac8acefe544d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2016
ms.author: bradsev;sachouks
translationtype: Human Translation
ms.sourcegitcommit: a67f2e77d3bc7da35a03b68d7f32fd3a2a42bfcd
ms.openlocfilehash: d19ab9fb96319203b2d90e1d203a26f6453db950


---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Mover dados de e para o Armazenamento de Blobs do Azure
[!INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

<!-- just in case, adding this to separate these two include references -->

[!INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]

O melhor método para você depende de seu cenário. O artigo [Cenários para análises avançadas no Aprendizado de Máquina do Azure](machine-learning-data-science-plan-sample-scenarios.md) ajudará você a determinar os recursos necessários para uma variedade de fluxos de trabalho de ciência de dados usados no processo de análise avançada.

> [!NOTE]
> Para obter uma introdução completa ao Armazenamento de Blobs do Azure, confira [Noções básicas do Serviço Blob do Azure](../storage/storage-dotnet-how-to-use-blobs.md) e [Serviço Blob do Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

Como alternativa, você pode usar o [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) para: 

* criar e agendar um pipeline que baixa os dados do Armazenamento de Blobs do Azure, 
* passá-lo para um serviço Web publicado do Azure Machine Learning, 
* receber os resultados da análise preditiva, e 
* carrega os resultados no armazenamento. 

Para obter mais informações, consulte [Criar pipelines preditivos usando o Azure Data Factory e o Aprendizado de Máquina do Azure](../data-factory/data-factory-azure-ml-batch-execution-activity.md).

## <a name="prerequisites"></a>Pré-requisitos
Este documento pressupõe que você tenha uma assinatura, uma conta de armazenamento do Azure e a chave de armazenamento correspondente dessa conta. Antes de carregar/baixar os dados, você deve conhecer o nome e a chave da sua conta do Armazenamento do Azure.

* Para configurar uma assinatura do Azure, consulte [Avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Para obter instruções sobre como criar uma conta de armazenamento e para obter informações sobre conta e chave, consulte [Sobre contas do armazenamento do Azure](../storage/storage-create-storage-account.md).




<!--HONumber=Nov16_HO3-->


