---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 08/30/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: bddb17a5163333a5aeb86b296a21867f0611d12f
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43303884"
---
| Recurso | Basic | Standard | Premium |
|---|---|---|---|---|
| Armazenamento<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| Tamanho máximo de camada de imagem | 20 GiB | 20 GiB | 50 GiB |
| ReadOps por minuto<sup>2, 3</sup> | 1.000 | 3.000 | 10.000 |
| WriteOps por minuto<sup>2, 4</sup> | 100 | 500 | 2.000 |
| MBps de largura de banda de download<sup>2</sup> | 30 | 60 | 100 |
| MBps de largura de banda de upload<sup>2</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 100 |
| Replicação geográfica | N/D | N/D | [Com suporte][geo-replication] |
| Confiança do conteúdo (versão prévia) | N/D | N/D | [Com suporte][content-trust] |

<sup>1</sup> Os limites de armazenamento especificados são a quantidade de armazenamento *incluída* para cada camada. Será cobrada uma taxa diária adicional por GiB para o armazenamento de imagens acima desses limites. Para obter informações de taxa, consulte [Preço de Registro de Contêiner][pricing].

<sup>2</sup> *ReadOps*, *WriteOps* e *Largura de Banda* são estimativas mínimas. O ACR se esforça para melhorar o desempenho conforme os requisitos de uso.

<sup>3</sup> [docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) se traduz em várias operações de leitura com base no número de camadas na imagem, além da recuperação do manifesto.

<sup>4</sup> [docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) se traduz em várias operações de gravação com base no número de camadas que devem ser enviadas por push. Um `docker push` inclui *ReadOps* para recuperar um manifesto para uma imagem existente.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md