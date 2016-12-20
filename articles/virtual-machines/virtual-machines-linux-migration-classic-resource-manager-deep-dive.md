---
title: "Análise técnica aprofundada sobre a migração com suporte da plataforma do clássico para o Azure Resource Manager | Microsoft Docs"
description: "Este artigo faz uma análise técnica detalhada sobre a migração de recursos com suporte da plataforma do clássico para o Azure Resource Manager"
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 29267453-f894-4180-bb67-dce2a0e062bb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/12/2016
ms.author: kasing
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: fc778c8b78a20cbcd9f3988ab28b763ab1c62f48


---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>Análise técnica aprofundada sobre a migração com suporte da plataforma do clássico para o Azure Resource Manager
Neste artigo, fazemos uma análise técnica da migração em nível de recursos para ajudá-lo a entender como a plataforma migra os recursos do modelo de implantação clássica para modelo de implantação do Azure Resource Manager. Para obter mais informações, leia o artigo de comunicado do serviço - [Migração de recursos de IaaS com suporte da plataforma do clássico para o Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md).

## <a name="detailed-guidance-on-migration-at-a-resource-and-feature-level"></a>Diretrizes detalhadas sobre a migração em nível de recursos
Você pode encontrar as representações do clássico e do Gerenciador de Recursos dos recursos na tabela abaixo. No momento, não há suporte para os recursos não listados nesta tabela.

| Representação do clássico | Representação do Gerenciador de Recursos | Anotações detalhadas |
| --- | --- | --- |
| Nome do serviço de nuvem |Nome DNS |Durante a migração, criamos um novo grupo de recursos para cada serviço de nuvem com o padrão de nomenclatura `<cloudservicename>-migrated`. Esse grupo de recursos contém todos os recursos. O nome do serviço de nuvem torna-se um nome DNS associado ao endereço IP público. |
| Máquina virtual |Máquina virtual |As propriedades específicas da VM são migradas sem alterações. Observe que determinadas informações de osProfile, como nome do computador, não foram armazenadas no modelo de implantação clássica e permanecem vazias após a migração. |
| Recursos de disco anexados à VM |Discos implícitos anexados à VM |Os discos não são modelados como recursos de nível superior no modelo de implantação do Gerenciador de Recursos. Eles são migrados como discos implícitos na VM. No momento, há suporte apenas aos discos anexados a uma VM. Para habilitar a migração, as VMs do Gerenciador de Recursos agora podem usar contas de armazenamento clássico. Isso permite que os discos sejam facilmente migrados para o modelo do Gerenciador de Recursos sem nenhuma atualização. |
| Extensões de VM |Extensões de VM |Todas as extensões de recurso, exceto as extensões XML, são migradas do modelo de implantação clássica. |
| Certificados de máquina virtual |Certificados no Cofre da Chave do Azure |Se um serviço de nuvem contiver certificados de serviço, a plataforma criará um novo cofre de chaves do Azure por serviço de nuvem e moverá os certificados para o cofre de chaves. As VMs serão atualizadas para fazer referência aos certificados por meio do cofre de chaves. |
| Configuração do WinRM |Configuração do WinRM em osProfile |A configuração do Gerenciamento Remoto do Windows é movida sem alterações, como parte da migração. |
| Propriedade do conjunto de disponibilidade |Recurso do conjunto de disponibilidade |A especificação do conjunto de disponibilidade era uma propriedade da VM no modelo de implantação clássica. Os conjuntos de disponibilidade se tornam um recurso de nível superior como parte da migração. Observe que não há suporte para a seguinte configuração: vários conjuntos de disponibilidade por serviço de nuvem ou um ou mais conjuntos de disponibilidade junto com VMs que não estão em nenhum conjunto de disponibilidade em um serviço de nuvem. |
| Configuração de rede em uma VM |Interface de rede primária |A configuração de rede em uma VM é representada como o recurso de interface de rede primária após a migração. Para as VMs que não estão em uma rede virtual, o endereço IP interno é alterado durante a migração. |
| Várias interfaces de rede em uma VM |Interfaces de rede |Se uma VM tiver várias interfaces de rede associadas, cada interface de rede se tornará um recurso de nível superior como parte da migração no modelo de implantação do Gerenciador de Recursos, juntamente com todas as propriedades. |
| Conjunto de pontos de extremidade com balanceamento de carga |Balanceador de carga |No modelo de implantação clássica, a plataforma atribuía um balanceador de carga implícito a cada serviço de nuvem. Durante a migração, criamos um novo recurso de balanceador de carga e o conjunto de pontos de extremidade com balanceamento de carga se torna as regras do balanceador de carga. |
| Regras NAT de entrada |Regras NAT de entrada |Os pontos de extremidade de entrada definidos na VM são convertidos em regras de Network Access Translation de entrada sob o balanceador de carga durante a migração. |
| Endereço VIP |Endereço IP público com o nome DNS |O endereço IP virtual se torna um endereço IP público e é associado ao balanceador de carga. |
| Rede virtual |Rede virtual |A rede virtual é migrada com todas as propriedades para o modelo de implantação do Gerenciador de Recursos. É criado um novo grupo de recursos com o nome `-migrated`. Observe as [configurações sem suporte](virtual-machines-windows-migration-classic-resource-manager.md). |
| IPs Reservados |Endereço IP público com método de alocação estático |Os IPs Reservados associados ao balanceador de carga são migrados, juntamente com a migração do serviço de nuvem ou da máquina virtual. No momento, não há suporte para a migração de IP reservado não associado. |
| Endereço IP público por VM |Endereço IP público com método de alocação dinâmico |O endereço IP público associado à VM é convertido como um recurso de endereço IP público, com o método de alocação definido como estático. |
| NSGs |NSGs |Os grupos de segurança de rede associados a uma sub-rede são clonados como parte da migração para o modelo de implantação do Gerenciador de Recursos. Observe que o NSG no modelo de implantação clássica não é removido durante a migração. No entanto, as operações do plano de gerenciamento referentes ao NSG serão bloqueadas quando a migração estiver em andamento. |
| Servidores DNS |Servidores DNS |Os servidores DNS associados a uma rede virtual ou à VM são migrados como parte da migração do recurso correspondente, juntamente com todas as propriedades. |
| UDRs |UDRs |As rotas definidas pelo usuário associados a uma sub-rede são clonadas como parte da migração para o modelo de implantação do Gerenciador de Recursos. Observe que a UDR no modelo de implantação clássica não é removida durante a migração. No entanto, as operações do plano de gerenciamento referentes à UDR serão bloqueadas quando a migração estiver em andamento. |
| Propriedade de encaminhamento IP em uma configuração de rede da VM |Propriedade de encaminhamento IP na NIC |A propriedade de encaminhamento IP em uma VM é convertida em uma propriedade na interface de rede durante a migração. |
| Balanceador de carga com vários IPs |Balanceador de carga com vários recursos IP públicos |Cada IP público associado ao balanceador de carga é convertido em um recurso IP público e associado ao balanceador de carga após a migração. |
| Nomes DNS internos na VM |Nomes DNS internos na NIC |Durante a migração, os sufixos DNS internos das VMs são migrados para uma propriedade somente leitura chamada "InternalDomainNameSuffix" no NIC. O sufixo permanece inalterado após a migração, e a resolução da VM deve continuar a funcionar como antes. |

