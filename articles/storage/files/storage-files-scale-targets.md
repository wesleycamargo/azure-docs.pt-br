---
title: Metas de desempenho e escalabilidade do Arquivos do Azure | Microsoft Docs
description: "Saiba mais sobre as metas de desempenho e escalabilidade para Arquivos do Azure, incluindo a capacidade, taxa de solicitação e limites de largura de banda de entrada e saída."
services: storage
documentationcenter: na
author: wmgries
manager: klaasl
editor: tamram
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: 381e96a0a777415b916e4093fe55aa0d355782a1
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2017
---
# <a name="azure-files-scalability-and-performance-targets"></a>Metas de desempenho e escalabilidade do Arquivos do Azure
O [Arquivos do Azure](storage-files-introduction.md) oferece compartilhamentos de arquivos totalmente gerenciados na nuvem, acessíveis por meio do protocolo SMB padrão no setor. Este artigo descreve as metas de escalabilidade e desempenho para Arquivos do Azure e Sincronização de Arquivos do Azure (visualização).

As metas de escalabilidade e desempenho listadas aqui são metas avançadas, mas podem ser afetadas por outras variáveis em sua implantação. Por exemplo, a taxa de transferência para um arquivo pode também ser limitada pela sua largura de banda de rede disponível, não apenas os servidores que hospedam o serviço de Arquivos do Azure. É altamente recomendável testar seu padrão de uso para determinar se a escalabilidade e o desempenho do Arquivos do Azure atende às suas necessidades. É nosso compromisso aumentar esses limites ao longo do tempo. Não hesite em fazer comentários, na seção de comentários abaixo ou no [UserVoice do Arquivos do Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files), sobre os limites que você gostaria que aumentássemos.

## <a name="azure-storage-account-scale-targets"></a>Metas de escalabilidade da conta de armazenamento do Azure
O recurso pai de um compartilhamento de arquivo do Azure é uma conta de armazenamento do Azure. Uma conta de armazenamento representa um pool de armazenamento do Azure que pode ser usado por vários serviços de armazenamento, incluindo Arquivos do Azure, para armazenar dados. Outros serviços que armazenam dados em contas de armazenamento são o armazenamento de Blobs do Azure, armazenamento de Filas do Azure e armazenamento de Tabelas do Azure. As metas a seguir se aplicam a todos os serviços de armazenamento que armazenam dados em uma conta de armazenamento:

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> A utilização da conta de armazenamento por outros serviços de armazenamento afeta seus os compartilhamentos de arquivo do Azure em sua conta de armazenamento. Por exemplo, se você atingir a capacidade máxima da conta de armazenamento com o armazenamento de Blobs do Azure, você não poderá criar novos arquivos em seu compartilhamento de arquivo do Azure, mesmo se o compartilhamento de arquivo do Azure estiver abaixo do tamanho máximo do compartilhamento.

## <a name="azure-files-scale-targets"></a>Destinos de escala de Arquivos do Azure
[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Destinos de escala de Sincronização de Arquivos do Azure
Com a Sincronização de Arquivos do Azure, fizemos o possível para conseguir um design com uso ilimitado, no entanto, isso nem sempre é possível. A tabela a seguir indica os limites de nossos testes e as metas que realmente são limites fixos:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="see-also"></a>Consulte também
- [Planejando uma implantação de Arquivos do Azure](storage-files-planning.md)
- [Planejando uma implantação da Sincronização de Arquivos do Azure](storage-sync-files-planning.md)
- [Metas de desempenho e escalabilidade para outros serviços de armazenamento](../common/storage-scalability-targets.md)