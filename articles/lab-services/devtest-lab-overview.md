---
title: Sobre os Laboratórios de Desenvolvimento/Teste | Microsoft Azure
description: Saiba como os Laboratórios de Desenvolvimento/Teste podem facilitar criar, gerenciar e monitorar as máquinas virtuais do Azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1b9eed3b-c69a-4c49-a36e-f388efea6f39
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: 599b4325e0eb9aa7f6ccca2626c6d4b14f38149d
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339322"
---
# <a name="about-azure-devtest-labs"></a>Sobre os Laboratórios de Desenvolvimento/Teste do Azure
O Azure DevTest Labs é um serviço que permite que os desenvolvedores em uma equipe para máquinas de virtuais com eficiência Self-serviços e/ou recursos de PaaS que eles precisam para desenvolver, testar, treinamento e demonstrações etc. sem aguardar a constante aprovações sobre as ferramentas que eles precisam. 

O laboratório já consiste em bases pré-configurado ou modelos do Resource Manager com todas as ferramentas necessárias e software que os desenvolvedores podem usar para criar ambientes. Os desenvolvedores podem criar seus ambientes em poucos minutos em vez de horas ou dias. 

Usando o DevTest Labs, você pode testar a versão mais recente do seu aplicativo, fazendo as seguintes tarefas:

- Provisionar rapidamente ambientes Windows e Linux usando modelos e artefatos reutilizáveis
- Integre facilmente seu pipeline de implantação com o DevTest Labs para provisionar ambientes sob demanda
- Escalar verticalmente seu teste Provisionando vários agentes de teste de carga e crie ambientes pré-provisionados para treinamentos e demonstrações.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]
> 
> 

## <a name="capabilities"></a>Recursos
Laboratórios de desenvolvimento/teste fornece os seguintes recursos para desenvolvedores que trabalham com máquinas virtuais:

- Crie uma máquina virtual rapidamente executando menos de cinco etapas simples.
- Escolha entre uma lista estruturada de bases VM que são configuradas, aprovado e autorizado pelo líder de equipe ou central de IT.
- Crie máquinas de pré-criada imagens personalizadas que têm todos os softwares e ferramentas instaladas na imagem. 
- Crie máquinas de fórmulas que são, essencialmente, imagens personalizadas e uma compilação mais recente do software que está instalado no momento da criação da máquina virtual.
- Instale os artefatos que são extensões implantadas na VM depois que ela é provisionada.
- Configurar o desligamento automático e auto start agendas nas máquinas para ter o desligamento no final do dia e, em seguida, até e executando manhã seguinte.
- Declaração simplesmente uma máquina virtual criada previamente sem ter que passar pelo processo de criação da máquina. 

Laboratórios de desenvolvimento/teste fornece os seguintes recursos para desenvolvedores que trabalham com os ambientes de PaaS:

- Crie ambientes de PaaS do Azure Resource Manager seguindo rapidamente menos de três etapas simples.
- Escolha entre uma lista estruturada de modelos do Resource Manager, que são configuradas, aprovado e autorizado pelo líder de equipe ou central IT.
- Crie um grupo de recursos vazio (área restrita) usando um modelo do Resource Manager para explorar inteira do Azure, mantendo ainda dentro do contexto do laboratório.
- Os Laboratórios de Teste/Desenvolvimento oferecem os seguintes benefícios na criação, configuração e gerenciamento ambientes de teste e de desenvolvedor na nuvem

Além de um modelo de autoatendimento para desenvolvedores de uma equipe, o serviço central permite a TI a controlar desperdiça, otimizar os custos de recursos e permanecer dentro orçamentos, fazendo as seguintes tarefas: 

- Desligamento automático de configuração e automaticamente iniciar agendas em máquinas virtuais.
- Definir políticas no número de usuários de máquinas virtuais pode criar.
- Definir políticas sobre os tamanhos de máquinas virtuais e a Galeria de usuários de imagens pode escolher.
- Controlando os custos e os destinos de configuração de laboratório.
- Ser notificado em altos custos projetados para o laboratório, para que você pode executar as ações necessárias. 

Laboratórios de desenvolvimento/teste fornece os seguintes benefícios na criação, configuração e gerenciamento de ambientes na nuvem:

## <a name="cost-control-and-governance"></a>Governança e controle de custos
Laboratórios de desenvolvimento/teste torna mais fácil controlar os custos, permitindo que você faça as seguintes tarefas:

- Definir políticas em seu laboratório - como o número de máquinas virtuais (VM) por usuário e o número de VMs por laboratório. 
- Crie políticas para desligar e iniciar VMs automaticamente.
- Permite controlar os custos em máquinas virtuais e recursos de PaaS rotacionados em seu laboratório, para que você pode permanecer dentro do orçamento predefinido. 
- Ajuda os desenvolvedores a se manter dentro do contexto do laboratório, para que eles não acabem bagunçar todos os recursos fora na assinatura ou grupo de recursos subjacentes.

## <a name="quickly-get-to-ready-to-test"></a>Chegue rapidamente ao estado Pronto para teste
Os Laboratório de Teste/Desenvolvimento permitem que você crie ambientes pré-provisionados com tudo o que a sua equipe precisa para começar a desenvolver e testar aplicativos. Declaração simplesmente os ambientes em que a última compilação boa do seu aplicativo está instalada e comece a trabalhar imediatamente. Ou use os contêineres para uma criação de ambiente mais rápida e descomplicada.

## <a name="create-once-use-everywhere"></a>Crie uma vez, use em qualquer lugar
Capturar e compartilhar modelos de ambiente de PaaS e artefatos de sua equipe ou organização, tudo no controle do código-fonte - para criar o desenvolvedor e ambientes de teste facilmente.

## <a name="worry-free-self-service"></a>Autoatendimento sem preocupações
DevTest Labs permite que seus desenvolvedores e testadores rapidamente e facilmente cria máquinas virtuais (IaaS) e os recursos de PaaS em apenas alguns cliques usando um conjunto de recursos de pré-configurado para eles.

## <a name="use-iaas-and-paas-resources"></a>Use os recursos de IaaS e PaaS 
DevTest Labs também permite aos desenvolvedores criar recursos de PaaS, como aplicativos Web, clusters do Service Fabric, Farms do SharePoint e assim por diante. dentro do laboratório usando um modelo do Resource Manager. Usar os modelos do Gerenciador de recursos de nosso repositório público do ambiente ou conecte o laboratório a seu próprio repositório Git para começar a usar em PaaS nos laboratórios. Você também pode controlar os custos sobre esses recursos para permanecer dentro dos orçamentos predefinidos. 

## <a name="integrate-with-your-existing-toolchain"></a>Integre-se com a sua cadeia de ferramentas existente
Use o plug-ins criados previamente ou nossa API para provisionar ambientes de desenvolvimento/teste diretamente da sua ferramenta preferencial CI (integração contínua), ambiente desenvolvimento integrado (IDE) ou pipeline de liberação automatizado. Você também pode usar nossa ferramenta de linha de comando abrangente.

## <a name="next-steps"></a>Próximas etapas
Confira os seguintes artigos: 

- Para saber mais sobre o DevTest Labs, consulte [conceitos dos laboratórios de desenvolvimento/teste](devtest-lab-concepts.md)
- Para obter uma explicação com instruções passo a passo, consulte [Tutorial: Configurar um laboratório usando o Azure DevTest Labs](tutorial-create-custom-lab.md)


