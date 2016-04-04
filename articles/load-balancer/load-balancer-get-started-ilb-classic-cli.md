<properties 
   pageTitle="Criar um balanceador de carga interno usando a CLI do Azure no modelo de implantação clássico | Microsoft Azure"
   description="Saiba como criar um balanceador de carga interno no modelo de implantação clássico usando a CLI do Azure"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carolz"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="joaoma" />

# Introdução à criação de um balanceador de carga interno (clássico) usando a CLI do Azure

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](load-balancer-get-started-ilb-arm-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]


## Para criar um conjunto de balanceadores de carga internos para máquinas virtuais

Para criar um conjunto de balanceadores de carga internos e os servidores que enviarão o tráfego para ele, é necessário fazer o seguinte:

1. Crie uma instância do Balanceamento de Carga Interno que será o ponto de extremidade do tráfego de entrada a ser balanceado nos servidores de um conjunto de balanceamento de carga.

1. Adicione pontos de extremidade correspondentes às máquinas virtuais que receberão o tráfego de entrada.

1. Configure os servidores que enviarão o tráfego com a carga a ser balanceada para enviar o tráfego para o endereço VIP (IP Virtual) da instância do Balanceamento de Carga Interno.

## Passo a passo da criação de um balanceador de carga interno usando a CLI

Este guia mostra como criar um balanceador de carga interno com base no cenário acima.

1. Se você nunca usou a CLI do Azure, consulte [Instalar e configurar a CLI do Azure](../../articles/xplat-cli-install.md) e siga as instruções até o ponto em que você seleciona sua conta e assinatura do Azure.

2. Execute o comando **azure config mode** para alternar para o modo clássico, como mostrado abaixo.

		azure config mode asm

	Saída esperada:

		info:    New mode is asm


## Criar ponto de extremidade e conjunto de balanceadores de carga 

O cenário pressupõe a existência das máquinas virtuais “DB1” e “DB2” em um serviço de nuvem chamado “mytestcloud”. As duas máquinas virtuais estão usando uma rede virtual chamada minha "testvnet" com a sub-rede "subnet-1".

Este guia criará um conjunto de balanceadores de carga internos usando a porta 1433 como a porta pública e 1433 como a porta local.

Esse é um cenário comum em que você tem máquinas virtuais do SQL no back-end usando um balanceador de carga interno para garantir que os servidores de banco de dados não sejam expostos diretamente usando um endereço IP público.


### Etapa 1 

Crie um conjunto do balanceador de carga interno usando o `azure network service internal-load-balancer add`.

	 azure service internal-load-balancer add -r mytestcloud -n ilbset -t subnet-1 -a 192.168.2.7

Parâmetros usados:

**-r** - nome do serviço de nuvem<BR> **-n** - nome do balanceador de carga interno<BR> **-t** - nome da sub-rede (a mesma sub-rede pelas máquinas virtuais que você adicionará ao balanceador de carga interno)<BR> **-a** - (opcional) adicionar um endereço IP privado estático<BR>

Confira `azure service internal-load-balancer --help` para obter mais informações.
 
É possível verificar as propriedades do balanceador de carga interno usando o comando `azure service internal-load-balancer list` *nome do serviço de nuvem*.

Apresentamos a seguir um exemplo da saída:

	azure service internal-load-balancer list my-testcloud
	info:    Executing command service internal-load-balancer list
	+ Getting cloud service deployment
	data:    Name    Type     SubnetName  StaticVirtualNetworkIPAddress
	data:    ------  -------  ----------  -----------------------------
	data:    ilbset  Private  subnet-1    192.168.2.7
	info:    service internal-load-balancer list command OK


## Etapa 2 

Você configura o conjunto de balanceadores de carga internos quando adicionar o primeiro ponto de extremidade. Você associará o ponto de extremidade, a máquina virtual e a porta de investigação ao conjunto de balanceador de carga interno nesta etapa.

	azure vm endpoint create db1 1433 -k 1433 tcp -t 1433 -r tcp -e 300 -f 600 -i ilbset

Parâmetros usados:

