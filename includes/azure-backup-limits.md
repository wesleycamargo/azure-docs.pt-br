---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: backup
author: rayne-wiselman
ms.service: backup
ms.topic: include
ms.date: 12/07/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 9ad1208283527f35e04dede2706fd0e424096dc5
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54300632"
---
Os limites a seguir se aplicam ao Backup do Azure.

| **Limite** | **Padrão** |
| --- | --- |
| Servidores/computadores que podem ser registrados em um cofre | Windows Server/Windows Client/System Center DPM: 50 <br/><br/> VMs de IaaS: 1000  |
| Tamanho de uma fonte de dados no armazenamento de cofre |Máximo de 54400 GB. O limite de 54400 GB não se aplica ao backup de VM de IaaS |
| Cofres de backup em uma assinatura do Azure |500 cofres por região |
| Agendar backups diários |Windows Server/Client: 3 vezes por dia<br/> System Center DPM: 2 vezes por dia <br/> VMs de IaaS: Uma vez por dia  |
| Discos de dados conectados a uma máquina virtual do Azure para backup | 16 |
| Discos de dados individuais conectados a uma máquina virtual do Azure para backup| 4095 GB|
