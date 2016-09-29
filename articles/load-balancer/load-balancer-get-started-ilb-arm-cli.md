<properties
   pageTitle="Criar um balanceador de carga interno usando a CLI do Azure no Gerenciador de Recursos | Microsoft Azure"
   description="Saiba como criar um balanceador de carga interno no Gerenciador de Recursos usando a CLI do Azure"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
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
   ms.date="08/31/2016"
   ms.author="sewhee" />

# Introdução à criação de um balanceador de carga interno usando a CLI do Azure

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [modelo de implantação clássico](load-balancer-get-started-ilb-classic-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## Implantar a solução usando a CLI do Azure

As etapas a seguir mostram como criar um balanceador de carga para a Internet usando o Azure Resource Manager com a CLI. Com o Azure Resource Manager, todos os recursos são criados e configurados individualmente e, em seguida, colocados juntos para criar um recurso.

Você precisa criar e configurar os seguintes objetos para implantar um balanceador de carga:

- Configuração de IP de front-end – contém endereços IP públicos para o tráfego de rede de entrada.
- Pool de endereços de back-end – contém NICs (interfaces de rede) para que as máquinas virtuais recebam o tráfego de rede do balanceador de carga.
- Regras de balanceamento de carga – contém regras de mapeamento de uma porta pública no balanceador de carga para uma porta no pool de endereços de back-end.
- Regras NAT de entrada – contém regras de mapeamento de uma porta pública no balanceador de carga para uma porta de uma máquina virtual específica no pool de endereços de back-end.
- Investigações – contém investigações de integridade usadas para verificar a disponibilidade de instâncias de máquinas virtuais no pool de endereços de back-end.

Para obter mais informações, confira [Suporte do Azure Resource Manager para balanceador de carga](load-balancer-arm.md).

## Configurar a CLI para usar o Gerenciador de Recursos

1. Se você nunca usou a CLI do Azure, consulte [Instalar e configurar a CLI do Azure](../../articles/xplat-cli-install.md) e siga as instruções até o ponto em que você seleciona sua conta e assinatura do Azure.

2. Execute o comando **azure config mode** para alternar para o modo do Gerenciador de Recursos, como mostrado abaixo.

        azure config mode arm

    Saída esperada:

        info:    New mode is arm

## Passo a passo da criação de um balanceador de carga interno

1. Entrar no Azure

        azure login

    Inserir as credenciais do Azure quando solicitado.

2. Altere as ferramentas de comando para o modo do gerenciador de recursos do Azure.

    arm do modo de configuração do Azure

## Criar um grupos de recursos

Todos os recursos do gerenciador de recursos do Azure estão associados a um grupo de recursos. Se você ainda não fez isso, crie um grupo de recursos.

    azure group create <resource group name> <location>

## Criar um conjunto do balanceador de carga interno

1. Criar um balanceador de carga interno

    No cenário a seguir, é criado um grupo de recursos chamado nrprg na região Leste dos EUA.

        azure network lb create -n nrprg -l eastus

    >[AZURE.NOTE] todos os recursos de um balanceador de carga interno, como uma rede virtual e sub-rede da rede virtual, devem estar no mesmo grupo de recursos e na mesma região.

2. Crie um endereço IP de front-end para o balanceador de carga interno.

    O endereço IP usado deve estar dentro do intervalo da sub-rede de sua rede virtual.

        azure network lb frontend-ip create -g nrprg -l ilbset -n feilb -a 10.0.0.7 -e nrpvnetsubnet -m nrpvnet

    Parâmetros usados:

    * **-g** – grupo de recursos
    * **-l** – nome do conjunto do balanceador de carga interno
    * **- n** – nome do IP de front-end
    * **-** – endereço IP privado no intervalo de sub-rede.
    * **-e** – nome da sub-rede
    * **-m** – nome da rede virtual

3. Crie o pool de endereços de back-end.

        azure network lb address-pool create -g nrprg -l ilbset -n beilb

    Parâmetros usados:

    * **-g** – grupo de recursos
    * **-l** – nome do conjunto do balanceador de carga interno
    * **-n** – nome do pool de endereços de back-end

    Depois de definir um endereço IP de front-end e um pool de endereços de back-end, você poderá criar regras de balanceador de carga, regras NAT de entrada e personalizar as investigações de integridade.

4. Crie uma regra do balanceador de carga para o balanceador de carga interno.

    Seguindo o cenário acima, o comando cria uma regra do balanceador de carga que escuta a porta 1433 no pool de front-end e envia o tráfego de rede com carga balanceada ao pool de endereços de back-end também usando a porta 1433.

        azure network lb rule create -g nrprg -l ilbset -n ilbrule -p tcp -f 1433 -b 1433 -t feilb -o beilb

    Parâmetros usados:

    * **-g** – grupo de recursos
    * **-l** – nome do conjunto do balanceador de carga interno
    * **-n** – nome da regra do balanceador de carga
    * **-p** – protocolo usado para a regra
    * **-f** – porta que está escutando o tráfego de rede de entrada no front-end do balanceador de carga
    * **-b** – porta de recebimento do tráfego de rede no pool de endereços de back-end

5. Crie regras NAT de entrada.

    As regras NAT de entrada são usadas para criar pontos de extremidade em um balanceador de carga, que são destinados a uma instância específica de máquina virtual. Seguindo o exemplo acima, duas regras NAT foram criadas para o acesso de área de trabalho remota.

        azure network lb inbound-nat-rule create -g nrprg -l ilbset -n NATrule1 -p TCP -f 5432 -b 3389

        azure network lb inbound-nat-rule create -g nrprg -l ilbset -n NATrule2 -p TCP -f 5433 -b 3389

    Parâmetros usados:

    * **-g** – grupo de recursos
    * **-l** – nome do conjunto do balanceador de carga interno
    * **- n** – nome da regra NAT de entrada
    * **-p** – protocolo usado para a regra
    * **-f** – porta que está escutando o tráfego de rede de entrada no front-end do balanceador de carga
    * **-b** – porta de recebimento do tráfego de rede no pool de endereços de back-end

5. Crie investigações de integridade para o balanceador de carga.

    Uma investigação de integridade verifica todas as instâncias da máquina virtual para se certificar de que ela pode enviar o tráfego de rede. A instância de máquina virtual com verificações de investigação com falha é removida do balanceador de carga até ele ficar online novamente e as verificações de investigação se tornarem íntegras.

        azure network lb probe create -g nrprg -l ilbset -n ilbprobe -p tcp -i 300 -c 4

    Parâmetros usados:

    * **-g** – grupo de recursos
    * **-l** – nome do conjunto do balanceador de carga interno
    * **- n** – nome da investigação de integridade
    * **-p** – protocolo usado pela investigação de integridade
    * **-i** – intervalo de investigação em segundos
    * **- c** – número de verificações

    >[AZURE.NOTE] A plataforma Microsoft Azure usa um endereço IPv4 estático e publicamente roteável para uma variedade de cenários administrativos. O endereço IP é 168.63.129.16. Esse endereço IP não deve ser bloqueado por nenhum firewall porque ele pode causar um comportamento inesperado. Em relação ao Balanceamento de Carga Interno do Azure, esse endereço IP é usado por testes de monitoramento do balanceador de carga para determinar o estado de integridade para máquinas virtuais em um conjunto com balanceamento de carga. Se um grupo de segurança de rede é usado para restringir o tráfego para máquinas virtuais do Azure em um conjunto com balanceamento de carga interno, ou então é aplicado a uma Sub-rede de Rede Virtual, certifique-se de que uma regra de segurança de rede seja adicionada para permitir o tráfego em 168.63.129.16.

## Criar NICs

Você precisa criar NICs (ou modificar as existentes) e associá-las a regras NAT, regras do balanceador de carga e testes.

1. Crie uma NIC chamada *lb-nic1-be* e a associe à regra NAT *rdp1* e ao pool de endereços de back-end *beilb*.

        azure network nic create -g nrprg -n lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" eastus

    Parâmetros:

    * **-g** - nome do grupo de recursos
    * **-n** - nome do recurso NIC
    * **--subnet-name** - nome da sub-rede
    * **--subnet-vnet-name** - nome da rede virtual
    * **-d** - ID do recurso de pool back-end - começa com /subscription/{subscriptionID/resourcegroups/<nome do grupo de recursos>/providers/Microsoft.Network/loadbalancers/<nome do balanceador de carga>/backendaddresspools/<nome do pool de back-end>
    * **-e** – a ID da regra NAT deve ser associada ao recurso NIC – começa com /subscriptions/####################################/resourceGroups/<nome do grupo de recursos>/providers/Microsoft.Network/loadBalancers/<nome do balanceador de carga>/inboundNatRules/<nome da regra NAT>

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

2. Crie uma NIC chamada *lb-nic2-be* e a associe à regra NAT *rdp2* e ao pool de endereços de back-end *beilb*.

        azure network nic create -g nrprg -n lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" eastus

3. Crie uma VM (máquina virtual) chamada *DB1* e a associe à NIC chamada *lb-nic1-be*. Uma conta de armazenamento chamada *web1nrp* foi criada antes da execução do comando a seguir.

        azure vm create --resource-group nrprg --name DB1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

    >[AZURE.IMPORTANT] As VMs em um balanceador de carga precisam estar no mesmo conjunto de disponibilidade. Use `azure availset create` para criar um conjunto de disponibilidade.

4. Crie uma VM (máquina virtual) chamada *DB2* e a associe à NIC chamada *lb-nic2-be*. Uma conta de armazenamento chamada *web1nrp* foi criada antes da execução do comando abaixo.

        azure vm create --resource-group nrprg --name DB2 --location eastus --vnet-    name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## Excluir um balanceador de carga

Para remover um balanceador de carga, use o comando a seguir

    azure network lb delete -g nrprg -n ilbset

Em que **nrprg** é o grupo de recursos e **ilbset** o nome do balanceador de carga interno.


## Próximas etapas

[Configurar um modo de distribuição do balanceador de carga usando a afinidade de IP de origem](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0914_2016-->