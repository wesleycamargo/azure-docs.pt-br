<properties writer="kathydav" editor="tysonn" manager="jeffreyg" /> 

**Observação**: Este artigo cria uma máquina virtual que não está conectada a uma rede virtual. Se você quiser que sua máquina virtual use uma rede virtual para que possa se conectar às suas máquinas virtuais diretamente pelo nome do host ou estabelecer conexões entre locais, use o método **Da Galeria** e especifique a rede virtual ao criar a máquina virtual. Para obter mais informações sobre redes virtuais, consulte [Visão geral da rede virtual do Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063)


Siga estas etapas para criar uma máquina virtual:

1. Faça logon no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com) usando sua conta do Azure.

2. No Portal de Gerenciamento, na parte inferior esquerda da página da Web, clique em **+Novo**, clique em **Máquina Virtual** e, em seguida, clique em **Da Galeria**.
	![Criar uma Nova Máquina Virtual][Image1]

3. Selecione uma imagem de máquina virtual do Windows Server 2008 R2 SP1 e, em seguida, clique na seta próxima na parte inferior direita da página.
	
4. Na página **configuração de máquina Virtual**, forneça as seguintes informações:

- Forneça um **Nome de Máquina Virtual**, como "testwinvm".
-Na caixa **Novo Nome de Usuário**, digite "Administrador".
- Na caixa **Nova Senha**, digite uma [senha forte](http://msdn.microsoft.com/pt-br/library/ms161962.aspx)
-Na caixa **Confirmar Senha**, digite a senha novamente.
- Selecione o **Tamanho** apropriado na lista suspensa.

	Clique na próxima seta para continuar.


5. Na página **Modo máquina virtual**, forneça as seguintes informações:

- Selecione **Máquina Virtual Autônoma**.
- Na caixa **Nome DNS**, digite um subdomínio válido no formato **testwinvm.cloudapp.net**
-Na caixa **Região/Grupo de Afinidade/Rede Virtual**, selecione uma região onde essa imagem virtual será hospedada.

   Clique na próxima seta para continuar.

	
6. Na página **Opções da máquina virtual**, selecione **(nenhum)** na caixa **Conjunto de Disponibilidade**. Clique na marca de seleção para continuar.
	

7. Aguarde enquanto o Azure prepara sua máquina virtual.


[Image1]: ./media/create-and-configure-windows-server-2008-vm-in-portal/CreateWinVM.png



