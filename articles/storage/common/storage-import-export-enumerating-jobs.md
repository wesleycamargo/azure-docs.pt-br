---
title: Listar todos os trabalhos de Importação/Exportação do Azure | Microsoft Docs
description: Saiba como listar todos os trabalhos do serviço de Importação/Exportação do Azure em uma assinatura.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 69daac71b69969a7ad9acfeb7095053f8138bf53
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39520873"
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Enumerando trabalhos no serviço de Importação/Exportação do Azure
Para enumerar todos os trabalhos em uma assinatura, chame a operação [List Jobs](/rest/api/storageimportexport/jobs#Jobs_List). `List Jobs` retorna uma lista de trabalhos, bem como os seguintes atributos:

-   O tipo de trabalho (Importação ou Exportação)

-   O estado atual do trabalho

-   A conta de armazenamento associada ao trabalho

## <a name="next-steps"></a>Próximas etapas

* [Usando a API REST do serviço de Importação/Exportação](storage-import-export-using-the-rest-api.md)
