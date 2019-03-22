---
title: Visão geral do OpenShift no Azure | Microsoft Docs
description: Uma visão geral de OpenShift no Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: haroldw
ms.openlocfilehash: 826085df8d928cab0a05527be8c464af5f4e9180
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58002507"
---
# <a name="openshift-in-azure"></a>OpenShift no Azure

O OpenShift é uma plataforma de aplicativo de contêiner aberta e extensível que traz docker e Kubernetes para a empresa.  

O OpenShift inclui Kubernetes para gerenciamento e orquestração do contêiner. Ele adiciona ferramentas concentradas no desenvolvedor e em operações que permitem:

- Desenvolvimento rápido de aplicativos.
- Implantação e dimensionamento fáceis.
- Manutenção de ciclo de vida de longo prazo para equipes e aplicativos.

Há múltiplas versões disponíveis de OpenShift:

- Plataforma do Contêiner do OpenShift
- OpenShift no Azure (OpenShift totalmente gerenciado em breve no final do T1 CY2019)
- OKD (anteriormente conhecido como origem do OpenShift)
- OpenShift Dedicado
- OpenShift Online

Das cinco versões abordadas neste artigo, somente duas estão disponíveis hoje para os clientes implantarem no Azure: OpenShift Container Platform e OKD.

## <a name="openshift-container-platform"></a>Plataforma do Contêiner do OpenShift

Plataforma de contêiner é uma [versão comercial](https://www.openshift.com) pronta para empresa do e suportada pelo Red Hat. Com essa versão, o cliente adquire os direitos necessários para a Plataforma de Contêiner OpenShift e é responsável pela instalação e o gerenciamento de toda a infraestrutura.

Porque o cliente é "dono" de toda a plataforma, eles podem instalá-lo em seu datacenter local ou em uma nuvem pública (por exemplo, o Azure).

## <a name="openshift-on-azure"></a>OpenShift no Azure

O OpenShift no Azure é uma oferta totalmente gerenciada do OpenShift em execução no Azure. Esse serviço é gerenciado e suportado pela Microsoft e Red Hat em conjunto. O cluster será implantado na assinatura do Azure do cliente. O serviço está atualmente em versão prévia privada e está programado para ser GA no início do T1 CY2019. Para clientes interessados em participar da versão prévia privada, preencha o [Formulário de Nomeação](https://aka.ms/openshiftazureinterest).  Mais informações serão fornecidas na medida que a oferta ficar mais próxima a GA.

## <a name="okd-formerly-openshift-origin"></a>OKD (anteriormente conhecido como origem do OpenShift)

O OKD é um projeto upstream de [software livre](https://www.okd.io/) do OpenShift que tem o suporte da comunidade. O OKD pode ser instalado em CentOS ou RHEL (Red Hat Enterprise Linux).

## <a name="openshift-dedicated"></a>OpenShift Dedicado

Dedicado é um OpenShift *multilocatário* gerenciado da Red Hat que usa a Plataforma de Contêiner. O Red Hat gerencia toda a infraestrutura subjacente (máquinas virtuais, cluster OpenShift, rede, armazenamento etc.). O cluster é específico para um cliente e é executado em uma nuvem pública (por exemplo, o Azure). Um cluster inicial inclui quatro nós de aplicativo e todos os custos são anuais e pagos antecipadamente.

## <a name="openshift-online"></a>OpenShift Online

Online é um OpenShift *multilocatário* gerenciado da Red Hat usando a Plataforma de Contêiner. O Red Hat gerencia toda a infraestrutura subjacente (máquinas virtuais, cluster OpenShift, rede, armazenamento etc.). 

Com essa versão, o cliente implanta contêineres, mas não tem nenhum controle em quais hosts os contêineres serão executados. Como ele é multilocatário, os contêineres podem ser colocalizados nos mesmos hosts de VM do que os contêineres de outros clientes. O custo é por contêiner.

## <a name="next-steps"></a>Próximas etapas

- [Configurar pré-requisitos comuns para OpenShift no Azure](./openshift-prerequisites.md)
- [Implantar o OpenShift Container Platform no Azure](./openshift-container-platform.md)
- [Implantar OKD no Azure](./openshift-okd.md)
- [Implantar OpenShift Origin no Azure Stack](./openshift-azure-stack.md)
- [Tarefas de pós-implantação](./openshift-post-deployment.md)
- [Solução de problemas de implantação do OpenShift](./openshift-troubleshooting.md)
