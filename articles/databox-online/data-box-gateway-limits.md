---
title: Limites do Azure Data Box Gateway | Microsoft Docs
description: Descreve os limites do sistema e tamanhos recomendados para o Microsoft Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/03/2018
ms.author: alkohli
ms.openlocfilehash: 60078845c98f2e241b00e184303dce0c860629e9
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2018
ms.locfileid: "49164426"
---
# <a name="azure-data-box-gateway-limits-preview"></a>Limites do Azure Data Box Gateway (versão prévia)


Considere esses limites ao implantar e operar sua solução do Microsoft Azure Data Box Gateway. 

> [!IMPORTANT] 
> O Data Box Gateway está em versão prévia. Examine os [termos de uso para a versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) antes de implantar essa solução. 


## <a name="data-box-gateway-service-limits"></a>Limites do serviço do Data Box Gateway

- Nesta versão, o serviço está disponível somente em determinadas regiões nos EUA, Europa e Pacífico Asiático. Para saber mais, acesse [Disponibilidade por região](#data-box-gateway-overview#region-availability). A conta de armazenamento deve estar fisicamente próxima à região em que o dispositivo foi implantado (pode ser diferente da área geográfica do serviço).
- Não há suporte para mover um recurso do Data Box Gateway para um grupo de recursos ou assinatura diferente. Para saber mais, confira [Mover recursos para um novo grupo de recursos ou assinatura](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).

## <a name="data-box-gateway-device-limits"></a>Limites de dispositivo do Data Box Gateway

A tabela a seguir descreve esses limites para o dispositivo do Data Box Gateway.

| DESCRIÇÃO | Valor |
|---|---|
|Não. de arquivos por dispositivo |100 milhões <br> O limite é aproximadamente 25 milhões de arquivos para cada 2 TB de espaço em disco com o limite máximo em 100 milhões |
|Não. de compartilhamentos por dispositivo |24 |
|Tamanho máximo do arquivo gravado em um compartilhamento|Para um dispositivo virtual de 2 TB, o tamanho máximo do arquivo é de 500 GB. <br> O tamanho máximo do arquivo aumenta com o tamanho do disco de dados na proporção anterior até atingir um máximo de 5 TB. |

## <a name="azure-storage-limits"></a>Limites de armazenamento do Azure

Esta seção descreve os limites de serviço de Armazenamento do Microsoft Azure e as convenções de nomenclatura necessárias para Arquivos do Azure, blobs de blocos do Azure e blobs de páginas do Azure, conforme aplicável ao serviço de Data Box Gateway/Data Box Edge. Examine os limites de armazenamento com cuidado e siga todas as recomendações.

Para obter as informações mais recentes sobre os limites de serviço de armazenamento do Azure e práticas recomendadas para nomear compartilhamentos, contêineres e arquivos, visite:

- [Nomenclatura e referência de contêineres](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Nomenclatura e referência de compartilhamentos](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Convenções de blobs de páginas e blobs de blocos](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Se há quaisquer arquivos ou diretórios que excedem os limites de serviço de Armazenamento do Microsoft Azure ou não estão em conformidade com as convenções de nomenclatura de Blobs/Arquivos do Azure, esses arquivos ou diretórios não são ingeridos no Armazenamento do Azure por meio do serviço Data Box Edge/Data Box Gateway.

## <a name="data-upload-caveats"></a>Limitações de upload de dados

As limitações a seguir se aplicam aos dados conforme são movidos para o Azure.

- Sugerimos que mais de um dispositivo não deve gravar no mesmo contêiner.
- Se você tiver um objeto existente do Azure (como um blob ou arquivo) na nuvem com o mesmo nome que o objeto que está sendo copiado, o dispositivo substituirá o arquivo na nuvem. 
- Uma hierarquia de diretórios vazios (sem nenhum arquivo) criada sob as pastas de compartilhamento não é carregada nos contêineres de blob.


## <a name="azure-storage-account-size-and-object-size-limits"></a>Limites de tamanho da conta de armazenamento e tamanho do objeto do Azure

Abaixo, são informados os limites do tamanho dos dados que são copiados para a conta de armazenamento. Verifique se os dados carregados por você estão em conformidade com esses limites. Para obter as informações mais atualizadas sobre esses limites, acesse [Destinos de escala de Armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) e [Destinos de escala de Arquivos do Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Tamanho dos dados copiados para a conta de armazenamento do Azure                      | Limite padrão          |
|---------------------------------------------------------------------|------------------------|
| Blob de páginas e blob de blocos                                            | 500 TB por conta de armazenamento|


## <a name="azure-object-size-limits"></a>Limites de tamanho do objeto do Azure

Aqui estão os tamanhos dos objetos do Azure que podem ser gravados. Verifique se todos os arquivos que são carregados estão em conformidade com esses limites.

| Tipo de objeto do Azure | Limite padrão                                             |
|-------------------|-----------------------------------------------------------|
| Blob de blocos        | ~ 8 TB                                                 |
| Blob de páginas         | 1 TB <br> Todos os arquivos carregados no formato de Blob de Páginas precisarão ser alinhados com 512 bytes (um integral múltiplo), caso contrário o upload falhará. <br> O VHD e o VHDX são alinhados com 512 bytes. |


## <a name="next-steps"></a>Próximas etapas

- [Preparar para implantar o Gateway do Azure Data Box](data-box-gateway-deploy-prep.md)
