<properties
	pageTitle="Criar uma VM do Oracle WebLogic Server 12c | Microsoft Azure"
	description="Crie uma máquina virtual do Oracle WebLogic Server 12c executando o Windows Server 2012 no Microsoft Azure usando o modelo de implantação do Gerenciador de Recursos."
	services="virtual-machines-windows"
	authors="rickstercdn"
	manager="timlt"
	documentationCenter=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="05/17/2016"
	ms.author="rclaus" />

#Criando uma Máquina Virtual do Oracle WebLogic Server 12c no Azure

[AZURE.INCLUDE [virtual-machines-common-oracle-support](../../includes/virtual-machines-common-oracle-support.md)]

O exemplo a seguir mostra como você pode criar um WebLogic Server 12c em execução em uma VM que você criou anteriormente e em que instalou o Oracle WebLogic 12c com o Windows Server 2012 no Azure.

##Para configurar a sua Máquina Virtual do Oracle WebLogic Server 12c no Azure

1. Faça logon no [Portal do Azure](https://ms.portal.azure.com/).

2.	Clique em **Máquinas Virtuais**.

3.	Clique no nome da Máquina Virtual na qual você deseja fazer logon.

4.	Clique em **Conectar**.

5.	Responda às solicitações conforme necessário para se conectar à Máquina Virtual. Quando for solicitado o nome do administrador e a senha, use os valores que você forneceu quando criou a Máquina Virtual.

6.	Na caixa de diálogo **Início Rápido da Plataforma WebLogic**, clique em **Introdução ao Servidor WebLogic**. (Se a caixa de diálogo **Início Rápido da Plataforma WebLogic** ainda não estiver aberta, abra-a clicando em **Windows Iniciar**, digitando **Iniciar o servidor de administrador para o domínio do WebLogic Server** e, em seguida, clicando no ícone **Iniciar o servidor de administrador para o domínio do WebLogic Server**.)

7.	Na caixa de diálogo **Boas-vindas**, selecione **Criar um novo domínio WebLogic** e, em seguida, clique em **Avançar**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image10.png)

8.	Na caixa de diálogo **Selecionar origem do domínio**, aceite os valores padrão e, em seguida, clique em **Avançar**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image11.png)

9.	Na caixa de diálogo **Especificar nome de domínio e local**, aceite os valores padrão e, em seguida, clique em **Avançar**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image12.png)

10.	Na caixa de diálogo **Configurar nome de usuário administrador e senha**:

	1.	[Opcional] Altere o nome de usuário de **weblogic** para um valor de sua escolha.

	2.	Especifique e confirme uma senha para o administrador do servidor WebLogic.

	3.	Clique em **Próximo**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image13.png)

11.	Na caixa de diálogo **Configurar modo de início do servidor e JDK**, selecione **Modo de produção**, selecione o JDK disponível (ou o navegador para um JDK, se desejado) e, em seguida, clique em **Avançar**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image14.png)

12.	Na caixa de diálogo **Selecionar configuração opcional**, não selecione nenhuma opção e, em seguida, clique em **Avançar**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image15.png)

13.	Na caixa de diálogo **Resumo da configuração**, clique em **Criar**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image16.png)

14.	Na caixa de diálogo **Criar domínio**, marque **Iniciar Servidor de Administração** e, em seguida, clique em **Concluído**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image17.png)

15.	Um prompt de comando para **startWebLogic.cmd** é iniciado. Quando solicitado, forneça o seu nome de usuário do WebLogic e a senha.

##Para instalar um aplicativo em uma Máquina Virtual no Oracle WebLogic Server 12c no Azure
1.	Ainda conectado à sua Máquina Virtual, copie localmente o exemplo de shoppingcart.war disponível em http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war. Por exemplo, crie uma pasta chamada **C:\\mywar** e salve o WAR na http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war para **c:\\mywar**.

2.	Abra o **Console de administração do WebLogic Server**, http://localhost:7001/console. Quando solicitado, forneça o seu nome de usuário do WebLogic e a senha.

