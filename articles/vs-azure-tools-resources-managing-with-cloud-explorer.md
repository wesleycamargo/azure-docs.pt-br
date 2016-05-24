<properties 
   pageTitle="Gerenciamento de recursos do Azure com o Cloud Explorer | Microsoft Azure"
   description="Saiba como usar o Cloud Explorer para navegar e gerenciar recursos do Azure no Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="05/08/2016"
   ms.author="tarcher" />

# Gerenciando recursos do Azure com o Cloud Explorer

##Visão geral

O Cloud Explorer foi projetado para permitir que você procure e gerencie os recursos do Azure de modo mais fácil e rápido no IDE do Visual Studio. Por exemplo, você pode usá-lo para abrir um aplicativo Web no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) ou em um navegador ou anexar um depurador a ele, ou então, você pode exibir as propriedades de um contêiner de blob e abri-lo no Editor de Contêiner de Blob.

O Cloud Explorer é criado na pilha do Azure Resource Manager, exatamente como o [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). Ele compreende recursos como grupos de recursos do Azure e serviços do Azure como Aplicativos Lógicos e Aplicativos de API; além disso, ele dá suporte a RBAC [(controle de acesso baseado em função)](../role-based-access-control-configure/). Para ver os recursos do Azure que foram adicionados ou alterados, escolha o botão **Atualizar** na barra de ferramentas do Cloud Explorer.

O Cloud Explorer é instalado como parte das Ferramentas do Visual Studio para o SDK do Azure 2.7.

## Pré-requisitos

- Visual Studio 2015 RTM.

- Ferramentas do Visual Studio para o SDK do Azure.
- Você também deve ter uma conta do Azure e estar conectado a ela para exibir recursos do Azure no Cloud Explorer. Se você não tiver uma, poderá criar uma conta em apenas alguns minutos. Se tiver uma assinatura do MSDN, consulte [Benefício do Azure para assinantes do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Caso contrário, consulte [criar uma conta de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Se o Cloud Explorer não estiver visível, você poderá exibi-lo escolhendo **Exibir**, **Outras Janelas** e **Cloud Explorer** na barra de menus.

## Gerenciar assinaturas e contas do Azure

Para ver os recursos do Azure no Cloud Explorer, você precisa fazer logon em uma conta do Azure com uma ou mais assinaturas ativas. Se você tem mais de uma conta do Azure, pode adicioná-las no Cloud Explorer e, em seguida, escolher as assinaturas que você deseja incluir no modo de exibição de recursos do Cloud Explorer.

Se você não usou o Azure antes ou não adicionou as contas necessárias ao Visual Studio, você será solicitado a fazê-lo.

## Para adicionar contas do Azure ao Cloud Explorer

1. Escolha o ícone Configurações na barra de ferramentas do Cloud Explorer.

1. Escolha o link **Adicionar uma conta**. Faça logon na conta do Azure cujos recursos você deseja procurar. A conta que você acabou de adicionar deve ser selecionada na lista suspensa seletora de conta. As assinaturas para essa conta aparecem sob a entrada de conta.

    ![Adicionando assinaturas do Azure](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819514.png)

    ![Escolhendo assinaturas do Azure](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819515.png)

1. Marque as caixas de seleção para as assinaturas de conta que você deseja procurar e, em seguida, escolha o botão **Aplicar**.

    Os recursos do Azure para as assinaturas selecionadas aparecem no Cloud Explorer.

## Para remover uma conta do Azure

1. Na barra de menus, escolha **Arquivo**, **Configurações de Conta**.

1. Na seção **todas as contas** da caixa de diálogo **Configurações de Conta**, escolha o comando **Remover** ao lado da conta que você deseja remover. Observe que este comando remove apenas a conta do Visual Studio — ele não afeta a conta do Azure em si.

## Exibir grupos ou tipos de recursos

Para exibir os recursos do Azure, você pode escolher um dos modos de exibição **Tipos de Recursos** ou **Grupos de Recursos**.

![Lista suspensa de modo de exibição de recursos](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819516.png)

- A exibição **Tipos de Recursos**, que também é a exibição comumente usada no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), mostra os recursos do Azure categorizados por tipo, como aplicativos Web, contas de armazenamento e máquinas virtuais. Isso é semelhante ao modo como os recursos do Azure aparecem no Gerenciador de Servidores.

- O modo de exibição Grupos de Recursos categoriza os recursos do Azure pelo grupo de recursos do Azure ao qual eles estão associados.

 
	Um grupo de recursos é um pacote de recursos do Azure, normalmente usados por um aplicativo específico. Para saber mais sobre grupos de recursos do Azure, consulte [Visão geral do Gerenciador de Recursos do Azure](./resource-group-overview.md).

## Exibir e navegar até recursos

Para navegar para um recurso do Azure e exibir suas informações no Cloud Explorer, expanda o tipo do item ou o grupo de recursos associado e, em seguida, escolha o recurso. Quando você escolhe um recurso, informações aparecem nas duas guias na parte inferior do Cloud Explorer.

![Escolher um modo de exibição de recursos](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819517.png)

- A guia **Ações** exibe as ações que podem ser executadas no Cloud Explorer para o recurso selecionado. Você também pode ver as ações disponíveis no menu de atalho do recurso.

- A guia **Propriedades** exibe as propriedades do recurso, como seu tipo, localidade e o grupo de recursos a que ele está associado.

Cada recurso tem a ação **Abrir no portal**. Quando você seleciona essa ação, o Cloud Explorer exibe o recurso selecionado no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). Esse recurso é particularmente útil para navegar para recursos muito aninhados.

Valores da propriedade e ações adicionais também podem aparecer de acordo com o recurso do Azure. Por exemplo, aplicativos Web e aplicativos lógicos também têm, além de **Abrir no portal**, as ações **Abrir no navegador** e **Anexar depurador**. Ações para abrir editores aparecem quando você seleciona um blob, fila ou tabela da conta de armazenamento. Os aplicativos do Azure têm as propriedades **URL** e **Status**, enquanto os recursos de armazenamento têm as propriedades de cadeia de conexão e chave.

## Pesquisar recursos

Para localizar recursos com um nome específico em assinaturas de sua conta do Azure, digite o nome na caixa de Pesquisa no Cloud Explorer.

![Localizando recursos no Cloud Explorer](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC820394.png)

Conforme você insere caracteres na caixa de Pesquisa, apenas os recursos que correspondem a esses caracteres são exibidos na árvore de recursos.

<!---HONumber=AcomDC_0511_2016-->