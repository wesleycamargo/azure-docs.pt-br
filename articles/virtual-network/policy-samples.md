---
title: Exemplos de modelos de política | Microsoft Docs
description: Amostras de modelo de política do Azure para a Rede Virtual.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: ''
ms.date: 05/02/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: b0b3b65ee2360a5cec32235f2ee5bf8d4839cc8b
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2019
ms.locfileid: "56342065"
---
# <a name="azure-policy-sample-templates-for-virtual-network"></a>Modelos de exemplo de política do Azure para a rede virtual

A tabela a seguir inclui links para amostras do [Azure Policy](../governance/policy/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). As amostras são encontradas no [repositório de amostras do Azure Policy](https://github.com/Azure/azure-policy).

| | |
|---|---|
|**Rede**||
| [NSG X em cada NIC](../governance/policy/samples/nsg-on-nic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige que um grupo de segurança de rede específico seja usado com cada adaptador de rede virtual. Você especifica a ID do grupo de segurança de rede a ser usada. |
| [NSG X em cada sub-rede](../governance/policy/samples/nsg-on-subnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige que um grupo de segurança de rede específico seja usado em cada sub-rede virtual. Você especifica a ID do grupo de segurança de rede a ser usada. |
| [Sem tabela de rotas](../governance/policy/samples/no-user-defined-route-table.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  |Proíbe que redes virtuais sejam implantadas com uma tabela de rotas. |
| [Usar sub-rede aprovada para adaptadores de rede de VM](../governance/policy/samples/use-approved-subnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige que os adaptadores de rede usem uma sub-rede aprovada. Especifique a ID da sub-rede aprovada. |
| [Usar vNet aprovada para adaptadores de rede de VM](../governance/policy/samples/use-approved-vnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige que os adaptadores de rede usem uma rede virtual aprovada. Especifique a ID da rede virtual aprovada. |
|**Monitoramento**||
| [Configuração de diagnóstico de auditoria](../governance/policy/samples/audit-diagnostic-setting.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Audita se as configurações de diagnóstico não estão habilitadas para tipos de recursos especificados. Você especifica uma matriz de tipos de recursos para verificar se as configurações de diagnóstico estão habilitadas. |
|**Convenções de nome e texto**||
| [Permite vários padrões de nome](../governance/policy/samples/allow-multiple-name-patterns.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Permite um dos vários padrões de nome a ser usado para recursos. |
| [Exigir padrão de curtir](../governance/policy/samples/enforce-like-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Verifique se os nomes dos recursos atendem à condição *like* de um padrão. |
| [Exigir padrão de correspondência](../governance/policy/samples/enforce-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Verifique se os nomes de recursos correspondem a um padrão de nomenclatura especificado. |
| [Exigir padrão de correspondência de marcas](../governance/policy/samples/enforce-tag-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Certifique-se de que um valor de marca corresponde a um padrão de texto. |
|**Marcas**||
| [Iniciativa de política de marcas de cobrança](../governance/policy/samples/billing-tags-policy-initiative.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige valores de marca especificados para o nome do produto e o centro de custo. Usa políticas internas para aplicar e impor as marcas necessárias. Especifique os valores necessários para as marcas.  |
| [Impor marca e seu valor em grupos de recursos](../governance/policy/samples/enforce-tag-on-resource-groups.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige uma marca e um valor em um grupo de recursos. Especifique o nome e o valor da marca exigidos.  |
| [Impor marca e seu valor](../governance/policy/samples/enforce-tag-value.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige um nome e um valor de marca especificados. Especifique o nome e o valor da marca a serem impostos.  |
| [Aplicar marca e seu valor padrão](../governance/policy/samples/apply-tag-default-value.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Acrescentará um nome e um valor de marca especificados se a marca não for indicada. Especifique o nome e o valor da marca a serem aplicados.  |
|**Geral**||
| [Locais permitidos](../governance/policy/samples/allowed-locations.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige que todos os recursos sejam implantados em locais aprovados. Especifique uma matriz de locais aprovados.  |
| [Tipos de recursos permitidos](../governance/policy/samples/allowed-resource-types.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Assegura que apenas os tipos de recurso aprovados sejam implantados. Especifique uma matriz de tipos de recursos permitidos.  |
| [Tipos de recursos não permitidos](../governance/policy/samples/not-allowed-resource-types.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Proíbe a implantação dos tipos de recursos especificados. Especifique uma matriz de tipos de recursos a ser bloqueada.  |