**-k** - porta da máquina virtual local<BR> **-t** - porta de investigação<BR> **-r** - protocolo de investigação<BR> **-e** - intervalo de investigação em segundos<BR> **-f** - intervalo de tempo limite em segundos <BR> **-i** - nome do balanceador de carga interno <BR>


## Etapa 3 

Verifique a configuração do balanceador de carga usando o *nome da máquina virtual* do `azure vm show`

	azure vm show DB1 

A saída será:

		azure vm show DB1
	info:    Executing command vm show
	+ Getting virtual machines
	data:    DNSName "mytestcloud.cloudapp.net"
	data:    Location "East US 2"
	data:    VMName "DB1"
	data:    IPAddress "192.168.2.4"
	data:    InstanceStatus "ReadyRole"
	data:    InstanceSize "Standard_D1"
	data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
	data:    OSDisk hostCaching "ReadWrite"
	data:    OSDisk name "db1-DB1-0-201511120457370846"
	data:    OSDisk mediaLink "https://XXXX.blob.core.windows.net/vhd"
	data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
	data:    OSDisk operatingSystem "Windows"
	data:    OSDisk iOType "Standard"
	data:    ReservedIPName ""
	data:    VirtualIPAddresses 0 address "137.116.64.107"
	data:    VirtualIPAddresses 0 name "db1ContractContract"
	data:    VirtualIPAddresses 0 isDnsProgrammed true
	data:    VirtualIPAddresses 1 address "192.168.2.7"
	data:    VirtualIPAddresses 1 name "ilbset"
	data:    Network Endpoints 0 localPort 5986
	data:    Network Endpoints 0 name "PowerShell"
	data:    Network Endpoints 0 port 5986
	data:    Network Endpoints 0 protocol "tcp"
	data:    Network Endpoints 0 virtualIPAddress "137.116.64.107"	
	data:    Network Endpoints 0 enableDirectServerReturn false
	data:    Network Endpoints 1 localPort 3389
	data:    Network Endpoints 1 name "Remote Desktop"
	data:    Network Endpoints 1 port 60173
	data:    Network Endpoints 1 protocol "tcp"
	data:    Network Endpoints 1 virtualIPAddress "137.116.64.107"
	data:    Network Endpoints 1 enableDirectServerReturn false
	data:    Network Endpoints 2 localPort 1433
	data:    Network Endpoints 2 name "tcp-1433-1433"
	data:    Network Endpoints 2 port 1433
	data:    Network Endpoints 2 loadBalancerProbe port 1433
	data:    Network Endpoints 2 loadBalancerProbe protocol "tcp"
	data:    Network Endpoints 2 loadBalancerProbe intervalInSeconds 300
	data:    Network Endpoints 2 loadBalancerProbe timeoutInSeconds 600
	data:    Network Endpoints 2 protocol "tcp"
	data:    Network Endpoints 2 virtualIPAddress "192.168.2.7"
	data:    Network Endpoints 2 enableDirectServerReturn false
	data:    Network Endpoints 2 loadBalancerName "ilbset"
	info:    vm show command OK


## Criar um ponto de extremidade da área de trabalho remota para uma máquina virtual

Você pode criar um ponto de extremidade da área de trabalho remota para encaminhar o tráfego de rede de uma porta pública para uma porta local, para uma máquina virtual específica, usando `azure vm endpoint create`.

	azure vm endpoint create web1 54580 -k 3389 


## Remover máquina virtual do balanceador de carga

É possível remover uma máquina virtual de um conjunto de balanceadores de carga internos excluindo o ponto de extremidade associado. Depois que o ponto de extremidade for removido, a máquina virtual não pertencerá mais ao conjunto de balanceadores de carga.

 Usando o exemplo acima, você pode remover o ponto de extremidade criado para a máquina virtual “DB1” do balanceador de carga interno usando o comando “lbset” `azure vm endpoint delete`.

	azure vm endpoint delete DB1 tcp-1433-1433


Confira `azure vm endpoint --help` para obter mais informações.


## Próximas etapas

[Configurar um modo de distribuição do balanceador de carga usando a afinidade de IP de origem](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0323_2016-->