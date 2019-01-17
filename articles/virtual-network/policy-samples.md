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
ms.openlocfilehash: a0153cf11c3fe817ce397b4d0c47a786a4334f39
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214803"
---
# <a name="azure-policy-sample-templates-for-virtual-network"></a>Modelos de exemplo de política do Azure para a rede virtual

A tabela a seguir inclui links para modelos de exemplo do [Azure Policy](../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json). As amostras são encontradas no [repositório de amostras do Azure Policy](https://github.com/Azure/azure-policy).

| | |
|---|---|
|**Rede**||
| [NSG X em cada NIC](../azure-policy/scripts/nsg-on-nic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige que um grupo de segurança de rede específico seja usado com cada adaptador de rede virtual. Você especifica a ID do grupo de segurança de rede a ser usada. |
| [NSG X em cada sub-rede](../azure-policy/scripts/nsg-on-subnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige que um grupo de segurança de rede específico seja usado em cada sub-rede virtual. Você especifica a ID do grupo de segurança de rede a ser usada. |
| [Sem tabela de rotas](../azure-policy/scripts/no-user-def-route-table.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  |Proíbe que redes virtuais sejam implantadas com uma tabela de rotas. |
| [Usar sub-rede aprovada para adaptadores de rede de VM](../azure-policy/scripts/use-approved-subnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige que os adaptadores de rede usem uma sub-rede aprovada. Especifique a ID da sub-rede aprovada. |
| [Usar vNet aprovada para adaptadores de rede de VM](../azure-policy/scripts/use-approved-vnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige que os adaptadores de rede usem uma rede virtual aprovada. Especifique a ID da rede virtual aprovada. |
|**Monitoramento**||
| [Configuração de diagnóstico de auditoria](../azure-policy/scripts/audit-diag-setting.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Audita se as configurações de diagnóstico não estão habilitadas para tipos de recursos especificados. Você especifica uma matriz de tipos de recursos para verificar se as configurações de diagnóstico estão habilitadas. |
|**Convenções de nome e texto**||
| [Permite vários padrões de nome](../azure-policy/scripts/allow-multiple-name-patterns.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Permite um dos vários padrões de nome a ser usado para recursos. |
| [Exigir padrão de curtir](../azure-policy/scripts/enforce-like-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Verifique se os nomes dos recursos atendem à condição *like* de um padrão. |
| [Exigir padrão de correspondência](../azure-policy/scripts/enforce-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Verifique se os nomes de recursos correspondem a um padrão de nomenclatura especificado. |
| [Exigir padrão de correspondência de marcas](../azure-policy/scripts/enforce-tag-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Certifique-se de que um valor de marca corresponde a um padrão de texto. |
|**Marcas**||
| [Iniciativa de política de marcas de cobrança](../governance/policy/samples/billing-tags-policy-initiative.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige valores de marca especificados para o nome do produto e o centro de custo. Usa políticas internas para aplicar e impor as marcas necessárias. Especifique os valores necessários para as marcas.  |
| [Impor marca e seu valor em grupos de recursos](../azure-policy/scripts/enforce-tag-rg.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige uma marca e um valor em um grupo de recursos. Especifique o nome e o valor da marca exigidos.  |
| [Impor marca e seu valor](../azure-policy/scripts/enforce-tag-val.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige um nome e um valor de marca especificados. Especifique o nome e o valor da marca a serem impostos.  |
| [Aplicar marca e seu valor padrão](../azure-policy/scripts/apply-tag-def-val.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Acrescentará um nome e um valor de marca especificados se a marca não for indicada. Especifique o nome e o valor da marca a serem aplicados.  |
|**Geral**||
| [Locais permitidos](../azure-policy/scripts/allowed-locs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige que todos os recursos sejam implantados em locais aprovados. Especifique uma matriz de locais aprovados.  |
| [Tipos de recursos permitidos](../azure-policy/scripts/allowed-res-types.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Assegura que apenas os tipos de recurso aprovados sejam implantados. Especifique uma matriz de tipos de recursos permitidos.  |
| [Tipos de recursos não permitidos](../azure-policy/scripts/not-allowed-res-type.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Proíbe a implantação dos tipos de recursos especificados. Especifique uma matriz de tipos de recursos a ser bloqueada.  |