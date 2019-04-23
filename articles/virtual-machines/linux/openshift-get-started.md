---
title: Visão geral do OpenShift no Azure | Microsoft Docs
description: Uma visão geral de OpenShift no Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/19/2019
ms.author: haroldw
ms.openlocfilehash: 53bed2131e81ee5ed0f46bde389262ee8349339a
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60006942"
---
# <a name="openshift-in-azure"></a>OpenShift no Azure

O OpenShift é uma plataforma de aplicativo de contêiner aberta e extensível que traz docker e Kubernetes para a empresa.  

O OpenShift inclui Kubernetes para gerenciamento e orquestração do contêiner. Ele adiciona ferramentas concentradas no desenvolvedor e em operações que permitem:

- Desenvolvimento rápido de aplicativos.
- Implantação e dimensionamento fáceis.
- Manutenção de ciclo de vida de longo prazo para equipes e aplicativos.

Há várias versões do OpenShift disponíveis.  Uma dessas versões, apenas dois estão disponíveis para os clientes implantem no Azure: OpenShift Container Platform e OKD (anteriormente conhecido como origem).

## <a name="openshift-container-platform"></a>Plataforma do Contêiner do OpenShift

Plataforma de contêiner é uma [versão comercial](https://www.openshift.com) pronta para empresa do e suportada pelo Red Hat. Com essa versão, o cliente adquire os direitos necessários para a Plataforma de Contêiner OpenShift e é responsável pela instalação e o gerenciamento de toda a infraestrutura.

Porque o cliente é "dono" de toda a plataforma, eles podem instalá-lo em seu datacenter local ou em uma nuvem pública (por exemplo, o Azure).

## <a name="azure-red-hat-openshift"></a>Red Hat OpenShift no Azure

Azure Red Hat OpenShift é uma oferta totalmente gerenciada do OpenShift em execução no Azure. Esse serviço é gerenciado e suportado pela Microsoft e Red Hat em conjunto. O cluster será implantado na assinatura do Azure do cliente. O serviço está programado para ser GA em torno de maio de 2019. Documentação separada para o serviço gerenciado estará disponível quando o serviço é GA.

## <a name="okd"></a>OKD

O OKD é um projeto upstream de [software livre](https://www.okd.io/) do OpenShift que tem o suporte da comunidade. O OKD pode ser instalado em CentOS ou RHEL (Red Hat Enterprise Linux).

## <a name="next-steps"></a>Próximas etapas

- [Configurar pré-requisitos comuns para OpenShift no Azure](./openshift-prerequisites.md)
- [Implantar o OpenShift Container Platform no Azure](./openshift-container-platform.md)
- [Implantar o OpenShift Container Platform Marketplace autogerenciada oferta](./openshift-marketplace-self-managed.md)
- [Implantar OpenShift Origin no Azure Stack](./openshift-azure-stack.md)
- [Tarefas de pós-implantação](./openshift-post-deployment.md)
- [Solução de problemas de implantação do OpenShift](./openshift-troubleshooting.md)
