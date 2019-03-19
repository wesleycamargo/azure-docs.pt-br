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
ms.openlocfilehash: 62eb75ef18d3ac81be65783e57c21c0aefd7a429
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554654"
---
| Recurso | Limite padrão |
| --- | :--- |
| Clusters máximo por assinatura | 100 |
| Número máximo de nós por cluster | 100 |
| Máximo pods por nó: [Rede básica][basic-networking] com Kubenet | 110 |
| Máximo pods por nó: [Rede avançada] [ advanced-networking] com Interface de rede de contêiner do Azure | Implantação da CLI do Azure: 30<sup>1</sup><br />Modelo do Gerenciador de recursos do Azure: 30<sup>1</sup><br />Implantação do portal: 30 |

<sup>1</sup>quando você implanta um cluster do serviço de Kubernetes do Azure (AKS) com a CLI do Azure ou um modelo do Resource Manager, esse valor é configurável até 110 pods por nó. Depois que você já implantou um cluster do AKS, ou se você implantar um cluster usando o portal do Azure, é possível configurar máximo pods por nó.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
