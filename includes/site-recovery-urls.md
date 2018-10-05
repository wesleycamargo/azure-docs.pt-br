---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 09/12/2018
ms.author: raynew
ms.openlocfilehash: ae8eebf9667f2bc03fdc1082fb38c19c5c645c10
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47060976"
---
**Nome** | **URL Comercial**  | **URL Governamental** | **Descrição** |
--- | --- | --- | ---
AD do Azure | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | Usado para controle de acesso e gerenciamento de identidades com AAD 
Backup | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | Usado para transferência de dados de replicação e coordenação 
Replicação | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | Usado para operações de gerenciamento de replicação e coordenação 
Armazenamento | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | Usado para acessar a conta de armazenamento que armazena os dados replicados 
Telemetria (opcional) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | Usado para telemetria 
Sincronização de horário | ``time.windows.com`` | ``time.nist.gov`` | Ssed para verificar a sincronização de data entre a hora do sistema e a global, em todas as implantações.


