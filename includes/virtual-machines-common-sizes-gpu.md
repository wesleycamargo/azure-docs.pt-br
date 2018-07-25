---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 07/06/2018
ms.author: danlep;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 268da0e5d078f7b6b4b36929dbf6755068adb444
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2018
ms.locfileid: "37907167"
---
Os tamanhos de VM otimizados para GPU são máquinas virtuais especializadas disponíveis com um ou vários GPUs NVIDIA. Esses tamanhos são projetados para cargas de trabalho de visualização e com muita computação e muitos gráficos. Este artigo fornece informações sobre o número e o tipo de GPUs, vCPUs, discos de dados e NICs. A taxa de transferência de armazenamento e a largura de banda de rede também são incluídos para cada tamanho neste agrupamento. 

* Os tamanhos **NC, NCv2, NCv3 e ND** são otimizados para aplicativos e algoritmos que fazem uso intensivo de rede e computação. Alguns exemplos são aplicativos baseados em CUDA e OpenCL e simulações, AI e Aprendizagem Profunda. 
* Os tamanhos **NV** são otimizados e projetados para cenários de visualização remota, streaming, jogos, codificação e VDI usando estruturas como OpenGL e DirectX.  


## <a name="nc-series"></a>Série NC

Armazenamento Premium: sem suporte

Cache de Armazenamento Premium: sem suporte

