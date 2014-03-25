# Criar uma máquina virtual executando o Windows Server #

É fácil criar uma máquina virtual que executa o sistema operacional Windows Server quando você usa a Galeria de Imagens no Portal de Gerenciamento do Windows Azure. Este tutorial ensina como criar uma máquina virtual com o Windows Server na nuvem para que você possa acessar e personalizar. Não é necessário experiência anterior com o Windows Azure para usar neste tutorial. 

Você aprenderá:

- [Sobre máquinas virtuais no Windows Azure] []
- [Como criar a máquina virtual] []
- [Como fazer o logon na máquina virtual após você criá-la] []
- [Como anexar um disco de dados à nova máquina virtual] []

**Observação**: Este tutorial cria uma máquina virtual que não está conectada a uma rede virtual.  Se você deseja que uma máquina virtual use uma rede virtual, você deve especificar a rede virtual ao criar a máquina virtual. Para obter mais informações sobre redes virtuais, consulte [Visão geral da rede virtual do Windows Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063)

##<a id="virtualmachine"> </a>Sobre máquinas virtuais no Windows Azure ##

A máquina virtual no Windows Azure é um servidor na nuvem que pode ser controlado e gerenciado. Depois de criar uma máquina virtual no Windows Azure, você pode acessá-la como qualquer outro servidor, além de excluí-la e recriá-la sempre que precisar. Arquivos do disco rígido virtual (arquivos .vhd) são usados para criar uma máquina virtual. Você pode usar os seguintes tipos de discos rígidos virtuais para criar uma máquina virtual:

- **Imagem** - Uma imagem é um VHD que é usado como um modelo para criar uma nova máquina virtual. Uma imagem não tem configurações específicas como uma máquina virtual em execução, por exemplo, o nome do computador e a conta de usuário. Se você criar uma máquina virtual a partir de uma imagem, um disco do sistema operacional é criado automaticamente para a nova máquina virtual.
- **Disco** - Um disco e um VHD que podem ser inicializados e montados como uma versão de um sistema operacional. Após uma imagem ser configurada, ela se torna um disco. Um disco sempre é criado quando você usa uma imagem para criar uma máquina virtual. Qualquer VHD que seja anexado ao hardware virtualizado e que esteja em execução como parte de um serviço é um disco. 

Você pode usar as seguintes opções para criar uma máquina virtual a partir de uma imagem:

- Crie uma máquina virtual usando uma imagem de plataforma disponível no Portal de gerenciamento do Windows Azure.
- Crie e carregue um arquivo .vhd que contém uma imagem do Windows Azure e, em seguida, use uma imagem que fez upload para criar uma máquina virtual. Para obter instruções, consulte [criação e carregando um disco rígido Virtual que contém o sistema operacional do Windows Server](/ pt-BR/gerenciar/windows/comum-tarefas/carregamento-um-vhd /).

