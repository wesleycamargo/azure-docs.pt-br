---
title: Definir atividade variável no Azure Data Factory | Microsoft Docs
description: Saiba como usar a atividade Definir Variável para definir o valor de uma variável existente definida em um pipeline do Data Factory
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: douglasl
ms.openlocfilehash: cc573028779bcd6b77394bbeefbea58f714b835c
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54017337"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Definir Atividade Variável no Azure Data Factory

Use a atividade Definir Variável para definir o valor de uma variável existente do tipo Cadeia de caracteres, Booliano ou Matriz definido em um pipeline do Data Factory.

## <a name="type-properties"></a>Propriedades de tipo

Propriedade | DESCRIÇÃO | Obrigatório
-------- | ----------- | --------
Nome | Nome da atividade no pipeline | SIM
Descrição | Texto descrevendo o que a atividade realiza | não
Tipo | Tipo de atividade é SetVariable | Sim
value | Literal de cadeia de caracteres ou valor do objeto de expressão usado para definir a variável especificada | Sim
variableName | Nome da variável que será definida por essa atividade | Sim


## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre uma atividade de fluxo de controle relacionada com suporte pelo Data Factory: 

- [Acrescentar Atividade Variável](control-flow-append-variable-activity.md)
