---
title: "Visualizando o uso da unidade para um trabalho de exportação | Microsoft Docs"
description: "Saiba como visualizar a lista de blobs selecionada para um trabalho de exportação no Serviço de Importação/Exportação do Azure"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 7707d744-7ec7-4de8-ac9b-93a18608dc9a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 358e3f2574cab0150c59f96b9bc4d32d959e94a8
ms.openlocfilehash: 9ba9a3970925466285ae1df4676501fbdd24bd66


---

# <a name="previewing-drive-usage-for-an-export-job"></a>Visualizando o uso da unidade para um trabalho de exportação
Antes de criar um trabalho de exportação, você precisa escolher um conjunto de blobs a ser exportado. O serviço de Importação/Exportação do Microsoft Azure permite que você use uma lista de caminhos ou prefixos de blob para representar os blobs selecionados.  
  
 Em seguida, é necessário determinar quantas unidades você precisa enviar. A ferramenta de Importação/Exportação do Microsoft Azure fornece o comando `PreviewExport` para visualizar o uso da unidade para os blobs selecionados, com base no tamanho das unidades que você pretende usar. É possível especificar os seguintes parâmetros:  
  
|Opção da linha de comando|Descrição|  
|--------------------------|-----------------|  
|**/logdir:**<LogDirectory\>|Opcional. O diretório de log. Os arquivos de log detalhados serão gravados nesse diretório. Se nenhum diretório de log for especificado, o diretório atual será usado como o diretório de log.|  
|**/sn:**<StorageAccountName\>|Obrigatório. O nome da conta de armazenamento do trabalho de exportação.|  
|**/sk:**<StorageAccountKey\>|Necessário somente se uma SAS do contêiner não for especificada. A chave de conta da conta de armazenamento do trabalho de exportação.|  
|**/csas:**<ContainerSas\>|Necessário somente se uma chave de conta de armazenamento não for especificada. A SAS do contêiner para listar os blobs a serem exportados no trabalho de exportação.|  
|**/ExportBlobListFile:**<ExportBlobListFile\>|Obrigatório. Caminho para o arquivo XML que contém a lista de caminhos ou prefixos de caminhos de blob para os blobs a serem exportados. O formato de arquivo usado no elemento `BlobListBlobPath` na operação [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) da API REST do Serviço de Importação/Exportação.|  
|**/DriveSize:**<DriveSize\>|Obrigatório. O tamanho das unidades a ser usado para um trabalho de exportação, *por exemplo*, 500 GB, 1,5 TB.|  
  
O seguinte exemplo demonstra o comando `PreviewExport`:  
  
```  
WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB    
```  
  
O arquivo de lista de blobs de exportação pode conter nomes e prefixos de blob, conforme mostrado aqui:  
  
```xml 
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

A ferramenta de Importação/Exportação do Azure lista todos os blobs a serem exportados e calcula como empacotá-los em unidades do tamanho especificado, levando em consideração qualquer sobrecarga necessária. Em seguida, estima o número de unidades necessárias para manter os blobs e as informações de uso da unidade.  
  
Este é um exemplo da saída, com a omissão dos logs informativos:  
  
```  
Number of unique blob paths/prefixes:   3  
Number of duplicate blob paths/prefixes:        0  
Number of nonexistent blob paths/prefixes:      1  
  
Drive size:     500.00 GB  
Number of blobs that can be exported:   6  
Number of blobs that cannot be exported:        2  
Number of drives needed:        3  
        Drive #1:       blobs = 1, occupied space = 454.74 GB  
        Drive #2:       blobs = 3, occupied space = 441.37 GB  
        Drive #3:       blobs = 2, occupied space = 131.28 GB    
```  
  
## <a name="see-also"></a>Consulte também  
[Azure Import-Export Tool Reference](storage-import-export-tool-how-to-v1.md) (Referência da Ferramenta de Importação/Exportação do Azure)



<!--HONumber=Feb17_HO3-->


