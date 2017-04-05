---
title: "Referência rápida de comandos para trabalhos de importação da Ferramenta de Importação/Exportação do Azure — v1 | Microsoft Docs"
description: "Referência de comandos da Ferramenta de Importação/Exportação do Azure para comandos de trabalho de importação usados com frequência. Este documento refere-se à v1 da Ferramenta de Importação/Exportação."
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
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 632100f324c47d69f64cff020aafbaa300ae8c2b
ms.lasthandoff: 03/30/2017


---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>Referência rápida de comandos usados com frequência para trabalhos de importação
Esta seção fornece uma referência rápida de alguns comandos usados com frequência. Para obter o uso detalhado, consulte [Preparing Hard Drives for an Import Job](storage-import-export-tool-preparing-hard-drives-import-v1.md) (Preparando os discos rígidos para um trabalho de importação).  

## <a name="prepare-the-disks-when-data-already-copied-to-the-disks"></a>Preparar os discos quando os dados já foram copiados para os discos
 Este é um comando de exemplo para preparar um disco quando os dados já foram copiados para o disco rígido que ainda não foi criptografado com o BitLocker:  
  
```  
  WAImportExport.exe PrepImport /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /t:d /encrypt /srcdir:d:\movies\drama /dstdir:movies/drama/ /skipwrite
```    

## <a name="copy-a-single-directory-to-a-hard-drive"></a>Copiar um único diretório para um disco rígido  
 Este é um comando de exemplo para copiar um único diretório de origem para um disco rígido que ainda não foi criptografado com o BitLocker:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
## <a name="copy-wwo-directories-to-a-hard-drive"></a>Copiar dois diretórios para um disco rígido  
 Para copiar dois diretórios de origem para uma unidade, você precisará de dois comandos.  
  
 O primeiro comando especifica o diretório de log, a chave de conta de armazenamento, a letra da unidade de destino e os requisitos de `format/encrypt`, além dos parâmetros comuns:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
 O segundo comando especifica o arquivo de diário, uma nova ID de sessão e as localizações de origem e de destino:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:music /srcdir:d:\Music /dstdir:entertainment/music/  
```  
  
## <a name="copy-a-large-file-to-a-hard-drive-in-a-second-copy-session"></a>Copiar um arquivo grande para um disco rígido em uma segunda sessão de cópia  
 Este é um comando de exemplo que copia um único arquivo grande para uma unidade que foi preparada em uma sessão de cópia anterior:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:dvd /srcfile:d:\dvd\favoritemovie.vhd /dstblob:dvd/favoritemovie.vhd  
```  
  
## <a name="next-steps"></a>Próximas etapas

* [Fluxo de trabalho de exemplo para preparar discos rígidos para um trabalho de importação](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)

