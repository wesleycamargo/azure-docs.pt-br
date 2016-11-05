---
title: Tabela de limites de Backup do Azure
description: Descreve os limites do sistema para Backup do Azure.
services: backup
documentationcenter: NA
author: Jim-Parker
manager: jwhit
editor: ''

ms.service: backup
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/05/2015
ms.author: trinadhk
;"jimpark";: ''
"\"aashishr\"": ''

---
Os limites a seguir se aplicam ao Backup do Azure.

| Identificador de limite | Limite padrão |
| --- | --- |
| Número de servidores/computadores que podem ser registrados em cada cofre |50 por Windows Server/Client/SCDPM <br/> 200 para VMs de IaaS |
| Tamanho de uma fonte de dados para dados armazenados no armazenamento de cofre do Azure |Máximo de 54400 GB <sup>1</sup> |
| Número de cofres de backup que podem ser criados em cada assinatura do Azure |25 |
| Número de vezes que o backup pode ser agendado por dia |Três vezes por dia para Windows Server/Cliente <br/> Duas vezes por dia para SCDPM <br/> Uma vez por dia para VMs de IaaS |
| Discos de dados conectados a uma máquina virtual do Azure para backup |16 |

* <sup>1</sup>O limite de 54400 GB não se aplica ao backup de VM de IaaS.

<!---HONumber=Oct15_HO3-->