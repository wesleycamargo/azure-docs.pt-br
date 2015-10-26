<properties 
   pageTitle="Gerenciar: modo de distribuição do balanceador de carga (afinidade de IP de origem)"
   description="Recursos de gerenciamento para o modo de distribuição do balanceador de carga do Azure" 
   services="virtual-network" 
   documentationCenter="" 
   authors="telmosampaio" 
   manager="carolz" 
   editor=""
   />

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/21/2015"
   ms.author="telmos"
   />
   
# Gerenciar a rede virtual: modo de distribuição do balanceador de carga (afinidade de IP de origem)
**Afinidade de IP de origem** (também conhecida como **afinidade de sessão** ou **afinidade IP do cliente**), um modo de distribuição do balanceador de carga do Azure, une conexões de um único cliente a um único servidor hospedado do Azure, em vez de distribuir cada conexão do cliente dinamicamente para diferentes servidores hospedados do Azure (o comportamento padrão do balanceador de carga).

Usando a afinidade de IP de origem, o balanceador de carga do Azure pode ser configurado para usar uma combinação de 2 tuplas (IP de origem, IP de destino) ou uma combinação de 3 tuplas (IP de origem, IP de destino, protocolo) para mapear o tráfego para o pool de servidores disponíveis hospedados do Azure. Ao usar a afinidade de IP de origem, as conexões iniciadas do mesmo computador cliente são tratadas por um único ponto de extremidade do DIP (um único servidor hospedado do Azure).

## Origem de serviço

A afinidade do IP de origem resolve uma [incompatibilidade anterior entre o balanceador de carga do Azure e o Gateway de Área de Trabalho Remota (DOC)](http://go.microsoft.com/fwlink/p/?LinkId=517389).

## Implementação

A afinidade de IP de origem podem ser configurada para:

* [Pontos de extremidade de máquina virtual](../virtual-machines/virtual-machines-set-up-endpoints.md)
* [Conjuntos de pontos de extremidade com balanceamento de carga](../load-balancer/load-balancer-overview.md)
* [Funções da Web](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)
* [Funções de trabalho](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)

## Cenários
1. Cluster de Gateway de Área de Trabalho Remota usando um único serviço de nuvem
2. Carregamento de mídia (ou seja, UDP para dados, TCP para controle)
  * O cliente inicia uma sessão TCP para os endereços IP públicos com balanceamento de carga hospedados do Azure
  * A solicitação do cliente é direcionada para um DIP pelo balanceador de carga; esse canal permanece ativo para monitorar a integridade da conexão
  * O cliente inicia uma sessão UDP para o mesmo endereço IP público com balanceamento de carga hospedado do Azure
  * O balanceador de carga do Azure direciona a solicitação para o mesmo ponto de extremidade DIP que a conexão TCP
  * O cliente carrega a mídia com maior taxa de transferência UDP, mantendo o canal de controle sobre TCP para confiabilidade
  
## Limitações
* Se o conjunto de balanceamento de carga for alterado (por exemplo, adicionando ou removendo uma máquina virtual), a distribuição de canal do cliente será recalculada; novas conexões de clientes existentes podem ser tratadas por um servidor diferente do que foi usado originalmente
* Usar a afinidade de IP de origem pode resultar em uma distribuição desigual do tráfego entre servidores hospedados do Azure
* Os clientes que encaminham o tráfego através de um proxy podem ser vistos como um único cliente pelo balanceador de carga do Azure

## Exemplos do PowerShell
Baixe [a versão mais recente do Azure PowerShell](https://github.com/Azure/azure-sdk-tools/releases) para obter melhores resultados.

### Adicione um ponto de extremidade do Azure a uma máquina virtual e defina o modo de distribuição do balanceador de carga

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 â€“LoadBalancerDistribution â€œsourceIPâ€�| Update-AzureVM  

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 Ã¢â‚¬â€œLoadBalancerDistribution Ã¢â‚¬Å“sourceIPÃ¢â‚¬ï¿½| Update-AzureVM  

LoadBalancerDistribution pode ser definido como sourceIP para balanceamento de carga de 2 tuplas (IP de origem, IP de destino), sourceIPProtocol para balanceamento de carga de 3 tuplas (IP de destino, IP de origem, protocolo) ou nenhum se você quiser o comportamento padrão (balanceamento de carga de 5 tuplas).

### Recupee uma configuração de modo de distribuição do balanceador de carga do ponto de extremidade
    PS C:\> Get-AzureVM â€“ServiceName "mySvc" -Name "MyVM1" | Get-AzureEndpoint
    
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

Se o elemento LoadBalancerDistribution não estiver presente, o balanceador de carga do Azure usa o algoritmo padrão de 5 tuplas

### Defina o modo de distribuição em um conjunto de pontos de extremidade com balanceamento de carga

    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 â€“LoadBalancerDistribution "sourceIP"

    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 Ã¢â‚¬â€œLoadBalancerDistribution "sourceIP"
    
Se os pontos de extremidade são parte de um conjunto de ponto de extremidade com balanceamento de carga, o modo de distribuição deve ser definido no conjunto de pontos de extremidade com balanceamento de carga.

## Exemplos de serviços de nuvem

Você pode aproveitar o SDK do Azure para .NET para atualizar seu serviço de nuvem

Configurações de ponto de extremidade para serviços de nuvem são feitas na. csdef. Para atualizar o modo de distribuição do balanceador de carga para uma implantação de serviços de nuvem, é necessária uma atualização da implantação.

Aqui está um exemplo de alterações .csdef para configurações do ponto de extremidade:

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
    
## Exemplos de API

Os desenvolvedores podem configurar a distribuição do balanceador de carga usando a API de gerenciamento de serviço. Certifique-se de adicionar o cabeçalho x-ms-version definido para a versão 2014-09-01 ou superior.

### Atualize a configuração do conjunto de balanceamento de carga especificado em uma implantação

#### Solicitação

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

#### Resposta

    HTTP/1.1 202 Accepted 
    Cache-Control: no-cache 
    Content-Length: 0 
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0 
    x-ms-servedbyregion: ussouth2 
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af 
    Date: Thu, 16 Oct 2014 22:49:21 GMT
 

<!---HONumber=Oct15_HO3-->