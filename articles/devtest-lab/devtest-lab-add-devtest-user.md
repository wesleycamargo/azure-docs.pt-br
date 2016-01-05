    <properties
	pageTitle="Add owners and users to a DevTest Lab | Microsoft Azure"
	description="Securely add a user who is not in your subscription to your Azure DevTest Lab."
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

# Adicionar proprietários e usuários a um Laboratório de Desenvolvimento/Teste

## Visão geral

O acesso a um Laboratório de Desenvolvimento/Teste é controlado pelo Controle de Acesso Baseado em Função do Azure (RBAC). Procure [Controle de Acesso Baseado em Função(RBAC)](https://azure.microsoft.com/searchresults?query=Role%20Based%20Access%20Control%20%28RBAC%29) no Portal de visualização do Azure para saber mais.

Você concede acesso ao seu Laboratório de Desenvolvimento/Teste por meio de duas funções:

 - **Proprietário**: os usuários atribuídos à função **proprietário** no nível de assinatura do Azure têm acesso completo ao laboratório, incluindo às funções de gerenciamento e monitoramento.

     >[AZURE.NOTE]As funções de **Proprietário** atribuídas em níveis RBAC diferente do nível de assinatura não têm suporte no Laboratório de Desenvolvimento/Teste. Não há suporte para a atribuição de um usuário à função **Proprietário** no Laboratório de Desenvolvimento/Teste.

 -  **Usuário do Laboratório de Desenvolvimento/Teste**: usuários atribuídos à função **Usuário do Laboratório de Desenvolvimento/Teste** pode criar, atualizar e excluir VMs no laboratório especificado. Os usuários podem ser *internos* (um membro do Active Directory do Azure para a assinatura) ou *externos* (um usuário que não é membro do AD do Azure, como um membro de uma organização parceira).
	-  Uma função **Usuário do Laboratório de Desenvolvimento/Teste** deve ser atribuída por meio dos blocos **Adicionar Usuários** do laboratório.
	-  Os usuários na função **Usuário do Laboratório de Desenvolvimento/Teste** podem executar essas operações apenas dentro do laboratório ao qual estão atribuídos. Por exemplo, um **Usuário do Laboratório de Desenvolvimento/Teste** não pode criar uma máquina virtual usando o serviço de Máquina Virtual da assinatura. A criação de uma máquina virtual só é permitida a partir da conta do Laboratório de Desenvolvimento/Teste.
	- Os usuários *externos* devem ter uma conta em um dos domínios de conta da Microsoft (ou seja, @hotmail.com, @live.com, @msn.com, @passport.com, @outlook.com ou qualquer variante de um país específico).

## Adicionar um proprietário ao laboratório

O Laboratório de Desenvolvimento/Teste considera os proprietários de uma assinatura do Azure que contém laboratórios como proprietários desses laboratórios. Embora seja possível adicionar outros proprietários a um Laboratório de Desenvolvimento/Teste por meio da folha do laboratório no Portal de visualização do Azure, isso não tem suporte no momento.

Para adicionar um proprietário a uma assinatura do Azure na qual você já tem laboratórios criados, ou na qual criará novos laboratórios, siga estas etapas:

1. Entre no [Portal de visualização do Azure](http://portal.azure.com).

1. No painel de navegação esquerdo, toque em **Assinaturas**.

	![Link Assinaturas](./media/devtest-lab-add-devtest-user/subscriptions.png)
	
1. Toque na assinatura que conterá os laboratórios.

1. Toque no ícone do **Access**.

	![Usuários do Access](./media/devtest-lab-add-devtest-user/access-users.png)

1. Na folha **Usuários**, toque em **Adicionar**.

	![Adicionar usuário](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. Na folha **Selecionar uma função**, toque em **Proprietário**.

1. Insira na caixa de texto **Usuário** o email do usuário que você deseja adicionar como proprietário da caixa de texto. Se não for possível encontrar o usuário, você obterá uma mensagem de erro explicando o problema. Se o usuário for encontrado, ele será listado na caixa de texto **Usuário**.

1. Toque no nome do usuário localizado.

1. Toque em **Selecionar**.

1. Toque em **OK** para fechar a folha **Adicionar acesso**.

1. Quando você retornar à folha **Usuários**, verá que o usuário foi adicionado como proprietário. Agora, essa pessoa será o proprietário de todos os laboratórios criados nessa assinatura e, portanto, será capaz de executar tarefas de proprietário.

## Adicionar um usuário do Laboratório de Desenvolvimento/Teste ao seu laboratório

Para adicionar um usuário do Laboratório de Desenvolvimento/Teste ao seu laboratório, execute estas etapas:

1. Entre no [Portal de visualização do Azure](http://portal.azure.com).

1. Toque em **Procurar**.

1. Toque em **Laboratório de Desenvolvimento/Teste**.

1. Na lista de laboratórios, toque no laboratório desejado.

1. Toque no ícone do **Access**.

	![Acesso de usuário](./media/devtest-lab-add-devtest-user/devtest-lab-home-blade.png)

1. Na folha **Usuários**, toque em **Adicionar**.

	![Adicionar usuário](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. Na folha **Selecionar uma função**, toque em **Usuário do Laboratório de Desenvolvimento/Teste**

1. Na folha **Adicionar usuários**:

	1. A folha **Adicionar usuários** exibirá uma lista de usuários internos. Se o usuário desejado já estiver na lista, você poderá simplesmente tocar na linha do usuário para selecioná-lo. Uma marca de seleção aparecerá à esquerda do usuário para indicar que o usuário foi selecionado. Para selecionar vários usuários, mantenha a tecla **&lt;Ctrl>** pressionada enquanto clica em cada usuário. Para cancelar a seleção de um usuário, mantenha a tecla**&lt;Ctrl>** pressionada e clique no usuário. Um contador na parte inferior da folha indica o número de usuários selecionados.

	1. Se o usuário desejado não estiver na lista, insira uma conta de email válida da Microsoft na caixa de texto **Usuários**. Se o endereço de email for válido, o usuário será exibido abaixo da caixa de texto **Usuário**. Basta tocar nele para selecioná-lo.

	1. Depois de selecionar os usuários que você deseja adicionar ao laboratório, toque em **Selecionar**.

	1. Toque em **OK** para fechar a folha **Adicionar acesso**.

	1. A folha **Usuários** exibe as funções e usuários adicionados.

<!---HONumber=Nov15_HO4-->