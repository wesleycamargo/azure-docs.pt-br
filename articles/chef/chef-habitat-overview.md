---
title: Usar o Habitat para implantar rapidamente seu aplicativo no Azure
description: Saiba como implantar consistentemente seu aplicativo para máquinas virtuais do Microsoft Azure e contêineres
keywords: azure, chef, devops, máquinas virtuais, visão geral, automatizar, habitat
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 2bdcd4c504822a2e60156b0ac565465e0cf23a85
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60388804"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Usar o Habitat para implantar rapidamente seu aplicativo no Azure
[Habitat](https://www.habitat.sh/) é um sistema de tempo de execução e empacotamento de aplicativo que empacota o aplicativo e sua automação em conjunto, como a unidade de implantação. Isso cria portabilidade máxima para o aplicativo, permitindo que ele seja implantado em máquinas virtuais, contêineres, diretamente em hardware ou em PaaS, sem reescrita nem reempacotamento.

Este artigo descreve os principais benefícios de usar o Habitat.

## <a name="modernize-and-move-legacy-applications"></a>Modernizar e mover aplicativos herdados
Libere os aplicativos herdados de sistemas operacionais mais antigos e middleware reempacotando-os com o Habitat. O artefato resultante é portátil e muda de plataforma facilmente para uma infraestrutura mais recente, assim como máquinas virtuais ou contêineres em execução na nuvem.

## <a name="accelerate-container-adoption"></a>Acelere a adoção do contêiner
Representando com precisão as dependências de tempo de execução, o Habitat resolve a implantação contínua de aplicativos complexos e orientados a microsserviços. Vá além da simples implantação azul/verde de componentes individuais e projete um comportamento de implantação sofisticado, sem gerar fluxos de orquestração complexos.

## <a name="run-any-application-anywhere"></a>Execute qualquer aplicativo em qualquer lugar
Com o Habitat, os aplicativos podem ser executados sem modificações em qualquer ambiente de tempo de execução. Isso inclui tudo de bare-metal e máquinas virtuais a sistemas de PaaS (como Docker), sistemas de gerenciamento de cluster (como Mesosphere ou Kubernetes) e contêineres (como o Pivotal Cloud Foundry).

## <a name="integrate-into-the-chef-devops-workflow"></a>Integrar o fluxo de trabalho do Chef DevOps
O projeto do Habitat é um de um projeto de software livre da Chef Software, com uma forte comunidade de suporte. O Habitat aproveita a experiência do Chef em automação de infraestrutura para colocar os recursos de automação sem precedentes em aplicativos. A Chef oferece suporte comercial para o Habitat e cria uma integração perfeita entre o Habitat e o Chef Automate, para automatizar totalmente o ciclo de versão do aplicativo do desenvolvimento à implantação.

## <a name="next-steps"></a>Próximas etapas

* [Experimente o Habitat](https://www.habitat.sh/learn/)
