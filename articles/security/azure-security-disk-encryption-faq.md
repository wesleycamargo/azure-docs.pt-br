---
title: Perguntas Frequentes sobre o Azure Disk Encryption | Microsoft Docs
description: Este artigo fornece respostas a perguntas frequentes sobre o Microsoft Azure Disk Encryption para VMs IaaS Windows e Linux.
services: security
documentationcenter: na
author: deventiwari
manager: avibm
editor: yuridio
ms.assetid: 7188da52-5540-421d-bf45-d124dee74979
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2017
ms.author: devtiw
ms.translationtype: HT
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: 0d15bf42c156ea7a72c54d690f4016877913efe4
ms.contentlocale: pt-br
ms.lasthandoff: 08/12/2017

---
# <a name="azure-disk-encryption-frequently-asked-questions-faq"></a>FAQ (perguntas frequentes) sobre o Azure Disk Encryption

Estas Perguntas Frequentes respondem a perguntas sobre o Azure Disk Encryption para VMs IaaS Windows e Linux. Para saber mais sobre esse serviço, leia [Azure Disk Encryption para VMs IaaS Windows e Linux](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

## <a name="general-questions"></a>Perguntas gerais
**P.** Qual é a região do Azure Disk Encryption em GA?

**R:** O Azure Disk Encryption para VMs IaaS Windows e Linux está disponível em GA em todas as regiões públicas do Azure.

**P:** Quais experiências de usuário estão disponíveis com o Azure Disk Encryption?

**R:** O GA do Azure Disk Encryption dá suporte a modelos do Azure Resource Manager, ao Azure PowerShell e à CLI do Azure. Isso dá uma grande flexibilidade, pois você tem três opções diferentes para habilitar a criptografia de disco para as VMs IaaS. Mais detalhes sobre a experiência do usuário e instruções passo a passo estão disponíveis nos cenários de implantação e experiências do Azure Disk Encryption.

**P:** Quanto custa o Azure Disk Encryption?

**R:** Não são cobradas taxas para a criptografia de discos de VM com o Azure Disk Encryption.

**P:** Em quais camadas de máquina virtual é possível usar o Azure Disk Encryption?

**R:** O Azure Disk Encryption está disponível somente em máquinas virtuais de camada padrão, incluindo [A, D, DS, G, GS, F](https://azure.microsoft.com/pricing/details/virtual-machines/) e assim por diante. VMs série IaaS, incluindo VMs com o armazenamento premium. Ele não está disponível para máquinas virtuais na Camada básica.

**P:** Quais distribuições do Linux têm suporte do Azure Disk Encryption?

**R:** O Azure Disk Encryption tem suporte nas seguintes distribuições e versões do servidor Linux:

| Distribuição Linux | Versão | Tipo de Volume com Suporte para Criptografia|
| --- | --- |--- |
| Ubuntu | 16.04-LTS-DIÁRIO | SO e Disco de Dados |
| Ubuntu | 14.04.5-LTS-DIÁRIO | SO e Disco de Dados |
| RHEL | 7.3 | SO e Disco de Dados |
| RHEL | 7,2 | SO e Disco de Dados |
| RHEL | 6,8 | SO e Disco de Dados |
| RHEL | 6.7 | Disco de dados |
| CentOS | 7.3 | SO e Disco de Dados |
| CentOS | 7.2n | SO e Disco de Dados |
| CentOS | 6,8 | SO e Disco de Dados |
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

**P:** Como posso começar a usar o Azure Disk Encryption?

**R:** Os clientes podem aprender a usá-lo lendo o white paper do Azure Disk Encryption localizado [aqui](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)

**P:** Posso criptografar volumes de dados e de inicialização com o Azure Disk Encryption?

**R:** Sim, você pode criptografar volumes de inicialização e de dados para VMs IaaS Windows e Linux. No caso de VMs Windows, você não pode criptografar os dados sem primeiro criptografar o volume do sistema operacional. No caso de VMs Linux, você pode criptografar o volume de dados sem criptografar o volume do sistema operacional primeiro. Depois de criptografar o volume do sistema operacional para Linux, não há mais suporte para a criptografia de um volume de sistema operacional para VMs IaaS Linux

**P:** O Azure Disk Encryption permite o recurso BYOK (“bring your own key”)?

**R:** Sim, você pode fornecer suas próprias chaves de criptografia de chave. Essas chaves ficam protegidas no Azure Key Vault, que é o repositório de chaves do Azure Disk Encryption. Para obter mais detalhes sobre os principais cenários de suporte a chaves de criptografia, confira as experiências e os cenários de implantação do Azure Disk Encryption

**P:** Posso usar uma chave de criptografia de chave criada pelo Azure?

**R:** Sim, você pode usar o Azure Key Vault para gerar a chave de criptografia de chave para ser usada pela criptografia de disco do Azure. Essas chaves ficam protegidas no Azure Key Vault, que é o repositório de chaves do Azure Disk Encryption. Para obter mais detalhes sobre os principais cenários de suporte a chaves de criptografia, confira as experiências e os cenários de implantação do Azure Disk Encryption

**P:** Posso usar o serviço de gerenciamento de chaves local/HSM para proteger as chaves de criptografia?

**R:** Você não pode usar o serviço de gerenciamento de chaves local/HSM para proteger as chaves de criptografia com o Azure Disk Encryption. Você só pode usar o serviço Azure Key Vault para proteger as chaves de criptografia. Para obter mais detalhes sobre os principais cenários de suporte a chaves de criptografia, confira as experiências e os cenários de implantação do Azure Disk Encryption

**P:** Quais são os pré-requisitos para configurar a criptografia de dados do Azure?

**R:** O Azure Disk Encryption exige o script do PowerShell para criar o aplicativo AAD, criar um novo cofre de chaves ou instalar um cofre de chaves existente para acesso à criptografia de disco a fim de habilitar a criptografia e proteger os segredos e as chaves.  Para obter mais detalhes sobre os cenários de suporte a chaves de criptografia de chave, confira os pré-requisitos do Azure Disk Encryption e cenários e experiências de implantação

**P:** Onde posso saber mais sobre como usar o PowerShell para configurar o Azure Disk Encryption?

**R:** Temos alguns ótimos artigos sobre como executar tarefas básicas do Azure Disk Encryption e cenários mais avançados. Para as tarefas básicas, confira Explorar o [Azure Disk Encryption com o Azure PowerShell - Parte 1](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/explore-azure-disk-encryption-with-azure-powershell/). Para cenários mais avançados, confira Explorar [Azure Disk Encryption com o Azure PowerShell - Parte 2](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2/)

**P:** Qual versão do Azure PowerShell tem suporte do Azure Disk Encryption?

**R:** Use a versão mais recente do SDK do Azure PowerShell para configurar o Azure Disk Encryption. Baixe a última versão do [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). NÃO há suporte para o Azure Disk Encryption pelo SDK do Azure versão 1.1.0.

> [!NOTE]
> A extensão de versão prévia do Azure Disk Encryption foi preterida. Para obter detalhes, consulte a documentação [aqui](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/)

**P:** Posso aplicar a criptografia de disco do Azure à minha imagem personalizada do Linux?

**R:** Você não pode aplicar a criptografia de disco do Azure à imagem personalizada do Linux. Há suporte apenas para imagens da galeria Linux nas distribuições com suporte indicadas acima. Não há suporte para imagens personalizadas do Linux no momento

**P:** Posso aplicar atualizações a uma VM Red Hat Linux usando a atualização do Yum?

**R:** Sim, você pode executar a atualização e/ou usar patch em uma VM Red Hat Linnux seguindo as orientações documentadas [aqui](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/13/applying-updates-to-a-encrypted-azure-iaas-red-hat-vm-using-yum-update/)

**P:** Onde posso ir para fazer uma pergunta ou comentários

**R:** você pode fornecer faça perguntas ou comentários sobre o fórum de criptografia de disco do Azure [aqui](https://social.msdn.microsoft.com/Forums/home?forum=AzureDiskEncryption)

## <a name="see-also"></a>Consulte também
Neste documento, você aprendeu mais sobre as perguntas mais frequentes relativas ao Azure Disk Encryption. Para saber mais sobre esse serviço e seus recursos, leia:

- [Aplicar a criptografia de disco na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Criptografar uma Máquina Virtual do Azure](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Criptografia de dados em repouso no Azure](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)

