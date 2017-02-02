---
title: "Enumerando trabalhos no serviço de Importação/Exportação do Azure | Microsoft Docs"
description: "Saiba como enumerar todos os trabalhos do serviço de Importação/Exportação do Azure em uma assinatura."
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
ms.date: 12/16/2016
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 22e37e26fec913a7638c71b2547c38f5efacd10b
ms.openlocfilehash: 65d8912c4eced92206ee1262f3e3307b3162cbd6


---

# <a name="enumerating-jobs"></a>Enumerando trabalhos
Para enumerar todos os trabalhos em uma assinatura, chame a operação [List Jobs](/rest/api/storageimportexport/jobs#Jobs_List). `List Jobs` retorna uma lista de trabalhos, bem como os seguintes atributos:

-   O tipo de trabalho (Importação ou Exportação)

-   O estado atual do trabalho

-   A conta de armazenamento associada ao trabalho

## <a name="see-also"></a>Consulte também
 [Usando a API REST do serviço de Importação/Exportação](storage-import-export-using-the-rest-api.md)



<!--HONumber=Dec16_HO3-->


