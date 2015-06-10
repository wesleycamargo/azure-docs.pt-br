<properties 
   authors="danielceckert" 
   documentationCenter="dev-center-name" 
   editor=""
   manager="jefco" 
   pageTitle="Gerenciar: tempo limite de ociosidade do balanceador de carga" 
   description="Recursos de gerenciamento para o tempo limite de ociosidade do balanceador de carga do Azure" 
   services="virtual-network" 
   />

<tags
   ms.author="danecke"
   ms.date="05/27/2015"
   ms.devlang="na"
   ms.service="virtual-network"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   />
   
# Gerenciar a rede virtual: tempo limite de ociosidade do TCP do balanceador de carga

**Tempo limite de ociosidade do TCP** permite que um desenvolvedor especifique um limite garantido por inatividade durante as sessões cliente-servidor que envolvem o balanceador de carga do Azure. Um valor de tempo limite de ociosidade de TCP de 4 minutos (o padrão para o balanceador de carga do Azure) significa que, se houver um período de inatividade que dura mais do que 4 minutos durante uma sessão de servidor e cliente envolvendo o balanceador de carga do Azure, a conexão será fechada.

Quando uma conexão cliente-servidor é fechada, o aplicativo cliente receberá uma mensagem de erro semelhante a “A conexão subjacente foi fechada: uma conexão que deveria ser mantida ativa foi fechada pelo servidor”.

[TCP Keep-Alive](http://tools.ietf.org/html/rfc1122#page-101) é uma prática comum para manter as conexões durante um longo período de tempo inativo [(exemplo da MSDN)](http://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Quando TCP Keep-Alive é usado, pacotes simples são enviados periodicamente por um cliente (normalmente com uma frequência menor do que o limite de tempo de ociosidade do servidor). O servidor considera essas transmissões como evidência de atividades de conexão mesmo quando nenhuma outra atividade ocorre – assim, o valor de tempo limite de ociosidade nunca é atendido e a conexão pode ser mantida por um longo período de tempo.

Enquanto o TCP Keep-Alive funcionar bem, geralmente não é uma opção para aplicativos móveis pois consome recursos de energia limitados em dispositivos móveis. Um aplicativo móvel que usa TCP Keep-Alive esgotará a bateria do dispositivo mais rapidamente uma vez que ele está continuamente consumindo energia pelo uso da rede.

Para dar suporte a cenários de dispositivo móvel, o balanceador de carga do Azure suporta um tempo limite de ociosidade do TCP configurável. Os desenvolvedores podem definir o tempo limite de ociosidade de TCP para qualquer duração entre 4 minutos e 30 minutos para conexões de entrada (o tempo limite de ociosidade do TCP configurável não se aplica a conexões de saída). Isso permite que os clientes mantenham uma sessão muito mais longa com um servidor com longos períodos de inatividade. Um aplicativo em um dispositivo móvel ainda pode optar por utilizar a técnica TCP Keep-Alive do TCP para preservar conexões que esperam períodos de mais de 30 minutos de inatividade, mas esse tempo lmiite de ociosidade do TCP permite que os aplicativos emitam solicitações TCP Keep-Alive com muito menos frequência do que antes, reduzindo significativamente a sobrecarga sobre os recursos de energia de dispositivos móveis.

## Implementação

O tempo limite de ociosidade do TCP pode ser configurado para:

* [IPs públicos em nível de instância](http://msdn.microsoft.com/library/azure/dn690118.aspx)
* [Conjuntos de pontos de extremidade com balanceamento de carga](http://msdn.microsoft.com/library/azure/dn655055.aspx)
* [Pontos de extremidade de máquina virtual](virtual-machines-set-up-endpoints.md)
* [Funções da Web](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)
* [Funções de trabalho](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)

## Próximas etapas
* TBD

## Exemplos do PowerShell
Baixe [a versão mais recente do Azure PowerShell](https://github.com/Azure/azure-sdk-tools/releases) para obter melhores resultados.

### Configure o tempo limite d TCP para o IP público em nível de instância para 15 minutos

    Set-AzurePublicIP –PublicIPName webip –VM MyVM -IdleTimeoutInMinutes 15

IdleTimeoutInMinutes é opcional. Se não for definido, o tempo limite padrão é 4 minutos. Agora seu valor pode ser definido entre 4 e 30 minutos.

### Defina o tempo limite ocioso durante a criação de um ponto de extremidade do Azure em uma máquina Virtual

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM

### Recupere a configuração de tempo limite de ociosidade

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

## Exemplos de serviços de nuvem

Você pode aproveitar o SDK do Azure para .NET para atualizar seu serviço de nuvem

Configurações de ponto de extremidade para serviços de nuvem são feitas na. csdef. Portanto, para atualizar o tempo limite do TCP para uma implantação de Serviços de Nuvem, uma atualização da implantação é necessária. Uma exceção é se o tempo limite do TCP é especificado somente para um IP público. As configurações de IP públicas estão em .cscfg e podem ser atualizados através da atualização e do upgrade da implantação.

As alterações do .csdef para as configurações do ponto de extremidade são:

    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
      </Endpoints>
    </WorkerRole>The .cscfg changes for the timeout setting on Public IPs are:
    
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

Os desenvolvedores podem configurar a distribuição do balanceador de carga usando a API de gerenciamento de serviço. Certifique-se de adicionar o cabeçalho x-ms-version definido para a versão 2014-06-01 ou superior.

### Atualize a configuração dos pontos de extremidade de entrada com balanceamento de carga especificada em todas as máquinas virtuais em uma implantação

#### Solicitação

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>

O valor de LoadBalancerDistribution pode ser sourceIP para afinidade de 2 tuplas, sourceIPProtocol para afinidade de 3 tuplas ou nenhum (para nenhuma afinidade, por exemplo, 5 tuplas)

#### Resposta

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

<!---HONumber=58-->