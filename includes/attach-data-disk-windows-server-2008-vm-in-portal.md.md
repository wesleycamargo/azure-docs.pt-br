Siga estas etapas para anexar um disco de dados:

1.  No [Portal de Gerenciamento do Azure][], clique em **Máquinas Virtuais** e selecione a máquina virtual que acabou de criar (**testwinvm**).

2.  Na barra de comandos, clique em **Anexar** e, em seguida, clique em **Anexar Disco Vazio**.

    A caixa de diálogo **Anexar disco vazio à máquina virtual** é aberta.

3.  O **Nome da Máquina Virtual**, **Local de Armazenamento** e **Nome do Arquivo** já estão definidos para você. Tudo o que você precisa fazer é digitar o tamanho desejado para o disco. Digite **5** no campo **Tamanho**.

    ![Anexar Disco Vazio][]

    **Observação:** Todos os discos são criados por meio de um arquivo VHD no armazenamento do Azure. Você pode fornecer um nome para o arquivo VHD que adicionar ao armazenamento, mas o Azure gera o nome do disco automaticamente.

4.  Clique na marca de seleção para anexar o disco de dados à máquina virtual.

5.  Clique no nome da máquina virtual para exibir o painel. Isso permite que você verifique se o disco de dados foi anexado com êxito à máquina virtual.

    O número de discos agora é 2 para a máquina virtual. O disco que você anexou está listado na tabela Discos.

    ![Anexar Disco Vazio][1]

    Depois de anexar os dados do disco à máquina virtual, o disco estará offline e não inicializado. Você precisa fazer logon na máquina virtual e inicializar o disco para poder usá-lo para armazenar dados.

## Conecte-se à Máquina Virtual usando a Área de Trabalho Remota e conclua a instalação

1.  Depois que a máquina virtual estiver provisionada, no Portal de Gerenciamento, clique em **Máquinas Virtuais** e, em seguida, clique em sua nova máquina virtual. As informações sobre sua máquina virtual são apresentadas.

2.  Na parte inferior da página, clique em **Conectar**. Abra o arquivo .rpd usando o programa Área de Trabalho Remota do Windows (*%windir%\\system32\\mstsc.exe*).

3.  Na caixa de diálogo **Segurança do Windows**, forneça a senha da conta **Administrador**. (Pode ser solicitado que você verifique as credenciais da máquina virtual.) Na primeira vez que você fizer logon nesta máquina virtual, talvez seja necessário concluir vários processos, incluindo a configuração da área de trabalho, atualizações do Windows e a conclusão de tarefas da configuração inicial do Windows. Quando você está conectado à máquina virtual com a Área de Trabalho Remota do Windows, a máquina virtual funciona como qualquer outro computador.

4.  Depois de fazer logon na máquina virtual, abra o **Gerenciador de Servidores**. No painel esquerdo, expanda **Armazenamento**e, em seguida, clique em **Gerenciamento de Disco**.

    ![Gerenciador de Servidores][]

5.  A janela **Inicializar Disco** é aberta. Clique em **OK**.

    ![Inicializar disco][]

6.  Com o botão direito do mouse na área de alocação de espaço para o Disco 2, clique em **Novo Volume Simples**e, em seguida, conclua o assistente com os valores padrão.

    ![Novo Volume Simples][]

    O disco está online e pronto para ser usado com uma nova letra de unidade.

    ![Inicialização bem-sucedida][]

  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [Anexar Disco Vazio]: ./media/attach-data-disk-windows-server-2008-vm-in-portal/AttachDataDiskWinVM2.png
  [1]: ./media/attach-data-disk-windows-server-2008-vm-in-portal/AttachDataDiskWinVM3.png
  [Gerenciador de Servidores]: ./media/attach-data-disk-windows-server-2008-vm-in-portal/servermanager.png
  [Inicializar disco]: ./media/attach-data-disk-windows-server-2008-vm-in-portal/initializedisk0.png
  [Novo Volume Simples]: ./media/attach-data-disk-windows-server-2008-vm-in-portal/initializediskvolume.png
  [Inicialização bem-sucedida]: ./media/attach-data-disk-windows-server-2008-vm-in-portal/initializesuccess.png
