---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 11/27/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: df7d8815eeb588e4e99041844642b6721e25dad7
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52585617"
---
## <a name="supported-distributions-and-drivers"></a>Distribuições e drivers com suporte

### <a name="nvidia-cuda-drivers"></a>Drivers NVIDIA CUDA

Os drivers NVIDIA CUDA para VMs das séries NC, NCv2, NCv3, ND e NDv2 (opcional para a série NV) são suportados apenas nas distribuições Linux listadas na tabela a seguir. As informações sobre o driver CUDA são atuais no momento da publicação. Para os drivers CUDA mais recentes, visite o site da [NVIDIA](https://developer.nvidia.com/cuda-zone). Certifique-se de instalar ou atualizar para os drivers mais recentes do CUDA para a sua distribuição. 

> [!TIP]
> Como alternativa à instalação manual do driver CUDA em uma VM do Linux, você pode implantar uma imagem de [Máquina Virtual de Ciência de Dados](../articles/machine-learning/data-science-virtual-machine/overview.md) do Azure. As edições DSVM para Ubuntu 16.04 LTS ou CentOS 7.4 vem com drivers NVIDIA CUDA pré-instalados, a Biblioteca de Rede Neural Avançada CUDA Neural e outras ferramentas.

| Distribuição | Driver |
| --- | -- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 ou 7.4<br/><br/> CentOS 7.3 ou 7.4, HPC baseado em CentOS 7.4 | NVIDIA CUDA 10.0, branch do driver R410 |

### <a name="nvidia-grid-drivers"></a>Drivers NVIDIA GRID

A Microsoft redistribui os instaladores do driver NVIDIA GRID para VMs das séries NV e NVv2 usadas como estações de trabalho virtuais ou para aplicativos virtuais. Instale somente esses drivers de grade em VMs do Azure NV, apenas em sistemas operacionais listados na tabela a seguir. Esses drivers incluem o licenciamento de Software de GPU Virtual de GRID no Azure. Não é necessário você configurar um servidor de licença de software vGPU NVIDIA.

| Distribuição | Driver |
| --- | -- |
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3 ou 7.4<br/><br/>Com base em CentOS 7.3 ou 7.4 | NVIDIA GRID 7, driver branch R410|

> [!WARNING] 
> A instalação de software de terceiros em produtos do Red Hat pode afetar os termos de suporte do Red Hat. Consulte o [artigo da Base de conhecimento do Red Hat](https://access.redhat.com/articles/1067).
>
