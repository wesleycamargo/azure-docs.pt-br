---
title: Solucionar problemas em sua caixa de dados do Azure | Microsoft Docs
description: Descreve como solucionar problemas encontrados no Azure Data Box quando carregar dados no Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 04/26/2019
ms.author: alkohli
ms.openlocfilehash: 329ea6ac52e972c42e0e2aab247a7ad8e04c76e7
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927351"
---
# <a name="troubleshoot-issues-related-to-azure-data-box"></a>Solucionar problemas relacionados ao Azure Data Box

Este artigo fornece detalhes para obter informações sobre como solucionar problemas que você pode ver ao usar o Azure Data Box.

## <a name="errors-during-data-copy"></a>Erros durante a cópia de dados

Todos os erros que são vistos durante a cópia de dados são resumidos nas seções a seguir.

### <a name="errorcontainerorsharenamelength"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH 

**Descrição do erro:** O nome do compartilhamento ou do contêiner deve ter entre 3 e 63 caracteres.

**Resolução sugerida:** A pasta no compartilhamento de dados de caixa (SMB e NFS) ao qual você copiou os dados se torna um contêiner do Azure em sua conta de armazenamento. 

- Sobre o **conectar e copiar** página de interface do usuário da web local Data Box, download e nomes de arquivos de erros para identificar a pasta com problemas de revisão.
- Altere o nome da pasta no compartilhamento de dados de caixa para certificar-se de que:

    - O nome tem entre 3 e 63 caracteres.
    - Os nomes podem ter apenas letras, números e hifens.
    - Os nomes não podem começar ou terminar com hifens.
    - Os nomes não podem ter hifens consecutivos.
    - Exemplos de nomes válidos: `my-folder-1`, `my-really-extra-long-folder-111`</li><li>
    - Exemplos de nomes que não são válidos: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    Para obter mais informações, consulte as convenções de nomenclatura do Azure para [nomes de contêiner](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) e [nomes de compartilhamento](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

### <a name="errorcontainerorsharenamelength"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**Descrição do erro:** O nome do compartilhamento ou do contêiner deve ter entre 3 e 63 caracteres. 

**Resolução sugerida:** A pasta no compartilhamento de dados de caixa (SMB e NFS) ao qual você copiou os dados se torna um contêiner do Azure em sua conta de armazenamento. 

- Sobre o **conectar e copiar** página de interface do usuário da web local Data Box, download e nomes de arquivos de erros para identificar a pasta com problemas de revisão.
- Altere o nome da pasta no compartilhamento de dados de caixa para certificar-se de que:

    - O nome tem entre 3 e 63 caracteres.
    - Os nomes podem ter apenas letras, números e hifens.
    - Os nomes não podem começar ou terminar com hifens.
    - Os nomes não podem ter hifens consecutivos.
    - Exemplos de nomes válidos: `my-folder-1`, `my-really-extra-long-folder-111`
    - Exemplos de nomes que não são válidos: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    Para obter mais informações, consulte as convenções de nomenclatura do Azure para [nomes de contêiner](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) e [nomes de compartilhamento](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).


### <a name="errorcontainerorsharenamealphanumericdash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**Descrição do erro:** O nome do compartilhamento ou do contêiner deve consistir apenas em letras, números ou hifens.

**Resolução sugerida:** A pasta no compartilhamento de dados de caixa (SMB e NFS) ao qual você copiou os dados se torna um contêiner do Azure em sua conta de armazenamento. 

- Sobre o **conectar e copiar** página de interface do usuário da web local Data Box, download e nomes de arquivos de erros para identificar a pasta com problemas de revisão.
- Altere o nome da pasta no compartilhamento de dados de caixa para certificar-se de que:

    - O nome tem entre 3 e 63 caracteres.
    - Os nomes podem ter apenas letras, números e hifens.
    - Os nomes não podem começar ou terminar com hifens.
    - Os nomes não podem ter hifens consecutivos.
    - Exemplos de nomes válidos: `my-folder-1`, `my-really-extra-long-folder-111`
    - Exemplos de nomes que não são válidos: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    Para obter mais informações, consulte as convenções de nomenclatura do Azure para [nomes de contêiner](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) e [nomes de compartilhamento](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

### <a name="errorcontainerorsharenameimproperdash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**Descrição do erro:** Os nomes de contêiner e nomes de compartilhamento não é possível iniciar ou terminar com hifens e não pode ter hifens consecutivos.

**Resolução sugerida:** A pasta no compartilhamento de dados de caixa (SMB e NFS) ao qual você copiou os dados se torna um contêiner do Azure em sua conta de armazenamento. 

- Sobre o **conectar e copiar** página de interface do usuário da web local Data Box, download e nomes de arquivos de erros para identificar a pasta com problemas de revisão.
- Altere o nome da pasta no compartilhamento de dados de caixa para certificar-se de que:

    - O nome tem entre 3 e 63 caracteres.
    - Os nomes podem ter apenas letras, números e hifens.
    - Os nomes não podem começar ou terminar com hifens.
    - Os nomes não podem ter hifens consecutivos.
    - Exemplos de nomes válidos: `my-folder-1`, `my-really-extra-long-folder-111`
    - Exemplos de nomes que não são válidos: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    Para obter mais informações, consulte as convenções de nomenclatura do Azure para [nomes de contêiner](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) e [nomes de compartilhamento](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

### <a name="errorcontainerorsharenamedisallowedfortype"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**Descrição do erro:** Os nomes de contêiner inadequada são especificados para os compartilhamentos de disco gerenciado.

**Resolução sugerida:** Para discos gerenciados, dentro de cada compartilhamento, as seguintes pastas são criadas que correspondem aos contêineres na conta de armazenamento: SSD Premium, padrão HDD e SSD Standard. Essas pastas correspondem à camada de desempenho para o disco gerenciado.

- Certifique-se de que você copie seus dados de blob de página (VHDs) em uma dessas pastas existentes. Somente os dados de um desses contêineres existentes são carregados no Azure.
- Qualquer outra pasta que é criada no mesmo nível como SSD Premium, padrão HDD e SSD Standard não corresponde a um nível de desempenho válido e não pode ser usada.
- Remova arquivos ou pastas criadas fora de níveis de desempenho.

Para obter mais informações, consulte [cópia em discos gerenciados](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).

### <a name="errorcontainerorsharecapacityexceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**Descrição do erro:** Compartilhamento de arquivos do Azure limita a um compartilhamento de 5 TB de dados. Esse limite foi excedido para alguns compartilhamentos.

**Resolução sugerida:** Sobre o **conectar e copiar** página da caixa de dados interface web local, baixe e examine os arquivos de erro.

Identifique as pastas que apresentam esse problema dos logs de erro e certifique-se de que os arquivos nessa pasta são abaixo de 5 TB.

### <a name="errorbloborfilenamecharactercontrol"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**Descrição do erro:** Os nomes de blob ou arquivo contêm caracteres de controle sem suporte.

**Resolução sugerida:** Os blobs ou os arquivos que você copiou contêm nomes com caracteres sem suporte.

Sobre o **conectar e copiar** página da web local da interface do usuário, baixe e examine os arquivos de erro.
Remova ou renomeie os arquivos para remover caracteres sem suporte.

Para obter mais informações, consulte as convenções de nomenclatura do Azure para [nomes de blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e [nomes de arquivo](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).

### <a name="errorbloborfilenamecharacterillegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**Descrição do erro:** Os nomes de blob ou arquivo contêm caracteres inválidos.

**Resolução sugerida:** Os blobs ou os arquivos que você copiou contêm nomes com caracteres sem suporte.

Sobre o **conectar e copiar** página da web local da interface do usuário, baixe e examine os arquivos de erro.
Remova ou renomeie os arquivos para remover caracteres sem suporte.

Para obter mais informações, consulte as convenções de nomenclatura do Azure para [nomes de blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e [nomes de arquivo](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="errorbloborfilenameending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**Descrição do erro:** Os nomes de blob ou arquivo estão terminando com caracteres inválidos.

**Resolução sugerida:** Os blobs ou os arquivos que você copiou contêm nomes com caracteres sem suporte.

Sobre o **conectar e copiar** página da web local da interface do usuário, baixe e examine os arquivos de erro.
Remova ou renomeie os arquivos para remover caracteres sem suporte.

Para obter mais informações, consulte as convenções de nomenclatura do Azure para [nomes de blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e [nomes de arquivo](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="errorbloborfilenamesegmentcount"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**Descrição do erro:** O nome de arquivo ou blob contém muitos segmentos de caminho.

**Resolução sugerida:** Os blobs ou os arquivos que você copiou excederem o número máximo de segmentos de caminho. Um segmento de caminho é a cadeia de caracteres entre os caracteres delimitadores consecutivos, por exemplo, a barra invertida /.

- Sobre o **conectar e copiar** página da web local da interface do usuário, baixe e examine os arquivos de erro.
- Certifique-se de que o [nomes de blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e [nomes de arquivo](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) estão em conformidade com as convenções de nomenclatura do Azure.

### <a name="errorbloborfilenameaggregatelength"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**Descrição do erro:** O nome de blob ou do arquivo é muito longo.

**Resolução sugerida:** Os nomes de arquivo ou blob excederem o comprimento máximo.

- Sobre o **conectar e copiar** página da web local da interface do usuário, baixe e examine os arquivos de erro.
- O nome do blob não deve exceder 1.024 caracteres.
- Remova ou renomeie os arquivos ou um blob, para que os nomes não excederem 1.024 caracteres.

Para obter mais informações, consulte as convenções de nomenclatura do Azure para nomes de arquivo e nomes de blob.

### <a name="errorbloborfilenamecomponentlength"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**Descrição do erro:** Um dos segmentos no nome do blob ou do arquivo é muito longo.

**Resolução sugerida:** Um dos segmentos de caminho no nome do blob ou arquivo excede o número máximo de caracteres. Um segmento de caminho é a cadeia de caracteres entre os caracteres delimitadores consecutivos, por exemplo, a barra invertida /.

- Sobre o **conectar e copiar** página da web local da interface do usuário, baixe e examine os arquivos de erro.
- Certifique-se de que o [nomes de blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e [nomes de arquivo](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) estão em conformidade com as convenções de nomenclatura do Azure.

### <a name="errorbloborfilesizelimit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**Descrição do erro:** O tamanho do arquivo excede o tamanho máximo do arquivo para upload.

**Resolução sugerida:** O blob ou o tamanho dos arquivos excede o limite máximo permitido para carregamento.

- Sobre o **conectar e copiar** página da web local da interface do usuário, baixe e examine os arquivos de erro.
- Certifique-se de que os tamanhos de blob e arquivo não excedam os limites de tamanho do objeto do Azure.

### <a name="errorbloborfilesizealignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**Descrição do erro:** O blob ou arquivo está alinhado incorretamente.

**Resolução sugerida:** O compartilhamento de blob de página na caixa de dados somente dá suporte a arquivos que são de 512 bytes alinhado (por exemplo, VHD/VHDX). Todos os dados copiados para o compartilhamento de blob de página são carregados no Azure como blobs de página.

Remova todos os dados não-VHD/VHDX do compartilhamento de blob de página. Você pode usar compartilhamentos de blob de blocos ou os arquivos do Azure para dados genéricos.

Para obter mais informações, consulte [blobs de página de visão geral de](../storage/blobs/storage-blob-pageblob-overview.md).

### <a name="errorbloborfiletypeunsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**Descrição do erro:** Um tipo de arquivo sem suporte está presente em um compartilhamento de disco gerenciado. Somente VHDs fixos são permitidos.

**Resolução sugerida:**

- Certifique-se de que você só pode carregar os VHDs fixos para criar discos gerenciados.
- Arquivos VHDX ou **dinâmica** e **diferenciais** VHDs não têm suporte.

### <a name="errordirectorydisallowedfortype"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**Descrição do erro:** Um diretório não é permitido em qualquer uma das pastas já existentes para os discos gerenciados. Somente VHDs fixos são permitidos nessas pastas.

**Resolução sugerida:** Para discos gerenciados, dentro de cada compartilhamento, as três pastas a seguir são criadas que correspondem aos contêineres na conta de armazenamento: SSD Premium, padrão HDD e SSD Standard. Essas pastas correspondem à camada de desempenho para o disco gerenciado.

- Certifique-se de que você copie seus dados de blob de página (VHDs) em uma dessas pastas existentes.
- Uma pasta ou diretório não é permitido nessas pastas existentes. Remova todas as pastas que você criou dentro das pastas já existentes.

Para obter mais informações, consulte [cópia em discos gerenciados](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [requisitos de sistema de armazenamento de Blob de caixa de dados](data-box-system-requirements-rest.md).
