---
title: "Definindo propriedades e metadados durante o processo de importação da Importação/Exportação do Azure | Microsoft Docs"
description: "Saiba como especificar as propriedades e os metadados a serem definidos nos blobs de destino durante a execução da ferramenta de Importação/Exportação do Azure para preparar as unidades."
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
ms.openlocfilehash: 2f597da2c056a2b4ea2af751e955fafdd7016744


---

# <a name="setting-properties-and-metadata-during-the-import-process"></a>Configurando propriedades e metadados durante o processo de importação

Ao executar a ferramenta de Importação/Exportação do Microsoft Azure para preparar as unidades, é possível especificar as propriedades e os metadados a serem definidos nos blobs de destino. Siga estas etapas:

1.  Para definir propriedades de blob, crie um arquivo de texto no computador local que especifica os nomes e valores das propriedades.
2.  Para definir metadados de blob, crie um arquivo de texto no computador local que especifica os nomes e valores dos metadados.
3.  Passe o caminho completo para um ou ambos os arquivos à ferramenta de Importação/Exportação do Azure como parte da operação `PrepImport`.

> [!NOTE]
>  Ao especificar um arquivo de propriedades ou de metadados como parte de uma sessão de cópia, essas propriedades ou esses metadados são definidos para cada blob importado como parte da sessão de cópia. Se desejar especificar outro conjunto de propriedades ou de metadados para alguns dos blobs importados, você precisará criar uma sessão de cópia separada com arquivos de propriedades ou de metadados diferentes.

## <a name="specify-blob-properties-in-a-text-file"></a>Especificar as propriedades de blob em um arquivo de texto

Para especificar as propriedades de blob, crie um arquivo de texto local e inclua um XML que especifica os nomes da propriedade como elementos e os valores da propriedade como valores. Este é um exemplo que especifica alguns valores de propriedade:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Properties>
    <Content-Type>application/octet-stream</Content-Type>
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>
    <Cache-Control>no-cache</Cache-Control>
</Properties>
```

Salve o arquivo em uma localização local como `C:\WAImportExport\ImportProperties.txt`.

## <a name="specify-blob-metadata-in-a-text-file"></a>Especificar os metadados de blob em um arquivo de texto

Da mesma forma, para especificar os metadados de blob, crie um arquivo de texto local que especifica os nomes dos metadados como elementos e os valores dos metadados como valores. Este é um exemplo que especifica alguns valores de metadados:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Metadata>
    <UploadMethod>Windows Azure Import/Export Service</UploadMethod>
    <DataSetName>SampleData</DataSetName>
    <CreationDate>10/1/2013</CreationDate>
</Metadata>
```

Salve o arquivo em uma localização local como `C:\WAImportExport\ImportMetadata.txt`.

## <a name="add-the-path-to-properties-and-metadata-files-in-datasetcsv"></a>Adicionar o caminho para os arquivos de propriedades e metadados no dataset.csv

```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
H:\Video\,https://mystorageaccount.blob.core.windows.net/video/,BlockBlob,rename,None,H:\mydirectory\properties.xml
H:\Photo\,https://mystorageaccount.blob.core.windows.net/photo/,BlockBlob,rename,None,H:\mydirectory\properties.xml
K:\Temp\FavoriteVideo.ISO,https://mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO,BlockBlob,rename,None,H:\mydirectory\properties.xml
\\myshare\john\music\,https://mystorageaccount.blob.core.windows.net/music/,BlockBlob,rename,None,H:\mydirectory\properties.xml
```

## <a name="next-steps"></a>Próximas etapas

[Formato de arquivo de propriedades e metadados do Serviço de Importação/Exportação](storage-import-export-file-format-metadata-and-properties.md)



<!--HONumber=Dec16_HO3-->


