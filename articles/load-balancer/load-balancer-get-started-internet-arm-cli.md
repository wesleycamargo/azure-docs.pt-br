<properties 
   pageTitle="Criar um balanceador de carga voltado para a Internet no Gerenciador de Recursos usando a CLI do Azure | Microsoft Azure"
   description="Saiba como criar um balanceador de carga voltado para a Internet no Gerenciador de Recursos usando a CLI do Azure"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/21/2015"
   ms.author="joaoma" />

# Introdução à criação de um balanceador de carga para a Internet usando a CLI do Azure

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo aborda o modelo de implantação do Gerenciador de Recursos. Se você está procurando por um modelo de implantação clássico do Azure, vá para [Introdução à criação de um balanceador de carga para a Internet usando a implantação clássica](load-balancer-get-started-internet-classic-portal.md)


[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]


## O que é necessário para criar um balanceador de carga para a Internet?

Você precisa criar e configurar os objetos a seguir para implantar um balanceador de carga.

- Configuração de IP de front-end – contém endereços IP públicos para o tráfego de rede de entrada. 

- Pool de endereços de back-end – contém NICs (Interfaces de Rede) para receber o tráfego do balanceador de carga.

- Regras de balanceamento de carga – contém regras de mapeamento de uma porta pública no balanceador de carga para portas nas NICs no pool de endereços de back-end.

- Regras NAT de entrada – contém regras de mapeamento de uma porta pública no balanceador de carga para portas nas NICs no pool de endereços de back-end.

- Testes – contém investigações de integridade usadas para verificar a disponibilidade de VMs vinculadas a NICs no pool de endereços de back-end.

É possível obter mais informações sobre componentes do balanceador de carga com o gerenciador de recursos do Azure em [Suporte do Gerenciador de Recursos do Azure para balanceador de carga](load-balancer-arm.md).

## Configurar a CLI para usar o Gerenciador de Recursos

1. Se você nunca usou a CLI do Azure, consulte [Instalar e configurar a CLI do Azure](xplat-cli.md) e siga as instruções até o ponto em que você seleciona sua conta e assinatura do Azure.

2. Execute o comando **azure config mode** para alternar para o modo do Gerenciador de Recursos, como mostrado abaixo.

		azure config mode arm

	Saída esperada:

		info:    New mode is arm

## Criar uma rede virtual e um endereço IP público para o pool de IPs de front-end

### Etapa 1

Crie uma rede virtual (VNet) chamada *NRPVnet* no local Leste dos EUA usando um grupo de recursos chamado *NRPRG*.

	azure network vnet create NRPRG NRPVnet eastUS -a 10.0.0.0/16

Crie uma sub-rede chamada *NRPVnetSubnet* com um bloco CIDR de 10.0.0.0/24 na *NRPVnet*.

	azure network vnet subnet create NRPRG NRPVnet NRPVnetSubnet -a 10.0.0.0/24

### Etapa 2

Crie um endereço IP público chamado *NRPPublicIP* a ser usado por um pool de IPs front-end com nome DNS *loadbalancernrp.eastus.cloudapp.azure.com*. O comando a seguir usa o tipo de alocação estática e o tempo limite de ociosidade de 4 minutos.

	azure network public-ip create -g NRPRG -n NRPPublicIP -l eastus -d loadbalancernrp -a static -i 4


>[AZURE.IMPORTANT]O balanceador de carga usará o rótulo de domínio do IP público como FQDN. Isso é uma mudança da implantação clássica, que usa serviço de nuvem como FQDN do balanceador de carga. Neste exemplo, o FQDN será *loadbalancernrp.eastus.cloudapp.azure.com*.

## Criar um balanceador de carga

No exemplo a seguir, o comando criará um balanceador de carga chamado *NRPlb* no grupo de recursos *NRPRG* no local do Azure *Leste dos EUA*.

	azure network lb create NRPRG NRPlb eastus

## Criar um pool de IPs de front-end e um pool de endereços de back-end

O exemplo a seguir cria o pool de IPs front-end que receberá o tráfego de rede de entrada para o balanceador de carga e o pool de IPs back-end no qual o pool de front-end enviará o tráfego de rede com balanceamento de carga.

### Etapa 1 

Crie um pool de IPs front-end associando o IP público criado na etapa anterior e o balanceador de carga.

	azure network lb frontend-ip create nrpRG NRPlb NRPfrontendpool -i nrppublicip

### Etapa 2 

Configurar um pool de endereços de back-end usado para receber o tráfego de entrada do pool de IPs front-end.

	azure network lb address-pool create NRPRG NRPlb NRPbackendpool

## Criar regras de balanceamento de carga, regras NAT e investigação

O exemplo abaixo cria os itens a seguir.

