---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: firewall
author: vhorne
ms.service: ''
ms.topic: include
ms.date: 10/15/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 1d52c9b3b9b91873bd6f34443f162c74d724a65c
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49367156"
---
### <a name="what-is-azure-firewall"></a>O que é o Firewall do Azure?

Firewall do Azure é um serviço de segurança de rede gerenciado e baseado em nuvem que protege seus recursos de Rede Virtual do Azure. É um firewall como serviço totalmente com estado com alta disponibilidade interna e escalabilidade de nuvem irrestrita. É possível criar, impor e registrar centralmente políticas de conectividade de rede e de aplicativo em assinaturas e redes virtuais.

### <a name="what-capabilities-are-supported-in-azure-firewall"></a>Quais recursos têm suporte no Firewall do Azure?

* Firewall como serviço com estado
* Alta disponibilidade interna com escalabilidade de nuvem irrestrita
* Filtragem de FQDN
* Marcas de FQDN
* Regras de filtragem de tráfego de rede
* Suporte a SNAT de saída
* Suporte a DNAT de entrada
* É possível criar, impor e registrar em log centralmente políticas de conectividade de rede e de aplicativo em VNETs e assinaturas do Azure
* Totalmente integrado ao Azure Monitor para registro em log e análise

### <a name="what-is-the-pricing-for-azure-firewall"></a>Qual é o preço para o Firewall do Azure?

O Firewall do Azure tem um custo fixo + um custo variável:

* Valor fixo: US$ 1,25/firewall/hora
* Valor variável: US$ 0,03/GB processado pelo firewall (entrada ou saída)

### <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Qual é o modelo de implantação típico para o Firewall do Azure?

É possível implantar o Firewall do Azure em qualquer rede virtual, mas os clientes normalmente o implantam em uma rede virtual central e emparelham outras redes virtuais a ele em um modelo de hub e spoke. Você pode definir a rota padrão de redes virtuais emparelhadas para apontar a essa rede virtual de firewall central.

### <a name="how-can-i-install-the-azure-firewall"></a>Como eu instalo o Firewall do Azure?

O Firewall do Azure pode ser configurado usando o portal do Azure, PowerShell, API REST ou usando modelos. Veja o [Tutorial: Implantar e configurar o Firewall do Azure usando o portal do Azure](../articles/firewall/tutorial-firewall-deploy-portal.md) para obter instruções passo a passo.

### <a name="what-are-some-azure-firewall-concepts"></a>Quais são alguns conceitos do Firewall do Azure?

O Firewall do Azure é compatível com regras e coleções de regras. Uma coleção de regras é um conjunto de regras que compartilham a mesma ordem e a mesma prioridade. Coleções de regras são executadas na ordem de prioridade. Coleções de regras de rede têm prioridade maior do que as coleções de regras de aplicativo, sendo que todas as regras são de terminação.

Há dois tipos de coleções de regras:

* *Regras de aplicativo*: permitem a você configurar os FQDNs (nomes de domínio totalmente qualificados) que podem ser acessados a partir de uma sub-rede.
* *Regras de rede*: permitem a você configurar regras que contêm os endereços de origem, protocolos, portas de destino e os endereço de destino.

### <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>O Firewall do Azure dá suporte à filtragem de tráfego de entrada?

O Firewall do Azure dá suporte à filtragem de entrada e saída. A proteção de entrada é para protocolos não HTTP/S. Por exemplo, protocolos RDP, SSH e FTP.

### <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Quais serviços de registro em log e análise têm suporte do Firewall do Azure?

O Firewall do Azure é integrado ao Azure Monitor para exibir e analisar logs de firewall. Os logs podem ser enviados para o Log Analytics, Armazenamento do Azure ou para Hubs de Eventos. Eles podem ser analisados no Log Analytics ou por diferentes ferramentas, como Excel e Power BI. Para obter mais informações, consulte [Tutorial: Monitorar logs do Firewall do Azure](../articles/firewall/tutorial-diagnostics.md).

### <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Como o Firewall do Azure funciona diferentemente dos serviços existentes, como NVAs no marketplace?

O Firewall do Azure é um serviço de firewall básico que pode atender a determinados cenários de cliente. Espera-se que você terá uma mistura de NVAs de terceiros e o Firewall do Azure. Trabalhar melhor juntos é uma prioridade principal.

