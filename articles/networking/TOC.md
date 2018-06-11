# Visão geral
## [Sobre a Rede do Azure](networking-overview.md)
## Arquitetura
### [Datacenters virtuais](networking-virtual-datacenter.md)
### [Roteamento assimétrico com vários caminhos de rede](../expressroute/expressroute-asymmetric-routing.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Designs de rede segura](../best-practices-network-security.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Topologia hub-spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)
### [Práticas recomendadas de segurança da rede](../security/azure-security-network-security-best-practices.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Dispositivos virtuais de rede altamente disponíveis](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha )
### [Combinar métodos de balanceamento de carga](../traffic-manager/traffic-manager-load-balancing-azure.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Recuperação de desastres usando o DNS do Azure e o Gerenciador de Tráfego](disaster-recovery-dns-traffic-manager.md)
## Planejar e projetar
### [Redes virtuais](../virtual-network/virtual-network-vnet-plan-design-arm.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Conectividade entre locais - VPN](../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Conectividade entre locais - privada dedicada](../expressroute/expressroute-workflows.md?toc=%2fazure%2fnetworking%2ftoc.json)

##  Conceitos
### [Redes virtuais](../virtual-network/virtual-networks-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Balanceamento de carga de rede](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Balanceamento de carga do aplicativo](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [DNS](../dns/dns-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Distribuição de tráfego com base em DNS](../traffic-manager/traffic-manager-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Conectar local - VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Conectar local - dedicado](../expressroute/expressroute-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json)


# Introdução
## [Criar sua primeira rede virtual](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)

# Como
## Conectividade com a Internet
### [Servidores públicos de balanceador de carga de rede](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Servidores públicos de balanceador de carga do aplicativo](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Proteger aplicativos Web](../application-gateway/application-gateway-web-application-firewall-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Distribuir o tráfego entre locais](../traffic-manager/traffic-manager-configure-geographic-routing-method.md?toc=%2fazure%2fnetworking%2ftoc.json)
## Conectividade interna
### [Servidores privados de balanceador de carga de rede](../load-balancer/load-balancer-get-started-ilb-arm-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Servidores privados de balanceador de carga do aplicativo](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Conectar redes virtuais (mesma localização)](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Conectar redes virtuais (localizações diferentes)](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
## Conectividade entre locais
### [Criar uma conexão VPN S2S (IPsec/IKE)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Criar uma conexão VPN P2S (SSTP com certificados)](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Criar uma conexão privada dedicada (ExpressRoute)](../expressroute/expressroute-howto-circuit-portal-resource-manager.md?toc=%2fazure%2fnetworking%2ftoc.json)

## Gerenciamento
### [Visão geral do monitoramento da rede](network-monitoring-overview.md)
### [Verificar o uso de recursos em relação aos limites do Azure](check-usage-against-limits.md)
### [Exibir topologia de rede](../network-watcher/network-watcher-topology-powershell.md?toc=%2fazure%2fnetworking%2ftoc.json)

## Scripts de exemplo
### [CLI do Azure](cli-samples.md)
### [PowerShell do Azure](powershell-samples.md)

## Tutoriais
### [VMs de balanceamento de carga](../virtual-machines/linux/tutorial-load-balance-nodejs.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Conectar computador a uma rede virtual](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)

# Referência
## [CLI do Azure](https://docs.microsoft.com/cli/azure/network)
## [PowerShell do Azure](https://docs.microsoft.com/powershell/module/azurerm.network/?view=azurermps-3.8.0)
## [.Net](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.network?view=azuremgmtnetwork-9.1.0-preview)
## [Node.js](https://azure.microsoft.com/develop/nodejs/#azure-sdk)
## [REST](https://msdn.microsoft.com/library/mt163658.aspx)

# Recursos
## [Modelos de autor](/azure/azure-resource-manager/resource-group-authoring-templates?toc=%2fazure%2fnetworking%2ftoc.json)
## [Roteiro do Azure](https://azure.microsoft.com/roadmap/?category=networking)
## [Modelos da comunidade](https://azure.microsoft.com/resources/templates/)
## [Blog de rede](http://azure.microsoft.com/blog/topics/networking)
## [Preços](https://azure.microsoft.com/pricing)
## [Calculadora de preço](https://azure.microsoft.com/pricing/calculator/)
## [Disponibilidade regional](https://azure.microsoft.com/regions/services/)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-virtual-network)
## [Vídeos](https://azure.microsoft.com/resources/videos/index/?services=virtual-network)

