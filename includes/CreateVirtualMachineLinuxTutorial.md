# Crie uma máquina virtual que executa Linux

É fácil criar uma máquina virtual que executa o sistema operacional Linux quando você usa a galeria de imagens no Portal de Gerenciamento do Azure. Este guia pressupõe que você não tem experiência anterior com o Azure. Você pode criar uma máquina virtual que executa o sistema operacional Linux na nuvem que possa acessar e personalizar.

> [WACOM.NOTE] Não é necessária nenhuma experiência com VMs do Azure para concluir este tutorial. Porém, você precisa de uma conta do Azure. Você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Criar uma conta do Azure][Criar uma conta do Azure].

Você aprenderá a:

-   [Sobre máquinas virtuais no Azure][Sobre máquinas virtuais no Azure]
-   [Como criar a máquina virtual][Como criar a máquina virtual]
-   [Como fazer o logon na máquina virtual após você criá-la][Como fazer o logon na máquina virtual após você criá-la]
-   [Como anexar um disco de dados à nova máquina virtual][Como anexar um disco de dados à nova máquina virtual]

**Observação**: Este tutorial cria uma máquina virtual que não está conectada a uma rede virtual. Se você deseja que uma máquina virtual use uma rede virtual, você deve especificar a rede virtual ao criar a máquina virtual. Para obter mais informações sobre redes virtuais, consulte [Visão geral da rede virtual do Azure][Visão geral da rede virtual do Azure].

## <span id="virtualmachine"></span> </a>Sobre máquinas virtuais no Azure

A máquina virtual no Azure é um servidor na nuvem que pode ser controlado e gerenciado. Após criar uma máquina virtual no Azure, você pode excluí-la e recriá-la sempre que precisar, além de acessá-la da mesma forma faria com um servidor no seu escritório. Arquivos do disco rígido virtual (VHD) são usados para criar uma máquina virtual. Os seguintes tipos de VHDs são usados para uma máquina virtual:

-   **Imagem** - Um VHD que é usado como um modelo para criar uma nova máquina virtual. Uma imagem é um modelo porque, não tem configurações específicas como uma máquina virtual em execução, por exemplo, o nome do computador e a conta de usuário. Se você criar uma máquina virtual usando uma imagem, um disco do sistema operacional é criado automaticamente para a nova máquina virtual.
-   **Disco** - Um disco é um VHD que pode ser inicializado e montado como uma versão em execução de um sistema operacional. Após uma imagem ser configurada, ela se torna um disco. Um disco sempre é criado quando você usa uma imagem para criar uma máquina virtual. Qualquer VHD que seja anexado ao hardware virtualizado e que esteja em execução como parte de um serviço é um disco

As seguintes opções estão disponíveis para usar imagens para criar uma máquina virtual:

-   Crie uma máquina virtual usando uma imagem fornecida na Galeria de Imagens do Portal de Gerenciamento do Azure.
-   Crie e carregue no Azure um arquivo .vhd que contém uma imagem e crie uma máquina virtual usando a imagem. Para obter mais informações sobre como criar e fazer upload de uma imagem personalizada, consulte [Criação e upload de um disco rígido virtual que contém o sistema operacional Linux][Criação e upload de um disco rígido virtual que contém o sistema operacional Linux].

Cada máquina virtual reside em um serviço de nuvem, por si só, ou agrupada com outras máquinas virtuais. Você pode criar várias máquinas virtuais no mesmo serviço de nuvem a fim de ativar as máquinas virtuais para se comunicarem uma com as outras, para balanceamento de carga entre máquinas virtuais e para manter a alta disponibilidade das máquinas. Para obter mais informações sobre os serviços de nuvem e máquinas virtuais, consulte a seção "Modelos de execução" em [Apresentando o Azure][Apresentando o Azure].

## <span id="custommachine"></span> </a>Como criar a máquina virtual

Você usa o método **Da galeria** para criar uma máquina virtual personalizada no Portal de Gerenciamento. Este método fornece mais opções para configurar a máquina virtual quando você criá-lo, como recursos conectados, o nome DNS e a conectividade de rede, se necessário.

