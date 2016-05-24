<properties
	pageTitle="Technical Deep Dive on Platform supported migration from Classic to Azure Resource Manager (Análise técnica aprofundada sobre a migração com suporte da plataforma do Clássico para o Azure Resource Manager)"
	description="Este artigo faz um detalhamento técnico dos recursos do serviço de migração com suporte da plataforma, do Gerenciamento de Serviços para o Azure Resource Manager"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="mahthi"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="mahthi"/>

# Technical Deep Dive on Platform supported migration from Classic to Azure Resource Manager (Análise técnica aprofundada sobre a migração com suporte da plataforma do Clássico para o Azure Resource Manager)

Neste artigo, fazemos um detalhamento técnico da migração em um nível de recursos para ajudar você a entender como a plataforma migra os recursos do Clássico para o Resource Manager. Leia o artigo de anúncio do serviço - [Migração de recursos de IaaS do clássico para o Azure Resource Manager oferece suporte para plataforma](virtual-machines-windows-migration-classic-resource-manager.md).

## Diretrizes detalhadas sobre migração no nível de recurso

Você pode encontrar a representação clássica e do gerenciador de recurso dos recursos abaixo. No momento, não há suporte para os recursos não listados nesta tabela.

| Representação Clássica | Representação do Resource Manager | Anotações detalhadas | | |
|--------------------------------------------------------|-------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|---|
| Nome do serviço de nuvem | Nome DNS | Durante a migração, criaremos um novo grupo de recursos para cada serviço de nuvem com o padrão de nomenclatura - `<cloudservicename>-migrated` que conterá todos os recursos. O Nome do Serviço de Nuvem torna-se um Nome DNS associado com o Endereço IP Público | | |
| Máquina Virtual | Máquina Virtual | As propriedades específicas da VM serão migradas como estão. Observe que determinadas informações de osProfile, como o nome do computador, não foram armazenadas no modelo de implantação clássico e, portanto, permanecerão vazias após a migração. | | |
| Recursos de disco anexados à VM | Discos implícitos anexados à VM | Os discos não são modelados como recursos de nível superior no modelo de implantação do gerenciador de recursos. Eles serão migrados como discos implícitos na VM. Neste momento, só há suporte para discos anexados a uma VMl. A migração de discos não associados terão suporte em breve. Para habilitar a migração, as contas de armazenamento clássicas agora podem ser usadas pelas VMs do gerenciador de recursos. Dessa forma, isso permite que os discos sejam facilmente migrados para o modelo do gerenciador de recursos sem qualquer atualização. | | |
| Extensões de VM | Extensões de VM | Todas as extensões de recurso, exceto as extensões XML, serão migradas do modelo de implantação clássico. | | |
| Certificados de Máquina Virtual | Certificados no Cofre da Chave do Azure | Se um serviço de nuvem contiver certificados de serviço, a plataforma criará um novo cofre da chave do Azure por serviço de nuvem e moverá os certificados para o cofre da chave. As VMs serão atualizadas para referenciar os certificados do cofre da chave. | | |
| Configuração do WinRM | Configuração do WinRM em osProfile | A configuração do WinRM será movida como está como parte da migração. | | |
| Propriedade do Conjunto de Disponibilidade | Recurso do Conjunto de Disponibilidade | A especificação Conjunto de Disponibilidade era uma propriedade na VM no modelo de implantação clássico. Os Conjuntos de Disponibilidade se tornarão um recurso de nível superior como parte da migração. Observe que não há suporte para a seguinte configuração - 'vários conjuntos de disponibilidade por serviço de nuvem' ou 'um ou mais conjuntos de disponibilidade junto com VMs que não estejam em qualquer conjunto de disponibilidade em um serviço de nuvem'. | | |
| Configuração de rede em uma VM | Interface de rede principal | A Configuração de Rede em uma VM agora será representada como o recurso de interface de rede principal após a migração. Para as VMs que não estejam em uma Rede Virtual, o endereço IP interno será alterado durante a migração. | | |
| Várias interfaces de rede em uma VM | Interfaces de Rede | Se uma VM tiver várias interfaces de rede associadas, cada interface de rede se tornará um recurso de nível superior como parte da migração no modelo de implantação do gerenciador de recursos, juntamente com todas as propriedades. | | |
| Conjuntos de pontos de extremidade com balanceamento de carga | Balanceador de carga | No modelo de implantação clássico, a plataforma atribuía um balanceador de carga implícito para cada Serviço de Nuvem. Durante a migração, criaremos um novo recurso de balanceador de carga e o conjunto de pontos de extremidade de balanceamento de carga se tornará as regras do balanceador de carga. | | |
| Regras NAT de Entrada | Regras NAT de Entrada | Os Pontos de Extremidade de Entrada definidos na VM serão convertidos em Regras NAT de Entrada sob o balanceador de carga durante a migração. | | |
| VIP ou Endereço IP Virtual | Endereço IP Público com o Nome DNS | O VIP ou o Endereço IP Virtual se tornará um Endereço IP público e será associado ao balanceador de carga. | | |
| Rede Virtual | Rede Virtual | A Rede Virtual será migrada com todas as propriedades para o modelo de implantação do gerenciador de recursos. Será criado um novo grupo de recursos com o nome `-migrated`. Consulte as configurações sem suporte [aqui](virtual-machines-windows-migration-classic-resource-manager.md) | | |
| IPs Reservados | Endereço IP Público com allocationMethod estático | O IP Reservado associado ao balanceador de carga é migrado em conjunto com a migração do serviço de nuvem ou da máquina virtual. No momento, não há suporte para a migração de IP reservado não associado. | | |
| Endereço IP Público por VM | Endereço IP Público com Allocationmethod dinâmico | O Endereço IP Público associado à máquina virtual é convertido como um recurso de endereço IP público com o método de alocação definido como estático. | | |
| Grupos de segurança de rede | Grupos de segurança de rede | Os NSGs associados a uma sub-rede são clonados como parte da migração para o modelo de implantação do gerenciador de recursos. Observe que o NSG no modelo de implantação clássico não é removido durante a migração. No entanto, as operações do plano de gerenciamento para o NSG serão bloqueadas quando a migração estiver em andamento. | | |
| Servidores DNS | Servidores DNS | Os Servidores DNS associados a uma Rede Virtual ou à VM são migrados como parte da migração do recurso correspondente, juntamente com todas as propriedades. | | |
| Rotas definidas pelo usuário | Rotas definidas pelo usuário | As UDRs associadas a uma sub-rede são clonadas como parte da migração para o modelo de implantação do resource manager. Observe que a UDR no modelo de implantação clássico não é removida durante a migração. No entanto, as operações do plano de gerenciamento para a UDR serão bloqueadas quando a migração estiver em andamento. | | |
| Propriedade de encaminhamento IP em uma configuração de rede da VM | Propriedade de encaminhamento IP na NIC | A propriedade de encaminhamento IP em uma VM é convertida em uma propriedade na interface de rede durante a migração | | |
| Balanceador de carga com vários IPs | Balanceador de carga com vários recursos IP públicos | Cada IP público associado ao balanceador de carga será convertido em um recurso IP público e associado com o balanceador de carga após a migração. | | |
| Nomes DNS internos na VM | Nomes DNS internos na NIC | Durante a migração, os sufixos DNS internos serão atualizados para corresponderem aos sufixos do modelo de implantação do gerenciador de recursos. Observe que a resolução de nome continuará a funcionar após a migração. No entanto, os sufixos associados às VMs serão alterados. | | |

