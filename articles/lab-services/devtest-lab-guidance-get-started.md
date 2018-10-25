---
title: Introdução ao Azure DevTest Labs
description: Este artigo oferece os principais cenários para usar o Azure DevTest Labs e dois caminhos gerais para começar a usar o serviço em sua organização.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: spelluru
ms.openlocfilehash: 1d00dbe23fbd724893ba2b964fc54a3ea9066dcb
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48250694"
---
# <a name="get-started-with-using-azure-devtest-labs"></a>Introdução ao Azure DevTest Labs
Depois de decidir explorar o DevTest Labs, há dois caminhos gerais: implantação de prova de conceito versus implantação dimensionada. 

Uma **implantação dimensionada** consiste em semanas/meses de revisão e planejamento com a intenção de implantar o DevTest Labs em toda a empresa que possui centenas ou milhares de desenvolvedores. 

Uma **implantação de prova de conceito** tem como foco um esforço concentrado de uma única equipe para estabelecer o valor organizacional. Embora possa ser tentador pensar em uma implantação dimensionada, a abordagem tende a falhar com mais frequência do que a opção de prova de conceito. Portanto, recomendamos que você comece pelo mais simples, aprenda com a primeira equipe, repita a mesma abordagem com outras duas ou três equipes e planeje uma implantação dimensionada com base no conhecimento adquirido. Para que sua prova de conceito seja bem-sucedida, recomendamos escolher uma ou duas equipes e identificar seus cenários (ambiente de desenvolvimento versus ambientes de teste), documentar seus casos de uso atuais e implantar o DevTest Labs. 

## <a name="scenarios"></a>Cenários
Há três cenários principais para uma implementação do DevTest Labs: 

- Áreas de trabalho de desenvolvedor
- Ambientes de teste
- Laboratórios/treinamento/hackathon

## <a name="developer-desktops"></a>Áreas de trabalho de desenvolvedor
Os desenvolvedores geralmente têm requisitos diferentes para os computadores de desenvolvimento de projetos diferentes. Com o DevTest Labs, os desenvolvedores podem ter acesso a VMs sob demanda que são pré-configuradas para atender seus cenários mais comuns. O DevTest Labs oferece os seguintes benefícios:

- As organizações podem fornecer ambientes comuns de desenvolvimento e teste, garantindo consistência entre as equipes
- Os desenvolvedores podem provisionar rapidamente seus computadores de desenvolvimento sob demanda
- Os desenvolvedores podem provisionar recursos em forma de autosserviço sem precisar de permissões no nível da assinatura
- A TI ou os administradores podem predefinir a topologia de rede e os desenvolvedores podem aproveitá-la diretamente, de forma simples e intuitiva, sem exigir acessos especiais
- Os desenvolvedores podem personalizar facilmente seus computadores de desenvolvimento sempre que for necessário
- Os administradores podem controlar os custos garantindo que:
    - Os desenvolvedores não podem utilizar mais VMs do que o necessário para o desenvolvimento
    - As VMs ficam fechadas quando não estão em uso
    - Elas permitem apenas um subconjunto de tamanhos de instância de VM para as cargas de trabalho específicas

## <a name="test-environments"></a>Ambientes de teste
Criar e gerenciar ambientes de teste em toda a empresa pode exigir um esforço significativo. Com o DevTest Labs, os ambientes de teste podem ser facilmente criados, atualizados ou duplicados e isso permite que as equipes acessem um ambiente totalmente configurado quando necessário. Neste cenário, o DevTest Labs oferece os seguintes benefícios:

- Os testadores podem testar a última versão de seus aplicativos provisionando ambientes Windows e Linux rapidamente com modelos e artefatos reutilizáveis.
- Os testadores podem escalar verticalmente seu teste de carga provisionando vários agentes de teste
- Os administradores podem conectar o laboratório ao VSTS para habilitar cenários de DevOps
- Os administradores podem controlar os custos garantindo que:
    - Os testadores não podem obter mais VMs do que o necessário
    - As VMs ficam fechadas quando não estão em uso
    - Elas permitem apenas um subconjunto de tamanhos de instância de VM para as cargas de trabalho específicas

## <a name="labs-workshops-trainings-and-hackathons"></a>Laboratórios, workshops, treinamentos e hackathons  
Um laboratório no Azure DevTest Labs funciona como um ótimo contêiner para atividades transitórias, como workshops, laboratórios práticos, treinamento ou hackathons. O serviço permite que você crie um laboratório onde pode fornecer modelos personalizados que cada estagiário pode usar para criar ambientes idênticos e isolados para treinamento. Você pode aplicar políticas para garantir que os ambientes de treinamento estejam disponíveis para cada estagiário apenas quando necessário e contém recursos suficientes, como máquinas virtuais, para o treinamento. Por fim, você pode compartilhar facilmente o laboratório com estagiários, que podem acessá-lo com apenas um clique. Após a conclusão da aula, excluir o laboratório e todos os recursos relacionados é uma tarefa simples.


## <a name="next-steps"></a>Próximas etapas
Confira o próximo artigo desta série: [Escalar verticalmente sua implantação do DevTest Labs](devtest-lab-guidance-scale.md)
