---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: firewall
author: vhorne
ms.service: ''
ms.topic: include
ms.date: 8/13/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 5601f8d90f107636d2899a024772dccc8f75b69d
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2018
ms.locfileid: "40132247"
---
### <a name="what-is-azure-firewall"></a>O que é o Firewall do Azure?

Firewall do Azure é um serviço de segurança de rede gerenciado e baseado em nuvem que protege seus recursos de Rede Virtual do Azure. É um firewall como serviço totalmente com estado com alta disponibilidade interna e escalabilidade de nuvem irrestrita. É possível criar, impor e registrar centralmente políticas de conectividade de rede e de aplicativo em assinaturas e redes virtuais. O Firewall do Azure está atualmente em visualização pública.

### <a name="what-capabilities-are-supported-in-the-azure-firewall-public-preview-release"></a>Quais funcionalidades têm suporte na visualização pública do Firewall do Azure?  

* Firewall como serviço com estado
* Alta disponibilidade interna com escalabilidade de nuvem irrestrita
* Filtragem de FQDN 
* Regras de filtragem de tráfego de rede
* Suporte a SNAT de saída
* É possível criar, impor e registrar em log centralmente políticas de conectividade de rede e de aplicativo em VNETs e assinaturas do Azure
* Totalmente integrado ao Azure Monitor para registro em log e análise 

### <a name="how-can-i-join-the-azure-firewall-public-preview"></a>Como posso ingressar na visualização pública do Firewall do Azure?

O Firewall do Azure está atualmente em uma visualização pública gerenciada à qual você pode ingressar usando o comando Register-AzureRmProviderFeature PowerShell, conforme explicado na documentação da visualização pública do Firewall do Azure.

### <a name="what-is-the-pricing-for-azure-firewall"></a>Qual é o preço para o Firewall do Azure?

O Firewall do Azure tem um custo fixo + um custo variável. Os preços estão a seguir, e eles têm um desconto adicional de 50% durante a visualização pública.

* Valor fixo: US$ 1,25/firewall/hora
* Valor variável: US$ 0,03/GB processado pelo firewall (entrada ou saída).

### <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Qual é o modelo de implantação típico para o Firewall do Azure?

Embora seja possível implantar o Firewall do Azure em qualquer VNET, os clientes normalmente implantariam o Firewall do Azure em uma VNET central e emparelhariam outras VNETs a ela em um modelo de Hub e Spoke. Você pode definir a rota padrão de VNETs emparelhadas para apontar para essa VNET de Firewall central.

### <a name="how-can-i-install-the-azure-firewall"></a>Como eu instalo o Firewall do Azure?

O Firewall do Azure pode ser instalado por meio do portal do Azure, do PowerShell, da API REST ou de Modelos. Veja o [Tutorial: Implantar e configurar o Firewall do Azure usando o portal do Azure](../articles/firewall/tutorial-firewall-deploy-portal.md) para obter instruções passo a passo.

### <a name="what-are-some-azure-firewall-concepts"></a>Quais são alguns conceitos do Firewall do Azure?

O Firewall do Azure é compatível com regras e coleções de regras. Uma coleção de regras é um conjunto de regras que compartilham a mesma ordem e a mesma prioridade. Coleções de regras são executadas na ordem de prioridade, coleções de regras de Rede têm prioridade maior do que as coleções de regras de aplicativo, todas as regras são de terminação.
Há dois tipos de coleções de regras:

* Regras de aplicativo: permitem que você configure os FQDNs (nomes de domínio totalmente qualificados) que podem ser acessados de uma sub-rede. 
* Regras de rede: permitem que você configure regras contendo o endereço de origem, o protocolo, a porta de destino e o endereço de destino. 

### <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>O Firewall do Azure dá suporte à filtragem de tráfego de entrada?

A visualização pública do Firewall do Azure dá suporte somente para filtragem de saída. Proteção de entrada para protocolos não HTTP/S (por exemplo: RDP, SSH, FTP) está planejada provisoriamente para GA do Firewall do Azure.  
 
### <a name="what-logginganalytics-is-supported-by-the-azure-firewall"></a>Que registro em log/análises são compatíveis com o Firewall do Azure?

O Firewall do Azure é integrado ao Azure Monitor para exibir e analisar logs do Firewall. Os logs podem ser enviados para o Log Analytics, para o Armazenamento do Azure ou para o Hub de Eventos. Eles podem ser analisados no Log Analytics ou por diferentes ferramentas, como Excel e Power BI. Veja o [Tutorial: Monitorar logs do Firewall do Azure](../articles/firewall/tutorial-diagnostics.md) para obter mais detalhes.

### <a name="how-does-azure-firewall-work-relative-to-existing-like-nvas-in-the-marketplace"></a>Como o Firewall do Azure funciona em relação ao existente, como NVAs no marketplace?

O Firewall do Azure é um serviço de firewall básico que pode atender a determinados cenários de cliente. Espera-se que você terá uma mistura de NVAs de terceiros e o Firewall do Azure. Trabalhar melhor juntos é uma prioridade principal.
 
### <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Qual é a diferença entre o WAF do Gateway de Aplicativo e o Firewall do Azure?

O WAF (Firewall de Aplicativo Web) é um recurso do Gateway de Aplicativo que fornece proteção de entrada centralizada de seus aplicativos Web contra vulnerabilidades e explorações comuns. O Firewall do Azure fornece proteção no nível da rede de saída para todas as portas e protocolos e proteção no nível do aplicativo para HTTP/S de saída. Proteção de entrada para protocolos não HTTP/S (por exemplo, RDP, SSH, FTP) está planejada provisoriamente para GA do Firewall do Azure.

### <a name="what-is-the-difference-between-network-security-groups-nsg-and-azure-firewall"></a>Qual é a diferença entre NSGs (Grupos de Segurança de Rede) e o Firewall do Azure?

O serviço do Firewall do Azure complementa a funcionalidade do Grupo de Segurança de Rede e, junto com ela, fornece maior segurança de rede de defesa em profundidade. Os NSGs fornecem filtragem de tráfego de camada de rede distribuída para limitar o tráfego para recursos em redes virtuais em cada assinatura.  O Firewall do Azure é um firewall como serviço de rede centralizado totalmente com estado, fornecendo proteção no nível da rede e do aplicativo em diferentes assinaturas e VNets (redes virtuais). 

### <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Como configurar o Firewall do Azure com meus pontos de extremidade de serviço?

Para obter acesso seguro aos serviços de PaaS, recomendamos Pontos de Extremidade de Serviço. Os clientes do Firewall do Azure podem optar por habilitar pontos de extremidade de serviço na sub-rede de Firewall do Azure e desabilitá-lo nas redes VNETs de spoke conectadas para aproveitar os benefícios de ambos os recursos: segurança de ponto de extremidade de serviço e registro em log central para todo o tráfego.

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

### <a name="what-are-the-known-service-limits"></a>Quais são os limites de serviço conhecidos?

* O Firewall do Azure tem um limite flexível de 1000 TB/firewall/mês. 
* O Firewall do Azure que está sendo executado em uma VNET central está sujeito às limitações de emparelhamento VNET: máx. de 50 VNETs de spoke.  
* O Firewall do Azure não funciona com emparelhamento global, assim, os clientes devem ter pelo menos uma implantação do firewall por região.
* O firewall do Azure dá suporte a 10 mil aplicativos e as regras de rede 10 k.