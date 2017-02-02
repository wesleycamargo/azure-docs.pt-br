---
title: "Referência rápida de comandos para trabalhos de importação da ferramenta de Importação/Exportação do Azure | Microsoft Docs"
description: "Referência de comandos da ferramenta de Importação/Exportação do Azure usados com frequência para trabalhos de importação"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 41bc5585f3d9dea2a08dc8a6bc1e4fdf9f0c8fc4
ms.openlocfilehash: 79b1d3f92671638ba43bac2f5428a1f903d11080


---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>Referência rápida de comandos usados com frequência para trabalhos de importação

Este artigo fornece uma referência rápida de alguns comandos usados com frequência. Para obter o uso detalhado, consulte [Preparing Hard Drives for an Import Job](storage-import-export-tool-preparing-hard-drives-import.md) (Preparando os discos rígidos para um trabalho de importação).

## <a name="import-job-quick-reference"></a>Referência rápida de trabalhos de importação

Para a primeira sessão:

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

Segunda sessão:

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /DataSet:dataset-2.csv
```

Anular a última sessão:

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /AbortSession
```

Retome a última sessão interrompida:

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /ResumeSession
```

Adicionar unidades à última sessão:

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /AdditionalDriveSet:driveset-2.csv
```

## <a name="next-steps"></a>Próximas etapas

[Fluxo de trabalho de exemplo para preparar discos rígidos para um trabalho de importação](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)



<!--HONumber=Dec16_HO3-->


