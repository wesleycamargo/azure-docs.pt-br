---
title: Visão geral do Azure Disk Encryption para VMs IaaS | Microsoft Docs
description: Este artigo fornece uma visão geral do Microsoft Azure Disk Encryption para VMs IaaS.
services: security
documentationcenter: na
author: mestew
manager: MBaldwin
ms.assetid: 30324adb-e24c-433a-9214-2d1465cbdf45
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2018
ms.author: mstewart
ms.openlocfilehash: 88500be4bae83049e8a7060719f4f85e7622c645
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42886984"
---
# <a name="azure-disk-encryption-for-iaas-vms"></a>Azure Disk Encryption para VMs IaaS 
O Microsoft Azure tem o compromisso de garantir a privacidade e a soberania dos seus dados e permitir que você controle os dados hospedados no Azure usando uma variedade de tecnologias avançadas para criptografar, controlar e gerenciar chaves de criptografia, bem como auditar e controlar o acesso aos dados. Esse controle permite que os clientes do Azure tenham a flexibilidade de escolher a solução que melhor atenda às necessidades de negócios. Este apresenta a você uma nova solução de tecnologia, "Azure Disk Encryption para VMs IaaS do Windows e Linux" para ajudá-lo a proteger e preservar seus dados e atender às obrigações de conformidade e segurança da organização. 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]


