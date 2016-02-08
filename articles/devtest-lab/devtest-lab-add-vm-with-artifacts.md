    <properties
	pageTitle="Add a VM with artifacts to a DevTest Lab | Microsoft Azure"
	description="Create a new virtual machine with Artifacts in DevTest Lab."
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
	ms.date="11/01/2015"
	ms.author="tarcher"/>

# Adicionar uma VM com artefatos a um Laboratório de Desenvolvimento/Teste do Azure

## Visão geral

Crie uma máquina virtual em um Laboratório de Desenvolvimento/Teste começando com uma imagem base do Azure ou com uma imagem que você carregou em seu laboratório.

Os *artefatos* do Laboratório de Desenvolvimento/Teste permitem que você especifique as ações que serão executadas quando a VM for criada. As ações de artefato podem executar procedimentos, como executar comandos do Powershell e do Bash e instalar software. Os *parâmetros* de artefato permitem que você personalize o artefato para seu cenário específico.

Seu laboratório inclui artefatos do Repositório público de artefatos de Laboratório de Desenvolvimento/Teste, bem como artefatos criados e adicionados ao seu próprio Repositório de Artefatos.

Este artigo mostra como criar uma VM em seu laboratório usando artefatos.

## Adicionar uma VM com artefatos

1. Entre no [Portal de visualização do Azure](https://portal.azure.com).

1. Toque em **Procurar** e em **Laboratórios de Desenvolvimento/Testes** na lista.

1. Na lista de laboratórios, toque no laboratório no qual você deseja criar a nova VM.

1. Na folha do laboratório, toque em **+ VM do Laboratório**, como mostra a figura a seguir. ![Folha inicial do laboratório de Desenvolvimento/Teste](./media/devtest-lab-add-vm-with-artifacts/devtestlab-home-blade-add-vm.png)

1. Na folha **VM do Laboratório**, insira um nome para a nova máquina virtual na caixa de texto **Nome da VM do Laboratório**.

1. Toque em **Base/Definir as configurações necessárias** e selecione uma imagem base para a VM.

    ![Configurações da VM do laboratório](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-lab-vm-blade-1.png)

1. Depois de selecionar uma imagem base, a folha **VM do Laboratório** será expandida para incluir os itens **Nome de Usuário** e **Senha**. Insira um **Nome de Usuário**, que receberá privilégios de administrador na máquina virtual.

    ![Folha VM do Laboratório expandida](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-lab-vm-blade-2.png)

1. Insira uma **Senha**.

1. Toque **Tamanho da VM** e selecione um dos itens predefinidos que especificam os núcleos de processador, o tamanho da RAM e o tamanho do disco rígido da VM a ser criada.

1. Toque em **Artefatos** e, na lista de artefatos, selecione e configure os artefatos que você deseja adicionar à imagem base. **Observação:** se você for iniciante em Laboratórios de Desenvolvimento/Teste ou na configuração de artefatos, pule para a seção [Selecionando e configurando um artefato](#configuring-an-artifact) e volte ao terminar.

1. Toque em **Criar** para adicionar a VM especificada ao laboratório.

1. A folha do laboratório exibe o status da criação da VM; primeiro como **Criando** e como **Executando** após a inicialização da VM. Para conectar-se à VM, toque em VM e, na folha da VM, toque em **Conectar**.

## Selecionar e configurar um artefato

Durante a criação de uma VM, você pode adicionar artefatos tocando em **Artefatos** na folha **VM do Laboratório**. Isso exibirá a folha **Adicionar Artefatos** que permite a adição e configuração dos artefatos da VM a partir do repositório do Laboratório de Desenvolvimento/Teste oficial (**Repositório Oficial**) e artefatos do repositório da equipe.

![Folha Adicionar Artefatos](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifact-blade.png)

**Adicionando um artefato a uma VM**

Siga estas etapas para cada artefato que você deseja adicionar à VM:

1. Toque no artefato desejado na folha **Adicionar Artefatos** para exibir uma folha que permite a especificação dos parâmetros do artefato.  

2. Insira os valores de parâmetro necessários e quaisquer parâmetros opcionais dos quais você precise.

3. Toque em **Adicionar** para adicionar o artefato e retornar à folha **Adicionar Artefatos**.

**Alterando a ordem de execução dos artefatos**

À medida que você adiciona e configura os artefatos para sua VM, um link mostrando o número atual de artefatos será exibido na parte superior da folha **Adicionar Artefatos**. Por padrão, as ações dos artefatos são executadas na ordem em que eles são adicionados à VM. Para alterar a ordem de execução dos artefatos, basta arrastar e soltar os artefatos na lista e tocar em **OK** após terminar.

**Exibindo/modificando os artefatos selecionados**

Execute estas etapas para exibir ou modificar os parâmetros de seus artefatos selecionados:

1. Na parte superior da folha **Adicionar Artefatos**, clique no link que indica quantos artefatos foram adicionadas à VM.

    ![](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. Para exibir ou editar os parâmetros de um artefato específico, toque nesse artefato na folha **Artefatos Selecionados**.

1. Faça as alterações necessárias e toque em **OK** para fechar a folha **Adicionar Artefato**.

1. Toque em **OK** para fechar a folha **Artefatos Selecionados**.

1. Toque em **OK** para fechar a folha **Adicionar Artefatos**.

## Próximas etapas

Saiba como [criar artefatos personalizados para sua VM](devtest-lab-artifact-author.md).

<!---HONumber=AcomDC_0128_2016-->