---
title: "Fazendo backup de manifestos de unidade de Importação/Exportação do Azure | Microsoft Docs"
description: "Saiba como fazer backup automático dos manifestos da unidade do serviço de Importação/Exportação do Microsoft Azure."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 594abd80-b834-4077-a474-d8a0f4b7928a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: c215112cc73f1378ba3ec2c7099b102aaf56db63
ms.openlocfilehash: 43c31b965f9f1471bce9fe4b9ac32a392b6ecbb1
ms.lasthandoff: 02/16/2017


---

# <a name="backing-up-drive-manifests-for-azure-importexport-jobs"></a>Fazendo backup de manifestos de unidade de trabalhos de Importação/Exportação do Azure
É possível fazer backup automático dos manifestos da unidade em blobs definindo a propriedade `BackupDriveManifest` como `true` nas operações [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) ou [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update). Por padrão, não é feito o backup dos manifestos da unidade. Os backups dos manifestos da unidade são armazenados como blobs de blocos em um contêiner na conta de armazenamento associada ao trabalho. Por padrão, o nome do contêiner é `waimportexport`, mas é possível especificar outro nome na propriedade `DiagnosticsPath` ao chamar as operações `Put Job` ou `Update Job Properties`. O blob do manifesto do backup é nomeado no seguinte formato: `waies/jobname_driveid_timestamp_manifest.xml`.

 É possível recuperar o URI dos manifestos da unidade de backup de um trabalho chamando a operação [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get). O URI do blob é retornado na propriedade `ManifestUri` de cada unidade.

## <a name="see-also"></a>Consulte também
 [Usando a API REST do serviço de Importação/Exportação](storage-import-export-using-the-rest-api.md)

