<properties writer="kathydav" editor="tysonn" manager="jeffreyg" />

**Importante**: Se deseja que sua máquina virtual use uma rede virtual, você deve especificar a rede virtual ao criar a máquina virtual. Uma máquina virtual pode ser configurada para ingressar em uma rede virtual somente quando você criar a máquina virtual. Para obter mais informações sobre redes virtuais, consulte [Visão geral da rede virtual do Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).

1.  Faça logon no [Portal de Gerenciamento do Azure][] utilizando sua conta do Azure.

2.  No Portal de Gerenciamento, na parte inferior esquerda da página da Web, clique em **+Novo**, clique em **Máquina Virtual** e, em seguida, clique em **Da Galeria**.

    ![Criar uma Nova Máquina Virtual][]

3.  Selecione uma imagem de máquina virtual OpenSUSE das **Imagens de Plataformas** e clique na próxima seta na parte inferior direta da página.

4.  Na página **configuração de máquina Virtual**, forneça as seguintes informações:

    -   Forneça um **Nome da Máquina Virtual**, como "testlinuxvm".
    -   Especifique um **Novo Nome de Usuário**, como "novousuário", que será adicionado ao arquivo de lista Sudoers.
    -   Na caixa **Nova Senha**, digite uma [senha forte][].
    -   Na caixa **Confirmar Senha**, digite a senha novamente.
    -   Selecione o **Tamanho** apropriado na lista suspensa.

    Clique na seta de avanço para continuar.

5.  Na página **Modo da máquina virtual**, forneça as seguintes informações:

    -   Selecione **Máquina Virtual Autônoma**.
    -   Na caixa **Nome DNS**, digite um endereço DNS válido. Por exemplo, "testlinuxvm".
    -   Na caixa **Região/Grupo de Afinidade/Rede Virtual**, selecione uma região onde essa imagem virtual será hospedada.

	Clique na seta de avanço para continuar.

6.  Na página **Opções da máquina virtual**, selecione **(nenhum)** na caixa **Conjunto de Disponibilidade**. Clique na marca de seleção para continuar.

7.  Aguarde enquanto o Azure prepara sua máquina virtual.

## Configurar pontos de extremidade

Uma vez que a máquina virtual é criada, você deveconfigure pontos de extremidade para se conectar remotamente.

1.  No Portal de Gerenciamento, clique em **Máquinas Virtuais**, em seguida clique no nome da sua nova máquina virtual e depois clique em **Pontos de extremidade**.

2.  Clique em **Editar Ponto de Extremidade** na parte inferior da página e edite o ponto de extremidade SSH de forma que a **Porta Pública** seja 22.

## Conectar-se à máquina virtual

Quando a máquina virtual for provisionada e os pontos de extremidade forem configurados, você poderá se conectar a ela usando SSH ou PuTTY.

### Conectando usando SSH

Se você estiver usando um computador com Linux, conecte-se à VM com SSH. No prompt de comando, execute:

    $ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180

Digite a senha do usuário.

### Conectando usando PuTTY

Se você estiver usando um computador com Windows, conecte-se à VM com PuTTY. O PuTTY pode ser baixado na [Página de Download do PuTTY][].

1.  Faça o download e salve o **putty.exe** em um diretório no seu computador. Abra um prompt de comando, navegue até essa pasta e execute o **putty.exe**.

2.  Digite "testlinuxvm.cloudapp.net" em **Nome de Host** e "22" na **Porta**.
    ![Tela do PuTTY][]

## Atualizar a máquina virtual (opcional)

1.  Depois de se conectar à máquina virtual, você pode, opcionalmente, instalar atualizações do sistema e caminhos. Execute:

    `$ sudo zypper update`

2.  Selecione **Software** e **atualização online**. É exibida uma lista de atualizações. Selecione **Aceitar** para iniciar a instalação e aplicar todos os patches novos (exceto as opcionais) que estão atualmente disponíveis para o seu sistema.

3.  Após a conclusão da instalação, selecione **Concluir**. O sistema agora está atualizado.

  [Visão geral da rede virtual do Azure]: http://go.microsoft.com/fwlink/p/?LinkID=294063
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [Criar uma Nova Máquina Virtual]: ./media/create-and-configure-opensuse-vm-in-portal/CreateVM.png
  [senha forte]: http://msdn.microsoft.com/en-us/library/ms161962.aspx
  [Página de Download do PuTTY]: http://www.puttyssh.org/download.html
  [Tela do PuTTY]: ./media/create-and-configure-opensuse-vm-in-portal/putty.png
