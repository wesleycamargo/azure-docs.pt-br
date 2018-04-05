---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 2/7/2018
ms.author: trinadhk;sogup
ms.custom: include file
ms.openlocfilehash: b345283f87c446ff3b583b0c5dd8a4be222303ae
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
Os limites a seguir se aplicam ao Backup do Azure.

| Identificador de limite | Limite padrão |
| --- | --- |
| Número de servidores/computadores que podem ser registrados em cada cofre |50 por Windows Server/Client/SCDPM  <br/> 200 para VMs de IaaS |
| Tamanho de uma fonte de dados para dados armazenados no armazenamento de cofre do Azure |Máximo de 54400 GB <sup>1</sup> |
| Número de cofres de backup que podem ser criados em cada assinatura do Azure |25 cofres dos Serviços de Recuperação por região |
| Número de vezes que o backup pode ser agendado por dia |Três vezes por dia para Windows Server/Cliente  <br/> 2 por dia para SCDPM <br/> Uma vez por dia para VMs de IaaS |
| Discos de dados conectados a uma máquina virtual do Azure para backup |16 |
| Tamanho dos discos de dados individuais conectados a uma máquina virtual do Azure para backup| 4095 GB <sup>2</sup>|

* <sup>1</sup>O limite de 54400 GB não se aplica ao backup de VM de IaaS.
 

