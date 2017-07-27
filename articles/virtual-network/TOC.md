# Visão geral
## [Redes Virtuais](virtual-networks-overview.md)
## [Encaminhamento IP e rotas definidas pelo usuário](virtual-networks-udr-overview.md)
## [Emparelhamento de rede virtual](virtual-network-peering-overview.md)
## [Continuidade dos negócios](virtual-network-disaster-recovery-guidance.md)
## [PERGUNTAS FREQUENTES](virtual-networks-faq.md)
## [Endereçamento IP](virtual-network-ip-addresses-overview-arm.md)
## Clássico
### [Endereçamento IP](virtual-network-ip-addresses-overview-classic.md)
### [Listas de controle de acesso](virtual-networks-acl.md)

# Introdução
## [Criar sua primeira rede virtual](virtual-network-get-started-vnet-subnet.md)

# Como
## Planejar e projetar
### [Redes virtuais](virtual-network-vnet-plan-design-arm.md)
### [Grupos de segurança de rede](virtual-networks-nsg.md)

## Implantar
### [Redes virtuais](virtual-networks-create-vnet-arm-pportal.md)
#### [PowerShell](virtual-networks-create-vnet-arm-ps.md)
#### [CLI](virtual-networks-create-vnet-arm-cli.md)
#### [Modelo](virtual-networks-create-vnet-arm-template-click.md)
#### Clássico
##### [Portal](virtual-networks-create-vnet-classic-pportal.md)
##### [PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md)
##### [CLI](virtual-networks-create-vnet-classic-cli.md)

### Grupos de segurança de rede
#### [Portal](virtual-networks-create-nsg-arm-pportal.md)
#### [PowerShell](virtual-networks-create-nsg-arm-ps.md)
#### [CLI](virtual-networks-create-nsg-arm-cli.md)
#### [Modelo](virtual-networks-create-nsg-arm-template.md)
#### Clássico
##### [PowerShell](virtual-networks-create-nsg-classic-ps.md)
##### [CLI](virtual-networks-create-nsg-classic-cli.md)

### Rotas definidas pelo usuário
#### [PowerShell](virtual-network-create-udr-arm-ps.md)
#### [CLI](virtual-network-create-udr-arm-cli.md)
#### [Modelo](virtual-network-create-udr-arm-template.md)
#### Clássico
##### [PowerShell](virtual-network-create-udr-classic-ps.md)
##### [CLI](virtual-network-create-udr-classic-cli.md)

### Emparelhamento de rede virtual
#### [Mesmo modelo de implantação - mesma assinatura](virtual-network-create-peering.md)
#### [Mesmo modelo de implantação - assinaturas diferentes](create-peering-different-subscriptions.md)
#### [Modelos de implantação diferentes - mesma assinatura](create-peering-different-deployment-models.md)
#### [Modelos de implantação diferentes - assinaturas diferentes](create-peering-different-deployment-models-subscriptions.md)

### Máquinas virtuais
#### Criar uma VM com um endereço IP público estático
##### [Portal](virtual-network-deploy-static-pip-arm-portal.md)
##### [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
##### [CLI](virtual-network-deploy-static-pip-arm-cli.md)
##### [Modelo](virtual-network-deploy-static-pip-arm-template.md)
##### Clássico
###### [PowerShell](virtual-networks-reserved-public-ip.md)

#### Criar uma VM com um endereço IP privado estático
##### [Portal](virtual-networks-static-private-ip-arm-pportal.md)
##### [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
##### [CLI](virtual-networks-static-private-ip-arm-cli.md)
##### Clássico
###### [Portal](virtual-networks-static-private-ip-classic-pportal.md)
###### [PowerShell](virtual-networks-static-private-ip-classic-ps.md)
###### [CLI](virtual-networks-static-private-ip-classic-cli.md)

#### Criar uma VM com várias interfaces de rede
##### [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### Clássico
###### [PowerShell](virtual-network-deploy-multinic-classic-ps.md)
###### [CLI](virtual-network-deploy-multinic-classic-cli.md)

#### Criar uma VM com vários endereços IP
##### [Portal do Azure](virtual-network-multiple-ip-addresses-portal.md)
##### [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
##### [CLI](virtual-network-multiple-ip-addresses-cli.md)
##### [Modelo](virtual-network-multiple-ip-addresses-template.md)

#### [Criar uma VM com rede acelerada](virtual-network-create-vm-accelerated-networking.md)

