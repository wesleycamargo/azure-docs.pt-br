<properties
	pageTitle="Serviços de Domínio do Azure AD: diretrizes de rede | Microsoft Azure"
	description="Considerações de rede para os Serviços de Domínio do Azure Active Directory"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="maheshu"/>

# Considerações de rede para Serviços de Domínio do Azure AD

## Como selecionar uma rede virtual do Azure
As diretrizes a seguir ajudam você a selecionar uma rede virtual a ser usada com os Serviços de Domínio do Azure AD.

### Tipo de rede virtual do Azure

- Você pode habilitar os Serviços de Domínio do Azure AD em uma rede virtual clássica do Azure.

- Os Serviços de Domínio do Azure AD **não podem ser habilitados em redes virtuais criadas usando o Azure Resource Manager**.

- Você pode conectar uma rede virtual baseada no Resource Manager a uma rede virtual clássica na qual os Serviços de Domínio do Azure AD estão habilitados. Depois disso, você poderá usar os Serviços de Domínio do Azure AD na rede virtual baseada no Resource Manager.

- **Redes Virtuais Regionais**: se você planeja usar uma rede virtual existente, certifique-se de que se trata de uma rede virtual regional.

    - Redes virtuais que usam o mecanismo de grupos de afinidade herdado não podem ser usadas com os Serviços de Domínio do Azure AD.

	- Para usar os Serviços de Domínio do Azure AD, [migre as redes virtuais herdadas para as redes virtuais regionais](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).


### Região do Azure para a rede virtual

- O domínio gerenciado pelos Serviços de Domínio do Azure AD é implantado na mesma região do Azure que a rede virtual que você escolheu para habilitar o serviço.

- Selecione uma rede virtual em uma região do Azure com suporte dos Serviços de Domínio do Azure AD.

