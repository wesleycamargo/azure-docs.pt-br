---
title: Limites do Azure Data Box | Microsoft Docs
description: Descreve os limites do sistema e os tamanhos recomendados para as conexões e os componentes do Microsoft Azure Data Box.
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/05/2018
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: fe42380288c0f139a2bae80a12f0ebc428a4c286
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993063"
---
# <a name="azure-data-box-limits"></a>Limita o Azure Data Box

Considere estes limites ao implantar e operar o Microsoft Azure Data Box. A tabela a seguir descreve esses limites para o Data Box.


## <a name="data-box-service-limits"></a>Limites de serviço do Data Box

 - O serviço Data Box está disponível apenas dentro dos EUA em todas as [Regiões da nuvem pública do Azure](https://azure.microsoft.com/regions/).
 - No caso do uso de várias contas de armazenamento com o serviço Data Box, todas as contas de armazenamento devem pertencer à mesma região do Azure.
 - É recomendado que você não use mais que três contas de armazenamento. O uso de mais contas de armazenamento poderia afetar o desempenho.

## <a name="data-box-limits"></a>Limites do Data Box

- O Data Box pode armazenar no máximo de 5 milhões de arquivos.

## <a name="azure-storage-limits"></a>Limites de armazenamento do Azure

Esta seção descreve os limites de serviço de Armazenamento do Azure e as convenções de nomenclatura necessárias para Arquivos do Azure, blobs de blocos do Azure e blobs de páginas do Azure, conforme aplicável ao serviço Data Box. Examine os limites de armazenamento com cuidado e siga todas as recomendações.

Para obter as informações mais recentes sobre os limites de serviço de armazenamento do Azure e práticas recomendadas para nomear compartilhamentos, contêineres e arquivos, visite:

- [Nomenclatura e referência de contêineres](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Nomenclatura e referência de compartilhamentos](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Convenções de blobs de páginas e blobs de blocos](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Se há quaisquer arquivos ou diretórios que excedem os limites de serviço de armazenamento do Azure ou não estão em conformidade com as convenções de nomenclatura de Blobs/Arquivos do Azure, esses arquivos ou diretórios não são ingeridos no Armazenamento do Azure por meio do serviço Data Box.

## <a name="data-upload-caveats"></a>Limitações de upload de dados

- Não copie dados diretamente em nenhum dos compartilhamentos pré-criados. Você precisa criar uma pasta no compartilhamento e, em seguida, copiar dados para a pasta.
- Uma pasta no *StorageAccount_BlockBlob* e no *StorageAccount_PageBlob* é um contêiner. Por exemplo, os contêineres são criados como *StorageAccount_BlockBlob/container* e *StorageAccount_PageBlob/container*.
- Cada pasta criada diretamente em *StorageAccount_AzureFiles* é convertida em um compartilhamento de arquivos do Azure.
- Se você tiver um objeto existente do Azure (como um blob ou arquivo) na nuvem com o mesmo nome que o objeto que está sendo copiado, o Data Box substituirá o arquivo na nuvem.
- Todos os arquivos gravados nos compartilhamentos *StorageAccount_BlockBlob* e *StorageAccount_PageBlob* são carregados como um blob de blocos e um blob de páginas, respectivamente.
- O Armazenamento de Blobs do Azure não dá suporte a diretórios. Se você criar uma pasta na pasta *StorageAccount_BlockBlob*, as pastas virtuais serão criadas no nome do blob. Para os Arquivos do Azure, a estrutura de diretório real é mantida.
- Não é carregada nenhuma hierarquia de diretórios vazios (sem nenhum arquivo) criada nas pastas *StorageAccount_BlockBlob* e *StorageAccount_PageBlob*.
- Se há erros ao carregar dados no Azure, um log de erros é criado na conta de armazenamento de destino. O caminho para esse log de erros estará disponível quando o upload for concluído e você poderá examinar o log para tomar uma ação corretiva. Não exclua os dados da origem sem verificar os dados carregados.

## <a name="azure-storage-account-size-limits"></a>Limites de tamanho da conta de armazenamento do Azure

Abaixo, são informados os limites do tamanho dos dados que são copiados para a conta de armazenamento. Verifique se os dados carregados por você estão em conformidade com esses limites. Para obter as informações mais atualizadas sobre esses limites, acesse [Destinos de escala de Armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) e [Destinos de escala de Arquivos do Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Tamanho dos dados copiados para a conta de armazenamento do Azure                      | Limite padrão          |
|---------------------------------------------------------------------|------------------------|
| Blob de blocos e blob de páginas                                            | 500 TiB por conta de armazenamento. <br> Isso inclui dados de todas as fontes, incluindo o Data Box.|
| Arquivos do Azure                                                          | 5 TiB por compartilhamento.<br> Todas as pastas *StorageAccount_AzureFiles* precisam seguir esse limite.       |

## <a name="azure-object-size-limits"></a>Limites de tamanho do objeto do Azure

Aqui estão os tamanhos dos objetos do Azure que podem ser gravados. Verifique se todos os arquivos que são carregados estão em conformidade com esses limites.

| Tipo de objeto do Azure | Limite padrão                                             |
|-------------------|-----------------------------------------------------------|
| Blob de blocos        | Aproximadamente 4,75 TiB                                                 |
| Blob de páginas         | 8 TiB <br> Todos os arquivos carregados no formato de blob de páginas precisarão ser alinhados com 512 bytes (um integral múltiplo), caso contrário o upload falhará. <br> O VHD e o VHDX são alinhados a 512 bytes. |
| Arquivos do Azure        | 1 TiB                                                      |

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Convenções de nomenclatura de arquivo, blob de páginas e blob de blocos do Azure

| Entidade                                       | Convenções                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nomes de contêiner de blob de blocos e blob de páginas | Precisa ser um nome DNS válido com tamanho de 3 a 63 caracteres. <br>  Deve começar com uma letra ou número. <br> Pode conter apenas letras minúsculas, números e o hífen (-). <br> Cada hífen (-) precisa ser imediatamente precedido e seguido por uma letra ou um número. <br> Hifens consecutivos não são permitidos em nomes. |
| Nomes de compartilhamento de arquivos do Azure                  | O mesmo que o descrito acima                                                                                                                                                                                                                                                                                                             |
| Nomes de arquivo e diretório para arquivos do Azure     |<li> Uso de maiúsculas e minúsculas preservado, sem diferenciação. Não deve exceder 255 caracteres. </li><li> Não pode terminar com barra (/). </li><li>Se fornecido, será removido automaticamente. </li><li> Os caracteres a seguir não são permitidos: ' "\ /: | < > * ?`</li><li> Os caracteres reservados de URL precisam ser escapados corretamente. </li><li> Os caracteres inválidos para caminho de URL não são permitidos. Pontos de código como \uE000 não são caracteres Unicode válidos. Alguns caracteres ASCII ou Unicode, como caracteres de controle (0x00 a 0x1F, \u0081, etc.), também não são permitidos. Para saber quais são as regras que regem as cadeias de caracteres Unicode em HTTP/1.1, confira o RFC 2616, Section 2.2: Basic Rules (RFC 2616, Seção 2.2: regras básicas) e o RFC 3987. </li><li> Os nomes de arquivo a seguir não são permitidos: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, caractere de ponto (.) e de dois pontos (..).</li>|
| Nomes de blob para blobs de blocos e blobs de página      | </li><li>Nomes de blob diferenciam maiúsculas de minúsculas e podem conter qualquer combinação de caracteres. </li><li>O nome do blob deve ter entre 1 e 1.024 caracteres. </li><li>Os caracteres reservados de URL precisam ser escapados corretamente. </li><li>O número de segmentos de caminho que incluem o nome do blob não pode exceder 254. Um segmento de caminho é a cadeia de caracteres entre caracteres delimitadores consecutivos (por exemplo, a barra '/') que correspondem ao nome de um diretório virtual.</li> |