1.  Entre no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
    Na barra de comandos, clique em **Novo**.

2.  Clique em **Máquina Virtual** e, em seguida, clique em **Da Galeria**.

3.  Em **Escolher uma imagem**, selecione uma imagem de uma das listas. (As imagens disponíveis podem ser diferentes dependendo da assinatura que você estiver usando). Clique na seta para continuar.

4.  Se várias versões da imagem estão disponíveis, na **Data de lançamento da versão**, selecione a versão que deseja usar.

5.  Em **Nome da Máquina Virtual**, digite o nome que você deseja usar. Para esta máquina virtual, digite **MyTestVM1**.

6.  Em **Tamanho**, selecione o tamanho que você deseja usar para a máquina virtual. O tamanho que você escolhe depende do número de núcleos que são necessários para o seu aplicativo. Para esta máquina virtual, escolha o menor tamanho disponível.

7.  Em **Novo nome de usuário**, digite o nome da conta que você usará para administrar a máquina virtual. Você não pode usar a raiz para o nome de usuário. Para esta máquina virtual, digite **NewUser1**.

8.  Em autenticação, verifique **Fornecer uma Senha**. Em seguida, forneça as informações necessárias e clique na seta para continuar.

9.  Você pode colocar máquinas virtuais juntas no serviço de nuvem, mas para este tutorial, você está criando uma única máquina virtual. Para fazer isso, selecione **Criar um novo serviço de nuvem**.

10. Em **Nome DNS de serviço de nuvem**, digite um nome que usa entre 3 e 24 letras minúsculas e números. Esse nome se torna parte do URI que é usado para entrar em contato com a máquina virtual por meio do serviço de nuvem. Para esta máquina virtual, digite **MyService1**.

11. Em **Região/Grupo de Afinidade/Rede Virtual**, selecione a região onde você deseja localizar a máquina virtual.

12. Você pode selecionar uma conta de armazenamento onde o arquivo VHD está armazenado. Para este tutorial, aceite a configuração padrão de **Usar uma conta de armazenamento gerada automaticamente**.

13. Em **Conjunto de disponibilidade**, para os fins deste tutorial usam-se a configuração padrão de **Nenhum**. Clique na marca de seleção para criar a máquina virtual e, em seguida, clique na seta para continuar.

14. Em **Agente de VM**, decida se deseja instalar o agente de VM. Este agente fornece o ambiente para a instalação de extensões que podem ajudá-lo a interagir com a máquina virtual. Para obter detalhes, consulte [Gerenciar extensões][Gerenciar extensões].

15. Em **Pontos de extremidade**, examine o ponto de extremidade que é criado automaticamente para permitir conexões Secure Shell (SSH) para a máquina virtual. (Pontos de extremidade permitem que os recursos na Internet ou outras redes virtuais se comuniquem com uma máquina virtual.) Você pode adicionar mais pontos de extremidade agora ou criá-los mais tarde. Para obter instruções sobre como criá-las mais tarde, consulte [Como configurar pontos de extremidade para uma máquina virtual][Como configurar pontos de extremidade para uma máquina virtual].

Após a criação da máquina virtual e do serviço de nuvem, o Portal de gerenciamento lista a nova máquina virtual em **Máquinas Virtuais** e lista o serviço de nuvem em **Serviços de Nuvem**. A máquina virtual e o serviço de nuvem são iniciados automaticamente.

## <span id="logon"></span> </a>Como fazer o logon na máquina virtual após você criá-la

Para gerenciar as configurações da máquina virtual e os aplicativos executados na máquina, você pode usar um cliente SSH. Para fazer isso, você deve instalar um cliente SSH no computador que você deseja usar para acessar a máquina virtual. Há muitos programas de cliente SSH que você pode escolher. Estas são algumas opções:

-   Se estiver usando um computador que executa um sistema operacional Windows, poderá usar um cliente SSH como PuTTY. Para obter mais informações, consulte [Download do PuTTY][Download do PuTTY].
-   Se estiver usando um computador que executa um sistema operacional Linux, poderá usar um cliente SSH como OpenSSH. Para obter mais informações, consulte [OpenSSH][OpenSSH].

