---
title: Configurar tempo limite de ociosidade do TCP do balanceador de carga | Microsoft Docs
description: Configurar tempo limite de ociosidade do TCP do balanceador de carga
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: ''

ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/03/2016
ms.author: sewhee

---
# Alterar as configurações de tempo limite de ociosidade do TCP para o Balanceador de Carga
Em sua configuração padrão, o Azure Load Balancer tem uma configuração de tempo limite de ociosidade de quatro minutos.

Isso significa que, se um período de inatividade for maior que o valor de tempo limite, não haverá nenhuma garantia de que a sessão TCP ou HTTP entre o cliente e o serviço de nuvem ainda existirá.

Quando a conexão é fechada, o aplicativo cliente recebe uma mensagem de erro semelhante a “A conexão subjacente foi fechada: uma conexão que deveria ser mantida ativa foi fechada pelo servidor”.

Uma prática comum para manter a conexão ativa por um período maior é usar o TCP keep alive. (Você pode encontrar [exemplos do .NET](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx).) Pacotes são enviados quando nenhuma atividade é detectada na conexão. Esta atividade de rede garante que o valor de tempo limite de ociosidade nunca será atingido e a conexão será mantida por um longo período.

Para evitar a perda da conexão, você deve configurar o TCP keep-alive com um intervalo menor do que a configuração de tempo limite de ociosidade ou aumentar o valor do tempo limite de ociosidade.

Embora um TCP keep-alive funcione bem em cenários nos quais uma bateria não seja uma restrição, ele geralmente não é recomendável para aplicativos móveis. Usar o TCP keep alive em um aplicativo móvel provavelmente consumirá a bateria do dispositivo mais rapidamente.

Para dar suporte a esses cenários, adicionamos suporte para um tempo limite de ociosidade configurável. Agora você pode defini-lo para uma duração entre quatro e 30 minutos. Essa configuração só funciona para conexões de entrada.

![Tempo limite de TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

As seções a seguir descrevem como alterar as configurações de tempo limite de ociosidade em máquinas virtuais e serviços de nuvem.

> [!NOTE]
> Para dar suporte para definir essas configurações, verifique se você instalou o pacote mais recente do Azure PowerShell.
> 
> 

## Configure o tempo limite de TCP para o IP Público em Nível de Instância para 15 minutos
    Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15

`IdleTimeoutInMinutes` é opcional. Se não for definido, o tempo limite padrão será de quatro minutos.

> [!NOTE]
> O intervalo de tempo limite aceitável é entre quatro e 30 minutos.
> 
> 

## Defina o tempo limite de ociosidade durante a criação de um ponto de extremidade do Azure em uma máquina virtual
Altere a configuração de tempo limite para um ponto de extremidade:

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM

Recupere a configuração do tempo limite de ociosidade:

    PS C:\> Get-AzureVM -ServiceName "MyService" -Name "MyVM" | Get-AzureEndpoint
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

## Defina o tempo limite do TCP em um conjunto do ponto de extremidade com balanceamento de carga
Se os pontos de extremidade forem parte de um conjunto de ponto de extremidade com balanceamento de carga, o tempo limite do TCP deverá ser definido no conjunto de pontos de extremidade com balanceamento de carga:

    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15

## Alterar as configurações de tempo limite para serviços de nuvem
Você pode usar o SDK do Azure para .NET 2.4 para atualizar seu serviço de nuvem.

Você pode fazer configurações de ponto de extremidade para serviços de nuvem no arquivo .csdef. A atualização do tempo limite do TCP para a implantação de um serviço de nuvem exige uma atualização da implantação. Uma exceção é se o tempo limite do TCP é especificado somente para um IP público. As configurações de IP público estão no arquivo .cscfg e podem ser atualizadas por intermédio da atualização e do upgrade da implantação.

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
Você pode configurar o tempo limite de ociosidade de TCP usando a API de Gerenciamento de Serviços. Certifique-se de que o cabeçalho x-ms-version esteja configurado para a versão 2014-06-01 ou superior.

Atualize a configuração dos pontos de extremidade de entrada com balanceamento de carga especificado em todas as máquinas virtuais em uma implantação.

    Request:

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>
<BR>

    Response:

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

[Introdução à configuração de um balanceador de carga para a Internet](load-balancer-get-started-internet-arm-ps.md)

[Configurar um modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md)

<!---HONumber=AcomDC_0914_2016-->