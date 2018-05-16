---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 05/01/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 7139ec67536a1c0e41c991db6d867b956f995c11
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2018
---
## <a name="supported-distributions-and-drivers"></a>Distribuições e drivers com suporte

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-cuda-drivers"></a>Séries NC, NCv2, NCv3 e ND - drivers NVIDIA CUDA

As informações sobre o driver CUDA na tabela a seguir são atuais no momento da publicação. Para os drivers CUDA mais recentes, visite o site da [NVIDIA](https://developer.nvidia.com/cuda-zone). Certifique-se de instalar ou atualizar para os drivers mais recentes do CUDA para a sua distribuição. 

> [!TIP]
> Como alternativa à instalação manual do driver CUDA em uma VM do Linux, você pode implantar uma imagem de [Máquina Virtual de Ciência de Dados](../articles/machine-learning/data-science-virtual-machine/overview.md) do Azure. As edições DSVM para Ubuntu 16.04 LTS ou CentOS 7.4 vem com drivers NVIDIA CUDA pré-instalados, a Biblioteca de Rede Neural Avançada CUDA Neural e outras ferramentas.

| Distribuição | Driver |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 ou 7.4<br/><br/> CentOS 7.3 ou 7.4, HPC baseado em CentOS 7.4 | NVIDIA CUDA 9.1, ramificação do driver R390 |

### <a name="nv-series---nvidia-grid-drivers"></a>Série NV - drivers NVIDIA GRID

A Microsoft redistribui os instaladores de driver NVIDIA GRID para VMs de NV. Instale somente esses drivers GRID em VMs de NV do Azure. Esses drivers incluem o licenciamento de Software de GPU Virtual de GRID no Azure.

| Distribuição | Driver |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3 ou 7.4<br/><br/>Com base em CentOS 7.3 ou 7.4 | NVIDIA GRID 6.0, branch do driver R390|



> [!WARNING] 
> A instalação de software de terceiros em produtos do Red Hat pode afetar os termos de suporte do Red Hat. Consulte o [artigo da Base de conhecimento do Red Hat](https://access.redhat.com/articles/1067).
>
