---
title: Atividade Anexar Variável no Azure Data Factory | Microsoft Docs
description: Saiba como definir a atividade Anexar Variável para adicionar um valor a uma variável de matriz existente definida em um pipeline do Data Factory
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: douglasl
ms.openlocfilehash: 03652ce20d82565d5714cdc43a01a9e7c3074f6a
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48903655"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Atividade Anexar Variável no Azure Data Factory

Use a atividade Anexar Variável para adicionar um valor a uma variável de matriz existente definida em um pipeline do Data Factory.

## <a name="type-properties"></a>Propriedades de tipo

Propriedade | DESCRIÇÃO | Obrigatório
-------- | ----------- | --------
Nome | Nome da atividade no pipeline | SIM
Descrição | Texto descrevendo o que a atividade realiza | não
Tipo | O tipo de atividade é AppendVariable | Sim
value | Literal de cadeia de caracteres ou valor do objeto de expressão usado para acrescentar à variável especificada | Sim
variableName | Nome da variável que será modificada por atividade, a variável deve ser do tipo "Matriz" | Sim

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre uma atividade de fluxo de controle relacionada com suporte pelo Data Factory: 

- [Definir Atividade Variável](control-flow-set-variable-activity.md)
