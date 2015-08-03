<properties 
   pageTitle="Configurar tempo limite de ociosidade do TCP do balanceador de carga | Microsoft Azure"
   description="Configurar tempo limite de ociosidade do TCP do balanceador de carga"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/01/2015"
   ms.author="joaoma" />

# Como alterar as configurações de tempo limite de ociosidade do TCP para o balanceador de carga

Em sua configuração padrão, o balanceador de carga do Azure tem uma configuração de “tempo limite de ociosidade” de 4 minutos.

Isso significa que, se você tiver um período de inatividade em suas sessões de TCP ou HTTP superior ao valor do tempo limite, não há garantia de que a conexão seja mantida entre o cliente e o serviço.

Quando a conexão é fechada, o aplicativo cliente recebe uma mensagem de erro semelhante a “A conexão subjacente foi fechada: uma conexão que deveria ser mantida ativa foi fechada pelo servidor”.

Uma prática comum para manter a conexão ativa por um período maior é usar TCP Keep Alive (você pode encontrar exemplos do .NET [aqui](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx)).

Pacotes são enviados quando nenhuma atividade é detectada na conexão. Mantendo a atividade de rede em andamento, o valor de tempo limite de ociosidade nunca é atingido e a conexão é mantida por um longo período.

A ideia é configurar o TCP Keep Alive com um intervalo menor do que a configuração de tempo limite padrão para evitar que a conexão caia, ou aumentar o valor do tempo limite de ociosidade para que a sessão de conexão do TCP permaneça ativa.

Enquanto o TCP Keep Alive funciona bem em cenários em que a bateria não é uma restrição, geralmente não é uma opção válida para aplicativos móveis. Usar TCP Keep Alive em um aplicativo móvel provavelmente consumirá a bateria do dispositivo mais rapidamente.

Para dar suporte a esses cenários, adicionamos suporte para tempo limite de ociosidade configurável. Agora você pode defini-lo para uma duração entre 4 e 30 minutos. Essa configuração só funciona para conexões de entrada.

![tcptimeout](./media/load-balancer-tcp-idle-timeout/image1.png)


## Como alterar as configurações de tempo limite de ociosidade em máquinas virtuais e serviços de nuvem

- Configure o tempo limite do TCP para um ponto de extremidade em uma máquina virtual por meio do PowerShell ou pela API de Gerenciamento de Serviços
- Configure o tempo limite do TCP para os conjuntos de ponto de extremidade de balanceamento de carga por meio do PowerShell ou pela API de Gerenciamento de Serviços.
- Configure o tempo limite do TCP para o IP Público em Nível de Instância
- Configure o tempo limite do TCP para suas funções Web/de trabalho por meio do modelo de serviço.
 

>[AZURE.NOTE]Lembre-se de que alguns comandos só existirão no pacote mais recente do PowerShell do Azure. Se o comando do PowerShell não existir, baixe um pacote mais recente do PowerShell.

 
### Configure o tempo limite do TCP para o IP Público em Nível de Instância para 15 minutos.

	Set-AzurePublicIP –PublicIPName webip –VM MyVM -IdleTimeoutInMinutes 15

IdleTimeoutInMinutes é opcional. Se não for definido, o tempo limite padrão é 4 minutos.

>[AZURE.NOTE]O intervalo de tempo limite aceitável é entre 4 e 30 minutos.
 
### Defina o tempo limite ocioso durante a criação de um ponto de extremidade do Azure em uma máquina Virtual

Para alterar a configuração de tempo limite para um ponto de extremidade

	Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM
 
Recupere a configuração de tempo limite de ociosidade

	PS C:> Get-AzureVM –ServiceName “MyService” –Name “MyVM” | Get-AzureEndpoint
	VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
	LBSetName : MyLoadBalancedSet
	LocalPort : 80
	Name : HTTP
	Port : 80
	Protocol : tcp
	Vip : 65.52.xxx.xxx
	ProbePath :
	ProbePort : 80
	ProbeProtocol : tcp
	ProbeIntervalInSeconds : 15
	ProbeTimeoutInSeconds : 31
	EnableDirectServerReturn : False
	Acl : {}
	InternalLoadBalancerName :
	IdleTimeoutInMinutes : 15
 
### Defina o tempo limite do TCP em um conjunto do ponto de extremidade com balanceamento de carga

Se os pontos de extremidade são parte de um conjunto o ponto de extremidade com balanceamento de carga, o tempo limite do TCP deve ser definido no conjunto de pontos de extremidade com balanceamento de carga:

	Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15
 
### Alterando configurações de tempo limite para serviços de nuvem

Você pode aproveitar o SDK do Azure para .NET 2.4 para atualizar seu serviço de nuvem.

Configurações de ponto de extremidade para serviços de nuvem são feitas na. csdef. Para atualizar o tempo limite do TCP para uma implantação de Serviços de Nuvem, é necessário uma atualização da implantação. Uma exceção é se o tempo limite do TCP é especificado somente para um IP público. As configurações de IP públicas estão em .cscfg e podem ser atualizados através da atualização e do upgrade da implantação.

As alterações do .csdef para as configurações do ponto de extremidade são:

	<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
	  <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
	  </Endpoints>
	</WorkerRole>

As alterações no .cscfg para a configuração de tempo limite em IPs públicos são:

	<NetworkConfiguration>
 	 <VirtualNetworkSite name="VNet"/>
 	 <AddressAssignments>
    <InstanceAddress roleName="VMRolePersisted">
      <PublicIPs>
        <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
      </PublicIPs>
    </InstanceAddress>
 	 </AddressAssignments>
	</NetworkConfiguration>

## Exemplo de API REST

Você pode configurar o tempo limite de ociosidade do TCP usando a API de gerenciamento de serviços. Certifique-se de adicionar o cabeçalho x-ms-version, definido para a versão 01-06-2014 ou posterior.
 
Atualize a configuração dos pontos de extremidade de entrada com balanceamento de carga especificada em todas as máquinas virtuais em uma implantação
	
	Request

	POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>
<BR>

	Response

	<LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
	<InputEndpoint>
	<LoadBalancedEndpointSetName>endpoint-set-name</LoadBalancedEndpointSetName>
	<LocalPort>local-port-number</LocalPort>
	<Port>external-port-number</Port>
	<LoadBalancerProbe>
	<Path>path-of-probe</Path>
	<Port>port-assigned-to-probe</Port>
	<Protocol>probe-protocol</Protocol>
	<IntervalInSeconds>interval-of-probe</IntervalInSeconds>
	<TimeoutInSeconds>timeout-for-probe</TimeoutInSeconds>
	</LoadBalancerProbe>
	<LoadBalancerName>name-of-internal-loadbalancer</LoadBalancerName>
	<Protocol>endpoint-protocol</Protocol>
	<IdleTimeoutInMinutes>15</IdleTimeoutInMinutes>
	<EnableDirectServerReturn>enable-direct-server-return</EnableDirectServerReturn>
	<EndpointACL>
	<Rules>
	<Rule>
	<Order>priority-of-the-rule</Order>
	<Action>permit-rule</Action>
	<RemoteSubnet>subnet-of-the-rule</RemoteSubnet>
	<Description>description-of-the-rule</Description>
	</Rule>
	</Rules>
	</EndpointACL>
	</InputEndpoint>
	</LoadBalancedEndpointList>

## Próximas etapas

[Visão geral do balanceador de carga interno](load-balancer-internal-overview.md)

[Introdução à configuração de um balanceador de carga para a Internet](load-balancer-internet-getstarted.md)

[Configurar um modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md)

 

<!---HONumber=July15_HO4-->