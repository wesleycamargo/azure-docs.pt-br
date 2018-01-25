---
title: "Serviços de Domínio do Azure AD: diretrizes de rede | Microsoft Docs"
description: "Considerações de rede para os Serviços de Domínio do Azure Active Directory"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 23a857a5-2720-400a-ab9b-1ba61e7b145a
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: maheshu
ms.openlocfilehash: a6f0089f13de10ba8bc1f9a656a2d21f9c559047
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2018
---
# <a name="networking-considerations-for-azure-ad-domain-services"></a>Considerações de rede para Serviços de Domínio do Azure AD
## <a name="how-to-select-an-azure-virtual-network"></a>Como selecionar uma rede virtual do Azure
As diretrizes a seguir ajudam você a selecionar uma rede virtual a ser usada com os Serviços de Domínio do Azure AD.

### <a name="type-of-azure-virtual-network"></a>Tipo de rede virtual do Azure
* **Redes virtuais do Resource Manager**: o Azure AD Domain Services pode ser habilitados em redes virtuais criadas usando o Azure Resource Manager.
* Não é possível habilitar o Azure AD Domain Services em uma rede virtual clássica do Azure.
* Você pode conectar outras redes virtuais à rede virtual na qual o Azure AD Domain Services está habilitado. Para saber mais, confira a seção [Conectividade de rede](active-directory-ds-networking.md#network-connectivity).

### <a name="azure-region-for-the-virtual-network"></a>Região do Azure para a rede virtual
* O domínio gerenciado pelos Serviços de Domínio do Azure AD é implantado na mesma região do Azure que a rede virtual que você escolheu para habilitar o serviço.
* Selecione uma rede virtual em uma região do Azure com suporte dos Serviços de Domínio do Azure AD.
* Confira a página [Serviços do Azure por região](https://azure.microsoft.com/regions/#services/) para conhecer as regiões do Azure nas quais os Serviços de Domínio do Azure AD estão disponíveis.

### <a name="requirements-for-the-virtual-network"></a>Requisitos para a rede virtual
* **Proximidade com suas cargas de trabalho do Azure**: selecione a rede virtual que atualmente hospeda/hospedará máquinas virtuais que precisam de acesso aos Serviços de Domínio do Azure AD. Se suas cargas de trabalho forem implantadas em uma rede virtual diferente do domínio gerenciado, você também pode optar por conectar redes virtuais.
* **Servidores DNS personalizados/traga seu próprio**: verifique se não há nenhum servidor DNS personalizado configurado para a rede virtual. Um exemplo de um servidor DNS personalizado é uma instância do DNS do Windows Server em execução em uma VM do Windows Server que você implantou na rede virtual. O Azure AD Domain Services não se integra a qualquer servidor DNS personalizado implantado na rede virtual.
* **Domínios existentes com o mesmo nome de domínio**: certifique-se de que não tenha um domínio existente com o mesmo nome de domínio disponível na rede virtual. Por exemplo, vamos supor que você tenha um domínio chamado ‘contoso.com’ já disponível na rede virtual selecionada. Posteriormente, você tenta habilitar um domínio gerenciado pelos Serviços de Domínio do AD do Azure com o mesmo nome de domínio (isto é, ‘contoso.com’) nessa rede virtual. Você encontra uma falha ao tentar habilitar os Serviços de Domínio do AD do Azure. Essa falha ocorre devido a conflitos de nome com o nome de domínio nessa rede virtual. Nessa situação, você deve usar um nome diferente para definir o domínio gerenciado pelos Serviços de Domínio do AD do Azure. Como alternativa, você pode desprovisionar o domínio existente e, em seguida, habilitar os Serviços de Domínio do AD do Azure.

> [!WARNING]
> Você não pode mover Serviços de Domínio para uma rede virtual diferente depois que tiver habilitado o serviço.
>
>


## <a name="guidelines-for-choosing-a-subnet"></a>Diretrizes para escolher uma sub-rede

![Design de sub-rede recomendado](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

* Implante os Serviços de Domínio do Azure AD em uma **sub-rede separada dedicada** em sua rede virtual do Azure.
* Não aplique NSGs à sub-rede dedicada de seu domínio gerenciado. Se você precisar aplicar NSGs à sub-rede dedicada, **não bloqueie as portas necessárias para executar e gerenciar seu domínio**.
* Não restrinja excessivamente o número de endereços IP disponíveis dentro da sub-rede dedicada para seu domínio gerenciado. Essa restrição impede que o serviço disponibilize dois controladores de domínio para seu domínio gerenciado.
* **Não habilite os Serviços de Domínio do Azure AD na sub-rede do gateway** da sua rede virtual.

> [!WARNING]
> Quando você associa um NSG a uma sub-rede na qual os Serviços de Domínio do Azure AD estão habilitados, pode interromper a capacidade da Microsoft de gerenciar o domínio e de fazer o atendimento de usuários. Além disso, a sincronização entre o seu locatário do Azure AD e seu domínio gerenciado é interrompida. **O SLA não se aplica a implantações em que um NSG tenha sido aplicado e impeça os Serviços de Domínio do Azure AD de atualizar e gerenciar seu domínio.**
>
>

## <a name="ports-required-for-azure-ad-domain-services"></a>Portas necessárias para os Serviços de Domínio do Azure AD
As portas a seguir são obrigatórias para os Serviços de Domínio do Azure AD executarem e manterem seu domínio gerenciado. Verifique se essas portas não estão bloqueadas para a sub-rede na qual você habilitou o domínio gerenciado.

| Número da porta | Obrigatório? | Finalidade |
| --- | --- | --- |
| 443 | Obrigatório |Sincronização com seu locatário do Azure AD |
| 5986 | Obrigatório | Gerenciamento do seu domínio |
| 3389 | Opcional | Gerenciamento do seu domínio |
| 636 | Opcional | Proteger o acesso LDAP (LDAPS) para seu domínio gerenciado |

**Porta 443 (sincronização com o Azure AD)**
* Ela é usada para sincronizar seu diretório do Azure AD com o domínio gerenciado.
* É obrigatório permitir o acesso a essa porta no seu NSG. Sem acesso a essa porta, o domínio gerenciado não é sincronizado com o diretório do Azure AD. Os usuários não podem entrar, já que as alterações em suas senhas não são sincronizadas com o domínio gerenciado.
* Você pode restringir o acesso de entrada a essa porta para os endereços IP que pertencem ao intervalo de endereços IP do Azure.

**Porta 5986 (comunicação remota do PowerShell)**
* Ela é usada para executar tarefas de gerenciamento usando a comunicação remota do PowerShell no seu domínio gerenciado.
* É obrigatório permitir o acesso através dessa porta no seu NSG. Sem acesso a essa porta, o domínio gerenciado não pode ser atualizado, configurado, monitorado ou ter o backup feito.
* Você pode restringir o acesso de entrada a essa porta para os seguintes endereços IP de origem: 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161
* Os controladores de domínio para seu domínio gerenciado normalmente não escutam nesta porta. O serviço abre essa porta nos controladores de domínio gerenciados somente quando uma operação de manutenção ou gerenciamento precisa ser executada para o domínio gerenciado. Assim que a operação for concluída, o serviço fecha essa porta nos controladores de domínio gerenciados.

**Porta 3389 (área de trabalho remota)**
* Ele é usado em conexões de área de trabalho remota para controladores de domínio do seu domínio gerenciado.
* A abertura dessa pelo NSG é opcional.
* Essa porta também permanece basicamente desativada no domínio gerenciado. Esse mecanismo não é usado de modo contínuo, uma vez que as tarefas de gerenciamento e monitoramento são executadas usando a comunicação remota do PowerShell. Essa porta é usada apenas no caso raro de a Microsoft precisar nos conectar remotamente ao seu domínio gerenciado para solucionar problemas de maneira avançada. A porta é fechada assim que a operação de solução de problemas é concluída.

**Porta 636 (LDAP Seguro)**
* Usada para habilitar acesso LDAP seguro para seu domínio gerenciado na Internet.
* A abertura dessa pelo NSG é opcional. Abra a porta somente se você tiver acesso LDAP Seguro pela Internet habilitado.
* Você pode restringir o acesso de entrada a essa porta aos endereços IP de origem dos quais você pretende se conectar por LDAP seguro.


## <a name="network-security-groups"></a>Grupos de segurança de rede
Um [NSG (grupo de segurança de rede)](../virtual-network/virtual-networks-nsg.md) contém uma lista de regras de ACL (lista de controle de acesso) que permitem ou negam o tráfego de rede para suas instâncias de VM em uma Rede Virtual. Os NSGs podem ser associados a sub-redes ou instâncias de VM individuais dentro dessa sub-rede. Quando um NSG é associado a uma sub-rede, as regras de ACL se aplicam a todas as instâncias de VM na sub-rede. Além disso, o tráfego para uma VM individual pode ser restrito ainda mais por meio da associação de um NSG diretamente à VM.

### <a name="sample-nsg-for-virtual-networks-with-azure-ad-domain-services"></a>NSG de exemplo para redes virtuais com o Azure AD Domain Services
A tabela a seguir ilustra um NSG de exemplo que você pode configurar para uma rede virtual com um domínio gerenciado do Azure AD Domain Services. Essa regra permite o tráfego de entrada nas portas necessárias para garantir que seu domínio gerenciado permaneça corrigido e atualizado e que possa ser monitorado pela Microsoft. A regra “DenyAll” padrão se aplica a todos os outros tráfegos de entrada da Internet.

Além disso, o NSG também ilustra como bloquear o acesso LDAP seguro pela Internet. Ignore esta regra se você não tiver habilitado o acesso LDAP seguro para seu domínio gerenciado pela Internet. O NSG contém um conjunto de regras que permitem o acesso LDAPS de entrada pela porta TCP 636 somente de um conjunto especificado de endereços IP. A regra de NSG para permitir o acesso LDAPS pela Internet de endereços IP especificados tem prioridade mais alta que a regra de NSG DenyAll.

![Exemplo de NSG para acesso LDAPS seguro pela Internet](.\media\active-directory-domain-services-alerts\default-nsg.png)

**Mais informações** - [Criar um grupo de segurança de rede](../virtual-network/virtual-networks-create-nsg-arm-pportal.md).


## <a name="network-connectivity"></a>Conectividade de rede
Um domínio gerenciado dos Azure AD Domain Services só pode ser habilitado dentro de uma única rede virtual no Azure.

### <a name="scenarios-for-connecting-azure-networks"></a>Cenários para conexão de redes do Azure
Conecte as redes virtuais do Azure para usar o domínio gerenciado em qualquer um dos seguintes cenários de implantação:

#### <a name="use-the-managed-domain-in-more-than-one-azure-virtual-network"></a>Usar o domínio gerenciado em mais de uma rede virtual do Azure
É possível conectar outras redes virtuais do Azure à rede virtual do Azure na qual você habilitou os Azure AD Domain Services. Esta conexão VPN/emparelhamento VNet permite que você use o domínio gerenciado com suas cargas de trabalho implantadas em outras redes virtuais.

![Conectividade de rede virtual clássica](./media/active-directory-domain-services-design-guide/classic-vnet-connectivity.png)

#### <a name="use-the-managed-domain-in-a-resource-manager-based-virtual-network"></a>Usar o domínio gerenciado em uma rede virtual baseada no Resource Manager
É possível conectar uma rede virtual baseada no Resource Manager para a rede virtual clássica do Azure na qual você habilitou os Serviços de Domínio do Azure AD. Esta conexão permite que você use o domínio gerenciado com suas cargas de trabalho implantadas na rede virtual baseada no Resource Manager.

![Resource Manager para conectividade de rede virtual clássica](./media/active-directory-domain-services-design-guide/classic-arm-vnet-connectivity.png)

### <a name="network-connection-options"></a>Opções de conexão de rede
* **Conexões de redes virtuais para redes virtuais usando o emparelhamento de redes virtuais**: o emparelhamento de rede virtual é um mecanismo que conecta duas redes virtuais na mesma região através da rede de backbone do Azure. Uma vez emparelhadas, as duas redes virtuais aparecerão como uma para todos os fins de conectividade. Elas ainda são gerenciadas como recursos separados, mas as máquinas virtuais nessas redes virtuais podem se comunicar diretamente usando o endereço IP privado.

    ![Conectividade de rede virtual usando emparelhamento](./media/active-directory-domain-services-design-guide/vnet-peering.png)

    [Mais informações - emparelhamento de rede virtual](../virtual-network/virtual-network-peering-overview.md)

* **Conexões de Redes Virtuais a Redes Virtuais usando conexões de VPN site a site**: conectar uma rede virtual a outra rede virtual (rede virtual a rede virtual) é semelhante a conectar uma rede virtual a um site local. Os dois tipos de conectividade usam um gateway de VPN para fornecer um túnel seguro usando IPsec/IKE.

    ![Conectividade de rede virtual usando o Gateway de VPN](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

    [Mais informações - conectar redes virtuais usando o gateway de VPN](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

<br>

## <a name="related-content"></a>Conteúdo relacionado
* [Emparelhamento de redes virtuais do Azure](../virtual-network/virtual-network-peering-overview.md)
* [Configurar uma conexão de rede virtual a rede virtual para o modelo de implantação clássica](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)
* [Grupos de Segurança de Rede do Azure](../virtual-network/virtual-networks-nsg.md)
* [Criar um Grupo de Segurança de Rede](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)