## <a name="illustration-of-a-simple-migration-walkthrough"></a>Ilustração de um passo a passo de migração simples
Nas capturas de tela de exemplo a seguir, é possível ver a representação de um serviço de nuvem com uma VM (não em uma rede virtual) após a fase de preparação.

![Captura de tela que mostra a representação Clássica após a preparação](./media/virtual-machines-windows-migration-classic-resource-manager/classic-migration-prepare-portal.png)
![Captura de tela que mostra a representação do Resource Manager após a preparação](./media/virtual-machines-windows-migration-classic-resource-manager/resourcemanager-migration-prepare-portal.png)

## <a name="next-steps"></a>Próximas etapas
Agora que você compreende a migração de recursos clássicos de IaaS para o Gerenciador de Recursos, poderá começar a migrar os recursos.

* [Usar o PowerShell para migrar recursos de IaaS do clássico para o Azure Resource Manager](virtual-machines-windows-ps-migration-classic-resource-manager.md)
* [Usar a CLI para migrar recursos de IaaS do clássico para o Azure Resource Manager](virtual-machines-linux-cli-migration-classic-resource-manager.md)
* [Migração de recursos de IaaS com suporte da plataforma do clássico para o Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md)
* [Clonar uma máquina virtual clássica para o Azure Resource Manager usando scripts da comunidade do PowerShell](virtual-machines-windows-migration-scripts.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Nov16_HO3-->


