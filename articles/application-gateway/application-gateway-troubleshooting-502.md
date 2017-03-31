---
title: "Solucionar problemas de erros de gateway de aplicativo inválido (502) do Application Gateway | Microsoft Docs"
description: Saiba como solucionar problemas de erros 502 do Application Gateway
services: application-gateway
documentationcenter: na
author: amitsriva
manager: rossort
editor: 
tags: azure-resource-manager
ms.assetid: 1d431ead-d318-47d8-b3ad-9c69f7e08813
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/16/2016
ms.author: amsriva
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: d61e50b7440dcd107df3e5dd085a36b149779553
ms.lasthandoff: 03/30/2017


---

# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Solução de problemas de erros de gateway incorreto no Application Gateway

## <a name="overview"></a>Visão geral

Após a configuração de um Application Gateway do Azure, um dos erros que os usuários podem encontrar é "Erro de Servidor: 502 - o servidor Web recebeu uma resposta inválida ao atuar como gateway ou proxy de servidor". Este erro pode ocorrer pelos seguintes motivos principais:

* O pool de back-end do Gateway de Aplicativo do Azure não está configurado ou está vazio.
* Nenhuma das VMs ou instâncias no Conjunto de Escala de VM está íntegra.
* VMs de back-end ou instâncias do Conjunto de Escala de VM não estão respondendo à investigação de integridade padrão.
* Configuração inválida ou incorreta de investigações de integridade personalizadas.
* Tempo limite de solicitação ou problemas de conectividade com solicitações de usuário.

## <a name="empty-backendaddresspool"></a>BackendAddressPool vazio

### <a name="cause"></a>Causa

Se o Application Gateway não tiver VMs ou o Conjunto de Escala de VM configurados no pool de endereços de back-end, não poderá encaminhar solicitações de clientes e gerará um erro de gateway incorreto.

### <a name="solution"></a>Solução

Verifique se o pool de endereços de back-end não está vazio. Isso pode ser feito por meio do PowerShell, da CLI ou do portal.