### Cenários de conectividade
#### [VNet (rede virtual) para VNet](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [VNet (Resource Manager) para uma VNet (Clássica)](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [VNet para rede local (VPN)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [VNet para rede local (ExpressRoute)](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Arquitetura de rede híbrida altamente disponível](../guidance/guidance-hybrid-network-expressroute-vpn-failover.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### Cenários de segurança
#### [Proteger redes com dispositivos virtuais](virtual-network-scenario-udr-gw-nva.md)
#### [DMZ entre o Azure e a Internet](../guidance/guidance-iaas-ra-secure-vnet-dmz.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Segurança de rede e serviço de nuvem](../best-practices-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [DMZ simples com NSGs](virtual-networks-dmz-nsg-asm.md)
##### [DMZ com firewall e NSGs](virtual-networks-dmz-nsg-fw-asm.md)
##### [DMZ com firewall, UDR e NSGs](virtual-networks-dmz-nsg-fw-udr-asm.md)
##### [Aplicativo de exemplo](virtual-networks-sample-app.md)

## Configurar
### Máquinas virtuais
#### [Adicionar ou remover interfaces de rede](virtual-network-network-interface-vm.md)
#### [Resolução de nomes de máquinas virtuais e serviços de nuvem](virtual-networks-name-resolution-for-vms-and-role-instances.md)
#### [Otimizar taxa de transferência de rede](virtual-network-optimize-network-bandwidth.md)
#### [Exibir e modificar nomes do host](virtual-networks-viewing-and-modifying-hostnames.md)
### Clássico
#### Listas de controle de acesso
##### [Portal](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [PowerShell](virtual-networks-acl-powershell.md)

## Gerenciar
### [Redes virtuais](virtual-network-manage-network.md)
#### [Sub-redes](virtual-network-manage-subnet.md)
#### [Emparelhamentos](virtual-network-manage-peering.md)
#### Clássico
##### [Arquivo de configuração de rede](virtual-networks-using-network-configuration-file.md)
##### [Migrar de um grupo de afinidade para uma região](virtual-networks-migrate-to-regional-vnet.md)
### Grupos de segurança de rede
#### [Portal](virtual-network-manage-nsg-arm-portal.md)
#### [PowerShell](virtual-network-manage-nsg-arm-ps.md)
#### [CLI](virtual-network-manage-nsg-arm-cli.md)
#### [Logs](virtual-network-nsg-manage-log.md)
### Interfaces de rede (NICs)
#### [Criar, alterar ou excluir NICs](virtual-network-network-interface.md)
#### [Adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md)
### Máquinas virtuais
#### [Mover uma VM para uma sub-rede diferente](virtual-networks-move-vm-role-to-subnet.md)
### [Endereços IP públicos](virtual-network-public-ip-address.md)

## Solucionar problemas
### Grupos de segurança de rede
#### [Portal](virtual-network-nsg-troubleshoot-portal.md)
#### [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
### Rotas
#### [Portal](virtual-network-routes-troubleshoot-portal.md)
#### [PowerShell](virtual-network-routes-troubleshoot-powershell.md)
### [Teste de taxa de transferência](virtual-network-bandwidth-testing.md)
### [Não é possível excluir as redes virtuais](virtual-network-troubleshoot-cannot-delete-vnet.md)

# Referência
## [PowerShell (Gerenciador de Recursos)](/powershell/module/azurerm.network)
## [PowerShell (Clássico)](/powershell/module/azure/)
## [CLI do Azure](/cli/azure/network)
## [Java](/java/api/)
## [REST (Gerenciador de Recursos)](https://msdn.microsoft.com/library/mt163658.aspx)
## [REST (Clássico)](https://msdn.microsoft.com/library/jj157182.aspx)


# Relacionados
## [Máquinas virtuais](/azure/virtual-machines/)
## [Application Gateway](/azure/application-gateway/)
## [DNS do Azure](/azure/dns/)
## [Gerenciador de Tráfego](/azure/traffic-manager/)
## [Balanceador de Carga](/azure/load-balancer/)
## [Gateway de VPN](/azure/vpn-gateway/)
## [ExpressRoute](/azure/expressroute/)

# Recursos
## [Roteiro do Azure](https://azure.microsoft.com/roadmap/?category=networking)
## [Blog de rede](http://azure.microsoft.com/blog/topics/networking)
## [Fórum de rede](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [Preços](https://azure.microsoft.com/pricing/details/virtual-network)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-virtual-network)
