---
title: "Cancelar/excluir um trabalho de Importação/Exportação do Azure | Microsoft Docs"
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
ms.date: 01/23/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 74182c8c357085f186aaa43adfaef80a083d16bb
ms.openlocfilehash: 26e06529cb9354030da32c235d3c4819d46d669d
ms.lasthandoff: 02/16/2017


---

# <a name="canceling-and-deleting-azure-importexport-jobs"></a>Cancelando e excluindo trabalhos de Importação/Exportação do Azure
É possível solicitar que um trabalho seja cancelado antes que ele entre no estado `Packaging` chamando a operação [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update) e definindo o elemento `CancelRequested` como `true`. O trabalho será cancelado com os melhores esforços. Se as unidades estiverem transferindo dados, os dados poderão continuar sendo transferidos mesmo depois que o cancelamento tiver sido solicitado.

 Um trabalho cancelado será movido para o estado `Completed` e mantido por 90 dias, período após o qual ele será excluído.

 Para excluir um trabalho, chame a operação [Delete Job](/rest/api/storageimportexport/jobs#Jobs_Delete) antes que o trabalho seja enviado (*ou seja*, enquanto o trabalho está no estado `Creating`). Também é possível excluir um trabalho quando ele está no estado `Completed`. Após a exclusão de um trabalho, suas informações e seu status não são mais acessíveis por meio da API REST ou do portal do Azure.

## <a name="see-also"></a>Consulte também
 [Usando a API REST do serviço de Importação/Exportação](storage-import-export-using-the-rest-api.md)

