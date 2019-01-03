---
title: Pré-requisitos de implantação (ASDK) de Kit de desenvolvimento de pilha do Azure | Microsoft Docs
description: Examine os requisitos de hardware e ambiente para o Azure Stack desenvolvimento ASDK (Kit).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 22032f9d2e60d3c51546c32df8b98f9633c95535
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53726522"
---
# <a name="azure-stack-deployment-planning-considerations"></a>Considerações de planejamento de implantação de pilha do Azure
Antes de implantar o Azure Stack desenvolvimento ASDK (Kit), certifique-se de que seu computador de host do kit de desenvolvimento atende aos requisitos descritos neste artigo.


## <a name="hardware"></a>Hardware
| Componente | Mínimo | Recomendadas |
| --- | --- | --- |
| Unidades de disco: Sistema operacional |1 disco do sistema operacional com o mínimo de 200 GB disponíveis para a partição do sistema (SSD ou HDD) |1 disco de SO com, no mínimo, 200 GB disponíveis para a partição do sistema (SSD ou HDD) |
| Unidades de disco: Dados do kit de desenvolvimento geral<sup>*</sup>  |4 discos. Cada disco fornece um mínimo de 240 GB de capacidade (SSD ou HDD). Todos os discos disponíveis são usados. |4 discos. Cada disco fornece um mínimo de 400 GB de capacidade (SSD ou HDD). Todos os discos disponíveis são usados. |
| Computação: CPU |Soquete duplo: 16 núcleos físicos (total) |Soquete duplo: 20 núcleos físicos (total) |
| Computação: Memória |192 GB DE RAM |256 GB DE RAM |
| Computação: BIOS |Hyper-V habilitado (com suporte a SLAT) |Hyper-V habilitado (com suporte a SLAT) |
| Rede: NIC |Certificação do Windows Server 2012 R2. Nenhum recurso especializado necessário |Certificação do Windows Server 2012 R2. Nenhum recurso especializado necessário |
| Certificação de logotipo de hardware |[Certificado para o Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[Certificados para o Windows Server 2016](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |

<sup>*</sup> Você precisa de mais do que isso recomendado capacidade, se você planeja adicionar muitos dos [itens do marketplace](asdk-marketplace-item.md) do Azure.

**Configuração da unidade de disco de dados:** Todas as unidades de dados devem ser do mesmo tipo (SAS, todos SATA ou NVMe todos os) e capacidade. Se as unidades de disco SAS forem usadas, as unidades de disco deve ser conectadas por meio de um único caminho (sem MPIO; o suporte a vários caminhos é fornecido).

**Opções de configuração de HBA**

* (Preferencial) HBA Simple
* RAID HBA – adaptador deve ser configurado no modo de "passagem"
* HBA RAID – Os discos devem ser configurados como Disco Único, RAID-0

**Combinações de tipo de mídia e barramento com suporte**

* HDD SATA
* HDD SAS
* HDD RAID
* SSD RAID (se o tipo de mídia é não especificado/unknown<sup>*</sup>)
* SSD SATA + HDD SATA
* SSD SAS + HDD SAS
* NVMe

<sup>*</sup> Controladores RAID sem a capacidade de passagem não podem reconhecer o tipo de mídia. Esses controladores de marcam HDD e SSD como não especificado. Nesse caso, o SSD é usado como um armazenamento persistente, em vez de dispositivos de cache. Portanto, você pode implantar o kit de desenvolvimento no SSDs.

**HBAs de exemplo**: LSI 9207 8i, LSI-9300-8i ou LSI-9265 8i no modo de passagem

Configurações de OEM de exemplo estão disponíveis.

## <a name="operating-system"></a>Sistema operacional
|  | **Requisitos** |
| --- | --- |
| **Versão do SO** |Windows Server 2016 ou posterior. A versão do sistema operacional não é essencial antes de inicia a implantação, conforme você vai Inicialize o computador host no VHD que está incluído na instalação do Azure Stack. O sistema operacional e todos os patches necessários já estão integrados na imagem. Não use nenhuma chave para ativar todas as instâncias do Windows Server usadas no kit de desenvolvimento. |

> [!TIP]
> Depois de instalar o sistema operacional, você pode usar o [Verificador de implantação para o Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) para confirmar que seu hardware atende a todos os requisitos.

## <a name="account-requirements"></a>Requisitos de conta
Normalmente, você deve implantar o kit de desenvolvimento com a conectividade de internet, onde você pode se conectar ao Microsoft Azure. Nesse caso, você deve configurar uma conta do Azure Active Directory (Azure AD) para implantar o kit de desenvolvimento.

Se seu ambiente não está conectado à internet, ou você não quiser usar o Azure AD, você pode implantar o Azure Stack, usando os serviços de Federação do Active Directory (AD FS). O kit de desenvolvimento inclui seus próprio instâncias do AD FS e serviços de domínio do Active Directory. Se você implantar usando essa opção, você não precisa configurar as contas antecipadamente.

>[!NOTE]
Se você implantar usando a opção AD FS, você deve reimplantar o Azure Stack para alternar para o Azure AD.

### <a name="azure-active-directory-accounts"></a>Contas do Active Directory do Azure
Para implantar o Azure Stack, usando uma conta do Azure AD, você deve preparar uma conta do AD do Azure antes de executar o script do PowerShell de implantação. Esta conta se tornará o Administrador Global do locatário do Azure AD. Ele é usado para provisionar e delegar a aplicativos e entidades de serviço para todos os serviços do Azure Stack que interagem com o Azure Active Directory e a API do Graph. Ele também é usado como o proprietário da assinatura do provedor padrão (que pode ser alterado posteriormente). Você pode efetuar login no portal do administrador do sistema do Azure Stack usando essa conta.

1. Crie uma conta do AD do Azure que é o administrador de diretório do AD do Azure pelo menos um. Se você já tiver uma, você pode usá-lo. Caso contrário, você pode criar uma gratuitamente no [ https://azure.microsoft.com/free/ ](https://azure.microsoft.com/pricing/free/) (na China, visite <http://go.microsoft.com/fwlink/?LinkID=717821> em vez disso). Se você planeja posteriormente [registro de pilha do Azure com o Azure](asdk-register.md), você também deve ter uma assinatura em que essa conta criada recentemente.
   
    Salve essas credenciais para uso como o administrador de serviço. Essa conta pode configurar e gerenciar nuvens de recursos, contas de usuário, planos de locatários, cotas e preços. No portal, ele pode criar nuvens de sites, nuvens privadas de máquina virtual, criar planos e gerenciar assinaturas de usuário.
1. Crie conta de usuário de pelo menos um teste no Azure AD para que você pode entrar para o kit de desenvolvimento como um locatário.
   
   | **Conta do Active Directory do Azure** | **Com suporte?** |
   | --- | --- |
   | Conta corporativa ou de Estudante com assinatura válida do Azure público |Sim |
   | Conta da Microsoft com assinatura válida do Azure Público |Sim |
   | Conta corporativa ou de Estudante com assinatura válida do Azure na China |Sim |
   | Conta corporativa ou de Estudante com assinatura válida do US Government Azure |Sim |

Após a implantação, a permissão de administrador global do Azure Active Directory não é necessária. No entanto, algumas operações podem exigir a credencial de administrador global. Por exemplo, um script de instalador do provedor de recursos ou um novo recurso que exigem uma permissão para ser concedida. Você temporariamente pode designar novamente as permissões da conta administrador global ou usar uma conta de administrador global separado que é proprietário do *padrão de assinatura do provedor*.

## <a name="network"></a>Rede
### <a name="switch"></a>Switch
Uma porta disponível em um comutador para a máquina do kit de desenvolvimento.  

A máquina do kit de desenvolvimento dá suporte à conexão a uma porta de tronco ou porta de acesso do comutador. Nenhum recurso especializado é necessário no comutador. Se você estiver usando uma porta de tronco ou se precisar configurar uma ID da VLAN, será necessário fornecer a ID da VLAN como um parâmetro de implantação.

### <a name="subnet"></a>Sub-rede
Não se conectam a máquina do kit de desenvolvimento para as seguintes sub-redes:

* 192.168.200.0/24
* 192.168.100.0/27
* 192.168.101.0/26
* 192.168.102.0/24
* 192.168.103.0/25
* 192.168.104.0/25

Essas sub-redes são reservadas para as redes internas dentro do ambiente do kit de desenvolvimento.

### <a name="ipv4ipv6"></a>IPv4/IPv6
Há suporte apenas para IPv4. Não é possível criar redes IPv6.

### <a name="dhcp"></a>DHCP
Verifique se há um servidor DHCP disponível na rede ao qual a NIC se conecta. Se o DHCP não estiver disponível, você deverá preparar uma rede IPv4 estática adicional, além daquela usada pelo host. Você deve fornecer o endereço IP e o gateway como um parâmetro de implantação.

### <a name="internet-access"></a>Acesso à Internet
O Azure Stack requer acesso à Internet, diretamente ou através de um proxy transparente. O Azure Stack não oferece suporte para a configuração de um proxy da web para habilitar o acesso à Internet. O IP do host e o novo IP atribuído ao AzS-BGPNAT01 (pelo DHCP ou IP estático) devem ser capaz de acessar a Internet. As portas 80 e 443 são usadas nos domínios graph.windows.net e login.microsoftonline.com.


## <a name="next-steps"></a>Próximas etapas
[Baixe o pacote de implantação ASDK](asdk-download.md)
