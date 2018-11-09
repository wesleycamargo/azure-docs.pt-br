---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: batch
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 10/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: c98a2146a019817152be9fae76638dbaa4d9de3d
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49458815"
---
| **Recurso** | **Limite padrão** | **Limite máximo** |
| --- | --- | --- |
| Contas do Lote por região e assinatura | 1 - 3 |50 |
| Núcleos dedicados por conta do Lote | 10 - 100 | N/A<sup>1</sup> |
| Núcleos de baixa prioridade por conta do Lote | 10 - 100 | N/A<sup>2</sup> |
| Trabalhos ativos e agendas de trabalho<sup>3</sup> por Conta de lote | 100 - 300 | 1000<sup>4</sup> |
| Núcleos por conta do Lote | 20 - 100 | 500<sup>4</sup> |

> [!NOTE]
> Os limites padrão variam dependendo do tipo de assinatura que você usa para criar uma conta do Lote. As cotas de núcleos mostradas são para as contas do Lote no modo do Serviço de lote. [Exibir as cotas na conta do Lote](../articles/batch/batch-quota-limit.md#view-batch-quotas). 

<sup>1</sup> O número de núcleos dedicados por conta do Lote pode ser aumentado, mas o número máximo não está especificado. Entre em contato com o suporte do Azure para discutir opções de aumento.

<sup>2</sup> O número de núcleos de baixa prioridade por conta do Lote pode ser aumentado, mas o número máximo não está especificado. Entre em contato com o suporte do Azure para discutir opções de aumento.

<sup>3</sup> Os trabalhos concluídos e as agendas de trabalho não são limitados.

<sup>4</sup> Entre em contato com o suporte do Azure se você deseja solicitar um aumento além desse limite.
