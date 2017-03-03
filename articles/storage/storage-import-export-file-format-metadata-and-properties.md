---
title: "Formato de arquivo de propriedades e metadados da Importação/Exportação do Azure | Microsoft Docs"
description: "Saiba como especificar metadados e propriedades para um ou mais blobs que fazem parte de um trabalho de importação ou exportação"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 840364c6-d9a8-4b43-a9f3-f7441c625069
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 8de848b1192ff1c10e0375053c4e03f18c06184e
ms.openlocfilehash: 0cac5419d4b481a5b783b4b9fbd4ad2d4d637042
ms.lasthandoff: 02/16/2017


---
# <a name="azure-importexport-service-metadata-and-properties-file-format"></a>Formato de arquivo de propriedades e metadados de serviço de Importação/Exportação do Azure
É possível especificar metadados e propriedades para um ou mais blobs como parte de um trabalho de importação ou exportação. Para definir metadados ou propriedades para blobs criados como parte de um trabalho de importação, você fornece um arquivo de metadados ou de propriedades no disco rígido que contém os dados a serem importados. Para um trabalho de exportação, os metadados e as propriedades são gravados em um arquivo de metadados ou de propriedades incluído no disco rígido retornado para você.  
  
## <a name="metadata-file-format"></a>Formato de arquivo de metadados  
O formato de um arquivo de metadados é o seguinte:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
[<metadata-name-1>metadata-value-1</metadata-name-1>]  
[<metadata-name-2>metadata-value-2</metadata-name-2>]  
. . .  
</Metadata>  
```
  
|Elemento XML|Tipo|Descrição|  
|-----------------|----------|-----------------|  
|`Metadata`|Elemento raiz|O elemento raiz do arquivo de metadados.|  
|`metadata-name`|Cadeia de caracteres|Opcional. O elemento XML especifica o nome dos metadados do blob e seu valor especifica o valor da configuração dos metadados.|  
  
## <a name="properties-file-format"></a>Formato de arquivo de propriedades  
O formato de um arquivo de propriedades é o seguinte:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
[<Last-Modified>date-time-value</Last-Modified>]  
[<Etag>etag</Etag>]  
[<Content-Length>size-in-bytes<Content-Length>]  
[<Content-Type>content-type</Content-Type>]  
[<Content-MD5>content-md5</Content-MD5>]  
[<Content-Encoding>content-encoding</Content-Encoding>]  
[<Content-Language>content-language</Content-Language>]  
[<Cache-Control>cache-control</Cache-Control>]  
</Properties>  
```
  
|Elemento XML|Tipo|Descrição|  
|-----------------|----------|-----------------|  
|`Properties`|Elemento raiz|O elemento raiz do arquivo de propriedades.|  
|`Last-Modified`|Cadeia de caracteres|Opcional. A hora da última modificação do blob. Somente para trabalhos de exportação.|  
|`Etag`|Cadeia de caracteres|Opcional. O valor da Etag do blob. Somente para trabalhos de exportação.|  
|`Content-Length`|Cadeia de caracteres|Opcional. O tamanho do blob em bytes. Somente para trabalhos de exportação.|  
|`Content-Type`|Cadeia de caracteres|Opcional. O tipo de conteúdo do blob.|  
|`Content-MD5`|Cadeia de caracteres|Opcional. O hash MD5 do blob.|  
|`Content-Encoding`|Cadeia de caracteres|Opcional. A codificação do conteúdo do blob.|  
|`Content-Language`|Cadeia de caracteres|Opcional. O idioma do conteúdo do blob.|  
|`Cache-Control`|Cadeia de caracteres|Opcional. A cadeia de caracteres de controle de cache do blob.|  
  
 Consulte [Set Blob Metadata](/rest/api/storageservices/fileservices/set-blob-properties) (Definir metadados do blob), [Set Blob Metadata](/rest/api/storageservices/fileservices/set-blob-metadata) (Definir metadados do blob)e [Setting and Retrieving Properties and Metadata for Blob Resources](/rest/api/storageservices/fileservices/setting-and-retrieving-properties-and-metadata-for-blob-resources) (Definindo e recuperando propriedades e metadados para recursos de blob) para obter as regras detalhadas sobre como definir propriedades e metadados do blob.

