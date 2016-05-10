<properties
	pageTitle="Gerenciar Fórmulas do DevTest Labs para criar máquinas virtuais | Microsoft Azure"
	description="Aprenda a criar, atualizar e remover fórmulas do DevTest Labs e a usá-las para criar novas VMs."
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
	ms.date="04/22/2016"
	ms.author="tarcher"/>

# Gerenciar Fórmulas do DevTest Labs para criar máquinas virtuais

## Visão geral

Fórmulas, como [Imagens Personalizadas](./devtest-lab-create-template.md) e [Imagens do Marketplace](./devtest-lab-configure-marketplace-images.md), fornecem um mecanismo para o provisionamento rápido de máquinas virtuais. Uma fórmula no DevTest Labs é uma lista de valores de propriedade padrão usado para criar uma VM de laboratório. Ao criar uma máquina virtual a partir de uma fórmula, os valores padrão poderão ser usados como são ou modificados.

Neste artigo, você aprenderá a realizar as seguintes tarefas:

- [Criar uma nova fórmula](#create-a-new-formula)
- [Usar uma fórmula para criar uma nova VM](#use-a-formula-to-create-a-new-vm)
- [Alterar uma Fórmula](#modify-a-formula)
- [Excluir uma Fórmula](#delete-a-formula)

> [AZURE.NOTE] As fórmulas são semelhantes às [Imagens Personalizadas](./devtest-lab-create-template.md) no sentido em que cada uma permite que você crie uma imagem base de um VHD que é usado para provisionar uma máquina virtual. Para ajudar a decidir o que é melhor para seu ambiente específico, consulte o artigo [Comparando Imagens e Fórmulas Personalizadas no DevTest Labs](./devtest-lab-comparing-vm-base-image-types.md).

## Criar uma nova Fórmula
Qualquer pessoa com permissões *Usuários* do DevTest Labs é capaz de criar VMs em um laboratório usando uma Fórmula como base. Existem duas maneiras de criar fórmulas:

- Do zero: use quando quiser definir todas as características da Fórmula desde o início.
- A partir de uma VM de laboratório existente: use quando desejar criar uma Fórmula com base nas configurações de uma máquina virtual existente.

### Criar uma nova Fórmula do zero
As etapas a seguir irão orientá-lo durante o processo de criação de uma nova Fórmula a partir do zero.

1. Entre no [Portal do Azure](https://portal.azure.com).

1. Toque em **Procurar** e toque em **DevTest Labs** na lista.

1. Na lista de laboratórios, toque no laboratório desejado.

1. A folha **Configurações** do laboratório selecionado será exibida.

1. Na folha **Configurações**, toque em **Fórmulas**.

    ![Menu Fórmula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)

1. Na folha **Fórmulas de laboratório**, toque em **+ Adicionar**.

    ![Adicionar uma nova fórmula](./media/devtest-lab-manage-formulas/add-formula.png)

1. Na folha **Escolher uma base**, toque na Imagem Personalizada ou na Imagem do Marketplace a partir do qual você deseja criar a Fórmula.

    ![Lista de base](./media/devtest-lab-manage-formulas/base-list.png)

1. Na folha **Criar fórmula**, especifique os seguintes valores:

	- **Nome da fórmula** – insira um nome para a Fórmula. Esse valor será exibido na lista de imagens de base quando você criar uma máquina virtual. O nome é validado durante a digitação e, se não servir, uma mensagem indicará os requisitos para um nome válido.
	- **Descrição** – digite uma descrição relevante para a Fórmula. Esse valor está disponível no menu de contexto da Fórmula quando você cria uma máquina virtual.
	- **Imagem** – este campo exibe o nome da imagem de base que você selecionou na folha anterior. 
	- **Tamanho da VM** – toque em um dos itens predefinidos que especificam os núcleos de processador, o tamanho da RAM e o tamanho do disco rígido da VM a ser criada.
	- **Rede virtual** – toque e selecione a rede virtual desejada.
	- **Sub-rede** – toque e selecione a sub-rede desejada.
	- **Endereço IP público** – se a política de laboratório for definida para permitir endereços IP públicos para a sub-rede selecionada, especifique se você deseja ou não que o endereço IP seja público selecionando **Sim** ou **Não**. Caso contrário, essa opção será desabilitada e selecionada como **Não**.
	- **Artefatos** – toque e, na lista de artefatos, selecione e configure os artefatos que você deseja adicionar à imagem base. Observe que os parâmetros de artefato que são cadeias de caracteres protegidos não são exibidos, já que a Fórmula não salva valores de cadeia de caracteres protegidos. 

    	![Criar fórmula](./media/devtest-lab-manage-formulas/create-formula.png)

1. Toque em **Criar** para criar a Fórmula. Quando o portal criar a Fórmula, ela será listada na folha **Fórmulas do laboratório**.

	![Fórmula recém-criada](./media/devtest-lab-manage-formulas/newly-created-formula.png)

### Criar uma nova Fórmula a partir de uma VM do laboratório
As etapas a seguir irão orientá-lo durante o processo de criação de uma Fórmula com base em uma VM existente.

> [AZURE.NOTE] Somente máquinas virtuais criadas depois de 30 de março de 2016 dão suporte à criação de uma nova Fórmula a partir de uma VM do laboratório.

1. Entre no [Portal do Azure](https://portal.azure.com).

1. Toque em **Procurar** e toque em **DevTest Labs** na lista.

1. Na lista de laboratórios, toque no laboratório desejado.

1. Na folha do laboratório, localize a seção com o título **Minhas VMs** e clique na VM a partir da qual você deseja criar a nova Fórmula.

	![VMs do Labs](./media/devtest-lab-manage-formulas/my-vms.png)

1. Na folha **Configurações** da VM, toque em **Criar Fórmula**.

	![Criar Fórmula](./media/devtest-lab-manage-formulas/create-formula-menu.png)

1. Na folha **Criar fórmula**, insira um **Nome** e uma **Descrição** para sua nova Fórmula e toque em **OK**. Após o portal criar a Fórmula, ela será listada na folha **Fórmulas do laboratório**.

	![Folha Criar Fórmula](./media/devtest-lab-manage-formulas/create-formula-blade.png)

## Alterar uma Fórmula
Para modificar uma Fórmula, siga estas etapas:

1. Entre no [Portal do Azure](https://portal.azure.com).

1. Toque em **Procurar** e toque em **DevTest Labs** na lista.

1. Na lista de laboratórios, toque no laboratório desejado.

1. Na folha **Configurações**, toque em **Fórmulas**.

    ![Menu Fórmula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)

1. Na folha **Fórmulas do laboratório**, toque na Fórmula que você deseja modificar.

1. Na folha **Atualizar fórmula**, faça as edições desejadas e toque em **Atualizar**.

## Excluir uma Fórmula 
Para excluir uma Fórmula, siga estas etapas:

1. Entre no [Portal do Azure](https://portal.azure.com).

1. Toque em **Procurar** e toque em **DevTest Labs** na lista.

1. Na lista de laboratórios, toque no laboratório desejado.

1. Na folha **Configurações**, toque em **Fórmulas**.

    ![Menu Fórmula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)

1. Na folha **Fórmulas do laboratório**, clique no botão de reticências à direita da fórmula que você deseja excluir.

    ![Menu Fórmula](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)

1. No menu de contexto da Fórmula, escolha **Excluir**.

    ![Menu de contexto da Fórmula](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)

1. Toque em **Sim** no diálogo de confirmação de exclusão.

    ![Menu de contexto da Fórmula](./media/devtest-lab-manage-formulas/formula-delete-confirmation.png)

## Próximas etapas
Depois de criar uma fórmula para uso durante a criação de uma VM, a próxima etapa é [adicionar uma VM ao seu DevTest Lab](./devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0427_2016-->