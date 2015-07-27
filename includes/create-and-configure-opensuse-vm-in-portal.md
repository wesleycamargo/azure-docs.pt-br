**Importante**: se você deseja que uma máquina virtual use uma rede virtual, você deve especificar a rede virtual ao criar a máquina virtual. Uma máquina virtual pode ser configurada para ingressar em uma rede virtual somente quando você criar a máquina virtual. Para obter mais informações sobre redes virtuais, consulte [Visão geral da rede virtual do Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).


1. Faça logon no [Portal de Gerenciamento do Azure][AzurePreviewPortal] utilizando sua conta do Azure.

2. No Portal de Gerenciamento, na parte inferior esquerda da página da Web, clique em **+Novo**, clique em **Máquina Virtual** e, em seguida, clique em **Da Galeria**.

	![Criar uma Nova Máquina Virtual][Image1]

3. No grupo **SUSE**, selecione uma imagem de máquina virtual OpenSUSE e clique na seta de avanço na parte inferior direita da página.


4. Na primeira página **Configuração de máquina virtual**, preencha ou verifique as configurações:

	- Digite um **Nome da máquina virtual**, como "testlinuxvm".
	- Verifique a **Camada** e escolha um **Tamanho**. A camada determina os tamanhos que você pode escolher.
	- Digite um **Novo nome de usuário**, como "newuser", que será adicionado ao arquivo de lista Sudoers.
	- Decide que tipo de **Autenticação** usar. Para obter diretrizes gerais de senha, consulte [Senhas fortes](http://msdn.microsoft.com/library/ms161962.aspx).


5. Na próxima página **Configuração de máquina virtual**, preencha ou verifique as configurações:
	- Use o padrão **Criar um novo serviço de nuvem**.
	- Na caixa **Nome de DNS**, digite um nome de DNS válido para usar como parte do endereço, como "testlinuxvm".
	- Na caixa **Região/Grupo de Afinidade/Rede Virtual**, selecione uma região onde essa imagem virtual será hospedada.

6.	Clique na seta de avanço para concluir, depois aguarde enquanto o Azure prepara sua máquina virtual e a inicia.

##Conectar-se à máquina virtual
Você usará SSH ou PuTTY para conectar-se à máquina virtual, dependendo do sistema operacional que está executando em seu computador:

- Se estiver usando Linux para conectar-se à máquina virtual, use SSH. No prompt de comando, execute:

	`$ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180`

	Digite a senha do usuário.

- Se estiver usando um computador Windows para conectar-se à máquina virtual, use PuTTY. É possível baixar o PuTTY na [Página de Download do PuTTY][PuTTYDownload].

	Faça o download e salve o **putty.exe** em um diretório no seu computador. Abra um prompt de comando, navegue até essa pasta e execute o **putty.exe**.

	Digite o nome do host, como "testlinuxvm.cloudapp.net” e digite "22" para a **Porta**.

	![Tela PuTTY][Image6]

##Atualizar a máquina virtual (opcional)
1. Depois de se conectar à máquina virtual, você pode, opcionalmente, instalar atualizações e correções do sistema. Para executar a atualização, digite:

	`$ sudo zypper update`

2. Selecione **Software**, em seguida, **Atualização online** para listar atualizações disponíveis. Selecione **Aceitar** para iniciar a instalação e aplique todas as novas correções disponíveis (exceto as opcionais).

3. Após a instalação ser concluída, selecione **Concluir**. O sistema agora está atualizado.

[PuTTYDownload]: http://www.puttyssh.org/download.html
[AzurePreviewPortal]: http://manage.windowsazure.com

[Image1]: ./media/create-and-configure-opensuse-vm-in-portal/CreateVM.png

[Image6]: ./media/create-and-configure-opensuse-vm-in-portal/putty.png

<!---HONumber=July15_HO3-->