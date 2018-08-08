---
title: Requisitos do serviço de Importação/Exportação do Azure | Microsoft Docs
description: Compreenda os requisitos de hardware e software para o serviço de Importação/Exportação do Azure.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: alkohli
ms.openlocfilehash: a86d5c1513594f5bc0df03b8ca7671a1f9541b4d
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39308047"
---
# <a name="azure-importexport-system-requirements"></a>Requisitos do sistema para Importação/Exportação do Azure

Este artigo descreve os requisitos importantes para o serviço de Importação/Exportação do Azure. Recomendamos que você leia as informações com atenção antes de usar o serviço de Importação/Exportação, e consulte-as quando precisar durante a operação.

## <a name="supported-operating-systems"></a>Sistemas operacionais com suporte

Para preparar os discos rígidos usando a ferramenta WAImportExport, os seguintes **sistemas operacionais de 64 bits dão suporte à Criptografia de Unidade de Disco BitLocker**.


|Plataforma |Versão |
|---------|---------|
|Windows     | Windows 7 Enterprise, Windows 7 Ultimate <br> Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012, Windows Server 2012 R2         |


## <a name="supported-storage-accounts"></a>Contas de armazenamento com suporte

Serviço de importação/exportação do Azure suporta as seguintes [contas de armazenamento do Azure](storage-account-options.md).
- Contas de armazenamento de v1 de uso gerais (implantações clássicas ou do Azure Resource Manager)
- Contas de Armazenamento de Blobs
- Contas de armazenamento de v2 de uso gerais

Cada trabalho pode ser usado para transferir dados para apenas uma conta de armazenamento, ou por meio dela. Em outras palavras, um único trabalho de importação/exportação não pode estender-se por várias contas de armazenamento. Para obter informações sobre como criar uma nova conta de armazenamento, consulte [Como criar uma conta de armazenamento](storage-create-storage-account.md#create-a-storage-account).

> [!IMPORTANT] 
> O serviço de Importação/Exportação do Azure não oferece suporte às contas de armazenamento em que o recurso [Pontos de Extremidade do Serviço de Rede Virtual](../../virtual-network/virtual-network-service-endpoints-overview.md) foi habilitado. 

## <a name="supported-storage-types"></a>Tipos de armazenamento com suporte

Os tipos de armazenamento na lista a seguir têm suporte com o serviço de Importação/Exportação do Azure.


|Trabalho  |Serviço de Armazenamento |Com suporte  |Sem suporte  |
|---------|---------|---------|---------|
|Importar     |  Armazenamento de Blobs do Azure <br><br> Armazenamento de arquivos do Azure       | Blobs de página e Blobs de bloco com suporte <br><br> Arquivos com suporte          |
|Exportação     |   Armazenamento de Blobs do Azure       | Blobs de blocos, blobs de páginas e blobs de acréscimo com suporte         | Arquivos do Azure sem suporte


## <a name="supported-hardware"></a>Hardware com suporte 

Para o serviço de Importação/Exportação do Azure, você precisa de discos com suporte para copiar dados.

### <a name="supported-disks"></a>Discos com suporte

Os discos na lista a seguir têm suporte para uso com o serviço de Importação/Exportação.


|Tipo de disco  |Tamanho  |Com suporte |Sem suporte  |
|---------|---------|---------|---------|
|SSD    |   2,5"      |         |         |
|HDD     |  2,5"<br>3,5"       |SATA II, SATA III         |HDD externo com adaptador interno de USB <br> Disco dentro de um invólucro de HDD externo         |


Um trabalho de importação/exportação único pode ter:
- No máximo 10 HDD/SSDs.
- Uma combinação de HDD/SSD de qualquer tamanho.

Um grande número de unidades pode ser distribuído em vários trabalhos e não há nenhum limite no número de trabalhos que podem ser criados. Para trabalhos de importação, somente o primeiro volume de dados na unidade é processado. O volume de dados deve ser formatado com NTFS.

Ao preparar discos rígidos e copiar os dados usando a ferramenta WAImportExport, você pode usar os adaptadores USB externos a seguir. A maioria dos adaptadores disponíveis no mercado USB 3.0 ou posteriores deve funcionar. 


## <a name="next-steps"></a>Próximas etapas

* [Configurar a ferramenta WAImportExport](storage-import-export-tool-how-to.md)
* [Transferir dados com o utilitário de linha de comando AzCopy](storage-use-azcopy.md)
* [Exemplo de API REST de importação e exportação do Azure](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