### <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Qual é a diferença entre o WAF do Gateway de Aplicativo e o Firewall do Azure?

O WAF (Firewall de Aplicativo Web) é um recurso do Gateway de Aplicativo que fornece proteção de entrada centralizada de seus aplicativos Web contra vulnerabilidades e explorações comuns. O Firewall do Azure fornece proteção de entrada para protocolos não HTTP / S (por exemplo, RDP, SSH, FTP), proteção de nível de rede de saída para todas as portas e protocolos e proteção no nível do aplicativo para HTTP / S de saída.

### <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Qual é a diferença entre NSGs (Grupos de Segurança de Rede) e o Firewall do Azure?

O serviço de Firewall do Azure complementa a funcionalidade de grupo de segurança de rede. Juntos, eles fornecem uma melhor segurança de rede de "defesa em profundidade". Os grupo de segurança de rede fornecem filtragem de tráfego de camada de rede distribuída para limitar o tráfego para recursos dentro de redes virtuais em cada assinatura. O Firewall do Azure é um firewall como serviço de rede centralizado totalmente com estado, que fornece proteção no nível de rede e de aplicativo em diferentes assinaturas e redes virtuais.

### <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Como configurar o Firewall do Azure com meus pontos de extremidade de serviço?

Para obter acesso seguro aos serviços de PaaS, recomendamos ponto de extremidade de serviço. Você pode optar por habilitar pontos de extremidade de serviço na sub-rede de Firewall do Azure e desabilitá-los nas redes virtuais spoke conectadas. Dessa forma você aproveita ambos os recursos – segurança de ponto de extremidade de serviço e o registro em log central para todo o tráfego.

### <a name="how-can-i-stop-and-start-azure-firewall"></a>Como fazer para parar e iniciar o Firewall do Azure?

Use os métodos *deallocate* e *allocate* do Azure PowerShell.

Por exemplo: 

```azurepowershell
# Stop an exisitng firewall

$azfw = Get-AzureRmFirewall -Name "FW Name” -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzureRmFirewall -AzureFirewall $azfw
```

```azurepowershell
#Start a firewall

$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip = Get-AzureRmPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzureRmFirewall -AzureFirewall $azfw
```

> [!NOTE]
> Você deve realocar um firewall e o IP público ao grupo de recursos e à assinatura originais.

### <a name="what-are-the-known-service-limits"></a>Quais são os limites de serviço conhecidos?

* O Firewall do Azure tem um limite flexível de 1000 TB por firewall por mês.
* Uma instância do Firewall do Azure que está em execução em uma rede virtual central tem limitações de emparelhamento de rede virtual, com um máximo de 50 redes virtuais de spoke.  
* O Firewall do Azure não funciona com emparelhamento global, assim, os clientes devem ter pelo menos uma implantação do firewall por região.
* O Firewall do Azure dá suporte a 10 mil regras de aplicativos e a 10 mil regras de rede.

### <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>O Firewall do Azure em uma rede virtual de hub pode encaminhar e filtrar o tráfego de rede entre duas redes virtuais de spoke?

Sim, você pode usar o Firewall do Azure em uma rede virtual de hub para encaminhar e filtrar o tráfego de rede entre duas redes virtuais de spoke. As sub-redes em cada uma das redes virtuais de spoke devem ter uma UDR apontando para o Firewall do Azure como um gateway padrão para que este cenário funcione corretamente.

### <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network"></a>O Firewall do Azure pode encaminhar e filtrar o tráfego de rede entre sub-redes na mesma rede virtual?

O tráfego entre sub-redes na mesma rede virtual ou em uma rede virtual diretamente emparelhada é encaminhado diretamente mesmo que a UDR aponte para o Firewall do Azure como o gateway padrão. O método recomendado para segmentação de rede interna é usar Grupos de Segurança de Rede. Para enviar tráfego de sub-rede para sub-rede para o firewall nesse cenário, a UDR deve conter o prefixo de rede de sub-rede de destino explicitamente em ambas as sub-redes.

### <a name="are-there-any-firewall-resource-group-restrictions"></a>Há qualquer firewall restrições no grupo de recursos?

Sim. O firewall, a sub-rede, a rede virtual e o endereço IP público devem estar todos no mesmo grupo de recursos.
