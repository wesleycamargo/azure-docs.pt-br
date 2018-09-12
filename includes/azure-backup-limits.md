---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 9/10/2018
ms.author: trinadhk;sogup
ms.custom: include file
ms.openlocfilehash: 64101ea5a3bbaac4a6b2e349a04d06ea84a87081
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44381064"
---
Os limites a seguir se aplicam ao Backup do Azure.

| Identificador de limite | Limite padrão |
| --- | --- |
| Número de servidores/computadores que podem ser registrados em cada cofre |50 por Windows Server/Client/SCDPM  <br/> 1000 para VMs IaaS |
| Tamanho de uma fonte de dados para dados armazenados no armazenamento de cofre do Azure |Máximo de 54400 GB <sup>1</sup> |
| Número de cofres de backup que podem ser criados em cada assinatura do Azure |500 cofres de Serviços de Recuperação por região |
| Número de vezes que o backup pode ser agendado por dia |Três vezes por dia para Windows Server/Cliente  <br/> 2 por dia para SCDPM <br/> Uma vez por dia para VMs de IaaS |
| Discos de dados conectados a uma máquina virtual do Azure para backup |16 |
| Tamanho dos discos de dados individuais conectados a uma máquina virtual do Azure para backup| 4095 GB|

* <sup>1</sup>O limite de 54400 GB não se aplica ao backup de VM de IaaS.
 