VMs da série NC têm a tecnologia do cartão [NVIDIA Tesla K80](http://images.nvidia.com/content/pdf/kepler/Tesla-K80-BoardSpec-07317-001-v05.pdf). Os usuários podem analisar dados mais rapidamente tirando proveito do CUDA para aplicativos de exploração de energia, simulações de falhas, renderização com ray tracing, aprendizado aprofundado e muito mais. A configuração NC24r fornece um adaptador de rede de alta taxa de transferência e baixa latência, otimizado para cargas de trabalho de computação paralela firmemente acopladas.


| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | GPU | Discos de dados máximos | Máximo de NICs |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 340 | 1 | 24 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 | 48 | 2 |
| Standard_NC24 |24 |224 | 1440 | 4 | 64 | 4 |
| Standard_NC24r* |24 |224 | 1440 | 4 | 64 | 4 |

1 GPU = metade de um cartão K80.

*Compatível com RDMA

## <a name="ncv2-series"></a>Série NCv2

Armazenamento Premium: com suporte

Cache de Armazenamento Premium: com suporte

VMs da série NCv2 têm a tecnologia de GPUs [NVIDIA Tesla P100](http://images.nvidia.com/content/tesla/pdf/nvidia-tesla-p100-datasheet.pdf). Essas GPUs podem fornecer desempenho computacional 2 vezes maior que da série NC. Os clientes podem aproveitar essas GPUs atualizadas para cargas de trabalho HPC tradicionais como modelagem de reservatório, sequenciamento de DNA, análise de proteína, simulações de Monte Carlo, entre outros. A configuração NC24rs v2 fornece um adaptador de rede de alta taxa de transferência e baixa latência, otimizado para cargas de trabalho de computação paralela firmemente acopladas.

> [!IMPORTANT]
> Para essa família de tamanho, a cota de vCPU (principal) em sua assinatura vem inicialmente definida como 0 em cada região. [Solicitar um aumento de cota de vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md) para essa família em uma [região disponível](https://azure.microsoft.com/regions/services/).
>

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | GPU | Discos de dados máximos | Máximo de NICs |
| --- | --- | --- | --- | --- | --- | ---  |
| Standard_NC6s_v2 |6 |112 | 736 | 1 | 12 | 4 |
| Standard_NC12s_v2 |12 |224 | 1474 | 2 | 24 | 8 |
| Standard_NC24s_v2 |24 |448 | 2948 | 4 | 32 | 8 |
| Standard_NC24rs_v2* |24 |448 | 2948 | 4 | 32 | 8 |

1 GPU = um cartão P100.

*Compatível com RDMA

## <a name="ncv3-series"></a>Série NCv3

Armazenamento Premium: com suporte

Cache de Armazenamento Premium: com suporte

VMs da série NCv3 têm a tecnologia de GPUs [NVIDIA Tesla V100](http://www.nvidia.com/content/PDF/Volta-Datasheet.pdf). Essas GPUs podem fornecer desempenho computacional 1,5 vezes maior da série NCv2. Os clientes podem aproveitar essas GPUs atualizadas para cargas de trabalho HPC tradicionais como modelagem de reservatório, sequenciamento de DNA, análise de proteína, simulações de Monte Carlo, entre outros. A configuração NC24rs v3 fornece um adaptador de rede de alta taxa de transferência e baixa latência, otimizado para cargas de trabalho de computação paralela firmemente acopladas.

> [!IMPORTANT]
> Para essa família de tamanho, a cota de vCPU (principal) em sua assinatura vem inicialmente definida como 0 em cada região. [Solicitar um aumento de cota de vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md) para essa família em uma [região disponível](https://azure.microsoft.com/regions/services/).
>

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | GPU | Discos de dados máximos | Máximo de NICs |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 |6 |112 | 736 | 1 | 12 | 4 |
| Standard_NC12s_v3 |12 |224 | 1474 | 2 | 24 | 8 |
| Standard_NC24s_v3 |24 |448 | 2948 | 4 | 32 | 8 | 
| Standard_NC24rs_v3* |24 |448 | 2948 | 4 | 32 | 8 |

1 GPU = um cartão V100.

*Compatível com RDMA

## <a name="nd-series"></a>Série ND

Armazenamento Premium: com suporte

Cache de Armazenamento Premium: com suporte

As máquinas virtuais da série ND são uma novidade da família GPU, criadas para cargas de trabalho de AI e Aprendizagem Profunda. Elas oferecem um desempenho excelente para treinamento e Inferência. Instâncias de ND também têm a tecnologia das GPUs [NVIDIA Tesla P40](http://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf). Essas instâncias oferecem um desempenho excelente para operações de ponto flutuante de precisão simples, para cargas de trabalho de AI que utilizam o Cognitive Toolkit, o TensorFlow, o Caffe e outras estruturas. A série ND também oferece um tamanho de memória de GPU muito maior (24 GB), permitindo usar modelos de rede neural muito maiores. Como a série NC, a série ND oferece uma configuração com uma baixa latência secundária, uma rede com alta taxa de transferência por meio de RDMA e a conectividade InfiniBand, permitindo executar trabalhos de grande escala que abrangem várias GPUs.

> [!IMPORTANT]
> Para essa família de tamanho, a cota de vCPU (principal) por região em sua assinatura vem inicialmente definida como 0. [Solicitar um aumento de cota de vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md) para essa família em uma [região disponível](https://azure.microsoft.com/regions/services/).
>

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | GPU | Discos de dados máximos | Máximo de NICs |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s |6 |112 | 736 | 1 | 12 | 4 |
| Standard_ND12s |12 |224 | 1474 | 2 | 24 | 8 | 
| Standard_ND24s |24 |448 | 2948 | 4 | 32 | 8 |
| Standard_ND24rs* |24 |448 | 2948 | 4 | 32 | 8 |

1 GPU = um cartão de P40.

*Compatível com RDMA

## <a name="nv-series"></a>Série NV

Armazenamento Premium: sem suporte

Cache de Armazenamento Premium: sem suporte

As máquinas virtuais da série NV têm a tecnologia das GPUs [ Tesla M60 da NVIDIA ](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) e NVIDIA GRID para aplicativos acelerados de área de trabalho e áreas de trabalho virtuais, em que os clientes podem visualizar seus dados ou simulações. Os usuários podem visualizar seus fluxos de trabalho com uso intensivo de gráficos em instâncias NV para obter capacidade gráfica superior, além de executar cargas de trabalho de precisão única, como codificação e renderização. 

Cada GPU em instâncias NV vem com uma licença GRID. Esta licença oferece flexibilidade para usar uma instância NV como uma estação de trabalho virtual para um único usuário ou 25 usuários simultâneos podem se conectar à VM para um cenário de aplicativo virtual.

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | GPU | Discos de dados máximos | Máximo de NICs | Estações de trabalho virtuais | Aplicativos virtuais | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |340 | 1 | 24 | 1 | 1 | 25 |
| Standard_NV12 |12 |112 |680 | 2 | 48 | 2 | 2 | 50 |
| Standard_NV24 |24 |224 |1440 | 4 | 64 | 4 | 4 | 100 |

1 GPU = metade de um cartão M60.

 
