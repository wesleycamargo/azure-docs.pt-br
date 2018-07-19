---
title: Atualização de sistema operacional do SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Executar a atualização de sistema operacional para o SAP HANA no Azure (Instâncias Grandes)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cff9be3b074dde4a0335675663133a8df81ae62d
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114585"
---
# <a name="operating-system-upgrade"></a>Atualização do Sistema Operacional
Este documento descreve os detalhes sobre as atualizações de sistema operacional em Instâncias Grandes de HANA.

>[!NOTE]
>A atualização do sistema operacional é de responsabilidade do cliente, o suporte a operações da Microsoft pode orientá-lo para as áreas principais a serem observadas durante a atualização. Antes de planejar uma atualização, você deve consultar o fornecedor do sistema operacional.

No momento do provisionamento da unidade HLI, a equipe de operações da Microsoft instalará o sistema operacional. Ao longo do tempo, você deverá manter o sistema operacional (exemplo: patches, ajustes, atualizações etc.) na unidade HLI.

Antes das principais mudanças do sistema operacional (por exemplo, Atualização SP1 a SP2), você deve contatar a equipe da Microsoft Operations ao abrir m tíquete de suporte para consultar.


Para a matriz de suporte das diferentes versões do SAP HANA com as diferentes versões do Linux, consulte [Nota SAP nº 2235581](https://launchpad.support.sap.com/#/notes/2235581).


## <a name="known-issues"></a>Problemas conhecidos

A seguir estão alguns problemas conhecidos comuns durante a atualização:
- Na SKU de classe Tipo II, o Software Foundation Server (SFS) é removido após a atualização do sistema operacional. Depois de atualizar o sistema operacional, você deverá reinstalar o SFS compatível.
- Drivers de placa Ethernet (ENIC e FNIC) revertidos para a versão mais antiga. Você deverá reinstalar a versão compatível dos drivers após a atualização.

## <a name="next-steps"></a>Próximas etapas
- Consulte [Backup e restauração](hana-overview-high-availability-disaster-recovery.md) para classe de SKU Tipo I de backup de sistema operacional.
- Consulte [Backup de Sistema Operacional para classe de SKU Tipo II](os-backup-type-ii-skus.md).