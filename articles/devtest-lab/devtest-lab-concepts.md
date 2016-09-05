<properties
	pageTitle="Conceitos do DevTest Labs | Microsoft Azure"
	description="Aprenda os conceitos básicos do DevTest Labs e como ele pode facilitar a criação, o gerenciamento e o monitoramento de máquinas virtuais do Azure"
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
	ms.date="08/22/2016"
	ms.author="tarcher"/>

#Conceitos dos Laboratórios de Desenvolvimento/Teste

> [AZURE.NOTE]
Este artigo é a parte 3 de uma série de 3 partes:
> 
> 1. [O que são os Laboratórios de Desenvolvimento/Teste?](devtest-lab-overview.md)
> 1. [Por que os Laboratórios de Desenvolvimento/Teste?](devtest-lab-why.md)
> 1. **[Conceitos do DevTest Labs](devtest-lab-concepts.md)**

##Visão geral

A lista a seguir contém as principais definições e conceitos dos Laboratórios de Desenvolvimento/Teste:

##Artefatos
Artefatos são usados para implantar e configurar seu aplicativo após o provisionamento de uma VM. Os artefatos podem ser:

- Ferramentas que você deseja instalar na VM, por exemplo, agentes, Fiddler e Visual Studio.
- Ações que você deseja executar na VM - como clonar um repositório.
- Aplicativos que você deseja testar.

Os artefatos são arquivos JSON do [Azure Resource Manager](../resource-group-overview.md) que contêm instruções para realizar a implantação e aplicar a configuração.

##Repositórios de artefatos
Repositórios de artefatos são repositórios git nos quais os artefatos são verificados. Os mesmos repositórios de artefato podem ser adicionados a vários laboratórios da sua organização, permitindo a reutilização e compartilhamento.

## Imagens base
Imagens base são imagens de VM com todas as ferramentas e configurações pré-instaladas e configuradas para criar uma VM rapidamente. Você pode provisionar uma VM escolhendo uma base existente e adicionando um artefato para instalar o agente de teste. Você pode salvar a VM provisionada como base para que a base possa ser usada sem precisar reinstalar o agente de teste para cada provisionamento da VM.

##Fórmulas 
Além de fornecerem as imagens base, as fórmulas também fornecem um mecanismo para provisionamento rápido de VMs. Uma fórmula em Laboratórios de Desenvolvimento/Teste é uma lista de valores de propriedade padrão usados para criar uma VM de laboratório. Com fórmulas, VMs com o mesmo conjunto de propriedades - como a imagem de base, tamanho de máquina virtual, rede virtual e artefatos - podem ser criadas sem a necessidade de especificar essas propriedades a cada vez. Ao criar uma máquina virtual a partir de uma fórmula, os valores padrão poderão ser usados como são ou modificados.

##Limites
Limites são um mecanismo usado para minimizar o desperdício em seu laboratório. Por exemplo, você pode definir um limite para restringir o número de VMs que podem ser criados por usuário ou em um laboratório.

##Políticas
Políticas ajudam no controle de custos em seu laboratório. Por exemplo, você pode criar uma política para desligar automaticamente máquinas virtuais com base em uma agenda definida.

##Níveis de segurança
O acesso de segurança é determinado pelo RBAC (controle de acesso baseado em função) do Azure. Para entender como funciona o acesso, é importante entender as diferenças entre um escopo, uma permissão e uma função, conforme definido pelo RBAC.

- Permissão – uma permissão é um acesso definido para uma ação específica (por exemplo, acesso de leitura para todas as máquinas virtuais).
- Função – uma função é um conjunto de permissões que podem ser agrupadas e atribuídas a um usuário. Por exemplo, a função *proprietário da assinatura* tem acesso a todos os recursos dentro de uma assinatura.
- Escopo – um escopo é um nível na hierarquia de um recurso do Azure, como um grupo de recursos, um único laboratório ou toda a assinatura.
 
Dentro do escopo do DevTest Labs, há dois tipos de funções para definir permissões de usuário: usuário de laboratório e proprietário de laboratório.

- Proprietário de laboratório – um proprietário de laboratório tem acesso a qualquer recurso no laboratório. Portanto, um proprietário de laboratório pode modificar políticas, ler e gravar quaisquer máquinas virtuais, alterar a rede virtual e assim por diante.
- Usuário de laboratório – um usuário de laboratório pode exibir todos os recursos de laboratório, como VMs, políticas e redes virtuais, mas não pode modificar políticas ou as VMs criadas por outros usuários.


Para ver como criar funções personalizadas no DevTest Labs, e você pode aprender como fazer isso neste artigo, [Grant user permissions to specific lab policies (Conceder permissões de usuário para políticas específicas do laboratório)](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Como os escopos são hierárquicos, quando um usuário tem permissões em um determinado escopo, ele recebe essas permissões automaticamente em cada escopo de nível inferior que está englobado. Por exemplo, se um usuário recebe a função de proprietário da assinatura, ele tem acesso a todos os recursos em uma assinatura, o que inclui todas as máquinas virtuais, todas as redes virtuais e todos os laboratórios. Portanto, um proprietário de assinatura herda automaticamente a função de proprietário de laboratório. No entanto, o oposto não é verdadeiro. O proprietário de um laboratório tem acesso a um laboratório, que é um escopo menor que o nível de assinatura. Portanto, um proprietário de laboratório não pode ver máquinas virtuais, redes virtuais ou recursos que estão fora do laboratório.

##Próximas etapas

[Criar um laboratório no DevTest Labs](devtest-lab-create-lab.md)

<!---HONumber=AcomDC_0824_2016-->