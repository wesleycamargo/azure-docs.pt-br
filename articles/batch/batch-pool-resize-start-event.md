---
title: "Evento inicial de redimensionamento de pool – Azure | Microsoft Docs"
ms.custom: 
ms.date: 2017-02-01
ms.prod: azure
ms.reviewer: 
ms.service: batch
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: 8bd33e8b-6390-4a34-95dc-2e63d8bce941
caps.latest.revision: 6
author: tamram
ms.author: tamram
manager: timlt
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 5064d86063251aeed22688d8fac133c4be1a88f8
ms.lasthandoff: 04/13/2017

---
# <a name="pool-resize-start-event"></a>Evento inicial de redimensionamento de pool
Corpo do log do evento inicial de redimensionamento de pool

## <a name="remarks"></a>Comentários
 Esse evento é emitido quando um redimensionamento de pool é iniciado. Como o redimensionamento do pool é um evento assíncrono, você pode esperar que um evento completo de redimensionamento de pool seja emitido quando a operação de redimensionamento é concluída.

 O exemplo a seguir mostra o corpo de um evento inicial de redimensionamento de pool para um redimensionamento de 0 a 2 nós com redimensionamento manual.

```
{
    "poolId": "myPool1",
    "nodeDeallocationOption": "invalid",
    "currentDedicated": 0,
    "targetDedicated": 2,
    "enableAutoScale": false,
    "isAutoPool": false
}
```

|Elemento|Tipo|Observações|
|-------------|----------|-----------|
|poolId|Cadeia de caracteres|A ID do pool.|
|nodeDeallocationOption|Cadeia de caracteres|Especifica quando os nós poderão ser removidos do pool, se o tamanho do pool estiver diminuindo.<br /><br /> Os valores possíveis são:<br /><br /> **colocar novamente na fila** – Finalize as tarefas em execução e coloque-as novamente na fila. As tarefas serão executadas novamente quando o trabalho for habilitado. Remova nós assim que tarefas forem terminadas.<br /><br /> **terminar** – Termine as tarefas em execução. As tarefas não serão executadas novamente. Remova nós assim que tarefas forem terminadas.<br /><br /> **taskcompletion** – Permita a conclusão das tarefas atualmente em execução. Não agende novas tarefas enquanto aguarda. Remova nós quando todas as tarefas forem concluídas.<br /><br /> **Retaineddata** – Permita que as tarefas atualmente em execução sejam concluídas e aguarde que os todos os períodos de retenção de dados das tarefas expirem. Não agende novas tarefas enquanto aguarda. Remova nós quando todos os períodos de retenção de tarefa expirem.<br /><br /> O valor padrão é colocar novamente na fila.<br /><br /> Se o tamanho do pool aumentar, o valor será definido como **inválido**.|
|currentDedicated|Int32|O número de nós de computação atualmente atribuídos ao pool.|
|targetDedicated|Int32|O número de nós de computação solicitados para o pool.|
|enableAutoScale|Bool|Especifica se o tamanho do pool é ajustado automaticamente com o tempo.|
|isAutoPool|Bool|Especifica se o pool foi criado por meio de um mecanismo de AutoPool do trabalho.|

