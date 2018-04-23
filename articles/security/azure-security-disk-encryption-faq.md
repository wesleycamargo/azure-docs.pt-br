---
title: Perguntas Frequentes sobre o Azure Disk Encryption | Microsoft Docs
description: Este artigo fornece respostas a perguntas frequentes sobre o Microsoft Azure Disk Encryption para VMs IaaS Windows e Linux.
services: security
documentationcenter: na
author: DevTiw
manager: avibm
editor: barclayn
ms.assetid: 7188da52-5540-421d-bf45-d124dee74979
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/18/2018
ms.author: devtiw
ms.openlocfilehash: cb523b4fbf6e8abdf5c5158ab041d3485add9b23
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
# <a name="azure-disk-encryption-faq"></a>Perguntas frequentes sobre o Azure Disk Encryption

Este artigo fornece respostas a perguntas frequentes sobre o Azure Disk Encryption para VMs IaaS Windows e Linux. Para saber mais sobre esse serviço, confira [Azure Disk Encryption para VMs IaaS Windows e Linux](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Onde o Azure Disk Encryption está na GA (disponibilidade geral)?

O Azure Disk Encryption para VMs IaaS Windows e Linux está na disponibilidade geral em todas as regiões públicas do Azure.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Quais experiências de usuário estão disponíveis com o Azure Disk Encryption?

A GA do Azure Disk Encryption dá suporte a modelos do Azure Resource Manager, ao Azure PowerShell e à CLI do Azure. Isso proporciona muita flexibilidade. Você tem três opções diferentes para habilitar a criptografia de disco para as VMs IaaS. Para saber mais sobre a experiência do usuário e as orientações passo a passo disponíveis no Azure Disk Encryption, confira os cenários de implantação e experiências do [Azure Disk Encryption](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Quanto custa o Azure Disk Encryption?

Não são cobradas taxas para a criptografia de discos de VM com o Azure Disk Encryption.

## <a name="which-virtual-machine-tiers-does-azure-disk-encryption-support"></a>A quais camadas de máquina virtual o Azure Disk Encryption dá suporte?

O Azure Disk Encryption está disponível nas VMs da camada padrão, incluindo as VMs IaaS da série [A, D, DS, G, GS e F](https://azure.microsoft.com/pricing/details/virtual-machines/). Ele também está disponível para VMs com armazenamento premium. Ele não está disponível em VMs da camada básica.

## <a name="what-linux-distributions-does-azure-disk-encryption-support"></a>A quais distribuições Linux o Azure Disk Encryption dá suporte?

O Azure Disk Encryption tem suporte nas seguintes distribuições e versões do servidor Linux:

| Distribuição Linux | Versão | Tipo de volume suportado para criptografia|
| --- | --- |--- |
| Ubuntu | 16.04-LTS-DIÁRIO | SO e disco de dados |
| Ubuntu | 14.04.5-LTS-DIÁRIO | SO e disco de dados |
| RHEL | 7.4 | Disco de dados* |
| RHEL | 7.3 | Disco de dados* |
| RHEL | 7,2 | Disco de dados* |
| RHEL | 6,8 | Disco de dados* |
| RHEL | 6.7 | Disco de dados* |
| CentOS | 7.3 | SO e disco de dados |
| CentOS | 7.2n | SO e disco de dados |
| CentOS | 6,8 | SO e disco de dados |
| CentOS | 7.1 | Disco de dados |
| CentOS | 7.0 | Disco de dados |
| CentOS | 6.7 | Disco de dados |
| CentOS | 6.6 | Disco de dados |
| CentOS | 6.5 | Disco de dados |
| openSUSE | 13.2 | Disco de dados |
| SLES | 12 SP1 | Disco de dados |
| SLES | Prioridade: 12-SP1 | Disco de dados |
| SLES | HPC 12 | Disco de dados |
| SLES | Prioridade: 11-SP4 | Disco de dados |
| SLES | 11 SP4 | Disco de dados |

*__O ADE tem suporte pelo RHEL para disco de dados. A implementação atual do ADE funciona para o disco do SO, mas atualmente não tem suporte em conjunto. A Microsoft e Red Hat estão trabalhando em uma solução para suporte em conjunto. Entretanto, você pode fazer referência ao white paper ADE para criptografia de disco do SO Linux [aqui](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption).__

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Como posso começar a usar o Azure Disk Encryption?

Para começar, leia o white paper [Azure Disk Encryption para VMs IaaS](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) Windows e Linux.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Posso criptografar volumes de dados e de inicialização com o Azure Disk Encryption?

Sim, você pode criptografar volumes de inicialização e de dados para VMs IaaS Windows e Linux. Em VMs Windows, você não pode criptografar os dados sem primeiro criptografar o volume do SO. Em VMs Linux, você pode criptografar o volume de dados sem criptografar o volume do SO primeiro. Depois de criptografar o volume do SO para Linux, não há mais suporte para desabilitar a criptografia de um volume de SO para VMs IaaS Linux.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok-capability"></a>O Azure Disk Encryption permite o recurso BYOK (bring your own key)?

Sim, você pode fornecer suas próprias chaves de criptografia de chave. Essas chaves ficam protegidas no Azure Key Vault, que é o repositório de chaves do Azure Disk Encryption. Para obter mais detalhes sobre os cenários de suporte a chaves de criptografia de chave, confira as experiências e os cenários de implantação do [Azure Disk Encryption](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Posso usar uma chave de criptografia de chave criada pelo Azure?

Sim, você pode usar o Azure Key Vault para gerar uma chave de criptografia de chave para ser usada pela criptografia de disco do Azure. Essas chaves ficam protegidas no Azure Key Vault, que é o repositório de chaves do Azure Disk Encryption. Para obter mais detalhes sobre os cenários de suporte a chaves de criptografia de chave, confira as experiências e os cenários de implantação do [Azure Disk Encryption](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Posso usar um serviço de gerenciamento de chaves local ou HSM para proteger as chaves de criptografia?

Você não pode usar o serviço de gerenciamento de chaves local ou HSM para proteger as chaves de criptografia com o Azure Disk Encryption. Você só pode usar o serviço Azure Key Vault para proteger as chaves de criptografia. Para obter mais detalhes sobre os cenários de suporte a chaves de criptografia de chave, confira as experiências e os cenários de implantação do [Azure Disk Encryption](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Quais são os pré-requisitos para configurar o Azure Disk Encryption?

Há um script do PowerShell como pré-requisito. Com esse script, você pode criar um aplicativo do Azure Active Directory, criar um novo cofre de chaves ou configurar um cofre de chaves existente para acesso à criptografia de disco a fim de habilitar a criptografia e a proteção de segredos e chaves. Para saber mais sobre os cenários de suporte a chaves de criptografia de chave, confira os pré-requisitos e cenários e experiências de implantação do [Azure Disk Encryption](azure-security-disk-encryption.md#prerequisites).

## <a name="where-can-i-get-more-information-on-how-to-use-powershell-for-configuring-azure-disk-encryption"></a>Onde posso saber mais sobre como usar o PowerShell para configurar o Azure Disk Encryption?

Temos alguns ótimos artigos sobre como executar tarefas básicas do Azure Disk Encryption e cenários mais avançados. Para as tarefas básicas, confira [Explore Azure Disk Encryption with Azure PowerShell – Part 1](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/explore-azure-disk-encryption-with-azure-powershell/) (Explorar o Azure Disk Encryption com o Azure PowerShell – Parte 1). Para cenários mais avançados, confira [Explore Azure Disk Encryption with Azure PowerShell – Part 2](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2/) (Explorar o Azure Disk Encryption com o Azure PowerShell – Parte 2).

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>À qual versão do Azure PowerShell o Azure Disk Encryption dá suporte?

Use a versão mais recente do SDK do Azure PowerShell para configurar o Azure Disk Encryption. Baixe a última versão do [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). O Azure Disk Encryption *não* é suportado pela versão 1.1.0 do SDK do Azure.

> [!NOTE]
> A extensão de versão prévia do Azure Disk Encryption foi preterida. Para obter detalhes, confira [Deprecating Azure disk encryption preview extension for Linux IaaS VMs](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/) (Substituindo a versão prévia da extensão de criptografia para VMs IaaS Linux).

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Posso aplicar o Azure Disk Encryption à minha imagem personalizada do Linux?

Você não pode aplicar o Azure Disk Encryption à imagem personalizada do Linux. Há suporte apenas para imagens da galeria Linux nas distribuições suportadas indicadas anteriormente. No momento, não há suporte para imagens personalizadas do Linux.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Posso aplicar atualizações a uma VM Red Hat Linux que usa a atualização do yum?

Sim, você pode executar uma atualização ou aplicar patch em uma VM do Red Hat Linux. Para saber mais, confira [Applying updates to an encrypted Azure IaaS Red Hat VM by using the yum update](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/13/applying-updates-to-a-encrypted-azure-iaas-red-hat-vm-using-yum-update/) (Aplicando atualizações a uma VM Red Hat IaaS do Azure criptografada usando a atualização do yum).

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>O que é o fluxo de trabalho recomendado de criptografia de disco do Azure recomendado para Linux?

O seguinte fluxo de trabalho é recomendado para ter os melhores resultados no Linux:
* Inicie na imagem da galeria de estoque não modificada que corresponde à distribuição e à versão desejadas do sistema operacional
* Faça backup de todas as unidades montadas que serão criptografadas.  Isso permite a recuperação em caso de falha, por exemplo, se a VM for reiniciada antes de a criptografia ser concluída.
* Criptografar (pode levar várias horas ou mesmo dias dependendo das características da VM e do tamanho dos discos de dados anexados)
* Personalizar e adicionar o software à imagem conforme necessário.

Se esse fluxo de trabalho não for possível, depender de SSE [(Criptografia do Serviço de Armazenamento)](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) na camada da conta de armazenamento de plataforma poderá ser uma alternativa para criptografia de disco cheio usando dm-crypt.

## <a name="what-is-the-disk-bek-volume-or-mntazurebekdisk"></a>O que é o disco "Volume Bek" ou "/mnt/azure_bek_disk"?

"Volume Bek" para Windows ou "/mnt/azure_bek_disk" para Linux é um volume de dados local que armazena com segurança as chaves de criptografia para VMs de IaaS do Azure Criptografadas.
> [!NOTE]
> Não exclua ou edite nenhum conteúdo neste disco. Não desmonte o disco, uma vez que a presença da chave de criptografia é necessária para operações de criptografia na VM IaaS.

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Onde posso fazer perguntas ou fornecer comentários?

Você pode fazer perguntas ou fornecer comentários no [Fórum do Azure Disk Encryption](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu mais sobre as perguntas mais frequentes relativas ao Azure Disk Encryption. Para saber mais sobre esse serviço e seus recursos, confira os seguintes artigos:

- [Aplicar a criptografia de disco na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Criptografar uma máquina virtual do Azure](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Criptografia de dados em repouso do Azure](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)