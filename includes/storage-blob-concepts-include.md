---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/17/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 7889fbc9373cbdfdfab891bf8b1cd610523c7032
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50088084"
---
O Armazenamento de Blobs do Azure é uma solução de armazenamento de objetos da Microsoft para a nuvem. O armazenamento de Blobs é otimizado para armazenar grandes quantidades de dados não estruturados, como texto ou dados binários.

O armazenamento de Blobs é ideal para:

* Fornecimento de imagens ou de documentos diretamente a um navegador.
* Armazenamento de arquivos para acesso distribuído.
* Transmissão por streaming de áudio e vídeo.
* Gravando nos arquivo de log.
* Armazenamento de dados de backup e restauração, recuperação de desastres e arquivamento.
* Armazenamento de dados para análise por um serviço local ou hospedado no Azure.

Os usuários ou aplicativos cliente podem acessar objetos de armazenamento de blobs via HTTP ou HTTPS&mdash;de qualquer lugar no mundo&mdash;por meio de URLs, da [API REST do Armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), do [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), da [CLI do Azure](https://docs.microsoft.com/cli/azure/storage) ou de uma biblioteca de clientes do Armazenamento do Azure. As bibliotecas de clientes de armazenamento estão disponíveis para várias linguagens, incluindo [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client), [Node.js](http://azure.github.io/azure-storage-node), [Python](https://docs.microsoft.com/python/azure/), [PHP](http://azure.github.io/azure-storage-php/) e [Ruby](http://azure.github.io/azure-storage-ruby).

## <a name="blob-service-concepts"></a>Conceitos do Serviço Blob

O armazenamento de Blobs expõe três recursos: sua conta de armazenamento, os contêineres na conta e os blobs em um contêiner. O diagrama a seguir mostra a relação entre esses recursos.

![Diagrama da arquitetura de armazenamento de Blob (objeto)](./media/storage-blob-concepts-include/blob1.png)

### <a name="storage-account"></a>Conta de armazenamento

Todo o acesso a objetos de dados no Armazenamento do Azure ocorre por meio de uma conta de armazenamento. Para saber mais, confira [Visão geral da conta de armazenamento do Azure](../articles/storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="container"></a>Contêiner

Um contêiner organiza um conjunto de blobs, semelhantes a uma pasta em um sistema de arquivos. Todos os blobs residem dentro de um contêiner. Uma conta de armazenamento pode incluir um número ilimitado de contêineres e um contêiner pode armazenar um número ilimitado de blobs. 

  > [!NOTE]
  > O nome do contêiner deve estar em letras minúsculas.

### <a name="blob"></a>Blob
 
O Armazenamento do Azure oferece três tipos de blobs&mdash;blobs de bloco, blobs de acréscimo e [blobs de página](../articles/storage/blobs/storage-blob-pageblob-overview.md) (usados para arquivos VHD).

* Blobs de bloco armazenam texto e dados binários, até aproximadamente 4,7 TB. Blobs de bloco são compostos de blocos de dados que podem ser gerenciados individualmente.
* Blobs de acréscimo são compostos de blocos, como blobs de blocos, mas são otimizados para operações de acréscimo. Blobs de acréscimo são ideais para cenários como o registro de dados das máquinas virtuais.
* Blobs de páginas armazenam arquivos de acesso aleatório de até 8 TB. Os blobs de página armazenam os arquivos VHD que dão suporte às VMs.

Todos os blobs residem dentro de um contêiner. Um contêiner é semelhante a uma pasta em um sistema de arquivos. Você pode organizar ainda mais os blobs em diretórios virtuais e navegar por eles como faria com um sistema de arquivos. 

Pode haver ocasiões em que grandes conjuntos de dados e restrições de rede inviabilizem o carregamento de dados para o armazenamento de blobs eletronicamente. Você pode usar o [Azure Data Box Disk](../articles/databox/data-box-disk-overview.md) para solicitar SSDs (unidades de estado sólido) à Microsoft. Você pode copiar seus dados para os discos e enviá-los à Microsoft para serem carregados no armazenamento de blobs.

Se você precisar exportar grandes quantidades de dados da conta de armazenamento, confira [Usar o serviço Importação/Exportação do Microsoft Azure a fim de transferir dados para o armazenamento de blobs](../articles/storage/common/storage-import-export-service.md).
  
Para obter detalhes sobre como nomear contêineres e blobs, confira [Nomenclatura e referência de contêineres, blobs e metadados](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).
