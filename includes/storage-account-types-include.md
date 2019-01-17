---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/02/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b278cce8f885f31f9d59fd389261812e04e58405
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/07/2019
ms.locfileid: "54066983"
---
O Armazenamento do Azure oferece três tipos de conta de armazenamento. Cada tipo é compatível com recursos diferentes e tem um modelo de preços próprio. Considere essas diferenças antes de criar uma conta de armazenamento para determinar o melhor tipo de conta para seus aplicativos. Os três tipos de contas de armazenamento são:

* **Contas para uso geral v2**: tipo de conta de armazenamento básico para blobs, arquivos, filas e tabelas. Recomendado para a maioria dos cenários que usam o Armazenamento do Azure.
* **Contas para uso geral v1**: tipo de conta herdada para blobs, arquivos, filas e tabelas. Use contas de uso geral v2 em vez desta opção, quando possível.
* **Contas de armazenamento de blobs**: contas de armazenamento somente de blobs. Use contas de uso geral v2 em vez desta opção, quando possível. 

A tabela a seguir descreve os tipos de conta de armazenamento e suas funcionalidades:

| Tipo de conta de armazenamento | Serviços com suporte                       | Níveis de desempenho compatíveis | Camadas de acesso compatíveis               | Opções de replicação                                                | Modelo de implantação<sup>1</sup>  | Criptografia<sup>2</sup> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------------|--------------------------------------------------------------------|-------------------|------------|
| Uso geral V2   | Blob, arquivo, fila, tabela e disco       | Standard, Premium           | Quente, frio, de arquivos<sup>3</sup> | LRS, ZRS<sup>4</sup>, GRS, RA-GRS | Gerenciador de Recursos | Criptografado  |
| Uso geral V1   | Blob, arquivo, fila, tabela e disco       | Standard, Premium           | N/D                                  | LRS, GRS, RA-GRS                                                   | Resource Manager, Clássico  | Criptografado  |
| Armazenamento de blob         | Blob (somente blobs de blocos e blobs de acréscimo) | Standard                    | Quente, frio, de arquivos<sup>3</sup>                            | LRS, GRS, RA-GRS                                                   | Gerenciador de Recursos  | Criptografado  |

<sup>1</sup>É recomendável usar o modelo de implantação do Azure Resource Manager. Ainda é possível criar contas de armazenamento que usam o modelo de implantação clássico em alguns locais, e as contas clássicas existentes ainda têm suporte. Para obter mais informações, consulte [Azure Resource Manager vs implantação clássica: compreenda os modelos de implantação e o estado de seus recursos](../articles/azure-resource-manager/resource-manager-deployment-model.md).

<sup>2</sup>Todas as contas de armazenamento são criptografadas usando a SSE (Criptografia do Serviço de Armazenamento) para dados em repouso. Para obter mais informações, consulte [Criptografia de serviço do Armazenamento do Azure para dados em repouso](../articles/storage/common/storage-service-encryption.md).

<sup>3</sup>A camada de acesso aos arquivos está disponível somente no nível de blob individual, e não no nível da conta de armazenamento. Somente os blobs de bloco e de acréscimo podem ser arquivados. Para obter mais informações, consulte [Armazenamento de Blobs do Azure: camadas de armazenamento de Arquivos, Frequente e Esporádico](../articles/storage/blobs/storage-blob-storage-tiers.md).

<sup>4</sup>O ZRS (armazenamento com redundância de zona) só está disponível para as contas de armazenamento padrão para uso geral v2. Para obter mais informações sobre o ZRS, consulte [Armazenamento com redundância de zona (ZRS): aplicativos de Armazenamento do Azure altamente disponíveis](../articles/storage/common/storage-redundancy-zrs.md). Para saber mais sobre outras opções de replicação, confira [Replicação do Armazenamento do Azure](../articles/storage/common/storage-redundancy.md).