## Ilustração de exemplo de um passo a passo de migração simples

Nas capturas de tela de exemplo mostradas abaixo, você verá a representação de um Serviço de Nuvem com uma VM (não em uma Rede Virtual) após a fase de Preparação.

![Captura de tela que mostra a representação do Clássico após o preparo](./media/virtual-machines-windows-migration-classic-resource-manager/classic-migration-prepare-portal.png) ![Captura de tela que mostra a representação do Resource Manager após o preparo](./media/virtual-machines-windows-migration-classic-resource-manager/resourcemanager-migration-prepare-portal.png)

## Próximas etapas

Agora que você compreende a migração de recursos de IaaS do Clássico para o Resource Manager, poderá começar a migrar os recursos.

- [Usar o PowerShell para migrar recursos de IaaS do Clássico para o Azure Resource Manager](virtual-machines-windows-ps-migration-classic-resource-manager.md)
- [Usar a CLI para migrar recursos de IaaS do Clássico para o Azure Resource Manager](virtual-machines-linux-cli-migration-classic-resource-manager.md)
- [Platform supported migration of IaaS resources from Classic to Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md) (Migração de recursos de IaaS com suporte da plataforma do Clássico para o Azure Resource Manager)
- [Clonar uma máquina virtual clássica para o Azure Resource Manager usando scripts da comunidade do PowerShell](virtual-machines-windows-migration-scripts.md)

<!---HONumber=AcomDC_0511_2016-->