---
title: "Ferramentas da comunidade do Gerenciamento de Serviços do Azure para a migração do Azure Resource Manager"
description: "Este artigo cataloga as ferramentas que foram fornecidas pela comunidade para dar assistência com a migração de recursos de IaaS do Gerenciamento de Serviços do Azure para a pilha do Azure Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 228b697b-3950-49f5-84bb-283bb56621b1
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/29/2016
ms.author: singhkay
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 032a7068cce78036caec587bfc53abddf5a36659


---
# <a name="community-tools-for-azure-service-management-to-azure-resource-manager-migration"></a>Ferramentas da comunidade do Gerenciamento de Serviços do Azure para a migração do Azure Resource Manager
Este artigo cataloga as ferramentas que foram fornecidas pela comunidade para dar assistência com a migração de recursos de IaaS do Gerenciamento de Serviços do Azure para a pilha do Azure Resource Manager.

> [!NOTE]
> Não há suporte oficial para essas ferramentas no Suporte da Microsoft. Portanto, são software livre no Github e aceitamos PRs para correções ou cenários adicionais. Para relatar um problema, use o recurso de problemas do GitHub.
> 
> A migração com essas ferramentas causará tempo de inatividade de sua Máquina Virtual clássica. Se você estiver buscando uma migração da plataforma com suporte, visite 
> 
> * [Platform supported migration of IaaS resources from Classic to Azure Resource Manager stack (Migração de recursos de IaaS com suporte da plataforma da pilha Clássica para o Azure Resource Manager)](virtual-machines-windows-migration-classic-resource-manager.md)
> * [Análise técnica aprofundada sobre a migração com suporte da plataforma do Clássico para o Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
> * [Migrar recursos de IaaS do Clássico para o Azure Resource Manager usando o Azure PowerShell](virtual-machines-windows-ps-migration-classic-resource-manager.md)
> 
> 

## <a name="asm2arm"></a>ASM2ARM
Este é um módulo de script do PowerShell para migrar sua **única** VM (Máquina Virtual) da pilha do Gerenciamento de Serviços do Azure para a pilha do Azure Resource Manager. 

[Link para a documentação da ferramenta](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/asm2arm)

## <a name="migaz"></a>migAz
migAz é uma opção adicional para migrar um conjunto completo de recursos de IaaS do Gerenciamento de Serviços do Azure para recursos de IaaS do Azure Resource Manager. A migração pode ocorrer na mesma assinatura ou entre assinaturas e tipos de assinatura diferentes (por ex.: assinaturas do CSP).

[Link para a documentação da ferramenta](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/migaz)




<!--HONumber=Nov16_HO3-->


