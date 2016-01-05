<properties 
   pageTitle="Endereços IP públicos e privados no Provedor de Recursos de Rede do Azure | Microsoft Azure"
   description="Saiba mais sobre endereços IP públicos e privados para o Provedor de Recursos de Rede no Gerenciador de Recursos do Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="joaoma" />

# Endereços IP na Rede Virtual do Azure
Este artigo aborda o endereçamento IP para máquinas virtuais, balanceadores de carga, gateways de VPN e Application Gateways.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)].

## Endereços IP públicos
Os endereços IP públicos permitem a comunicação dos recursos do Microsoft Azure com a Internet e com outros serviços do Azure voltados ao público, como o [Cache Redis do Azure](https://azure.microsoft.com/services/cache) e os [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs). Um endereço IP público é um recurso independente e pode ser associado a vários tipos de recursos do Azure, como [máquinas virtuais](virtual-machines-about.md) (VM) e [balanceadores de carga](load-balancer-overview.md) (LB).

### Método de alocação de endereço IP público
Os endereços IP públicos são alocados de um pool de endereços IP disponíveis em um determinado local. Confira a lista completa de intervalos de endereços IP usados pelos datacenters do Microsoft Azure em [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653).

Há dois métodos em que um endereço IP é alocado para um recurso IP público: *dinâmico* ou *estático*. O método de alocação padrão é o *dinâmico*. No caso do método *dinâmico*, o endereço IP **não** é alocado para o *recurso de endereço IP público* no momento da respectiva criação. Quando você cria ou inicia um recurso, como o balanceador de carga ou a VM, associado ao *recurso de endereço IP público*, um IP endereço é alocado do pool de endereços IP. Esse endereço IP é desalocado e liberado para o pool disponível quando o recurso associado é excluído ou interrompido.

Quando você usa o método de alocação *estático*, o endereço IP é alocado ao *recurso de endereço IP público* no momento da respectiva criação. Nesse caso, o mesmo endereço IP permanece alocado, independentemente do estado do recurso associado. Ele é liberado para o pool disponível somente quando o *recurso de Endereço IP público* é excluído ou quando o respectivo método de alocação é modificado para *dinâmico*.

### Resolução DNS
Você pode especificar um rótulo de nome de domínio DNS para um recurso de Endereço IP público, que cria uma entrada DNS correspondente nos servidores DNS do Azure. O FQDN *domainnamelabel*.*location*.cloudapp.azure.com correspondente pode ser resolvido globalmente para o endereço IP alocado ao recurso de endereço IP público.

Abordaremos os diversos tipos de recursos que podem ser associados a um endereço IP público a seguir.

### Máquina Virtual
Um endereço IP público é associado a uma [máquina virtual](virtual-machines-about.md) (VM) por meio de uma NIC. Cada VM pode ter apenas um endereço IP público, não importa o número de NICs associadas a ele. Você pode associar apenas um *recurso de endereço IP público* alocado *dinamicamente* a uma NIC de VM. No caso de uma VM com várias NICs, o *recurso de endereço IP público* pode ser associado apenas à NIC principal.

### Balanceador de carga do Azure
Um endereço IP público pode ser associado a uma configuração de front-end do [Balanceador de Carga do Azure](load-balancer-overview.md), que serve como balanceamento de carga do endereço VIP (IP virtual) acessível pela Internet. Você pode associar tanto o recurso de endereço IP público *dinâmico* como o *estático* a um LB. É possível associar vários *recursos de Endereço IP público* para uma configuração de front-end do Balanceador de Carga, que possibilita trabalhar em cenários, como ambientes com vários locatários, com diversos sites baseados em SSL.

### Application Gateway
Um endereço IP público pode ser associado a uma configuração de front-end do [Application Gateway do Azure](application-gateway-introduction.md), para configurá-lo com um VIP (IP virtual) com balanceamento de carga acessível na Internet. Atualmente, você pode associar apenas um recurso de endereço IP público alocado *dinamicamente* a um Application Gateway. No entanto, é possível associar vários endereços IP públicos.

### Gateway de VPN
Um endereço IP público deve estar associado a uma configuração de IP do [Gateway de VPN do Azure](vpn-gateway-about-vpngateways.md), no processo de criação de uma conexão VPN entre uma rede virtual do Azure e uma rede local ou outra rede virtual do Azure. Atualmente, você pode associar apenas um recurso de endereço IP público alocado *dinamicamente* a um Gateway de VPN.

### Imediato

|Recurso|Alocação estática|Alocação dinâmica|Vários endereços IP|
|---|---|---|---|
|VM (máquina virtual)/NIC (placa de interface de rede)|Sim|Não|Não|
|Front-end do balanceador de carga|Sim|Sim|Sim|
|Front-end do Application Gateway|Sim|Não|Sim|
|Gateway de VPN|Sim|Não|Não|

## Endereços IP privados
Os endereços IP privados permitem comunicações entre os recursos de uma rede virtual sem usar endereços IP acessíveis pela Internet. Esse endereço IP é alocado em um intervalo de endereços da sub-rede dentro da rede virtual.

### Método de alocação de endereço IP privado
Os endereços IP podem ser alocados pelo método *dinâmico* ou *estático*. O método de alocação padrão é o *dinâmico*, em que o endereço IP é alocado usando o protocolo DHCP. Como alternativa, o método de alocação pode ser especificado explicitamente como *estático* juntamente com a especificação de um endereço IP. Nesse caso, o recurso é alocado no endereço IP especificado, desde que ele esteja dentro do intervalo de endereços da sub-rede e que esteja livre (não alocado para outros recursos).

Há diversos tipos de recursos que podem ser atribuídos a um endereço IP privado. Observe que os métodos de alocação *estático* ou *dinâmico* funcionam com todos esses tipos de recursos.

### Máquina Virtual
Um endereço IP privado é atribuído à NIC de uma [máquina virtual](virtual-machines-about.md) (VM). Cada VM pode ter a mesma quantidade de endereços IP privados que as NICs associadas a ela.

#### Resolução do nome do host DNS interno
Todas as VMs do Azure são configuradas com [servidores DNS gerenciados do Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution), a menos que seja explicitamente especificado durante a criação. No caso do uso de servidores DNS gerenciados do Azure, um registro DNS que resolve o nome do host da VM para o respectivo endereço IP privado é criado automaticamente. No caso de uma VM com várias NICs, o nome do host é resolvido para o endereço IP privado da NIC principal.

### Balanceador de Carga Interno
Um endereço IP privado pode ser atribuído a um front-end do [Balanceador de Carga Interno do Azure](load-balancer-internal-overview.md), que serve como o balanceamento de carga do VIP (IP virtual) para os recursos, dentro da respectiva rede virtual. Dessa forma, é possível fazer o balanceamento de carga sem expor o endereço IP à Internet.

### Application Gateway
Você pode atribuir um endereço IP privado a um front-end do [Application Gateway do Azure](application-gateway-introduction.md), para configurá-lo com um ponto de extremidade interno não exposto à Internet, também conhecido como ponto de extremidade do ILB. Os métodos de comportamento e alocação são semelhantes ao ILB descrito acima.

### Imediato
|Recurso|Alocação estática|Alocação dinâmica|Vários endereços IP|
|---|---|---|---|
|VM (máquina virtual)/NIC (placa de interface de rede)|Sim|Sim|Sim|
|Front-end do balanceador de carga|Sim|Sim|Sim|
|Front-end do Application Gateway|Sim|Sim|Sim|

## Próximas etapas


[Referência do Azure PowerShell para rede](https://msdn.microsoft.com/library/azure/mt163510.aspx)

[Idioma de modelo do Gerenciador de Recursos do Azure](../resource-group-authoring-templates.md)

[Rede do Azure - modelos usados frequentemente](https://github.com/Azure/azure-quickstart-templates)

[Provedor de recursos de computação](../virtual-machines-azurerm-versus-azuresm)

[Visão Geral do Gerenciador de Recursos do Azure](../resource-group-overview)

[Controle de acesso com base em função no Gerenciador de Recursos do Azure](https://msdn.microsoft.com/library/azure/dn906885.aspx)

[Usando marcas no Gerenciador de Recursos do Azure](https://msdn.microsoft.com/library/azure/dn848368.aspx)

[Implantações de modelo](https://msdn.microsoft.com/library/azure/dn790549.aspx)

<!---HONumber=AcomDC_1210_2015-->