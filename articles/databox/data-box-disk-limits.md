---
title: Limites de Azure Data Box Disk | Microsoft Docs
description: Descreve os limites do sistema e tamanhos recomendados para o Microsoft Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 02/05/2019
ms.author: alkohli
ms.openlocfilehash: 6a7f7943e9d567a953c0e21697dfe4fdedd6e8f0
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55744782"
---
# <a name="azure-data-box-disk-limits"></a>Limites do Azure Data Box Disk


Considere esses limites ao implantar e operar sua solução de Microsoft Azure Data Box Disk. 

## <a name="data-box-service-limits"></a>Limites de serviço do Data Box

 - O serviço do Data Box está disponível somente nos EUA, UE, Canadá e Austrália em todas as regiões do Azure para nuvem pública do Azure.
 - Uma única conta de armazenamento é compatível com o Data Box Disk.

## <a name="data-box-disk-performance"></a>Desempenho do Data Box Disk

Quando testado com discos conectados via USB 3.0, o desempenho do disco foi de até 430 MB/s. Os números reais variam dependendo do tamanho do arquivo usado. Para arquivos menores, o desempenho poderá ser mais baixo.

## <a name="azure-storage-limits"></a>Limites de armazenamento do Azure

Esta seção descreve os limites de serviço de Armazenamento do Azure e as convenções de nomenclatura necessárias para Arquivos do Azure, blobs de blocos do Azure e blobs de páginas do Azure, conforme aplicável ao serviço Data Box. Examine os limites de armazenamento com cuidado e siga todas as recomendações.

Para obter as informações mais recentes sobre os limites de serviço de armazenamento do Azure e práticas recomendadas para nomear compartilhamentos, contêineres e arquivos, visite:

- [Nomenclatura e referência de contêineres](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Nomenclatura e referência de compartilhamentos](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Convenções de blobs de páginas e blobs de blocos](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Se há quaisquer arquivos ou diretórios que excedem os limites de serviço de armazenamento do Azure ou não estão em conformidade com as convenções de nomenclatura de Blobs/Arquivos do Azure, esses arquivos ou diretórios não são ingeridos no Armazenamento do Azure por meio do serviço Data Box.

## <a name="data-upload-caveats"></a>Limitações de upload de dados

- Não copie dados diretamente para os discos. Copiar dados para pastas *BlockBlob* e *PageBlob* pré-criadas.
- Uma pasta sob as pastas *BlockBlob* e *PageBlob* é um contêiner. Por exemplo, os contêineres são criados como *BlockBlob/contêiner* e *PageBlob/contêiner*.
- Se você tiver um objeto existente do Azure (como um blob) na nuvem com o mesmo nome que o objeto que está sendo copiado, o Data Box Disk substituirá o arquivo na nuvem.
- Todos os arquivos gravados nos compartilhamentos *BlockBlob* e *PageBlob* são carregados como um blob de blocos e um blob de páginas, respectivamente.
- Qualquer hierarquia de diretórios vazios (sem nenhum arquivo) criada sob as pastas *BlockBlob* e *PageBlob* não é carregada.
- Se há erros ao carregar dados no Azure, um log de erros é criado na conta de armazenamento de destino. O caminho para esse log de erros está disponível no portal quando o upload é concluído e você pode examinar o log para tomar uma ação corretiva. Não exclua os dados da origem sem verificar os dados carregados.

## <a name="azure-storage-account-size-limits"></a>Limites de tamanho da conta de armazenamento do Azure

Abaixo, são informados os limites do tamanho dos dados que são copiados para a conta de armazenamento. Verifique se os dados carregados por você estão em conformidade com esses limites. Para obter as informações mais atualizadas sobre esses limites, acesse [Destinos de escala de Armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) e [Destinos de escala de Arquivos do Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Tamanho dos dados copiados para a conta de armazenamento do Azure                      | Limite padrão          |
|---------------------------------------------------------------------|------------------------|
| Blob de páginas e blob de blocos                                            | 500 TB por conta de armazenamento. <br> Isso inclui dados de todas as fontes, incluindo o Data Box Disk.|


## <a name="azure-object-size-limits"></a>Limites de tamanho do objeto do Azure

Aqui estão os tamanhos dos objetos do Azure que podem ser gravados. Verifique se todos os arquivos que são carregados estão em conformidade com esses limites.

| Tipo de objeto do Azure | Limite padrão                                             |
|-------------------|-----------------------------------------------------------|
| Blob de blocos        | Aproximadamente 4,75 TiB                                                 |
| Blob de páginas         | 8 TiB <br> (Todos os arquivos carregados no formato de Blob de Páginas precisarão ser alinhados com 512 bytes (um integral múltiplo), caso contrário o upload falhará. <br> O VHD e VHDX são alinhados com 512 bytes.) |


## <a name="azure-block-blob-and-page-blob-naming-conventions"></a>Convenções de nomenclatura de blob de páginas e blob de blocos do Azure

| Entidade                                       | Convenções                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nomes de contêiner de blob de blocos e blob de páginas | Precisa ser um nome DNS válido com tamanho de 3 a 63 caracteres. <br>  Deve começar com uma letra ou número. <br> Pode conter apenas letras minúsculas, números e o hífen (-). <br> Cada hífen (-) precisa ser imediatamente precedido e seguido por uma letra ou um número. <br> Hifens consecutivos não são permitidos em nomes. |
| Nomes de blob para blob de blocos e blob de páginas      | Nomes de blob diferenciam maiúsculas de minúsculas e podem conter qualquer combinação de caracteres. <br> O nome do blob deve ter entre 1 e 1.024 caracteres. <br> Os caracteres reservados de URL precisam ser escapados corretamente. <br>O número de segmentos de caminho que incluem o nome do blob não pode exceder 254. Um segmento de caminho é a cadeia de caracteres entre caracteres delimitadores consecutivos (por exemplo, a barra '/') que correspondem ao nome de um diretório virtual. |


## <a name="next-steps"></a>Próximas etapas
* Examine os [requisitos de sistema do Data Box](data-box-system-requirements.md)
