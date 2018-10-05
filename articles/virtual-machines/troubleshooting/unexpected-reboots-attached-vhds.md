---
title: Solução de problemas de reinicializações inesperadas de VMs com VHDs anexados em VMs do Azure | Microsoft Docs
description: Como solucionar problemas de reinicializações inesperadas de VMs.
keywords: conexão ssh recusada, erro de ssh, ssh do azure, falha de conexão SSH
services: virtual-machines
author: genlin
manager: cshepard
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.topic: article
ms.date: 05/01/2018
ms.author: genli
ms.openlocfilehash: cffc6166ab7d0864646421b35fbecafdd80eb27e
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47411086"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Solucionar problemas de reinicializações inesperadas de VMs com VHDs anexados

Se uma Máquina Virtual (VM) do Azure tem um grande número de VHDs anexados que estão na mesma conta de armazenamento, você pode exceder os destinos de escalabilidade para uma conta de armazenamento individuais, fazendo com que a VM reinicie inesperadamente. Verifique as métricas de minuto da conta de armazenamento (**TotalRequests**/**TotalIngress**/**TotalEgress**) para ver se há picos que excedem as metas de escalabilidade de uma conta de armazenamento. Consulte [Métricas mostram um aumento no PercentThrottlingError](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) para assistência para determinar se a limitação ocorreu em sua conta de armazenamento.

Em geral, cada operação de entrada ou saída individual em um VHD a partir de uma máquina virtual se traduz em operações **Get Page** ou **Put Page** no blob de página subjacente. Portanto, você pode usar os IOPS estimados para o seu ambiente para ajustar quantos VHDs você pode ter em uma única conta de armazenamento com base no comportamento específico do seu aplicativo. A Microsoft recomenda ter 40 ou menos discos em uma única conta de armazenamento. Consulte [Metas de desempenho e escalabilidade do Armazenamento do Microsoft Azure](../../storage/common/storage-scalability-targets.md) para obter detalhes sobre as metas de escalabilidade atuais para contas de armazenamento, em particular a largura de banda total e a taxa de solicitação total para o tipo de conta de armazenamento que você está usando.

Se você estiver excedendo as metas de escalabilidade para sua conta de armazenamento, coloque seus VHDs em várias contas de armazenamento para reduzir a atividade de cada conta individual.
