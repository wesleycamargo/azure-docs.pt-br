---
title: "Armazenamento do Azure de pilha: As diferenças e considerações"
description: "Entenda as diferenças entre o armazenamento de pilha do Azure e armazenamento do Azure, junto com as considerações de implantação de pilha do Azure."
services: azure-stack
documentationcenter: 
author: xiaofmao
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: xiaofmao
ms.openlocfilehash: 4d6fb44fd6fd2261059ca45093d8b49345adfa74
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2017
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Armazenamento do Azure de pilha: As diferenças e considerações

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Pilha de armazenamento do Azure é o conjunto de serviços de nuvem de armazenamento na pilha do Microsoft Azure. Pilha de armazenamento do Azure fornece o blob, tabela, fila e funcionalidade de gerenciamento de conta com a semântica consistente com o Azure.

Este artigo resume as diferenças de armazenamento do Azure pilha conhecidas do armazenamento do Azure. Ele também resume outras considerações para ter em mente quando você implanta a pilha do Azure. Para saber mais sobre as diferenças de alto nível entre a pilha do Azure e o Azure, consulte o [chave considerações](azure-stack-considerations.md) tópico.

## <a name="cheat-sheet-storage-differences"></a>Roteiro: diferenças de armazenamento

| Recurso | Azure (global) | Azure Stack |
| --- | --- | --- |
|Armazenamento de arquivos|Compartilhamentos de arquivos SMB baseado em nuvem com suporte|Ainda não tem suporte
|Dados em criptografia de rest|criptografia AES de 256 bits|Ainda não tem suporte
|Tipo de conta de armazenamento|Contas de armazenamento de Blob do Azure e de uso gerais|Para fins gerais somente
|Opções de replicação|Armazenamento com redundância local, armazenamento com redundância geográfica, armazenamento com redundância geográfica com acesso de leitura e armazenamento com redundância de zona|Armazenamento com redundância local
|Armazenamento Premium|Com suporte total|Pode ser provisionado, mas nenhum limite de desempenho ou a garantia de
|Discos gerenciados|Premium e standard com suporte|Ainda não tem suporte
|Nome de blob|1.024 caracteres (2.048 bytes)|880 caracteres (1,760 bytes)
|Tamanho máximo do blob de bloco|4,75 TB (100 MB X 50.000 blocos)|50.000 x 4 MB (aproximadamente 195 GB)
|Cópia do instantâneo de blob de página|Backup do Azure não gerenciado VM discos anexados a uma VM em execução com suporte|Ainda não tem suporte
|Cópia de instantâneo incremental do blob de página|Premium e blobs de página do Azure padrão com suporte|Ainda não tem suporte
|Tamanho máximo do blob de página|8 TB|1 TB
|Tamanho de página de blob de página|512 bytes|4 KB
|Tamanho de chave de linha e chave de partição de tabela|1.024 caracteres (2.048 bytes)|400 caracteres (800 bytes)

### <a name="metrics"></a>Métricas
Também há algumas diferenças com as métricas de armazenamento:
* Os dados de transações em métricas de armazenamento não diferenciam largura de banda de rede internos ou externos.
* Os dados de transações em métricas de armazenamento não tem acesso de máquina virtual para os discos montados.

## <a name="api-version"></a>Versão da API
As seguintes versões têm suporte com o armazenamento do Azure pilha:

* Serviços de dados de armazenamento do Azure: [versão 2015-04-05 da API REST](https://docs.microsoft.com/rest/api/storageservices/Version-2015-04-05?redirectedfrom=MSDN)
* Serviços de gerenciamento de armazenamento do Azure: [2015-05-01-preview, 2015-06-15 e 2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN) 

## <a name="next-steps"></a>Próximas etapas

* [Introdução às ferramentas de desenvolvimento de armazenamento de pilha do Azure](azure-stack-storage-dev.md)
* [Introdução ao armazenamento de pilha do Azure](azure-stack-storage-overview.md)

