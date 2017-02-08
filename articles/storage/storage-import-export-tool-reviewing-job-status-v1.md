---
title: "Examinando o status do trabalho com arquivos de log de cópia | Microsoft Docs"
description: "Saiba como usar os arquivos de log criados durante a execução do trabalho de importação ou exportação para ver o status do Trabalho de Importação/Exportação."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: c69d1d69-6403-4eee-9949-0185faeecfa1
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 25cd0308115994463bd50562594d4e2bb88e8286
ms.openlocfilehash: 66d7690249cb653dfb8d2e591d1ce66162d98a7e


---

# <a name="reviewing-job-status-with-copy-log-files"></a>Examinando o status do trabalho com arquivos de log de cópia
Quando o serviço de Importação/Exportação do Microsoft Azure processa unidades associadas a um trabalho de importação ou exportação, ele grava arquivos de log de cópia na conta de armazenamento para a qual ou da qual os blobs estão sendo importados ou exportados. O arquivo de log contém o status detalhado sobre cada arquivo importado ou exportado. A URL para cada arquivo de log de cópia é retornada ao consultar o status de um trabalho concluído; consulte [Get Job](/rest/api/storageservices`Get-Job3) (Obter Trabalho) para obter mais informações.  
  
 Estas são URLs de exemplo para arquivos de log de cópia de um trabalho de importação com duas unidades:  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath /waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  
  
 Consulte [Import-Export Service Log File Format](storage-import-export-file-format-log.md) (Formato de arquivo de log do Serviço de Importação/Exportação) para obter o formato dos logs de cópia e a lista completa de códigos de status.  
  
## <a name="see-also"></a>Consulte também  
 [Configurando a ferramenta de Importação/Exportação do Azure](storage-import-export-tool-setup-v1.md)   
 [Preparing Hard Drives for an Import Job](storage-import-export-tool-preparing-hard-drives-import-v1.md)  (Preparando os discos rígidos para um trabalho de importação)  
 [Repairing an Import Job](storage-import-export-tool-repairing-an-import-job-v1.md)  (Reparando um trabalho de importação)  
 [Repairing an Export Job](storage-import-export-tool-repairing-an-export-job-v1.md)  (Reparando um trabalho de exportação)  
 [Solução de problemas da Ferramenta de Importação/Exportação do Azure](storage-import-export-tool-troubleshooting-v1.md)



<!--HONumber=Dec16_HO2-->


