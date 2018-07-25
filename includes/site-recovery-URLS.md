---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 06/28/2018
ms.author: raynew
ms.openlocfilehash: f7d6c3f68618fec839ccff06b73ba44d106999d2
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38765531"
---
| NOME | URL comercial | URL governamental | DESCRIÇÃO |
|---|---|---|---|
| AD do Azure | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | Usado para controle de acesso e gerenciamento de identidades com AAD |
| Backup | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | Usado para transferência de dados de replicação e coordenação |
| Replicação | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | Usado para operações de gerenciamento de replicação e coordenação |
| Armazenamento | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | Usado para acessar a conta de armazenamento que armazena os dados replicados |
| Telemetria (opcional) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | Usado para telemetria |

``time.nist.gov`` e ``time.windows.com`` são usados para verificar a sincronização de data/hora entre o tempo do sistema e o tempo global, em todas as implantações.


