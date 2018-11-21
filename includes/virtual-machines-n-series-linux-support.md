---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 11/08/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: c829b8d6fedaabfb9b43c6352c8188128cf36701
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2018
ms.locfileid: "51333733"
---
## <a name="supported-distributions-and-drivers"></a>Distribuições e drivers com suporte

### <a name="nvidia-cuda-drivers"></a>Drivers NVIDIA CUDA

Drivers NVIDIA Tesla para NC, NCv2, NCv3 e VMs da série ND (opcional para a série NV) têm suporte apenas nas distribuições de Linux listadas na tabela a seguir. As informações sobre o driver CUDA são atuais no momento da publicação. Para os drivers CUDA mais recentes, visite o site da [NVIDIA](https://developer.nvidia.com/cuda-zone). Certifique-se de instalar ou atualizar para os drivers mais recentes do CUDA para a sua distribuição. 

> [!TIP]
> Como alternativa à instalação manual do driver CUDA em uma VM do Linux, você pode implantar uma imagem de [Máquina Virtual de Ciência de Dados](../articles/machine-learning/data-science-virtual-machine/overview.md) do Azure. As edições DSVM para Ubuntu 16.04 LTS ou CentOS 7.4 vem com drivers NVIDIA CUDA pré-instalados, a Biblioteca de Rede Neural Avançada CUDA Neural e outras ferramentas.

| Distribuição | Driver |
| --- | -- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 ou 7.4<br/><br/> CentOS 7.3 ou 7.4, HPC baseado em CentOS 7.4 | NVIDIA CUDA 10.0, branch do driver R410 |

### <a name="nvidia-grid-drivers"></a>Drivers NVIDIA GRID

A Microsoft redistribui os instaladores do driver NVIDIA GRID para VMs das séries NV e NVv2 usadas como estações de trabalho virtuais ou para aplicativos virtuais. Instale somente esses drivers de grade em VMs do Azure NV, apenas em sistemas operacionais listados na tabela a seguir. Esses drivers incluem o licenciamento de Software de GPU Virtual de GRID no Azure. Não é necessário você configurar um servidor de licença de software vGPU NVIDIA.

| Distribuição | Driver |
| --- | -- |
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3 ou 7.4<br/><br/>Com base em CentOS 7.3 ou 7.4 | NVIDIA GRID 6.2, branch do driver R390|

> [!WARNING] 
> A instalação de software de terceiros em produtos do Red Hat pode afetar os termos de suporte do Red Hat. Consulte o [artigo da Base de conhecimento do Red Hat](https://access.redhat.com/articles/1067).
>
