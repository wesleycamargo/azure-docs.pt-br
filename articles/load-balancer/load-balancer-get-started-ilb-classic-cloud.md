---
title: Criar um balanceador de carga interno para os serviços de nuvem no modelo de implantação clássico | Microsoft Docs
description: Saiba como criar um balanceador de carga interno no modelo de implantação clássico usando o PowerShell
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: ''
tags: azure-service-management

ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2016
ms.author: sewhee

---
# Introdução à criação de um balanceador de carga interno (clássico) para os serviços de nuvem
[!INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

<BR>

[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]

Saiba como [executar estas etapas usando o modelo do Resource Manager](load-balancer-get-started-ilb-arm-ps.md).

## Configurar o balanceador de carga interno para os serviços de nuvem
Há suporte para o balanceador de carga interno tanto para máquinas virtuais quanto para serviços de nuvem. Um ponto de extremidade de balanceador de carga interno criado no serviço de nuvem que está fora de uma rede virtual regional só estará acessível no serviço de nuvem.

A configuração do balancedor de carga interno deve ser definida durante a criação da primeira implantação no serviço de nuvem, conforme mostrado no exemplo abaixo.

> [!IMPORTANT]
> Um pré-requisito para executar as etapas a seguir é ter uma rede virtual já criada para a implantação em nuvem. Será necessário o nome de rede virtual e de sub-rede para criar o Balanceamento de Carga Interno.
> 
> 

### Etapa 1
Abra o arquivo de configuração de serviço (.cscfg) para a sua implantação de nuvem no Visual Studio e adicione a seção a seguir para criar o Balanceamento de Carga Interno no último item "`</Role>`" para a configuração de rede.

    <NetworkConfiguration>
      <LoadBalancers>
        <LoadBalancer name="name of the load balancer">
          <FrontendIPConfiguration type="private" subnet="subnet-name" staticVirtualNetworkIPAddress="static-IP-address"/>
        </LoadBalancer>
      </LoadBalancers>
    </NetworkConfiguration>


Vamos adicionar os valores para o arquivo de configuração de rede para mostrar como ele se parecerá. No exemplo, suponha que você criou uma sub-rede chamada "test\_vnet" com uma sub-rede 10.0.0.0/24 chamada test\_subnet e um endereço IP estático 10.0.0.4. O balanceador de carga será nomeado testLB.

    <NetworkConfiguration>
      <LoadBalancers>
        <LoadBalancer name="testLB">
          <FrontendIPConfiguration type="private" subnet="test_subnet" staticVirtualNetworkIPAddress="10.0.0.4"/>
        </LoadBalancer>
      </LoadBalancers>
    </NetworkConfiguration>

Para obter mais informações sobre o esquema do balanceador de carga, confira [Adicionar balanceador de carga](https://msdn.microsoft.com/library/azure/dn722411.aspx).

### Etapa 2
Altere o arquivo de definição de serviço (.csdef) para adicionar pontos de extremidade ao Balanceamento de Carga Interno. No momento em que uma instância de função é criada, o arquivo de definição de serviço adiciona as instâncias de função ao Balanceamento de Carga Interno.

    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancer="load-balancer-name" />
      </Endpoints>
    </WorkerRole>

Seguindo os mesmos valores do exemplo acima, vamos adicionar os valores ao arquivo de definição de serviço.

    <WorkerRole name=WorkerRole1" vmsize="A7" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="endpoint1" protocol="http" localPort="80" port="80" loadBalancer="testLB" />
      </Endpoints>
    </WorkerRole>

O tráfego de rede terá a carga equilibrada usando o balanceador de carga testLB com a porta 80 para solicitações de entrada, enviando a instâncias de função de trabalho também na porta 80.

## Próximas etapas
[Configurar um modo de distribuição do balanceador de carga usando a afinidade de IP de origem](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0824_2016-->