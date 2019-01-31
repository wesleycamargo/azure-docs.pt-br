---
title: Evento de exclusão de pool – Azure | Microsoft Docs
description: Referência de exclusão do pool de lote evento inicial.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: e715ccd0f5e79f9c640a3c060b0252b798748b4d
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55474541"
---
# <a name="pool-delete-complete-event"></a>Evento de conclusão de exclusão de pool

 Esse evento é emitido quando uma operação de exclusão de pool é concluída.

 O exemplo a seguir mostra o corpo de um evento de conclusão de exclusão de pool.

```
{
    "id": "myPool1",
    "startTime": "2016-09-09T22:13:48.579Z",
    "endTime": "2016-09-09T22:14:08.836Z"
}
```

|Elemento|Tipo|Observações|
|-------------|----------|-----------|
|ID|Cadeia de caracteres|A ID do pool.|
|startTime|DateTime|A hora de início da exclusão do pool.|
|endTime|DateTime|A hora de conclusão da exclusão do pool.|

## <a name="remarks"></a>Comentários
Para obter mais informações sobre estados e códigos de erro para a operação de redimensionamento do pool, consulte [Excluir um pool de uma conta](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).