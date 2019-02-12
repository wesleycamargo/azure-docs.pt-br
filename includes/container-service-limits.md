---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 10/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 23c25953d2f493d2dd799bfd11dbbb69db002d1b
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55736209"
---
| Recurso | Limite padrão |
| --- | :--- |
| Máximo de clusters por assinatura | 100 |
| Nós máx por cluster | 100 |
| Pods máximos por nó: [Rede básica][basic-networking] com Kubenet | 110 |
| Pods máximos por nó: [Rede avançada][advanced-networking] com CNI do Azure | Implantação da CLI do Azure: 30<sup>1</sup><br />Modelo do Resource Manager: 30<sup>1</sup><br />Implantação do portal: 30 |

<sup>1</sup> Quando você implanta um cluster de AKS com a CLI do Azure ou com um modelo do Resource Manager esse valor pode ser configurado para até **110 pods por nó**. Não é possível configurar os pods máximos por nó após implantar um cluster de AKS ou se você implantar um cluster usando o portal do Azure.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
