---
title: Habilitar ou desabilitar o monitoramento da VM do Azure
description: Descreve como habilitar ou desabilitar o monitoramento da VM do Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: kmouss
manager: timlt
editor: 
ms.assetid: 6ce366d2-bd4c-4fef-a8f5-a3ae2374abcc
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/08/2016
ms.author: kmouss
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 9b2fe579113d6ca6bfd27d82eb9d4657657d44ff
ms.contentlocale: pt-br
ms.lasthandoff: 05/25/2017

---
# <a name="enable-or-disable-azure-vm-monitoring"></a>Habilitar ou desabilitar o monitoramento da VM do Azure

Esta seção descreve como habilitar ou desabilitar o monitoramento de máquinas virtuais em execução no Azure. Você pode habilitar ou desabilitar o monitoramento usando o portal ou a interface de linha de comando do Azure para Mac, Linux e Windows (a CLI do Azure).

> [!IMPORTANT]
> Este documento descreve a versão 2.3 da Extensão de Diagnóstico do Linux, a qual está preterida. A versão 2.3 será suportada até 30 de junho de 2018.
>
> A versão 3.0 da Extensão de Diagnóstico do Linux pode ser ativada em vez disso. Para obter mais informações, consulte a [documentação](./diagnostic-extension.md).

## <a name="enable--disable-monitoring-through-the-azure-portal"></a>Habilite/desabilite o monitoramento por meio do portal do Azure

É possível habilitar o monitoramento da VM do Azure, que fornece dados sobre a sua instância em períodos de um minuto. (alterações de armazenamento se aplicam). Os dados detalhados de diagnóstico são então disponibilizados para a VM nos gráficos de portal ou por meio da API. Por padrão, o portal do Azure permite o monitoramento baseado em host de um conjunto limitado de métricas. É possível ativar o monitoramento de métricas dentro de uma VM enquanto a VM está em execução ou em estado parado.

* Abra o Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
* Na navegação à esquerda, clique em Máquinas virtuais.
* Na lista de máquinas virtuais, escolha uma instância em execução ou parada. A folha "máquina virtual" é aberta.
* Clique em Todas as configurações.
* Clique em Diagnóstico.
* Altere o status para Ativado ou Desativado. Nessa folha, você também pode escolher o nível de detalhes de monitoramento que deseja habilitar para a máquina virtual.

![Habilite/desabilite o monitoramento por meio do portal do Azure.][1]

## <a name="enable--disable-monitoring-with-azure-cli"></a>Habilitar/desabilitar o monitoramento com a CLI do Azure

Para habilitar o monitoramento de uma VM do Azure.

* Crie um arquivo (denominado PrivateConfig.json):

```json
{
        "storageAccountName":"the storage account to receive data",
        "storageAccountKey":"the key of the account"
}
```

* Ative a extensão através da CLI do Azure.

```azurecli
azure vm extension set myvm LinuxDiagnostic Microsoft.OSTCExtensions 2.3 --private-config-path PrivateConfig.json
```

Para obter mais informações, consulte [Usar a Extensão de Diagnóstico do Linux para monitorar os dados de desempenho e diagnóstico da VM do Linux](classic/diagnostic-extension-v2.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

<!--Image references-->
[1]: ./media/vm-monitoring/portal-enable-disable.png

