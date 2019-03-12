---
title: Diferenças do armazenamento do Azure stack e considerações | Microsoft Docs
description: Entenda as diferenças entre o armazenamento do Azure stack e o armazenamento do Azure, juntamente com as considerações de implantação do Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: mabrigg
ms.reviwer: xiaofmao
ms.lastreviewed: 01/30/2019
ms.openlocfilehash: bbf5076c195fde6a7c5fcabd8e347b7a0d433e8f
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57763242"
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Armazenamento do Azure Stack: Diferenças e considerações

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

O armazenamento do Azure Stack é o conjunto de serviços de nuvem de armazenamento no Microsoft Azure Stack. O armazenamento do Azure Stack fornece blob, tabela, fila e funcionalidade de gerenciamento de conta com a semântica consistente do Azure.

Este artigo resume as diferenças do armazenamento do Azure Stack conhecidas dos serviços de armazenamento do Azure. Ela também lista as coisas a considerar ao implantar o Azure Stack. Para saber mais sobre as diferenças de alto nível entre global do Azure e o Azure Stack, consulte o [considerações da chave](azure-stack-considerations.md) artigo.

## <a name="cheat-sheet-storage-differences"></a>Folha de dados: Diferenças do armazenamento

| Recurso | (Global) do Azure | Azure Stack |
| --- | --- | --- |
|Armazenamento de arquivos|Compartilhamentos de arquivos SMB baseado em nuvem com suporte|Ainda não tem suporte
|Criptografia do serviço de armazenamento do Azure para dados em repouso|criptografia AES de 256 bits. Dá suporte à criptografia usando chaves gerenciadas pelo cliente no cofre de chaves.|Criptografia de AES de 128 bits do BitLocker. Não há suporte para criptografia usando chaves gerenciadas pelo cliente.
|Tipo de conta de armazenamento|Contas de armazenamento de BLOBs, V1 e V2 para fins gerais|Uso geral V1 apenas.
|Opções de replicação|Armazenamento com redundância local, armazenamento com redundância geográfica, armazenamento com redundância geográfica de acesso de leitura e armazenamento com redundância de zona|armazenamento com redundância local.
|Armazenamento Premium|Com suporte total|Pode ser provisionado, mas nenhum limite de desempenho ou garantia.
|Discos gerenciados|Premium e standard com suporte|Suporte quando você usa a versão 1808 ou posterior.
|Nome de blob|1.024 caracteres (2.048 bytes)|880 caracteres (1.760 bytes)
|Tamanho máximo do blob de blocos|4,75 TB (100 MB X 50.000 blocos)|4,75 TB (100 MB x 50.000 blocos) para a atualização 1802 ou a versão mais recente. 50.000 x 4 MB (aproximadamente 195 GB) para versões anteriores.
|Cópia de instantâneo de blob de página|Backup do Azure não gerenciado VM discos anexados a uma VM em execução com suporte|Ainda não tem suporte.
|Cópia de instantâneo incremental de blob de página|Premium e blobs de página padrão do Azure com suporte|Ainda não tem suporte.
|A cobrança de blob de página|Cobranças são incorridas para páginas exclusivas, estejam eles no blob ou no instantâneo. Não seria incorrer em encargos adicionais para instantâneos associados a um blob até que o blob de base que está sendo atualizado.|Cobranças são incorridas para instantâneos de blob e assiociated base. Poderia incorrer em encargos adicionais para cada instantâneo individual.
|Camadas de armazenamento para o armazenamento de BLOBs|Frequente, esporádica ou de camadas de armazenamento de arquivos.|Ainda não tem suporte.
|Exclusão reversível para o armazenamento de BLOBs|Geral disponível|Ainda não tem suporte.
|Tamanho máximo do blob de página|8 TB|1 TB
|Tamanho da página do blob de páginas|512 bytes|4 KB
|Tamanho de chave de linha e chave de partição de tabela|1.024 caracteres (2.048 bytes)|400 caracteres (800 bytes)
|Instantâneo de blob|O número máximo de instantâneos de um blob não é limitado.|O número máximo de instantâneos de um blob é 1.000.
|Autenticação do Azure AD para o armazenamento|Na visualização|Ainda não tem suporte.
|Blobs imutáveis|Geral disponível|Ainda não tem suporte.
|Regras de firewall e rede virtual para o armazenamento|Geral disponível|Ainda não tem suporte.|

Também há diferenças com métricas de armazenamento:

* Os dados de transação nas métricas de armazenamento não diferenciam largura de banda de rede interno ou externo.
* Os dados de transação nas métricas de armazenamento incluem o acesso de máquina virtual para os discos montados.

## <a name="api-version"></a>Versão da API

As seguintes versões têm suporte com o armazenamento do Azure Stack:

APIs de serviços de armazenamento do Azure:

1811 ou versões mais recentes de atualização:

- [09-11-2017](https://docs.microsoft.com/rest/api/storageservices/version-2017-11-09)
- [29-07-2017](https://docs.microsoft.com/rest/api/storageservices/version-2017-07-29)
- [17-04-2017](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
- [31-05-2016](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
- [11-12-2015](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
- [08-07-2015](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
- [05-04-2015](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Versões anteriores:

- [17-04-2017](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
- [31-05-2016](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
- [11-12-2015](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
- [08-07-2015](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
- [05-04-2015](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

APIs de gerenciamento de serviços de armazenamento do Azure:

1811 ou versões mais recentes de atualização:

- [2017-10-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2017-06-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-12-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-05-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

Versões anteriores:

- [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

Para obter mais informações sobre bibliotecas de cliente de armazenamento do Azure Stack com suporte, consulte: [Introdução às ferramentas de desenvolvimento de armazenamento do Azure Stack](azure-stack-storage-dev.md).

## <a name="next-steps"></a>Próximas etapas

* [Introdução às ferramentas de desenvolvimento do armazenamento do Azure Stack](azure-stack-storage-dev.md)
* [Usar ferramentas de transferência de dados para o armazenamento do Azure Stack](azure-stack-storage-transfer.md)
* [Introdução ao armazenamento do Azure Stack](azure-stack-storage-overview.md)
