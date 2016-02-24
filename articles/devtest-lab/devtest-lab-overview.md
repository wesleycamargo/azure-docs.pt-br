<properties 
	pageTitle="O que é o serviço de Laboratório de Desenvolvimento/Teste? | Microsoft Azure"
	description="Saiba como o Laboratório de Desenvolvimento/Teste pode facilitar a criar, gerenciar e monitorar as máquinas virtuais do Azure"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/30/2016"
	ms.author="tarcher"/>

#Adicionar um Laboratório de Teste de Desenvolvimento?

Os desenvolvedores e testadores estão tentando resolver os atrasos na criação e gerenciamento de seus ambientes indo para a nuvem. O Azure resolve o problema de atrasos de ambiente e permite autoatendimento dentro de uma nova estrutura com boa relação custo-benefício. No entanto, os desenvolvedores e testadores ainda precisam gastar um tempo considerável configurando seus ambientes de autoatendimento. Além disso, os tomadores de decisão não estão certos sobre como utilizar a nuvem para maximizar suas economias sem acrescentar muita sobrecarga de processo.

O Laboratório de Desenvolvimento de Teste do Azure é um serviço que ajuda os desenvolvedores e testadores a rapidamente criar ambientes no Azure, minimizando o desperdício e o controle de custos. Você pode testar a versão mais recente do seu aplicativo, provisionamento ambientes Windows e Linux rapidamente usando modelos reutilizáveis e artefatos. Integre facilmente seu pipeline de implantação do Laboratório de Teste de Desenvolvimento para provisionar ambientes sob demanda. Dimensione seu teste de carga provisionando vários agentes de teste e crie ambientes previamente provisionados para treinamento e demonstrações.

##Por que o Laboratório de Teste de Desenvolvimento?

O Laboratório de Teste de Desenvolvimento oferece os seguintes benefícios na criação, configuração e gerenciamento ambientes de teste e de desenvolvedor na nuvem

###Autoatendimento sem preocupações

O Laboratório de Teste de Desenvolvimento facilita o controle de custos, permitindo que você defina diretivas em seu laboratório - como o número de máquinas virtuais (VM) por usuário e o número de VMs por laboratório. O Laboratório de Teste de Desenvolvimento também permite que você crie políticas para desligar e iniciar automaticamente as máquinas virtuais.

###Chegue rapidamente ao estado “Pronto para teste”

O Laboratório de Teste de Desenvolvimento permite que você crie ambientes pré-provisionados com tudo o que a sua equipe precisa para começar a desenvolver e testar aplicativos. Basta declarar os ambientes onde a última compilação boa do seu aplicativo foi instalada e começar a trabalhar imediatamente. Ou use os contêineres para uma criação de ambiente mais rápida e descomplicada.

###Crie uma vez, use em qualquer lugar

Capture e compartilhe modelos e artefatos de ambiente com sua equipe ou organização, tudo no controle do código-fonte, para criar ambientes de desenvolvedor e de teste facilmente.

###Integra-se com a sua cadeia de ferramentas existente

Aproveite plug-ins pré-fabricados ou nossa API para provisionar ambientes de Desenvolvimento/Teste diretamente da sua ferramenta de CI (integração contínua), IDE (ambiente de desenvolvimento integrado) ou pipeline de liberação automatizada preferido. Você também pode usar nossa ferramenta de linha de comando abrangente.

##Conceitos de Laboratório de Teste de Desenvolvimento

A lista a seguir contém as principais definições e conceitos do laboratório de Desenvolvimento de Teste:

**Artefatos** são usados para implantar e configurar seu aplicativo após o provisionamento de uma VM. Os artefatos podem ser:

- Ferramentas que você deseja instalar na VM, por exemplo, agentes, Fiddler e Visual Studio.
- Ações que você deseja executar na VM - como clonar um repositório.
- Aplicativos que você deseja testar.

Os artefatos são o Gerenciador de Recursos do Azure (ARM) com base em arquivos JSON que contém instruções para realizar a implantação e aplicar a configuração. Você pode ler mais sobre o ARM na [Visão geral do Gerenciador de Recursos do Azure](/resource-group-overview.md).

**Repositórios de artefatos** são repositórios git onde os artefatos são verificados. Os mesmos repositórios de artefato podem ser adicionados a vários laboratórios da sua organização, permitindo a reutilização e compartilhamento.

**Base** é uma imagem de VM com todas as ferramentas e configurações pré-instaladas e configuradas para criar rapidamente uma máquina virtual. Você pode provisionar uma VM escolhendo uma base existente e adicionando um artefato para instalar o agente de teste. Você pode salvar a VM provisionada como base para que a base possa ser usada sem precisar reinstalar o agente de teste para cada provisionamento da VM.

**Caps** é um mecanismo para minimizar o desperdício em seu laboratório. Por exemplo, você pode definir um limite para restringir o número de VMs que podem ser criados por usuário ou em um laboratório.

**Políticas** ajuda no controle de custos em seu laboratório. Por exemplo, você pode criar uma política para desligar automaticamente máquinas virtuais com base em uma agenda definida.

##Próximas etapas

Para iniciar o Laboratório de Desenvolvimento de Teste, siga o tutorial passo a passo para [Criar um laboratório de Teste de Desenvolvimento do Azure](devtest-lab-create-lab.md).

<!---HONumber=AcomDC_0204_2016-->