<properties
	pageTitle="Adicionar proprietários e usuários a um laboratório | Microsoft Azure"
	description="Adicionar com segurança um usuário que não está em sua assinatura aos Azure DevTest Labs"
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

# Adicionar proprietários e usuários a um laboratório

> [AZURE.NOTE] Assista ao vídeo que acompanha este artigo: [How to set security in DevTest Labs](/documentation/videos/how-to-set-security-in-your-devtest-lab) (Como definir a segurança no DevTest Labs).

## Visão geral
O acesso a Laboratórios de Desenvolvimento/Teste é controlado pelo RBAC (Controle de Acesso Baseado em Função do Azure). Pesquise [RBAC (Controle de Acesso Baseado em Função)](https://azure.microsoft.com/search/?q=role%20based%20access%20control) no [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) para saber mais.

Você permite acesso ao seu laboratório por meio de duas funções:

- **Proprietário**: os usuários atribuídos à função **Proprietário** no nível de laboratório têm acesso completo ao laboratório, incluindo as funções de gerenciamento e monitoramento. A função **Proprietário** atribuída no nível de laboratório não concede aos usuários permissões para acessar recursos na assinatura fora do escopo do laboratório. Os usuários atribuídos à função **Proprietário** no nível de assinatura do Azure automaticamente têm direitos de **Proprietário** sobre quaisquer laboratórios criados nessa assinatura.

-  **Usuário de Laboratórios de Desenvolvimento/Teste**: usuários atribuídos à função **Usuário de Laboratórios de Desenvolvimento/Teste** podem criar, atualizar e excluir VMs no laboratório especificado. Os usuários podem ser *internos* (um membro do Active Directory do Azure para a assinatura) ou *externos* (um usuário que não é membro do AD do Azure, como um membro de uma organização parceira).
	-  Uma função **Usuário de Laboratórios de Desenvolvimento/Teste** deve ser atribuída por meio dos blocos **Adicionar Usuários** do laboratório.
	-  Os usuários na função **Usuário de Laboratórios de Desenvolvimento/Teste** podem executar essas operações apenas dentro do laboratório ao qual estão atribuídos. Por exemplo, um **Usuário de Laboratórios de Desenvolvimento/Teste** não pode criar uma máquina virtual usando o serviço de Máquina Virtual da assinatura. A criação de uma máquina virtual só é permitida por meio da conta de Laboratórios de Desenvolvimento/Teste.
	- Os usuários *externos* devem ter uma conta em um dos domínios de conta da Microsoft (ou seja, @hotmail.com, @live.com, @msn.com, @passport.com, @outlook.com ou qualquer variante de um país específico).
 
## Adicionar um proprietário ao laboratório

Os Laboratórios de Desenvolvimento/Teste consideram os proprietários de uma assinatura do Azure que contém laboratórios como proprietários desses laboratórios. Embora seja possível adicionar outros proprietários a um laboratório por meio da folha do laboratório no [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), não há suporte para isso no momento.

Para adicionar um proprietário a uma assinatura do Azure na qual você já tem laboratórios criados, ou na qual criará novos laboratórios, siga estas etapas:

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. No painel de navegação esquerdo, selecione **Assinaturas**.

	![Link Assinaturas](./media/devtest-lab-add-devtest-user/subscriptions.png)
	
1. Selecione a assinatura que conterá o(s) laboratório(s).

1. Selecione o ícone do **Access**.

	![Usuários do Access](./media/devtest-lab-add-devtest-user/access-users.png)

1. Na folha **Usuários**, selecione **Adicionar**.

	![Adicionar usuário](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. Na folha **Selecionar uma função**, selecione **Proprietário**.

1. Insira na caixa de texto **Usuário** o email do usuário que você deseja adicionar como proprietário da caixa de texto. Se não for possível encontrar o usuário, você obterá uma mensagem de erro explicando o problema. Se o usuário for encontrado, ele será listado na caixa de texto **Usuário**.

1. Selecione o nome de usuário localizado.

1. Selecione **Selecionar**.

1. Selecione **OK** para fechar a folha **Adicionar acesso**.

1. Quando você retornar à folha **Usuários**, verá que o usuário foi adicionado como proprietário. Agora, essa pessoa será o proprietário de todos os laboratórios criados nessa assinatura e, portanto, será capaz de executar tarefas de proprietário.

## Adicionar um usuário de Laboratórios de Desenvolvimento/Teste ao seu laboratório

Para adicionar um usuário de Laboratórios de Desenvolvimento/Teste ao seu laboratório, execute estas etapas:

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Procurar**.

1. Selecione **DevTest Labs**.

1. Na lista de laboratórios, selecione o laboratório desejado.

1. Selecione o ícone do **Access**.

	![Acesso de usuário](./media/devtest-lab-add-devtest-user/devtest-lab-home-blade.png)

1. Na folha **Usuários**, selecione **Adicionar**.

	![Adicionar usuário](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. Na folha **Selecionar uma função**, selecione **Usuário do DevTest Labs**

1. Na folha **Adicionar usuários**:

	1. A folha **Adicionar usuários** exibirá uma lista de usuários internos. Se o usuário desejado já estiver na lista, basta selecionar a linha do usuário para selecioná-lo. Uma marca de seleção aparecerá à esquerda do usuário para indicar que o usuário foi selecionado. Para selecionar vários usuários, mantenha a tecla **&lt;Ctrl>** pressionada enquanto seleciona cada usuário. Para anular a seleção de um usuário, mantenha a tecla **&lt;Ctrl>** pressionada e selecione o usuário. Um contador na parte inferior da folha indica o número de usuários selecionados.

	1. Se o usuário desejado não estiver na lista, insira uma conta de email válida da Microsoft na caixa de texto **Usuários**. Se o endereço de email for válido, o usuário será exibido abaixo da caixa de texto **Usuário**.

	1. Depois de selecionar os usuários que você deseja adicionar ao laboratório, escolha **Selecionar**.

	1. Selecione **OK** para fechar a folha **Adicionar acesso**.

1. A folha **Usuários** exibe as funções e usuários adicionados.

<!---HONumber=AcomDC_0608_2016-->