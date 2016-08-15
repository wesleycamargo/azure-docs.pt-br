<properties
	pageTitle="Gerenciar fórmulas de Laboratórios de Desenvolvimento/Teste para criar VMs | Microsoft Azure"
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
	ms.date="06/01/2016"
	ms.author="tarcher"/>

# Gerenciar fórmulas de Laboratórios de Desenvolvimento/Teste para criar VMs

## Visão geral

Fórmulas, como [imagens personalizadas](./devtest-lab-create-template.md) e [imagens do Marketplace](./devtest-lab-configure-marketplace-images.md), fornecem um mecanismo para o provisionamento rápido de VMs. Uma fórmula em Laboratórios de Desenvolvimento/Teste é uma lista de valores de propriedade padrão usados para criar uma VM de laboratório. Ao se criar uma VM com base em uma fórmula, os valores padrão podem ser usados como estão ou ser modificados.

Neste artigo, você aprenderá a realizar as seguintes tarefas:

- [Criar uma nova fórmula](#create-a-new-formula)
- [Usar uma fórmula para criar uma nova VM](#use-a-formula-to-create-a-new-vm)
- [Modificar uma Fórmula](#modify-a-formula)
- [Excluir uma fórmula](#delete-a-formula)

> [AZURE.NOTE] As fórmulas são semelhantes às [imagens personalizadas](./devtest-lab-create-template.md), pois cada uma delas permite que você crie uma imagem base de um VHD que é usado para provisionar uma VM. Para ajudar a decidir o que é melhor para seu ambiente específico, confira o artigo [Comparar imagens e fórmulas personalizadas em Laboratórios de Desenvolvimento/Teste](./devtest-lab-comparing-vm-base-image-types.md).

## Criar uma nova fórmula
Qualquer pessoa com permissões de *Usuários* de Laboratórios de Desenvolvimento/Teste pode criar VMs em um laboratório usando uma fórmula como base. Existem duas maneiras de criar fórmulas:

- Do zero: use quando quiser definir todas as características da fórmula desde o início.
- Com base em uma VM de laboratório existente: use quando desejar criar uma fórmula com base nas configurações de uma VM existente.

### Criar uma nova fórmula do zero
As etapas a seguir o orientarão durante o processo de criação de uma nova fórmula do zero.

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Procurar**, e, em seguida, **DevTest Labs** na lista.

1. Na lista de laboratórios, selecione o laboratório desejado.

1. A folha **Configurações** do laboratório selecionado será exibida.

1. Na folha **Configurações**, selecione **Fórmulas**.

    ![Menu Fórmula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)

1. Na folha **Fórmulas do laboratório**, selecione **+ Adicionar**.

    ![Adicionar uma nova fórmula](./media/devtest-lab-manage-formulas/add-formula.png)

1. Na folha **Escolher uma base**, selecione a imagem personalizada ou uma imagem do Marketplace com base na qual você deseja criar a fórmula.

    ![Lista de base](./media/devtest-lab-manage-formulas/base-list.png)

1. Na folha **Criar fórmula**, especifique os seguintes valores:

	- **Nome da fórmula** – digite um nome para a fórmula. Esse valor será exibido na lista de imagens de base quando você criar uma máquina virtual. O nome é validado durante a digitação e, se não servir, uma mensagem indicará os requisitos para um nome válido.
	- **Descrição** – digite uma descrição relevante para a fórmula. Esse valor está disponível no menu de contexto da fórmula quando você cria uma VM.
	- **Imagem** – este campo exibe o nome da imagem de base que você selecionou na folha anterior.
	- **Tamanho da VM** – Selecione um dos itens predefinidos que especificam os núcleos de processador, o tamanho da RAM e o tamanho do disco rígido da VM a ser criada.
	- **Rede virtual** – Selecione e escolha a rede virtual desejada.
	- **Sub-rede** – Selecione e escolha a sub-rede desejada.
	- **Endereço IP público** – se a política de laboratório for definida para permitir endereços IP públicos para a sub-rede selecionada, especifique se você deseja ou não que o endereço IP seja público selecionando **Sim** ou **Não**. Caso contrário, essa opção será desabilitada e selecionada como **Não**.
	- **Artefatos** – Selecione e, na lista de artefatos, escolha e configure os artefatos que você deseja adicionar à imagem base. Observe que os parâmetros de artefato que são cadeias de caracteres protegidas não são exibidos, já que a fórmula não salva valores de cadeias de caracteres protegidas.

    	![Criar fórmula](./media/devtest-lab-manage-formulas/create-formula.png)

1. Selecione **Criar** para criar a fórmula. Quando a fórmula tiver sido criada com êxito, será listada na folha **Fórmulas de laboratório**.

	![Fórmula recém-criada](./media/devtest-lab-manage-formulas/newly-created-formula.png)

### Criar uma nova fórmula com base em uma VM de laboratório
As etapas a seguir o orientarão durante o processo de criação de uma fórmula com base em uma VM existente.

> [AZURE.NOTE] Apenas VMs criadas depois de 30 de março de 2016 dão suporte à criação de uma nova fórmula com base em uma VM de laboratório.

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Procurar**, e, em seguida, **DevTest Labs** na lista.

1. Na lista de laboratórios, selecione o laboratório desejado.

1. Na folha do laboratório, localize a seção com o título **Minhas VMs** e selecione a VM com base na qual você deseja criar a nova fórmula.

	![VMs do Labs](./media/devtest-lab-manage-formulas/my-vms.png)

1. Na folha **Configurações** da VM, selecione **Criar fórmula**.

	![Criar fórmula](./media/devtest-lab-manage-formulas/create-formula-menu.png)

1. Na folha **Criar fórmula**, insira um **Nome** e uma **Descrição** para a nova fórmula e selecione **OK**. Quando a fórmula tiver sido criada com êxito, será listada na folha **Fórmulas de laboratório**.

	![Folha Criar fórmula](./media/devtest-lab-manage-formulas/create-formula-blade.png)

## Modificar uma fórmula
Para modificar uma fórmula, siga estas etapas:

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Procurar**, e, em seguida, **DevTest Labs** na lista.

1. Na lista de laboratórios, selecione o laboratório desejado.

1. Na folha **Configurações**, selecione **Fórmulas**.

    ![Menu Fórmula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)

1. Na folha **Fórmulas do laboratório**, selecione a fórmula que você deseja modificar.

1. Na folha **Atualizar fórmula**, faça as edições desejadas e selecione **Atualizar**.

## Excluir uma fórmula 
Para excluir uma fórmula, siga estas etapas:

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Procurar**, e, em seguida, **DevTest Labs** na lista.

1. Na lista de laboratórios, selecione o laboratório desejado.

1. Na folha **Configurações**, selecione **Fórmulas**.

    ![Menu Fórmula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)

1. Na folha **Fórmulas do laboratório**, clique nas reticências à direita da fórmula que você deseja excluir.

    ![Menu Fórmula](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)

1. No menu de contexto da fórmula, selecione **Excluir**.

    ![Menu de contexto da Fórmula](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)

1. Selecione **Sim** na caixa de diálogo de confirmação de exclusão.

    ![Menu de contexto da Fórmula](./media/devtest-lab-manage-formulas/formula-delete-confirmation.png)

## Postagens de blogs relacionadas

- [Imagens personalizadas ou fórmulas?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## Próximas etapas
Após criar uma fórmula para uso durante a criação de uma VM, a próxima etapa é [adicionar uma VM ao laboratório](./devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0803_2016-->