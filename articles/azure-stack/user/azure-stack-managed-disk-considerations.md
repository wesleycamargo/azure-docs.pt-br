---
title: Diferenças e considerações para o Managed Disks no Azure Stack | Microsoft Docs
description: Saiba mais sobre as diferenças e considerações ao trabalhar com o Managed Disks no Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: sethm
ms.reviewer: jiahan
ms.openlocfilehash: 4bad339ca704f14f57c120db5e731d0dbfeb8d59
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53793421"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Discos gerenciados do Azure Stack: Diferenças e considerações
Este artigo resume as diferenças conhecidas entre discos gerenciados do Azure Stack e Managed Disks do Azure. Para saber mais sobre as diferenças de alto nível entre o Azure Stack e o Azure, consulte a [considerações da chave](azure-stack-considerations.md) artigo.

Managed Disks simplifica o gerenciamento de disco para VMs IaaS ao gerenciar o [contas de armazenamento](/azure/azure-stack/azure-stack-manage-storage-accounts) associados com os discos de VM.

> [!Note]  
> Discos gerenciados no Azure Stack está disponível no 1808.
  

## <a name="cheat-sheet-managed-disk-differences"></a>Folha de dados: Diferenças do disco de gerenciado

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
|Tamanho do disco  |Disco Premium do Azure: P4 (32 GiB) para P80 (32 TiB)<br>Disco SSD Standard do Azure: E10 (128 GiB) para E80 (32 TiB)<br>Disco HDD Standard do Azure: S4 (32 GiB) para S80 (32 TiB) |M4: 32 GiB<br>M6: 64 GiB<br>M10: 128 GiB<br>M15: 256 GiB<br>M20: 512 GiB<br>M30: 1024 GiB |
|Cópia de instantâneo de discos|Instantâneo do Azure discos gerenciados anexados a uma VM em execução com suporte|Ainda não tem suporte |
|Análise de desempenho de discos |Métricas de agregação e métricas por disco com suporte |Ainda não tem suporte |
|Migração      |Forneça a ferramenta para migrar de VMs de Gerenciador de recursos do Azure não gerenciado existentes sem a necessidade de recriar a VM  |Ainda não tem suporte |

> [!Note]  
> Gerenciado discos IOPs e taxa de transferência no Azure Stack é um número de limite, em vez de um número provisionado, o que pode ser afetado pelo hardware e cargas de trabalho em execução no Azure Stack.


## <a name="metrics"></a>Métricas
Também há diferenças com métricas de armazenamento:
- Com o Azure Stack, os dados de transação nas métricas de armazenamento não diferenciam largura de banda de rede interno ou externo.
- Dados de transação de pilha do Azure nas métricas de armazenamento não incluem o acesso de máquina virtual para os discos montados.


## <a name="api-versions"></a>Versões de API
Pilha de Managed Disks do Azure suporta as seguintes versões de API:
- 2017-03-30

## <a name="known-issues"></a>Problemas conhecidos
Depois de aplicar o 1809 de atualização, você pode encontrar os seguintes problemas ao implantar VMs com discos gerenciados:

   - Se a assinatura foi criada antes da atualização 1808, implantando uma VM com discos gerenciados pode falhar com uma mensagem de erro interno. Para resolver o erro, siga estas etapas para cada assinatura:
      1. No portal do locatário, vá para **assinaturas** e localize a assinatura. Clique em **provedores de recursos**, em seguida, clique em **Microsoft. Compute**e, em seguida, clique em **registrar novamente**.
      2. Sob a mesma assinatura, vá para **controle de acesso (IAM)**, verifique se **do Azure Stack – Managed Disk** está listado.
   - Se você tiver configurado um ambiente multilocatário, implantar as VMs em uma assinatura associada a um diretório de convidado pode falhar com uma mensagem de erro interno. Para resolver o erro, siga estas etapas no [deste artigo](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) para reconfigurar a cada um dos seus diretórios de convidado.


## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre máquinas virtuais do Azure Stack](azure-stack-compute-overview.md)
