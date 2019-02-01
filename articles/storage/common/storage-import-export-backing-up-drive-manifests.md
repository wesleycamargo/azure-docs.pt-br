---
title: Fazendo backup de manifestos de unidade de Importação/Exportação do Azure | Microsoft Docs
description: Saiba como fazer backup automático dos manifestos da unidade do serviço de Importação/Exportação do Microsoft Azure.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 61881508e18a2c7dbe1bc3be72d34423f862437a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55473384"
---
# <a name="backing-up-drive-manifests-for-azure-importexport-jobs"></a>Fazendo backup de manifestos de unidade de trabalhos de Importação/Exportação do Azure

É possível fazer backup automático dos manifestos da unidade em blobs definindo a propriedade `BackupDriveManifest` como `true` nas operações de API REST [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) ou [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update). Por padrão, não é feito o backup dos manifestos da unidade. Os backups dos manifestos da unidade são armazenados como blobs de blocos em um contêiner na conta de armazenamento associada ao trabalho. Por padrão, o nome do contêiner é `waimportexport`, mas é possível especificar outro nome na propriedade `DiagnosticsPath` ao chamar as operações `Put Job` ou `Update Job Properties`. O blob do manifesto do backup é nomeado no seguinte formato: `waies/jobname_driveid_timestamp_manifest.xml`.

 É possível recuperar o URI dos manifestos da unidade de backup de um trabalho chamando a operação [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get). O URI do blob é retornado na propriedade `ManifestUri` de cada unidade.

## <a name="next-steps"></a>Próximas etapas

* [Usando a API REST do serviço de Importação/Exportação](storage-import-export-using-the-rest-api.md)
