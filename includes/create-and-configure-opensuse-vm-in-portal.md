<properties writer="kathydav" editor="tysonn" manager="jeffreyg" /> 

**Importante**: se você deseja que uma máquina virtual use uma rede virtual, você deve especificar a rede virtual ao criar a máquina virtual. Uma máquina virtual pode ser configurada para ingressar em uma rede virtual somente quando você criar a máquina virtual. Para obter mais informações sobre redes virtuais, consulte [Visão geral da rede virtual do Windows Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063)


1. Faça logon no [Portal de Gerenciamento do Windows Azure][AzurePreviewPortal] usando sua conta do Windows Azure.

2. No Portal de Gerenciamento, na parte inferior esquerda da página da Web, clique em **+Novo**, clique em **Máquina Virtual** e, em seguida, clique em **Da Galeria**.

	![Criar uma Nova Máquina Virtual][Image1]

3. Selecione uma imagem de máquina virtual OpenSUSE das **Imagens de Plataformas** e clique na próxima seta na parte inferior direta da página.


4. Na página **Configuração de máquina Virtual**, forneça as seguintes informações:

	- Forneça um **Nome da Máquina Virtual**, como "testlinuxvm".
	- Especifique um **Novo Nome de Usuário**, como "novousuário", que será adicionado ao arquivo de lista Sudoers.
	- Na caixa **Nova Senha**, digite uma [senha forte](http://msdn.microsoft.com/pt-br/library/ms161962.aspx)
	-Na caixa **Confirmar Senha**, digite a senha novamente.
	- Selecione o **Tamanho** apropriado na lista suspensa.

	Clique na próxima seta para continuar.

5. Na página **Modo da Máquina Virtual**, forneça as seguintes informações:
	- Selecione **Máquina Virtual Autônoma**.
	- Na caixa **Nome DNS**, digite um endereço DNS válido.  Por exemplo, "testlinuxvm"
	-Na caixa **Região/Grupo de Afinidade/Rede Virtual**, selecione uma região onde essa imagem virtual será hospedada.

   Clique na próxima seta para continuar.
	
6. Na página **Opções da Máquina Virtual**, selecione **(nenhum)** na caixa **Conjunto de Disponibilidade**. Clique na marca de seleção para continuar.
	
7. Aguarde enquanto o Windows Azure prepara sua máquina virtual.

##Configurar pontos de extremidade
Uma vez que a máquina virtual é criada, você deveconfigure pontos de extremidade para se conectar remotamente.

1. No Portal de Gerenciamento, clique em **Máquinas Virtuais**, em seguida clique no nome da sua nova máquina virtual e depois clique em **Pontos de extremidade**.

2. Clique em **Editar Ponto de Extremidade** na parte inferior da página e edite o ponto de extremidade SSH de forma que a **Porta Pública** seja 22.

##Conectar-se à máquina virtual
Quando a máquina virtual for provisionada e os pontos de extremidade forem configurados, você poderá se conectar a ela usando SSH ou PuTTY.

###Conectando usando SSH
Se você estiver usando um computador com Linux, conecte-se à VM com SSH.  No prompt de comando, execute:

	$ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180

Digite a senha do usuário.

###Conectando usando PuTTY
Se você estiver usando um computador com Windows, conecte-se à VM com PuTTY. O PuTTY pode ser baixado na [Página de Download do PuTTY][PuTTYDownLoad]. 

1. Faça o download e salve o **putty.exe** em um diretório no seu computador. Abra um prompt de comando, navegue até essa pasta e execute o **putty.exe**.

2. Digite "testlinuxvm.cloudapp.net" em **Nome de Host** e "22" na **Porta**.
![Tela PuTTY][Image6]  

##Atualizar a máquina virtual (opcional)
1. Depois de se conectar à máquina virtual, você pode, opcionalmente, instalar atualizações do sistema e caminhos. Execute:

	`$ sudo zypper update`

2. Selecione **Software** e **atualização online**  É exibida uma lista de atualizações.  Selecione **Aceitar** para iniciar a instalação e aplicar todos os patches novos (exceto as opcionais) que estão atualmente disponíveis para o seu sistema. 

3. Após a conclusão da instalação, selecione **Concluir**  O sistema agora está atualizado.

[PuTTYDownload]: http://www.puttyssh.org/download.html
[AzurePreviewPortal]: http://manage.windowsazure.com

[Image1]: ./media/create-and-configure-opensuse-vm-in-portal/CreateVM.png




[Image6]: ./media/create-and-configure-opensuse-vm-in-portal/putty.png

