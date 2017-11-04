---
title: "Visão geral do OpenShift no Azure | Microsoft Docs"
description: "Visão geral do OpenShift no Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: f9641b52db91a4356f6d5789a8cd78a6bb3da02b
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2017
---
# <a name="openshift-overview"></a>Visão geral do OpenShift

O OpenShift é uma plataforma de aplicativo de contêiner aberta e extensível que traz docker e Kubernetes para a empresa.  

O OpenShift inclui Kubernetes para gerenciamento e orquestração do contêiner. Ele adiciona ferramentas concentradas no desenvolvedor e em operações que permitem:

- Desenvolvimento rápido de aplicativos
- Implantação e dimensionamento fáceis
- Manutenção de ciclo de vida de longo prazo para equipes e aplicativos

Há várias ofertas do OpenShift e duas estão disponíveis para execução no Azure.

- Origem do OpenShift
- Plataforma do Contêiner do OpenShift
- OpenShift Online
- OpenShift Dedicado

Das quatro ofertas abordadas, duas estão disponíveis para que os clientes as implantem no Azure por conta própria - Origem do OpenShift e Plataforma de Contêiner do OpenShift.

## <a name="openshift-origin"></a>Origem do OpenShift

O projeto upstream de [software livre](https://www.openshift.org/) do OpenShift tem o suporte da comunidade. A origem pode ser instalada em CentOS ou RHEL.

## <a name="openshift-container-platform"></a>Plataforma do Contêiner do OpenShift

Versão pronta para a empresa ([oferta comercial](https://www.openshift.com)) do Red Hat com suporte da Red Hat. O cliente adquire os direitos necessários para a Plataforma de Contêiner OpenShift e é responsável pela instalação e o gerenciamento de toda a infraestrutura.

Como o cliente é "dono" de toda a plataforma, pode instalar em seus datacenters locais, nuvem pública (Azure, AWS, Google etc.), entre outros.

## <a name="openshift-online"></a>OpenShift Online

OpenShift **multilocatário** gerenciado da Red Hat (usando a Plataforma de Contêiner). O Red Hat gerencia toda a infraestrutura subjacente (máquinas virtuais, cluster OpenShift, rede, armazenamento etc.). 

O cliente implanta contêineres, mas não tem nenhum controle em quais hosts os contêineres serão executados. Como ele é multilocatário, os contêineres podem ser colocalizados nos mesmos hosts de VM do que os contêineres de outros clientes. O custo é por contêiner.

## <a name="openshift-dedicated"></a>OpenShift Dedicado

OpenShift de **um locatário** gerenciado da Red Hat (usando a Plataforma de Contêiner). O Red Hat gerencia toda a infraestrutura subjacente (máquinas virtuais, cluster OpenShift, rede, armazenamento etc.). O cluster é específico para um cliente e é executado em uma nuvem pública (AWS, Google, Azure - lançamento no início de 2018). O cluster inicial inclui quatro Nós de Aplicativo para $48 mil/ano (pagamento adiantado de um ano inteiro).

## <a name="next-steps"></a>Próximas etapas

- [Configurar pré-requisitos comuns para OpenShift no Azure](./openshift-prerequisites.md)
- [Implantar a origem do OpenShift](./openshift-origin.md)
- [Implantar a plataforma de contêiner do OpenShift](./openshift-container-platform.md)
- [Tarefas pós-implantação](./openshift-post-deployment.md)
- [Solução de problemas de implantação do OpenShift](./openshift-troubleshooting.md)
