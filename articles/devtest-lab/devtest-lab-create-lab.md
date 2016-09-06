<properties
	pageTitle="Criar um laboratório no DevTest Labs | Microsoft Azure"
	description="Crie um novo laboratório no DevTest Labs para máquinas virtuais"
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
	ms.topic="get-started-article"
	ms.date="08/25/2016"
	ms.author="tarcher"/>

# Criar Laboratórios de Desenvolvimento/Teste do Azure

## Pré-requisitos

Para criar um laboratório, você precisará de:

- Uma assinatura do Azure. Para saber mais sobre as opções de compra do Azure, consulte [Como comprar o Azure](https://azure.microsoft.com/pricing/purchase-options/) ou [Avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/). Você deve ser o proprietário da assinatura para criar o laboratório.
- Um Grupo de Recursos do Azure para o laboratório. Consulte [Visão Geral do Gerenciador de Recursos do Azure](../resource-group-overview.md) e [Controle de Acesso Baseado em Função do Azure](../active-directory/role-based-access-control-configure.md).

## Criar um laboratório

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Procurar**.

1. Toque em **Laboratórios de Desenvolvimento/Teste** na lista.

1. Na folha **Laboratórios de Desenvolvimento/Teste**, selecione **Adicionar**.

    ![Adicionar um laboratório](./media/devtest-lab-create-lab/add-lab-button.png)

1. Na folha **Criar um Laboratório de Desenvolvimento/Teste**:

    1. Insira um **Nome do Laboratório** para o novo laboratório.
    1. Selecione a **Assinatura** para associar ao laboratório.
    1. Selecione um **Local** no qual o laboratório será armazenado.
    1. Selecione **Criar**.

    ![Criar uma folha de laboratório](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## Próximas etapas

Depois de criar seu laboratório, aqui estão algumas das próximas etapas a serem consideradas:

- [Proteger o acesso a um laboratório](devtest-lab-add-devtest-user.md).

- [Definir políticas de laboratório](devtest-lab-set-lab-policy.md).

- [Criar um modelo de laboratório](devtest-lab-create-template.md).

- [Criar artefatos personalizados para suas VMs](devtest-lab-artifact-author.md).

- [Adicionar uma VM com artefatos a um laboratório](devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0831_2016-->