Este tutorial mostra como usar o programa PuTTY para acessar a máquina virtual.

1.  Encontre o **Nome do Host** e as **Informações da porta** no Portal de gerenciamento. Você pode encontrar as informações que você precisa no painel da máquina virtual. Clique no nome da máquina virtual e procure os **Detalhes de SSH** na seção **Visão Rápida** do painel de controle.

    ![Obtenha os detalhes de SSH][Obtenha os detalhes de SSH]

2.  Abra o programa PuTTY.

3.  Digite o **Nome do Host** e as **Informações da porta** coletados do painel de controle e, em seguida, clique em **abrir**.

    ![Digite o nome do host e as informações da porta][Digite o nome do host e as informações da porta]

4.  Faça logon na máquina virtual usando a conta de NewUser1 especificada quando a máquina foi criada.

    ![Faça logon em uma nova máquina virtual][Faça logon em uma nova máquina virtual]

    Você agora pode trabalhar com a máquina virtual, como faria com qualquer outro servidor.

## <span id="attachdisk"></span> </a>Como anexar um disco de dados à nova máquina virtual

Seu aplicativo pode precisar armazenar dados. Para configurar isso, você pode anexar um disco de dados à máquina virtual criada anteriormente. A maneira mais fácil de fazer isso é anexar um disco de dados vazio à máquina.

**Observação: Disco de Dados vs. Disco de Recurso**
Os discos de dados residem no Armazenamento do Azure e podem ser usados para o armazenamento persistente de arquivos e dados de aplicativo.

Cada máquina virtual criada também tem um *Disco de Recurso* local temporário anexado. Devido aos dados em um disco de recurso poderem não ser duráveis entre as reinicializações, geralmente ele é usado por aplicativos e processos em execução na máquina virtual para o armazenamento de dados transitório e temporário. Ele também é usado para armazenar páginas ou trocar de arquivos do sistema operacional.

No Linux, o Disco de Recurso é normalmente gerenciado pelo agente do Linux do Azure e montado automaticamente em **/mnt/resource** (ou **/mnt** nas imagens do Ubuntu). Observe que o disco de recurso é um disco *temporário* e pode ser esvaziado quando a VM é desprovisionada. Já no Linux, o disco de dados pode ser nomeado pelo kernel como `/dev/sdc` e os usuários precisarão particionar, formatar e montar esse recurso. Consulte o [Guia de usuário agente do Linux do Azure][Guia de usuário agente do Linux do Azure] para obter mais informações.

1.  Se você ainda não fez isso, entre no Portal de Gerenciamento do Azure.

2.  Clique em **Máquinas Virtuais**e, em seguida, selecione a máquina virtual **MyTestVM1** criada anteriormente.

3.  Na barra de comandos, clique em **Anexar** e, em seguida, clique em **Anexar Disco Vazio**.

    A caixa de diálogo **Anexar Disco Vazio** é exibida.

    ![Definir detalhes do disco][Definir detalhes do disco]

4.  O **Nome da Máquina Virtual**, **Local de Armazenamento** e **Nome do Arquivo** já estão definidos para você. Tudo o que você precisa fazer é digitar o tamanho desejado para o disco. Digite **5** no campo **Tamanho**.

    **Observação:** Todos os discos são criados por meio de um arquivo VHD no armazenamento do Azure. Você pode fornecer um nome para o arquivo VHD que é adicionado ao armazenamento, mas o nome do disco é gerado automaticamente.

5.  Clique na marca de seleção para anexar o disco de dados à máquina virtual.

6.  Você pode verificar se o disco de dados foi anexado com êxito à máquina virtual, observando o painel. Clique no nome da máquina virtual para exibir o painel.

    O número de discos agora é 2 para a máquina virtual e o disco que você anexou é listado na tabela **Discos**.

    ![Anexar disco com êxito][Anexar disco com êxito]

O disco de dados que acabou de anexar à máquina virtual estará offline e não será inicializado depois de anexá-lo. Você deve fazer logon no computador e inicializar o disco para usá-lo para armazenar dados.

