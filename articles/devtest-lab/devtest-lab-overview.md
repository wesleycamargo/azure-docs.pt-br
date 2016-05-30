<properties
	pageTitle="O que são Laboratórios de Desenvolvimento/Teste? | Microsoft Azure"
	description="Saiba como os Laboratórios de Desenvolvimento/Teste podem facilitar criar, gerenciar e monitorar as máquinas virtuais do Azure"
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
	ms.date="05/08/2016"
	ms.author="tarcher"/>

#O que são os Laboratórios de Desenvolvimento/Teste?

Os desenvolvedores e testadores estão tentando resolver os atrasos na criação e gerenciamento de seus ambientes indo para a nuvem. O Azure resolve o problema de atrasos de ambiente e permite autoatendimento dentro de uma nova estrutura com boa relação custo-benefício. No entanto, os desenvolvedores e testadores ainda precisam gastar um tempo considerável configurando seus ambientes de autoatendimento. Além disso, os tomadores de decisão não estão certos sobre como utilizar a nuvem para maximizar suas economias sem acrescentar muita sobrecarga de processo.

Os Laboratórios de Desenvolvimento/Teste do Azure são um serviço que ajuda os desenvolvedores e testadores a rapidamente criar ambientes no Azure, minimizando o desperdício e o controle de custos. Você pode testar a versão mais recente do seu aplicativo, provisionamento ambientes Windows e Linux rapidamente usando modelos reutilizáveis e artefatos. Integre facilmente seu pipeline de implantação dos Laboratórios de Teste/Desenvolvimento para provisionar ambientes sob demanda. Dimensione seu teste de carga provisionando vários agentes de teste e crie ambientes previamente provisionados para treinamento e demonstrações.

##Por que os Laboratórios de Desenvolvimento/Teste?

Os Laboratórios de Teste/Desenvolvimento oferecem os seguintes benefícios na criação, configuração e gerenciamento ambientes de teste e de desenvolvedor na nuvem

###Autoatendimento sem preocupações

Os Laboratórios de Teste/Desenvolvimento facilitam o controle de custos, permitindo que você defina diretivas em seu laboratório - como o número de máquinas virtuais (VM) por usuário e o número de VMs por laboratório. Os Laboratórios de Teste/Desenvolvimento também permitem que você crie políticas para desligar e iniciar automaticamente as máquinas virtuais.

###Chegue rapidamente ao estado “Pronto para teste”

Os Laboratório de Teste/Desenvolvimento permitem que você crie ambientes pré-provisionados com tudo o que a sua equipe precisa para começar a desenvolver e testar aplicativos. Basta declarar os ambientes onde a última compilação boa do seu aplicativo foi instalada e começar a trabalhar imediatamente. Ou use os contêineres para uma criação de ambiente mais rápida e descomplicada.

###Crie uma vez, use em qualquer lugar

Capture e compartilhe modelos e artefatos de ambiente com sua equipe ou organização, tudo no controle do código-fonte, para criar ambientes de desenvolvedor e de teste facilmente.

###Integra-se com a sua cadeia de ferramentas existente

Aproveite plug-ins pré-fabricados ou nossa API para provisionar ambientes de Desenvolvimento/Teste diretamente da sua ferramenta de CI (integração contínua), IDE (ambiente de desenvolvimento integrado) ou pipeline de liberação automatizada preferido. Você também pode usar nossa ferramenta de linha de comando abrangente.

##Conceitos dos Laboratórios de Desenvolvimento/Teste

A lista a seguir contém as principais definições e conceitos dos Laboratórios de Desenvolvimento/Teste:

**Artefatos** são usados para implantar e configurar seu aplicativo após o provisionamento de uma VM. Os artefatos podem ser:

- Ferramentas que você deseja instalar na VM, por exemplo, agentes, Fiddler e Visual Studio.
- Ações que você deseja executar na VM - como clonar um repositório.
- Aplicativos que você deseja testar.

Os artefatos são o Gerenciador de Recursos do Azure (ARM) com base em arquivos JSON que contém instruções para realizar a implantação e aplicar a configuração. Você pode ler mais sobre o ARM na [Visão geral do Gerenciador de Recursos do Azure](../resource-group-overview.md).

**Repositórios de artefatos** são repositórios git onde os artefatos são verificados. Os mesmos repositórios de artefato podem ser adicionados a vários laboratórios da sua organização, permitindo a reutilização e compartilhamento.

**Base** é uma imagem de VM com todas as ferramentas e configurações pré-instaladas e configuradas para criar rapidamente uma máquina virtual. Você pode provisionar uma VM escolhendo uma base existente e adicionando um artefato para instalar o agente de teste. Você pode salvar a VM provisionada como base para que a base possa ser usada sem precisar reinstalar o agente de teste para cada provisionamento da VM.

**Fórmulas**, além de imagens de base, fornecem um mecanismo para provisionamento rápido de máquina virtual. Uma fórmula no DevTest Labs é uma lista de valores de propriedade padrão usado para criar uma VM de laboratório. Com fórmulas, VMs com o mesmo conjunto de propriedades - como a imagem de base, tamanho de máquina virtual, rede virtual e artefatos - podem ser criadas sem a necessidade de especificar essas propriedades a cada vez. Ao criar uma máquina virtual a partir de uma fórmula, os valores padrão poderão ser usados como são ou modificados.

**Caps** é um mecanismo para minimizar o desperdício em seu laboratório. Por exemplo, você pode definir um limite para restringir o número de VMs que podem ser criados por usuário ou em um laboratório.

**Políticas** ajuda no controle de custos em seu laboratório. Por exemplo, você pode criar uma política para desligar automaticamente máquinas virtuais com base em uma agenda definida.

##Próximas etapas

Para começar com os Laboratórios de Desenvolvimento/Teste, siga o tutorial passo a passo [Criar um laboratório nos Laboratórios de Desenvolvimento/Teste do Azure](devtest-lab-create-lab.md).

<!---HONumber=AcomDC_0518_2016-->