<properties
	pageTitle="Adicionar uma VM com artefatos a um Azure DevTest Labs | Microsoft Azure"
	description="Saiba como adicionar uma VM com artefatos no Azure DevTest Labs"
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
	ms.date="08/30/2016"
	ms.author="tarcher"/>

# Adicionar uma VM com artefatos a um Azure DevTest Labs

> [AZURE.VIDEO how-to-create-vms-with-artifacts-in-a-devtest-lab]

Você cria uma VM em um laboratório por meio de uma *base* que é uma [imagem personalizada](./devtest-lab-create-template.md), uma [fórmula](./devtest-lab-manage-formulas.md) ou uma [imagem do Marketplace](./devtest-lab-configure-marketplace-images.md).

Os *artefatos* dos Laboratórios de Desenvolvimento/Teste permitem que você especifique as *ações* que serão executadas quando a VM for criada.

As ações de artefato podem executar procedimentos, como execução de scripts do Windows PowerShell, execução de comandos Bash e instalação de software.

Os *parâmetros* de artefato permitem que você personalize o artefato para seu cenário específico.

Este artigo mostra como criar uma VM em seu laboratório com artefatos.

## Adicionar uma VM com artefatos

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Mais Serviços** e selecione **DevTest Labs** na lista.

1. Na lista de laboratórios, selecione o laboratório no qual você deseja criar a VM.

1. Na folha **Visão geral** do laboratório, selecione **+ Máquina Virtual**. ![Botão Adicionar VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-home-blade-add-vm.png)

1. Na folha **Escolher uma base**, selecione uma base para a VM.

1. Na folha **Máquina virtual**, insira um nome para a nova máquina virtual na caixa de texto **Nome da máquina virtual**.

	![Folha VM de Laboratório](./media/devtest-lab-add-vm-with-artifacts/devtestlab-lab-vm-blade.png)

1. Insira um **Nome de Usuário**, que receberá privilégios de administrador na máquina virtual.

1. Se você quiser usar uma senha armazenada em seu *repositório secreto*, selecione **Usar segredos do meu repositório secreto** e especifique um valor de chave que corresponda ao seu segredo (senha). Caso contrário, simplesmente digite uma senha no campo de texto rotulado **Digite um valor**.
 
1. Selecione **Tamanho da máquina virtual** e selecione um dos itens predefinidos que especificam os núcleos de processador, o tamanho da RAM e o tamanho do disco rígido da VM a ser criada.

1. Selecione **Rede virtual** e escolha a rede virtual desejada.

1. Selecione **Sub-rede** e escolha a sub-rede.

1. Se a política de laboratório for definida para permitir endereços IP públicos para a sub-rede selecionada, especifique se você deseja ou não que o endereço IP seja público selecionando **Sim** ou **Não**. Caso contrário, essa opção será desabilitada e selecionada como **Não**.

1. Selecione **Artefatos** e, na lista de artefatos, selecione e configure os artefatos que você deseja adicionar à imagem base. **Observação:** se você for iniciante em Laboratórios de Desenvolvimento/Teste ou na configuração de artefatos, vá para a seção [Adicionar um artefato existente a uma VM](#add-an-existing-artifact-to-a-vm) e volte aqui quando terminar.

1. Se quiser exibir ou copiar o modelo do Azure Resource Manager, pule para a seção [Salvar modelo do Azure Resource Manager](#save-arm-template) e retorne para cá quando terminar.

1. Selecione **Criar** para adicionar a VM especificada ao laboratório.

1. A folha do laboratório exibe o status da criação da VM; primeiro como **Criando** e como **Executando** após a inicialização da VM.

1. Acesse a seção [Próximas etapas](#next-steps).

## Adicionar um artefato existente a uma VM

Durante a criação de uma VM, você pode adicionar artefatos existentes. Cada laboratório inclui artefatos do Repositório Público de Artefatos de Laboratórios de Desenvolvimento/Teste, bem como artefatos criados e adicionados por você a seu próprio Repositório de Artefatos. Para saber como criar artefatos, confira o artigo [Aprenda a criar seus próprios artefatos para uso com Laboratórios de Desenvolvimento/Teste](devtest-lab-artifact-author.md).

1. Na folha **Máquina Virtual**, selecione **Artefatos**.

1. Na folha **Adicionar Artefatos**, selecione o artefato desejado.

	![Folha Adicionar Artefatos](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifact-blade.png)

1. Insira os valores de parâmetro necessários e quaisquer parâmetros opcionais dos quais você precise.

1. Selecione **Adicionar** para adicionar o artefato e retornar à folha **Adicionar Artefatos**.

1. Continue adicionando artefatos à sua VM conforme o necessário.

1. Após adicionar os artefatos, você pode [alterar a ordem em que eles são executados](#change-the-order-in-which-artifacts-are-run). Você pode também voltar e [exibir ou modificar um artefato](#view-or-modify-an-artifact).

## Alterar a ordem de execução dos artefatos

Por padrão, as ações dos artefatos são executadas na ordem em que eles são adicionados à VM. As etapas a seguir ilustram como alterar a ordem de execução dos artefatos.

1. Na parte superior da folha **Adicionar Artefatos**, selecione o link que indica o número de artefatos adicionados à VM.

    ![Número de artefatos adicionados à VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. Para especificar a ordem na qual os artefatos são executados, arraste e solte os artefatos na ordem desejada. **Observação:** se tiver problemas ao arrastar o artefato, verifique se está arrastando do lado esquerdo do artefato.

1. Selecione **OK** quando tiver concluído.

## Exibir ou modificar um artefato

As etapas a seguir ilustram como exibir ou modificar os parâmetros de um artefato:

1. Na parte superior da folha **Adicionar Artefatos**, selecione o link que indica o número de artefatos adicionados à VM.

    ![Número de artefatos adicionados à VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. Na folha **Artefatos Selecionados**, selecione o artefato que você deseja exibir ou editar.

1. Na folha **Adicionar Artefato**, faça as alterações necessárias e selecione **OK** para fechar a folha **Adicionar Artefato**.

1. Selecione **OK** para fechar a folha **Artefatos Selecionados**.

## Salvar o modelo do Azure Resource Manager

Um modelo do Azure Resource Manager é uma forma declarativa de definir uma implantação repetível. As etapas a seguir explicam como salvar o modelo do Azure Resource Manager para a VM que está sendo criada. Depois de salvo, você pode usar o modelo do Azure Resource Manager para [implantar novas VMs com o Azure PowerShell](../resource-group-overview.md#template-deployment).

1. Na folha **Máquina virtual**, selecione **Exibir Modelo de ARM**.

1. Na **folha Exibir o Modelo do Azure Resource Manager**, selecione o texto do modelo.

1. Copie o texto selecionado para a área de transferência.

1. Selecione **OK** para fechar a **folha Exibir Modelo do Azure Resource Manager**.

1. Abra um editor de texto.

1. Cole o texto do modelo da área de transferência.

1. Salve o arquivo para uso posterior.

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## Próximas etapas

- Após a criação da VM, você poderá se conectar à VM selecionando **Conectar** na folha da VM.
- Saiba como [criar artefatos personalizados para sua VM do DevTest Labs](devtest-lab-artifact-author.md).
- Explorar a [galeria de modelos de Início Rápido do ARM do DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)

<!---HONumber=AcomDC_0907_2016-->