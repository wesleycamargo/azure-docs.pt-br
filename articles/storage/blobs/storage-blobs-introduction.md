---
title: Introdução ao armazenamento de Blobs – armazenamento de objetos no Azure | Microsoft Docs
description: O armazenamento de Blobs do Azure é projetado para armazenar grandes quantidades de dados de objeto não estruturados, como texto ou dados binários. Os aplicativos podem acessar objetos no armazenamento de Blobs do PowerShell ou da CLI do Azure a partir do código por meio de bibliotecas de cliente de Armazenamento do Azure ou em REST.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: overview
ms.date: 03/27/2018
ms.author: tamram
ms.openlocfilehash: 0fff0032ec2452413bcd1df3175634b14a64208f
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2018
---
# <a name="introduction-to-blob-storage"></a>Introdução ao armazenamento de Blobs

o armazenamento de Blobs do Azure é uma solução de armazenamento em nuvem da Microsoft para objetos de dados. O armazenamento de Blobs pode armazenar grandes quantidades de dados de objeto não estruturados, como texto ou dados binários. Os dados no armazenamento de Blobs podem ser acessados de qualquer lugar no mundo via HTTP ou HTTPS. Você pode usar o armazenamento de Blob para expor dados publicamente para o mundo ou para armazenar dados do aplicativo de forma privada.

Usos comuns de armazenamento de Blob incluem:

* Fornecer imagens ou documentos diretamente a um navegador
* Armazenar arquivos para acesso distribuído
* Transmitir por streaming áudio e vídeo
* Armazenando para backup e restauração, recuperação de desastres e arquivamento
* Armazenando dados para análise por um serviço local ou hospedado do Azure
* Armazenando VHDs para usar com as máquinas virtuais do Azure

## <a name="blob-service-concepts"></a>Conceitos do Serviço Blob

O serviço Blob contém os seguintes componentes:

![Arquitetura de blob](./media/storage-blobs-introduction/blob1.png)

* **Conta de Armazenamento:** todo o acesso ao Armazenamento do Azure é feito através de uma conta de armazenamento. Esta conta de armazenamento pode ser uma **conta de armazenamento de uso geral (v1 ou v2)** ou podem ser **contas de armazenamento de Blobs**. Consulte [Sobre as contas de armazenamento do Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para obter mais informações.

* **Contêiner:** um contêiner fornece um agrupamento de conjunto de blobs. Todos os blobs devem ter um contêiner. Uma conta pode conter um número ilimitado de contêineres. Um contêiner pode armazenar um número ilimitado de blobs. Observe que o nome do contêiner deve estar em letras minúsculas.

* **Blob:** um arquivo de qualquer tipo e tamanho. O Armazenamento do Azure oferece três tipos de blobs: blobs de blocos, [blobs de páginas](storage-blob-pageblob-overview.md) e blobs de acréscimo.
  
    *Blobs de blocos* são ideais para armazenar arquivos de texto ou binários, como documentos e arquivos de mídia. *Blobs de anexo* são semelhantes aos blobs de blocos, pois são constituídos de blocos, mas são otimizados para anexas operações. Portanto, são úteis em cenários de registro em log. Um blob de blocos único pode conter até 50.000 blocos de até 100 MB cada um, com um tamanho total de pouco mais de 4,75 TB (100 MB X 50.000). Um blob de acréscimo único pode conter até 50.000 blocos de até 4 MB cada um, com um tamanho total de pouco mais de 195 GB (4 MB X 50.000).
  
    *Blobs de páginas* podem ter até 8 TB e são mais eficientes para operações frequentes de leitura/gravação. Máquinas virtuais do Azure usam blobs de páginas como sistema operacional e discos de dados.
  
    Para obter detalhes sobre como nomear contêineres e blobs, confira [Nomenclatura e referência de contêineres, blobs e metadados](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).

## <a name="next-steps"></a>Próximas etapas

* [Criar uma conta de armazenamento](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Introdução ao armazenamento de blobs usando .NET](storage-dotnet-how-to-use-blobs.md)
* [Exemplos de Armazenamento do Azure usando .NET](../common/storage-samples-dotnet.md)
* [Exemplos de Armazenamento do Azure usando Java](../common/storage-samples-java.md)
