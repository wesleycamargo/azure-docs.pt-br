---
title: Mover dados para e do armazenamento do Azure Blob | Microsoft Docs
description: Mover dados bidirecionalmente no armazenamento de BLOBs do Azure
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: fa3fd5dfdd0fbdb8200b0c5c8df512caedbe735c
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441835"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Mover dados bidirecionalmente no armazenamento de BLOBs do Azure

O Processo de Ciência de Dados de Equipe exige que os dados sejam incluídos ou carregados em uma variedade de ambientes de armazenamento diferentes para serem processados ou analisados da maneira mais apropriada em cada estágio do processo.
Os artigos a seguir descrevem como mover dados para e do armazenamento do Azure Blob usando diferentes tecnologias.

* [Gerenciador de Armazenamento do Azure](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [AzCopy](move-data-to-azure-blob-using-azcopy.md)
* [Python](move-data-to-azure-blob-using-python.md)
* [SSIS](move-data-to-azure-blob-using-ssis.md)

O melhor método para você depende de seu cenário. O artigo [Cenários para análises avançadas no Azure Machine Learning](plan-sample-scenarios.md) ajudará você a determinar os recursos necessários para uma variedade de fluxos de trabalho de ciência de dados usados no processo de análise avançada.

> [!NOTE]
> Para obter uma introdução completa ao Armazenamento de Blobs do Azure, confira [Noções básicas do Serviço Blob do Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) e [Serviço Blob do Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

Como alternativa, você pode usar o [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) para: 

* criar e agendar um pipeline que baixa os dados do Armazenamento de Blobs do Azure, 
* passá-lo para um serviço Web publicado do Azure Machine Learning, 
* receber os resultados da análise preditiva, e 
* carrega os resultados no armazenamento. 

Para obter mais informações, consulte [Criar pipelines preditivos usando o Azure Data Factory e o Azure Machine Learning](../../data-factory/transform-data-using-machine-learning.md).

## <a name="prerequisites"></a>Pré-requisitos
Este documento pressupõe que você tenha uma assinatura, uma conta de armazenamento do Azure e a chave de armazenamento correspondente dessa conta. Antes de carregar/baixar os dados, você deve conhecer o nome e a chave da sua conta do Armazenamento do Azure.

* Para configurar uma assinatura do Azure, consulte [Avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Para obter instruções sobre como criar uma conta de armazenamento e para obter informações sobre conta e chave, consulte [Sobre contas do armazenamento do Azure](../../storage/common/storage-create-storage-account.md).