Cada máquina virtual reside em um serviço de nuvem, por si só, ou agrupada com outras máquinas virtuais. Você pode criar várias máquinas virtuais no mesmo serviço de nuvem a fim de ativar as máquinas virtuais para se comunicarem uma com as outras, para balanceamento de carga entre máquinas virtuais e para manter a alta disponibilidade das máquinas. Para obter mais informações sobre os serviços de nuvem e máquinas virtuais, consulte a seção "Modelos de execução" em [Apresentando o Windows Azure](http://go.microsoft.com/fwlink/p/?LinkId=311926)

##<a id="custommachine"> </a>Como criar a máquina virtual##

Este tutorial mostra como usar o **da galeria** método no Portal de gerenciamento para criar uma máquina virtual personalizada. Este método fornece mais opções para configurar a máquina virtual quando o método **Criação rápida** ara configurar a máquina virtual quando você criá-lo, como recursos conectados, o nome DNS e a conectividade de rede, se necessário.


1. Entre no [Portal de Gerenciamento](http://manage.windowsazure.com) do Windows Azure.

2. Na barra de comandos, clique em **Novo**.

3. Clique em **Máquina Virtual** e, em seguida, clique em **Da Galeria**.
	
4. Em **Escolher uma imagem**, selecione uma imagem de uma das listas. (As imagens disponíveis podem ser diferentes dependendo da assinatura que você estiver usando). Clique na seta para continuar.
	
5. Se várias versões da imagem estão disponíveis, na **Data de lançamento da versão**, selecione a versão que deseja usar.

6. Em **Nome de Máquina Virtual**, digite o nome que você deseja usar para a máquina virtual. Para esta máquina virtual, digite **MyTestVM1**.

7. Em **tamanho**, selecione o tamanho da máquina virtual. O tamanho que você deveria selecionar depende do número de núcleos que são necessários para o seu aplicativo. Para esta máquina virtual, escolha o menor tamanho disponível.

8. Em **Novo Nome de Usuário**, digite um nome para a conta administrativa que você deseja usar para gerenciar o servidor. Para esta máquina virtual, digite **MyTestVM1Admin**.

9. Em **Nova Senha**, digite uma senha forte para a conta administrativa da máquina virtual. Em **Confirmar Senha**, digite a senha novamente. Clique na seta para continuar.

10. Você pode colocar máquinas virtuais juntas no serviço de nuvem para fornecer aplicações robustas, mas para este tutorial, você somente cria uma única máquina virtual. Para fazer isso, selecione **Criar um novo serviço de nuvem**.

11. Em **Nome DNS de serviço de nuvem**, digite um nome que usa entre 3 e 24 letras minúsculas e números. Esse nome se torna parte do URI que é usado para entrar em contato com a máquina virtual por meio do serviço de nuvem. Para esta máquina virtual, digite **MyService1**.

12. Em **Região/Grupo de Afinidade/Rede Virtual**, selecione a região onde você deseja localizar a máquina virtual.

13. Você pode selecionar uma conta de armazenamento onde o arquivo VHD está armazenado. Para este tutorial, aceite a configuração padrão de **Usar uma conta de armazenamento gerada automaticamente**.

14. Em **Conjunto de disponibilidade**, para os fins deste tutorial usam-se a configuração padrão de **Nenhum**. Clique na seta para continuar.

15.  Em **Agente de VM**, decida se deseja instalar o agente de VM. Este agente fornece o ambiente para a instalação de extensões que podem ajudá-lo a interagir com a máquina virtual. Para obter detalhes, consulte [Usando extensões](http://go.microsoft.com/FWLink/p/?LinkID=394093). **Importante**: O agente de VM pode ser instalado somente quando você criar a máquina virtual. 
 
16. Em **pontos de extremidade**, examine os novos pontos de extremidade que serão criados para permitir conexões para a máquina virtual, como a área de trabalho remota ou Windows PowerShell. Você pode também adicionar mais pontos de extremidade agora ou criá-los mais tarde. Para obter instruções sobre como criá-los mais tarde, consulte [Como definir a comunicação com uma máquina virtual](http://www.windowsazure.com/pt-BR/manage/linux/how-to-guides/setup-endpoints/).

17. Clique na marca de seleção para criar a máquina virtual.
    
	Após a criação da máquina virtual e do serviço em nuvem, o Portal de gerenciamento lista a nova máquina virtual em **Máquinas Virtuais** e lista o serviço em nuvem em **Serviços de Nuvem**. A máquina virtual e o serviço de nuvem são iniciados automaticamente.


## <a id="logon"> </a>Como fazer o logon na máquina virtual após você criá-la ##

Você pode fazer logon na máquina virtual que você criou para gerenciar as configurações e os aplicativos que estão em execução.

1. Entre no [Portal de Gerenciamento](http://manage.windowsazure.com) do Windows Azure.

2. Clique em **Máquinas Virtuais**, e, em seguida, selecione a máquina virtual **MyTestVM1**.

3. Na barra de comandos, clique em **Conectar**.

4. Clique em **abrir** para usar o arquivo de protocolo de desktop remoto foi criado automaticamente para a máquina virtual.

5. Clique em **Conectar**.

	![Continuar com a conexão](./media/CreateVirtualMachineWindowsTutorial/connectpublisher.png)

6. Na caixa senha, digite o nome de usuário e a senha que você especificou quando criou a máquina virtual e, em seguida, clique em **OK**

7. Clique em **Sim** para verificar a identidade da máquina virtual.

	![Verificar a identidade do computador](./media/CreateVirtualMachineWindowsTutorial/connectverify.png)

	Você agora pode trabalhar com a máquina virtual, como faria com qualquer outro servidor.

## <a id="attachdisk"> </a>Como anexar um disco de dados à nova máquina virtual ##

Seu aplicativo pode precisar armazenar dados. Para configurar como anexar um disco de dados à nova máquina virtual. A maneira mais fácil de fazer isso é anexar um disco de dados vazio à máquina virtual.

1. Entre no [Portal de Gerenciamento](http://manage.windowsazure.com) do Windows Azure.

2. Clique em **Máquinas Virtuais**, e, em seguida, selecione a máquina virtual **MyTestVM1**.

3. Na barra de comandos, clique em **Anexar** e, em seguida, clique em **Anexar Disco Vazio**.

	A caixa de diálogo **Anexar Disco Vazio** é exibida.

4. O **Nome da Máquina Virtuale**, **Local de Armazenamento**, **Nome do Arquivo**, e **Preferência de cachê do Host** já estão definidos para você. Tudo o que você precisa fazer é digitar o tamanho desejado para o disco. Digite **5** no campo **Tamanho**.

	**Observação:** todos os discos são criados a partir de um arquivo VHD no armazenamento do Windows Azure. Você pode fornecer um nome para o arquivo VHD que é adicionado ao armazenamento, mas o Windows Azure gera o nome do disco automaticamente.

5. Clique na marca de seleção para anexar o disco de dados à máquina virtual.

6. Clique no nome para a função de máquina virtual. Isso exibe o painel, assim você pode verificar se o disco de dados foi anexado com êxito à máquina virtual, observando o painel.

	A máquina virtual tem agora 2 discos. O disco que você anexou está listado na tabela **Discos**.

	![Anexar disco vazio](./media/CreateVirtualMachineWindowsTutorial/attachemptysuccess.png)

	Depois de anexar os dados do disco à máquina virtual, o disco estará offline e não inicializado. Antes de você poder usar os dados armazenados, você precisa fazer logon na máquina virtual e inicializar o disco.

7. Conectar-se à máquina virtual usando as etapas listadas na seção anterior, **Como fazer logon na máquina virtual depois de criá-la**.

8. Depois de fazer logon na máquina virtual, abra o **Gerenciador de Servidores**. No painel esquerdo, expanda **Armazenamento**e, em seguida, clique em **Gerenciamento de Disco**.

	![Inicializar o disco no Gerenciador de servidores](./media/CreateVirtualMachineWindowsTutorial/servermanager.png)

9. Clique com o botão direito em **Disco 2** e, em seguida, clique em **Inicializar disco**.

	![Começar inicialização](./media/CreateVirtualMachineWindowsTutorial/initializedisk.png)

10. Clique em **OK** para iniciar o processo de inicialização.

11. Com o botão direito do mouse na área de alocação de espaço para o Disco 2, clique em **Novo Volume Simples**e, em seguida, conclua o assistente com os valores padrão.

	![Criar o volume](./media/CreateVirtualMachineWindowsTutorial/initializediskvolume.png)

	O disco está online e pronto para ser usado com uma nova letra de unidade.

	![Inicialização bem-sucedida](./media/CreateVirtualMachineWindowsTutorial/initializesuccess.png)


##Próximas etapas 

Para saber mais sobre a configuração de máquinas virtuais do Windows no Windows Azure, consulte os seguintes artigos:

-[Como conectar máquinas virtuais em um serviço em nuvem](http://www.windowsazure.com/pt-BR/documentation/articles/cloud-services-connect-virtual-machine/)

-[Gerenciar a disponibilidade de máquinas virtuais](http://www.windowsazure.com/pt-BR/documentation/articles/manage-availability-virtual-machines/)

[Sobre máquinas virtuais no Windows Azure]: #virtualmachine
[como criar a máquina virtual]: #custommachine
[Como fazer logon na máquina virtual depois de criá-la]: #logon
[Como anexar um disco de dados para a nova máquina virtual]: #attachdisk
[Como configurar a comunicação com a máquina virtual]: #endpoints

