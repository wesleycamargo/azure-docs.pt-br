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
ms.openlocfilehash: 4251f379c517d5ccfd0430987e3d5280208590ff
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49400337"
---
| Recurso | Limite padrão |
| --- | :--- |
| Máximo de clusters por assinatura | 100 |
| Nós máx por cluster | 100 |
| Máximo de pods por nó: [Rede básica][basic-networking] com Kubenet | 110 |
| Máximo de pods por nó: [Rede avançada][advanced-networking] com CNI do Azure | Implantação da CLI do Azure: 30<sup>1</sup><br />Modelo do Resource Manager: 30<sup>1</sup><br />Implantação do portal: 30 |

<sup>1</sup> Quando você implanta um cluster de AKS com a CLI do Azure ou com um modelo do Resource Manager esse valor pode ser configurado para até **110 pods por nó**. Não é possível configurar os pods máximos por nó após implantar um cluster de AKS ou se você implantar um cluster usando o portal do Azure.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
