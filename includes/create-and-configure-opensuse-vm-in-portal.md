<properties writer="kathydav" editor="tysonn" manager="timlt" />

1. Entre no [Portal do Azure](http://manage.windowsazure.com). Confira a oferta [Avaliação Gratuita](http://azure.microsoft.com/pricing/free-trial/) caso você ainda não tenha uma assinatura.

2. Na barra de comandos na parte inferior da janela, clique em **Novo**.

3. Em **Computação**, clique em **Máquina Virtual** e em **Da Galeria**.

	![Criar uma Nova Máquina Virtual][Image1]

4. No grupo **SUSE**, selecione uma imagem de máquina virtual OpenSUSE e clique na seta para continuar.

5. Na primeira página **Configuração da máquina virtual**:

	- Digite um **Nome da máquina virtual**, como "testlinuxvm".
	- Verifique a **Camada** e escolha um **Tamanho**. A camada determina os tamanhos que você pode escolher. O tamanho afeta o custo de utilização e as opções de configuração, por exemplo, quantos discos de dados é possível anexar. Para obter detalhes, consulte [Tamanhos das máquinas virtuais](../articles/virtual-machines-size-specs.md).
	- Digite um **Novo Nome de Usuário** ou aceite o padrão, **azureuser**. Esse nome é adicionado ao arquivo da lista Sudoers.
	- Decide que tipo de **Autenticação** usar. Para obter diretrizes gerais de senha, consulte [Senhas fortes](http://msdn.microsoft.com/library/ms161962.aspx).

6. Na próxima página **Configuração da máquina virtual**:

	- Use o padrão **Criar um novo serviço de nuvem**.
	- Na caixa **Nome de DNS**, digite um nome de DNS válido para usar como parte do endereço, como "testlinuxvm".
	- Na caixa **Região/Grupo de Afinidade/Rede Virtual**, selecione uma região onde essa imagem virtual será hospedada.
	- Em **Pontos de extremidade**, mantenha o ponto de extremidade do SSH. Agora, você pode adicionar outros, ou adicionar, alterar ou excluí-los após a criação da máquina virtual.

	>[AZURE.NOTE]Se você desejar que uma máquina virtual use uma rede virtual, **deverá** especificar a rede virtual ao criar a máquina virtual. Você não pode adicionar uma máquina virtual a uma rede virtual após a criação da máquina virtual. Para saber mais, confira [Visão geral da rede virtual](virtual-networks-overview.md).

7.	Na última página de **Configuração da máquina virtual**, mantenha as configurações padrão e, em seguida, clique na marca de seleção para concluir.

O portal relaciona a nova máquina virtual sob **Máquinas Virtuais**. Embora o status será indicado como **(Provisionando)**, a máquina virtual está sendo configurada. Quando o status indicar **Executando**, você poderá passar para a próxima etapa.

##Conectar-se à máquina virtual

Você usará SSH ou PuTTY para se conectar à máquina virtual, dependendo do sistema operacional no computador do qual você se conectará:

- Em um computador executando o Linux, use SSH. No prompt de comando, digite:

	`$ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180`

	Digite a senha do usuário.

- Em um computador executando o Windows, use PuTTY. Se ele não estiver instalado, baixe-o na [Página de Download do PuTTY][PuTTYDownload].

	Salve **putty.exe** em um diretório no computador. Abra um prompt de comando, navegue até essa pasta e execute **putty.exe**.

	Digite o nome do host, como "testlinuxvm.cloudapp.net” e digite "22" para a **Porta**.

	![Tela PuTTY][Image6]

##Atualizar a máquina virtual (opcional)

1. Depois de se conectar à máquina virtual, você poderá, opcionalmente, instalar atualizações e correções do sistema. Para executar a atualização, digite:

	`$ sudo zypper update`

2. Selecione **Software**, em seguida, **Atualização online** para listar atualizações disponíveis. Selecione **Aceitar** para iniciar a instalação e aplique todas as novas correções disponíveis (exceto as opcionais).

3. Após a instalação ser concluída, selecione **Concluir**. O sistema agora está atualizado.

[PuTTYDownload]: http://www.puttyssh.org/download.html

[Image1]: ./media/create-and-configure-opensuse-vm-in-portal/CreateVM.png

[Image6]: ./media/create-and-configure-opensuse-vm-in-portal/putty.png

<!---HONumber=Oct15_HO3-->