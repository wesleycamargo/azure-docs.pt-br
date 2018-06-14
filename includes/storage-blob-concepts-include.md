---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/09/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 203f5a766c4c8a8f1e577f6be1e18d0f9ac95403
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
ms.locfileid: "31433265"
---
O Armazenamento de Blobs do Azure é uma solução de armazenamento de objetos da Microsoft para a nuvem. O armazenamento de blobs é otimizado para armazenar grandes quantidades de dados não estruturados, como texto ou dados binários.

O armazenamento de Blob é ideal para:

* Fornecimento de imagens ou de documentos diretamente a um navegador.
* Armazenamento de arquivos para acesso distribuído.
* Transmissão por streaming de áudio e vídeo.
* Gravando nos arquivo de log.
* Armazenamento de dados de backup e restauração, recuperação de desastres e arquivamento.
* Armazenamento de dados para análise por um serviço local ou hospedado no Azure.

Os objetos no armazenamento de Blobs podem ser acessados de qualquer lugar no mundo via HTTP ou HTTPS. Usuários ou aplicativos cliente podem acessar blobs por meio de URLs, da [API REST do Armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), do [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), da [CLI do Azure](https://docs.microsoft.com/cli/azure/storage) ou de uma biblioteca de cliente de Armazenamento do Azure. As bibliotecas de clientes de armazenamento estão disponíveis para várias linguagens, incluindo [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client), [Node.js](http://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/en/latest/index.html), [PHP](http://azure.github.io/azure-storage-php/) e [Ruby](http://azure.github.io/azure-storage-ruby).

## <a name="blob-service-concepts"></a>Conceitos do Serviço Blob

O armazenamento de Blobs expõe três recursos: sua conta de armazenamento, os contêineres na conta e os blobs em um contêiner. O diagrama a seguir mostra a relação entre esses recursos.

![Diagrama da arquitetura de armazenamento de Blob (objeto)](./media/storage-blob-concepts-include/blob1.png)

### <a name="storage-account"></a>Conta de armazenamento

Todo o acesso a objetos de dados no Armazenamento do Azure ocorre por meio de uma conta de armazenamento. Para saber mais, confira [Sobre as contas de armazenamento do Azure](../articles/storage/common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="container"></a>Contêiner

Um contêiner organiza um conjunto de blobs, semelhantes a uma pasta em um sistema de arquivos. Todos os blobs residem dentro de um contêiner. Uma conta de armazenamento pode conter um número ilimitado de contêineres e um contêiner pode armazenar um número ilimitado de blobs. Observe que o nome do contêiner deve estar em letras minúsculas.

### <a name="blob"></a>Blob
 
O Armazenamento do Azure oferece três tipos de blobs: blobs de bloco, blobs de acréscimo e [blobs de página](../articles/storage/blobs/storage-blob-pageblob-overview.md) (usados para arquivos VHD).

* Blobs de bloco armazenam texto e dados binários, até aproximadamente 4,7 TB. Blobs de bloco são compostos de blocos de dados que podem ser gerenciados individualmente.
* Blobs de acréscimo são compostos de blocos, como blobs de blocos, mas são otimizados para operações de acréscimo. Blobs de acréscimo são ideais para cenários como o registro de dados das máquinas virtuais.
* Blobs de páginas armazenam arquivos de acesso aleatório de até 8 TB. Os blobs de página armazenam os arquivos VHD que dão suporte às VMs.

Todos os blobs residem dentro de um contêiner. Um contêiner é semelhante a uma pasta em um sistema de arquivos. Você pode organizar ainda mais os blobs em diretórios virtuais e percorrê-los como faria com um sistema de arquivos. 

Para grandes conjuntos de dados onde as restrições de rede fazem com que o carregamento ou download de dados para o armazenamento de Blobs seja virtualmente impossível, você pode enviar um conjunto de discos rígidos para a Microsoft para importar ou exportar dados diretamente a partir do data center. Para saber mais, veja [Usar o serviço de Importação/Exportação do Microsoft Azure para transferir dados ao armazenamento Blob](../articles/storage/common/storage-import-export-service.md).
  
Para obter detalhes sobre como nomear contêineres e blobs, confira [Nomenclatura e referência de contêineres, blobs e metadados](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).
