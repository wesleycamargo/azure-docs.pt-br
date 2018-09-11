---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: container-service
author: mmacy
ms.service: container-service
ms.topic: include
ms.date: 08/31/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 1e8913d31677f3da9b6eb9d2216d8d9ec8b186b4
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2018
ms.locfileid: "43667003"
---
| Recurso | Limite padrão |
| --- | :--- |
| Máximo de clusters por assinatura | 100 |
| Nós máx por cluster | 100 |
| Máximo de pods por nó: [Rede básica][basic-networking] com Kubenet | 110 |
| Máximo de pods por nó: [Rede avançada][advanced-networking] com CNI do Azure | Implantação da CLI do Azure: 110<sup>1</sup><br />Modelo do Resource Manager: 110<sup>1</sup><br />Implantação do portal: 30 |

<sup>1</sup> Esse valor é configurável na implantação de cluster durante a implantação de um cluster do AKS com a CLI do Azure ou um modelo do Resource Manager.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
