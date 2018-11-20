---
title: Perguntas Frequentes sobre o Azure Disk Encryption | Microsoft Docs
description: Este artigo fornece respostas a perguntas frequentes sobre o Microsoft Azure Disk Encryption para VMs IaaS Windows e Linux.
author: mestew
ms.service: security
ms.subservice: Azure Disk Encryption
ms.topic: article
ms.author: mstewart
ms.date: 10/16/2018
ms.openlocfilehash: 8da966154f1da8cb1be090f70d80e4484e9bce0d
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377284"
---
# <a name="azure-disk-encryption-faq"></a>Perguntas frequentes sobre o Azure Disk Encryption

Este artigo fornece respostas a perguntas frequentes sobre o Azure Disk Encryption para VMs IaaS Windows e Linux. Para saber mais sobre esse serviço, confira [Azure Disk Encryption para VMs IaaS Windows e Linux](azure-security-disk-encryption-overview.md).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Onde o Azure Disk Encryption está na GA (disponibilidade geral)?

O Azure Disk Encryption para VMs IaaS Windows e Linux está na disponibilidade geral em todas as regiões públicas do Azure.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Quais experiências de usuário estão disponíveis com o Azure Disk Encryption?

A GA do Azure Disk Encryption dá suporte a modelos do Azure Resource Manager, ao Azure PowerShell e à CLI do Azure. As diferentes experiências de usuário oferecem flexibilidade. Você tem três opções diferentes para habilitar a criptografia de disco para as VMs IaaS. Para obter mais informações sobre a experiência de usuário e a orientação passo a passo disponível no Azure Disk Encryption, consulte [habilitar o Azure Disk Encryption para Windows](azure-security-disk-encryption-windows.md) e [habilitar o Azure Disk Encryption para Linux](azure-security-disk-encryption-linux.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Quanto custa o Azure Disk Encryption?

Não há nenhum encargo para criptografar discos de VM com o Azure Disk Encryption, mas há encargos associados ao uso do Azure Key Vault. Para obter mais informações sobre os custos do Cofre de Chaves do Azure, consulte a página de preços [Cofre de chaves](https://azure.microsoft.com/pricing/details/key-vault/).


## <a name="which-virtual-machine-tiers-does-azure-disk-encryption-support"></a>A quais camadas de máquina virtual o Azure Disk Encryption dá suporte?

O Azure Disk Encryption está disponível nas VMs da camada padrão, incluindo as VMs IaaS da série [A, D, DS, G, GS e F](https://azure.microsoft.com/pricing/details/virtual-machines/). Ele também está disponível para VMs com armazenamento premium. Ele não está disponível em VMs da camada básica.

## <a name="bkmk_LinuxOSSupport"></a> Azure Disk Encryption dá suporte a quais distribuições do Linux?

O Azure Disk Encryption tem suporte nas seguintes distribuições e versões do servidor Linux:

| Distribuição Linux | Versão | Tipo de volume suportado para criptografia|
| --- | --- |--- |
| Ubuntu | 16.04-LTS-DIÁRIO | SO e disco de dados |
| Ubuntu | 14.04.5-LTS-DIÁRIO | SO e disco de dados |
| RHEL | 7.5 | Disco de dados* |
| RHEL | 7.4 | Disco de dados* |
| RHEL | 7.3 | Disco de dados* |
| RHEL | 7,2 | Disco de dados* |
| RHEL | 6,8 | Disco de dados* |
| RHEL | 6.7 | Disco de dados* |
| CentOS | 7.4 | SO e disco de dados |
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


*__O ADE tem suporte pelo RHEL para disco de dados. A implementação atual do ADE funciona para o disco do SO, mas atualmente não tem suporte em conjunto. A Microsoft e Red Hat estão trabalhando em uma solução para suporte em conjunto. Enquanto isso, você pode fazer referência ao [artigo](azure-security-disk-encryption-linux.md) Azure Disk Encryption para Linux__

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Como posso começar a usar o Azure Disk Encryption?

Para começar, leia as [visão geral do Azure Disk Encryption](azure-security-disk-encryption-overview.md).

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Posso criptografar volumes de dados e de inicialização com o Azure Disk Encryption?

Sim, você pode criptografar volumes de inicialização e de dados para VMs IaaS Windows e Linux. Em VMs Windows, não é possível criptografar os dados sem primeiro criptografar o volume do SO. Para VMs Linux, é possível criptografar o volume de dados sem precisar criptografar o volume do sistema operacional primeiro. Depois de criptografar o volume do SO para Linux, não há suporte para desabilitar a criptografia em um volume do SO para VMs do Linux IaaS.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>O Azure Disk Encryption permite o recurso BYOK (Bring Your Own Key)?

Sim, você pode fornecer suas próprias chaves de criptografia de chave. Essas chaves ficam protegidas no Azure Key Vault, que é o repositório de chaves do Azure Disk Encryption. Para obter mais informações sobre os principais cenários de suporte a chaves de criptografia, consulte [Pré-requisitos do Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Posso usar uma chave de criptografia de chave criada pelo Azure?

Sim, você pode usar o Azure Key Vault para gerar uma chave de criptografia de chave para ser usada pela criptografia de disco do Azure. Essas chaves ficam protegidas no Azure Key Vault, que é o repositório de chaves do Azure Disk Encryption. Para obter mais informações sobre a chave de criptografia, consulte [Pré-requisitos do Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Posso usar um serviço de gerenciamento de chaves local ou HSM para proteger as chaves de criptografia?

Você não pode usar o serviço de gerenciamento de chaves local ou HSM para proteger as chaves de criptografia com o Azure Disk Encryption. Você só pode usar o serviço Azure Key Vault para proteger as chaves de criptografia. Para obter mais informações sobre os cenários de suporte a chaves de criptografia de chave, consulte [pré-requisitos do Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Quais são os pré-requisitos para configurar o Azure Disk Encryption?

Há pré-requisitos para o Azure Disk Encryption. Consulte o artigo [Pré-requisitos do Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) para criar um novo cofre de chaves ou configurar um cofre de chaves existente para acesso da criptografia de disco para habilitar a criptografia e proteger segredos e chaves. Para obter mais informações sobre os principais cenários de suporte a chave de criptografia, consulte [Visão geral do Azure Disk Encryption](azure-security-disk-encryption-overview.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Quais são os pré-requisitos para configurar o Azure Disk Encryption com um aplicativo do Azure AD (versão anterior)?

Há pré-requisitos para o Azure Disk Encryption. Consulte o [artigo Pré-requisitos de criptografia de disco do Azure](azure-security-disk-encryption-prerequisites-aad.md) para criar um aplicativo do Azure Active Directory, criar um novo cofre de chaves ou configurar um cofre de chaves existente para criptografia de disco para ativar a criptografia e proteger segredos e chaves. Para obter mais informações sobre os principais cenários de suporte a chave de criptografia, consulte [Visão geral do Azure Disk Encryption](azure-security-disk-encryption-overview.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Ainda há suporte para o Azure Disk Encryption usando um aplicativo do Azure AD (versão anterior)?
Sim. Ainda há suporte para criptografia de disco usando um aplicativo do Azure AD. No entanto, ao criptografar novas VMs, é recomendável que você use o novo método em vez de criptografar com um aplicativo do Azure AD. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Posso migrar VMs que foram criptografadas com um aplicativo do Azure AD para a criptografia sem um aplicativo do Azure AD?
  Atualmente, não há um caminho de migração direto para computadores que foram criptografados com um aplicativo do Azure AD para criptografia sem um aplicativo do Azure AD. Além disso, também não há um caminho direto da criptografia sem um aplicativo do Azure AD para a criptografia com o aplicativo do AD. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>À qual versão do Azure PowerShell o Azure Disk Encryption dá suporte?

Use a versão mais recente do SDK do Azure PowerShell para configurar o Azure Disk Encryption. Baixe a última versão do [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). O Azure Disk Encryption *não* é suportado pela versão 1.1.0 do SDK do Azure.

> [!NOTE]
> A extensão de versão prévia do Azure Disk Encryption foi preterida. Para obter detalhes, confira [Deprecating Azure disk encryption preview extension for Linux IaaS VMs](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/) (Substituindo a versão prévia da extensão de criptografia para VMs IaaS Linux).

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Posso aplicar o Azure Disk Encryption à minha imagem personalizada do Linux?

Você não pode aplicar a criptografia de disco do Azure à sua imagem personalizada do Linux. Somente as imagens da galeria Linux para as distribuições suportadas chamadas anteriormente são suportadas. Imagens personalizadas do Linux não são suportadas atualmente.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Posso aplicar atualizações a uma VM Red Hat Linux que usa a atualização do yum?

Sim, você pode executar uma atualização ou aplicar patch em uma VM do Red Hat Linux. Para saber mais, confira [Applying updates to an encrypted Azure IaaS Red Hat VM by using the yum update](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/13/applying-updates-to-a-encrypted-azure-iaas-red-hat-vm-using-yum-update/) (Aplicando atualizações a uma VM Red Hat IaaS do Azure criptografada usando a atualização do yum).

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>O que é o fluxo de trabalho recomendado de criptografia de disco do Azure recomendado para Linux?

O seguinte fluxo de trabalho é recomendado para ter os melhores resultados no Linux:
* Inicie na imagem da galeria de estoque não modificada que corresponde à distribuição e à versão do sistema operacional necessárias
* Faça backup de todas as unidades montadas que serão criptografadas.  Esse backup permitirá a recuperação se houver uma falha, por exemplo, se a VM for reinicializada antes da conclusão da criptografia.
* Criptografar (pode levar várias horas ou mesmo dias dependendo das características da VM e do tamanho dos discos de dados anexados)
* Personalizar e adicionar o software à imagem conforme necessário.

Se esse fluxo de trabalho não for possível, usar a SSE [(Criptografia do Serviço de Armazenamento)](../storage/common/storage-service-encryption.md) na camada da conta de armazenamento de plataforma poderá ser uma alternativa para a criptografia de disco completo usando dm-crypt.

## <a name="what-is-the-disk-bek-volume-or-mntazurebekdisk"></a>O que é o disco "Volume Bek" ou "/mnt/azure_bek_disk"?

"Volume de Bek" para Windows ou "/mnt/azure_bek_disk" para Linux é um volume de dados local que armazena com segurança as chaves de criptografia para VMs de IaaS do Azure criptografadas.
> [!NOTE]
> Não exclua ou edite nenhum conteúdo neste disco. Não desmonte o disco, uma vez que a presença da chave de criptografia é necessária para operações de criptografia na VM IaaS.


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Qual método de criptografia é usado pela criptografia de disco do Azure?

No Windows, o ADE usa o método de criptografia AES256 do Bitlocker (AES256WithDiffuser em versões anteriores ao Windows Server 2012). No Linux, o ADE usa o padrão dmcrypt de aes-xts-plain64 com uma chave mestra de volume de 256 bits.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Se eu usar EncryptFormatAll e especificar todos os tipos de volume, ele apagará os dados em unidades de dados que já tivermos criptografado?
Não, os dados não serão apagados de unidades de dados que já tiverem sido criptografadas usando o Azure Disk Encryption. Semelhante ao modo como o EncryptFormatAll não criptografou novamente a unidade do sistema operacional, ele não criptografará novamente a unidade de dados já criptografada. Para obter mais informações, veja os [critérios do EncryptFormatAll](azure-security-disk-encryption-linux.md#bkmk_EFACriteria).        

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Onde posso fazer perguntas ou fornecer comentários?

Você pode fazer perguntas ou fornecer comentários no [Fórum do Azure Disk Encryption](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu mais sobre as perguntas mais frequentes relativas ao Azure Disk Encryption. Para obter mais informações sobre esse serviço, veja os seguintes artigos:

- [Visão geral do Azure Disk Encryption](azure-security-disk-encryption-overview.md)
- [Aplicar a criptografia de disco na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Criptografia de dados em repouso do Azure](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