- uma regra NAT para converter todo o tráfego de entrada na porta 3441 para a porta 3389<sup>1</sup>
- uma regra NAT para converter todo o tráfego de entrada na porta 3442 para a porta 3389
- uma regra de balanceador de carga para equilibrar todo o tráfego de entrada na porta 80 para a porta 80 de endereços no pool de back-end.
- uma regra de investigação que verificará o status de integridade em uma página denominada *HealthProbe.aspx*.

<sup>1</sup> regras NAT são associadas a uma instância de máquina virtual específica por trás do balanceador de carga. O tráfego de rede de entrada na porta 3341 receberá uma máquina virtual específica na porta 3389 associada a uma regra NAT no exemplo abaixo. Você deve escolher um protocolo para a regra NAT, UDP ou TCP. Ambos os protocolos não podem ser atribuídos à mesma porta.

### Etapa 1

Criar as regras NAT.

	azure network lb inbound-nat-rule create -g nrprg -l nrplb -n rdp1 -p tcp -f 3441 -b 3389
	azure network lb inbound-nat-rule create -g nrprg -l nrplb -n rdp2 -p tcp -f 3442 -b 3389

Parâmetros:

- **-g** - nome do grupo de recursos
- **-l** - nome do balanceador de carga 
- **- n** - nome do recurso, quer seja a regra nat, a investigação ou a regra de balanceamento de carga.
- **-p** - protocolo (pode ser TCP ou UDP)  
- **-f** - porta de front-end a ser usada (o comando probe usa -f para definir o caminho da investigação)
- **-b** - porta de back-end a ser usada

### Etapa 2

Crie uma regra de balanceador de carga.

	azure network lb probe create -g nrprg -l nrplb -n healthprobe -p "http" -o 80 -f healthprobe.aspx -i 15 -c 4

### Etapa 3

Crie um teste de integridade.

	azure network lb rule create -g nrprg -l nrplb -n HTTP -p tcp -f 80 -b 80

### Etapa 4

Verifique suas configurações.

	azure network lb show nrprg nrplb

Saída esperada:

	info:    Executing command network lb show
	+ Looking up the load balancer "nrplb"
	+ Looking up the public ip "NRPPublicIP"	
	data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb
	data:    Name                            : nrplb
	data:    Type                            : Microsoft.Network/loadBalancers
	data:    Location                        : eastus
	data:    Provisioning State              : Succeeded
	data:    Frontend IP configurations:
	data:      Name                          : NRPfrontendpool
	data:      Provisioning state            : Succeeded
	data:      Public IP address id          : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/publicIPAddresses/NRPPublicIP
	data:      Public IP allocation method   : Static
	data:      Public IP address             : 40.114.13.145
	data:
	data:    Backend address pools:
	data:      Name                          : NRPbackendpool
	data:      Provisioning state            : Succeeded
	data:
	data:    Load balancing rules:
	data:      Name                          : HTTP
	data:      Provisioning state            : Succeeded
	data:      Protocol                      : Tcp
	data:      Frontend port                 : 80
	data:      Backend port                  : 80
	data:      Enable floating IP            : false
	data:      Idle timeout in minutes       : 4
	data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
	data:      Backend address pool          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
	data:
	data:    Inbound NAT rules:
	data:      Name                          : rdp1
	data:      Provisioning state            : Succeeded
	data:      Protocol                      : Tcp
	data:      Frontend port                 : 3441
	data:      Backend port                  : 3389
	data:      Enable floating IP            : false
	data:      Idle timeout in minutes       : 4
	data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
	data:
	data:      Name                          : rdp2
	data:      Provisioning state            : Succeeded
	data:      Protocol                      : Tcp
	data:      Frontend port                 : 3442
	data:      Backend port                  : 3389
	data:      Enable floating IP            : false
	data:      Idle timeout in minutes       : 4
	data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
	data:
	data:    Probes:
	data:      Name                          : healthprobe
	data:      Provisioning state            : Succeeded
	data:      Protocol                      : Http
	data:      Port                          : 80
	data:      Interval in seconds           : 15
	data:      Number of probes              : 4
	data:
	info:    network lb show command OK

## Criar NICs

Você precisa criar NICs (ou modificar as existentes) e associá-las a regras NAT, regras do balanceador de carga e testes.

### Etapa 1 

Crie uma NIC chamada *lb-nic1-be* e associe-a à regra NAT *rdp1* e ao pool de endereços de back-end *NRPbackendpool*.
	
	azure network nic create -g nrprg -n lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" eastus

Parâmetros:

