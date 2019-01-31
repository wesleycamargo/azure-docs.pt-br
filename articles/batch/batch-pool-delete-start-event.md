---
title: Iniciar eventos de exclusão de pool lote do Azure | Documentos do Microsoft
description: Referência para excluir um pool de lote evento inicial.
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
ms.openlocfilehash: 2352971af3844b56f93c16ebaf6cb23bd5fd8a5a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55474285"
---
# <a name="pool-delete-start-event"></a>Evento inicial de exclusão de pool

 Esse evento é emitido quando uma operação de exclusão de pool é iniciada. Como a exclusão do pool é um evento assíncrono, você pode esperar que um evento completo de conclusão de exclusão de pool seja emitido quando a operação de exclusão é concluída.

 O exemplo a seguir mostra o corpo de um evento de início de exclusão de pool.

```
{
    "id": "myPool1"
}
```

|Elemento|Tipo|Observações|
|-------------|----------|-----------|
|ID|Cadeia de caracteres|A ID do pool.|