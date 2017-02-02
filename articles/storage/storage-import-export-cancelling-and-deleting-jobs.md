---
title: "Cancelando e excluindo trabalhos do serviço de Importação/Exportação do Azure | Microsoft Docs"
description: "Saiba como cancelar e excluir trabalhos do serviço de Importação/Exportação do Microsoft Azure"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: fd3d66f0-1dbb-4c75-9223-307d5abaeefc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 22e37e26fec913a7638c71b2547c38f5efacd10b
ms.openlocfilehash: 640b814e4280f5cce029bea7324881bbfef4b1a4


---

# <a name="cancelling-and-deleting-jobs"></a>Cancelando e excluindo trabalhos
É possível solicitar que um trabalho seja cancelado antes que ele entre no estado `Packaging` chamando a operação [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update) e definindo o elemento `CancelRequested` como `true`. O trabalho será cancelado com os melhores esforços. Se as unidades estiverem transferindo dados, os dados poderão continuar sendo transferidos mesmo depois que o cancelamento tiver sido solicitado.

 Um trabalho cancelado será movido para o estado `Completed` e mantido por 90 dias, período após o qual ele será excluído.

 Para excluir um trabalho, chame a operação [Delete Job](/rest/api/storageimportexport/jobs#Jobs_Delete) antes que o trabalho seja enviado (*ou seja*, enquanto o trabalho está no estado `Creating`). Também é possível excluir um trabalho quando ele está no estado `Completed`. Após a exclusão de um trabalho, suas informações e seu status não são mais acessíveis por meio da API REST ou do portal do Azure.

## <a name="see-also"></a>Consulte também
 [Usando a API REST do serviço de Importação/Exportação](storage-import-export-using-the-rest-api.md)



<!--HONumber=Dec16_HO3-->


