---
title: "Fluxo de trabalho de exemplo para preparar discos rígidos para um trabalho de Importação do Azure | Microsoft Docs"
description: "Veja um passo a passo para o processo completo para preparar unidades para um trabalho de importação no serviço de Importação/Exportação do Azure"
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
ms.date: 01/23/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 50fbd0d214c825137c3ac6873be27b9d2d53cee1
ms.openlocfilehash: ebed1f1d4cb6cca96e02322d2f76abd6e9810ecc
ms.lasthandoff: 02/16/2017


---

# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>Fluxo de trabalho de exemplo para preparo dos discos rígidos para um trabalho de importação

Este artigo explica o processo completo de preparar unidades para um trabalho de importação.

## <a name="sample-data"></a>Dados de amostra

Este exemplo importa os seguintes dados para uma conta de armazenamento do Azure denominada `mystorageaccount`:

|Local|Descrição|Tamanho dos dados|
|--------------|-----------------|-----|
|H:\Video|Uma coleção de vídeos|12 TB|
|H:\Photo|Uma coleção de fotos|30 GB|
|K:\Temp\FavoriteMovie.ISO|Uma imagem de disco Blu-ray™|25 GB|
|\\\bigshare\john\music|Uma coleção de arquivos de música em um compartilhamento de rede|10 GB|

## <a name="storage-account-destinations"></a>Destinos de conta de armazenamento

O trabalho de importação importará os dados nos destinos a seguir na conta de armazenamento:

|Fonte|Blob de destino ou diretório virtual|
|------------|-------------------------------------------|
|H:\Video|https://mystorageaccount.blob.core.windows.net/video|
|H:\Photo|https://mystorageaccount.blob.core.windows.net/photo|
|K:\Temp\FavoriteMovie.ISO|https://mystorageaccount.blob.core.windows.net/favorite/FavoriteMovies.ISO|
|\\\bigshare\john\music|https://mystorageaccount.blob.core.windows.net/music|

Com esse mapeamento, o arquivo `H:\Video\Drama\GreatMovie.mov` será importado para o blob `https://mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov`.

## <a name="determine-hard-drive-requirements"></a>Determinar os requisitos de disco rígido

Em seguida, para determinar quantos discos rígidos são necessários, calcule o tamanho dos dados:

`12TB + 30GB + 25GB + 10GB = 12TB + 65GB`

Neste exemplo, duas unidades de disco rígido de 8TB devem ser suficientes. No entanto, como o diretório de origem `H:\Video` tem 12TB de dados e capacidade do disco rígido único é apenas de 8TB, você poderá especificar isso na seguinte forma no arquivo **dataset.csv**:

```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
H:\Video\,https://mystorageaccount.blob.core.windows.net/video/,BlockBlob,rename,None,H:\mydirectory\properties.xml
H:\Photo\,https://mystorageaccount.blob.core.windows.net/photo/,BlockBlob,rename,None,H:\mydirectory\properties.xml
K:\Temp\FavoriteVideo.ISO,https://mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO,BlockBlob,rename,None,H:\mydirectory\properties.xml
\\myshare\john\music\,https://mystorageaccount.blob.core.windows.net/music/,BlockBlob,rename,None,H:\mydirectory\properties.xml
```

## <a name="attach-drives-and-configure-the-job"></a>Anexar discos e configurar o trabalho

Você anexará ambos os discos ao computador e criará volumes. Em seguida, crie o arquivo **driveset.csv**:

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
X,Format,SilentMode,Encrypt,
Y,Format,SilentMode,Encrypt,
```

A ferramenta distribui dados em dois discos rígidos de forma otimizada.

Além disso, você pode definir os metadados para todos os arquivos a seguir:

* **UploadMethod:** serviço de Importação/Exportação do Microsoft Azure
* **DataSetName:** SampleData
* **CreationDate:** 10/1/2013

Para definir metadados para os arquivos importados, crie um arquivo de texto `c:\WAImportExport\SampleMetadata.txt`, com o seguinte conteúdo:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Metadata>
    <UploadMethod>Windows Azure Import/Export Service</UploadMethod>
    <DataSetName>SampleData</DataSetName>
    <CreationDate>10/1/2013</CreationDate>
</Metadata>
```

Você também pode definir algumas propriedades para o `FavoriteMovie.ISO` blob:

* **Content-Type:** application/octet-stream
* **Content-MD5:** Q2hlY2sgSW50ZWdyaXR5IQ==
* **Cache-Control:** no-cache

Para definir essas propriedades, crie um arquivo de texto `c:\WAImportExport\SampleProperties.txt`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Properties>
    <Content-Type>application/octet-stream</Content-Type>
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>
    <Cache-Control>no-cache</Cache-Control>
</Properties>
```

## <a name="run-the-azure-importexport-tool-waimportexportexe"></a>Execute a ferramenta de Importação/Exportação do Azure (WAImportExport.exe)

Agora você está pronto para executar a ferramenta de Importação/Exportação do Azure para preparar as duas unidades de disco rígido.

**Para a primeira sessão:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

Se mais dados precisam ser adicionados, crie outro arquivo de conjunto de dados (mesmo formato que Initialdataset).

**Para a segunda sessão:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

Depois de concluir as sessões de cópia, você pode desconectar as duas unidades do computador de cópia e enviá-las para o centro de dados Azure apropriado. Você vai carregar os dois arquivos do diário, `<FirstDriveSerialNumber>.xml` e `<SecondDriveSerialNumber>.xml`, quando você cria a tarefa de importação no portal do Azure.

## <a name="next-steps"></a>Próximas etapas

* [Preparação de discos rígidos para um trabalho de importação](storage-import-export-tool-preparing-hard-drives-import.md)
* [Referência rápida para comandos usados frequentemente](storage-import-export-tool-quick-reference.md)

