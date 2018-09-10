---
title: Configurando propriedades e metadados usando a Importação/Exportação do Azure | Microsoft Docs
description: Saiba como especificar as propriedades e os metadados a serem definidos nos blobs de destino durante a execução da Ferramenta de Importação/Exportação do Azure para preparar as unidades.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: d68de35591d1b17a62278a0bc4adf2b9ee20cf02
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39524994"
---
# <a name="setting-properties-and-metadata-during-the-import-process"></a>Configurando propriedades e metadados durante o processo de importação

Quando você executa a Ferramenta de Importação/Exportação do Microsoft Azure para preparar as unidades, é possível especificar as propriedades e os metadados a serem definidos nos blobs de destino. Siga estas etapas:

1.  Para definir propriedades de blob, crie um arquivo de texto no computador local que especifica os nomes e valores das propriedades.
2.  Para definir metadados de blob, crie um arquivo de texto no computador local que especifica os nomes e valores dos metadados.
3.  Passe o caminho completo para um ou ambos os arquivos à Ferramenta de Importação/Exportação do Azure como parte da operação `PrepImport`.

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
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>
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

* [Formato de arquivo de propriedades e metadados de serviço de Importação/Exportação](../storage-import-export-file-format-metadata-and-properties.md)
