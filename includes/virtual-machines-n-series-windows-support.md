---
title: "Arquivo de inclusão"
description: "Arquivo de inclusão"
services: virtual-machines-windows
author: dlepow
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 03/01/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 506c2a4cf675a347dc4c45c9ccf8bce95de2f6fc
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2018
---
## <a name="supported-operating-systems-and-drivers"></a>Sistemas operacionais e drivers com suporte

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-tesla-drivers"></a>Série NC, NCv2, NCv3 e ND - drivers NVIDIA Tesla

| SO | Driver |
| -------- |------------- |
| Windows Server 2016 | [385.54](http://us.download.nvidia.com/Windows/Quadro_Certified/385.54/385.54-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [385.54](http://us.download.nvidia.com/Windows/Quadro_Certified/385.54/385.54-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

> [!NOTE]
> Os links de download do driver Tesla são atuais no momento da publicação. Para os drivers mais recentes, visite o site da [NVIDIA](http://www.nvidia.com/).
>

### <a name="nv-series---nvidia-grid-drivers"></a>Série NV - drivers NVIDIA GRID

| SO | Driver |
| -------- |------------- |
| Windows Server 2016 | [GRID 5.2 (386.09)](https://go.microsoft.com/fwlink/?linkid=836843) (.exe) |
| Windows Server 2012 R2 | [GRID 5.2 (386.09)](https://go.microsoft.com/fwlink/?linkid=836844) (.exe)  |

> [!NOTE]
> A Microsoft redistribui os instaladores de driver NVIDIA GRID para VMs de NV. Instale somente esses drivers GRID em VMs de NV do Azure. Esses drivers incluem o licenciamento de Software de GPU Virtual de GRID no Azure.
>