- Confira a página [Serviços do Azure por região](https://azure.microsoft.com/regions/#services/) para conhecer as regiões do Azure nas quais os Serviços de Domínio do Azure AD estão disponíveis.


### Requisitos para a rede virtual

- **Proximidade com suas cargas de trabalho do Azure**: selecione a rede virtual que atualmente hospeda/hospedará máquinas virtuais que precisam de acesso aos Serviços de Domínio do Azure AD.

- **Servidores DNS personalizados/traga seu próprio**: verifique se não há nenhum servidor DNS personalizado configurado para a rede virtual.

- **Domínios existentes com o mesmo nome de domínio**: certifique-se de que não tenha um domínio existente com o mesmo nome de domínio disponível na rede virtual. Por exemplo, vamos supor que você tenha um domínio chamado ‘contoso.com’ já disponível na rede virtual selecionada. Posteriormente, você tenta habilitar um domínio gerenciado pelos Serviços de Domínio do AD do Azure com o mesmo nome de domínio (isto é, ‘contoso.com’) nessa rede virtual. Você encontra uma falha ao tentar habilitar os Serviços de Domínio do AD do Azure. Essa falha ocorre devido a conflitos de nome com o nome de domínio nessa rede virtual. Nessa situação, você deve usar um nome diferente para definir o domínio gerenciado pelos Serviços de Domínio do AD do Azure. Como alternativa, você pode desprovisionar o domínio existente e, em seguida, habilitar os Serviços de Domínio do AD do Azure.

> [AZURE.WARNING] Você não pode mover Serviços de Domínio para uma rede virtual diferente depois que tiver habilitado o serviço.


## Grupos de Segurança de Rede e design de sub-rede
O [NSG (Grupo de segurança de rede)](../virtual-network/virtual-networks-nsg.md) contém uma lista de regras de ACL (Lista de Controle de Acesso) que permitem ou negam o tráfego de rede para suas instâncias de VM em uma Rede Virtual. Os NSGs podem ser associados a sub-redes ou instâncias de VM individuais dentro dessa sub-rede. Quando um NSG é associado a uma sub-rede, as regras de ACL se aplicam a todas as instâncias de VM na sub-rede. Além disso, o tráfego para uma VM individual pode ser restrito ainda mais por meio da associação de um NSG diretamente à VM.

> [AZURE.NOTE] **Implante os Serviços de Domínio do Azure AD em uma sub-rede separada dedicada em sua rede virtual do Azure. Não aplique o NSG à sub-rede dedicada. Não habilite os Serviços de Domínio do Azure AD na sub-rede do gateway da sua rede virtual.**

![Design de sub-rede recomendado](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

> [AZURE.WARNING] Quando você associa um NSG a uma sub-rede na qual os Serviços de Domínio do Azure AD estão habilitados, pode interromper a capacidade da Microsoft de gerenciar o domínio e de fazer o atendimento de usuários. Além disso, a sincronização entre o seu locatário do Azure AD e seu domínio gerenciado é interrompida. **O SLA não se aplica a implantações em que um NSG foi aplicado à sub-rede na qual os Serviços De domínio do Azure AD estão habilitados.**


## Conectividade de rede
Um domínio gerenciado dos Serviços de Domínio do Azure AD só pode ser habilitado dentro de uma única rede virtual clássica no Azure. Não há suporte para as redes virtuais criadas usando o Azure Resource Manager.

### Cenários para conexão de redes do Azure
Conecte as redes virtuais do Azure para usar o domínio gerenciado em qualquer um dos seguintes cenários de implantação:

#### Usar o domínio gerenciado em mais de uma rede virtual clássica do Azure
É possível conectar outras redes virtuais clássicas do Azure à rede virtual clássica do Azure na qual você habilitou os Serviços de Domínio do Azure AD. Esta conexão permite que você use o domínio gerenciado com suas cargas de trabalho implantadas em outras redes virtuais.

![Conectividade de rede virtual clássica](./media/active-directory-domain-services-design-guide/classic-vnet-connectivity.png)

#### Usar o domínio gerenciado em uma rede virtual baseada no Resource Manager
É possível conectar uma rede virtual baseada no Resource Manager para a rede virtual clássica do Azure na qual você habilitou os Serviços de Domínio do Azure AD. Esta conexão permite que você use o domínio gerenciado com suas cargas de trabalho implantadas na rede virtual baseada no Resource Manager.

![Resource Manager para conectividade de rede virtual clássica](./media/active-directory-domain-services-design-guide/classic-arm-vnet-connectivity.png)


### Opções de conexão de rede

- **Conexões de Redes Virtuais a Redes Virtuais usando conexões de VPN site a site**: conectar uma rede virtual a outra rede virtual (rede virtual a rede virtual) é semelhante a conectar uma rede virtual a um site local. Os dois tipos de conectividade usam um gateway de VPN para fornecer um túnel seguro usando IPsec/IKE.

	![Conectividade de rede virtual usando o Gateway de VPN](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

    [Mais informações - conectar redes virtuais usando o gateway de VPN](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)


- **Conexões de redes virtuais para redes virtuais usando o emparelhamento de redes virtuais**: o emparelhamento de rede virtual é um mecanismo que conecta duas redes virtuais na mesma região através da rede de backbone do Azure. Uma vez emparelhadas, as duas redes virtuais aparecerão como uma para todos os fins de conectividade. Elas ainda são gerenciadas como recursos separados, mas as máquinas virtuais nessas redes virtuais podem se comunicar diretamente usando o endereço IP privado.

    ![Conectividade de rede virtual usando emparelhamento](./media/active-directory-domain-services-design-guide/vnet-peering.png)

	[Mais informações - emparelhamento de rede virtual](../virtual-network/virtual-network-peering-overview.md)



<br>

## Conteúdo relacionado

- [Emparelhamento de redes virtuais do Azure](../virtual-network/virtual-network-peering-overview.md)

- [Configurar uma conexão de rede virtual a rede virtual para o modelo de implantação clássica](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

- [Grupos de Segurança de Rede do Azure](../virtual-network/virtual-networks-nsg.md)

<!---HONumber=AcomDC_0921_2016-->