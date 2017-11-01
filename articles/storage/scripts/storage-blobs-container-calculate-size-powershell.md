---
title: "Amostra de script do Azure PowerShell – calcular o tamanho do contêiner de blob | Microsoft Docs"
description: "Calcule o tamanho de um contêiner no Armazenamento de Blobs do Azure, totalizando o tamanho de cada um dos seus blobs."
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 10/23/2017
ms.author: fryu
ms.openlocfilehash: 46f3eac0129da41062caba2da090f9e532505d67
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2017
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Calcular o tamanho de um contêiner de Armazenamento de Blobs

Este script calcula o tamanho de um contêiner no Armazenamento de Blobs do Azure, totalizando o tamanho dos blobs no contêiner.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="understand-the-size-of-blob-storage-container"></a>Entender o tamanho do contêiner de Armazenamento de blobs

O tamanho total do contêiner de Armazenamento de blobs inclui o tamanho do próprio contêiner e o tamanho de todos os blobs no contêiner.

O exemplo a seguir descreve como a capacidade de armazenamento é calculada para Contêineres de blobs e Blobs. Abaixo, Len(X) significa o número de caracteres na cadeia de caracteres.

### <a name="blob-containers"></a>Contêineres de blobs

A seguir, veja como estimar a quantidade de armazenamento consumido por contêiner de blobs:

`
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
`

A seguir, encontra-se a divisão:
* 48 bytes de sobrecarga para cada contêiner inclui a Hora da última modificação, as Permissões, as Configurações públicas e alguns metadados do sistema.
* O nome do contêiner é armazenado como Unicode. Portanto, multiplique o número de caracteres por 2.
* Para cada metadado do contêiner de blobs armazenado, armazenamos o comprimento do nome (armazenado como ASCII), mais o comprimento do valor da cadeia de caracteres.
* Os 512 bytes por Identificador assinado inclui o nome do identificador assinado, hora de início, hora de expiração e as permissões.

### <a name="blobs"></a>Blobs

A seguir, veja como estimar a quantidade de armazenamento consumido por blob:

* Blob de blocos (instantâneo ou blob de base)

`
124 bytes + Len(BlobName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
SizeInBytes(data in unique committed data blocks stored) +
SizeInBytes(data in uncommitted data blocks)
`

* Blob de páginas (instantâneo ou blob de base)

`
124 bytes + Len(BlobName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
number of nonconsecutive page ranges with data * 12 bytes +
SizeInBytes(data in unique pages stored)
`

A seguir, encontra-se a divisão:

* 124 bytes de sobrecarga para o blob, que inclui a Hora da última modificação, o Tamanho, Cache-Control, Content-Type, Content-Language, Content-Encoding, Content-MD5, Permissões, Informações sobre o instantâneo, Concessão e alguns metadados do sistema.
* O nome do blob é armazenado como Unicode. Portanto, multiplique o número de caracteres por 2.
* Em seguida, para cada metadado armazenado, o comprimento do nome (armazenado como ASCII), mais o comprimento do valor da cadeia de caracteres.
* Depois, para Blobs de blocos
    * 8 bytes para a lista de blocos
    * Número de blocos vezes o tamanho da ID do bloco, em bytes
    * Mais o tamanho dos dados em todos os blocos confirmados e não confirmados. Observe que, quando os instantâneos são usados, esse tamanho inclui apenas os dados exclusivos para esse blob de instantâneo ou de base. Se os blocos não confirmados não forem usados após uma semana, eles serão excluídos e, em seguida, nesse momento, eles não serão considerados para a cobrança depois disso.
* Em seguida, para Blobs de páginas
    * Número de intervalos de página não consecutivos que têm dados vezes 12 bytes. Esse é o número de intervalos de páginas exclusivas que você vê ao chamar a API GetPageRanges.
    * Mais o tamanho dos dados, em bytes, de todas as páginas armazenadas. Observe que, quando os instantâneos são usados, esse tamanho inclui apenas as páginas exclusivas para o blob de base ou de instantâneo que está sendo contado.

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a Cobrança do Armazenamento do Azure, consulte [Understanding Microsoft Azure Storage Billing](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/) (Noções básicas sobre a Cobrança do Armazenamento do Microsoft Azure).

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

Amostras adicionais de script do PowerShell de armazenamento podem ser encontradas em [Amostras do PowerShell para Armazenamento do Azure](../blobs/storage-samples-blobs-powershell.md).
