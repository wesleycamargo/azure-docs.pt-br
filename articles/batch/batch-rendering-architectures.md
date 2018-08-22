---
title: Renderização do Azure - arquiteturas de referência
description: Arquiteturas para usar o Lote do Microsoft Azure e outros serviços do Azure para estender um farm de renderização local disparando na nuvem
services: batch
author: davefellows
manager: jeconnoc
ms.author: danlep
ms.date: 08/13/2018
ms.topic: conceptual
ms.openlocfilehash: 0fe101ee6eb88094034b90c4d39f06ba509c9512
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2018
ms.locfileid: "40099610"
---
# <a name="reference-architectures-for-azure-rendering"></a>Arquiteturas de referência para renderização do Azure

Este artigo mostra diagramas de arquitetura de alto nível para cenários para estender ou "disparar" um farm de renderização local para o Azure. Os exemplos mostram opções diferentes para serviços de armazenamento, rede e computação do Azure.

## <a name="hybrid-with-nfs-or-cfs"></a>Híbrido com NFS ou CFS

O diagrama a seguir mostra um cenário híbrido que inclui os serviços do Azure a seguir:

* **Computação** - Pool do Lote do Azure ou Conjunto de Dimensionamento de Máquinas Virtuais.

* **Rede** - Local: VPN ou Microsoft Azure ExpressRoute. Azure: VNET do Azure.

* **Armazenamento** - Arquivos de entrada e saída: NFS ou CFS usando VMs do Azure, sincronizados com armazenamento local por meio da Sincronização de Arquivos do Azure e RSync.

  ![Intermitência de nuvem - Híbrido com NFS ou CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs.png)

## <a name="hybrid-with-blobfuse"></a>Híbrido com Blobfuse

O diagrama a seguir mostra um cenário híbrido que inclui os serviços do Azure a seguir:

* **Computação** - Pool do Lote do Azure ou Conjunto de Dimensionamento de Máquinas Virtuais.

* **Rede** - Local: VPN ou Microsoft Azure ExpressRoute. Azure: VNET do Azure.

* **Armazenamento** - Arquivos de entrada e saída: armazenamento de Blobs, montado para computar recursos por meio do Azure Blobfuse.

  ![Intermitência de nuvem - Híbrido com Blobfuse](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Computação híbrida e armazenamento

O diagrama a seguir mostra um cenário híbrido totalmente conectado para computação e armazenamento e inclui os serviços do Azure a seguir:

* **Computação** - Pool do Lote do Azure ou Conjunto de Dimensionamento de Máquinas Virtuais.

* **Rede** - Local: VPN ou Microsoft Azure ExpressRoute. Azure: VNET do Azure.

* **Armazenamento** - Entre instalações: Avere vFXT. Arquivamento opcional de arquivos locais por meio do Azure Data Box para armazenamento de Blobs.

  ![Intermitência de nuvem - Computação híbrida e armazenamento](./media/batch-rendering-architectures/hybrid-compute-storage.png)


## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre como usar [Gerenciadores de renderização](batch-rendering-render-managers.md) com o Lote do Azure.

* Saiba mais sobre as opções para [Renderizar no Azure](batch-rendering-service.md).