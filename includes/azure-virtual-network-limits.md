---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: networking
author: jimdial
ms.service: networking
ms.topic: include
ms.date: 08/16/2018
ms.author: jdial
ms.custom: include file
ms.openlocfilehash: 673faff51c3d06e50941a6982a9f654c28598d86
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47006461"
---
<a name="virtual-networking-limits-classic"></a>Os limites a seguir aplicam-se apenas a recursos de rede gerenciados pelo modelo de implantação clássico por assinatura. Saiba como [exibir seu uso de recursos atual em relação a seus limites de assinatura](../articles/networking/check-usage-against-limits.md).

| Recurso | Limite padrão | Limite máximo |
| --- | --- | --- |
| Redes virtuais |50 |100 |
| Sites de redes locais |20 |entrar em contato com o suporte |
| Servidores DNS por rede virtual |20 |100 |
| Endereços IP privados por rede virtual |4096 |4096 |
| Fluxos TCP ou UDP simultâneos por NIC de uma máquina virtual ou instância de função |500K |500K |
| Grupos de segurança de rede (NSG) |100 |200 |
| Regras de NSG por NSG |200 |1000 |
| Tabelas de rota definida pelo usuário |100 |200 |
| Rotas definidas pelo usuário por tabela de rotas |100 |400 |
| Endereços IP públicos (dinâmicos) |5 |entrar em contato com o suporte |
| Endereços IP públicos reservados |20 |entrar em contato com o suporte |
| VIP público por implantação |5 |entrar em contato com o suporte |
| VIP (ILB) privado por implantação |1 |1 |
| ACLs (Listas de Controle de Acesso de Pontos de Extremidade) |50 |50 |

#### <a name="azure-resource-manager-virtual-networking-limits"></a>Limites de Rede – Azure Resource Manager
Os limites a seguir aplicam-se apenas a recursos de rede gerenciados pelo Azure Resource Manager por região e assinatura. Saiba como [exibir seu uso de recursos atual em relação a seus limites de assinatura](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Recentemente, aumentamos todos os limites padrão para seus limites máximos. Se não houver nenhuma coluna **Limite Máximo**, o recurso não terá limites ajustáveis. Se o suporte aumentou esses limites no passado, mas você não consegue vê-los atualizados como mostrado abaixo, [abra uma solicitação de atendimento ao cliente online sem custo adicional](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| Recurso | Limite padrão | 
| --- | --- |
| Redes virtuais |1000 |
| Sub-redes por rede virtual |3000 |
| Emparelhamentos de rede virtual por Rede Virtual |100 |
| Servidores DNS por rede virtual |25 |
| Endereços IP privados por rede virtual |65536 |
| Endereços IP privados por interface de rede |256 |
| Fluxos TCP ou UDP simultâneos por NIC de uma máquina virtual ou instância de função |500K |
| NIC (Interfaces de rede) |24.000 |
| Grupos de segurança de rede (NSG) |5.000 |
| Regras de NSG por NSG |1000 |
| Endereços e intervalos IP especificados para a origem ou o destino em um grupo de segurança |4000 |
| Grupos de segurança do aplicativo |3000 |
| Grupos de segurança do aplicativo por configuração de IP, por NIC |20 |
| Configurações de IP por grupo de segurança do aplicativo |4000 |
| Os grupos de segurança do aplicativo que podem ser especificados em todas as regras de segurança de um grupo de segurança de rede |100 |
| Tabelas de rota definida pelo usuário |200 |
| Rotas definidas pelo usuário por tabela de rotas |400 |
| Certificados raiz de ponto a site por gateway de VPN |20 |
| TAPs de rede virtual |100 |
| Configurações de TAP do adaptador de rede por TAP de rede virtual |100 |

#### <a name="publicip-address"></a>Limites do endereço IP público

| Recurso | Limite padrão | Limite máximo |
| --- | --- | --- |
| Endereços IP públicos - dinâmicos |(Básico) 200 |entrar em contato com o suporte |
| Endereços IP públicos - estáticos |(Básico) 200 |entrar em contato com o suporte |
| Endereços IP públicos - estáticos |(Standard) 200 |entrar em contato com o suporte |

#### <a name="load-balancer"></a>Limites do Load Balancer
Os limites a seguir aplicam-se apenas a recursos de rede gerenciados pelo Azure Resource Manager por região e assinatura. Saiba como [exibir o uso de recursos atual em relação aos limites de assinatura](../articles/networking/check-usage-against-limits.md)

| Recurso | Limite padrão | Limite máximo |
| --- | --- | --- |
| Balanceadores de Carga | 100 | 1000 |
| Regras por recurso, Básico | 250 | 250 |
| Regras por recurso, Standard | 1500 | 1500 |
| Regras por configuração de IP | 299 |299 |
| Configurações de IP de front-end, Básico | 10 | 200 |
| Configurações de IP de front-end, Standard | 10 | 600 |
| Pool de back-end, Básico | 100, um único conjunto de disponibilidade | 100, um único conjunto de disponibilidade |
| Pool de back-end, Standard | 1000, única VNet | 1000, única VNet |
| Recursos de back-end pelo Load Balancer, Standard* | 150 | 150 |
| Portas de alta disponibilidade, Standard | 1 por front-end interno | 1 por front-end interno |

**Até 150 recursos, qualquer combinação de máquinas virtuais autônomas, conjuntos de disponibilidade e conjuntos de dimensionamento de máquinas virtuais.

[Entre em contato com o suporte](../articles/azure-supportability/resource-manager-core-quotas-request.md ) caso precise aumentar esses limites.

