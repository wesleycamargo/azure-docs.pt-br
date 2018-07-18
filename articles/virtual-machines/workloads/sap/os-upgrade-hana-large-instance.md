---
title: Atualização de sistema operacional do SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Executar a atualização de sistema operacional para o SAP HANA no Azure (Instâncias Grandes)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1354b67ade78a0da1578ef9d98c3b1e8edb41cd4
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077741"
---
# <a name="operating-system-upgrade"></a>Atualização do Sistema Operacional
Este documento descreve os detalhes sobre as atualizações de sistema operacional em Instâncias Grandes de HANA.

>[!NOTE]
>A atualização do sistema operacional é de responsabilidade do cliente, o suporte a operações da Microsoft pode orientá-lo para as áreas principais a serem observadas durante a atualização. Antes de planejar uma atualização, você deve consultar o fornecedor do sistema operacional.

No momento do provisionamento da unidade HLI, a equipe de operações da Microsoft instalará o sistema operacional. Ao longo do tempo, você deverá manter o sistema operacional (exemplo: patches, ajustes, atualizações etc.) na unidade HLI.

Antes de fazer as alterações principais no sistema operacional (por exemplo, atualizar um sistema operacional), você **deverá** considerar a seguinte matriz de compatibilidade. Você **deverá** também contatar a equipe de Operações da Microsoft abrindo um tíquete de suporte para consultar antes de iniciar as atividades principais do sistema operacional, como atualização.

Para a matriz de suporte das diferentes versões do SAP HANA com as diferentes versões do Linux, consulte [Nota SAP nº 2235581](https://launchpad.support.sap.com/#/notes/2235581).

A compatibilidade a seguir foi testada para os HLIs. Se o servidor de HLI estiver fora da matriz de compatibilidade, entre em contato com o suporte de operação da Microsoft.

## <a name="for-type-i-class-sku-category"></a>Para a categoria de SKU de classe Tipo I

| Configuração | SUSE12 SP1 | SUSE12 SP2 | RHEL 7.2 | RHEL 7.3|
| --- | --- | --- | --- | --- |
| Firmware de Servidor | 3.1(2b) | 3.1(2b) | 3.1(2b) | 3.1(2b) |
| Versão de ENIC | 2.3.0.44 | 2.3.0.44 | 2.3.0.30 | 2.3.0.44 |
| Versão de FNIC | 1.6.0.34 | 1.6.0.34 | 1.6.0.27 | 1.6.0.36 |
| EDAC | Desabilitado | Desabilitado | Desabilitado | Desabilitado |
| Versão do kernel | 4.4.21-69-default | 3.12.49-11-default | 3.10.0-327.el7.x86_64 | 3.10.0-693.17.1 |


## <a name="for-type-ii-class-sku-category"></a>Para a categoria de SKU de classe Tipo II

| Configuração | SUSE12 SP1 | SUSE12 SP2 | RHEL 7.2 | RHEL 7.3|
| --- | --- | --- | --- | --- |
| Versão do Firmware de RMC | 1.1.121  | 1.1.121  | 1.1.121  | 1.1.121 |
| Versão do Firmware de BMC | 1.0.43   | 1.0.43   | 1.0.43   | 1.0.43  |
| Versão de Software Foundation Server (SFS) | 2.16    | 2.16    | 2.14/2.16   | 2.16   |
| BIOS | 5.2.6    | 5.2.6    | 5.2.6    | 5.2.6   |
| Versão de i40e    | 2.0.19     | 2.0.19     | 1.5.10-k    | 1.5.10-k   |
| Versão do kernel    | 3.12.49-11.1     | 4.4.21-69.1     | 3.10.0-327    | 3.10.0-693.17.1   |


## <a name="known-issues"></a>Problemas conhecidos

A seguir estão alguns problemas conhecidos comuns durante a atualização:
- Na SKU de classe Tipo II, o Software Foundation Server (SFS) é removido após a atualização do sistema operacional. Depois de atualizar o sistema operacional, você deverá reinstalar o SFS compatível.
- Drivers de placa Ethernet (ENIC e FNIC) revertidos para a versão mais antiga. Você deverá reinstalar a versão compatível dos drivers após a atualização.

## <a name="next-steps"></a>Próximas etapas
- Consulte [Backup e restauração](hana-overview-high-availability-disaster-recovery.md) para classe de SKU Tipo I de backup de sistema operacional.
- Consulte [Backup de Sistema Operacional para classe de SKU Tipo II](os-backup-type-ii-skus.md).