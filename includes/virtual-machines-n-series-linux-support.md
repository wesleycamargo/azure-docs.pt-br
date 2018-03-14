---
title: "Arquivo de inclusão"
description: "Arquivo de inclusão"
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 03/01/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 22d37ca30f1319f46a52b96be1c527f6f56719ab
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2018
---
## <a name="supported-distributions-and-drivers"></a>Distribuições e drivers com suporte

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-cuda-drivers"></a>Séries NC, NCv2, NCv3 e ND - drivers NVIDIA CUDA
| Distribuição | Driver |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 ou 7.4<br/><br/> CentOS 7.3 ou 7.4 | NVIDIA CUDA 9.1, ramificação do driver R390 |

> [!IMPORTANT]
> Certifique-se de instalar ou atualizar para os drivers mais recentes do CUDA para a sua distribuição. Os drivers mais antigos que a versão R390 poderão apresentar problemas com kernels do Linux atualizados.
>

### <a name="nv-series---nvidia-grid-drivers"></a>Série NV - drivers NVIDIA GRID

| Distribuição | Driver |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3<br/><br/>CentOS 7.3 | NVIDIA GRID 5.2, ramificação do driver R384|

> [!NOTE]
> A Microsoft redistribui os instaladores de driver NVIDIA GRID para VMs de NV. Instale somente esses drivers GRID em VMs de NV do Azure. Esses drivers incluem o licenciamento de Software de GPU Virtual de GRID no Azure.
>

> [!WARNING] 
> A instalação de software de terceiros em produtos do Red Hat pode afetar os termos de suporte do Red Hat. Consulte o [artigo da Base de conhecimento do Red Hat](https://access.redhat.com/articles/1067).
>