- **-g** - nome do grupo de recursos
- **-n** - nome do recurso NIC
- **--subnet-name** - nome da sub-rede 
- **--subnet-vnet-name** - nome da rede virtual
- **-d** - ID do recurso de pool back-end - começa com /subscription/{subscriptionID/resourcegroups/<resourcegroup-name>/providers/Microsoft.Network/loadbalancers/<load-balancer-name>/backendaddresspools/<name-of-the-backend-pool> 
- **-e** - ID da regra NAT que será associada ao recurso NIC - começa com /subscriptions/####################################/resourceGroups/<resourcegroup-name>/providers/Microsoft.Network/loadBalancers/<load-balancer-name>/inboundNatRules/<nat-rule-name>


Saída esperada:

	info:    Executing command network nic create
	+ Looking up the network interface "lb-nic1-be"
	+ Looking up the subnet "nrpvnetsubnet"
	+ Creating network interface "lb-nic1-be"
	+ Looking up the network interface "lb-nic1-be"
	data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
	data:    Name                            : lb-nic1-be
	data:    Type                            : Microsoft.Network/networkInterfaces
	data:    Location                        : eastus
	data:    Provisioning state              : Succeeded
	data:    Enable IP forwarding            : false
	data:    IP configurations:
	data:      Name                          : NIC-config
	data:      Provisioning state            : Succeeded
	data:      Private IP address            : 10.0.0.4
	data:      Private IP Allocation Method  : Dynamic
	data:      Subnet                        : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet
	data:      Load balancer backend address pools
	data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
	data:      Load balancer inbound NAT rules:
	data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1
	data:
	info:    network nic create command OK

### Etapa 2

Crie uma NIC chamada *lb-nic2-be* e associe-a à regra NAT *rdp2* e ao pool de endereços de back-end *NRPbackendpool*.

 	azure network nic create -g nrprg -n lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" eastus

### Etapa 3 

Crie uma máquina virtual (VM) chamada *web1* e associe-a ao NIC chamado *lb-nic1-be*. Uma conta de armazenamento chamada *web1nrp* foi criada antes da execução do comando a seguir.

	azure vm create --resource-group nrprg --name web1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

>[AZURE.IMPORTANT]As VMs em um balanceador de carga precisam estar no mesmo conjunto de disponibilidade. Use `azure availset create` para criar um conjunto de disponibilidade.

A saída será a seguinte:

	info:    Executing command vm create
	+ Looking up the VM "web1"
	Enter username: azureuser
	Enter password for azureuser: *********
	Confirm password: *********
	info:    Using the VM Size "Standard_A1"
	info:    The [OS, Data] Disk or image configuration requires storage account
	+ Looking up the storage account web1nrp
	+ Looking up the availability set "nrp-avset"
	info:    Found an Availability set "nrp-avset"
	+ Looking up the NIC "lb-nic1-be"
	info:    Found an existing NIC "lb-nic1-be"
	info:    Found an IP configuration with virtual network subnet id "/subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet" in the NIC "lb-nic1-be"
	info:    This is a NIC without publicIP configured
	+ Creating VM "web1"
	info:    vm create command OK

>[AZURE.NOTE]A mensagem informativa **Esta é uma NIC sem IP público configurado** é esperada, pois a NIC criada para o balanceador de carga se conectará à Internet por meio do endereço IP público do balanceador de carga.

Como o NIC *lb-nic1-be* está associado à regra NAT *rdp1*, você pode se conectar à *web1* usando o RDP por meio da porta 3441 no balanceador de carga.

### Etapa 4

Crie uma máquina virtual (VM) chamada *web2* e a associe-a à NIC chamada *lb-nic2-be*. Uma conta de armazenamento chamada *web1nrp* foi criada antes da execução do comando a seguir.

	azure vm create --resource-group nrprg --name web2 --location eastus --vnet-	name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## Atualizar um balanceador de carga existente

Você pode adicionar regras referenciando um balanceador de carga existente. No exemplo a seguir, uma nova regra de balanceador de carga é adicionada a um balanceador de carga **NRPlb** existente

	azure network lb rule create -g nrprg -l nrplb -n lbrule2 -p tcp -f 8080 -b 8051 -t frontendnrppool -o NRPbackendpool

Parâmetros:

**-g** -nome do grupo de recursos<br> **-l** -nome do balanceador de carga<BR> **-n** - nome de regra do balanceador de carga<BR> **-p** -protocolo<BR> **-f** - porta de front-end<BR> **-b** - porta de back-end<BR> **-t** -nome do pool de front-end<BR> **-b** - nome do pool de back-end<BR>

## Excluir um balanceador de carga 


Para remover um balanceador de carga, use o comando a seguir

	azure network lb delete -g nrprg -n nrplb 

Em que **nrprg** é o grupo de recursos e **nrplb** é o nome do balanceador de carga.

## Próximas etapas

[Introdução à configuração de um balanceador de carga interno](load-balancer-internal-getstarted.md)

[Configurar um modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)

<!---HONumber=Nov15_HO3-->