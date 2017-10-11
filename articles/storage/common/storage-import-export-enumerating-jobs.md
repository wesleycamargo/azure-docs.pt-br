---
title: "Listar todos os trabalhos de Importação/Exportação do Azure | Microsoft Docs"
description: "Saiba como listar todos os trabalhos do serviço de Importação/Exportação do Azure em uma assinatura."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: f2e619be-1bbd-4a54-9472-9e2f70a83b64
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 1977bfc0e516088310f45ecdd960287eeed2c2d8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2017
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Enumerando trabalhos no serviço de Importação/Exportação do Azure
Para enumerar todos os trabalhos em uma assinatura, chame a operação [List Jobs](/rest/api/storageimportexport/jobs#Jobs_List). `List Jobs` retorna uma lista de trabalhos, bem como os seguintes atributos:

-   O tipo de trabalho (Importação ou Exportação)

-   O estado atual do trabalho

-   A conta de armazenamento associada ao trabalho

## <a name="next-steps"></a>Próximas etapas

* [Usando a API REST do serviço de Importação/Exportação](storage-import-export-using-the-rest-api.md)
