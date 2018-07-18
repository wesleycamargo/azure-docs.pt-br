---
title: Visão geral do OpenShift no Azure | Microsoft Docs
description: Uma visão geral de OpenShift no Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: c8e740a66271c88b3abb036867d1760cc9e77607
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33944494"
---
# <a name="openshift-in-azure"></a>OpenShift no Azure

O OpenShift é uma plataforma de aplicativo de contêiner aberta e extensível que traz docker e Kubernetes para a empresa.  

O OpenShift inclui Kubernetes para gerenciamento e orquestração do contêiner. Ele adiciona ferramentas concentradas no desenvolvedor e em operações que permitem:

- Desenvolvimento rápido de aplicativos.
- Implantação e dimensionamento fáceis.
- Manutenção de ciclo de vida de longo prazo para equipes e aplicativos.

Há múltiplas versões disponíveis de OpenShift:

- Origem do OpenShift
- Plataforma do Contêiner do OpenShift
- OpenShift Online
- OpenShift Dedicado

Das quatro versões abordadas neste artigo, apenas duas estão disponíveis para que os clientes as implantem no Azure: Origem do OpenShift e Plataforma de Contêiner do OpenShift.

## <a name="openshift-origin"></a>Origem do OpenShift

Origin é um projeto upstream de [software livre](https://www.openshift.org/) do OpenShift que tem o suporte da comunidade. Origin pode ser instalada em CentOS ou Red Hat Enterprise Linux (RHEL).

## <a name="openshift-container-platform"></a>Plataforma do Contêiner do OpenShift

Plataforma de contêiner é uma [versão comercial](https://www.openshift.com) pronta para empresa do e suportada pelo Red Hat. Com essa versão, o cliente adquire os direitos necessários para a Plataforma de Contêiner OpenShift e é responsável pela instalação e o gerenciamento de toda a infraestrutura.

Como o cliente é "dono" de toda a plataforma, pode instalar em seus datacenters locais, nuvem pública (Azure, AWS, Google etc.), entre outros.

## <a name="openshift-online"></a>OpenShift Online

Online é um OpenShift *multilocatário* gerenciado da Red Hat usando a Plataforma de Contêiner. O Red Hat gerencia toda a infraestrutura subjacente (máquinas virtuais, cluster OpenShift, rede, armazenamento etc.). 

Com essa versão, o cliente implanta contêineres, mas não tem nenhum controle em quais hosts os contêineres serão executados. Como ele é multilocatário, os contêineres podem ser colocalizados nos mesmos hosts de VM do que os contêineres de outros clientes. O custo é por contêiner.

## <a name="openshift-dedicated"></a>OpenShift Dedicado

Dedicado é um OpenShift *multilocatário* gerenciado da Red Hat usando a Plataforma de Contêiner. O Red Hat gerencia toda a infraestrutura subjacente (máquinas virtuais, cluster OpenShift, rede, armazenamento etc.). O cluster é específico para um cliente e é executado em uma nuvem pública (AWS, Google, Azure - lançamento no início de 2018). Um cluster inicial inclui quatro nós de aplicativo para $48.000 por ano (pagamento adiantado).

## <a name="next-steps"></a>Próximas etapas

- [Configurar pré-requisitos comuns para OpenShift no Azure](./openshift-prerequisites.md)
- [Implantar OpenShift Origin no Azure](./openshift-origin.md)
- [Implantar o OpenShift Container Platform no Azure](./openshift-container-platform.md)
- [Tarefas de pós-implantação](./openshift-post-deployment.md)
- [Solução de problemas de implantação do OpenShift](./openshift-troubleshooting.md)