## <a name="overview"></a>Visão geral
ADE (Azure Disk Encryption) é um recurso que ajuda você a criptografar os discos de máquina virtual IaaS do Windows e Linux. O ADE aproveita a funcionalidade [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) padrão do setor do Windows e a funcionalidade [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer criptografia de volume para o SO e discos de dados. A solução é integrada ao [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) para ajudar a controlar e gerenciar os segredos e as chaves de criptografia de disco. A solução também garante que todos os dados em discos da máquina virtual sejam criptografados em repouso no armazenamento do Azure.

O Azure Disk Encryption para VMs IaaS do Windows e Linux agora está em **Disponibilidade Geral** em todas as regiões públicas do Azure e regiões do AzureGov para VMs Padrão e VMs com armazenamento premium. Ao aplicar a solução de gerenciamento de Azure Disk Encryption, você pode atender às seguintes necessidades de negócios:

* As VMs IaaS ficam protegidas em repouso usando a tecnologia de criptografia padrão da indústria para tratar da segurança organizacional e dos requisitos de conformidade.
* As VMs IaaS são inicializadas por meio de políticas e chaves controladas pelo cliente, e você pode auditar seu uso no cofre de chaves.


Se você usar a Central de Segurança do Azure, ela alertará se houver máquinas virtuais que não estejam criptografadas. Esses alertas serão mostrados como Alta Severidade e a recomendação é criptografar essas máquinas virtuais.
![Alerta de criptografia de disco na Central de Segurança do Azure](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> Determinadas recomendações podem aumentar o uso de recursos de dados, rede ou computação, resultando em custos adicionais de licença ou inscrição.


## <a name="encryption-scenarios"></a>Cenários de criptografia
A solução Azure Disk Encryption dá suporte aos seguintes cenários do cliente:

* Habilitar a criptografia em novas VMs IaaS criadas a partir de VHD pré-criptografado e chaves de criptografia 
* Habilitar criptografia em novas VMs de IaaS criadas a partir das imagens da Galeria do Azure com suporte
* Habilitar a criptografia em VMs IaaS existentes em execução no Azure
* Habilitar a criptografia em conjuntos de dimensionamento de máquinas virtuais do Windows
* Habilitar a criptografia em unidades de dados para conjuntos de dimensionamento de máquinas virtuais do Linux
* Desabilitar a criptografia em VMs IaaS do Windows
* Desabilitar a criptografia em unidades de dados para VMs IaaS do Linux
* Desabilitar a criptografia em conjuntos de dimensionamento de máquinas virtuais do Windows
* Desabilitar a criptografia em unidades de dados para conjuntos de dimensionamento de máquinas virtuais do Linux
* Ativar criptografia de VMs de disco gerenciado
* Atualizar configurações de criptografia de uma VM de armazenamento criptografada premium e não premium existente
* Fazer backup e restauração de VMs criptografadas

A solução dá suporte aos seguintes cenários para VMs IaaS quando habilitadas no Microsoft Azure:

* Integração com o Cofre da Chave do Azure
* VMs de camada padrão: [ A, D, DS, G, GS, F e VMs IaaS ](https://azure.microsoft.com/pricing/details/virtual-machines/)
    * As [VMs do Linux](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport), dentro dessas camadas, devem atender ao requisito mínimo de memória de 7 GB
* Habilitar a criptografia em VMs de IaaS do Windows e Linux, VMs de disco gerenciado e VMs do conjunto de dimensionamento por meio das imagens da Galeria do Azure com suporte
* Desabilitar a criptografia no SO e nas unidades de dados para VMs IaaS do Windows, VMs de conjunto de dimensionamento e VMs de disco gerenciado
* Desabilitar a criptografia em unidades de dados para VMs IaaS do Linux, VMs de conjunto de dimensionamento e VMs de disco gerenciado
* Habilitar a criptografia em VMs IaaS executando o sistema operacional Windows Client
* Habilitar a criptografia em volumes com caminhos de montagem
* Habilitar criptografia em VMs do Linux configuradas com disk striping (RAID) usando mdadm
* Habilitar criptografia no Linux VMs utilizando LVM para discos de dados
* Habilitar criptografia no SO e nos discos de dados de VM do Linux 
* Habilitar a criptografia em VMs do Windows configuradas com os Espaços de Armazenamento
* Atualizar configurações de criptografia de uma VM de armazenamento criptografada premium e não premium existente
* Fazer backup e restauração de VMs criptografada para cenários KEK e não KEK (KEK – Chave de Criptografia de Chaves)
* Para todas as regiões do Público do Azure e AzureGov há suporte

A solução não dá suporte aos seguintes cenários, recursos e tecnologia:

* VMs IaaS da camada Básica
* Como desabilitar a criptografia em unidades do sistema operacional para VMs IaaS do Linux
* Desabilitar criptografia em uma unidade de dados se a unidade do SO estiver criptografada para VMs de IaaS do Linux
* VMs de IaaS que são criadas usando o método de criação de VM clássico
* Habilitar a criptografia em imagens personalizadas do cliente de VMs IaaS do Linux
* Integração com o Serviço de Gerenciamento de Chaves no local
* Arquivos do Azure (sistema de arquivos compartilhados), NFS (Network File System), volumes dinâmicos e VMs do Windows configuradas com Sistemas RAID baseados em software

## <a name="encryption-features"></a>Recursos de criptografia
Quando você habilita e a implanta o Azure Disk Encryption para VMs IaaS do Azure, os seguintes recursos são habilitados, dependendo da configuração fornecida:

* Criptografia do volume do sistema operacional para proteger o volume de inicialização em repouso no armazenamento
* Criptografia de volumes de dados para proteger os volumes de dados em repouso no armazenamento
* Como desabilitar a criptografia em unidades do sistema operacional e de dados para VMs IaaS do Windows
* Desabilitar a criptografia nas unidades de dados para VMs de IaaS do Linux (somente se a unidade do SO não estiver criptografada)
* Proteger as chaves de criptografia e segredos em sua assinatura de cofre de chaves
* Comunicando o status de criptografia da VM IaaS criptografada
* Remoção de definições de configuração de criptografia de disco da máquina virtual IaaS
* Backup e restauração de VMs criptografadas usando o serviço de Backup do Azure

o Azure Disk Encryption para VMS IaaS para a solução Windows e Linux inclui:

* A extensão da criptografia de disco para Windows.
* A extensão da criptografia de disco para Linux.
* Os cmdlets do PowerShell de criptografia de disco.
* Os cmdlets da CLI (interface de linha de comando) do Azure para criptografia de disco.
* Os modelos do Azure Resource Manager de criptografia de disco.

Há suporte para a solução de Azure Disk Encryption em VMs IaaS executando o Windows ou o sistema operacional Linux. Para obter mais informações sobre os sistemas operacionais com suporte, consulte o artigo [Pré-requisitos](azure-security-disk-encryption-prerequisites.md).

> [!NOTE]
> Não há cobrança de taxas adicionais para a criptografia de discos de VM com o Azure Disk Encryption. O [preço do Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) padrão aplica-se ao cofre de chaves usado para armazenar as chaves de criptografia. 


## <a name="encryption-workflow"></a>Fluxo de trabalho de criptografia

 Para habilitar a criptografia de disco para VMs do Windows e do Linux, faça o seguinte:

1. Escolha um cenário de criptografia entre os cenários de criptografia anteriores.
2. Opte por habilitar a criptografia de disco por meio do modelo do Gerenciador de Recursos de Azure Disk Encryption, cmdlets do PowerShell ou comando da CLI e especifique a configuração de criptografia.

   * Para o cenário do VHD criptografado pelo cliente, carregue o VHD criptografado para sua conta de armazenamento e o material de chave de criptografia para o cofre de chaves. Em seguida, forneça a configuração de criptografia para habilitar a criptografia em uma nova VM IaaS.
   * Para novas VMs que são criadas com base no Marketplace e VMs existentes que já estão em execução no Azure, forneça a configuração de criptografia para habilitar a criptografia na VM IaaS.

3. Conceda acesso à plataforma Azure para ler o material de chave de criptografia (chaves de criptografia BitLocker para sistemas Windows e frase secreta para Linux) de seu cofre de chaves para habilitar a criptografia na VM IaaS.

4. O Azure atualiza o modelo de serviço de VM com a configuração do cofre de chaves e a criptografia e define sua VM criptografada.

 ![Microsoft Antimalware no Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>Fluxo de trabalho de descriptografia
Para desabilitar a criptografia de disco para VMs IaaS, conclua as seguintes etapas de alto nível:

1. Opte por desabilitar a criptografia (descriptografia) em uma VM IaaS em execução no Azure e especifique a configuração da descriptografia. É possível por meio do modelo do Azure Disk Encryption Resource Manager, cmdlets do PowerShell ou CLI do Azure.

 Esta etapa desabilitará a criptografia do sistema operacional ou o volume de dados ou ambos na VM IaaS do Windows em execução. No entanto, como mencionado na seção anterior, não há suporte para desabilitar a criptografia de disco do sistema operacional para Linux. A etapa de descriptografia é permitida apenas para unidades de dados em VMs do Linux, desde que o disco do SO não esteja criptografado.
2. O Azure atualiza o modelo de serviço da VM e a VM IaaS é marcada como descriptografada. O conteúdo da VM não está criptografado em repouso.

> [!NOTE]
> A operação de desabilitação da criptografia não exclui seu cofre de chaves nem o material da chave de criptografia (chaves de criptografia do BitLocker para sistemas Windows ou a Frase secreta para Linux).
 > Não há suporte para desativação de criptografia de disco de OS para Linux. A etapa de descriptografia é permitida somente para unidades de dados em VMs Linux.
Desabilitar criptografia do disco de dados para Linux não tem suporte se a unidade do SO estiver criptografada.


## <a name="encryption-workflow-previous-release"></a>Fluxo de trabalho de criptografia (versão anterior)

A nova versão do Azure Disk Encryption elimina a necessidade de fornecer um parâmetro de aplicativo do Azure AD para habilitar a criptografia de disco de VM. Com a nova versão, você não precisa fornecer uma credencial do Azure AD durante a etapa de habilitação de criptografia. Todas as novas VMs devem ser criptografadas sem os parâmetros do aplicativo do Azure AD, usando a nova versão. As VMs que já foram criptografadas com os parâmetros de aplicativo do Azure AD ainda têm suporte e devem continuar a ser mantidas com a sintaxe do AAD. Para habilitar a criptografia de disco para VMs do Windows e do Linux (versão anterior), faça o seguinte:

1. Escolha um cenário de criptografia entre os cenários de criptografia anteriores.
2. Opte por habilitar a criptografia de disco por meio do modelo do Gerenciador de Recursos de Azure Disk Encryption, cmdlets do PowerShell ou comando da CLI e especifique a configuração de criptografia.

   * Para o cenário do VHD criptografado pelo cliente, carregue o VHD criptografado para sua conta de armazenamento e o material de chave de criptografia para o cofre de chaves. Em seguida, forneça a configuração de criptografia para habilitar a criptografia em uma nova VM IaaS.
   * Para novas VMs que são criadas com base no Marketplace e VMs existentes que já estão em execução no Azure, forneça a configuração de criptografia para habilitar a criptografia na VM IaaS.

3. Conceda acesso à plataforma Azure para ler o material de chave de criptografia (chaves de criptografia BitLocker para sistemas Windows e frase secreta para Linux) de seu cofre de chaves para habilitar a criptografia na VM IaaS.

4. Forneça a identidade de aplicativo Azure AD (Azure Active Directory) para gravar o material de chave de criptografia no cofre de chaves. Isso permite a criptografia na VM IaaS para os cenários mencionados na etapa 2.

5. O Azure atualiza o modelo de serviço de VM com a configuração do cofre de chaves e a criptografia e define sua VM criptografada.


## <a name="terminology"></a>Terminologia
Para entender alguns dos termos comuns usados por essa tecnologia, use a seguinte tabela de terminologia:

| Terminologia | Definição |
| --- | --- |
| AD do Azure | Azure AD significa [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Uma conta do Azure AD é usada para autenticar, armazenar e recuperar segredos de um cofre de chaves. |
| Cofre da Chave do Azure | Key Vault é um serviço de gerenciamento de chaves criptográfico baseado em módulos de segurança de hardware validados pelo FIPS (Federal Information Processing Standards), o qual ajuda a proteger as chaves criptográficas e os segredos confidenciais. Para obter mais informações, confira a documentação do [Key Vault](https://azure.microsoft.com/services/key-vault/). |
| BitLocker |O [BitLocker](https://technet.microsoft.com/library/hh831713.aspx) é uma tecnologia de criptografia de volume do Windows reconhecida pela indústria e usada para habilitar a criptografia de disco em VMs de IaaS do Windows. |
| BEK | As chaves de criptografia do BitLocker são usadas para criptografar o volume de inicialização do sistema operacional e os volumes de dados. As chaves do BitLocker são protegidas em um cofre de chaves como segredos. |
| CLI | Confira [Interface de linha de comando do Azure](/cli/azure/install-azure-cli).|
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) é o subsistema de criptografia de disco transparente baseado em Linux usado para habilitar a criptografia de disco nas VMs de IaaS do Linux. |
| KEK | A criptografia de chave é a chave assimétrica (RSA 2048) que pode ser usada para proteger ou encapsular o segredo. É possível fornecer uma chave protegida por HSM (módulos de segurança de hardware) ou uma chave protegida por software. Para obter mais informações, consulte a documentação do [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). |
| Cmdlets de DNS | Confira [Cmdlets do Azure PowerShell](/powershell/azure/overview). |

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Pré-requisitos do Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md)
