---
title: "Os pré-requisitos de implantação do Kit de desenvolvimento de pilha do Azure | Microsoft Docs"
description: Exiba os requisitos de hardware e de ambiente para o Kit de desenvolvimento de pilha do Azure (operador de nuvem).
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 32a21d9b-ee42-417d-8e54-98a7f90f7311
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/14/2017
ms.author: jeffgilb
ms.openlocfilehash: 8a0d23e14ef50034d5f9595cf154c3513a09c464
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="azure-stack-deployment-prerequisites"></a>Pré-requisitos de implantação de pilha do Azure

*Aplica-se a: Kit de desenvolvimento de pilha do Azure*

Antes de implantar [Kit de desenvolvimento de pilha do Azure](azure-stack-poc.md), verifique se o computador atende aos seguintes requisitos:


## <a name="hardware"></a>Hardware
| Componente | Mínimo | Recomendadas |
| --- | --- | --- |
| Unidades de disco: sistema operacional |1 disco de SO com, no mínimo, 200 GB disponíveis para a partição do sistema (SSD ou HDD) |1 disco de SO com, no mínimo, 200 GB disponíveis para a partição do sistema (SSD ou HDD) |
| Unidades de disco: dados do kit de desenvolvimento geral * |4 discos. Cada disco fornece um mínimo de 140 GB de capacidade (SSD ou HDD). Todos os discos disponíveis serão usados. |4 discos. Cada disco fornece um mínimo de 250 GB de capacidade (SSD ou HDD). Todos os discos disponíveis serão usados. |
| Computação: CPU |Dois soquetes: 12 de núcleos (total) |Dois soquetes: 16 núcleos (total) |
| Computação: memória |96 GB de RAM |128 GB de RAM (esse é o mínimo para dar suporte a provedores de recursos de PaaS).|
| Computação: BIOS |Hyper-V habilitado (com suporte a SLAT) |Hyper-V habilitado (com suporte a SLAT) |
| Rede: NIC |Certificação do Windows Server 2012 R2 necessária para a NIC; nenhum recurso especializado necessário |Certificação do Windows Server 2012 R2 necessária para a NIC; nenhum recurso especializado necessário |
| Certificação de logotipo de hardware |[Certificado para o Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[Certificado para o Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |

\*Você precisará de mais do que isso recomendado capacidade se você planeja adicionar muitas o [itens do marketplace](azure-stack-download-azure-marketplace-item.md) do Azure.

**Configuração de unidade de disco de dados:** todas as unidades de dados devem ser do mesmo tipo (SAS, todos os SATA ou NVMe todos os) e capacidade. Se os discos rígidos SAS são usados, as unidades de disco deve ser anexadas por meio de um único caminho (sem MPIO, suporte a vários caminhos é fornecido).

**Opções de configuração de HBA**

* (Preferencial) HBA Simple
* RAID HBA – adaptador deve ser configurado no modo de "passagem"
* HBA RAID – Os discos devem ser configurados como Disco Único, RAID-0

**Combinações de tipo de mídia e barramento com suporte**

* HDD SATA
* HDD SAS
* HDD RAID
* SSD de RAID (se o tipo de mídia é não especificado/desconhecido\*)
* SSD SATA + HDD SATA
* SSD SAS + HDD SAS
* NVMe

\*Controladores RAID sem a capacidade de passagem não podem reconhecer o tipo de mídia. Esses controladores marcarão HDD e SSD como Não especificado. Nesse caso, o SSD será usado como armazenamento persistente, em vez de dispositivos de caching. Portanto, você pode implantar o kit de desenvolvimento os SSDs.

**HBAs de exemplo**: LSI 9207-8i, LSI-9300-8i ou LSI-9265-8i no modo de passagem

Configurações de OEM de exemplo estão disponíveis.

## <a name="operating-system"></a>Sistema operacional
|  | **Requisitos** |
| --- | --- |
| **Versão do SO** |Windows Server 2012 R2 ou posterior. A versão do sistema operacional não crítica antes de inicia a implantação, como você vai Inicialize o computador de host em um VHD que está incluído na instalação do Azure pilha. O sistema operacional e todos os patches necessários já estão integrados à imagem. Não use todas as chaves para ativar a qualquer instância de servidor do Windows usada no kit de desenvolvimento. |

## <a name="deployment-requirements-check-tool"></a>Ferramenta de verificação de requisitos de implantação
Depois de instalar o sistema operacional, você pode usar o [Verificador de implantação do Azure pilha](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) para confirmar se o hardware atende a todos os requisitos.

## <a name="account-requirements"></a>Requisitos de conta
Normalmente, você implanta o kit de desenvolvimento de conectividade com a internet, onde você pode se conectar ao Microsoft Azure. Nesse caso, você deve configurar uma conta do Azure Active Directory (AD do Azure) para implantar o kit de desenvolvimento.

Se seu ambiente não está conectado à internet, ou você não quiser usar o AD do Azure, você pode implantar a pilha do Azure usando os serviços de Federação do Active Directory (AD FS). O kit de desenvolvimento inclui seus próprio instâncias do AD FS e serviços de domínio do Active Directory. Se você implantar usando essa opção, você não precisa configurar contas antecipadamente.

>[!NOTE]
Se você implantar usando a opção AD FS, você deve reimplantar a pilha do Azure para alternar para o AD do Azure.

### <a name="azure-active-directory-accounts"></a>Contas do Active Directory do Azure
Para implantar a pilha do Azure usando uma conta do AD do Azure, você deve preparar uma conta do AD do Azure antes de executar a implantação de script do PowerShell. Essa conta se torna o Administrador Global do locatário do AD do Azure. Ele é usado para provisionar e delegar a aplicativos e entidades de serviço para todos os serviços do Azure pilha que interagem com o Active Directory do Azure e API do Graph. Ele também é usado como o proprietário da assinatura do provedor padrão (que você pode alterar posteriormente). Você pode efetuar login no portal do administrador do seu sistema de pilha do Azure usando essa conta.

1. Crie uma conta do AD do Azure que é o administrador do diretório para pelo menos um AD do Azure. Se você já tiver um, você pode usá-lo. Caso contrário, você pode criar um gratuitamente em [http://azure.microsoft.com/en-us/pricing/free-trial/](http://azure.microsoft.com/pricing/free-trial/) (na China, visite <http://go.microsoft.com/fwlink/?LinkID=717821> em vez disso). Se você planeja posteriormente [registrar pilha do Azure com o Azure](azure-stack-register.md), você também deve ter uma assinatura neste recém-criada em conta.
   
    Salvar essas credenciais para uso na etapa 6 de [implantar o kit de desenvolvimento](azure-stack-run-powershell-script.md#deploy-the-development-kit). Isso *administrador de serviço* conta pode configurar e gerenciar nuvens de recursos, contas de usuário, planos de locatários, cotas e preços. No portal, ele pode criar nuvens de sites, nuvens privadas de máquina virtual, criar planos e gerenciar assinaturas de usuário.
2. [Criar](azure-stack-add-new-user-aad.md) pelo menos uma conta de forma que você pode entrar o Kit de desenvolvimento como um locatário.
   
   | **Conta do Active Directory do Azure** | **Com suporte?** |
   | --- | --- |
   | Conta corporativa ou escolar com assinatura pública válida do Azure |Sim |
   | Conta da Microsoft com assinatura válida do Azure Público |Sim |
   | Conta corporativa ou escolar com assinatura válida do Azure na China |Sim |
   | Conta corporativa ou escolar com assinatura válida do US Government Azure |Sim |

## <a name="network"></a>Rede
### <a name="switch"></a>Switch
Uma porta disponível em um comutador para a máquina do kit de desenvolvimento.  

A máquina do kit de desenvolvimento oferece suporte para conexão a uma porta de tronco ou a porta do comutador acesso. Nenhum recurso especializado é necessário no comutador. Se você estiver usando uma porta de tronco ou se precisar configurar uma ID da VLAN, será necessário fornecer a ID da VLAN como um parâmetro de implantação. Você pode ver exemplos de [lista de parâmetros de implantação](azure-stack-run-powershell-script.md).

### <a name="subnet"></a>Sub-rede
Não conectar-se a máquina do kit de desenvolvimento para as seguintes sub-redes:

* 192.168.200.0/24
* 192.168.100.0/27
* 192.168.101.0/26
* 192.168.102.0/24
* 192.168.103.0/25
* 192.168.104.0/25

Essas sub-redes são reservadas para as redes internas no ambiente do kit de desenvolvimento.

### <a name="ipv4ipv6"></a>IPv4/IPv6
Há suporte apenas para IPv4. Não é possível criar redes IPv6.

### <a name="dhcp"></a>DHCP
Verifique se há um servidor DHCP disponível na rede ao qual a NIC se conecta. Se o DHCP não estiver disponível, você deverá preparar uma rede IPv4 estática adicional, além daquela usada pelo host. Você deve fornecer o endereço IP e o gateway como um parâmetro de implantação. Você pode ver exemplos de [lista de parâmetros de implantação](azure-stack-run-powershell-script.md).

### <a name="internet-access"></a>Acesso à Internet
A pilha do Azure requer acesso à Internet, diretamente ou através de um proxy transparente. A pilha do Azure não oferece suporte à configuração de um proxy da web para habilitar o acesso à Internet. O IP do host e o novo IP atribuído ao MAS-BGPNAT01 (pelo DHCP ou IP estático) devem ser capaz de acessar a Internet. As portas 80 e 443 são usadas em domínios de graph.windows.net e login.microsoftonline.com.

## <a name="telemetry"></a>Telemetria

Telemetria nos ajuda a formatar versões futuras da pilha do Azure. Ele nos permite responder rapidamente aos comentários, fornece novos recursos e melhorar a qualidade. A pilha do Microsoft Azure inclui o Windows Server 2016 e do SQL Server 2014. Nenhum destes produtos são alterados de configurações padrão e ambos são descritas pela declaração de privacidade do Microsoft Enterprise. A pilha do Azure também contém o software de código-fonte aberto que não foi modificado para enviar telemetria à Microsoft. Aqui estão alguns exemplos de dados de telemetria de pilha do Azure:

- informações de registro de implantação
- Quando um alerta é aberto e fechado
- o número de recursos de rede

Para oferecer suporte ao fluxo de dados de telemetria, a porta 443 (HTTPS) deve estar aberta em sua rede. O ponto de extremidade do cliente é https://vortex-win.data.microsoft.com.

Se você não quiser fornecer Telemetria da pilha do Azure, você pode desativá-lo no host do kit de desenvolvimento e as máquinas virtuais de infraestrutura conforme explicado a seguir.

### <a name="turn-off-telemetry-on-the-development-kit-host-optional"></a>Desativar a telemetria no host do kit de desenvolvimento (opcional)

>[!NOTE]
Se você deseja desativar a telemetria para o host do kit de desenvolvimento, você deve fazer isso antes de executar o script de implantação.

Antes de [a execução do script asdk installer.ps1]() para implantar o host do kit de desenvolvimento, inicialize o CloudBuilder.vhdx e execute o seguinte script em uma janela elevada do PowerShell:
```powershell
### Get current AllowTelmetry value on DVM Host
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
### Set & Get updated AllowTelemetry value for ASDK-Host 
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name "AllowTelemetry" -Value '0'  
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
```

Configuração **AllowTelemetry** como 0 desativa a telemetria para a implantação do Windows e a pilha do Azure. Somente os eventos críticos de segurança do sistema operacional são enviados. A configuração controla telemetria do Windows em todos os hosts e máquinas virtuais da infraestrutura e é reaplicada ao novo/VMs de nós quando ocorrem operações de expansão.


### <a name="turn-off-telemetry-on-the-infrastructure-virtual-machines-optional"></a>Desativar a telemetria nas máquinas virtuais de infraestrutura (opcional)

Após a implantação for bem-sucedida, execute o seguinte script em uma janela elevada do PowerShell (como o usuário AzureStack\AzureStackAdmin) no host do kit de desenvolvimento:

```powershell
$AzSVMs= get-vm |  where {$_.Name -like "AzS-*"}
### Show current AllowTelemetry value for all AzS-VMs
invoke-command -computername $AzSVMs.name {(Get-ItemProperty -Path `
"HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" -Name AllowTelemetry).AllowTelemetry}
### Set & Get updated AllowTelemetry value for all AzS-VMs
invoke-command -computername $AzSVMs.name {Set-ItemProperty -Path `
"HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" -Name "AllowTelemetry" -Value '0'}
invoke-command -computername $AzSVMs.name {(Get-ItemProperty -Path `
"HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" -Name AllowTelemetry).AllowTelemetry}
```

Para configurar a telemetria do SQL Server, consulte [como configurar o SQL Server 2016](https://support.microsoft.com/en-us/help/3153756/how-to-configure-sql-server-2016-to-send-feedback-to-microsoft).

### <a name="usage-reporting"></a>Relatório de uso

Por meio do registro, a pilha do Azure também está configurada para informações sobre o uso de encaminhamento para o Azure. Relatório de uso é controlado independentemente da telemetria. Você pode desativar o uso de relatórios quando [registrando](azure-stack-register.md) usando o script no Github. Basta definir o **$reportUsage** parâmetro **$false**.

Dados de uso são formatados como detalhado no [dados de uso da pilha do relatório do Azure para o Azure](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-usage-reporting). Usuários do Kit de desenvolvimento de pilha do Azure, na verdade, não são cobrados. Essa funcionalidade está incluída no kit de desenvolvimento para que você possa testar para ver como funciona o relatório de uso. 


## <a name="next-steps"></a>Próximas etapas
[Baixe o pacote de implantação do kit de desenvolvimento do Azure pilha](https://azure.microsoft.com/overview/azure-stack/try/?v=try)

[Implantar o kit de desenvolvimento de pilha do Azure](azure-stack-run-powershell-script.md)