1.  Conectar-se à máquina virtual usando as etapas listadas acima em **Como fazer logon na máquina virtual depois de criá-la**.

2.  Na janela SSH, digite o comando a seguir e insira a senha da conta:

    `sudo grep SCSI /var/log/messages`

    Você pode localizar o identificador do último disco de dados que foi adicionado nas mensagens que são exibidas.

    ![Identificar o disco][Identificar o disco]

3.  Na janela SSH, digite o seguinte comando para criar um novo dispositivo e, em seguida, digite a senha da conta:

    `sudo fdisk /dev/sdc`

    > [WACOM.NOTE] Neste exemplo, você talvez precise usar `sudo -i` em algumas distribuições se /sbin ou /usr/sbin não estiverem em seu `$PATH`.

4.  Digite **n** para criar uma nova partição.

    ![Criar novo dispositivo][Criar novo dispositivo]

5.  Digite **p** para definir a partição como primária, digite **1** para torná-la a primeira partição e clique enter para aceitar o valor padrão para o cilindro.

    ![Criar partição][Criar partição]

6.  Digite **p** para ver os detalhes sobre o disco que está sendo particionado.

    ![Listar informações de disco][Listar informações de disco]

7.  Digite **w** para gravar as configurações do disco.

    ![Gravar as alterações de disco][Gravar as alterações de disco]

8.  Você deve criar o sistema de arquivos na nova partição. Como exemplo, digite o seguinte comando para criar o sistema de arquivo e, em seguida, digite a senha da conta:

    `sudo mkfs -t ext4 /dev/sdc1`

    ![Criar sistema de arquivos][Criar sistema de arquivos]

    > [WACOM.NOTE] Observe que, nos sistemas SUSE Linux Enterprise 11, só há acesso somente leitura para sistemas de arquivos ext4. Para esses sistemas é recomendável formatar o novo sistema de arquivos como ext3 em vez de ext4.

9.  Em seguida, você deve ter um diretório disponível para montar o novo sistema de arquivos. Como exemplo, digite o seguinte comando para criar um novo diretório para montar a unidade e, em seguida, digite a senha da conta:

    `sudo mkdir /datadrive`

10. Digite o seguinte comando para montar a unidade:

    `sudo mount /dev/sdc1 /datadrive`

    Agora o disco de dados está pronto para ser usado como **/datadrive**.

11. Adicione a nova unidade ao /etc/fstab:

    Para garantir que a unidade seja novamente montada automaticamente após uma reinicialização, ela deve ser adicionada ao arquivo /etc/fstab. Além disso, é altamente recomendável que o UUID (Identificador Universal Exclusivo) seja usado no /etc/fstab para referir-se à unidade em vez de apenas o nome do dispositivo (por exemplo, /dev/sdc1). Para localizar o UUID da nova unidade, você pode usar o utilitário **blkid**:

        `sudo -i blkid`

    Uma saída será semelhante ao seguinte:

        `/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"`
        `/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"`
        `/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"`

    > [WACOM.NOTE] O blkid pode não exigir acesso sudo em todos os casos, no entanto, pode ser mais fácil executar com `sudo -i` em algumas distribuições se /sbin ou/usr/sbin não é `$PATH`.

    **Cuidado:** Edição inadequada do arquivo /etc/fstab pode resultar em um sistema não inicializável. Se não tiver certeza, consulte a documentação de distribuição para obter informações sobre como editá-lo corretamente. Também é recomendável que um backup do arquivo /etc/fstab seja criado antes da edição.

    Usando um editor de texto, insira as informações sobre o novo sistema de arquivo no final do arquivo /etc/fstab. Neste exemplo, usaremos o valor UUID para o novo dispositivo **/dev/sdc1** criado nas etapas anteriores e no ponto de montagem de **/datadrive**:

        `UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2`

    Ou, em sistemas baseados em SUSE Linux, talvez você precise usar um formato ligeiramente diferente:

        `/dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /   ext3   defaults   1   2`

    Se as partições ou unidades de dados adicionais forem criadas será necessário inseri-las separadamente em/etc/fstab também.

    Agora você pode testar se o sistema de arquivo é montado corretamente ao simplesmente desmontar e, em seguida, montar novamente o sistema de arquivo, ou seja, usando o ponto de montagem de exemplo `/datadrive` criado nas etapas anteriores:

        `sudo umount /datadrive`
        `sudo mount /datadrive`

    Se o segundo comando produzir um erro, verifique o arquivo /etc/fstab para obter a sintaxe correta.

    > [WACOM.NOTE] Remover subsequentemente um disco de dados sem editar fstab pode fazer com que a VM falhe ao ser inicializada. Se esta é uma ocorrência comum, então a maioria das distribuições fornecem tanto as opções `nofail` e/ou `nobootwait` fstab que permitirá que o sistema inicialize o mesmo se o disco não estiver presente. Consulte a documentação da distribuição para obter mais informações sobre esses parâmetros.

