---
title: Cancelar e excluir um trabalho de Importação/Exportação do Azure | Microsoft Docs
description: Saiba como cancelar e excluir trabalhos do serviço de Importação/Exportação do Microsoft Azure.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: e11cf974a5f33020c889844dd34f51612e13036e
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39521006"
---
# <a name="canceling-and-deleting-azure-importexport-jobs"></a>Cancelando e excluindo trabalhos de Importação/Exportação do Azure

 Para solicitar que um trabalho seja cancelado antes que ele entre no estado `Packaging`, chame a operação [Atualizar Propriedades do Trabalho](/rest/api/storageimportexport/jobs#Jobs_Update) e defina o elemento `CancelRequested` como `true`. O trabalho é cancelado com os melhores esforços. Se as unidades estiverem transferindo dados, os dados poderão continuar sendo transferidos mesmo depois que o cancelamento tiver sido solicitado.

 Um trabalho cancelado é movido para o estado `Completed` e é mantido por 90 dias, ponto em que é excluído.

 Para excluir um trabalho, chame a operação [Delete Job](/rest/api/storageimportexport/jobs#Jobs_Delete) antes que o trabalho seja enviado (ou seja, enquanto o trabalho está no estado `Creating`). Também é possível excluir um trabalho quando ele está no estado `Completed`. Após a exclusão de um trabalho, suas informações e seu status não são mais acessíveis por meio da API REST ou do portal do Azure.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>Próximas etapas

* [Usando a API REST do serviço de Importação/Exportação](storage-import-export-using-the-rest-api.md)
