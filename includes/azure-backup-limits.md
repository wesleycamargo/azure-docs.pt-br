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
ms.openlocfilehash: 1f41567f7d5dcc9b6536b47ef639ba53abff8ec4
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38755443"
---
Os limites a seguir se aplicam ao Backup do Azure.

| Identificador de limite | Limite padrão |
| --- | --- |
| Número de servidores/computadores que podem ser registrados em cada cofre |50 por Windows Server/Client/SCDPM  <br/> 1000 para VMs IaaS |
| Tamanho de uma fonte de dados para dados armazenados no armazenamento de cofre do Azure |Máximo de 54400 GB <sup>1</sup> |
| Número de cofres de backup que podem ser criados em cada assinatura do Azure |500 cofres de Serviços de Recuperação por região |
| Número de vezes que o backup pode ser agendado por dia |Três vezes por dia para Windows Server/Cliente  <br/> 2 por dia para SCDPM <br/> Uma vez por dia para VMs de IaaS |
| Discos de dados conectados a uma máquina virtual do Azure para backup |16 |
| Tamanho dos discos de dados individuais conectados a uma máquina virtual do Azure para backup| 4095 GB <sup>2</sup>|

* <sup>1</sup>O limite de 54400 GB não se aplica ao backup de VM de IaaS.
 

