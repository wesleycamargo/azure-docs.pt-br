    <properties
	pageTitle="Create a DevTest Lab | Microsoft Azure"
	description="Create a new DevTest Lab lab for virtual machines"
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
	ms.date="01/30/2016"
	ms.author="tarcher"/>

# Criar um Laboratório de Desenvolvimento/Teste do Azure

## Pré-requisitos

Para criar um Laboratório de Desenvolvimento/Teste, será necessário:

- Uma assinatura do Azure. Para saber mais sobre as opções de compra do Azure, consulte [Como comprar o Azure](https://azure.microsoft.com/pricing/purchase-options/) ou [Avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/). Você deve ser o proprietário da assinatura para criar o laboratório.
- Um Grupo de Recursos do Azure para o laboratório. Consulte [Visão Geral do Gerenciador de Recursos do Azure](/resource-group-overview.md) e [Controle de Acesso Baseado em Função do Azure](/active-directory/role-based-access-control-configure.md).


## Criar um laboratório

1. Entre no [Portal do Azure](https://portal.azure.com).

1. Toque em **Procurar**.

1. Toque em **Laboratórios de Desenvolvimento/Teste** na lista.

1. Na folha **Laboratórios de Desenvolvimento/Teste**, toque em **Adicionar**.

    ![Adicionar um Laboratório de Desenvolvimento/Teste](./media/devtest-lab-create-lab/add-lab-button.png)

1. Na folha **Criar um Laboratório de Desenvolvimento/Teste**:

    1. Insira um **Nome do Laboratório** para o novo laboratório.
    1. Selecione a **Assinatura** para associar ao laboratório.
    1. Selecione um **Local** no qual o laboratório será armazenado.
    1. Toque em **Criar**.

    ![Criar uma folha Laboratório de Desenvolvimento/Teste](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

## Próximas etapas

Depois de criar seu laboratório, aqui estão algumas das próximas etapas a serem consideradas:

- [Acesso seguro a um Laboratório de Desenvolvimento/Teste](devtest-lab-add-devtest-user.md).

- [Definir políticas de laboratório](devtest-lab-set-lab-policy.md).

- [Criar um modelo de laboratório](devtest-lab-create-template.md).

- [Criar artefatos personalizados para suas VMs](devtest-lab-artifact-author.md).

- [Adicionar uma VM com artefatos a um Laboratório de Desenvolvimento/Teste do Azure](devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0309_2016-->