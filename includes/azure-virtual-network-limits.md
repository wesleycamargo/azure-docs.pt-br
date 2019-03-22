---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: networking
author: jimdial
ms.service: networking
ms.topic: include
ms.date: 02/07/2019
ms.author: jdial
ms.custom: include file
ms.openlocfilehash: c6c57390e0a2fba0c79d3198df0f5577eb813f88
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553229"
---
<a name="virtual-networking-limits-classic"></a>Os limites a seguir aplicam-se apenas a recursos de rede gerenciados pelo modelo de implantação clássico por assinatura. Saiba como [exibir seu uso de recursos atual em relação a seus limites de assinatura](../articles/networking/check-usage-against-limits.md).

| Recurso | Limite padrão | Limite máximo |
| --- | --- | --- |
| Redes Virtuais |50 |100 |
| Sites de redes locais |20 |Entre em contato com o suporte. |
| Servidores DNS por rede virtual |20 |20 |
| Endereços IP privados por rede virtual |4.096 |4.096 |
| Fluxos TCP ou UDP simultâneos por NIC de uma máquina virtual ou instância de função |500.000 até 1.000.000 para duas ou mais NICs. |500.000 até 1.000.000 para duas ou mais NICs. |
| Grupos de segurança de rede (NSG) |100 |200 |
| Regras de NSG por NSG |200 |1.000 |
| Tabelas de rotas definidas pelo usuário |100 |200 |
| Rotas definidas pelo usuário por tabela de rotas |100 |400 |
| Endereços IP públicos (dinâmicos) |5 |Entre em contato com o suporte. |
| Endereços IP públicos reservados |20 |Entre em contato com o suporte. |
| VIP público por implantação |5 |Entre em contato com o suporte. |
| VIP privado (balanceamento de carga interno) por implantação |1 |1 |
| Listas de controle de acesso do ponto de extremidade (ACLs) |50 |50 |

#### <a name="azure-resource-manager-virtual-networking-limits"></a>Limites de rede – Azure Resource Manager
Os limites a seguir aplicam-se apenas a recursos de rede gerenciados pelo Azure Resource Manager por região e assinatura. Saiba como [exibir seu uso de recursos atual em relação a seus limites de assinatura](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Recentemente, aumentamos todos os limites padrão para seus limites máximos. Se não houver nenhuma coluna de limite máximo, o recurso não tem limites ajustáveis. Se você tivesse esses limites aumentados pelo suporte no passado e não vir os limites atualizados nas tabelas a seguir, [abrir uma solicitação de suporte do cliente online sem custo adicional](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| Recurso | Limite padrão | 
| --- | --- |
| Redes Virtuais |1.000 |
| Sub-redes por rede virtual |3.000 |
| Emparelhamentos de rede virtual por rede virtual |100 |
| Servidores DNS por rede virtual |20 |
| Endereços IP privados por rede virtual |65,536 |
| Endereços IP privados por interface de rede |256 |
| Endereços IP privados por máquina virtual |256 |
| Fluxos TCP ou UDP simultâneos por NIC de uma máquina virtual ou instância de função |500,000 |
| Placas de interface de rede |65,536 |
| Grupos de segurança de rede |5.000 |
| Regras de NSG por NSG |1.000 |
| Endereços e intervalos IP especificados para a origem ou o destino em um grupo de segurança |4.000 |
| Grupos de segurança do aplicativo |3.000 |
| Grupos de segurança do aplicativo por configuração de IP, por NIC |20 |
| Configurações de IP por grupo de segurança do aplicativo |4.000 |
| Os grupos de segurança do aplicativo que podem ser especificados em todas as regras de segurança de um grupo de segurança de rede |100 |
| Tabelas de rotas definidas pelo usuário |200 |
| Rotas definidas pelo usuário por tabela de rotas |400 |
| Certificados de raiz de ponto a site por Gateway de VPN do Azure |20 |
| TAPs de rede virtual |100 |
| Configurações de TAP do adaptador de rede por TAP de rede virtual |100 |

#### <a name="publicip-address"></a>Limites do endereço IP público
| Recurso | Limite padrão | Limite máximo |
| --- | --- | --- |
| Endereços IP públicos - dinâmicos | 1.000 para Basic. |Entre em contato com o suporte. |
| Endereços IP públicos - estáticos | 200 para Basic. |Entre em contato com o suporte. |
| Endereços IP públicos - estáticos | 200 para Standard.|Entre em contato com o suporte. |
| Tamanho de prefixo IP público (visualização) | / 28 | / 28 |

#### <a name="load-balancer"></a>Limites do balanceador de carga
Os limites a seguir aplicam-se apenas a recursos de rede gerenciados pelo Azure Resource Manager por região e assinatura. Saiba como [exibir seu uso de recursos atual em relação a seus limites de assinatura](../articles/networking/check-usage-against-limits.md).

| Recurso | Limite padrão |
| --- | --- |
| Balanceadores de carga | 1.000 | 
| Regras por recurso, Básico | 250 |
| Regras por recurso, Standard | 1.500 | 
| Regras por configuração de IP | 299 |
| Regras por NIC | 500 |
| Configurações de IP de front-end, básico | 200 |
| Configurações de IP de front-end, Standard | 600 |
| Pool de back-end, básico | 100, único conjunto de disponibilidade |
| Pool de back-end, Standard | 1.000, única rede virtual |
| Recursos de back-end por balanceador de carga Standard<sup>1</sup> | 150 |
| Portas de alta disponibilidade, Standard | 1 por interno front-end |

<sup>1</sup>o limite é de até 150 recursos, em qualquer combinação de recursos da máquina virtual autônoma, o conjunto de disponibilidade, recursos e recursos do conjunto de dimensionamento de máquina virtual.