```powershell
Get-AzureRmApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

A saída do cmdlet anterior deve conter o pool de endereços de back-end não vazio. A seguir há um exemplo em que são retornados dois pools que estão configurados com endereços FQDN ou IP para VMs de back-end. O estado de provisionamento de BackendAddressPool deve ser 'Bem-sucedido'.

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

Se todas as instâncias do BackendAddressPool estiverem não íntegras, o Application Gateway não terá um back-end para o qual rotear a solicitação do usuário. Esse também pode ser o caso quando instâncias de back-end estão íntegras, mas não têm o aplicativo necessário implantado.

### <a name="solution"></a>Solução

Verifique se as instâncias estão íntegras e se o aplicativo está configurado corretamente. Verifique se as instâncias de back-end podem responder a um ping de outra VM na mesma VNet. Se a configuração tiver sido feita com um ponto de extremidade público, verifique se uma solicitação do navegador ao aplicativo Web é operacional.

## <a name="problems-with-default-health-probe"></a>Problemas com a investigação de integridade padrão

### <a name="cause"></a>Causa

Os erros 502 também podem ser indicadores frequentes de que a investigação de integridade padrão não consegue acessar VMs de back-end. Quando uma instância do Application Gateway é provisionada, configura automaticamente um teste de integridade padrão para cada BackendAddressPool usando as propriedades de BackendHttpSetting. Nenhuma entrada do usuário é necessária para definir essa investigação. Especificamente, quando uma regra de balanceamento de carga é configurada, é feita uma associação entre BackendHttpSetting e BackendAddressPool. Uma investigação padrão é configurada para cada um dessas associações, e o Application Gateway inicia uma conexão de verificação de integridade periódica para cada instância em BackendAddressPool na porta especificada no elemento BackendHttpSetting. A tabela a seguir lista os valores associados à investigação de integridade padrão.

| Propriedades da investigação | Valor | Descrição |
| --- | --- | --- |
| URL de investigação |http://127.0.0.1/ |Caminho da URL |
| Intervalo |30 |Intervalo da investigação em segundos |
| Tempo limite |30 |Tempo limite da investigação em segundos |
| Limite não íntegro |3 |Contagem de repetições da investigação. O servidor de back-end é marcado após a contagem de falhas de investigação consecutivas atingir o limite de não íntegro. |

### <a name="solution"></a>Solução

* Verifique se um site padrão está configurado e está escutando em 127.0.0.1.
* Se BackendHttpSetting especificar uma porta diferente de 80, o site padrão deverá ser configurado para escutar nessa porta.
* A chamada para http://127.0.0.1:porta. deve retornar um código de resultado de HTTP 200. Ele deve ser retornado dentro do período de tempo limite de 30 segundos.
* Verifique se a porta configurada está aberta e se não há regras de firewall ou Grupos de Segurança de Rede do Azure que bloqueiam o tráfego de entrada ou de saída na porta configurada.
* Se VMs clássicas do Azure ou o Serviço de Nuvem forem usados com FQDN ou IP Público, verifique se o [ponto de extremidade](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) correspondente está aberto.
* Se a VM for configurada por meio do Azure Resource Manager e estiver fora da rede virtual em que o Application Gateway está implantado, o [Grupo de Segurança de Rede](../virtual-network/virtual-networks-nsg.md) deverá ser configurado para permitir o acesso na porta desejada.

## <a name="problems-with-custom-health-probe"></a>Problemas com a investigação de integridade personalizada

### <a name="cause"></a>Causa

Investigações de integridade personalizadas oferecem flexibilidade adicional para o comportamento de investigação padrão. Ao usar investigações personalizadas, os usuários podem configurar o intervalo de investigação, a URL e o caminho a testar e quantas respostas com falha devem ser aceitas antes de marcar a instância do pool de back-end como não íntegra. As propriedades adicionais a seguir são adicionadas.

| Propriedades da investigação | Descrição |
| --- | --- |
| Name |O nome da investigação. Este é o nome usado para se referir à investigação nas configurações de HTTP de back-end. |
| Protocolo |O protocolo usado para enviar a investigação. A investigação usa o protocolo definido nas configurações de HTTP do back-end |
| Host |O nome do host para enviar a investigação. Aplicável somente quando vários sites são configurados no Application Gateway. Isso é diferente do nome de host de VM. |
| Caminho |O caminho relativo da investigação. Um caminho válido começa com '/'. A investigação é enviada para \<protocol\>://\<host\>:\<port\>\<path\> |
| Intervalo |Intervalo de investigação em segundos. Este é o intervalo de tempo entre duas investigações consecutivas. |
| Tempo limite |Tempo limite da investigação em segundos. Se uma resposta válida não for recebida dentro desse período de tempo limite, a investigação será marcada como com falha. |
| Limite não íntegro |Contagem de repetições da investigação. O servidor de back-end é marcado após a contagem de falhas de investigação consecutivas atingir o limite de não íntegro. |

### <a name="solution"></a>Solução

Valide se a Investigação de Integridade Personalizada está configurada corretamente conforme a tabela anterior. Além das etapas de solução de problemas anteriores, também verifique o seguinte:

* Verifique se a investigação foi especificada corretamente, conforme o [guia](application-gateway-create-probe-ps.md).
* Se o Application Gateway estiver configurado para um único site, por padrão, o nome do Host deverá ser especificado como '127.0.0.1', a menos que seja configurado de outra forma na investigação personalizada.
* Verifique se uma chamada para http://\<host\>:\<port\>\<path\> retorna um código de resultado HTTP 200.
* Verifique se Interval, Time-out e UnhealtyThreshold estão dentro dos intervalos aceitáveis.

## <a name="request-time-out"></a>Tempo limite de solicitação

### <a name="cause"></a>Causa

Quando é recebida uma solicitação de usuário, o Application Gateway aplica as regras configuradas para a solicitação e a roteia para uma instância de pool de back-end. Ele aguarda por um intervalo de tempo configurável para receber uma resposta da instância de back-end. Por padrão, o intervalo é de **30 segundos**. Se o Application Gateway não receber uma resposta do aplicativo de back-end nesse intervalo, a solicitação de usuário obterá um erro 502.

### <a name="solution"></a>Solução

O Application Gateway permite aos usuários definir essa configuração por meio de BackendHttpSetting, que pode então ser aplicado a diferentes pools. Os pools de back-end diferentes podem ter BackendHttpSetting diferentes e, assim, um tempo limite de solicitação diferente configurado.

```powershell
    New-AzureRmApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="next-steps"></a>Próximas etapas

Se as etapas anteriores não resolverem o problema, abra um [tíquete de suporte](https://azure.microsoft.com/support/options/).


