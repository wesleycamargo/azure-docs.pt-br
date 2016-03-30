<properties
	pageTitle="Adicionar uma VM com artefatos para um Laboratório de Desenvolvimento/Teste | Microsoft Azure"
	description="Saiba como adicionar uma VM com artefatos a um Laboratório de Desenvolvimento/Teste"
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
	ms.date="03/13/2016"
	ms.author="tarcher"/>

# Adicionar uma VM com artefatos a um Laboratório de Desenvolvimento/Teste

> [AZURE.NOTE] Clique no link a seguir para exibir o vídeo que acompanha este artigo: [como criar VMs com artefatos em um Laboratório de Desenvolvimento/Teste](/documentation/videos/how-to-create-vms-with-artifacts-in-a-devtest-lab)

## Visão geral

Você pode criar uma VM em um Laboratório de Desenvolvimento/Teste a partir de uma imagem de base que pode ser uma [imagem personalizada](./devtest-lab-create-template.md) ou uma imagem do Marketplace.

Os *artefatos* do Laboratório de Desenvolvimento/Teste permitem que você especifique as *ações* que serão executadas quando a VM for criada.

As ações de artefato podem executar procedimentos, como executar scripts do PowerShell do Windows, execução de comandos Bash e instalação de software.

Os *parâmetros* de artefato permitem que você personalize o artefato para seu cenário específico.

Este artigo mostra como criar uma VM em seu laboratório usando artefatos.

## Adicionar uma VM com artefatos

1. Entre no [Portal do Azure](https://portal.azure.com).

1. Toque em **Procurar** e toque em **Laboratórios de Desenvolvimento/Teste** na lista.

1. Na lista de laboratórios, toque no laboratório no qual você deseja criar a nova VM.

1. Na folha do laboratório, toque em **+ VM do Laboratório**, como mostra a figura a seguir. ![Folha inicial do laboratório de Desenvolvimento/Teste](./media/devtest-lab-add-vm-with-artifacts/devtestlab-home-blade-add-vm.png)

1. Na folha **VM do Laboratório**, insira um nome para a nova máquina virtual na caixa de texto **Nome da VM do Laboratório**.

1. Toque em **Base/Definir as configurações necessárias** e selecione uma imagem base para a VM.

    ![Configurações da VM do laboratório](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-lab-vm-blade-1.png)

1. Após selecionar uma imagem de base e tocar em **OK**, a folha **VM do Laboratório** expandirá para incluir elementos de interface do usuário a fim de especificar informações sobre a conta de usuário, incluindo o **Nome de Usuário**, o **Tipo de Autenticação** (se o tipo de sistema operacional para a base selecionada for Linux) e a **Senha** (supondo um tipo de autenticação de *Senha*).

    ![Folha VM do Laboratório expandida](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-lab-vm-blade-2.png)

1. Insira um **Nome de Usuário**, que receberá privilégios de administrador na máquina virtual.

1. Se o tipo de sistema operacional da base selecionada for Linux, especifique uma autenticação do tipo *Senha* ou *Chave Pública SSH*.

1. Dependendo do tipo de autenticação especificado, insira uma senha ou a chave pública SSH.

1. Toque **Tamanho da VM** e selecione um dos itens predefinidos que especificam os núcleos de processador, o tamanho da RAM e o tamanho do disco rígido da VM a ser criada.

1. Toque em **Rede Virtual** e selecione a rede virtual desejada.

1. Toque em **Sub-rede** e selecione a sub-rede.

1. Se a política de laboratório for definida para permitir endereços IP públicos para a sub-rede selecionada, especifique se você deseja ou não que o endereço IP seja público selecionando **Sim** ou **Não**. Caso contrário, essa opção será desabilitada e selecionada como **Não**.

1. Toque em **Artefatos** e, na lista de artefatos, selecione e configure os artefatos que você deseja adicionar à imagem base. **Observação:** se você for iniciante em Laboratórios de Desenvolvimento/Teste ou na configuração de artefatos, pule para a seção [Adicionar um artefato existente a uma VM](#add-an-existing-artifact-to-a-vm) e volte aqui quando terminar.

1. Toque em **Criar** para adicionar a VM especificada ao laboratório.

1. A folha do laboratório exibe o status da criação da VM; primeiro como **Criando** e como **Executando** após a inicialização da VM.

1. Acesse a seção [Próximas etapas](#next-steps).

## Adicionar um artefato existente a uma VM

Durante a criação de uma VM, você pode adicionar artefatos existentes. Cada laboratório inclui artefatos do Repositório Público de Artefatos do Laboratório de Desenvolvimento/Teste, bem como artefatos criados e adicionados por você ao seu próprio Repositório de Artefatos. Para descobrir como criar artefatos, confira o artigo [Aprenda a criar seus próprios artefatos para uso com Laboratórios de Desenvolvimento/Teste](devtest-lab-artifact-author.md).

1. Na folha **VM do Laboratório**, toque em **Artefatos**. 

1. Na folha **Adicionar Artefatos**, toque no artefato desejado.

![Folha Adicionar Artefatos](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifact-blade.png)

1. Insira os valores de parâmetro necessários e quaisquer parâmetros opcionais dos quais você precise.  

1. Toque em **Adicionar** para adicionar o artefato e retornar à folha **Adicionar Artefatos**.

1. Continue adicionando artefatos à sua VM conforme o necessário.

1. Depois de adicionar os artefatos, você pode [alterar a ordem na qual eles são executados](#change-the-order-in-which-artifacts-are-run). Você pode também voltar e [exibir ou modificar um artefato](#view-or-modify-an-artifact).

## Alterar a ordem de execução dos artefatos

Por padrão, as ações dos artefatos são executadas na ordem em que eles são adicionados à VM. As etapas a seguir ilustram como alterar a ordem de execução dos artefatos.

1. Na parte superior da folha **Adicionar Artefatos**, toque no link que indica o número de artefatos adicionadas à VM.

    ![](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. Arraste e solte os artefatos na lista para refletir a ordem desejada. **Observação:** se você tiver problemas ao arrastar o artefato, certifique-se de que você esteja arrastando a partir do lado esquerdo do artefato.

1. Toque em **OK** quando terminar.

## Exibir ou modificar um artefato

As etapas a seguir ilustram como exibir ou modificar os parâmetros de um artefato:

1. Na parte superior da folha **Adicionar Artefatos**, toque no link que indica o número de artefatos adicionadas à VM.

    ![](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. Na folha **Artefatos Selecionados**, toque no artefato que você deseja exibir ou editar.

1. Na folha **Adicionar Artefato**, faça quaisquer alterações necessárias e toque em **OK** para fechar a folha **Adicionar Artefato**.

1. Toque em **OK** para fechar a folha **Artefatos Selecionados**.

## Próximas etapas

- Após a criação da VM, você poderá se conectar à VM tocando em **Conectar** na folha da VM.
- Descubra como criar artefatos com o artigo [Aprenda a criar seus próprios artefatos para uso com Laboratórios de Desenvolvimento/Teste](devtest-lab-artifact-author.md).

<!---HONumber=AcomDC_0316_2016-->