## Próximas etapas

Para saber mais sobre o Linux no Azure, consulte os seguintes artigos:

-   [Introdução ao Linux no Azure][Introdução ao Linux no Azure]

-   [Como usar as Ferramentas de Linha de Comando do Azure para Mac e Linux][Como usar as Ferramentas de Linha de Comando do Azure para Mac e Linux]

  [Criar uma conta do Azure]: http://www.windowsazure.com/pt-br/develop/php/tutorials/create-a-windows-azure-account/
  [Sobre máquinas virtuais no Azure]: #virtualmachine
  [Como criar a máquina virtual]: #custommachine
  [Como fazer o logon na máquina virtual após você criá-la]: #logon
  [Como anexar um disco de dados à nova máquina virtual]: #attachdisk
  [Visão geral da rede virtual do Azure]: http://go.microsoft.com/fwlink/p/?LinkID=294063
  [Criação e upload de um disco rígido virtual que contém o sistema operacional Linux]: /pt-br/manage/linux/common-tasks/upload-a-vhd/
  [Apresentando o Azure]: http://go.microsoft.com/fwlink/p/?LinkId=311926
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [Gerenciar extensões]: http://go.microsoft.com/FWLink/p/?LinkID=390493
  [Como configurar pontos de extremidade para uma máquina virtual]: http://azure.microsoft.com/pt-br/documentation/articles/virtual-machines-set-up-endpoints/
  [Download do PuTTY]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
  [OpenSSH]: http://www.openssh.org/
  [Obtenha os detalhes de SSH]: ./media/CreateVirtualMachineLinuxTutorial/SSHdetails.png
  [Digite o nome do host e as informações da porta]: ./media/CreateVirtualMachineLinuxTutorial/putty.png
  [Faça logon em uma nova máquina virtual]: ./media/CreateVirtualMachineLinuxTutorial/sshlogin.png
  [Guia de usuário agente do Linux do Azure]: http://www.windowsazure.com/pt-br/manage/linux/how-to-guides/linux-agent-guide/
  [Definir detalhes do disco]: ./media/CreateVirtualMachineLinuxTutorial/attachnewdisklinux.png
  [Anexar disco com êxito]: ./media/CreateVirtualMachineLinuxTutorial/attachemptysuccess.png
  [Identificar o disco]: ./media/CreateVirtualMachineLinuxTutorial/diskmessages.png
  [Criar novo dispositivo]: ./media/CreateVirtualMachineLinuxTutorial/diskpartition.png
  [Criar partição]: ./media/CreateVirtualMachineLinuxTutorial/diskcylinder.png
  [Listar informações de disco]: ./media/CreateVirtualMachineLinuxTutorial/diskinfo.png
  [Gravar as alterações de disco]: ./media/CreateVirtualMachineLinuxTutorial/diskwrite.png
  [Criar sistema de arquivos]: ./media/CreateVirtualMachineLinuxTutorial/diskfilesystem.png
  [Introdução ao Linux no Azure]: http://www.windowsazure.com/pt-br/documentation/articles/introduction-linux/
  [Como usar as Ferramentas de Linha de Comando do Azure para Mac e Linux]: http://www.windowsazure.com/pt-br/documentation/articles/xplat-cli/
