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
ms.openlocfilehash: 4847d9ce551c9acf1e4fb6325c770187b2cfd89f
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54052284"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Usar o Habitat para implantar rapidamente seu aplicativo no Azure
[Habit](https://www.habitat.sh/) é o primeiro de seu projeto de código-fonte aberto tipo que oferece uma nova abordagem inteiramente para o gerenciamento do aplicativo. O Habit faz com que o aplicativo e sua automação da unidade de implantação. Quando os aplicativos são encapsulados em um “habitat” leve, o ambiente de tempo de execução, seja um contêiner, bare metal ou PaaS, não é mais o foco e não restringe o aplicativo. 

Este artigo descreve os benefícios de usar o Habitat.

## <a name="support-for-the-modern-application"></a>Suporte para o aplicativo moderno
Os pacotes do Habitat incluem tudo que um aplicativo precisa ser executado em todo o ciclo de vida. Os componentes principais do Habitat são:
- Formato de empacotamento - aplicativos em um pacote no programa Habitat são atômicos, imutáveis e auditáveis.
- O supervisor do Habitat executa os pacotes de aplicativos com reconhecimento das relações de pares dos pactes, atualizar estratégia e políticas de segurança. O supervisor do Habitat configura e gerencia o aplicativo para qualquer ambiente que esteja presente.
- O ecossistema do Habitat também fornece um serviço de compilação que usa um plano de compilação do Habitat, cria o pacote de programa Habitat e publica em um repositório.

## <a name="run-any-application-anywhere"></a>Execute qualquer aplicativo em qualquer lugar
Com o Habitat, os aplicativos podem ser executados sem modificações em qualquer ambiente de tempo de execução. O aplicativo pode ser qualquer coisa, de bare-metal e máquinas virtuais a sistemas de PaaS (como Docker), sistemas de gerenciamento de cluster (como Mesosphere ou Kubernetes) e contêineres (como o Pivotal Cloud Foundry).

## <a name="easily-port-legacy-applications"></a>Aplicativos herdados da porta facilmente
Quando os aplicativos herdados são encapsulados em um pacote do Habitat, os aplicativos são independentes do ambiente para o qual foram originalmente projetados. Os pacotes podem ser movidos rapidamente para ambientes mais modernos, como a nuvem ou contêineres. Além disso, como os pacotes do Habitat têm uma interface voltada padrão e para fora, os aplicativos herdados se tornam muito mais fácil de gerenciar.

## <a name="improve-the-container-experience"></a>Melhorar a experiência de contêiner
O Habitat reduz a complexidade de gerenciar contêineres em ambientes de produção. Automatizando a configuração do aplicativo em um contêiner, o Habitat aborda o desenvolvedores que enfrentam desafios ao mover aplicativos baseados no contêiner de ambientes de desenvolvimento para produção.

## <a name="integrate-into-the-chef-devops-workflow"></a>Integrar o fluxo de trabalho do Chef DevOps
O projeto do Habitat é patrocinado pelo Chef. O Habitat aproveita a experiência do Chef em automação de infraestrutura para colocar os recursos de automação sem precedentes em aplicativos. Chef oferecerá suporte comercial para o Habitat e garantirá a integração perfeita entre o Habitat e Chef Delivery para automatizar o ciclo de versão do aplicativo do desenvolvimento à implantação.

## <a name="next-steps"></a>Próximas etapas
* [Criar uma Máquina Virtual do Windows no Azure usando o Chef](/azure/virtual-machines/windows/chef-automation)