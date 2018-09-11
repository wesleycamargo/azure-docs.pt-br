---
title: Diferenças e considerações para o Managed Disks no Azure Stack | Microsoft Docs
description: Saiba mais sobre as diferenças e considerações ao trabalhar com o Managed Disks no Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: brenduns
ms.reviewer: jiahan
ms.openlocfilehash: 26f8880d01da00780317ee2a6f66ee5007576a50
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44347657"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Managed Disks do Azure Stack: Diferenças e considerações
Este artigo resume as diferenças conhecidas entre discos gerenciados do Azure Stack e Managed Disks do Azure. Para saber mais sobre as diferenças de alto nível entre o Azure Stack e o Azure, consulte a [considerações da chave](azure-stack-considerations.md) artigo.

Managed Disks simplifica o gerenciamento de disco para VMs IaaS ao gerenciar o [contas de armazenamento](/azure/azure-stack/azure-stack-manage-storage-accounts) associados com os discos de VM.
  

## <a name="cheat-sheet-managed-disk-differences"></a>Folha de dados: gerenciado diferenças de disco

| Recurso | (Global) do Azure | Azure Stack |
| --- | --- | --- |
|Criptografia para dados em repouso |Azure Storage Service Encryption (SSE), Azure Disk Encryption (ADE)     |Criptografia de AES de 128 bits do BitLocker      |
|Imagem          | Suporte a imagem personalizada gerenciada |Ainda não tem suporte|
|Opções de backup |Dão suporte ao serviço de Backup do Azure |Ainda não tem suporte |
|Opções de recuperação de desastre |Suporte do Azure Site Recovery |Ainda não tem suporte|
|Tipos de disco     |Padrão HDD, SSD Standard (visualização) e SSD Premium |Premium SSD, HDD padrão |
|Discos Premium  |Com suporte total |Pode ser provisionado, mas nenhum limite de desempenho ou a garantia de  |
|IOPs de discos Premium  |Depende do tamanho do disco  |2300 IOPs por disco |
|Taxa de transferência de discos Premium |Depende do tamanho do disco |145 MB/segundo por disco |
|Tamanho máximo do disco  |4 TB       |1 TB       |
|Análise de desempenho de discos |Métricas de agregação e métricas por disco com suporte |Ainda não tem suporte |
|Migração      |Forneça a ferramenta para migrar de VMs de Gerenciador de recursos do Azure não gerenciado existentes sem a necessidade de recriar a VM  |Ainda não tem suporte |


## <a name="metrics"></a>Métricas
Também há diferenças com métricas de armazenamento:
- Com o Azure Stack, os dados de transação nas métricas de armazenamento não diferenciam largura de banda de rede interno ou externo.
- Dados de transação de pilha do Azure nas métricas de armazenamento não incluem o acesso de máquina virtual para os discos montados.


## <a name="api-versions"></a>Versões de API
Pilha de Managed Disks do Azure suporta as seguintes versões de API:
- 2017-03-30


## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre máquinas virtuais do Azure Stack](azure-stack-compute-overview.md)
