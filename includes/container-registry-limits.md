---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 05/29/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 92a5d162e7a0b2c752a2f8e9c5941edf43e539e3
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991061"
---
| Recurso | Basic | Standard | Premium |
|---|---|---|---|---|
| Armazenamento | 10 GiB | 100 GiB| 500 GiB |
| Tamanho máximo de camada de imagem | 20 GiB | 20 GiB | 50 GiB |
| ReadOps por minuto<sup>1, 2</sup> | 1.000 | 3.000 | 10.000 |
| WriteOps por minuto<sup>1, 3</sup> | 100 | 500 | 2.000 |
| MBps de largura de banda de download<sup>1</sup> | 30 | 60 | 100 |
| MBps de largura de banda de upload<sup>1</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 100 |
| Replicação geográfica | N/D | N/D | [Com suporte](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication) |

<sup>1</sup> *ReadOps*, *WriteOps* e *Largura de banda* são estimativas mínimas. O ACR se esforça para melhorar o desempenho conforme os requisitos de uso.

<sup>2</sup> [docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) se traduz em várias operações de leitura com base no número de camadas na imagem, além da recuperação do manifesto.

<sup>3</sup> [docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) se traduz em várias operações de gravação com base no número de camadas que devem ser enviadas por push. Um `docker push` inclui *ReadOps* para recuperar um manifesto para uma imagem existente.
