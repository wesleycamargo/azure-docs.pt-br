<properties 
   pageTitle="Configurar modo de distribuição do Balanceador de Carga | Microsoft Azure"
   description="Como configurar o modo de distribuição do balanceador de carga do Azure para dar suporte à afinidade de IP de origem"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/05/2016"
   ms.author="sewhee" />


# Modo de distribuição do balanceador de carga (afinidade do IP de origem)

Apresentamos um novo modo de distribuição chamado Afinidade de IP de origem (também conhecido como afinidade de sessão ou afinidade de IP do cliente). O Balanceador de Carga do Azure pode ser configurado para usar uma tupla 2 (IP de origem, IP de destino) ou tupla 3 ( IP de origem, IP de destino, Protocolo) para mapear o tráfego até os servidores disponíveis. Ao usar a afinidade de IP de origem, as conexões iniciadas no mesmo computador cliente vão para o mesmo ponto de extremidade DIP.

![balanceador de carga baseado em hash](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

A afinidade de IP de origem resolve uma incompatibilidade entre o Balanceador de Carga do Azure e o Gateway de Área de Trabalho Remota. Agora você pode compilar um farm de gateway de área de trabalho remota em um único serviço de nuvem. Outro cenário de uso é o carregamento de mídia em que o carregamento de dados reais ocorre por meio de UDP, mas no qual o plano de controle é obtido por meio de TCP:

- Primeiro, um cliente inicia uma sessão TCP com o endereço público do balanceamento de carga e é direcionado para um DIP específico; esse canal permanece ativo para monitorar a integridade da conexão
- Uma nova sessão UDP no mesmo computador cliente é iniciada para o mesmo ponto de extremidade público de balanceamento de carga. A expectativa aqui é que essa conexão seja também direcionada para o mesmo ponto de extremidade DIP que a conexão TCP anterior, para que o carregamento da mídia possa ser executado em alta taxa de transferência enquanto mantém um canal de controle pelo TCP.
 
Observe que, se o conjunto de balanceamento de carga for alterado (removendo ou adicionando uma máquina virtual), a distribuição de solicitações de cliente será recalculada. Você não pode depender de novas conexões de sessões de cliente existentes que terminam no mesmo servidor. Além disso, o uso do modo de distribuição de afinidade do IP de origem pode causar uma distribuição desigual de tráfego. Clientes que executam proxies subjacentes podem ser vistos como um aplicativo cliente exclusivo.

O algoritmo de distribuição usado é um hash de tupla 5 (IP de origem, porta de origem, IP de destino, porta de destino, tipo de protocolo) para mapear o tráfego até os servidores disponíveis. Ele fornece permanência somente dentro de uma sessão de transporte. Os pacotes na mesma sessão TCP ou UDP serão direcionados para a mesma instância do DIP (IP de Datacenter), atrás do ponto de extremidade de balanceamento de carga. Quando o cliente fecha e reabre a conexão ou inicia uma nova sessão por meio do mesmo IP de origem, a porta de origem é alterada e faz com que o tráfego vá para um ponto de extremidade DIP diferente.

![balanceador de carga baseado em hash](./media/load-balancer-distribution-mode/load-balancer-distribution.png)


## Definindo configurações de afinidade de IP de origem para o balanceador de carga
 
Para máquinas virtuais, você pode usar o PowerShell para alterar as configurações de tempo limite:
 
Adicione um ponto de extremidade do Azure a uma máquina virtual e defina o modo de distribuição do balanceador de carga

	Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM

>[AZURE.NOTE] LoadBalancerDistribution pode ser definido como sourceIP para balanceamento de carga de 2 tuplas (IP de origem, IP de destino), sourceIPProtocol para balanceamento de carga de 3 tuplas (IP de destino, IP de origem, protocolo) ou nenhum se quiser o comportamento padrão de balanceamento de carga de 5 tuplas


Recupee uma configuração de modo de distribuição do balanceador de carga do ponto de extremidade

	PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

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
	LoadBalancerDistribution : sourceIP
 
Se o elemento LoadBalancerDistribution não estiver presente, o balanceador de carga do Azure usará o algoritmo padrão de tupla 5.

 
### Defina o modo de distribuição em um conjunto de pontos de extremidade com balanceamento de carga

Se os pontos de extremidade forem parte de um conjunto de pontos de extremidade de balanceamento de carga, o modo de distribuição deverá ser definido no conjunto de pontos de extremidade de balanceamento de carga:

	Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocol TCP -ProbePort 8080 –LoadBalancerDistribution sourceIP

### Configuração de serviço de nuvem para alterar o modo de distribuição

Você pode aproveitar o SDK do Azure para .NET 2.5 (a ser lançado em novembro) para atualizar as configurações do ponto de extremidade de serviço de nuvem para os Serviços de Nuvem feitos no .csdef. Para atualizar o modo de distribuição do balanceador de carga para uma implantação de serviços de nuvem, é necessária uma atualização da implantação. Aqui está um exemplo de alterações .csdef para configurações do ponto de extremidade:

	<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
  	<Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
  	</Endpoints>
	</WorkerRole>
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


## Exemplo de API

Você pode configurar a distribuição do balanceador de carga usando a API de gerenciamento de serviços. Certifique-se de adicionar o cabeçalho `x-ms-version`, definido para a versão de `2014-09-01` ou posterior.
 
Atualize a configuração do conjunto de balanceamento de carga especificado em uma implantação

Exemplo de solicitação

	POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet 

	x-ms-version: 2014-09-01 

	Content-Type: application/xml 

	<LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance"> 
	<InputEndpoint> 
	<LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName> 
	<LocalPort> local-port-number </LocalPort> 
	<Port> external-port-number </Port> 
	<LoadBalancerProbe> 
	<Port> port-assigned-to-probe </Port> 
	<Protocol> probe-protocol </Protocol> 
	<IntervalInSeconds> interval-of-probe </IntervalInSeconds> 
	<TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds> 
	</LoadBalancerProbe> 
	<Protocol> endpoint-protocol </Protocol> 
	<EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn> 
	<IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes> 
	<LoadBalancerDistribution>sourceIP</LoadBalancerDistribution> 
	</InputEndpoint> 
	</LoadBalancedEndpointList>

O valor de LoadBalancerDistribution pode ser sourceIP para afinidade de 2 tuplas, sourceIPProtocol para afinidade de 3 tuplas ou nenhum (para nenhuma afinidade, por exemplo, 5 tuplas)

	Response

	HTTP/1.1 202 Accepted 
	Cache-Control: no-cache 
	Content-Length: 0 
	Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0 
	x-ms-servedbyregion: ussouth2 
	x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af 
	Date: Thu, 16 Oct 2014 22:49:21 GMT

## Próximas etapas

[Visão geral do balanceador de carga interno](load-balancer-internal-overview.md)

[Introdução à configuração de um balanceador de carga para a Internet](load-balancer-get-started-internet-arm-ps.md)

[Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0824_2016-->