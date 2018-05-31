---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines-windows
author: dlepow
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 05/16/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 9b36a8e90c20c8f0d08ca241e48e9306fb5b10f8
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34307498"
---
## <a name="supported-operating-systems-and-drivers"></a>Sistemas operacionais e drivers com suporte

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-tesla-cuda-drivers"></a>NC, NCv2, NCv3 e série ND - drivers NVIDIA Tesla (CUDA)

Os links de download do driver na tabela a seguir são atuais no momento da publicação. Para os drivers mais recentes, visite o site da [NVIDIA](http://www.nvidia.com/).

> [!TIP]
> Como alternativa à instalação manual do driver CUDA em uma VM do Windows Server, você pode implantar uma imagem da [Máquina Virtual de Ciência de Dados](../articles/machine-learning/data-science-virtual-machine/overview.md) do Azure. As edições DSVM do Windows Server 2016 instalam previamente os drivers NVIDIA CUDA, a Biblioteca de Rede Neural Profunda CUDA e outras ferramentas.


| SO | Driver |
| -------- |------------- |
| Windows Server 2016 | [391.29](http://us.download.nvidia.com/Windows/Quadro_Certified/391.29/391.29-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [391.29](http://us.download.nvidia.com/Windows/Quadro_Certified/391.29/391.29-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

### <a name="nv-series---nvidia-grid-drivers"></a>Série NV - drivers NVIDIA GRID

A Microsoft redistribui os instaladores de driver NVIDIA GRID para VMs de NV. Instale somente esses drivers GRID em VMs de NV do Azure. Esses drivers incluem o licenciamento de Software de GPU Virtual de GRID no Azure.

| SO | Driver |
| -------- |------------- |
| Windows Server 2016<br/><br/>Windows 10 | [GRID 6.0 (391.03)](https://go.microsoft.com/fwlink/?linkid=836843) (.exe) |
| Windows Server 2012 R2 | [GRID 6.0 (391.03)](https://go.microsoft.com/fwlink/?linkid=836844) (.exe)  |