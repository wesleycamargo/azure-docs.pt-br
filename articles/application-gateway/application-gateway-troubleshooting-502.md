---
title: Solucionar problemas de erros de Gateway incorreto de Gateway de aplicativo do Azure (502)
description: Saiba como solucionar problemas de erros 502 do Gateway de Aplicativo
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: amsriva
ms.openlocfilehash: 2a1c7e480e896da6852949c9d765d17290e4e9ce
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64697157"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Solução de problemas de erros de gateway incorreto no Application Gateway

Saiba como solucionar problemas de erros de gateway inválido (502) recebidos ao usar o Gateway de aplicativo do Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Visão geral

Depois de configurar um gateway de aplicativo, um dos erros que você pode ver é "Erro de servidor: 502 - o servidor Web recebeu uma resposta inválida ao atuar como gateway ou servidor proxy". Este erro pode ocorrer pelos seguintes motivos principais:

* NSG, UDR ou DNS personalizado está bloqueando o acesso aos membros do pool de back-end.
* VMs de back-end ou instâncias do conjunto de dimensionamento de máquinas virtuais não estão respondendo à investigação de integridade padrão.
* Configuração inválida ou incorreta de investigações de integridade personalizadas.
* Do Gateway de aplicativo do Azure [pool de back-end não está configurado ou está vazio](#empty-backendaddresspool).
* Nenhuma das VMs ou instâncias no [conjunto de dimensionamento de máquinas virtuais é íntegra](#unhealthy-instances-in-backendaddresspool).
* [Tempo limite de solicitação ou problemas de conectividade](#request-time-out) com solicitações de usuário.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Problema com o Grupo de Segurança de Rede, com a Rota definida pelo usuário ou com o DNS personalizado

### <a name="cause"></a>Causa

Se o acesso no back-end está bloqueado devido a um NSG, UDR ou DNS personalizado, instâncias de gateway de aplicativo não é possível acessar o pool de back-end. Isso faz com que as falhas de investigação, resultando em 502 erros.

O NSG/UDR poderia estar presente na sub-rede do gateway de aplicativo ou a sub-rede em que as VMs de aplicativo são implantadas.

Da mesma forma, a presença de um DNS personalizado na VNet também poderá causar problemas. Um FQDN utilizado para membros do pool de back-end pode não resolver corretamente pelo servidor DNS configurado pelo usuário para a rede virtual.

### <a name="solution"></a>Solução

Valide a configuração de DNS, UDR e NSG realizando as seguintes etapas:

* Verifique os NSGs associados com a sub-rede de gateway de aplicativo. Certifique-se de que a comunicação com o back-end não está bloqueada.
* Verifique a que UDR associada com a sub-rede de gateway de aplicativo. Certifique-se de que a UDR não está direcionando o tráfego para fora da sub-rede de back-end. Por exemplo, verificar o roteamento para soluções de virtualização ou as rotas padrão que está sendo anunciadas para a sub-rede de gateway de aplicativo por meio de ExpressRoute/VPN de rede.

```azurepowershell
$vnet = Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Verifique se o NSG e a rota com a VM de back-end estão funcionando corretamente

```azurepowershell
Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* Verifique se há um DNS personalizado na VNet. O DNS pode ser verificado examinando os detalhes das propriedades da VNet na saída.

```json
Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
Se estiver presente, certifique-se de que o servidor DNS pode resolver o FQDN do membro do pool de back-end corretamente.

## <a name="problems-with-default-health-probe"></a>Problemas com a investigação de integridade padrão

### <a name="cause"></a>Causa

502 erros também podem ser indicadores frequentes de que a investigação de integridade padrão não é possível acessar as VMs de back-end.

Quando uma instância do gateway de aplicativo é provisionada, ele configura automaticamente uma investigação de integridade padrão para cada BackendAddressPool usando as propriedades de BackendHttpSetting. Nenhuma entrada do usuário é necessária para definir essa investigação. Especificamente, quando uma regra de balanceamento de carga é configurada, uma associação é feita entre um BackendHttpSetting e um BackendAddressPool. Uma investigação padrão é configurada para cada um dessas associações e o gateway de aplicativo inicia uma conexão de verificação de integridade periódicas para cada instância em BackendAddressPool na porta especificada no elemento BackendHttpSetting. 

A tabela a seguir lista os valores associados com a investigação de integridade padrão:

| Propriedades da investigação | Value | DESCRIÇÃO |
| --- | --- | --- |
| URL de investigação |`http://127.0.0.1/` |Caminho da URL |
| Interval |30 |Intervalo da investigação em segundos |
| Tempo limite |30 |Tempo limite da investigação em segundos |
| Limite não íntegro |3 |Contagem de repetições da investigação. O servidor de back-end é marcado após a contagem de falhas de investigação consecutivas atingir o limite de não íntegro. |

### <a name="solution"></a>Solução

* Verifique se um site padrão está configurado e está escutando em 127.0.0.1.
* Se BackendHttpSetting especificar uma porta diferente de 80, o site padrão deverá ser configurado para escutar nessa porta.
* A chamada para `http://127.0.0.1:port` deve retornar um código de resultado de HTTP 200. Isso deve ser retornado dentro do período de tempo limite de 30 segundos.
* Certifique-se de que a porta configurada está aberta e que não há regras de firewall ou grupos de segurança de rede do Azure, que bloqueiam o tráfego de entrada ou saído na porta configurada.
* Se o serviço de nuvem ou VMs clássicas do Azure é usado com um FQDN ou um IP público, certifique-se de que correspondente [ponto de extremidade](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) é aberto.
* Se a VM é configurada por meio do Azure Resource Manager e está fora da VNet em que o gateway de aplicativo é implantado, uma [grupo de segurança de rede](../virtual-network/security-overview.md) deve ser configurado para permitir o acesso na porta desejada.

## <a name="problems-with-custom-health-probe"></a>Problemas com a investigação de integridade personalizada

### <a name="cause"></a>Causa

Investigações de integridade personalizadas oferecem flexibilidade adicional para o comportamento de investigação padrão. Ao usar investigações personalizadas, você pode configurar o intervalo de investigação, a URL, o caminho a testar e quantas respostas com falha devem ser aceitas antes de marcar a instância do pool de back-end como não íntegra.

As seguintes propriedades adicionais são adicionadas:

| Propriedades da investigação | DESCRIÇÃO |
| --- | --- |
| NOME |O nome da investigação. Este é o nome usado para se referir à investigação nas configurações de HTTP de back-end. |
| Protocol |O protocolo usado para enviar a investigação. A investigação usa o protocolo definido nas configurações de HTTP do back-end |
| Host |O nome do host para enviar a investigação. Aplicável somente quando vários sites são configurados no gateway de aplicativo. Isso é diferente do nome de host de VM. |
| Caminho |O caminho relativo da investigação. Um caminho válido começa com '/'. A investigação é enviada para \<protocol\>://\<host\>:\<port\>\<path\> |
| Interval |Intervalo de investigação em segundos. Este é o intervalo de tempo entre duas investigações consecutivas. |
| Tempo limite |Tempo limite da investigação em segundos. Se uma resposta válida não for recebida dentro desse período de tempo limite, a investigação é marcada como com falha. |
| Limite não íntegro |Contagem de repetições da investigação. O servidor de back-end é marcado após a contagem de falhas de investigação consecutivas atingir o limite de não íntegro. |

### <a name="solution"></a>Solução

Valide se a Investigação de Integridade Personalizada está configurada corretamente conforme a tabela anterior. Além das etapas de solução de problemas anteriores, também verifique o seguinte:

* Verifique se a investigação foi especificada corretamente, conforme o [guia](application-gateway-create-probe-ps.md).
* Se o gateway de aplicativo é configurado para um único site, por padrão, o Host do nome deve ser especificado como `127.0.0.1`, a menos que configurado de outra forma na investigação personalizada.
* Verifique se uma chamada para http://\<host\>:\<port\>\<path\> retorna um código de resultado HTTP 200.
* Certifique-se de que o intervalo de tempo limite e UnhealtyThreshold estão dentro dos intervalos aceitáveis.
* Se usar uma investigação HTTPS, certifique-se de que o servidor de back-end não requer o SNI, configurando para isso um certificado fallback no próprio servidor de back-end.

## <a name="request-time-out"></a>Tempo limite de solicitação

### <a name="cause"></a>Causa

Quando uma solicitação de usuário é recebida, o gateway de aplicativo aplica as regras configuradas para a solicitação e encaminhá-la a uma instância de pool de back-end. Ele aguarda por um intervalo de tempo configurável para receber uma resposta da instância de back-end. Por padrão, esse intervalo é **20** segundos. Se o gateway de aplicativo não receber uma resposta do aplicativo de back-end nesse intervalo, a solicitação de usuário obtém um erro 502.

### <a name="solution"></a>Solução

O Gateway de aplicativo permite que você definir essa configuração por meio de BackendHttpSetting, que pode então ser aplicado a diferentes pools. Pools de back-end diferentes podem ter BackendHttpSetting diferentes e um limite de solicitação diferentes configurados.

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>BackendAddressPool vazio

### <a name="cause"></a>Causa

Se o gateway de aplicativo não tiver VMs ou conjunto de dimensionamento de máquina virtual configurado no pool de endereços de back-end, ele não pode encaminhar solicitações de clientes e enviará uma mensagem de erro de gateway incorreto.

### <a name="solution"></a>Solução

Certifique-se de que o pool de endereços de back-end não está vazio. Isso pode ser feito por meio do PowerShell, da CLI ou do portal.

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

A saída do cmdlet anterior deve conter o pool de endereços de back-end não vazio. O exemplo a seguir mostra dois pools retornado que são configurados com um FQDN ou um endereços IP para as VMs de back-end. O estado de provisionamento de BackendAddressPool deve ser 'Bem-sucedido'.

BackendAddressPoolsText:

```json
[{
    "BackendAddresses": [{
        "ipAddress": "10.0.0.10",
        "ipAddress": "10.0.0.11"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool01",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
}, {
    "BackendAddresses": [{
        "Fqdn": "xyx.cloudapp.net",
        "Fqdn": "abc.cloudapp.net"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool02",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
}]
```

## <a name="unhealthy-instances-in-backendaddresspool"></a>Instâncias não íntegras em BackendAddressPool

### <a name="cause"></a>Causa

Se todas as instâncias do BackendAddressPool estiverem não íntegros, o gateway de aplicativo não tem qualquer back-end, a solicitação de usuário de rota para. Isso também pode ser o caso quando instâncias de back-end estão íntegras, mas não tem o aplicativo necessário implantado.

### <a name="solution"></a>Solução

Verifique se as instâncias estão íntegras e se o aplicativo está configurado corretamente. Verifique se as instâncias de back-end podem responder a um ping de outra VM na mesma rede virtual. Se configurado com um ponto de extremidade público, certifique-se de que uma solicitação do navegador para o aplicativo web é operacional.

## <a name="next-steps"></a>Próximas etapas

Se as etapas anteriores não resolverem o problema, abra uma [tíquete de suporte](https://azure.microsoft.com/support/options/).