3.	Dentro do **Console de administração do WebLogic Server**, clique em **Bloquear e Editar**, **Implantações** e, em seguida, clique em **Instalar**.

4.	Para **Caminho**, digite **C:\\mywar\\shoppingcart.war**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image18.png)

	Clique em **Próximo**.

5.	Selecione I**nstalar essa implantação como um aplicativo** e, em seguida, clique em **Avançar**.

6.	Clique em **Concluir**.

7.	Dentro do **Console de administração do WebLogic Server**, clique em **Salvar** e, em seguida, em **Ativar alterações**.

8.	Clique em **Implantações**, selecione **carrinho de compras**, clique em **Iniciar** e, em seguida, clique em **Todas as solicitações de serviço**. Quando solicitado a confirmar, clique em **Sim**.

9.	Para ver o aplicativo de carrinho de compras em execução localmente, abra um navegador para <http://localhost:7001/shoppingcart>

10.	Crie um ponto de extremidade para a sua Máquina Virtual:

	1. Faça logon no [Portal do Azure](https://ms.portal.azure.com/).

	2.	Clique em **Procurar**.

	3.	Clique em **Máquinas Virtuais**.

	4.	Selecione a Máquina Virtual

	5.	Clique em **Configurações**

	6.	Clique em **Pontos de Extremidade**.

	7.	Clique em **Adicionar**.

	8.	Especifique um nome para o ponto de extremidade

		1. Use **TCP** para o protocolo

		2. Use **80** para a porta pública

		3. Use **7001** para a porta privada.

	9.	Deixe o restante das opções como estão.

	10. Clique em **OK**

11.	Permitir uma conexão de entrada por meio do firewall na porta 7001.

	1.	Faça logon na Máquina Virtual.

	2.	Clique em **Windows Iniciar**, digite **Firewall do Windows com Segurança Avançada** e clique no ícone **Firewall do Windows com Segurança Avançada**. Isso abre o console de gerenciamento do **Firewall do Windows com Segurança Avançada**.

	3.	No console de gerenciamento do firewall, clique em **Regras de Entrada** no painel esquerdo (se você não vir **Regras de Entrada**, expanda o nó superior no painel à esquerda) e, em seguida, clique em Nova Regra no painel direito.

	4.	Para **Tipo de Regra**, selecione **Porta** e clique em **Avançar**.

	5.	Para **Protocolo e Porta**, selecione **TCP**, selecione **Portas locais específicas**, digite **7001** para a porta e, em seguida, clique em **Avançar**.

	6.	Selecione **Permitir a conexão** e clique em **Avançar**.

	7.	Aceite os padrões para os perfis aos quais a regra se aplica e clique em **Avançar**.

	8.	Especifique um nome para a regra e, opcionalmente, uma descrição e depois clique em **Concluir**.

12.	Para ver o aplicativo de carrinho de compras em execução na Internet, abra um navegador para a URL na forma de `http://<<unique_domain_name>>/shoppingcart`. (Você pode determinar o valor de <<*unique\_domain\_name*>> dentro do [Portal do Azure](https://ms.portal.azure.com/) clicando em **Máquinas Virtuais** e, em seguida, selecionando a máquina virtual que você está usando para executar o Oracle WebLogic Server).


##Recursos adicionais
Agora que você configurou a Máquina Virtual executando o Oracle WebLogic Server, consulte os seguintes tópicos para obter informações adicionais.

-	[Imagens de Máquinas Virtuais Oracle – diversas considerações](virtual-machines-windows-classic-oracle-considerations.md)

-	[Documentação do produto Oracle WebLogic Server](http://www.oracle.com/technetwork/middleware/weblogic/documentation/index.html)

-	[Oracle WebLogic Server 12c usando o Linux no Microsoft Azure](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

-	[Imagens de Máquina Virtual Oracle para Azure](virtual-machines-linux-classic-oracle-images.md)

<!---HONumber=AcomDC_0601_2016-->