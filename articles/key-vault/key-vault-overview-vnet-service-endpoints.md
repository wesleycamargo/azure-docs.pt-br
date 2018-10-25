---
ms.assetid: ''
title: Pontos de Extremidade de Serviço de VNET para Azure Key Vault | Microsoft Docs
description: Visão geral dos Pontos de Extremidade de Serviço de Rede Virtual para Key Vault
services: key-vault
author: amitbapat
ms.author: ambapat
manager: mbaldwin
ms.date: 08/31/2018
ms.service: key-vault
ms.workload: identity
ms.topic: conceptual
ms.openlocfilehash: 9b0600a7afb07600116440461037c7abcb9236de
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2018
ms.locfileid: "49404317"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Pontos de Extremidade de Serviço de Rede Virtual para Azure Key Vault

Os Pontos de Extremidade de Serviço de Rede Virtual para Key Vault permitem restringir o acesso à Rede Virtual especificada e/ou a uma lista de intervalos de endereços IPv4 (protocolo IP versão 4). Qualquer chamador que conecte o cofre de chaves de fora dessas origens terá acesso negado. Se o cliente tiver optado por permitir "Serviços confiáveis da Microsoft", como Office 365 Exchange Online, Office 365 SharePoint Online, Azure, Azure Resource Manager, Azure Backup etc., as conexões desses serviços passarão pelo firewall. Certamente, será necessário ainda que esses chamadores apresentem um token AAD válido e tenham permissões (configuradas como políticas de acesso) para executar a operação solicitada. Leia mais detalhes técnicos sobre [Pontos de Extremidade de Serviço de Rede Virtual](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="usage-scenarios"></a>Cenários de uso

É possível configurar [redes virtuais e firewalls do Key Vault](key-vault-network-security.md) para negar acesso ao tráfego de todas as redes (incluindo o tráfego de Internet), por padrão. O acesso pode ser concedido ao tráfego de redes virtuais específicas do Azure e/ou intervalos de endereços IP da Internet públicos, permitindo que você crie um limite de rede seguro para os aplicativos.

> [!NOTE]
> Os firewalls do Key Vault e as regras de rede virtual aplicam-se SOMENTE ao [plano de dados](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control). Operações do plano de controle do Key Vault (como criar, excluir, modificar operações, configurar políticas de acesso, configurar firewalls e regras de rede virtual) não são afetadas por firewalls e regras de rede virtual.

Por exemplo,
* Se você estiver usando o Key Vault para armazenar chaves de criptografia, segredos do aplicativo, certificados e quer bloquear o acesso ao seu cofre de chaves da Internet pública.
* Você quer bloquear o acesso ao cofre de chaves para que apenas seu aplicativo ou uma pequena lista de hosts designados possa conectar o cofre de chaves
* Você tem um aplicativo executando na VNET (rede virtual) do Azure e essa VNET é bloqueada para todo o tráfego de entrada e saída. O aplicativo ainda precisa conectar o cofre de chaves para obter segredos ou certificados ou usar chaves criptográficas.

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Configurar redes virtuais e firewalls do Key Vault

Aqui estão as etapas necessárias para configurar redes virtuais e firewalls. Essas etapas permanecem as mesmas, independentemente de qual interface (portal do Azure, PowerShell, CLI) você usará para configurar as regras de rede virtual e firewall.
1. Opcional, mas altamente recomendável: habilite o [registro em log do cofre de chaves](key-vault-logging.md) para ver os logs de acesso detalhados. Isso irá ajudá-lo nos diagnósticos, quando os firewalls e as regras de rede virtual impedirem o acesso a um cofre de chaves.
2. Habilite 'pontos de extremidade de serviço para o cofre de chaves' para redes virtuais de destino e sub-redes
3. Defina regras de rede virtual e firewalls para um cofre de chaves para restringir o acesso a esse cofre de chaves de redes virtuais específicas, sub-redes e intervalos de endereços IPv4.
4. Se esse cofre de chaves precisar ser acessado por qualquer serviço confiável da Microsoft, será necessário habilitar a opção para permitir que o 'Serviço Confiável do Azure' conecte o cofre de chaves.

Consulte [Configurar Redes Virtuais e Firewalls do Azure Key Vault](key-vault-network-security.md) para obter instruções detalhadas passo a passo.

> [!IMPORTANT]
> Depois que as regras de firewall estiverem ativas, todas as operações do [plano de dados](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) SOMENTE poderão ser executadas quando as solicitações do chamador originarem-se de redes virtuais ou intervalos de endereços IPV4 permitidos. Isso também aplica-se ao acesso ao cofre de chaves do portal do Azure. Embora um usuário possa navegar em um cofre de chaves do portal do Azure, talvez ele não consiga listar chaves/segredos/certificados, se o computador cliente não estiver na lista permitida. Isso também afeta o 'Seletor do Cofre de Chaves' de outros serviços do Azure. Os usuários poderão ver a lista de cofres de chaves, mas não listar chaves, se as regras de firewall impedirem o computador cliente.


> [!NOTE]
> * Um máximo de 127 regras de VNET e 127 regras de IPv4 são permitidas. 
> * Os intervalos de endereços pequenos usando o prefixo "/31" ou "/32" não têm suporte. Esses intervalos devem ser configurados usando regras de endereço IP individuais.
> * Regras de rede IP somente são permitidas para endereços IP públicos. Os intervalos de endereços IP reservados para redes privadas (conforme definido na RFC 1918) não são permitidos em regras de IP. Redes privadas incluem endereços que iniciam com *10.*\*, *172.16.*\* e *192.168.*\*. 
> * Atualmente, somente há suporte para endereços IPv4.

## <a name="trusted-services"></a>Serviços confiáveis
Aqui, é apresentada uma lista de serviços confiáveis que poderão acessar um cofre de chaves se a opção 'Permitir serviços confiáveis' estiver habilitada.

|Serviço confiável|Cenários de uso|
| --- | --- |
|Serviço de implantação de Máquinas Virtuais do Microsoft Azure|[Implantar certificados para VMs a partir do Key Vault gerenciado pelo cliente](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/)|
|Serviço de implantação de modelo do Azure Resource Manager|[Transmitir valores seguros durante a implantação](../azure-resource-manager/resource-manager-keyvault-parameter.md)|
|Serviço de criptografia de volume do Azure Disk Encryption|Permitir acesso à Chave do BitLocker (VM do Windows) ou à Frase Secreta de DM (VM do Linux) e à Chave de Criptografia de Chave durante a implantação da VM para habilitar o [Azure Disk Encryption](../security/azure-security-disk-encryption.md)|
|Serviço de Backup do Azure|Permitir backup e restauração de chaves e segredos relevantes durante o backup de VM do Azure, usando o [Backup do Azure](../backup/backup-introduction-to-azure-backup.md)|
|Exchange Online e SharePoint Online|Permitir acesso à chave de cliente para Criptografia do Serviço com [Chave de Cliente](https://support.office.com/article/Controlling-your-data-in-Office-365-using-Customer-Key-f2cd475a-e592-46cf-80a3-1bfb0fa17697).|
|Proteção de Informações do Azure|Permitir acesso à chave de locatário para [Proteção de Informações do Azure.](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)|
|Serviços de Aplicativos|[Implantar Certificado do Aplicativo Web do Azure por meio do Key Vault](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)|
|SQL do Azure|[Transparent Data Encryption com suporte Bring Your Own Key para Data Warehouse e Banco de Dados SQL do Azure](../sql-database/transparent-data-encryption-byok-azure-sql.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current)|
|Armazenamento do Azure|[Criptografia do Serviço de Armazenamento usando chaves gerenciadas pelo cliente no Azure Key Vault](../storage/common/storage-service-encryption-customer-managed-keys.md)|
|Repositório Azure Data Lake|[Criptografia de dados no Azure Data Lake Store](../data-lake-store/data-lake-store-encryption.md) com chave gerenciada pelo cliente|



> [!NOTE]
> As políticas relevantes de acesso ao cofre de chaves devem ser definidas para permitir que os serviços correspondentes tenham acesso ao cofre de chaves.

## <a name="next-steps"></a>Próximas etapas

* [Proteger seu cofre de chaves](key-vault-secure-your-key-vault.md)
* [Configurar Redes Virtuais e Firewalls do Azure Key Vault](key-vault-network-security.md)