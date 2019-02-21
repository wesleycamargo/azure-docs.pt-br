---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3d100ec08b7b6d70366e605daf9c67edc6ab3bf3
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2019
ms.locfileid: "56331061"
---
As contas de Armazenamento Premium têm as seguintes metas de escalabilidade:

| Capacidade total da conta | Largura de banda total para uma conta de armazenamento com redundância local |
| --- | --- | 
| Capacidade do disco: 35 TB <br>Capacidade do instantâneo: 10 TB | Até 50 gigabits para entrada<sup>1</sup> + saída<sup>2</sup> |

<sup>1</sup> Todos os dados (solicitações) que são enviados para uma conta de armazenamento

<sup>2</sup> Todos os dados (respostas) que são recebidos de uma conta de armazenamento

Se você estiver usando contas de Armazenamento Premium para discos não gerenciados, e seu aplicativo ultrapassar as metas de escalabilidade de uma única conta de armazenamento, convém migrar para discos gerenciados. Se você não deseja migrar discos gerenciados, crie seu aplicativo para usar várias contas de armazenamento. Em seguida, particione os dados nessas contas de armazenamento. Por exemplo, se você quiser anexar discos de 51 TB em várias VMs, separe-as entre duas contas de armazenamento. O limite para uma conta de armazenamento único premium é de 35 TB. Certifique-se de que uma única conta de Armazenamento Premium nunca tenha mais do que 35 TB de discos provisionados.